# utl-calculating-median-values-by-group-wps-r-python-sql
Calculating median values by group wps r python sql
    %let pgm=utl-calculating-median-values-by-group-wps-r-python-sql;

    Calculating median values by group wps r python sql

      SOLUTIONS

         1 wps sql
         2 wps r sql
         3 wps python sql
         4 wps r dplyr

    /*               _     _
     _ __  _ __ ___ | |__ | | ___ _ __ ___
    | `_ \| `__/ _ \| `_ \| |/ _ \ `_ ` _ \
    | |_) | | | (_) | |_) | |  __/ | | | | |
    | .__/|_|  \___/|_.__/|_|\___|_| |_| |_|
    |_|
    */

    /**************************************************************************************************************************/
    /*                                         |                                    |                                         */
    /*            INPUT                        |         PROCESS                    |               OUTPUT                    */
    /*                                         |                                    |                                         */
    /*  SD1.HAVE total obs=19                  |                                    |                                         */
    /*                                         |                                    |                                         */
    /*  Obs    SEX    AGE    HEIGHT    WEIGHT  | SAME SQL CODE IN WPS R AND PYTHON  |         MEDIAN_    MEDIAN_    MEDIAN_   */
    /*                                         |                                    |  SEX      AGE       HEIGHT     WEIGHT   */
    /*    1     M      14     69.0      112.5  |  select                            |                                         */
    /*    2     F      13     56.5       84.0  |    sex                             |   F       13.0      62.50       90.00   */
    /*    3     F      13     65.3       98.0  |   ,median(age)    as median_age    |   M       13.5      64.15      107.25   */
    /*    4     F      14     62.8      102.5  |   ,median(height) as median_height |                                         */
    /*    5     M      14     63.5      102.5  |   ,median(weight) as median_weight |                                         */
    /*    6     M      12     57.3       83.0  |  from                              |                                         */
    /*    7     F      12     59.8       84.5  |    sd1.have                        |                                         */
    /*    8     F      15     62.5      112.5  |  group                             |                                         */
    /*    9     M      13     62.5       84.0  |    by sex                          |                                         */
    /*   10     M      12     59.0       99.5  |                                    |                                         */
    /*   11     F      11     51.3       50.5  | WPS R dplyr                        |                                         */
    /*   12     F      14     64.3       90.0  |                                    |                                         */
    /*   13     F      12     56.3       77.0  | want = have %>%                    |                                         */
    /*   14     F      15     66.5      112.0  | group_by(SEX) %>%                  |                                         */
    /*   15     M      16     72.0      150.0  | summarise(across(where(is.numeric),|                                         */
    /*   16     M      12     64.8      128.0  |   ~ median(.x, na.rm = TRUE)));    |                                         */
    /*   17     M      15     67.0      133.0  |                                    |                                         */
    /*   18     M      11     57.5       85.0  |                                    |                                         */
    /*   19     M      15     66.5      112.0  |                                    |                                         */
    /*                                         |                                    |                                         */
    /**************************************************************************************************************************/

    /*                   _
    (_)_ __  _ __  _   _| |_
    | | `_ \| `_ \| | | | __|
    | | | | | |_) | |_| | |_
    |_|_| |_| .__/ \__,_|\__|
            |_|
    */

    options validvarname=upcase;
    libname sd1 "d:/sd1";
    data sd1.have;
      set sashelp.class(drop=name);
    run;quit;


    /**************************************************************************************************************************/
    /*                                                                                                                        */
    /*  SD1.HAVE total obs=19                                                                                                 */
    /*                                                                                                                        */
    /*   SEX    AGE    HEIGHT    WEIGHT                                                                                       */
    /*                                                                                                                        */
    /*    M      14     69.0      112.5                                                                                       */
    /*    F      13     56.5       84.0                                                                                       */
    /*    F      13     65.3       98.0                                                                                       */
    /*    F      14     62.8      102.5                                                                                       */
    /*    M      14     63.5      102.5                                                                                       */
    /*    M      12     57.3       83.0                                                                                       */
    /*    F      12     59.8       84.5                                                                                       */
    /*    F      15     62.5      112.5                                                                                       */
    /*    M      13     62.5       84.0                                                                                       */
    /*    M      12     59.0       99.5                                                                                       */
    /*    F      11     51.3       50.5                                                                                       */
    /*    F      14     64.3       90.0                                                                                       */
    /*    F      12     56.3       77.0                                                                                       */
    /*    F      15     66.5      112.0                                                                                       */
    /*    M      16     72.0      150.0                                                                                       */
    /*    M      12     64.8      128.0                                                                                       */
    /*    M      15     67.0      133.0                                                                                       */
    /*    M      11     57.5       85.0                                                                                       */
    /*    M      15     66.5      112.0                                                                                       */
    /*                                                                                                                        */
    /**************************************************************************************************************************/

    /*                                  _
    / | __      ___ __  ___   ___  __ _| |
    | | \ \ /\ / / `_ \/ __| / __|/ _` | |
    | |  \ V  V /| |_) \__ \ \__ \ (_| | |
    |_|   \_/\_/ | .__/|___/ |___/\__, |_|
                 |_|                 |_|
    */

    %utl_submit_wps64x('
    libname sd1 "d:/sd1";
    options validvarname=any;
    proc sql;
      create
        table want as
      select
        sex
       ,median(age)    as median_age
       ,median(height) as median_height
       ,median(weight) as median_weight
      from
        sd1.have
      group
        by sex
    ;quit;
    proc print split="_";
    run;quit;
    ');

    /**************************************************************************************************************************/
    /*                                                                                                                        */
    /* The WPS R System                                                                                                       */
    /*                                                                                                                        */
    /*               median    median    median                                                                               */
    /* Obs    SEX     age      height    weight                                                                               */
    /*                                                                                                                        */
    /*  1      F      13.0      62.50     90.00                                                                               */
    /*  2      M      13.5      64.15    107.25                                                                               */
    /*                                                                                                                        */
    /**************************************************************************************************************************/

    /*___                                          _
    |___ \  __      ___ __  ___   _ __   ___  __ _| |
      __) | \ \ /\ / / `_ \/ __| | `__| / __|/ _` | |
     / __/   \ V  V /| |_) \__ \ | |    \__ \ (_| | |
    |_____|   \_/\_/ | .__/|___/ |_|    |___/\__, |_|
                     |_|                        |_|
    */

    %utl_submit_wps64x("
    libname sd1 'd:/sd1';
    proc r;
    export data=sd1.have r=have;
    submit;
    library(sqldf);
    want<-sqldf('
      select
        sex
       ,median(age)    as median_age
       ,median(height) as median_height
       ,median(weight) as median_weight
      from
        have
      group
        by sex
    ');
    want;
    endsubmit;
    import data=sd1.want r=want;
    run;quit;
    proc print data=sd1.want split='_';
    run;quit;
    ");

    /*                                _   _                             _
    __      ___ __  ___   _ __  _   _| |_| |__   ___  _ __    ___  __ _| |
    \ \ /\ / / `_ \/ __| | `_ \| | | | __| `_ \ / _ \| `_ \  / __|/ _` | |
     \ V  V /| |_) \__ \ | |_) | |_| | |_| | | | (_) | | | | \__ \ (_| | |
      \_/\_/ | .__/|___/ | .__/ \__, |\__|_| |_|\___/|_| |_| |___/\__, |_|
             |_|         |_|    |___/                                |_|
    */

    %utlfkil(d:/xpt/want.xpt);

    /*----                                                                   ----*/
    /*----  elimiate issues with datastep and view with the same name        ----*/
    /*----                                                                   ----*/

    proc datasets lib=sd1 nolist mt=data mt=view nodetails;delete want; run;quit;
    proc datasets lib=work nolist mt=data mt=view nodetails;delete want; run;quit;

    %utl_submit_wps64x("
    options validvarname=any lrecl=32756;
    libname sd1 'd:/sd1';
    proc python;
    export data=sd1.have    python=have;
    submit;
    import pyreadstat as ps;
    from os import path;
    import pandas as pd;
    import numpy as np;
    from pandasql import sqldf;
    mysql = lambda q: sqldf(q, globals());
    from pandasql import PandaSQL;
    pdsql = PandaSQL(persist=True);
    sqlite3conn = next(pdsql.conn.gen).connection.connection;
    sqlite3conn.enable_load_extension(True);
    sqlite3conn.load_extension('c:/temp/libsqlitefunctions.dll');
    mysql = lambda q: sqldf(q, globals());
    want = pdsql('''
      select
        sex
       ,median(age)    as median_age
       ,median(height) as median_height
       ,median(weight) as median_weight
      from
        have
      group
        by sex
    ''');
    print(want);
    ps.write_xport(want,'d:\\xpt\\want.xpt',table_name='want',file_format_version=5
    ,column_labels=['SEX','MEDIAN_AGE','MEDIAN_HEIGHT','MEDIAN_WEIGHT']);
    endsubmit;
    ");

    libname xpt xport "d:/xpt/want.xpt";
    proc contents data=xpt._all_;
    run;quit;

    data want_py_long_names;
      %utl_rens(xpt.want) ;
      set want;
    run;quit;
    libname xpt clear;

    proc print data=want_py_long_names;
    run;quit;

    /**************************************************************************************************************************/
    /*                                                                                                                        */
    /* LABELS in the XPT File are used to provide long names. The V5 export file does not support names longer then 8 bytes.  */
    /*                                                                                                                        */
    /*  Alphabetic List of Variables and Attributes                                                                           */
    /*                                                                                                                        */
    /* #    Variable    Type    Len    Label                                                                                  */
    /*                                                                                                                        */
    /* 2    MEDIAN_A    Num       8    MEDIAN_AGE                                                                             */
    /* 3    MEDIAN_H    Num       8    MEDIAN_HEIGHT                                                                          */
    /* 4    MEDIAN_W    Num       8    MEDIAN_WEIGHT                                                                          */
    /* 1    SEX         Char      1    SEX                                                                                    */
    /*                                                                                                                        */
    /* The WPS PYTHON Procedure                                                                                               */
    /*                                                                                                                        */
    /*   SEX  median_age  median_height  median_weight                                                                        */
    /*                                                                                                                        */
    /* 0   F        13.0          62.50          90.00                                                                        */
    /* 1   M        13.5          64.15         107.25                                                                        */
    /*                                                                                                                        */
    /*                                                                                                                        */
    /* SD1.WANT                                                                                                               */
    /*               MEDIAN_    MEDIAN_    MEDIAN_                                                                            */
    /* Obs    SEX      AGE       HEIGHT     WEIGHT                                                                            */
    /*                                                                                                                        */
    /*  1      F       13.0      62.50       90.00                                                                            */
    /*  2      M       13.5      64.15      107.25                                                                            */
    /*                                                                                                                        */
    /**************************************************************************************************************************/

    proc datasets lib=sd1 nolist mt=data mt=view nodetails;delete want; run;quit;

    %utl_submit_wps64x("
    libname sd1 'd:/sd1';
    proc r;
    export data=sd1.have r=have;
    submit;
    library(dplyr);
    want = have %>%
      group_by(SEX) %>%
      summarise(across(where(is.numeric), ~ median(.x, na.rm = TRUE)));
    want;
    endsubmit;
    import data=sd1.want r=want;
    run;quit;
    proc print data=sd1.want split='_';
    run;quit;
    ");

    /**************************************************************************************************************************/
    /*                                                                                                                        */
    /* The WPS R System                                                                                                       */
    /*                                                                                                                        */
    /*   SEX     AGE HEIGHT WEIGHT                                                                                            */
    /*   <chr> <dbl>  <dbl>  <dbl>                                                                                            */
    /*                                                                                                                        */
    /* 1 F      13     62.5    90                                                                                             */
    /* 2 M      13.5   64.2   107.                                                                                            */
    /*                                                                                                                        */
    /* The WPS System                                                                                                         */
    /*                                                                                                                        */
    /* Obs    SEX    AGE     HEIGHT    WEIGHT                                                                                 */
    /*                                                                                                                        */
    /*  1      F     13.0     62.50     90.00                                                                                 */
    /*  2      M     13.5     64.15    107.25                                                                                 */
    /*                                                                                                                        */
    /**************************************************************************************************************************/


    /*              _
      ___ _ __   __| |
     / _ \ `_ \ / _` |
    |  __/ | | | (_| |
     \___|_| |_|\__,_|

    */
