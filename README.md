# utl-extract-the-last-non-missing-value-from-a-one-dimensional-array
Extract the last non missing value from a one dimensional array
    Extract the last non missing value from a one dimensional array                                                                
                                                                                                                                   
       Three Solutions                                                                                                             
                                                                                                                                   
              1. addrlong, peekclong and pokelong                                                                                  
              2. Transpose with key                                                                                                
                                                                                                                                   
    github                                                                                                                         
    https://tinyurl.com/y3g6jzg3                                                                                                   
    https://github.com/rogerjdeangelis/utl-extract-the-last-non-missing-value-from-a-one-dimensional-array                         
                                                                                                                                   
    https://tinyurl.com/yy5eoy6u                                                                                                   
    https://communities.sas.com/t5/SAS-Programming/how-to-find-out-the-last-non-missing-value-in-a-list-of-variable/m-p/539992     
                                                                                                                                   
    *_                   _                                                                                                         
    (_)_ __  _ __  _   _| |_                                                                                                       
    | | '_ \| '_ \| | | | __|                                                                                                      
    | | | | | |_) | |_| | |_                                                                                                       
    |_|_| |_| .__/ \__,_|\__|                                                                                                      
            |_|                                                                                                                    
    ;                                                                                                                              
                                                                                                                                   
    data have;                                                                                                                     
    input a b c d;                                                                                                                 
    cards;                                                                                                                         
    1 2 . 4                                                                                                                        
    4 . 3 6                                                                                                                        
    2 3 5 .                                                                                                                        
    . 2 3 5                                                                                                                        
    ;;;;                                                                                                                           
    run;quit;                                                                                                                      
                                                                                                                                   
    *            _               _                                                                                                 
      ___  _   _| |_ _ __  _   _| |_                                                                                               
     / _ \| | | | __| '_ \| | | | __|                                                                                              
    | (_) | |_| | |_| |_) | |_| | |_                                                                                               
     \___/ \__,_|\__| .__/ \__,_|\__|                                                                                              
                    |_|                                                                                                            
    ;                                                                                                                              
                            | RULES                                                                                                
     WORK.WANT total obs=4  |                                                                                                      
                            |                                                                                                      
       A    B    C    D     |   LASTNONMISSING                                                                                     
                            |                                                                                                      
       1    2    .    4     |        4   ** last non missing in the 1st row                                                        
       4    .    3    6     |        6   ** last non missing in the 2nd row                                                        
       2    3    5    .     |        5                                                                                             
       .    2    3    5     |        5                                                                                             
                                                                                                                                   
    *          _       _   _                                                                                                       
     ___  ___ | |_   _| |_(_) ___  _ __  ___                                                                                       
    / __|/ _ \| | | | | __| |/ _ \| '_ \/ __|                                                                                      
    \__ \ (_) | | |_| | |_| | (_) | | | \__ \                                                                                      
    |___/\___/|_|\__,_|\__|_|\___/|_| |_|___/                                                                                      
                                                                                                                                   
    ;                                                                                                                              
                                                                                                                                   
    -----------------------------------                                                                                            
    1. addrlong, peekclong and pokelong                                                                                            
    -----------------------------------                                                                                            
    data want;                                                                                                                     
                                                                                                                                   
     set have;                                                                                                                     
                                                                                                                                   
     array nums[*] A--D;                                                                                                           
                                                                                                                                   
     lastNonMissing=.;                                                                                                             
                                                                                                                                   
     chrBin=peekclong(addrlong(A),dim(nums)*8);                                                                                    
                                                                                                                                   
     chrBin=transtrn(chrBin,put(.,rb8.),trimn(''));                                                                                
                                                                                                                                   
     call pokelong(substr(chrBin,lengthn(chrBin)-7,8),addrlong(lastNonMissing));                                                   
                                                                                                                                   
     drop chrBin;                                                                                                                  
                                                                                                                                   
    run;quit;                                                                                                                      
                                                                                                                                   
    /*                                                                                                                             
    WORK.WANT total obs=4                                                                                                          
                                                                                                                                   
      A    B    C    D    LASTNONMISSING                                                                                           
                                                                                                                                   
      1    2    .    9           9                                                                                                 
      4    .    3    6           6                                                                                                 
      2    3    5    .           5                                                                                                 
      .    2    3    5           5                                                                                                 
    */                                                                                                                             
                                                                                                                                   
                                                                                                                                   
    ---------------------                                                                                                          
    2. Transpose with key                                                                                                          
    ---------------------                                                                                                          
                                                                                                                                   
    data havKey/view=havKey;                                                                                                       
      set have;                                                                                                                    
      key=_n_;                                                                                                                     
    run;quit;                                                                                                                      
                                                                                                                                   
    proc transpose data=havkey out=havXpo(                                                                                         
        rename=(col1=lastNonMissing _name_=ColumnNonMissing)                                                                       
        where=(lastNonMissing ne .));                                                                                              
      by _all_ notsorted ;                                                                                                         
      var a -- d ;                                                                                                                 
    run;quit;                                                                                                                      
                                                                                                                                   
    data havLst;                                                                                                                   
      set havXpo;                                                                                                                  
      by key;                                                                                                                      
      if last.key;                                                                                                                 
    run;quit;                                                                                                                      
                                                                                                                                   
    Up to 40 obs WORK.HAVLST total obs=4                                                                                           
                                                                                                                                   
    Obs    A    B    C    D    KEY    COLUMNNONMISSING    LASTNONMISSING                                                           
                                                                                                                                   
     1     1    2    .    9     1            D                   9                                                                 
     2     4    .    3    6     2            D                   6                                                                 
     3     2    3    5    .     3            C                   5                                                                 
     4     .    2    3    5     4            D                   5                                                                 
                                                                                                                                   
                                                                                                                                   
