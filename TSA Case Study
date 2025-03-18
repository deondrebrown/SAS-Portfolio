/*Acessing Data*/

%let path=/home/u63754207/ECRB94/data;
libname tsa "&path";

options validvarname=v7;

proc import datafile="&path/TSAClaims2002_2017.csv"
				dbms=csv
				out=tsa.ClaimsImport
				replace;
		guessingrows=max;
run;

/*Explore Data*/

proc print data=tsa.ClaimsImport (obs=20);
run;

proc contents data=tsa.Claimsimport varnum;
run;

proc freq data=tsa.Claimsimport;
	tables  claim_site
			disposition
			claim_type
			date_received
			incident_date / nocum nopercent;
		format incident_date date_received year4.;
run;

proc print data=tsa.claimsimport;
	where date_received < incident_date;
	format date_received incident_date date9.;
run;

/* Remove duplicate rows.*/

proc sort data=tsa.ClaimsImport
		out=tsa.Claims_NoDups noduprecs;
		by _all_;
run;
/* Sort the data by ascending Incident_Date*/

proc sort data=tsa.Claims_NoDups;
		by Incident_Date;
run;

data tsa.claims_cleaned;
	set tsa.claims_nodups;
/* Clean the Claim_site column.*/
	if Claim_Site in ('-', ' ') then Claim_Site="Unknown";
/* Clean the Disposition column.*/
	if Disposition in ('-', ' ') then Disposition="Unknown";
		else if disposition = 'losed: Contractor Claim' then Disposition = 'Closed:Contractor Claim';
		else if Disposition = 'Closed: Canceled' then Disposition = 'Closed:Canceled';
/* Clean the Claim_Type column.*/
	if Claim_Type in ('-', ' ') then Claim_Type="Unknown";
		else if Claim_Type = 'Passenger Property Loss/Personal Injur' then CLaim_Type='Passenger Property Loss';
		else if Claim_type = 'Passenger Property Loss/Personal Injury' then Claim_Type='Passenger Property Loss';
		else if Claim_type = 'Property Damage/Personal Injury' then Claim_Type='Property Damage';
/* Convert all State values to uppercase and all StateName values to proper case*/
 State=upcase(state);
 StateName=propcase(StateName);
/* Create a new column to indicate date issues*/
	if(Incident_date > Date_Received or
		Date_received = . or
		Incident_Date = . or
		year(Incident_Date) < 2002 or
		year(Incident_Date) > 2017 or
		year(Date_Received) < 2002 or
		year(Date_Received) > 2017) then Date_Issues="Needs Review";

/* Add permanent labels and formats.*/
	format Incident_Date Date_Received date9. Close_Amount Dollar20.2;
	label Airport_Code="Airport Code"
		  Airport_Name="Aiport Name"
		  Claim_Number="Claim Number"
		  Claim_Type="Claim Type"
		  Close_Amount="Close Amount"
		  Date_Issues="Date Issues"
		  Date_Received="Date Received"
		  Incident_Date="Incident Date"
		  Item_Category="Item Category";
/* Drop County and City.*/
	drop county city;
run;


proc freq data=tsa.Claims_Cleaned order=freq;
		tables Claim_Site
			   Disposition
			   Claim_Type
			   Date_Issues/ nopercent nocum;
run;




/* OVERALL ANALYSIS*/
title "Overall Date Issues in the Data";
proc freq data=TSA.Claims_Cleaned;
		table Date_Issues /missing nocum nopercent;
run;
title;


ods graphics on;
title "Overall Claims by Year";
proc freq data=TSA.Claims_Cleaned;
		table Incident_Date /nocum nopercent plots=freqplot;
		format Incident_Date year4.;
		where Date_Issues is null;
run;
title;
