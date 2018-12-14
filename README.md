# utl-skipping-empty-files-when-importing
Skipping empty files when importing.

    Skipping empty files when importing                                                                                                
                                                                                                                                       
    This code will not have any warning or error associated with the empt file.                                                        
    80% of the code is error checking?                                                                                                 
                                                                                                                                       
    SAS Forun                                                                                                                          
    https://tinyurl.com/ybjvogg6                                                                                                       
    https://communities.sas.com/t5/SAS-Programming/Bypass-an-empty-file-when-Importing-multiple-files-into-a/m-p/519285                
                                                                                                                                       
    INPUT                                                                                                                              
    =====                                                                                                                              
                                                                                                                                       
     Three csv file                                                                                                                    
                                                                                                                                       
         c:/utl/class.csv                                                                                                              
         c:/utl/empty.csv      ** 0 bytes;                                                                                             
         c:/utl/classfit.csv                                                                                                           
                                                                                                                                       
     How to create the input                                                                                                           
                                                                                                                                       
         dm 'dexport sashelp.class "c:/utl/class.csv" replace';                                                                        
         dm 'dexport sashelp.classfit "c:/utl/classfit.csv" replace';                                                                  
                                                                                                                                       
         * create empty file;                                                                                                          
         x "nul>c:/utl/empty.csv";   * create empty may need admin rights;                                                             
                                                                                                                                       
     EXAMPLE OUTPUT                                                                                                                    
     --------------                                                                                                                    
                                                                                                                                       
     WORK.LOG total obs=3                                                                                                              
                                                                                                                                       
              FYL            RC    EMPTY       ERR    TABLE          STATUS                                                            
                                                                                                                                       
      c:/utl/classfit.csv     0      0          0     classfit    table created                                                        
      c:/utl/empty.csv        0      1 EMPTY    0     empty       no table output check error and empty flag                           
      c:/utl/class.csv        0      0          0     class       table created                                                        
                                                                                                                                       
                                                                                                                                       
     TWO WORK TABLES  (not three)                                                                                                      
     ---------------                                                                                                                   
                                                                                                                                       
     WORK.CLASS.sas7bdat                                                                                                               
     WORK.CLASSFIT.sas7bdat                                                                                                            
                                                                                                                                       
                                                                                                                                       
    PROCESS                                                                                                                            
    =======                                                                                                                            
                                                                                                                                       
    * just in case;                                                                                                                    
    proc datasets lib=work;                                                                                                            
      delete class classfit;                                                                                                           
    run;quit;                                                                                                                          
    %symdel fyl file cc table empty / nowarn;                                                                                          
                                                                                                                                       
     data log ;                                                                                                                        
                                                                                                                                       
       do fyl='c:/utl/classfit.csv','c:/utl/empty.csv','c:/utl/class.csv';                                                             
                                                                                                                                       
           call symputx('fyl',fyl);                                                                                                    
                                                                                                                                       
           rc=dosubl('                                                                                                                 
                * I think long ago Paul Dorfman was first to post this?;                                                               
                data _null_;                                                                                                           
                                                                                                                                       
                  infile "&fyl" end = empty ;                                                                                          
                                                                                                                                       
                  if empty then  call symputx("empty",empty); /* 1 if empty */                                                         
                  else call symputx("empty","0");                                                                                      
                                                                                                                                       
                  table=scan("&fyl",3,"/.");                                                                                           
                                                                                                                                       
                  call symputx("file","&fyl");                                                                                         
                  call symputx("table",table);                                                                                         
                 run;quit;                                                                                                             
                 %let cc=&syserr;                                                                                                      
           ');                                                                                                                         
                                                                                                                                       
           empty=symgetn("empty"); /* 1 if empty */                                                                                    
           err=symgetn("syserr");  /* 1 error */                                                                                       
                                                                                                                                       
           table=symget('table');                                                                                                      
           file=quote(symget('file'));                                                                                                 
                                                                                                                                       
           if not err then do;                                                                                                         
              if not empty then do;                                                                                                    
                   cmd = catx(" ","dm '","dimport" ,file,table,"replace';run;quit;");                                                  
                   call execute(cmd);                                                                                                  
                   status="table created                             ";                                                                
              end;                                                                                                                     
              else status="no table output check error and empty flags";                                                               
           end;                                                                                                                        
                                                                                                                                       
                                                                                                                                       
           output;                                                                                                                     
                                                                                                                                       
       end;                                                                                                                            
       stop;                                                                                                                           
    run;quit;                                                                                                                          
                                                                                                                                       
    *                _              _       _                                                                                          
     _ __ ___   __ _| | _____    __| | __ _| |_ __ _                                                                                   
    | '_ ` _ \ / _` | |/ / _ \  / _` |/ _` | __/ _` |                                                                                  
    | | | | | | (_| |   <  __/ | (_| | (_| | || (_| |                                                                                  
    |_| |_| |_|\__,_|_|\_\___|  \__,_|\__,_|\__\__,_|                                                                                  
                                                                                                                                       
    ;                                                                                                                                  
                                                                                                                                       
    same as above                                                                                                                      
                                                                                                                                       
      dm 'dexport sashelp.class "c:/utl/class.csv" replace';                                                                           
      dm 'dexport sashelp.classfit "c:/utl/classfit.csv" replace';                                                                     
                                                                                                                                       
      * create empty file;                                                                                                             
      x "nul>c:/utl/empty.csv";   * create empty may need admin rights;                                                                
                                                                                                                                       
                                                                                                                                       
                                                                                                                                       
