# utl-nine-ways-to-update-master-dataset-using-a-transaction-dataset-by-id-sas-python-and-r
Nine ways to update master dataset using a transaction dataset by id sas python and r
    %let pgm=utl-nine-ways-to-update-master-dataset-using-a-transaction-dataset-by-id-sas-python-and-r;

    Nine ways to update master dataset using a transaction dataset by id sas python and r;

    As a side note. I don't think you can input and output the same dataset with a hash?

    PROBLEM

      UPDATE MASTER TABLE USING TRANSACTION TABLE

            INPUT           PROCESS            OUTPUT

            MASTER        TRANSACTION          MASTER
         ID    INDUSTRY   D    INDUSTRY     ID    INDUSTRY
          1       a       1                  1       a
          2       b       2                  2       b
          3               3       c          3       c
          4               4       d          4       d

    SOLUTIONS

          1 datastep update        (updates in place)
          2 dtastep modify         (updates in place)
          3 datastep merge         (automaticall overwrites maxter)
          4 enhanced modify        (updates in place)
          5 keyed set              (requires an unique key)
          6 sql left join          (automaticall overwrites maxter)
          7 sql update             (updates in place)
          8 r sqldf left join      (overwrite)
          9 python pandasql        (overwrite)

    Related repos on end

    github
    https://tinyurl.com/3s8zavud
    https://github.com/rogerjdeangelis/utl-nine-ways-to-update-master-dataset-using-a-transaction-dataset-by-id-sas-python-and-r

    inspired by
    https://goo.gl/CUZAuY
    http://stackoverflow.com/questions/42724713/how-to-join-or-update-the-table-in-sas

    NOT YOU SHOULD LEFT JUSTIFY OR HIGHLIGHT ALL THE CODE IN PROCESS SECTION BEFORE SUMITTING IT

    /**************************************************************************************************************************/
    /*                             |                                                                  |                       */
    /* INPUTS                      |   PROCESS                                                        |      OUTPUT           */
    /* ======                      |   =======                                                        |                       */
    /*                             |                                                                  |                       */
    /* WORK.MASTER total obs=4     | 1 DATASTEP UPDATE                                                | Obs    ID    INDUSTRY */
    /*                             | =================                                                |                       */
    /*  id    industry             |                                                                  |  1      1       a     */
    /*                             | data master;                                                     |  2      2       b     */
    /*   1       a                 |     update master transaction;                                   |  3      3       c     */
    /*   2       b                 |     by id;                                                       |  4      4       d     */
    /*   3                         |  run;quit;                                                       |                       */
    /*   4                         |                                                                  |                       */
    /*                             |------------------------------------------------------------------+-----------------------*/
    /*                             |                                                                  |                       */
    /*                             | 2 DATASTEP MODIFY (original master)                              |                       */
    /* TRANSACTION total obs=4     | ====================================                             |                       */
    /*                             |                                                                  |                       */
    /*  id    industry             | data master;                                                     |                       */
    /*                             |  input id industry $;                                            |                       */
    /*   1                         | cards4;                                                          |                       */
    /*   2                         | 1  a                                                             |                       */
    /*   3       c                 | 2  b                                                             |                       */
    /*   4       d                 | 3  .                                                             |                       */
    /*                             | 4  .                                                             |                       */
    /*                             | ;;;;                                                             |                       */
    /* * CREATE THE MASTER;        | run;quit;                                                        |                       */
    /*                             |                                                                  |                       */
    /* data master;                | data master;                                                     |                       */
    /*  input id industry $;       |     modify master transaction;                                   |                       */
    /* cards4;                     |     by id;                                                       |                       */
    /* 1  a                        | run;quit;                                                        |                       */
    /* 2  b                        |                                                                  |                       */
    /* 3  .                        |------------------------------------------------------------------+-----------------------*/
    /* 4  .                        |                                                                  |                       */
    /* ;;;;                        | 3 DATASTEP MERGE                                                 |                       */
    /* run;quit;                   |                                                                  |                       */
    /*                             | data master;                                                     |                       */
    /*                             |  input id industry $;                                            |                       */
    /* * CREATE THE TRANSACTION;   | cards4;                                                          |                       */
    /*                             | 1  a                                                             |                       */
    /* data transaction;           | 2  b                                                             |                       */
    /*   input id industry $;      | 3  .                                                             |                       */
    /* cards4;                     | 4  .                                                             |                       */
    /* 1  .                        | ;;;;                                                             |                       */
    /* 2  .                        | run;quit;                                                        |                       */
    /* 3  c                        |                                                                  |                       */
    /* 4  d                        | data master;                                                     |                       */
    /* ;;;;                        |                                                                  |                       */
    /* run;quit;                   |   merge master transaction                                       |                       */
    /*                             |     (rename=industry=industry_right);                            |                       */
    /*                             |   by id;                                                         |                       */
    /*                             |                                                                  |                       */
    /*                             |   industry=coalescec(industry                                    |                       */
    /*                             |     ,industry_right);                                            |                       */
    /*                             |   drop industry_right ;                                          |                       */
    /*                             |                                                                  |                       */
    /*                             | run;quit;                                                        |                       */
    /*                             |                                                                  |                       */
    /*                             |------------------------------------------------------------------+-----------------------*/
    /*                             |                                                                  |                       */
    /*                             | 4  ENHANCED MODIFY                                               |                       */
    /*                             | ==================                                               |                       */
    /*                             |                                                                  |                       */
    /*                             | data master;                                                     |                       */
    /*                             |  input id industry $;                                            |                       */
    /*                             | cards4;                                                          |                       */
    /*                             | 1  a                                                             |                       */
    /*                             | 2  b                                                             |                       */
    /*                             | 3  .                                                             |                       */
    /*                             | 4  .                                                             |                       */
    /*                             | ;;;;                                                             |                       */
    /*                             | run;quit;                                                        |                       */
    /*                             |                                                                  |                       */
    /*                             | data master;                                                     |                       */
    /*                             |    modify master transaction;                                    |                       */
    /*                             |    by id;                                                        |                       */
    /*                             |    select (_iorc_);                                              |                       */
    /*                             |      when (%sysrc(_dsenmr)) /* nomatch MASTER _Add */            |                       */
    /*                             |        do;                                                       |                       */
    /*                             |            put "Nomatch";                                        |                       */
    /*                             |           _error_=0;                                             |                       */
    /*                             |        end;                                                      |                       */
    /*                             |      when (%sysrc(_sok))   /* matched Update or Delete */        |                       */
    /*                             |        do;                                                       |                       */
    /*                             |            put "Matched ";                                       |                       */
    /*                             |            replace master;                                       |                       */
    /*                             |           _error_=0;                                             |                       */
    /*                             |        end;                                                      |                       */
    /*                             |      otherwise                                                   |                       */
    /*                             |        do;                                                       |                       */
    /*                             |           put "Unexpected ERROR Condition: _IORC_ =" _IORC_;     |                       */
    /*                             |           _error_=0;                                             |                       */
    /*                             |        end;                                                      |                       */
    /*                             |    end;                                                          |                       */
    /*                             | run;quit;                                                        |                       */
    /*                             |                                                                  |                       */
    /*                             |------------------------------------------------------------------+-----------------------*/
    /*                             |                                                                  |                       */
    /*                             | 5  KEYED SET                                                     |                       */
    /*                             | ============                                                     |                       */
    /*                             |                                                                  |                       */
    /*                             | data master;                                                     |                       */
    /*                             |  input id industry $;                                            |                       */
    /*                             | cards4;                                                          |                       */
    /*                             | 1  a                                                             |                       */
    /*                             | 2  b                                                             |                       */
    /*                             | 3  .                                                             |                       */
    /*                             | 4  .                                                             |                       */
    /*                             | ;;;;                                                             |                       */
    /*                             | run;quit;                                                        |                       */
    /*                             |                                                                  |                       */
    /*                             | data Master;                                                     |                       */
    /*                             |    set transaction;                                              |                       */
    /*                             |    set master(rename=industry=industry2) key=id;                 |                       */
    /*                             |    select(_iorc_);                                               |                       */
    /*                             |       when (%sysrc(_sok)) do;                                    |                       */
    /*                             |          if missing(industry) then                               |                       */
    /*                             |            industry=industry2;                                   |                       */
    /*                             |          status="MATCH ON ID   ";                                |                       */
    /*                             |       end;                                                       |                       */
    /*                             |       when (%sysrc(_dsenom)) do;                                 |                       */
    /*                             |          status="NO MATCH IN ID";                                |                       */
    /*                             |          _error_=0;                                              |                       */
    /*                             |          output;                                                 |                       */
    /*                             |       end;                                                       |                       */
    /*                             |       otherwise;                                                 |                       */
    /*                             |    end;                                                          |                       */
    /*                             |    output;                                                       |                       */
    /*                             | run;quit;                                                        |                       */
    /*                             |                                                                  |                       */
    /*                             |------------------------------------------------------------------+-----------------------*/
    /*                             |                                                                  |                       */
    /*                             | 6  SQL LEFT JOIN                                                 |                       */
    /*                             | ==================                                               |                       */
    /*                             |                                                                  |                       */
    /*                             | data master;                                                     |                       */
    /*                             |  input id industry $;                                            |                       */
    /*                             | cards4;                                                          |                       */
    /*                             | 1  a                                                             |                       */
    /*                             | 2  b                                                             |                       */
    /*                             | 3  .                                                             |                       */
    /*                             | 4  .                                                             |                       */
    /*                             | ;;;;                                                             |                       */
    /*                             | run;quit;                                                        |                       */
    /*                             |                                                                  |                       */
    /*                             | proc sql;                                                        |                       */
    /*                             |    create                                                        |                       */
    /*                             |      table master as                                             |                       */
    /*                             |    select                                                        |                       */
    /*                             |      l.id                                                        |                       */
    /*                             |     ,coalesce(l.industry                                         |                       */
    /*                             |        , r.industry) as industry                                 |                       */
    /*                             |    from                                                          |                       */
    /*                             |      master as l left join transaction as r                      |                       */
    /*                             |    on                                                            |                       */
    /*                             |     l.id = r.id                                                  |                       */
    /*                             | ;quit;                                                           |                       */
    /*                             |                                                                  |                       */
    /*                             |------------------------------------------------------------------+-----------------------*/
    /*                             |                                                                  |                       */
    /*                             | 7  SQL UPDATE                                                    |                       */
    /*                             | ==================                                               |                       */
    /*                             |                                                                  |                       */
    /*                             | data master;                                                     |                       */
    /*                             |  input id industry $;                                            |                       */
    /*                             | cards4;                                                          |                       */
    /*                             | 1  a                                                             |                       */
    /*                             | 2  b                                                             |                       */
    /*                             | 3  .                                                             |                       */
    /*                             | 4  .                                                             |                       */
    /*                             | ;;;;                                                             |                       */
    /*                             | run;quit;                                                        |                       */
    /*                             |                                                                  |                       */
    /*                             | proc sql;                                                        |                       */
    /*                             | update master as a                                               |                       */
    /*                             |     set industry=(                                               |                       */
    /*                             |          select                                                  |                       */
    /*                             |              industry                                            |                       */
    /*                             |          from                                                    |                       */
    /*                             |              transaction as b                                    |                       */
    /*                             |          where                                                   |                       */
    /*                             |              a.id=b.id and industry ne '')                       |                       */
    /*                             |         where exists (                                           |                       */
    /*                             |          select                                                  |                       */
    /*                             |             *                                                    |                       */
    /*                             |          from                                                    |                       */
    /*                             |            transaction as b                                      |                       */
    /*                             |          where                                                   |                       */
    /*                             |            a.id=b.id                                             |                       */
    /*                             |          and                                                     |                       */
    /*                             |           industry ne '');                                       |                       */
    /*                             | select * from master;                                            |                       */
    /*                             | quit;                                                            |                       */
    /*                             |                                                                  |                       */
    /*                             |------------------------------------------------------------------+-----------------------*/
    /*                             |                                                                  |                       */
    /*                             | 8 R SQLDF LEFT JOIN                                              |                       */
    /*                             | ==================                                               |                       */
    /*                             |                                                                  |                       */
    /*                             | %utl_rbegin;                                                     |                       */
    /*                             | parmcards4;                                                      |                       */
    /*                             | library(sqldf);                                                  |                       */
    /*                             | source("c:/temp/fn_tosas9.R");                                   |                       */
    /*                             | master <-read.table(                                             |                       */
    /*                             |   header = TRUE                                                  |                       */
    /*                             |  ,text = "                                                       |                       */
    /*                             | id industry                                                      |                       */
    /*                             | 1 a                                                              |                       */
    /*                             | 2 b                                                              |                       */
    /*                             | 3 NA                                                             |                       */
    /*                             | 4 NA                                                             |                       */
    /*                             | ");                                                              |                       */
    /*                             | transact<- read.table(                                           |                       */
    /*                             |   header = TRUE                                                  |                       */
    /*                             |  ,text = "                                                       |                       */
    /*                             | id industry                                                      |                       */
    /*                             | 1 NA                                                             |                       */
    /*                             | 2 NA                                                             |                       */
    /*                             | 3 c                                                              |                       */
    /*                             | 4 d                                                              |                       */
    /*                             | ");                                                              |                       */
    /*                             | str(transact);                                                   |                       */
    /*                             | str(master);                                                     |                       */
    /*                             | want<- sqldf('                                                   |                       */
    /*                             |       select                                                     |                       */
    /*                             |         l.id                                                     |                       */
    /*                             |        ,coalesce(l.industry                                      |                       */
    /*                             |          ,r.industry) as industry                                |                       */
    /*                             |       from                                                       |                       */
    /*                             |         master as l left join transact as r                      |                       */
    /*                             |       on                                                         |                       */
    /*                             |        l.id = r.id                                               |                       */
    /*                             |      ');                                                         |                       */
    /*                             | want;                                                            |                       */
    /*                             | fn_tosas9(dataf=want);                                           |                       */
    /*                             | ;;;;                                                             |                       */
    /*                             | %utl_rend;                                                       |                       */
    /*                             |                                                                  |                       */
    /*                             | libname tmp "c:/temp";                                           |                       */
    /*                             | proc print data=tmp.want;                                        |                       */
    /*                             | run;quit;                                                        |                       */
    /*                             |                                                                  |                       */
    /*                             |------------------------------------------------------------------+-----------------------*/
    /*                             |                                                                  |                       */
    /*                             | 9 PYTHON PANDASQL                                                |                       */
    /*                             | =================                                                |                       */
    /*                             |                                                                  |                       */
    /*                             | libname tmp "c:/temp";                                           |                       */
    /*                             | proc datasets lib=tmp;                                           |                       */
    /*                             |   delete want;                                                   |                       */
    /*                             | run;quit;                                                        |                       */
    /*                             |                                                                  |                       */
    /*                             | %utl_pybegin;                                                    |                       */
    /*                             | parmcards4;                                                      |                       */
    /*                             | from os import path;                                             |                       */
    /*                             | import sys;                                                      |                       */
    /*                             | import os;                                                       |                       */
    /*                             | import subprocess;                                               |                       */
    /*                             | import time;                                                     |                       */
    /*                             | import pandas as pd;                                             |                       */
    /*                             | import numpy as np;                                              |                       */
    /*                             | import pyreadstat as ps;                                         |                       */
    /*                             | from pandasql import sqldf;                                      |                       */
    /*                             | mysql = lambda q: sqldf(q, globals());                           |                       */
    /*                             | from pandasql import PandaSQL;                                   |                       */
    /*                             | pdsql = PandaSQL(persist=True);                                  |                       */
    /*                             | sqlite3conn = next(pdsql.conn.gen).connection.connection;        |                       */
    /*                             | sqlite3conn.enable_load_extension(True);                         |                       */
    /*                             | sqlite3conn.load_extension("c:/temp/libsqlitefunctions.dll");    |                       */
    /*                             | mysql = lambda q: sqldf(q, globals());                           |                       */
    /*                             | master = [[1, "a"], [2, "b"], [3, None], [4, None]];             |                       */
    /*                             | master=pd.DataFrame(master, columns=["id", "industry"]);         |                       */
    /*                             | transact = [[1, None], [2, None], [3, "c"], [4, "d"]];           |                       */
    /*                             | transact=pd.DataFrame(transact, columns=["id", "industry"]);     |                       */
    /*                             | print(transact);                                                 |                       */
    /*                             | transact.info();                                                 |                       */
    /*                             | want = pdsql("""                                                 |                       */
    /*                             |    select                                                        |                       */
    /*                             |         l.id                                                     |                       */
    /*                             |        ,coalesce(l.industry                                      |                       */
    /*                             |          ,r.industry) as industry                                |                       */
    /*                             |       from                                                       |                       */
    /*                             |         master as l left join transact as r                      |                       */
    /*                             |       on                                                         |                       */
    /*                             |        l.id = r.id                                               |                       */
    /*                             |      """);                                                       |                       */
    /*                             | print(want);                                                     |                       */
    /*                             | exec(open("c:/temp/fn_tosas9.py").read());                       |                       */
    /*                             | fn_tosas9(                                                       |                       */
    /*                             |    want                                                          |                       */
    /*                             |    ,dfstr="want"                                                 |                       */
    /*                             |    ,timeest=3                                                    |                       */
    /*                             |    );                                                            |                       */
    /*                             | ;;;;                                                             |                       */
    /*                             | %utl_pyend;                                                      |                       */
    /*                             |                                                                  |                       */
    /*                             | libname tmp "c:/temp";                                           |                       */
    /*                             | proc print data=tmp.want;                                        |                       */
    /*                             | run;quit;                                                        |                       */
    /*                             |                                                                  |                       */
    */ /**********************************************************************************************+************************/


    REPO
    -------------------------------------------------------------------------------------------------------------------------------------
    https://github.com/rogerjdeangelis/utl_sql_update_master_using_a_transaction_table_mysql_database
    https://github.com/rogerjdeangelis/utl-given-master-sas-table-update-master-with-data-from-a-csv-file
    https://github.com/rogerjdeangelis/utl_using_a_transaction_table_to_update__master_table_very_simple_code
    https://github.com/rogerjdeangelis/utl-using-sentinels-to-restrict-update-to-common-columns-in-master-table-and-transaction-table
    https://github.com/rogerjdeangelis/utl-Combime-flags-across-and-within-groups-datastep-sql-update
    https://github.com/rogerjdeangelis/utl-given-master-sas-table-update-master-with-data-from-a-csv-file
    https://github.com/rogerjdeangelis/utl-given-yesterdays-and-todays-tables-flag-updates-inserts-and-deletes
    https://github.com/rogerjdeangelis/utl-in-palce-updates-to-an-existing-shared-excel-workbook
    https://github.com/rogerjdeangelis/utl-keeping-a-ring-of-last-four-daily-table-updates
    https://github.com/rogerjdeangelis/utl-ods-excel-update-excel-sheet-in-place-python
    https://github.com/rogerjdeangelis/utl-update-a-table-when-a-variable-has-been-defined-as-both-character-and-numeric
    https://github.com/rogerjdeangelis/utl-update-an-excel-workbook-in-place
    https://github.com/rogerjdeangelis/utl-update-an-existing-excel-named-range-R-python-sas
    https://github.com/rogerjdeangelis/utl-update-existing-excel-sheet-in-place-using-r-dcom-client
    https://github.com/rogerjdeangelis/utl-update-first-row-with-total-sales
    https://github.com/rogerjdeangelis/utl-update-master-table-using-multiple-transaction-tables
    https://github.com/rogerjdeangelis/utl-update-missing-data-in-table-a-using-repeated-partial-data-from-table-b
    https://github.com/rogerjdeangelis/utl-using-sentinels-to-restrict-update-to-common-columns-in-master-table-and-transaction-table
    https://github.com/rogerjdeangelis/utl_excel_create_sql_insert_and_value_statements_to_update_databases
    https://github.com/rogerjdeangelis/utl_excel_update_inplace
    https://github.com/rogerjdeangelis/utl_excel_update_rectangle
    https://github.com/rogerjdeangelis/utl_excel_update_xlsm_workbook_using_SAS_dataset
    https://github.com/rogerjdeangelis/utl_update_150mm_using_20_dimension_tables
    https://github.com/rogerjdeangelis/utl_using_a_macro_to_update_the_contents_of_a_parent_datastep_variable_at_execution_time
    https://github.com/rogerjdeangelis/utl_using_value_of_a_variable_rather_than_the_variable_name_to_update_a_key

    /*              _
      ___ _ __   __| |
     / _ \ `_ \ / _` |
    |  __/ | | | (_| |
     \___|_| |_|\__,_|

    */
