# utl_consecutive_and_extra_emails_in_the_same_week
Consecutive and extra emails in the same week.  Keywords: sas sql join merge big data analytics macros oracle teradata mysql sas communities stackoverflow statistics artificial inteligence AI Python R Java Javascript WPS Matlab SPSS Scala Perl C C# Excel MS Access JSON graphics maps NLP natural language processing machine learning igraph DOSUBL DOW loop stackoverflow SAS community.

    Consecutive and extra emails in the same week

    Same reults in SAS and WPS

    github
    https://tinyurl.com/y75l62km
    https://github.com/rogerjdeangelis/utl_consecutive_and_extra_emails_in_the_same_week

    SAS-L
    https://listserv.uga.edu/cgi-bin/wa?A2=SAS-L;77ffa4f9.1806d


    INPUT
    =====

     Act   Account Number
     Ind   Indicator       'ES' means email sent
     Id    Enrollee


     WORK.HAVE total obs=190       + RULES
                                   |
       ACT    IND     ID       DT  |
                                   |
        1     ES     1000    21363 |
        1     ES     1000    21367 |
        1     ES     1000    21383 |
                                   |
        1     ES     1001    21392 |
                                   |
        2     ES     2000    21363 |
        2     ES     2000    21368 |
        2     ES     2000    21376 |
                                     RULES for extra emails in a week
        2     ES     2001    21363 |
        2     ES     2002    21375 | These are more than a week apart

        2     ES     2002    21384 | Count=1 One extra email
        2     ES     2002    21387 |

        2     ES     2002    21419 | Count=2 Two extra emails
        2     ES     2002    21423 |
        2     ES     2002    21426 |
                                     Total Count = 3 extra emails


                                     RULES for consecutive emails
        3     ES     3002    21358 |
        3     ES     3002    21369 | ** no consecutive
        3     ES     3002    21380 |


        3     ES     3002    21393 | ** Emailed on consecutive days
        3     ES     3002    21394 | **
     ....


    PROCESS
    =======

    * how many recieved emails on consecutive days;
    data wantseq;
      retain dif;
      set havSrt;
      by act ind id;
      if first.id then dif=.;
      else dif=dif(dt);
      if last.id and dif=1 then output;
    run;quit;

    /*
    Input check 3002 had a consecutive

    23     3     ES     3002    21358
    24     3     ES     3002    21369
    25     3     ES     3002    21380

    26     3     ES     3002    21393  ** only 1 consecutive;
    27     3     ES     3002    21394  **


    Up to 40 obs WORK.WANTSEQ total obs=1

    Obs    DIF    ACT    IND     ID       DT

     1      1      3     ES     3002    21394   * Only 3002 had consecutive emails.
    */

    * how many had extra emails within a week;
    data wantwek;
      retain cnt 0;
      set havSrt;
      by act ind id;
      if first.id then dif=constant('big');
      else dif=dif(dt);
      if dif le 7 then cnt=cnt+1;
      if last.id and cnt>0 then do;output;cnt=0;end;
      drop dif;
    run;quit;


    /* check
     9     2     ES     2002    21375

    10     2     ES     2002    21384
    11     2     ES     2002    21387   ** extra within a week

    12     2     ES     2002    21419   ** two extra within a week
    13     2     ES     2002    21423
    14     2     ES     2002    21426
                                        Total 3 in the same week
    */

    OUTPUT
    ======

     * there was only one in the sample data;

     WORK.WANTSEQ total obs=1

      DIF    ACT    IND     ID       DT

       1      3     ES     3002    21394   * Only 3002 had consecutive emails.


     * extra emails within a week
     WORK.WANTWEK total obs=42

     Extra
     Emails    ACT    IND      ID       DT

       1       1    ES      1000    21383
       1       2    ES      2000    21376
       3       2    ES      2002    21426
       1       3    ES      3000    21386
       3       3    ES      3001    21410
       2       3    ES      3002    21394
       3       4    ES      4000    21409
       2       4    ES      4003    21426
       3       5    ES      5000    21426
       2       5    ES      5002    21405
       2       5    ES      5004    21433
     ...

    *                _              _       _
     _ __ ___   __ _| | _____    __| | __ _| |_ __ _
    | '_ ` _ \ / _` | |/ / _ \  / _` |/ _` | __/ _` |
    | | | | | | (_| |   <  __/ | (_| | (_| | || (_| |
    |_| |_| |_|\__,_|_|\_\___|  \__,_|\__,_|\__\__,_|

    ;

    * make data;
    data have;

     do act=1 to 10;
       do ind='ES';
         do id=act*1000 to act*1001;
            do dt=today() to today() + int(100*uniform(1234)) + 1;
               if uniform(2345)<.05 then output;
            end;
         end;
       end;
     end;

    run;quit;

    proc sort data=have out=havSrt;
      by act ind id dt;
    run;quit;

    *          _       _   _
     ___  ___ | |_   _| |_(_) ___  _ __
    / __|/ _ \| | | | | __| |/ _ \| '_ \
    \__ \ (_) | | |_| | |_| | (_) | | | |
    |___/\___/|_|\__,_|\__|_|\___/|_| |_|

    ;

    %utl_submit_wps64('
    libname wrk sas7bdat "%sysfunc(pathname(work))";
    data wantseq;
      retain dif;
      set wrk.havSrt;
      by act ind id;
      if first.id then dif=.;
      else dif=dif(dt);
      if last.id and dif=1 then output;
    run;quit;
    proc print;
    run;quit;
    data wantwek;
      retain cnt 0;
      set wrk.havSrt;
      by act ind id;
      if first.id then dif=constant("big");
      else dif=dif(dt);
      if dif le 7 then cnt=cnt+1;
      if last.id and cnt>0 then do;output;cnt=0;end;
      drop dif;
    run;quit;
    proc print;
    run;quit;
    ');

