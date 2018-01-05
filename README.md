# utl_rowwise_frequencies_in_R_and_sas
Rowwise frequencies in R and SAS Keywords: sas sql join merge big data analytics macros oracle teradata mysql sas communities stackoverflow statistics artificial inteligence AI Python R Java Javascript WPS Matlab SPSS Scala Perl C C# Excel MS Access JSON graphics maps NLP natural language processing machine learning igraph DOSUBL DOW loop stackoverflow SAS community.
    Rowwise frequencies in R and SAS

    github
    https://github.com/rogerjdeangelis/utl_rowwise_frequencies_in_R_and_sas

    see
    https://goo.gl/mcrgXE
    https://stackoverflow.com/questions/45084312/calculate-column-wise-sum-for-each-row-add-new-columns-for-each-value-in-dataf

      Two Solutions

           1. SAS  transpose and proc corrresp
           2. WPS/R IML/R mtabulate

    INPUT
    =====

     SD1.HAVE total obs=4              | RULES ( compute frequency across row)
                                       |
       SEQ     A     B     C     D     |    _11    _22    _33    _44
                                       |
        1     11    11    33    44     |     2      0      1      1
        2     11    11    22    33     |     2      1      1      0
        3     22    22    33    44     |     0      2      1      1
        4     22    33    44    11     |     1      1      1      1
                                       |

    PROCESS (working code)
    ======================

      SAS

        proc transpose data=sd1.have out=havXpo;
           by seq;
        run;quit;

        proc corresp data=havXpo dim=1 observed;
          tables seq, col1;
        run;quit;

      WPS/R (working code)

       want <- cbind(have,  mtabulate(as.data.frame(t(have))));

    OUTPUT

       SAS WORK.HAVTAB total obs=5

        LABEL    _11    _22    _33    _44      SUM

         1        2      0      1      1         4
         2        2      1      1      0         4
         3        0      2      1      1         4
         4        1      1      1      1         4

         Sum      5      4      4      3        16


        R  WORK.WANTWPS total obs=4

          Obs    _11    _22    _33    _44

           1      2      0      1      1
           2      2      1      1      0
           3      0      2      1      1
           4      1      1      1      1

    *                _               _       _
     _ __ ___   __ _| | _____     __| | __ _| |_ __ _
    | '_ ` _ \ / _` | |/ / _ \   / _` |/ _` | __/ _` |
    | | | | | | (_| |   <  __/  | (_| | (_| | || (_| |
    |_| |_| |_|\__,_|_|\_\___|   \__,_|\__,_|\__\__,_|

    ;

    options validvarname=upcase;
    libname sd1 "d:/sd1";
    data sd1.have;
      retain seq 0;
      input A B C D;
      seq=_n_;
    cards4;
    11 11 33 44
    11 11 22 33
    22 22 33 44
    22 33 44 11
    ;;;;
    run;quit;

    *
     ___  __ _ ___
    / __|/ _` / __|
    \__ \ (_| \__ \
    |___/\__,_|___/

    ;

    proc transpose data=sd1.have out=havXpo;
    by seq;
    run;quit;

    ods exclude all;
    ods output observed=havTab;
    proc corresp data=havXpo dim=1 observed;
    tables seq, col1;
    run;quit;
    ods select all;

    *                     ____
    __      ___ __  ___  |  _ \
    \ \ /\ / / '_ \/ __| | |_) |
     \ V  V /| |_) \__ \ |  _ <
      \_/\_/ | .__/|___/ |_| \_\
             |_|
    ;
    %utl_submit_wps64('
    libname sd1 sas7bdat "d:/sd1";
    libname wrk sas7bdat "%sysfunc(pathname(work))";
    proc r;
    submit;
    source("C:/Program Files/R/R-3.3.2/etc/Rprofile.site", echo=T);
    library(qdapTools);
    library(haven);
    have<-read_sas("d:/sd1/have.sas7bdat");
    want <- mtabulate(as.data.frame(t(have)))[,5:8];
    endsubmit;
    import r=want data=wrk.wantwps;
    run;quit;
    ');

