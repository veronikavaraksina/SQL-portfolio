# World GNI data

This data is from World Bank website and consists of three years of GNI indicator among differeny countries.

What is GNI?

GNI stands for Gross National Income. It is a measure of the total income earned by a country's residents, both domestically and internationally, during a specific period, usually a year. GNI takes into account the total value of goods and services produced within a country's borders (Gross Domestic Product or GDP) and adds net income from abroad, such as remittances from citizens working overseas and income earned by domestic companies from foreign investments.

GNI is an important economic indicator used to assess the economic well-being of a country and its citizens. It allows comparisons of income levels and economic performance among different countries and helps in determining a country's ability to generate and distribute wealth. GNI per capita, which is GNI divided by the population, is often used to compare living standards and economic development between countries.

The dataset is simple and well suite for the sql analysis. There are 3 columns, which are:
- country
- year
- value - which represents the total dollar gni

#### Cleaning
There are some cleaning required, such as getting rid of extra white space in country names.

Additionally we will remove country name "South Asia" as it represents a region rather than a country.


```sql
%%sql

update world_dept.country_stats 
set country = replace(country, '  ', ' ');

delete from world_dept.country_stats
where country = 'South Asia';

select
	count(distinct country) as number_of_countries 
from world_dept.country_stats;
```

     * postgresql://postgres:***@localhost:5432/master
    363 rows affected.
    0 rows affected.
    1 rows affected.
    




<table>
    <thead>
        <tr>
            <th>number_of_countries</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>121</td>
        </tr>
    </tbody>
</table>



Now the dataset is more auitable for the analysis and we can see that there are 121 countries represented in this dataset.

#### Totaling the amount of GNI by countries


```sql
%%sql

select
	country,
	sum(value) as total_income
from world_dept.country_stats cs
where value is not null
group by country
order by sum(value) desc;
```

     * postgresql://postgres:***@localhost:5432/master
    119 rows affected.
    




<table>
    <thead>
        <tr>
            <th>country</th>
            <th>total_income</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>China</td>
            <td>46399800000000</td>
        </tr>
        <tr>
            <td>India</td>
            <td>8560040000000</td>
        </tr>
        <tr>
            <td>Russian Federation</td>
            <td>4825440000000</td>
        </tr>
        <tr>
            <td>Brazil</td>
            <td>4784830000000</td>
        </tr>
        <tr>
            <td>Mexico</td>
            <td>3544440000000</td>
        </tr>
        <tr>
            <td>Indonesia</td>
            <td>3269960000000</td>
        </tr>
        <tr>
            <td>Turkiye</td>
            <td>2261984000000</td>
        </tr>
        <tr>
            <td>Thailand</td>
            <td>1500258000000</td>
        </tr>
        <tr>
            <td>Argentina</td>
            <td>1295976000000</td>
        </tr>
        <tr>
            <td>Nigeria</td>
            <td>1273798000000</td>
        </tr>
        <tr>
            <td>Philippines</td>
            <td>1211892000000</td>
        </tr>
        <tr>
            <td>Bangladesh</td>
            <td>1193180000000</td>
        </tr>
        <tr>
            <td>South Africa</td>
            <td>1120002000000</td>
        </tr>
        <tr>
            <td>Egypt Arab Rep</td>
            <td>1037714000000</td>
        </tr>
        <tr>
            <td>Vietnam</td>
            <td>987897000000</td>
        </tr>
        <tr>
            <td>Pakistan</td>
            <td>951876000000</td>
        </tr>
        <tr>
            <td>Colombia</td>
            <td>890168000000</td>
        </tr>
        <tr>
            <td>Peru</td>
            <td>623663000000</td>
        </tr>
        <tr>
            <td>Iraq</td>
            <td>620729000000</td>
        </tr>
        <tr>
            <td>Iran Islamic Rep</td>
            <td>522603000000</td>
        </tr>
        <tr>
            <td>Ukraine</td>
            <td>510868000000</td>
        </tr>
        <tr>
            <td>Kazakhstan</td>
            <td>481659000000</td>
        </tr>
        <tr>
            <td>Algeria</td>
            <td>473559000000</td>
        </tr>
        <tr>
            <td>Morocco</td>
            <td>361207000000</td>
        </tr>
        <tr>
            <td>Ethiopia</td>
            <td>313081999765</td>
        </tr>
        <tr>
            <td>Kenya</td>
            <td>306396947130</td>
        </tr>
        <tr>
            <td>Ecuador</td>
            <td>305933082000</td>
        </tr>
        <tr>
            <td>Dominican Republic</td>
            <td>249429356001</td>
        </tr>
        <tr>
            <td>Sri Lanka</td>
            <td>242597707602</td>
        </tr>
        <tr>
            <td>Guatemala</td>
            <td>236085633253</td>
        </tr>
        <tr>
            <td>Bulgaria</td>
            <td>213668113262</td>
        </tr>
        <tr>
            <td>Ghana</td>
            <td>211941278756</td>
        </tr>
        <tr>
            <td>Myanmar</td>
            <td>206132814476</td>
        </tr>
        <tr>
            <td>Tanzania</td>
            <td>191817936802</td>
        </tr>
        <tr>
            <td>Uzbekistan</td>
            <td>189787168617</td>
        </tr>
        <tr>
            <td>Belarus</td>
            <td>185980123200</td>
        </tr>
        <tr>
            <td>Cote d Ivoire</td>
            <td>184099635815</td>
        </tr>
        <tr>
            <td>Costa Rica</td>
            <td>179079613722</td>
        </tr>
        <tr>
            <td>Angola</td>
            <td>178982964261</td>
        </tr>
        <tr>
            <td>Serbia</td>
            <td>161081647215</td>
        </tr>
        <tr>
            <td>Congo Dem Rep</td>
            <td>148392686572</td>
        </tr>
        <tr>
            <td>Azerbaijan</td>
            <td>142044970589</td>
        </tr>
        <tr>
            <td>Jordan</td>
            <td>133095315775</td>
        </tr>
        <tr>
            <td>Tunisia</td>
            <td>127063792924</td>
        </tr>
        <tr>
            <td>Cameroon</td>
            <td>123122799440</td>
        </tr>
        <tr>
            <td>Bolivia</td>
            <td>115618934125</td>
        </tr>
        <tr>
            <td>Uganda</td>
            <td>111162349950</td>
        </tr>
        <tr>
            <td>Paraguay</td>
            <td>109103138981</td>
        </tr>
        <tr>
            <td>Nepal</td>
            <td>104856220643</td>
        </tr>
        <tr>
            <td>Lebanon</td>
            <td>92551662172</td>
        </tr>
        <tr>
            <td>Sudan</td>
            <td>88410572681</td>
        </tr>
        <tr>
            <td>El Salvador</td>
            <td>75996940000</td>
        </tr>
        <tr>
            <td>Cambodia</td>
            <td>75921212284</td>
        </tr>
        <tr>
            <td>Papua New Guinea</td>
            <td>74044086154</td>
        </tr>
        <tr>
            <td>Senegal</td>
            <td>73731109819</td>
        </tr>
        <tr>
            <td>Honduras</td>
            <td>71444588330</td>
        </tr>
        <tr>
            <td>Bosnia and Herzegovina</td>
            <td>62278111436</td>
        </tr>
        <tr>
            <td>Zimbabwe</td>
            <td>61906417386</td>
        </tr>
        <tr>
            <td>Zambia</td>
            <td>59787084828</td>
        </tr>
        <tr>
            <td>Lao PDR</td>
            <td>53199673027</td>
        </tr>
        <tr>
            <td>Mali</td>
            <td>51887582289</td>
        </tr>
        <tr>
            <td>Burkina Faso</td>
            <td>50709183755</td>
        </tr>
        <tr>
            <td>Haiti</td>
            <td>50339789403</td>
        </tr>
        <tr>
            <td>Georgia</td>
            <td>49462792417</td>
        </tr>
        <tr>
            <td>Albania</td>
            <td>48092330917</td>
        </tr>
        <tr>
            <td>Benin</td>
            <td>47336912787</td>
        </tr>
        <tr>
            <td>Botswana</td>
            <td>47159281025</td>
        </tr>
        <tr>
            <td>Gabon</td>
            <td>47066817771</td>
        </tr>
        <tr>
            <td>Mozambique</td>
            <td>44615614716</td>
        </tr>
        <tr>
            <td>Turkmenistan</td>
            <td>43575785421</td>
        </tr>
        <tr>
            <td>Jamaica</td>
            <td>41958025965</td>
        </tr>
        <tr>
            <td>Niger</td>
            <td>41823837717</td>
        </tr>
        <tr>
            <td>Madagascar</td>
            <td>40907394240</td>
        </tr>
        <tr>
            <td>Yemen Rep</td>
            <td>40690853845</td>
        </tr>
        <tr>
            <td>Mauritius</td>
            <td>40436507787</td>
        </tr>
        <tr>
            <td>Armenia</td>
            <td>39712641824</td>
        </tr>
        <tr>
            <td>Guinea</td>
            <td>39626245249</td>
        </tr>
        <tr>
            <td>Afghanistan</td>
            <td>39403128816</td>
        </tr>
        <tr>
            <td>Moldova</td>
            <td>38904744908</td>
        </tr>
        <tr>
            <td>Mongolia</td>
            <td>37708575763</td>
        </tr>
        <tr>
            <td>Nicaragua</td>
            <td>37089398152</td>
        </tr>
        <tr>
            <td>North Macedonia</td>
            <td>36888779588</td>
        </tr>
        <tr>
            <td>Malawi</td>
            <td>35230814264</td>
        </tr>
        <tr>
            <td>Chad</td>
            <td>32470358089</td>
        </tr>
        <tr>
            <td>Rwanda</td>
            <td>30800190715</td>
        </tr>
        <tr>
            <td>Tajikistan</td>
            <td>29714630616</td>
        </tr>
        <tr>
            <td>Congo Rep</td>
            <td>28796547318</td>
        </tr>
        <tr>
            <td>Kosovo</td>
            <td>25195333619</td>
        </tr>
        <tr>
            <td>Mauritania</td>
            <td>23746837509</td>
        </tr>
        <tr>
            <td>Kyrgyz Republic</td>
            <td>23442990429</td>
        </tr>
        <tr>
            <td>Togo</td>
            <td>23269400718</td>
        </tr>
        <tr>
            <td>Somalia</td>
            <td>20621944478</td>
        </tr>
        <tr>
            <td>Guyana</td>
            <td>17545646398</td>
        </tr>
        <tr>
            <td>Montenegro</td>
            <td>16393526186</td>
        </tr>
        <tr>
            <td>Fiji</td>
            <td>13701275946</td>
        </tr>
        <tr>
            <td>Maldives</td>
            <td>12914332585</td>
        </tr>
        <tr>
            <td>Eswatini</td>
            <td>12080777981</td>
        </tr>
        <tr>
            <td>Sierra Leone</td>
            <td>12068031898</td>
        </tr>
        <tr>
            <td>Djibouti</td>
            <td>9343619181</td>
        </tr>
        <tr>
            <td>Liberia</td>
            <td>9128558610</td>
        </tr>
        <tr>
            <td>Burundi</td>
            <td>8293795538</td>
        </tr>
        <tr>
            <td>Lesotho</td>
            <td>8129907566</td>
        </tr>
        <tr>
            <td>Central African Republic</td>
            <td>7531013626</td>
        </tr>
        <tr>
            <td>Timor Leste</td>
            <td>7491938739</td>
        </tr>
        <tr>
            <td>Gambia The</td>
            <td>5609617179</td>
        </tr>
        <tr>
            <td>Cabo Verde</td>
            <td>5500880184</td>
        </tr>
        <tr>
            <td>St Lucia</td>
            <td>5303430516</td>
        </tr>
        <tr>
            <td>Belize</td>
            <td>5014626871</td>
        </tr>
        <tr>
            <td>Solomon Islands</td>
            <td>4792731917</td>
        </tr>
        <tr>
            <td>Guinea Bissau</td>
            <td>4567101521</td>
        </tr>
        <tr>
            <td>Bhutan</td>
            <td>4478690944</td>
        </tr>
        <tr>
            <td>Comoros</td>
            <td>3717285988</td>
        </tr>
        <tr>
            <td>Grenada</td>
            <td>3162324259</td>
        </tr>
        <tr>
            <td>Vanuatu</td>
            <td>3016062871</td>
        </tr>
        <tr>
            <td>St Vincent and the Grenadines</td>
            <td>2655215460</td>
        </tr>
        <tr>
            <td>Samoa</td>
            <td>2366309317</td>
        </tr>
        <tr>
            <td>Dominica</td>
            <td>1660460286</td>
        </tr>
        <tr>
            <td>Sao Tome and Principe</td>
            <td>1456254617</td>
        </tr>
        <tr>
            <td>Tonga</td>
            <td>1083822909</td>
        </tr>
    </tbody>
</table>



We can see clear leaders when it comes to GNI in the last 3 years with China taking first place.

However, is that leadership caused by GNI growth in the past three years?

#### Pivoting the table and analysing the growth of GNI

To analyse the yoy change we can pivot the data. That will allow us to draw a comparison between 2019 and 2021.

That can be done through adding tablefunc extension and using crosstab function like the following.

The pivoted table will be saved as a view.


```sql
%%sql

create view world_dept.pivot_country_stats as (select *
from crosstab(
	'select
	country,
	year,
	sum(value) as total_gni
from world_dept.country_stats
group by country, year
order by country, year',
	'select distinct year from world_dept.country_stats order by year'
) as ct (
	country text,
	"2019" bigint,
	"2020" bigint,
	"2021" bigint
	));
```


```sql
%%sql

select *
from world_dept.pivot_country_stats;
```

     * postgresql://postgres:***@localhost:5432/master
    121 rows affected.
    




<table>
    <thead>
        <tr>
            <th>country</th>
            <th>2019</th>
            <th>2020</th>
            <th>2021</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>Afghanistan</td>
            <td>19098305631</td>
            <td>20304823185</td>
            <td>None</td>
        </tr>
        <tr>
            <td>Albania</td>
            <td>15200249154</td>
            <td>14881636667</td>
            <td>18010445096</td>
        </tr>
        <tr>
            <td>Algeria</td>
            <td>167568000000</td>
            <td>142065000000</td>
            <td>163926000000</td>
        </tr>
        <tr>
            <td>Angola</td>
            <td>63042946079</td>
            <td>49106327000</td>
            <td>66833691182</td>
        </tr>
        <tr>
            <td>Argentina</td>
            <td>435009000000</td>
            <td>379419000000</td>
            <td>481548000000</td>
        </tr>
        <tr>
            <td>Armenia</td>
            <td>13798304656</td>
            <td>12445092091</td>
            <td>13469245077</td>
        </tr>
        <tr>
            <td>Azerbaijan</td>
            <td>46318529412</td>
            <td>42422882353</td>
            <td>53303558824</td>
        </tr>
        <tr>
            <td>Bangladesh</td>
            <td>365626000000</td>
            <td>389379000000</td>
            <td>438175000000</td>
        </tr>
        <tr>
            <td>Belarus</td>
            <td>62499323071</td>
            <td>57861857571</td>
            <td>65618942558</td>
        </tr>
        <tr>
            <td>Belize</td>
            <td>1781800236</td>
            <td>1526981670</td>
            <td>1705844965</td>
        </tr>
        <tr>
            <td>Benin</td>
            <td>14248202201</td>
            <td>15488837356</td>
            <td>17599873230</td>
        </tr>
        <tr>
            <td>Bhutan</td>
            <td>2306109176</td>
            <td>2172581768</td>
            <td>None</td>
        </tr>
        <tr>
            <td>Bolivia</td>
            <td>40048420232</td>
            <td>36212961245</td>
            <td>39357552648</td>
        </tr>
        <tr>
            <td>Bosnia and Herzegovina</td>
            <td>20135274748</td>
            <td>19782860888</td>
            <td>22359975800</td>
        </tr>
        <tr>
            <td>Botswana</td>
            <td>15819870731</td>
            <td>14681334422</td>
            <td>16658075872</td>
        </tr>
        <tr>
            <td>Brazil</td>
            <td>1816020000000</td>
            <td>1410300000000</td>
            <td>1558510000000</td>
        </tr>
        <tr>
            <td>Bulgaria</td>
            <td>66968982828</td>
            <td>68559607877</td>
            <td>78139522557</td>
        </tr>
        <tr>
            <td>Burkina Faso</td>
            <td>15326472488</td>
            <td>16664828374</td>
            <td>18717882893</td>
        </tr>
        <tr>
            <td>Burundi</td>
            <td>2588683263</td>
            <td>2795055419</td>
            <td>2910056856</td>
        </tr>
        <tr>
            <td>Cabo Verde</td>
            <td>1939681905</td>
            <td>1662754662</td>
            <td>1898443617</td>
        </tr>
        <tr>
            <td>Cambodia</td>
            <td>25525556830</td>
            <td>24862473361</td>
            <td>25533182093</td>
        </tr>
        <tr>
            <td>Cameroon</td>
            <td>38827845867</td>
            <td>39920133568</td>
            <td>44374820005</td>
        </tr>
        <tr>
            <td>Central African Republic</td>
            <td>2386023522</td>
            <td>2456788006</td>
            <td>2688202098</td>
        </tr>
        <tr>
            <td>Chad</td>
            <td>11140998595</td>
            <td>9948225336</td>
            <td>11381134158</td>
        </tr>
        <tr>
            <td>China</td>
            <td>14239900000000</td>
            <td>14583300000000</td>
            <td>17576600000000</td>
        </tr>
        <tr>
            <td>Colombia</td>
            <td>316058000000</td>
            <td>267842000000</td>
            <td>306268000000</td>
        </tr>
        <tr>
            <td>Comoros</td>
            <td>1170987117</td>
            <td>1227286626</td>
            <td>1319012245</td>
        </tr>
        <tr>
            <td>Congo Dem Rep</td>
            <td>48961722123</td>
            <td>47444089515</td>
            <td>51986874934</td>
        </tr>
        <tr>
            <td>Congo Rep</td>
            <td>10017207893</td>
            <td>8895297692</td>
            <td>9884041733</td>
        </tr>
        <tr>
            <td>Costa Rica</td>
            <td>60558173821</td>
            <td>58595348834</td>
            <td>59926091067</td>
        </tr>
        <tr>
            <td>Cote d Ivoire</td>
            <td>56975603680</td>
            <td>59471452570</td>
            <td>67652579565</td>
        </tr>
        <tr>
            <td>Djibouti</td>
            <td>2995009917</td>
            <td>3082506550</td>
            <td>3266102714</td>
        </tr>
        <tr>
            <td>Dominica</td>
            <td>602072449</td>
            <td>511125661</td>
            <td>547262176</td>
        </tr>
        <tr>
            <td>Dominican Republic</td>
            <td>84872399734</td>
            <td>75019702329</td>
            <td>89537253938</td>
        </tr>
        <tr>
            <td>Ecuador</td>
            <td>104935000000</td>
            <td>96468082000</td>
            <td>104530000000</td>
        </tr>
        <tr>
            <td>Egypt Arab Rep</td>
            <td>292071000000</td>
            <td>353899000000</td>
            <td>391744000000</td>
        </tr>
        <tr>
            <td>El Salvador</td>
            <td>25559270000</td>
            <td>23324950000</td>
            <td>27112720000</td>
        </tr>
        <tr>
            <td>Eritrea</td>
            <td>None</td>
            <td>None</td>
            <td>None</td>
        </tr>
        <tr>
            <td>Eswatini</td>
            <td>4042279502</td>
            <td>3574279788</td>
            <td>4464218691</td>
        </tr>
        <tr>
            <td>Ethiopia</td>
            <td>95322999765</td>
            <td>107045000000</td>
            <td>110714000000</td>
        </tr>
        <tr>
            <td>Fiji</td>
            <td>5045561192</td>
            <td>4295596044</td>
            <td>4360118710</td>
        </tr>
        <tr>
            <td>Gabon</td>
            <td>15511134185</td>
            <td>14503352951</td>
            <td>17052330635</td>
        </tr>
        <tr>
            <td>Gambia The</td>
            <td>1783608280</td>
            <td>1799313001</td>
            <td>2026695898</td>
        </tr>
        <tr>
            <td>Georgia</td>
            <td>16692817892</td>
            <td>15104331382</td>
            <td>17665643143</td>
        </tr>
        <tr>
            <td>Ghana</td>
            <td>65527306781</td>
            <td>70776628000</td>
            <td>75637343975</td>
        </tr>
        <tr>
            <td>Grenada</td>
            <td>1093858741</td>
            <td>999228444</td>
            <td>1069237074</td>
        </tr>
        <tr>
            <td>Guatemala</td>
            <td>75615995031</td>
            <td>76142264951</td>
            <td>84327373271</td>
        </tr>
        <tr>
            <td>Guinea</td>
            <td>12782497681</td>
            <td>12853242662</td>
            <td>13990504906</td>
        </tr>
        <tr>
            <td>Guinea Bissau</td>
            <td>1470018484</td>
            <td>1456255017</td>
            <td>1640828020</td>
        </tr>
        <tr>
            <td>Guyana</td>
            <td>5127130584</td>
            <td>5314860101</td>
            <td>7103655713</td>
        </tr>
        <tr>
            <td>Haiti</td>
            <td>14835495479</td>
            <td>14536821917</td>
            <td>20967472007</td>
        </tr>
        <tr>
            <td>Honduras</td>
            <td>23102311443</td>
            <td>22170450616</td>
            <td>26171826271</td>
        </tr>
        <tr>
            <td>India</td>
            <td>2804310000000</td>
            <td>2631760000000</td>
            <td>3123970000000</td>
        </tr>
        <tr>
            <td>Indonesia</td>
            <td>1085080000000</td>
            <td>1030410000000</td>
            <td>1154470000000</td>
        </tr>
        <tr>
            <td>Iran Islamic Rep</td>
            <td>291561000000</td>
            <td>231042000000</td>
            <td>None</td>
        </tr>
        <tr>
            <td>Iraq</td>
            <td>232470000000</td>
            <td>182172000000</td>
            <td>206087000000</td>
        </tr>
        <tr>
            <td>Jamaica</td>
            <td>15386446833</td>
            <td>13355443262</td>
            <td>13216135870</td>
        </tr>
        <tr>
            <td>Jordan</td>
            <td>44510374789</td>
            <td>43573532535</td>
            <td>45011408451</td>
        </tr>
        <tr>
            <td>Kazakhstan</td>
            <td>158975000000</td>
            <td>156099000000</td>
            <td>166585000000</td>
        </tr>
        <tr>
            <td>Kenya</td>
            <td>98774891656</td>
            <td>98931055474</td>
            <td>108691000000</td>
        </tr>
        <tr>
            <td>Kosovo</td>
            <td>8079698836</td>
            <td>7903802849</td>
            <td>9211831934</td>
        </tr>
        <tr>
            <td>Kyrgyz Republic</td>
            <td>8091718900</td>
            <td>7524451491</td>
            <td>7826820038</td>
        </tr>
        <tr>
            <td>Lao PDR</td>
            <td>17656109554</td>
            <td>17853650705</td>
            <td>17689912768</td>
        </tr>
        <tr>
            <td>Lebanon</td>
            <td>50250537701</td>
            <td>24776994399</td>
            <td>17524130072</td>
        </tr>
        <tr>
            <td>Lesotho</td>
            <td>2776399636</td>
            <td>2510186663</td>
            <td>2843321267</td>
        </tr>
        <tr>
            <td>Liberia</td>
            <td>2965834700</td>
            <td>2887982540</td>
            <td>3274741370</td>
        </tr>
        <tr>
            <td>Madagascar</td>
            <td>13737093371</td>
            <td>12841695474</td>
            <td>14328605395</td>
        </tr>
        <tr>
            <td>Malawi</td>
            <td>10841609234</td>
            <td>11990729153</td>
            <td>12398475877</td>
        </tr>
        <tr>
            <td>Maldives</td>
            <td>5047935906</td>
            <td>3436977492</td>
            <td>4429419187</td>
        </tr>
        <tr>
            <td>Mali</td>
            <td>16584807144</td>
            <td>16726618040</td>
            <td>18576157105</td>
        </tr>
        <tr>
            <td>Mauritania</td>
            <td>7793428220</td>
            <td>7810254723</td>
            <td>8143154566</td>
        </tr>
        <tr>
            <td>Mauritius</td>
            <td>15722182254</td>
            <td>11907064651</td>
            <td>12807260882</td>
        </tr>
        <tr>
            <td>Mexico</td>
            <td>1232600000000</td>
            <td>1051140000000</td>
            <td>1260700000000</td>
        </tr>
        <tr>
            <td>Moldova</td>
            <td>12595179795</td>
            <td>12253493780</td>
            <td>14056071333</td>
        </tr>
        <tr>
            <td>Mongolia</td>
            <td>12634360578</td>
            <td>12053017629</td>
            <td>13021197556</td>
        </tr>
        <tr>
            <td>Montenegro</td>
            <td>5604482667</td>
            <td>4856800492</td>
            <td>5932243027</td>
        </tr>
        <tr>
            <td>Morocco</td>
            <td>117528000000</td>
            <td>113027000000</td>
            <td>130652000000</td>
        </tr>
        <tr>
            <td>Mozambique</td>
            <td>15113488971</td>
            <td>13741900073</td>
            <td>15760225672</td>
        </tr>
        <tr>
            <td>Myanmar</td>
            <td>66388788286</td>
            <td>76406297227</td>
            <td>63337728963</td>
        </tr>
        <tr>
            <td>Nepal</td>
            <td>34539802030</td>
            <td>33830032733</td>
            <td>36486385880</td>
        </tr>
        <tr>
            <td>Nicaragua</td>
            <td>12130235456</td>
            <td>11760740991</td>
            <td>13198421705</td>
        </tr>
        <tr>
            <td>Niger</td>
            <td>13479237976</td>
            <td>13156292048</td>
            <td>15188307693</td>
        </tr>
        <tr>
            <td>Nigeria</td>
            <td>433449000000</td>
            <td>416521000000</td>
            <td>423828000000</td>
        </tr>
        <tr>
            <td>North Macedonia</td>
            <td>12024709921</td>
            <td>11647074287</td>
            <td>13216995380</td>
        </tr>
        <tr>
            <td>Pakistan</td>
            <td>315299000000</td>
            <td>294847000000</td>
            <td>341730000000</td>
        </tr>
        <tr>
            <td>Papua New Guinea</td>
            <td>23707267070</td>
            <td>24398606590</td>
            <td>25938212494</td>
        </tr>
        <tr>
            <td>Paraguay</td>
            <td>36804812203</td>
            <td>34274587389</td>
            <td>38023739389</td>
        </tr>
        <tr>
            <td>Peru</td>
            <td>220067000000</td>
            <td>196348000000</td>
            <td>207248000000</td>
        </tr>
        <tr>
            <td>Philippines</td>
            <td>414552000000</td>
            <td>389166000000</td>
            <td>408174000000</td>
        </tr>
        <tr>
            <td>Russian Federation</td>
            <td>1639590000000</td>
            <td>1453320000000</td>
            <td>1732530000000</td>
        </tr>
        <tr>
            <td>Rwanda</td>
            <td>10008305994</td>
            <td>9947623076</td>
            <td>10844261645</td>
        </tr>
        <tr>
            <td>Samoa</td>
            <td>816189687</td>
            <td>776615798</td>
            <td>773503832</td>
        </tr>
        <tr>
            <td>Sao Tome and Principe</td>
            <td>425925040</td>
            <td>477536662</td>
            <td>552792915</td>
        </tr>
        <tr>
            <td>Senegal</td>
            <td>22788439046</td>
            <td>23900827506</td>
            <td>27041843267</td>
        </tr>
        <tr>
            <td>Serbia</td>
            <td>48729123911</td>
            <td>51693913436</td>
            <td>60658609868</td>
        </tr>
        <tr>
            <td>Sierra Leone</td>
            <td>4025178583</td>
            <td>3985252722</td>
            <td>4057600593</td>
        </tr>
        <tr>
            <td>Solomon Islands</td>
            <td>1553502887</td>
            <td>1581793496</td>
            <td>1657435534</td>
        </tr>
        <tr>
            <td>Somalia</td>
            <td>6440983145</td>
            <td>6929252419</td>
            <td>7251708914</td>
        </tr>
        <tr>
            <td>South Africa</td>
            <td>378251000000</td>
            <td>329819000000</td>
            <td>411932000000</td>
        </tr>
        <tr>
            <td>Sri Lanka</td>
            <td>81433165366</td>
            <td>78690157905</td>
            <td>82474384331</td>
        </tr>
        <tr>
            <td>St Lucia</td>
            <td>1984594296</td>
            <td>1584324107</td>
            <td>1734512113</td>
        </tr>
        <tr>
            <td>St Vincent and the Grenadines</td>
            <td>902727752</td>
            <td>867488489</td>
            <td>884999219</td>
        </tr>
        <tr>
            <td>Sudan</td>
            <td>30335029650</td>
            <td>25514356434</td>
            <td>32561186597</td>
        </tr>
        <tr>
            <td>Syrian Arab Republic</td>
            <td>None</td>
            <td>None</td>
            <td>None</td>
        </tr>
        <tr>
            <td>Tajikistan</td>
            <td>9631253672</td>
            <td>9516009649</td>
            <td>10567367295</td>
        </tr>
        <tr>
            <td>Tanzania</td>
            <td>61908444374</td>
            <td>62347075302</td>
            <td>67562417126</td>
        </tr>
        <tr>
            <td>Thailand</td>
            <td>523870000000</td>
            <td>488208000000</td>
            <td>488180000000</td>
        </tr>
        <tr>
            <td>Timor Leste</td>
            <td>2730967300</td>
            <td>2382618800</td>
            <td>2378352639</td>
        </tr>
        <tr>
            <td>Togo</td>
            <td>7242517959</td>
            <td>7591296131</td>
            <td>8435586628</td>
        </tr>
        <tr>
            <td>Tonga</td>
            <td>553664950</td>
            <td>530157959</td>
            <td>None</td>
        </tr>
        <tr>
            <td>Tunisia</td>
            <td>40513426936</td>
            <td>41022471910</td>
            <td>45527894078</td>
        </tr>
        <tr>
            <td>Turkiye</td>
            <td>748179000000</td>
            <td>710565000000</td>
            <td>803240000000</td>
        </tr>
        <tr>
            <td>Turkmenistan</td>
            <td>43575785421</td>
            <td>None</td>
            <td>None</td>
        </tr>
        <tr>
            <td>Uganda</td>
            <td>34437033637</td>
            <td>36964314055</td>
            <td>39761002258</td>
        </tr>
        <tr>
            <td>Ukraine</td>
            <td>155795000000</td>
            <td>160040000000</td>
            <td>195033000000</td>
        </tr>
        <tr>
            <td>Uzbekistan</td>
            <td>60644519146</td>
            <td>59702701802</td>
            <td>69439947669</td>
        </tr>
        <tr>
            <td>Vanuatu</td>
            <td>1010743251</td>
            <td>986507194</td>
            <td>1018812426</td>
        </tr>
        <tr>
            <td>Vietnam</td>
            <td>313593000000</td>
            <td>327628000000</td>
            <td>346676000000</td>
        </tr>
        <tr>
            <td>Yemen Rep</td>
            <td>21869747275</td>
            <td>18821106570</td>
            <td>None</td>
        </tr>
        <tr>
            <td>Zambia</td>
            <td>22904080807</td>
            <td>17601655546</td>
            <td>19281348475</td>
        </tr>
        <tr>
            <td>Zimbabwe</td>
            <td>18896621255</td>
            <td>17447769297</td>
            <td>25562026834</td>
        </tr>
    </tbody>
</table>



The next step would be to identify the growth or fall of GNI is percentages.

#### What country experience the highest growth of GNI and which the lowest?


```sql
%%sql

with yoy_table as (select
	country,
	round(("2021" - "2019")/"2019"::decimal, 2) as yoy_change,
	rank() over(order by round(("2021" - "2019")/"2019"::decimal, 2) desc) as rank
from world_dept.pivot_country_stats
where "2019" is not null and "2021" is not null
order by yoy_change desc)
select
	country,
	(case when yoy_change >= 0 then '+' else '' end) ||
	(yoy_change * 100)::text || '%' as yoy_change,
	rank
from yoy_table
where rank = 1 or
	rank = (select count(*) from yoy_table);
```

     * postgresql://postgres:***@localhost:5432/master
    2 rows affected.
    




<table>
    <thead>
        <tr>
            <th>country</th>
            <th>yoy_change</th>
            <th>rank</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>Haiti</td>
            <td>+41.00%</td>
            <td>1</td>
        </tr>
        <tr>
            <td>Lebanon</td>
            <td>-65.00%</td>
            <td>113</td>
        </tr>
    </tbody>
</table>



By doing this query, we can see that the Haiti has achieved astonishing 40% GNI growth and Lebanon has lost 65% of GNI and is placed last.


```sql
%%sql

with yoy_table as (select
	country,
	round(("2021" - "2019")/"2019"::decimal, 2) as yoy_change,
	rank() over(order by round(("2021" - "2019")/"2019"::decimal, 2) desc) as rank
from world_dept.pivot_country_stats
where "2019" is not null and "2021" is not null
order by yoy_change desc)
select
	round(avg(yoy_change)*100,1)::text || '%' as world_gni_yoy
from yoy_table;
```

     * postgresql://postgres:***@localhost:5432/master
    1 rows affected.
    




<table>
    <thead>
        <tr>
            <th>world_gni_yoy</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>6.4%</td>
        </tr>
    </tbody>
</table>



In conclusion, the worlds GNI grew from 2019 to 2021 6%.
