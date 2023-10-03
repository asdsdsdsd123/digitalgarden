---
{"dg-publish":true,"dg-home":null,"aliases":null,"tags":["002-SAS/01-SAS基础知识点"],"permalink":"/002-SAS/01-SAS基础知识点/08 hash of data step/","dgPassFrontmatter":true}
---


```sas

/*2.Lookup using composite ket;*/  
data lb; 	
length subjid lbtestcd lborres lbdtc $200; 	
subjid="1011";lbtestcd="CA";lborres="2";lbdtc="2023-05-12";output; 
subjid="1012";lbtestcd="CA";lborres="3";lbdtc="2023-05-12";output;  	
subjid="1013";lbtestcd="CA";lborres="44";lbdtc="2023-05-12";output;   	
subjid="1014";lbtestcd="CA";lborres="222";lbdtc="2023-05-12";output;   	
subjid="1015";lbtestcd="CA";lborres="13";lbdtc="2023-05-12";output;   
	
subjid="1011";lbtestcd="CA";lborres="4";lbdtc="2023-05-13";output; 	
subjid="1012";lbtestcd="CA";lborres="8";lbdtc="2023-05-13";output;  	
subjid="1013";lbtestcd="CA";lborres="24";lbdtc="2023-05-13";output;   	
subjid="1014";lbtestcd="CA";lborres="622";lbdtc="2023-05-13";output;   	
subjid="1015";lbtestcd="CA";lborres="153";lbdtc="2023-05-13";output; 
	
subjid="1011";lbtestcd="K";lborres="1";lbdtc="2023-05-12";output; 	
subjid="1012";lbtestcd="K";lborres="2";lbdtc="2023-05-12";output;  	
subjid="1013";lbtestcd="K";lborres="3";lbdtc="2023-05-12";output;   	
subjid="1014";lbtestcd="K";lborres="4";lbdtc="2023-05-12";output;   	
subjid="1015";lbtestcd="K";lborres="5";lbdtc="2023-05-12";output;    	
subjid="1011";lbtestcd="K";lborres="4";lbdtc="2023-05-13";output; 	
subjid="1012";lbtestcd="K";lborres="8";lbdtc="2023-05-13";output;  	
subjid="1013";lbtestcd="K";lborres="24";lbdtc="2023-05-13";output;   	
subjid="1014";lbtestcd="K";lborres="622";lbdtc="2023-05-13";output;   	
subjid="1015";lbtestcd="K";lborres="153";lbdtc="2023-05-13";output;    
run;  

******************************************Inner join***************************************；
******************************************Inner join***************************************；
data base; 	
set lb; 	
where lbdtc="2023-05-12"; 	
lbblfl="Y"; 	
base=lborres; 
run;  

data lb2; 	
length lbblfl base $200;   
   /*不指定长度会报错：Undeclared data symbol stateList for hash object at line 194 column 3.*/
if _n_=1 then do; 		
declare hash h(dataset:'base'); 		
h.definekey('subjid','lbtestcd','lbtestcd'); 		
h.definedata('base','lbblfl'); 	 		
h.definedone(); 	
end;  	

set lb; 	
if h.find() eq 0 then output;   /*inner join*/  
run; 	 
******************************************Inner join***************************************；
******************************************Inner join***************************************；



******************************************left join***************************************；
******************************************left join***************************************；
data base; 	
set lb; 	
where lbdtc="2023-05-12"; 	
lbblfl="Y"; 	
base=lborres; 
if subjid="1015" then delete;
run;  

data lb2; 	
length lbblfl base $200;  
if _n_=1 then do; 		
declare hash h(dataset:'base'); 		
h.definekey('subjid','lbtestcd','lbtestcd'); 		
h.definedata('base','lbblfl'); 	 		
h.definedone(); 	
end;  	

set lb; 	
if h.find() eq 0 then yn=1;     /*不写这句，没有作用，lbblfl base无法merege上*/
run; 	 

**********************************************************************************；
******************************************left join*******************************；


```

