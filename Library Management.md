# Library Management Analysis

This project was inspired by: https://github.com/AlexanderWong/Library-Management-System/blob/master/LibraryManagementSystemAndStoredProceduresFINAL.sql

I have simplified the project and rebuilt it using postgressql.

The schema consists of 8 tables, all describing a system of books written by authors that are distributed in different store branches and are being loaned to borrowers.

To create a schema I have used the data provided in the link, created tables ans as postgres syntax is different, I had to create sequences for unique ids. Here is an example of that:

CREATE SEQUENCE tbl_borrower2_id_seq start 100;
	
CREATE TABLE tbl_borrower (
		borrower_CardNo integer not null default nextval('tbl_borrower2_id_seq'),
		borrower_BorrowerName VARCHAR(100) NOT NULL,
		borrower_BorrowerAddress VARCHAR(200) NOT NULL,
		borrower_BorrowerPhone VARCHAR(50) NOT NULL
	);	
	
ALTER SEQUENCE tbl_borrower2_id_seq
OWNED BY tbl_borrower.borrower_CardNo;

#### 1. How many copies of the book titled "The Lost Tribe" are owned by the library branch whose name is "Sharpstown"?


```sql
%%sql

select book_copies_no_of_copies
from library_management.tbl_book_copies tbc
inner join library_management.tbl_book tb 
on tbc.book_copies_bookid = tb.book_bookid
inner join library_management.tbl_library_branch tlb 
on tlb.library_branch_branchid = tbc.book_copies_branchid
where book_title = 'The Lost Tribe' and library_branch_branchname = 'Sharpstown';
```

     * postgresql://postgres:***@localhost:5432/master
    1 rows affected.
    




<table>
    <thead>
        <tr>
            <th>book_copies_no_of_copies</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>5</td>
        </tr>
    </tbody>
</table>



#### 2. How many copies of the book titled "The Lost Tribe" are owned by each library branch?


```sql
%%sql

select
	library_branch_branchname,
	book_copies_no_of_copies
from library_management.tbl_book_copies tbc
inner join library_management.tbl_book tb 
on tbc.book_copies_bookid = tb.book_bookid
inner join library_management.tbl_library_branch tlb 
on tlb.library_branch_branchid = tbc.book_copies_branchid
where book_title = 'The Lost Tribe';
```

     * postgresql://postgres:***@localhost:5432/master
    4 rows affected.
    




<table>
    <thead>
        <tr>
            <th>library_branch_branchname</th>
            <th>book_copies_no_of_copies</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>Sharpstown</td>
            <td>5</td>
        </tr>
        <tr>
            <td>Central</td>
            <td>5</td>
        </tr>
        <tr>
            <td>Saline</td>
            <td>5</td>
        </tr>
        <tr>
            <td>Ann Arbor</td>
            <td>5</td>
        </tr>
    </tbody>
</table>



#### 3. Retrieve the names of all borrowers who do not have any books checked out.


```sql
%%sql

select borrower_borrowername 
from library_management.tbl_borrower tb
where borrower_cardno not in
	(select book_loans_cardno
	from library_management.tbl_book_loans) ;
```

     * postgresql://postgres:***@localhost:5432/master
    1 rows affected.
    




<table>
    <thead>
        <tr>
            <th>borrower_borrowername</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>Jane Smith</td>
        </tr>
    </tbody>
</table>



#### 4. For each book authored by "Stephen King", retrieve the title and the number of copies owned by the library branch whose name is "Central".


```sql
%%sql

select
	book_title,
	book_copies_no_of_copies
from library_management.tbl_book_authors tba
inner join library_management.tbl_book_copies tbc 
on tba.book_authors_bookid = tbc.book_copies_bookid
inner join library_management.tbl_library_branch tlb 
on tbc.book_copies_branchid = tlb.library_branch_branchid 
inner join library_management.tbl_book tb
on tb.book_bookid = tba.book_authors_bookid 
where book_authors_authorname = 'Stephen King' and library_branch_branchname = 'Central';
```

     * postgresql://postgres:***@localhost:5432/master
    2 rows affected.
    




<table>
    <thead>
        <tr>
            <th>book_title</th>
            <th>book_copies_no_of_copies</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>It</td>
            <td>5</td>
        </tr>
        <tr>
            <td>The Green Mile</td>
            <td>5</td>
        </tr>
    </tbody>
</table>



#### 5. For each library branch, retrieve the branch name and the total number of books loaned out from that branch.


```sql
%%sql

select
	library_branch_branchname,
	count(book_loans_loansid)
from library_management.tbl_book_loans tbl
left join library_management.tbl_library_branch tlb
on tbl.book_loans_branchid = tlb.library_branch_branchid
group by library_branch_branchname;
```

     * postgresql://postgres:***@localhost:5432/master
    4 rows affected.
    




<table>
    <thead>
        <tr>
            <th>library_branch_branchname</th>
            <th>count</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>Saline</td>
            <td>10</td>
        </tr>
        <tr>
            <td>Ann Arbor</td>
            <td>20</td>
        </tr>
        <tr>
            <td>Sharpstown</td>
            <td>10</td>
        </tr>
        <tr>
            <td>Central</td>
            <td>11</td>
        </tr>
    </tbody>
</table>



#### 6. Retrieve the names, addresses, and number of books checked out for all borrowers who have more than five books checked out.


```sql
%%sql

select
	borrower_borrowername,
	borrower_borroweraddress,
	count(*)
from library_management.tbl_book_loans tbl
left join library_management.tbl_borrower tb
on tbl.book_loans_cardno = tb.borrower_cardno
group by borrower_borrowername, borrower_borroweraddress
having count(*) > 5;
```

     * postgresql://postgres:***@localhost:5432/master
    5 rows affected.
    




<table>
    <thead>
        <tr>
            <th>borrower_borrowername</th>
            <th>borrower_borroweraddress</th>
            <th>count</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>Joe Smith</td>
            <td>1321 4th Street, New York, NY 10014</td>
            <td>7</td>
        </tr>
        <tr>
            <td>Tom Li</td>
            <td>981 Main Street, Ann Arbor, MI 48104</td>
            <td>14</td>
        </tr>
        <tr>
            <td>Tom Haverford</td>
            <td>23 75th Street, New York, NY 10014</td>
            <td>6</td>
        </tr>
        <tr>
            <td>Michael Horford</td>
            <td>653 Glen Avenue, Ann Arbor, MI 48104</td>
            <td>8</td>
        </tr>
        <tr>
            <td>Angela Thompson</td>
            <td>2212 Green Avenue, Ann Arbor, MI 48104</td>
            <td>11</td>
        </tr>
    </tbody>
</table>


