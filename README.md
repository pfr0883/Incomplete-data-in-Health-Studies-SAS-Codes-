# Incomplete-data-in-Health-Studies-SAS-Codes-

TITLE "Logistic REGRESSION - CCA";
proc genmod data=pas_new;
class HIV IC Raceth Age Status;
model HIV = IC Raceth Age Status Drinks_last30days;
run;




TITLE "Multiple Imputation via FCS";
*imputation phase;
proc mi data= pas_new nimpute=100 out=mi_fcs seed=54321;
class HIV IC Raceth Age Status;
fcs plots=trace(mean std);
var HIV IC Raceth Age Status Drinks_last30days;
fcs discrim(IC Raceth Age Status/classeffects=include) nbiter =100;
run;


*analysis phase;
proc genmod data=mi_fcs;
class HIV IC Raceth Age Status;
model HIV = IC Raceth Age Status Drinks_last30days;
by _imputation_;
ods output ParameterEstimates=gm_fcs;
run;


*pooling phase;
PROC MIANALYZE parms(classvar=level)=gm_fcs;
class IC Raceth Age Status;
MODELEFFECTS INTERCEPT IC Raceth Age Status Drinks_last30days;
RUN;

