# World Population Analysis

This project was inspired by LoicChamplong Data Analysis:
https://github.com/LoicChamplong/Data-Analysis-SQL/blob/master/Analysis_of_the_2015_World_population/Analysis.ipynb

The data showcases geographical and demographic data of 2015.
In this project I will use SQL to explore and analyse the data.
There are two tables in the schema. The first being county table, that includes:
- country_code - this is unique two letter code
- country_name - the full name of a country

The second table represent the statistical data of each country:
- country_code
- country_area
- area_land
- area_water
- population
- population_growth
- birth_rate
- death_rate
- migration_rate

The dataset is composed of 261 countries.

#### What is the maximum and minimum of population and population growth in all the countries?


```sql
%%sql

with temp_table as (select
	country_code,
	population,
	population_growth,
	max(population) over() as max_population,
	min(population) over() as min_population,
	max(population_growth) over() as max_pop_growth,
	min(population_growth) over() as min_pop_growth
from world_pop.population 
where country_code <> 'xx' and population is not null)
select country_name, category, population, population_growth 
from
	(select
	country_code,
	population,
	population_growth,
	(case when population = max_population then 'max population'
	when population = min_population then 'min population'
	when population_growth = max_pop_growth then 'max population growth'
	when population_growth = min_pop_growth then 'min population growth'
	else '' end) as category 
	from temp_table) as sub
inner join world_pop.country c 
on sub.country_code = c.country_code 
where category <> '';
```

     * postgresql://postgres:***@localhost:5432/master
    7 rows affected.
    




<table>
    <thead>
        <tr>
            <th>country_name</th>
            <th>category</th>
            <th>population</th>
            <th>population_growth</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>China</td>
            <td>max population</td>
            <td>1367485388</td>
            <td>0.45</td>
        </tr>
        <tr>
            <td>South Sudan</td>
            <td>max population growth</td>
            <td>12042910</td>
            <td>4.02</td>
        </tr>
        <tr>
            <td>Holy See (Vatican City)</td>
            <td>min population growth</td>
            <td>842</td>
            <td>0.00</td>
        </tr>
        <tr>
            <td>Cocos (Keeling) Islands</td>
            <td>min population growth</td>
            <td>596</td>
            <td>0.00</td>
        </tr>
        <tr>
            <td>Greenland</td>
            <td>min population growth</td>
            <td>57733</td>
            <td>0.00</td>
        </tr>
        <tr>
            <td>Pitcairn Islands</td>
            <td>min population growth</td>
            <td>48</td>
            <td>0.00</td>
        </tr>
        <tr>
            <td>Antarctica</td>
            <td>min population</td>
            <td>0</td>
            <td>None</td>
        </tr>
    </tbody>
</table>



From the query we can made a conclusion that Antartica has the lowest population of 0, the highest population count is in China. Four countries have 0 population growth and the highest population growth occured in South Sudan.

#### Finding the outliers

There are multeple ways to find outliers and in this case I have used to of them:
- create bins to see what values are skewed.
- create percentile rank to see countries with highest and lowest population.

The first query is:


```sql
%%sql

with bins as (select
	country_code,
	width_bucket(population, 0, 1400000000, 10) as buckets
from world_pop.population p 
where country_code <> 'xx' and population is not null)
select
	buckets,
	country_name
from bins
inner join world_pop.country c 
on bins.country_code = c.country_code 
order by buckets desc;
```

     * postgresql://postgres:***@localhost:5432/master
    241 rows affected.
    




<table>
    <thead>
        <tr>
            <th>buckets</th>
            <th>country_name</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>10</td>
            <td>China</td>
        </tr>
        <tr>
            <td>9</td>
            <td>India</td>
        </tr>
        <tr>
            <td>4</td>
            <td>European Union</td>
        </tr>
        <tr>
            <td>3</td>
            <td>United States</td>
        </tr>
        <tr>
            <td>2</td>
            <td>Pakistan</td>
        </tr>
        <tr>
            <td>2</td>
            <td>Russia</td>
        </tr>
        <tr>
            <td>2</td>
            <td>Indonesia</td>
        </tr>
        <tr>
            <td>2</td>
            <td>Nigeria</td>
        </tr>
        <tr>
            <td>2</td>
            <td>Bangladesh</td>
        </tr>
        <tr>
            <td>2</td>
            <td>Brazil</td>
        </tr>
        <tr>
            <td>1</td>
            <td>Azerbaijan</td>
        </tr>
        <tr>
            <td>1</td>
            <td>Bahamas, The</td>
        </tr>
        <tr>
            <td>1</td>
            <td>Bahrain</td>
        </tr>
        <tr>
            <td>1</td>
            <td>Barbados</td>
        </tr>
        <tr>
            <td>1</td>
            <td>Belarus</td>
        </tr>
        <tr>
            <td>1</td>
            <td>Belgium</td>
        </tr>
        <tr>
            <td>1</td>
            <td>Belize</td>
        </tr>
        <tr>
            <td>1</td>
            <td>Benin</td>
        </tr>
        <tr>
            <td>1</td>
            <td>Bhutan</td>
        </tr>
        <tr>
            <td>1</td>
            <td>Bolivia</td>
        </tr>
        <tr>
            <td>1</td>
            <td>Bosnia and Herzegovina</td>
        </tr>
        <tr>
            <td>1</td>
            <td>Botswana</td>
        </tr>
        <tr>
            <td>1</td>
            <td>Brunei</td>
        </tr>
        <tr>
            <td>1</td>
            <td>Bulgaria</td>
        </tr>
        <tr>
            <td>1</td>
            <td>Burkina Faso</td>
        </tr>
        <tr>
            <td>1</td>
            <td>Burma</td>
        </tr>
        <tr>
            <td>1</td>
            <td>Burundi</td>
        </tr>
        <tr>
            <td>1</td>
            <td>Cambodia</td>
        </tr>
        <tr>
            <td>1</td>
            <td>Cameroon</td>
        </tr>
        <tr>
            <td>1</td>
            <td>Canada</td>
        </tr>
        <tr>
            <td>1</td>
            <td>Cabo Verde</td>
        </tr>
        <tr>
            <td>1</td>
            <td>Central African Republic</td>
        </tr>
        <tr>
            <td>1</td>
            <td>Chad</td>
        </tr>
        <tr>
            <td>1</td>
            <td>Chile</td>
        </tr>
        <tr>
            <td>1</td>
            <td>Colombia</td>
        </tr>
        <tr>
            <td>1</td>
            <td>Comoros</td>
        </tr>
        <tr>
            <td>1</td>
            <td>Congo, Democratic Republic of the</td>
        </tr>
        <tr>
            <td>1</td>
            <td>Congo, Republic of the</td>
        </tr>
        <tr>
            <td>1</td>
            <td>Costa Rica</td>
        </tr>
        <tr>
            <td>1</td>
            <td>Cote dIvoire</td>
        </tr>
        <tr>
            <td>1</td>
            <td>Croatia</td>
        </tr>
        <tr>
            <td>1</td>
            <td>Cuba</td>
        </tr>
        <tr>
            <td>1</td>
            <td>Cyprus</td>
        </tr>
        <tr>
            <td>1</td>
            <td>Czech Republic</td>
        </tr>
        <tr>
            <td>1</td>
            <td>Denmark</td>
        </tr>
        <tr>
            <td>1</td>
            <td>Djibouti</td>
        </tr>
        <tr>
            <td>1</td>
            <td>Dominica</td>
        </tr>
        <tr>
            <td>1</td>
            <td>Dominican Republic</td>
        </tr>
        <tr>
            <td>1</td>
            <td>Ecuador</td>
        </tr>
        <tr>
            <td>1</td>
            <td>Egypt</td>
        </tr>
        <tr>
            <td>1</td>
            <td>El Salvador</td>
        </tr>
        <tr>
            <td>1</td>
            <td>Equatorial Guinea</td>
        </tr>
        <tr>
            <td>1</td>
            <td>Eritrea</td>
        </tr>
        <tr>
            <td>1</td>
            <td>Estonia</td>
        </tr>
        <tr>
            <td>1</td>
            <td>Ethiopia</td>
        </tr>
        <tr>
            <td>1</td>
            <td>Fiji</td>
        </tr>
        <tr>
            <td>1</td>
            <td>Finland</td>
        </tr>
        <tr>
            <td>1</td>
            <td>France</td>
        </tr>
        <tr>
            <td>1</td>
            <td>Gabon</td>
        </tr>
        <tr>
            <td>1</td>
            <td>Gambia, The</td>
        </tr>
        <tr>
            <td>1</td>
            <td>Georgia</td>
        </tr>
        <tr>
            <td>1</td>
            <td>Germany</td>
        </tr>
        <tr>
            <td>1</td>
            <td>Ghana</td>
        </tr>
        <tr>
            <td>1</td>
            <td>Greece</td>
        </tr>
        <tr>
            <td>1</td>
            <td>Grenada</td>
        </tr>
        <tr>
            <td>1</td>
            <td>Guatemala</td>
        </tr>
        <tr>
            <td>1</td>
            <td>Guinea</td>
        </tr>
        <tr>
            <td>1</td>
            <td>Guinea-Bissau</td>
        </tr>
        <tr>
            <td>1</td>
            <td>Guyana</td>
        </tr>
        <tr>
            <td>1</td>
            <td>Haiti</td>
        </tr>
        <tr>
            <td>1</td>
            <td>Honduras</td>
        </tr>
        <tr>
            <td>1</td>
            <td>Hungary</td>
        </tr>
        <tr>
            <td>1</td>
            <td>Iceland</td>
        </tr>
        <tr>
            <td>1</td>
            <td>Iran</td>
        </tr>
        <tr>
            <td>1</td>
            <td>Iraq</td>
        </tr>
        <tr>
            <td>1</td>
            <td>Ireland</td>
        </tr>
        <tr>
            <td>1</td>
            <td>Israel</td>
        </tr>
        <tr>
            <td>1</td>
            <td>Italy</td>
        </tr>
        <tr>
            <td>1</td>
            <td>Jamaica</td>
        </tr>
        <tr>
            <td>1</td>
            <td>Japan</td>
        </tr>
        <tr>
            <td>1</td>
            <td>Jordan</td>
        </tr>
        <tr>
            <td>1</td>
            <td>Kazakhstan</td>
        </tr>
        <tr>
            <td>1</td>
            <td>Kenya</td>
        </tr>
        <tr>
            <td>1</td>
            <td>Kiribati</td>
        </tr>
        <tr>
            <td>1</td>
            <td>Korea, North</td>
        </tr>
        <tr>
            <td>1</td>
            <td>Korea, South</td>
        </tr>
        <tr>
            <td>1</td>
            <td>Kosovo</td>
        </tr>
        <tr>
            <td>1</td>
            <td>Kuwait</td>
        </tr>
        <tr>
            <td>1</td>
            <td>Kyrgyzstan</td>
        </tr>
        <tr>
            <td>1</td>
            <td>Laos</td>
        </tr>
        <tr>
            <td>1</td>
            <td>Latvia</td>
        </tr>
        <tr>
            <td>1</td>
            <td>Lebanon</td>
        </tr>
        <tr>
            <td>1</td>
            <td>Lesotho</td>
        </tr>
        <tr>
            <td>1</td>
            <td>Liberia</td>
        </tr>
        <tr>
            <td>1</td>
            <td>Libya</td>
        </tr>
        <tr>
            <td>1</td>
            <td>Liechtenstein</td>
        </tr>
        <tr>
            <td>1</td>
            <td>Lithuania</td>
        </tr>
        <tr>
            <td>1</td>
            <td>Luxembourg</td>
        </tr>
        <tr>
            <td>1</td>
            <td>Macedonia</td>
        </tr>
        <tr>
            <td>1</td>
            <td>Madagascar</td>
        </tr>
        <tr>
            <td>1</td>
            <td>Malawi</td>
        </tr>
        <tr>
            <td>1</td>
            <td>Malaysia</td>
        </tr>
        <tr>
            <td>1</td>
            <td>Maldives</td>
        </tr>
        <tr>
            <td>1</td>
            <td>Mali</td>
        </tr>
        <tr>
            <td>1</td>
            <td>Malta</td>
        </tr>
        <tr>
            <td>1</td>
            <td>Marshall Islands</td>
        </tr>
        <tr>
            <td>1</td>
            <td>Mauritania</td>
        </tr>
        <tr>
            <td>1</td>
            <td>Mauritius</td>
        </tr>
        <tr>
            <td>1</td>
            <td>Mexico</td>
        </tr>
        <tr>
            <td>1</td>
            <td>Micronesia, Federated States of</td>
        </tr>
        <tr>
            <td>1</td>
            <td>Moldova</td>
        </tr>
        <tr>
            <td>1</td>
            <td>Monaco</td>
        </tr>
        <tr>
            <td>1</td>
            <td>Mongolia</td>
        </tr>
        <tr>
            <td>1</td>
            <td>Montenegro</td>
        </tr>
        <tr>
            <td>1</td>
            <td>Morocco</td>
        </tr>
        <tr>
            <td>1</td>
            <td>Mozambique</td>
        </tr>
        <tr>
            <td>1</td>
            <td>Namibia</td>
        </tr>
        <tr>
            <td>1</td>
            <td>Nauru</td>
        </tr>
        <tr>
            <td>1</td>
            <td>Nepal</td>
        </tr>
        <tr>
            <td>1</td>
            <td>Netherlands</td>
        </tr>
        <tr>
            <td>1</td>
            <td>New Zealand</td>
        </tr>
        <tr>
            <td>1</td>
            <td>Nicaragua</td>
        </tr>
        <tr>
            <td>1</td>
            <td>Niger</td>
        </tr>
        <tr>
            <td>1</td>
            <td>Norway</td>
        </tr>
        <tr>
            <td>1</td>
            <td>Oman</td>
        </tr>
        <tr>
            <td>1</td>
            <td>Palau</td>
        </tr>
        <tr>
            <td>1</td>
            <td>Panama</td>
        </tr>
        <tr>
            <td>1</td>
            <td>Papua New Guinea</td>
        </tr>
        <tr>
            <td>1</td>
            <td>Paraguay</td>
        </tr>
        <tr>
            <td>1</td>
            <td>Peru</td>
        </tr>
        <tr>
            <td>1</td>
            <td>Philippines</td>
        </tr>
        <tr>
            <td>1</td>
            <td>Poland</td>
        </tr>
        <tr>
            <td>1</td>
            <td>Portugal</td>
        </tr>
        <tr>
            <td>1</td>
            <td>Qatar</td>
        </tr>
        <tr>
            <td>1</td>
            <td>Romania</td>
        </tr>
        <tr>
            <td>1</td>
            <td>Rwanda</td>
        </tr>
        <tr>
            <td>1</td>
            <td>Saint Kitts and Nevis</td>
        </tr>
        <tr>
            <td>1</td>
            <td>Saint Lucia</td>
        </tr>
        <tr>
            <td>1</td>
            <td>Saint Vincent and the Grenadines</td>
        </tr>
        <tr>
            <td>1</td>
            <td>Samoa</td>
        </tr>
        <tr>
            <td>1</td>
            <td>San Marino</td>
        </tr>
        <tr>
            <td>1</td>
            <td>Sao Tome and Principe</td>
        </tr>
        <tr>
            <td>1</td>
            <td>Saudi Arabia</td>
        </tr>
        <tr>
            <td>1</td>
            <td>Senegal</td>
        </tr>
        <tr>
            <td>1</td>
            <td>Serbia</td>
        </tr>
        <tr>
            <td>1</td>
            <td>Seychelles</td>
        </tr>
        <tr>
            <td>1</td>
            <td>Sierra Leone</td>
        </tr>
        <tr>
            <td>1</td>
            <td>Singapore</td>
        </tr>
        <tr>
            <td>1</td>
            <td>Slovakia</td>
        </tr>
        <tr>
            <td>1</td>
            <td>Slovenia</td>
        </tr>
        <tr>
            <td>1</td>
            <td>Solomon Islands</td>
        </tr>
        <tr>
            <td>1</td>
            <td>Somalia</td>
        </tr>
        <tr>
            <td>1</td>
            <td>South Africa</td>
        </tr>
        <tr>
            <td>1</td>
            <td>South Sudan</td>
        </tr>
        <tr>
            <td>1</td>
            <td>Spain</td>
        </tr>
        <tr>
            <td>1</td>
            <td>Sri Lanka</td>
        </tr>
        <tr>
            <td>1</td>
            <td>Sudan</td>
        </tr>
        <tr>
            <td>1</td>
            <td>Suriname</td>
        </tr>
        <tr>
            <td>1</td>
            <td>Swaziland</td>
        </tr>
        <tr>
            <td>1</td>
            <td>Sweden</td>
        </tr>
        <tr>
            <td>1</td>
            <td>Switzerland</td>
        </tr>
        <tr>
            <td>1</td>
            <td>Syria</td>
        </tr>
        <tr>
            <td>1</td>
            <td>Tajikistan</td>
        </tr>
        <tr>
            <td>1</td>
            <td>Tanzania</td>
        </tr>
        <tr>
            <td>1</td>
            <td>Thailand</td>
        </tr>
        <tr>
            <td>1</td>
            <td>Timor-Leste</td>
        </tr>
        <tr>
            <td>1</td>
            <td>Togo</td>
        </tr>
        <tr>
            <td>1</td>
            <td>Tonga</td>
        </tr>
        <tr>
            <td>1</td>
            <td>Trinidad and Tobago</td>
        </tr>
        <tr>
            <td>1</td>
            <td>Tunisia</td>
        </tr>
        <tr>
            <td>1</td>
            <td>Turkey</td>
        </tr>
        <tr>
            <td>1</td>
            <td>Turkmenistan</td>
        </tr>
        <tr>
            <td>1</td>
            <td>Tuvalu</td>
        </tr>
        <tr>
            <td>1</td>
            <td>Uganda</td>
        </tr>
        <tr>
            <td>1</td>
            <td>Ukraine</td>
        </tr>
        <tr>
            <td>1</td>
            <td>United Arab Emirates</td>
        </tr>
        <tr>
            <td>1</td>
            <td>United Kingdom</td>
        </tr>
        <tr>
            <td>1</td>
            <td>Uruguay</td>
        </tr>
        <tr>
            <td>1</td>
            <td>Uzbekistan</td>
        </tr>
        <tr>
            <td>1</td>
            <td>Vanuatu</td>
        </tr>
        <tr>
            <td>1</td>
            <td>Holy See (Vatican City)</td>
        </tr>
        <tr>
            <td>1</td>
            <td>Venezuela</td>
        </tr>
        <tr>
            <td>1</td>
            <td>Vietnam</td>
        </tr>
        <tr>
            <td>1</td>
            <td>Yemen</td>
        </tr>
        <tr>
            <td>1</td>
            <td>Zambia</td>
        </tr>
        <tr>
            <td>1</td>
            <td>Zimbabwe</td>
        </tr>
        <tr>
            <td>1</td>
            <td>Taiwan</td>
        </tr>
        <tr>
            <td>1</td>
            <td>Christmas Island</td>
        </tr>
        <tr>
            <td>1</td>
            <td>Cocos (Keeling) Islands</td>
        </tr>
        <tr>
            <td>1</td>
            <td>Norfolk Island</td>
        </tr>
        <tr>
            <td>1</td>
            <td>Hong Kong</td>
        </tr>
        <tr>
            <td>1</td>
            <td>Macau</td>
        </tr>
        <tr>
            <td>1</td>
            <td>Faroe Islands</td>
        </tr>
        <tr>
            <td>1</td>
            <td>Greenland</td>
        </tr>
        <tr>
            <td>1</td>
            <td>French Polynesia</td>
        </tr>
        <tr>
            <td>1</td>
            <td>New Caledonia</td>
        </tr>
        <tr>
            <td>1</td>
            <td>Saint Barthelemy</td>
        </tr>
        <tr>
            <td>1</td>
            <td>Saint Martin</td>
        </tr>
        <tr>
            <td>1</td>
            <td>Saint Pierre and Miquelon</td>
        </tr>
        <tr>
            <td>1</td>
            <td>Wallis and Futuna</td>
        </tr>
        <tr>
            <td>1</td>
            <td>Aruba</td>
        </tr>
        <tr>
            <td>1</td>
            <td>Curacao</td>
        </tr>
        <tr>
            <td>1</td>
            <td>Sint Maarten</td>
        </tr>
        <tr>
            <td>1</td>
            <td>Cook Islands</td>
        </tr>
        <tr>
            <td>1</td>
            <td>Niue</td>
        </tr>
        <tr>
            <td>1</td>
            <td>Tokelau</td>
        </tr>
        <tr>
            <td>1</td>
            <td>Svalbard</td>
        </tr>
        <tr>
            <td>1</td>
            <td>Akrotiri</td>
        </tr>
        <tr>
            <td>1</td>
            <td>Anguilla</td>
        </tr>
        <tr>
            <td>1</td>
            <td>Bermuda</td>
        </tr>
        <tr>
            <td>1</td>
            <td>British Virgin Islands</td>
        </tr>
        <tr>
            <td>1</td>
            <td>Cayman Islands</td>
        </tr>
        <tr>
            <td>1</td>
            <td>Dhekelia</td>
        </tr>
        <tr>
            <td>1</td>
            <td>Falkland Islands (Islas Malvinas)</td>
        </tr>
        <tr>
            <td>1</td>
            <td>Gibraltar</td>
        </tr>
        <tr>
            <td>1</td>
            <td>Guernsey</td>
        </tr>
        <tr>
            <td>1</td>
            <td>Jersey</td>
        </tr>
        <tr>
            <td>1</td>
            <td>Isle of Man</td>
        </tr>
        <tr>
            <td>1</td>
            <td>Montserrat</td>
        </tr>
        <tr>
            <td>1</td>
            <td>Pitcairn Islands</td>
        </tr>
        <tr>
            <td>1</td>
            <td>Saint Helena, Ascension, and Tristan da Cunha</td>
        </tr>
        <tr>
            <td>1</td>
            <td>Turks and Caicos Islands</td>
        </tr>
        <tr>
            <td>1</td>
            <td>American Samoa</td>
        </tr>
        <tr>
            <td>1</td>
            <td>Guam</td>
        </tr>
        <tr>
            <td>1</td>
            <td>Northern Mariana Islands</td>
        </tr>
        <tr>
            <td>1</td>
            <td>Puerto Rico</td>
        </tr>
        <tr>
            <td>1</td>
            <td>Virgin Islands</td>
        </tr>
        <tr>
            <td>1</td>
            <td>Antarctica</td>
        </tr>
        <tr>
            <td>1</td>
            <td>Gaza Strip</td>
        </tr>
        <tr>
            <td>1</td>
            <td>West Bank</td>
        </tr>
        <tr>
            <td>1</td>
            <td>Afghanistan</td>
        </tr>
        <tr>
            <td>1</td>
            <td>Western Sahara</td>
        </tr>
        <tr>
            <td>1</td>
            <td>Albania</td>
        </tr>
        <tr>
            <td>1</td>
            <td>Algeria</td>
        </tr>
        <tr>
            <td>1</td>
            <td>Andorra</td>
        </tr>
        <tr>
            <td>1</td>
            <td>Angola</td>
        </tr>
        <tr>
            <td>1</td>
            <td>Antigua and Barbuda</td>
        </tr>
        <tr>
            <td>1</td>
            <td>Argentina</td>
        </tr>
        <tr>
            <td>1</td>
            <td>Armenia</td>
        </tr>
        <tr>
            <td>1</td>
            <td>Australia</td>
        </tr>
        <tr>
            <td>1</td>
            <td>Austria</td>
        </tr>
    </tbody>
</table>




```sql
%%sql

with bins as (select
	country_code,
	width_bucket(population, 0, 1400000000, 10) as buckets
from world_pop.population p 
where country_code <> 'xx' and population is not null)
select
	buckets,
	count(country_code)
from bins
group by buckets
order by buckets desc;
```

     * postgresql://postgres:***@localhost:5432/master
    6 rows affected.
    




<table>
    <thead>
        <tr>
            <th>buckets</th>
            <th>count</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>10</td>
            <td>1</td>
        </tr>
        <tr>
            <td>9</td>
            <td>1</td>
        </tr>
        <tr>
            <td>4</td>
            <td>1</td>
        </tr>
        <tr>
            <td>3</td>
            <td>1</td>
        </tr>
        <tr>
            <td>2</td>
            <td>6</td>
        </tr>
        <tr>
            <td>1</td>
            <td>231</td>
        </tr>
    </tbody>
</table>



We can see that majority of countries fall into first bucket! And there are no countries in between bucket 5 and 8.
We can see that China and India are definetely outliers due to high population.

The second query is:


```sql
%%sql

select
	country_name,
	percent_rank() over(order by population) as pct_rank
from world_pop.population p
inner join world_pop.country c
on p.country_code = c.country_code 
where p.country_code <> 'xx' and population is not null;
```

     * postgresql://postgres:***@localhost:5432/master
    241 rows affected.
    




<table>
    <thead>
        <tr>
            <th>country_name</th>
            <th>pct_rank</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>Antarctica</td>
            <td>0.0</td>
        </tr>
        <tr>
            <td>Pitcairn Islands</td>
            <td>0.004166666666666667</td>
        </tr>
        <tr>
            <td>Cocos (Keeling) Islands</td>
            <td>0.008333333333333333</td>
        </tr>
        <tr>
            <td>Holy See (Vatican City)</td>
            <td>0.0125</td>
        </tr>
        <tr>
            <td>Niue</td>
            <td>0.016666666666666666</td>
        </tr>
        <tr>
            <td>Tokelau</td>
            <td>0.020833333333333332</td>
        </tr>
        <tr>
            <td>Christmas Island</td>
            <td>0.025</td>
        </tr>
        <tr>
            <td>Svalbard</td>
            <td>0.029166666666666667</td>
        </tr>
        <tr>
            <td>Norfolk Island</td>
            <td>0.03333333333333333</td>
        </tr>
        <tr>
            <td>Falkland Islands (Islas Malvinas)</td>
            <td>0.0375</td>
        </tr>
        <tr>
            <td>Montserrat</td>
            <td>0.041666666666666664</td>
        </tr>
        <tr>
            <td>Saint Pierre and Miquelon</td>
            <td>0.04583333333333333</td>
        </tr>
        <tr>
            <td>Saint Barthelemy</td>
            <td>0.05</td>
        </tr>
        <tr>
            <td>Saint Helena, Ascension, and Tristan da Cunha</td>
            <td>0.05416666666666667</td>
        </tr>
        <tr>
            <td>Nauru</td>
            <td>0.058333333333333334</td>
        </tr>
        <tr>
            <td>Cook Islands</td>
            <td>0.0625</td>
        </tr>
        <tr>
            <td>Tuvalu</td>
            <td>0.06666666666666667</td>
        </tr>
        <tr>
            <td>Wallis and Futuna</td>
            <td>0.07083333333333333</td>
        </tr>
        <tr>
            <td>Akrotiri</td>
            <td>0.075</td>
        </tr>
        <tr>
            <td>Dhekelia</td>
            <td>0.075</td>
        </tr>
        <tr>
            <td>Anguilla</td>
            <td>0.08333333333333333</td>
        </tr>
        <tr>
            <td>Palau</td>
            <td>0.0875</td>
        </tr>
        <tr>
            <td>Gibraltar</td>
            <td>0.09166666666666666</td>
        </tr>
        <tr>
            <td>Monaco</td>
            <td>0.09583333333333334</td>
        </tr>
        <tr>
            <td>Saint Martin</td>
            <td>0.1</td>
        </tr>
        <tr>
            <td>San Marino</td>
            <td>0.10416666666666667</td>
        </tr>
        <tr>
            <td>British Virgin Islands</td>
            <td>0.10833333333333334</td>
        </tr>
        <tr>
            <td>Liechtenstein</td>
            <td>0.1125</td>
        </tr>
        <tr>
            <td>Sint Maarten</td>
            <td>0.11666666666666667</td>
        </tr>
        <tr>
            <td>Faroe Islands</td>
            <td>0.12083333333333333</td>
        </tr>
        <tr>
            <td>Turks and Caicos Islands</td>
            <td>0.125</td>
        </tr>
        <tr>
            <td>Saint Kitts and Nevis</td>
            <td>0.12916666666666668</td>
        </tr>
        <tr>
            <td>Northern Mariana Islands</td>
            <td>0.13333333333333333</td>
        </tr>
        <tr>
            <td>American Samoa</td>
            <td>0.1375</td>
        </tr>
        <tr>
            <td>Cayman Islands</td>
            <td>0.14166666666666666</td>
        </tr>
        <tr>
            <td>Greenland</td>
            <td>0.14583333333333334</td>
        </tr>
        <tr>
            <td>Guernsey</td>
            <td>0.15</td>
        </tr>
        <tr>
            <td>Bermuda</td>
            <td>0.15416666666666667</td>
        </tr>
        <tr>
            <td>Marshall Islands</td>
            <td>0.15833333333333333</td>
        </tr>
        <tr>
            <td>Dominica</td>
            <td>0.1625</td>
        </tr>
        <tr>
            <td>Andorra</td>
            <td>0.16666666666666666</td>
        </tr>
        <tr>
            <td>Isle of Man</td>
            <td>0.17083333333333334</td>
        </tr>
        <tr>
            <td>Seychelles</td>
            <td>0.175</td>
        </tr>
        <tr>
            <td>Antigua and Barbuda</td>
            <td>0.17916666666666667</td>
        </tr>
        <tr>
            <td>Jersey</td>
            <td>0.18333333333333332</td>
        </tr>
        <tr>
            <td>Saint Vincent and the Grenadines</td>
            <td>0.1875</td>
        </tr>
        <tr>
            <td>Virgin Islands</td>
            <td>0.19166666666666668</td>
        </tr>
        <tr>
            <td>Micronesia, Federated States of</td>
            <td>0.19583333333333333</td>
        </tr>
        <tr>
            <td>Kiribati</td>
            <td>0.2</td>
        </tr>
        <tr>
            <td>Tonga</td>
            <td>0.20416666666666666</td>
        </tr>
        <tr>
            <td>Grenada</td>
            <td>0.20833333333333334</td>
        </tr>
        <tr>
            <td>Aruba</td>
            <td>0.2125</td>
        </tr>
        <tr>
            <td>Curacao</td>
            <td>0.21666666666666667</td>
        </tr>
        <tr>
            <td>Guam</td>
            <td>0.22083333333333333</td>
        </tr>
        <tr>
            <td>Saint Lucia</td>
            <td>0.225</td>
        </tr>
        <tr>
            <td>Sao Tome and Principe</td>
            <td>0.22916666666666666</td>
        </tr>
        <tr>
            <td>Samoa</td>
            <td>0.23333333333333334</td>
        </tr>
        <tr>
            <td>New Caledonia</td>
            <td>0.2375</td>
        </tr>
        <tr>
            <td>Vanuatu</td>
            <td>0.24166666666666667</td>
        </tr>
        <tr>
            <td>French Polynesia</td>
            <td>0.24583333333333332</td>
        </tr>
        <tr>
            <td>Barbados</td>
            <td>0.25</td>
        </tr>
        <tr>
            <td>Bahamas, The</td>
            <td>0.25416666666666665</td>
        </tr>
        <tr>
            <td>Iceland</td>
            <td>0.25833333333333336</td>
        </tr>
        <tr>
            <td>Belize</td>
            <td>0.2625</td>
        </tr>
        <tr>
            <td>Maldives</td>
            <td>0.26666666666666666</td>
        </tr>
        <tr>
            <td>Malta</td>
            <td>0.2708333333333333</td>
        </tr>
        <tr>
            <td>Brunei</td>
            <td>0.275</td>
        </tr>
        <tr>
            <td>Cabo Verde</td>
            <td>0.2791666666666667</td>
        </tr>
        <tr>
            <td>Luxembourg</td>
            <td>0.2833333333333333</td>
        </tr>
        <tr>
            <td>Western Sahara</td>
            <td>0.2875</td>
        </tr>
        <tr>
            <td>Suriname</td>
            <td>0.2916666666666667</td>
        </tr>
        <tr>
            <td>Macau</td>
            <td>0.29583333333333334</td>
        </tr>
        <tr>
            <td>Solomon Islands</td>
            <td>0.3</td>
        </tr>
        <tr>
            <td>Montenegro</td>
            <td>0.30416666666666664</td>
        </tr>
        <tr>
            <td>Guyana</td>
            <td>0.30833333333333335</td>
        </tr>
        <tr>
            <td>Equatorial Guinea</td>
            <td>0.3125</td>
        </tr>
        <tr>
            <td>Bhutan</td>
            <td>0.31666666666666665</td>
        </tr>
        <tr>
            <td>Comoros</td>
            <td>0.32083333333333336</td>
        </tr>
        <tr>
            <td>Djibouti</td>
            <td>0.325</td>
        </tr>
        <tr>
            <td>Fiji</td>
            <td>0.32916666666666666</td>
        </tr>
        <tr>
            <td>Cyprus</td>
            <td>0.3333333333333333</td>
        </tr>
        <tr>
            <td>Trinidad and Tobago</td>
            <td>0.3375</td>
        </tr>
        <tr>
            <td>Timor-Leste</td>
            <td>0.3416666666666667</td>
        </tr>
        <tr>
            <td>Estonia</td>
            <td>0.3458333333333333</td>
        </tr>
        <tr>
            <td>Mauritius</td>
            <td>0.35</td>
        </tr>
        <tr>
            <td>Bahrain</td>
            <td>0.3541666666666667</td>
        </tr>
        <tr>
            <td>Swaziland</td>
            <td>0.35833333333333334</td>
        </tr>
        <tr>
            <td>Gabon</td>
            <td>0.3625</td>
        </tr>
        <tr>
            <td>Guinea-Bissau</td>
            <td>0.36666666666666664</td>
        </tr>
        <tr>
            <td>Gaza Strip</td>
            <td>0.37083333333333335</td>
        </tr>
        <tr>
            <td>Kosovo</td>
            <td>0.375</td>
        </tr>
        <tr>
            <td>Lesotho</td>
            <td>0.37916666666666665</td>
        </tr>
        <tr>
            <td>Gambia, The</td>
            <td>0.38333333333333336</td>
        </tr>
        <tr>
            <td>Slovenia</td>
            <td>0.3875</td>
        </tr>
        <tr>
            <td>Latvia</td>
            <td>0.39166666666666666</td>
        </tr>
        <tr>
            <td>Macedonia</td>
            <td>0.3958333333333333</td>
        </tr>
        <tr>
            <td>Botswana</td>
            <td>0.4</td>
        </tr>
        <tr>
            <td>Qatar</td>
            <td>0.4041666666666667</td>
        </tr>
        <tr>
            <td>Namibia</td>
            <td>0.4083333333333333</td>
        </tr>
        <tr>
            <td>West Bank</td>
            <td>0.4125</td>
        </tr>
        <tr>
            <td>Kuwait</td>
            <td>0.4166666666666667</td>
        </tr>
        <tr>
            <td>Lithuania</td>
            <td>0.42083333333333334</td>
        </tr>
        <tr>
            <td>Jamaica</td>
            <td>0.425</td>
        </tr>
        <tr>
            <td>Mongolia</td>
            <td>0.42916666666666664</td>
        </tr>
        <tr>
            <td>Albania</td>
            <td>0.43333333333333335</td>
        </tr>
        <tr>
            <td>Armenia</td>
            <td>0.4375</td>
        </tr>
        <tr>
            <td>Oman</td>
            <td>0.44166666666666665</td>
        </tr>
        <tr>
            <td>Uruguay</td>
            <td>0.44583333333333336</td>
        </tr>
        <tr>
            <td>Moldova</td>
            <td>0.45</td>
        </tr>
        <tr>
            <td>Mauritania</td>
            <td>0.45416666666666666</td>
        </tr>
        <tr>
            <td>Puerto Rico</td>
            <td>0.4583333333333333</td>
        </tr>
        <tr>
            <td>Panama</td>
            <td>0.4625</td>
        </tr>
        <tr>
            <td>Bosnia and Herzegovina</td>
            <td>0.4666666666666667</td>
        </tr>
        <tr>
            <td>Liberia</td>
            <td>0.4708333333333333</td>
        </tr>
        <tr>
            <td>New Zealand</td>
            <td>0.475</td>
        </tr>
        <tr>
            <td>Croatia</td>
            <td>0.4791666666666667</td>
        </tr>
        <tr>
            <td>Congo, Republic of the</td>
            <td>0.48333333333333334</td>
        </tr>
        <tr>
            <td>Costa Rica</td>
            <td>0.4875</td>
        </tr>
        <tr>
            <td>Ireland</td>
            <td>0.49166666666666664</td>
        </tr>
        <tr>
            <td>Georgia</td>
            <td>0.49583333333333335</td>
        </tr>
        <tr>
            <td>Norway</td>
            <td>0.5</td>
        </tr>
        <tr>
            <td>Turkmenistan</td>
            <td>0.5041666666666667</td>
        </tr>
        <tr>
            <td>Central African Republic</td>
            <td>0.5083333333333333</td>
        </tr>
        <tr>
            <td>Slovakia</td>
            <td>0.5125</td>
        </tr>
        <tr>
            <td>Finland</td>
            <td>0.5166666666666667</td>
        </tr>
        <tr>
            <td>Denmark</td>
            <td>0.5208333333333334</td>
        </tr>
        <tr>
            <td>Kyrgyzstan</td>
            <td>0.525</td>
        </tr>
        <tr>
            <td>Singapore</td>
            <td>0.5291666666666667</td>
        </tr>
        <tr>
            <td>United Arab Emirates</td>
            <td>0.5333333333333333</td>
        </tr>
        <tr>
            <td>Sierra Leone</td>
            <td>0.5375</td>
        </tr>
        <tr>
            <td>Nicaragua</td>
            <td>0.5416666666666666</td>
        </tr>
        <tr>
            <td>El Salvador</td>
            <td>0.5458333333333333</td>
        </tr>
        <tr>
            <td>Lebanon</td>
            <td>0.55</td>
        </tr>
        <tr>
            <td>Libya</td>
            <td>0.5541666666666667</td>
        </tr>
        <tr>
            <td>Eritrea</td>
            <td>0.5583333333333333</td>
        </tr>
        <tr>
            <td>Papua New Guinea</td>
            <td>0.5625</td>
        </tr>
        <tr>
            <td>Paraguay</td>
            <td>0.5666666666666667</td>
        </tr>
        <tr>
            <td>Laos</td>
            <td>0.5708333333333333</td>
        </tr>
        <tr>
            <td>Hong Kong</td>
            <td>0.575</td>
        </tr>
        <tr>
            <td>Serbia</td>
            <td>0.5791666666666667</td>
        </tr>
        <tr>
            <td>Bulgaria</td>
            <td>0.5833333333333334</td>
        </tr>
        <tr>
            <td>Togo</td>
            <td>0.5875</td>
        </tr>
        <tr>
            <td>Israel</td>
            <td>0.5916666666666667</td>
        </tr>
        <tr>
            <td>Jordan</td>
            <td>0.5958333333333333</td>
        </tr>
        <tr>
            <td>Switzerland</td>
            <td>0.6</td>
        </tr>
        <tr>
            <td>Tajikistan</td>
            <td>0.6041666666666666</td>
        </tr>
        <tr>
            <td>Austria</td>
            <td>0.6083333333333333</td>
        </tr>
        <tr>
            <td>Honduras</td>
            <td>0.6125</td>
        </tr>
        <tr>
            <td>Belarus</td>
            <td>0.6166666666666667</td>
        </tr>
        <tr>
            <td>Azerbaijan</td>
            <td>0.6208333333333333</td>
        </tr>
        <tr>
            <td>Sweden</td>
            <td>0.625</td>
        </tr>
        <tr>
            <td>Hungary</td>
            <td>0.6291666666666667</td>
        </tr>
        <tr>
            <td>Haiti</td>
            <td>0.6333333333333333</td>
        </tr>
        <tr>
            <td>Benin</td>
            <td>0.6375</td>
        </tr>
        <tr>
            <td>Dominican Republic</td>
            <td>0.6416666666666667</td>
        </tr>
        <tr>
            <td>Somalia</td>
            <td>0.6458333333333334</td>
        </tr>
        <tr>
            <td>Czech Republic</td>
            <td>0.65</td>
        </tr>
        <tr>
            <td>Burundi</td>
            <td>0.6541666666666667</td>
        </tr>
        <tr>
            <td>Greece</td>
            <td>0.6583333333333333</td>
        </tr>
        <tr>
            <td>Bolivia</td>
            <td>0.6625</td>
        </tr>
        <tr>
            <td>Portugal</td>
            <td>0.6666666666666666</td>
        </tr>
        <tr>
            <td>Cuba</td>
            <td>0.6708333333333333</td>
        </tr>
        <tr>
            <td>Tunisia</td>
            <td>0.675</td>
        </tr>
        <tr>
            <td>Belgium</td>
            <td>0.6791666666666667</td>
        </tr>
        <tr>
            <td>Chad</td>
            <td>0.6833333333333333</td>
        </tr>
        <tr>
            <td>Guinea</td>
            <td>0.6875</td>
        </tr>
        <tr>
            <td>South Sudan</td>
            <td>0.6916666666666667</td>
        </tr>
        <tr>
            <td>Rwanda</td>
            <td>0.6958333333333333</td>
        </tr>
        <tr>
            <td>Senegal</td>
            <td>0.7</td>
        </tr>
        <tr>
            <td>Zimbabwe</td>
            <td>0.7041666666666667</td>
        </tr>
        <tr>
            <td>Guatemala</td>
            <td>0.7083333333333334</td>
        </tr>
        <tr>
            <td>Zambia</td>
            <td>0.7125</td>
        </tr>
        <tr>
            <td>Cambodia</td>
            <td>0.7166666666666667</td>
        </tr>
        <tr>
            <td>Ecuador</td>
            <td>0.7208333333333333</td>
        </tr>
        <tr>
            <td>Netherlands</td>
            <td>0.725</td>
        </tr>
        <tr>
            <td>Mali</td>
            <td>0.7291666666666666</td>
        </tr>
        <tr>
            <td>Syria</td>
            <td>0.7333333333333333</td>
        </tr>
        <tr>
            <td>Chile</td>
            <td>0.7375</td>
        </tr>
        <tr>
            <td>Malawi</td>
            <td>0.7416666666666667</td>
        </tr>
        <tr>
            <td>Niger</td>
            <td>0.7458333333333333</td>
        </tr>
        <tr>
            <td>Kazakhstan</td>
            <td>0.75</td>
        </tr>
        <tr>
            <td>Burkina Faso</td>
            <td>0.7541666666666667</td>
        </tr>
        <tr>
            <td>Angola</td>
            <td>0.7583333333333333</td>
        </tr>
        <tr>
            <td>Romania</td>
            <td>0.7625</td>
        </tr>
        <tr>
            <td>Sri Lanka</td>
            <td>0.7666666666666667</td>
        </tr>
        <tr>
            <td>Australia</td>
            <td>0.7708333333333334</td>
        </tr>
        <tr>
            <td>Cote dIvoire</td>
            <td>0.775</td>
        </tr>
        <tr>
            <td>Taiwan</td>
            <td>0.7791666666666667</td>
        </tr>
        <tr>
            <td>Cameroon</td>
            <td>0.7833333333333333</td>
        </tr>
        <tr>
            <td>Madagascar</td>
            <td>0.7875</td>
        </tr>
        <tr>
            <td>Korea, North</td>
            <td>0.7916666666666666</td>
        </tr>
        <tr>
            <td>Mozambique</td>
            <td>0.7958333333333333</td>
        </tr>
        <tr>
            <td>Ghana</td>
            <td>0.8</td>
        </tr>
        <tr>
            <td>Yemen</td>
            <td>0.8041666666666667</td>
        </tr>
        <tr>
            <td>Saudi Arabia</td>
            <td>0.8083333333333333</td>
        </tr>
        <tr>
            <td>Uzbekistan</td>
            <td>0.8125</td>
        </tr>
        <tr>
            <td>Venezuela</td>
            <td>0.8166666666666667</td>
        </tr>
        <tr>
            <td>Peru</td>
            <td>0.8208333333333333</td>
        </tr>
        <tr>
            <td>Malaysia</td>
            <td>0.825</td>
        </tr>
        <tr>
            <td>Nepal</td>
            <td>0.8291666666666667</td>
        </tr>
        <tr>
            <td>Afghanistan</td>
            <td>0.8333333333333334</td>
        </tr>
        <tr>
            <td>Morocco</td>
            <td>0.8375</td>
        </tr>
        <tr>
            <td>Canada</td>
            <td>0.8416666666666667</td>
        </tr>
        <tr>
            <td>Sudan</td>
            <td>0.8458333333333333</td>
        </tr>
        <tr>
            <td>Iraq</td>
            <td>0.85</td>
        </tr>
        <tr>
            <td>Uganda</td>
            <td>0.8541666666666666</td>
        </tr>
        <tr>
            <td>Poland</td>
            <td>0.8583333333333333</td>
        </tr>
        <tr>
            <td>Algeria</td>
            <td>0.8625</td>
        </tr>
        <tr>
            <td>Argentina</td>
            <td>0.8666666666666667</td>
        </tr>
        <tr>
            <td>Ukraine</td>
            <td>0.8708333333333333</td>
        </tr>
        <tr>
            <td>Kenya</td>
            <td>0.875</td>
        </tr>
        <tr>
            <td>Colombia</td>
            <td>0.8791666666666667</td>
        </tr>
        <tr>
            <td>Spain</td>
            <td>0.8833333333333333</td>
        </tr>
        <tr>
            <td>Korea, South</td>
            <td>0.8875</td>
        </tr>
        <tr>
            <td>Tanzania</td>
            <td>0.8916666666666667</td>
        </tr>
        <tr>
            <td>South Africa</td>
            <td>0.8958333333333334</td>
        </tr>
        <tr>
            <td>Burma</td>
            <td>0.9</td>
        </tr>
        <tr>
            <td>Italy</td>
            <td>0.9041666666666667</td>
        </tr>
        <tr>
            <td>United Kingdom</td>
            <td>0.9083333333333333</td>
        </tr>
        <tr>
            <td>France</td>
            <td>0.9125</td>
        </tr>
        <tr>
            <td>Thailand</td>
            <td>0.9166666666666666</td>
        </tr>
        <tr>
            <td>Congo, Democratic Republic of the</td>
            <td>0.9208333333333333</td>
        </tr>
        <tr>
            <td>Turkey</td>
            <td>0.925</td>
        </tr>
        <tr>
            <td>Germany</td>
            <td>0.9291666666666667</td>
        </tr>
        <tr>
            <td>Iran</td>
            <td>0.9333333333333333</td>
        </tr>
        <tr>
            <td>Egypt</td>
            <td>0.9375</td>
        </tr>
        <tr>
            <td>Vietnam</td>
            <td>0.9416666666666667</td>
        </tr>
        <tr>
            <td>Ethiopia</td>
            <td>0.9458333333333333</td>
        </tr>
        <tr>
            <td>Philippines</td>
            <td>0.95</td>
        </tr>
        <tr>
            <td>Mexico</td>
            <td>0.9541666666666667</td>
        </tr>
        <tr>
            <td>Japan</td>
            <td>0.9583333333333334</td>
        </tr>
        <tr>
            <td>Russia</td>
            <td>0.9625</td>
        </tr>
        <tr>
            <td>Bangladesh</td>
            <td>0.9666666666666667</td>
        </tr>
        <tr>
            <td>Nigeria</td>
            <td>0.9708333333333333</td>
        </tr>
        <tr>
            <td>Pakistan</td>
            <td>0.975</td>
        </tr>
        <tr>
            <td>Brazil</td>
            <td>0.9791666666666666</td>
        </tr>
        <tr>
            <td>Indonesia</td>
            <td>0.9833333333333333</td>
        </tr>
        <tr>
            <td>United States</td>
            <td>0.9875</td>
        </tr>
        <tr>
            <td>European Union</td>
            <td>0.9916666666666667</td>
        </tr>
        <tr>
            <td>India</td>
            <td>0.9958333333333333</td>
        </tr>
        <tr>
            <td>China</td>
            <td>1.0</td>
        </tr>
    </tbody>
</table>



This method doesnt showcase as much difference between, for example, United States and China. However, we can also see some outliers on the other side, like Antarctice, Pitcairm Islands and Cocos Island.

#### Finding Densely Populated Countries


```sql
%%sql

with density_table as (select
	country_name,
	floor(population/area_land::decimal) as density
from world_pop.population p
inner join world_pop.country c 
on p.country_code = c.country_code 
where population <> 0 and population is not null and area_land <> 0
order by density desc)
select
	country_name,
	density,
	rank() over (order by density desc) as rank
from density_table;
```

     * postgresql://postgres:***@localhost:5432/master
    232 rows affected.
    




<table>
    <thead>
        <tr>
            <th>country_name</th>
            <th>density</th>
            <th>rank</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>Macau</td>
            <td>21168</td>
            <td>1</td>
        </tr>
        <tr>
            <td>Monaco</td>
            <td>15267</td>
            <td>2</td>
        </tr>
        <tr>
            <td>Singapore</td>
            <td>8259</td>
            <td>3</td>
        </tr>
        <tr>
            <td>Hong Kong</td>
            <td>6655</td>
            <td>4</td>
        </tr>
        <tr>
            <td>Gaza Strip</td>
            <td>5191</td>
            <td>5</td>
        </tr>
        <tr>
            <td>Gibraltar</td>
            <td>4876</td>
            <td>6</td>
        </tr>
        <tr>
            <td>Bahrain</td>
            <td>1771</td>
            <td>7</td>
        </tr>
        <tr>
            <td>Maldives</td>
            <td>1319</td>
            <td>8</td>
        </tr>
        <tr>
            <td>Malta</td>
            <td>1310</td>
            <td>9</td>
        </tr>
        <tr>
            <td>Bermuda</td>
            <td>1299</td>
            <td>10</td>
        </tr>
        <tr>
            <td>Bangladesh</td>
            <td>1297</td>
            <td>11</td>
        </tr>
        <tr>
            <td>Sint Maarten</td>
            <td>1167</td>
            <td>12</td>
        </tr>
        <tr>
            <td>Guernsey</td>
            <td>847</td>
            <td>13</td>
        </tr>
        <tr>
            <td>Jersey</td>
            <td>838</td>
            <td>14</td>
        </tr>
        <tr>
            <td>Taiwan</td>
            <td>725</td>
            <td>15</td>
        </tr>
        <tr>
            <td>Barbados</td>
            <td>675</td>
            <td>16</td>
        </tr>
        <tr>
            <td>Mauritius</td>
            <td>660</td>
            <td>17</td>
        </tr>
        <tr>
            <td>Aruba</td>
            <td>623</td>
            <td>18</td>
        </tr>
        <tr>
            <td>Lebanon</td>
            <td>604</td>
            <td>19</td>
        </tr>
        <tr>
            <td>Saint Martin</td>
            <td>588</td>
            <td>20</td>
        </tr>
        <tr>
            <td>San Marino</td>
            <td>541</td>
            <td>21</td>
        </tr>
        <tr>
            <td>Rwanda</td>
            <td>513</td>
            <td>22</td>
        </tr>
        <tr>
            <td>Korea, South</td>
            <td>506</td>
            <td>23</td>
        </tr>
        <tr>
            <td>Netherlands</td>
            <td>500</td>
            <td>24</td>
        </tr>
        <tr>
            <td>West Bank</td>
            <td>493</td>
            <td>25</td>
        </tr>
        <tr>
            <td>Nauru</td>
            <td>454</td>
            <td>26</td>
        </tr>
        <tr>
            <td>India</td>
            <td>420</td>
            <td>27</td>
        </tr>
        <tr>
            <td>Burundi</td>
            <td>418</td>
            <td>28</td>
        </tr>
        <tr>
            <td>Tuvalu</td>
            <td>418</td>
            <td>28</td>
        </tr>
        <tr>
            <td>Puerto Rico</td>
            <td>405</td>
            <td>30</td>
        </tr>
        <tr>
            <td>Marshall Islands</td>
            <td>398</td>
            <td>31</td>
        </tr>
        <tr>
            <td>Israel</td>
            <td>395</td>
            <td>32</td>
        </tr>
        <tr>
            <td>Belgium</td>
            <td>374</td>
            <td>33</td>
        </tr>
        <tr>
            <td>Haiti</td>
            <td>366</td>
            <td>34</td>
        </tr>
        <tr>
            <td>Comoros</td>
            <td>349</td>
            <td>35</td>
        </tr>
        <tr>
            <td>Japan</td>
            <td>348</td>
            <td>36</td>
        </tr>
        <tr>
            <td>Sri Lanka</td>
            <td>341</td>
            <td>37</td>
        </tr>
        <tr>
            <td>Philippines</td>
            <td>338</td>
            <td>38</td>
        </tr>
        <tr>
            <td>Curacao</td>
            <td>334</td>
            <td>39</td>
        </tr>
        <tr>
            <td>Grenada</td>
            <td>321</td>
            <td>40</td>
        </tr>
        <tr>
            <td>Vietnam</td>
            <td>304</td>
            <td>41</td>
        </tr>
        <tr>
            <td>Virgin Islands</td>
            <td>299</td>
            <td>42</td>
        </tr>
        <tr>
            <td>Guam</td>
            <td>297</td>
            <td>43</td>
        </tr>
        <tr>
            <td>El Salvador</td>
            <td>296</td>
            <td>44</td>
        </tr>
        <tr>
            <td>American Samoa</td>
            <td>273</td>
            <td>45</td>
        </tr>
        <tr>
            <td>Jamaica</td>
            <td>272</td>
            <td>46</td>
        </tr>
        <tr>
            <td>Saint Lucia</td>
            <td>270</td>
            <td>47</td>
        </tr>
        <tr>
            <td>United Kingdom</td>
            <td>264</td>
            <td>48</td>
        </tr>
        <tr>
            <td>Saint Vincent and the Grenadines</td>
            <td>263</td>
            <td>49</td>
        </tr>
        <tr>
            <td>Pakistan</td>
            <td>258</td>
            <td>50</td>
        </tr>
        <tr>
            <td>Trinidad and Tobago</td>
            <td>238</td>
            <td>51</td>
        </tr>
        <tr>
            <td>Liechtenstein</td>
            <td>235</td>
            <td>52</td>
        </tr>
        <tr>
            <td>Germany</td>
            <td>231</td>
            <td>53</td>
        </tr>
        <tr>
            <td>British Virgin Islands</td>
            <td>221</td>
            <td>54</td>
        </tr>
        <tr>
            <td>Luxembourg</td>
            <td>220</td>
            <td>55</td>
        </tr>
        <tr>
            <td>Nepal</td>
            <td>220</td>
            <td>55</td>
        </tr>
        <tr>
            <td>Dominican Republic</td>
            <td>216</td>
            <td>57</td>
        </tr>
        <tr>
            <td>Cayman Islands</td>
            <td>212</td>
            <td>58</td>
        </tr>
        <tr>
            <td>Italy</td>
            <td>210</td>
            <td>59</td>
        </tr>
        <tr>
            <td>Antigua and Barbuda</td>
            <td>209</td>
            <td>60</td>
        </tr>
        <tr>
            <td>Korea, North</td>
            <td>207</td>
            <td>61</td>
        </tr>
        <tr>
            <td>Seychelles</td>
            <td>203</td>
            <td>62</td>
        </tr>
        <tr>
            <td>Switzerland</td>
            <td>203</td>
            <td>62</td>
        </tr>
        <tr>
            <td>Sao Tome and Principe</td>
            <td>201</td>
            <td>64</td>
        </tr>
        <tr>
            <td>Nigeria</td>
            <td>199</td>
            <td>65</td>
        </tr>
        <tr>
            <td>Saint Kitts and Nevis</td>
            <td>198</td>
            <td>66</td>
        </tr>
        <tr>
            <td>Gambia, The</td>
            <td>194</td>
            <td>67</td>
        </tr>
        <tr>
            <td>Malawi</td>
            <td>190</td>
            <td>68</td>
        </tr>
        <tr>
            <td>Qatar</td>
            <td>189</td>
            <td>69</td>
        </tr>
        <tr>
            <td>Uganda</td>
            <td>188</td>
            <td>70</td>
        </tr>
        <tr>
            <td>Andorra</td>
            <td>182</td>
            <td>71</td>
        </tr>
        <tr>
            <td>Anguilla</td>
            <td>180</td>
            <td>72</td>
        </tr>
        <tr>
            <td>Kosovo</td>
            <td>171</td>
            <td>73</td>
        </tr>
        <tr>
            <td>Kuwait</td>
            <td>156</td>
            <td>74</td>
        </tr>
        <tr>
            <td>Isle of Man</td>
            <td>153</td>
            <td>75</td>
        </tr>
        <tr>
            <td>Micronesia, Federated States of</td>
            <td>149</td>
            <td>76</td>
        </tr>
        <tr>
            <td>Tonga</td>
            <td>148</td>
            <td>77</td>
        </tr>
        <tr>
            <td>China</td>
            <td>146</td>
            <td>78</td>
        </tr>
        <tr>
            <td>Indonesia</td>
            <td>141</td>
            <td>79</td>
        </tr>
        <tr>
            <td>Guatemala</td>
            <td>139</td>
            <td>80</td>
        </tr>
        <tr>
            <td>Togo</td>
            <td>138</td>
            <td>81</td>
        </tr>
        <tr>
            <td>Czech Republic</td>
            <td>137</td>
            <td>82</td>
        </tr>
        <tr>
            <td>Cabo Verde</td>
            <td>135</td>
            <td>83</td>
        </tr>
        <tr>
            <td>Thailand</td>
            <td>133</td>
            <td>84</td>
        </tr>
        <tr>
            <td>Denmark</td>
            <td>131</td>
            <td>85</td>
        </tr>
        <tr>
            <td>Kiribati</td>
            <td>130</td>
            <td>86</td>
        </tr>
        <tr>
            <td>Cyprus</td>
            <td>128</td>
            <td>87</td>
        </tr>
        <tr>
            <td>Poland</td>
            <td>126</td>
            <td>88</td>
        </tr>
        <tr>
            <td>Azerbaijan</td>
            <td>118</td>
            <td>89</td>
        </tr>
        <tr>
            <td>Portugal</td>
            <td>118</td>
            <td>89</td>
        </tr>
        <tr>
            <td>Ghana</td>
            <td>115</td>
            <td>91</td>
        </tr>
        <tr>
            <td>Slovakia</td>
            <td>113</td>
            <td>92</td>
        </tr>
        <tr>
            <td>Northern Mariana Islands</td>
            <td>112</td>
            <td>93</td>
        </tr>
        <tr>
            <td>Tokelau</td>
            <td>111</td>
            <td>94</td>
        </tr>
        <tr>
            <td>Hungary</td>
            <td>110</td>
            <td>95</td>
        </tr>
        <tr>
            <td>Albania</td>
            <td>110</td>
            <td>95</td>
        </tr>
        <tr>
            <td>Wallis and Futuna</td>
            <td>109</td>
            <td>97</td>
        </tr>
        <tr>
            <td>Armenia</td>
            <td>108</td>
            <td>98</td>
        </tr>
        <tr>
            <td>Moldova</td>
            <td>107</td>
            <td>99</td>
        </tr>
        <tr>
            <td>Austria</td>
            <td>105</td>
            <td>100</td>
        </tr>
        <tr>
            <td>France</td>
            <td>103</td>
            <td>101</td>
        </tr>
        <tr>
            <td>Turkey</td>
            <td>103</td>
            <td>101</td>
        </tr>
        <tr>
            <td>Cuba</td>
            <td>100</td>
            <td>103</td>
        </tr>
        <tr>
            <td>Dominica</td>
            <td>98</td>
            <td>104</td>
        </tr>
        <tr>
            <td>Slovenia</td>
            <td>98</td>
            <td>104</td>
        </tr>
        <tr>
            <td>Spain</td>
            <td>96</td>
            <td>106</td>
        </tr>
        <tr>
            <td>Romania</td>
            <td>94</td>
            <td>107</td>
        </tr>
        <tr>
            <td>Costa Rica</td>
            <td>94</td>
            <td>107</td>
        </tr>
        <tr>
            <td>Benin</td>
            <td>94</td>
            <td>107</td>
        </tr>
        <tr>
            <td>Malaysia</td>
            <td>92</td>
            <td>110</td>
        </tr>
        <tr>
            <td>Serbia</td>
            <td>92</td>
            <td>110</td>
        </tr>
        <tr>
            <td>Syria</td>
            <td>92</td>
            <td>110</td>
        </tr>
        <tr>
            <td>Jordan</td>
            <td>91</td>
            <td>113</td>
        </tr>
        <tr>
            <td>Egypt</td>
            <td>88</td>
            <td>114</td>
        </tr>
        <tr>
            <td>Cambodia</td>
            <td>88</td>
            <td>114</td>
        </tr>
        <tr>
            <td>Burma</td>
            <td>86</td>
            <td>116</td>
        </tr>
        <tr>
            <td>Iraq</td>
            <td>84</td>
            <td>117</td>
        </tr>
        <tr>
            <td>Swaziland</td>
            <td>83</td>
            <td>118</td>
        </tr>
        <tr>
            <td>Timor-Leste</td>
            <td>82</td>
            <td>119</td>
        </tr>
        <tr>
            <td>Sierra Leone</td>
            <td>82</td>
            <td>119</td>
        </tr>
        <tr>
            <td>Greece</td>
            <td>82</td>
            <td>119</td>
        </tr>
        <tr>
            <td>Macedonia</td>
            <td>82</td>
            <td>119</td>
        </tr>
        <tr>
            <td>Brunei</td>
            <td>81</td>
            <td>123</td>
        </tr>
        <tr>
            <td>Kenya</td>
            <td>80</td>
            <td>124</td>
        </tr>
        <tr>
            <td>Croatia</td>
            <td>79</td>
            <td>125</td>
        </tr>
        <tr>
            <td>Honduras</td>
            <td>78</td>
            <td>126</td>
        </tr>
        <tr>
            <td>Ukraine</td>
            <td>76</td>
            <td>127</td>
        </tr>
        <tr>
            <td>Bosnia and Herzegovina</td>
            <td>75</td>
            <td>128</td>
        </tr>
        <tr>
            <td>Morocco</td>
            <td>74</td>
            <td>129</td>
        </tr>
        <tr>
            <td>French Polynesia</td>
            <td>73</td>
            <td>130</td>
        </tr>
        <tr>
            <td>Cote dIvoire</td>
            <td>73</td>
            <td>130</td>
        </tr>
        <tr>
            <td>Senegal</td>
            <td>72</td>
            <td>132</td>
        </tr>
        <tr>
            <td>Tunisia</td>
            <td>71</td>
            <td>133</td>
        </tr>
        <tr>
            <td>Ireland</td>
            <td>71</td>
            <td>133</td>
        </tr>
        <tr>
            <td>Georgia</td>
            <td>70</td>
            <td>135</td>
        </tr>
        <tr>
            <td>Samoa</td>
            <td>70</td>
            <td>135</td>
        </tr>
        <tr>
            <td>Burkina Faso</td>
            <td>69</td>
            <td>137</td>
        </tr>
        <tr>
            <td>United Arab Emirates</td>
            <td>69</td>
            <td>137</td>
        </tr>
        <tr>
            <td>Uzbekistan</td>
            <td>68</td>
            <td>139</td>
        </tr>
        <tr>
            <td>Bulgaria</td>
            <td>66</td>
            <td>140</td>
        </tr>
        <tr>
            <td>Eritrea</td>
            <td>64</td>
            <td>141</td>
        </tr>
        <tr>
            <td>Lesotho</td>
            <td>64</td>
            <td>141</td>
        </tr>
        <tr>
            <td>Saint Helena, Ascension, and Tristan da Cunha</td>
            <td>63</td>
            <td>143</td>
        </tr>
        <tr>
            <td>Mexico</td>
            <td>62</td>
            <td>144</td>
        </tr>
        <tr>
            <td>Norfolk Island</td>
            <td>61</td>
            <td>145</td>
        </tr>
        <tr>
            <td>Guinea-Bissau</td>
            <td>61</td>
            <td>145</td>
        </tr>
        <tr>
            <td>Ecuador</td>
            <td>57</td>
            <td>147</td>
        </tr>
        <tr>
            <td>Tajikistan</td>
            <td>57</td>
            <td>147</td>
        </tr>
        <tr>
            <td>Tanzania</td>
            <td>57</td>
            <td>147</td>
        </tr>
        <tr>
            <td>Turks and Caicos Islands</td>
            <td>53</td>
            <td>150</td>
        </tr>
        <tr>
            <td>Iran</td>
            <td>53</td>
            <td>150</td>
        </tr>
        <tr>
            <td>Montserrat</td>
            <td>51</td>
            <td>152</td>
        </tr>
        <tr>
            <td>Yemen</td>
            <td>50</td>
            <td>153</td>
        </tr>
        <tr>
            <td>Cameroon</td>
            <td>50</td>
            <td>153</td>
        </tr>
        <tr>
            <td>Fiji</td>
            <td>49</td>
            <td>155</td>
        </tr>
        <tr>
            <td>Nicaragua</td>
            <td>49</td>
            <td>155</td>
        </tr>
        <tr>
            <td>Panama</td>
            <td>49</td>
            <td>155</td>
        </tr>
        <tr>
            <td>Afghanistan</td>
            <td>49</td>
            <td>155</td>
        </tr>
        <tr>
            <td>Montenegro</td>
            <td>48</td>
            <td>159</td>
        </tr>
        <tr>
            <td>Belarus</td>
            <td>47</td>
            <td>160</td>
        </tr>
        <tr>
            <td>Guinea</td>
            <td>47</td>
            <td>160</td>
        </tr>
        <tr>
            <td>Lithuania</td>
            <td>46</td>
            <td>162</td>
        </tr>
        <tr>
            <td>Palau</td>
            <td>46</td>
            <td>162</td>
        </tr>
        <tr>
            <td>South Africa</td>
            <td>44</td>
            <td>164</td>
        </tr>
        <tr>
            <td>Colombia</td>
            <td>44</td>
            <td>164</td>
        </tr>
        <tr>
            <td>Liberia</td>
            <td>43</td>
            <td>166</td>
        </tr>
        <tr>
            <td>Cocos (Keeling) Islands</td>
            <td>42</td>
            <td>167</td>
        </tr>
        <tr>
            <td>Cook Islands</td>
            <td>41</td>
            <td>168</td>
        </tr>
        <tr>
            <td>Madagascar</td>
            <td>40</td>
            <td>169</td>
        </tr>
        <tr>
            <td>Faroe Islands</td>
            <td>36</td>
            <td>170</td>
        </tr>
        <tr>
            <td>Zimbabwe</td>
            <td>36</td>
            <td>170</td>
        </tr>
        <tr>
            <td>United States</td>
            <td>35</td>
            <td>172</td>
        </tr>
        <tr>
            <td>Djibouti</td>
            <td>35</td>
            <td>172</td>
        </tr>
        <tr>
            <td>Congo, Democratic Republic of the</td>
            <td>35</td>
            <td>172</td>
        </tr>
        <tr>
            <td>Venezuela</td>
            <td>33</td>
            <td>175</td>
        </tr>
        <tr>
            <td>Bahamas, The</td>
            <td>32</td>
            <td>176</td>
        </tr>
        <tr>
            <td>Mozambique</td>
            <td>32</td>
            <td>176</td>
        </tr>
        <tr>
            <td>Latvia</td>
            <td>31</td>
            <td>178</td>
        </tr>
        <tr>
            <td>Estonia</td>
            <td>29</td>
            <td>179</td>
        </tr>
        <tr>
            <td>Laos</td>
            <td>29</td>
            <td>179</td>
        </tr>
        <tr>
            <td>Kyrgyzstan</td>
            <td>29</td>
            <td>179</td>
        </tr>
        <tr>
            <td>Equatorial Guinea</td>
            <td>26</td>
            <td>182</td>
        </tr>
        <tr>
            <td>Brazil</td>
            <td>24</td>
            <td>183</td>
        </tr>
        <tr>
            <td>Sweden</td>
            <td>23</td>
            <td>184</td>
        </tr>
        <tr>
            <td>Chile</td>
            <td>23</td>
            <td>184</td>
        </tr>
        <tr>
            <td>Peru</td>
            <td>23</td>
            <td>184</td>
        </tr>
        <tr>
            <td>Saint Pierre and Miquelon</td>
            <td>23</td>
            <td>184</td>
        </tr>
        <tr>
            <td>Solomon Islands</td>
            <td>22</td>
            <td>188</td>
        </tr>
        <tr>
            <td>Vanuatu</td>
            <td>22</td>
            <td>188</td>
        </tr>
        <tr>
            <td>Zambia</td>
            <td>20</td>
            <td>190</td>
        </tr>
        <tr>
            <td>Bhutan</td>
            <td>19</td>
            <td>191</td>
        </tr>
        <tr>
            <td>Uruguay</td>
            <td>19</td>
            <td>191</td>
        </tr>
        <tr>
            <td>Finland</td>
            <td>18</td>
            <td>193</td>
        </tr>
        <tr>
            <td>Norway</td>
            <td>17</td>
            <td>194</td>
        </tr>
        <tr>
            <td>Paraguay</td>
            <td>17</td>
            <td>194</td>
        </tr>
        <tr>
            <td>Algeria</td>
            <td>16</td>
            <td>196</td>
        </tr>
        <tr>
            <td>Somalia</td>
            <td>16</td>
            <td>196</td>
        </tr>
        <tr>
            <td>New Zealand</td>
            <td>16</td>
            <td>196</td>
        </tr>
        <tr>
            <td>Angola</td>
            <td>15</td>
            <td>199</td>
        </tr>
        <tr>
            <td>Argentina</td>
            <td>15</td>
            <td>199</td>
        </tr>
        <tr>
            <td>Belize</td>
            <td>15</td>
            <td>199</td>
        </tr>
        <tr>
            <td>Niger</td>
            <td>14</td>
            <td>202</td>
        </tr>
        <tr>
            <td>New Caledonia</td>
            <td>14</td>
            <td>202</td>
        </tr>
        <tr>
            <td>Papua New Guinea</td>
            <td>14</td>
            <td>202</td>
        </tr>
        <tr>
            <td>Mali</td>
            <td>13</td>
            <td>205</td>
        </tr>
        <tr>
            <td>Congo, Republic of the</td>
            <td>13</td>
            <td>205</td>
        </tr>
        <tr>
            <td>Saudi Arabia</td>
            <td>12</td>
            <td>207</td>
        </tr>
        <tr>
            <td>Christmas Island</td>
            <td>11</td>
            <td>208</td>
        </tr>
        <tr>
            <td>Turkmenistan</td>
            <td>11</td>
            <td>208</td>
        </tr>
        <tr>
            <td>Oman</td>
            <td>10</td>
            <td>210</td>
        </tr>
        <tr>
            <td>Bolivia</td>
            <td>9</td>
            <td>211</td>
        </tr>
        <tr>
            <td>Chad</td>
            <td>9</td>
            <td>211</td>
        </tr>
        <tr>
            <td>Russia</td>
            <td>8</td>
            <td>213</td>
        </tr>
        <tr>
            <td>Central African Republic</td>
            <td>8</td>
            <td>213</td>
        </tr>
        <tr>
            <td>Kazakhstan</td>
            <td>6</td>
            <td>215</td>
        </tr>
        <tr>
            <td>Gabon</td>
            <td>6</td>
            <td>215</td>
        </tr>
        <tr>
            <td>Niue</td>
            <td>4</td>
            <td>217</td>
        </tr>
        <tr>
            <td>Canada</td>
            <td>3</td>
            <td>218</td>
        </tr>
        <tr>
            <td>Botswana</td>
            <td>3</td>
            <td>218</td>
        </tr>
        <tr>
            <td>Iceland</td>
            <td>3</td>
            <td>218</td>
        </tr>
        <tr>
            <td>Suriname</td>
            <td>3</td>
            <td>218</td>
        </tr>
        <tr>
            <td>Guyana</td>
            <td>3</td>
            <td>218</td>
        </tr>
        <tr>
            <td>Mauritania</td>
            <td>3</td>
            <td>218</td>
        </tr>
        <tr>
            <td>Libya</td>
            <td>3</td>
            <td>218</td>
        </tr>
        <tr>
            <td>Australia</td>
            <td>2</td>
            <td>225</td>
        </tr>
        <tr>
            <td>Namibia</td>
            <td>2</td>
            <td>225</td>
        </tr>
        <tr>
            <td>Western Sahara</td>
            <td>2</td>
            <td>225</td>
        </tr>
        <tr>
            <td>Mongolia</td>
            <td>1</td>
            <td>228</td>
        </tr>
        <tr>
            <td>Pitcairn Islands</td>
            <td>1</td>
            <td>228</td>
        </tr>
        <tr>
            <td>Greenland</td>
            <td>0</td>
            <td>230</td>
        </tr>
        <tr>
            <td>Falkland Islands (Islas Malvinas)</td>
            <td>0</td>
            <td>230</td>
        </tr>
        <tr>
            <td>Svalbard</td>
            <td>0</td>
            <td>230</td>
        </tr>
    </tbody>
</table>



In this case we had to get rid of zero population countries, like Antarctica due to divison. We can see that the popuation density is rather low on Svalbard, Falkland Island and Greenland. Highest population to area of land ratio is in Macau, Monaco and Singapore. Hong Kong is forth.


```python
engine = create_engine('postgresql://postgres:Xufa4259!@localhost:5432/master')
```


```python
from sqlalchemy import create_engine
%load_ext sql
```


```python
%sql postgresql://postgres:Xufa4259!@localhost:5432/master
```
