# select
select    --:P102_BANK

                /*FTRS_DESC ,
                TO_CHAR(FRPD_DATE,'yyyy/MM/dd','nls_calendar=persian') FRPD_DATE,   
                FRPD_AMOUNT ,
                FRPD_TYPE_DESC,
                FPRC_CODE,
                FRPD_ROW_NUM,
                FPRC_TYPE,
                FCON_DESC,
                FTRS_TRS,
                FRPD_FTAF_TAF_PERSON,
                1,
                FRPD_DESC,
                nvl(FBRC_DATE,FCHQ_DATE) FCHQ_FBRC_DATE_DATE,
                --FBRC_DATE,
                 B,
                 A
               -- FCHQ_DATE*/
                
                FTRS_DESC ,
                TO_CHAR(FRPD_DATE,'yyyy/MM/dd','nls_calendar=persian') FRPD_DATE,   
                FRPD_AMOUNT ,
                FRPD_TYPE_DESC,
                FPRC_CODE,
                FRPD_ROW_NUM,
                FPRC_TYPE,
                FCON_DESC,
                FTRS_TRS,
                FRPD_FTAF_TAF_PERSON,
                1,
                FRPD_DESC,
                TO_CHAR(FBRC_DATE,'yyyy/MM/dd','nls_calendar=persian') FBRC_DATE,   --FBRC_DATE,
                 B,  --گردش
                 A,  --- شماره فیش یا چک
                FCHQ_DATE,
                SH_GHABZ ,
                SHENASE_P,
                SHAKHS_DAR_VAJH,
                DESC_CHQ_FSTC
                
from 
(   --11

SELECT * FROM (  --22
                --دريافت نقد (فيش) و  در حالتي که بانک مورد نظر دريافت کننده است
                --دريافت نقد     دريافتي   --تکي
                --دريافتني ها
                SELECT   --NVL(SUM(FRPD_AMOUNT),0) A  --817918290
                FTRS_DESC ,
                FRPD_DATE,   
                FRPD_AMOUNT ,
                CO_FNC_GET_CGREF_DESC('ACTION_TYPE',FRPD_TYPE)  FRPD_TYPE_DESC,
                FPRC_CODE,
                FRPD_ROW_NUM,
                FPRC_TYPE,
                (SELECT FCON_DESC FROM FCONP WHERE FCON_CON = FRPD_FCON_CON) FCON_DESC,
                FTRS_TRS,
                (SELECT  FTAF_DESC FROM FTAFA WHERE FRPD_FTAF_TAF_PERSON=FTAF_TAF ) FRPD_FTAF_TAF_PERSON,
                1,
                to_char(FRPD_DESC) FRPD_DESC,
                FBRC_DATE,
                to_char(FBRC_BANK_RCP_NO) B,
                to_char(FBRC_BANK_RCP_NO) A,
                NULL    FCHQ_DATE,
                NULL    SH_GHABZ ,
                NULL    SHENASE_P,
                FBRC_PAYER_NAME SHAKHS_DAR_VAJH,
                NULL DESC_CHQ_FSTC
                FROM    FRPDP, FPRCP , FTRSP  T ,FRBRP ,FBRCP
                WHERE   FPRC_PRC = FRPD_FPRC_PRC
                --AND     FRPD_STATE = P_FRPD_STATE **
                AND     FRBR_FBRC_BRC=FBRC_BRC(+)
                AND     FRBR_FRPD_RPD(+)=FRPD_RPD
                AND     FPRC_PAY_RCV_TYPE =1
                AND     FRPD_TYPE =1
                AND     FPRC_STATE <> 4
                --AND     FRPD_FTAF_TAF_GETTER =  FTRS_FTAF_TAF
                AND     FRPD_FTRS_TRS_GETTER =:P102_BANK-- FTRS_TRS
                AND     FRPD_STOCK_DOC IS NULL
                AND      (FTRS_TRS = :P102_BANK/*P_FTAF_TAF*/ OR :P102_BANK/*P_FTAF_TAF*/ IS NULL)

 --select 311686377656+817918290 from DUAL  --312504295946

  --  () و انتقالي در حالتي که بانک مورد نظر دريافت کننده است
                --د و انتقالي بدون چک دريافتي   --تکي
                --دريافتني ها

                UNION all

                SELECT    --NVL(SUM(FRPD_AMOUNT),0)
                FTRS_DESC ,
                FRPD_DATE,   
                FRPD_AMOUNT,
                CO_FNC_GET_CGREF_DESC('ACTION_TYPE',FRPD_TYPE)  FRPD_TYPE_DESC,
                FPRC_CODE,
                FRPD_ROW_NUM,
                FPRC_TYPE,
                (SELECT FCON_DESC FROM FCONP WHERE FCON_CON = FRPD_FCON_CON) FCON_DESC,
                FTRS_TRS,
                (SELECT  FTAF_DESC FROM FTAFA WHERE FRPD_FTAF_TAF_PERSON=FTAF_TAF ) FRPD_FTAF_TAF_PERSON,
                1,
                to_char(FRPD_DESC) FRPD_DESC,
                FBRC_DATE,
                to_char(FBRC_BANK_RCP_NO) B,
                to_char(FBRC_BANK_RCP_NO) A,
                NULL  FCHQ_DATE,
                NULL SH_GHABZ ,
                NULL SHENASE_P,
                FBRC_PAYER_NAME SHAKHS_DAR_VAJH,
                 NULL DESC_CHQ_FSTC
                FROM    FRPDP, FPRCP , FTRSP  T ,FRBRP ,FBRCP
                WHERE   FPRC_PRC = FRPD_FPRC_PRC
                AND      FPRC_STATE <> 4
                --AND     FRPD_STATE = P_FRPD_STATE **
                AND     FRPD_RPD=FRBR_FRPD_RPD(+)
                AND     FRBR_FBRC_BRC=FBRC_BRC(+)
                AND     FPRC_PAY_RCV_TYPE=1
                AND     FRPD_TYPE =7
                --AND     FRPD_FTAF_TAF_GETTER =  FTRS_FTAF_TAF
                AND     FRPD_FTRS_TRS_GETTER =:P102_BANK-- FTRS_TRS
                AND     FRPD_STOCK_DOC IS NULL
                AND      (FTRS_TRS =:P102_BANK  OR :P102_BANK IS NULL)  
--------------------------------------------------------------------------------------------------------------
union all     --درياف نقد و انتقالي نقد - گروهي - مبالغ مثبت
            SELECT   --NVL(SUM(FRPD_AMOUNT),0)
            FTRS_DESC, FRPD_DATE, FRPD_AMOUNT,
            CO_FNC_GET_CGREF_DESC('ACTION_TYPE',FRPD_TYPE) FRPD_TYPE_DESC,
            FPRC_CODE,FRPD_ROW_NUM,FPRC_TYPE,
            (SELECT FCON_DESC FROM FCONP WHERE FCON_CON = FRPD_FCON_CON) FCON_DESC,
            FTRS_TRS,(SELECT DISTINCT FTAF_DESC FROM FTAFA WHERE FRPD_FTAF_TAF_PERSON=FTAF_TAF ) FRPD_FTAF_TAF_PERSON,1,
            to_char(FRPD_DESC),
            NULL FBRC_DATE,NULL FBRC_BANK_RCP_NO , null FBRC_BANK_RCP_NO,NULL  FCHQ_DATE,
                NULL SH_GHABZ ,
                NULL SHENASE_P,
                NULL SHAKHS_DAR_VAJH,
                 NULL DESC_CHQ_FSTC
            FROM    FRPDP, FTRSP, FPRCP
            WHERE   FPRC_PRC = FRPD_FPRC_PRC
            AND     FPRC_STATE <> 4
            --AND     FRPD_STATE = P_FRPD_STATE
            AND     FPRC_PAY_RCV_TYPE = 2
            --AND     FRPD_BED_OR_BES = 2
            AND     FRPD_TYPE IN (1,7)
            AND     FRPD_FTAF_TAF_GETTER =:P102_BANK--  FTRS_FTAF_TAF
            AND     FRPD_STOCK_DOC IS NULL
            AND     FTRS_TRS =:P102_BANK-- P_FTAF_TAF;
  ------------------------------------------------------------------------------------------------------------------
 union all      ---
            -- دريافت اوراق بهادار - چک روز و چک مدت دار داراي گردش افزايش موجودي- تکي
                SELECT  --NVL(SUM(FRPD_AMOUNT),0)
                FTRS_DESC,  --c
                CASE 
                WHEN Q.FCHQ_WITH_TIME = 2 THEN   TRUNC(FCHQ_DATE)
                WHEN Q.FCHQ_WITH_TIME = 1 THEN  (SELECT TRUNC(FPRT_DATE)
                FROM FPRTP WHERE FPRT_PRT =(SELECT MAX(FPRT_PRT)FROM FPRTP WHERE FPRT_FCHQ_CHQ = Q.FCHQ_CHQ AND FPRT_FRPD_RPD = FRPD_RPD AND FPRT_TYPE = 3 ))
                END FRPD_DATE,
                 FCHQ_FRPD_AMOUNT,
                CO_FNC_GET_CGREF_DESC('ACTION_TYPE',FRPD_TYPE) FRPD_TYPE_DESC,
                FPRC_CODE,FRPD_ROW_NUM,FPRC_TYPE,
                (SELECT FCON_DESC FROM FCONP WHERE FCON_CON = FRPD_FCON_CON) FCON_DESC,
                FTRS_TRS,(SELECT DISTINCT FTAF_DESC FROM FTAFA WHERE FRPD_FTAF_TAF_PERSON=FTAF_TAF ) FRPD_FTAF_TAF_PERSON,1,
                to_char(FRPD_DESC),
                (SELECT FPRT_DATE FROM FPRTP WHERE FPRT_PRT =(SELECT MAX(FPRT_PRT)FROM FPRTP WHERE FPRT_FCHQ_CHQ = Q.FCHQ_CHQ AND FPRT_FRPD_RPD = FRPD_RPD AND FPRT_TYPE = 3 )) FPRT_DATE,
                (SELECT to_char(FPRT_FOLLOWUP_ID) FROM FPRTP  WHERE FPRT_PRT =(SELECT MAX(FPRT_PRT)FROM FPRTP WHERE FPRT_FCHQ_CHQ = Q.FCHQ_CHQ AND FPRT_FRPD_RPD = FRPD_RPD AND FPRT_TYPE = 3 ))FPRT_FOLLOWUP_ID,
                to_char(FCHQ_CHEQUE_NO),TO_CHAR(FCHQ_DATE,'yyyy/MM/dd','nls_calendar=persian') FCHQ_DATE,
                CASE WHEN FRPD_STOCK_DOC=7 THEN SUBSTR(FCHQ_CHEQUE_NO,1,INSTR(FCHQ_CHEQUE_NO,'*')-1) 
                     WHEN FRPD_STOCK_DOC=1 THEN NULL END  SH_GHABZ ,
                CASE WHEN FRPD_STOCK_DOC=7 THEN SUBSTR(FCHQ_CHEQUE_NO,INSTR(FCHQ_CHEQUE_NO,'*')+1) 
                     WHEN FRPD_STOCK_DOC=1 THEN NULL END  SHENASE_P,
                FCHQ_IN_MONEY SHAKHS_DAR_VAJH,
                 FCHQ_DESC DESC_CHQ_FSTC
        FROM    FRCPP C, FCHQP Q, FRPDP R, FTRSP T , FPRCP P
        WHERE   C.FRCP_FCHQ_CHQ = Q.FCHQ_CHQ
        AND     FCHQ_CHQ = FRCP_FCHQ_CHQ
    
        AND     C.FRCP_FRPD_RPD = R.FRPD_RPD
        AND     P.FPRC_PRC = R.FRPD_FPRC_PRC
        AND     R.FRPD_FTRS_TRS_GETTER = :P102_BANK-- T.FTRS_TRS
        AND      (FTRS_TRS = :P102_BANK/*P_FTAF_TAF*/ OR :P102_BANK/*P_FTAF_TAF*/ IS NULL)
        AND     R.FRPD_STOCK_DOC IN( 1,7)   --چک و شناسه
        --AND     R.FRPD_STATE = P_FRPD_STATE
        AND     P.FPRC_PAY_RCV_TYPE = 1
        AND     R.FRPD_TYPE IN (3,7)
        and     FCHQ_GUARANTY=2 --by kashani
        AND     FCHQ_CANCEL = 2 -- غير ابطال
        AND    (Q.FCHQ_WITH_TIME = 2 OR
               (Q.FCHQ_WITH_TIME = 1 AND
                    Q.FCHQ_CHQ = (SELECT FPRT_FCHQ_CHQ
                                  FROM   FPRTP
                      WHERE  FPRT_PRT =  (SELECT MAX(FPRT_PRT)FROM FPRTP WHERE FPRT_FCHQ_CHQ = Q.FCHQ_CHQ AND FPRT_FRPD_RPD = R.FRPD_RPD AND FPRT_TYPE =3 AND TRUNC(FPRT_DATE) between  TO_DATE(:P102_FR_DATE,'yyyy/MM/dd','nls_calendar=persian')
       and 
       TO_DATE(:P102_TO_DATE,'yyyy/MM/dd','nls_calendar=persian') ))
               ))   ---FOROOZAN
 -----------------------------------------------------------------------------------------------------------
 union all         -- دريافت اوراق بهادار - چک روز و چک مدت دار داراي گردش افزايش موجودي- گروهي
                SELECT -- NVL(SUM(FRPD_AMOUNT),0)
                FTRS_DESC,  --c
                CASE 
                WHEN Q.FCHQ_WITH_TIME = 2 THEN   FCHQ_DATE
                WHEN Q.FCHQ_WITH_TIME = 1 THEN  (SELECT FPRT_DATE
                FROM FPRTP WHERE FPRT_PRT =(SELECT MAX(FPRT_PRT)FROM FPRTP WHERE FPRT_FCHQ_CHQ = Q.FCHQ_CHQ AND FPRT_FRPD_RPD = FRPD_RPD AND FPRT_TYPE = 3 ))
                END FRPD_DATE,
                FRPD_AMOUNT,
                CO_FNC_GET_CGREF_DESC('ACTION_TYPE',FRPD_TYPE) FRPD_TYPE_DESC,
                FPRC_CODE,FRPD_ROW_NUM,FPRC_TYPE,
                (SELECT FCON_DESC FROM FCONP WHERE FCON_CON = FRPD_FCON_CON) FCON_DESC,
                FTRS_TRS,(SELECT DISTINCT FTAF_DESC FROM FTAFA WHERE FRPD_FTAF_TAF_PERSON=FTAF_TAF ) FRPD_FTAF_TAF_PERSON,1,
                to_char(FRPD_DESC),
                (SELECT FPRT_DATE FROM FPRTP WHERE FPRT_PRT =(SELECT MAX(FPRT_PRT)FROM FPRTP WHERE FPRT_FCHQ_CHQ = Q.FCHQ_CHQ AND FPRT_FRPD_RPD = FRPD_RPD AND FPRT_TYPE = 3 )) FPRT_DATE,
                (SELECT to_char(FPRT_FOLLOWUP_ID) FROM FPRTP  WHERE FPRT_PRT =(SELECT MAX(FPRT_PRT)FROM FPRTP WHERE FPRT_FCHQ_CHQ = Q.FCHQ_CHQ AND FPRT_FRPD_RPD = FRPD_RPD AND FPRT_TYPE = 3 ))FPRT_FOLLOWUP_ID,
                 to_char(FCHQ_CHEQUE_NO),TO_CHAR(FCHQ_DATE,'yyyy/MM/dd','nls_calendar=persian') FCHQ_DATE,
                CASE WHEN FRPD_STOCK_DOC=7 THEN SUBSTR(FCHQ_CHEQUE_NO,1,INSTR(FCHQ_CHEQUE_NO,'*')-1) 
                     WHEN FRPD_STOCK_DOC=1 THEN NULL END  SH_GHABZ ,
                CASE WHEN FRPD_STOCK_DOC=7 THEN SUBSTR(FCHQ_CHEQUE_NO,INSTR(FCHQ_CHEQUE_NO,'*')+1) 
                     WHEN FRPD_STOCK_DOC=1 THEN NULL END  SHENASE_P,
                  FCHQ_IN_MONEY SHAKHS_DAR_VAJH,
                    FCHQ_DESC DESC_CHQ_FSTC 
            FROM    FCHQP Q, FRPDP R, FTRSP T, FPRCP P
            WHERE   Q.FCHQ_FPRC_PRC = P.FPRC_PRC
            AND     P.FPRC_PRC = R.FRPD_FPRC_PRC
            AND     R.FRPD_FTAF_TAF_GETTER =  T.FTRS_FTAF_TAF
            AND     T.FTRS_TRS =:P102_BANK--P_FTAF_TAF
            AND     R.FRPD_STOCK_DOC IN( 1,7)
            --AND     R.FRPD_STATE = P_FRPD_STATE
            and     FCHQ_GUARANTY=2 --by kashani
            AND     P.FPRC_PAY_RCV_TYPE = 2
            AND     R.FRPD_TYPE IN (3,7)
        AND    (Q.FCHQ_WITH_TIME = 2 OR
               (Q.FCHQ_WITH_TIME = 1 AND
                    Q.FCHQ_CHQ = (SELECT FPRT_FCHQ_CHQ
                                  FROM   FPRTP
                      WHERE  FPRT_PRT =  (SELECT MAX(FPRT_PRT)FROM FPRTP WHERE FPRT_FCHQ_CHQ = Q.FCHQ_CHQ AND FPRT_FRPD_RPD = R.FRPD_RPD AND FPRT_TYPE =3 AND FPRT_DATE between  TO_DATE(:P102_FR_DATE,'yyyy/MM/dd','nls_calendar=persian')
       and 
       TO_DATE(:P102_TO_DATE,'yyyy/MM/dd','nls_calendar=persian') ))
               ))   
 -------------------------------------------------------------------------------------------
 UNION ALL
           -- دريافت اوراق بهاداري - حواله- تکي
            SELECT  --NVL(SUM(Q.FSTC_FRPD_AMOUNT),0)
            FTRS_DESC,  --c
             FRPD_DATE, FSTC_FRPD_AMOUNT,
            CO_FNC_GET_CGREF_DESC('ACTION_TYPE',FRPD_TYPE) FRPD_TYPE_DESC,
            FPRC_CODE,FRPD_ROW_NUM,FPRC_TYPE,
            (SELECT FCON_DESC FROM FCONP WHERE FCON_CON = FRPD_FCON_CON) FCON_DESC,
            FTRS_TRS,(SELECT DISTINCT FTAF_DESC FROM FTAFA WHERE FRPD_FTAF_TAF_PERSON=FTAF_TAF ) FRPD_FTAF_TAF_PERSON,1,
            to_char(FRPD_DESC),
            NULL FPRT_DATE,
            NULL FPRT_FOLLOWUP_ID,
             to_char(FSTC_STC_NO),TO_CHAR(FSTC_DATE,'yyyy/MM/dd','nls_calendar=persian') FCHQ_DATE,
                NULL SH_GHABZ ,
                NULL SHENASE_P,
               FSTC_IN_MONEY SHAKHS_DAR_VAJH,
               FSTC_DESC DESC_CHQ_FSTC
            FROM    FSTRP C, FSTCP Q, FRPDP R, FTRSP T, FPRCP P
            WHERE   C.FSTR_FSTC_STC = Q.FSTC_STC
            AND     C.FSTR_FRPD_RPD = R.FRPD_RPD
            AND     P.FPRC_PRC = R.FRPD_FPRC_PRC
            AND     R.FRPD_FTAF_TAF_GETTER =  T.FTRS_FTAF_TAF
            AND     T.FTRS_TRS = :P102_BANK
    AND  FPRC_STATE <> 4
            AND     R.FRPD_STOCK_DOC = 3
            and     FSTC_GUARANTY=2  ---kashani
            --AND     R.FRPD_STATE = P_FRPD_STATE
            AND     P.FPRC_PAY_RCV_TYPE = 1
            AND     R.FRPD_TYPE IN (3,7)
            /*AND     FRPD_DATE between P_FR_DATE AND P_TO_DATE*/

------------------------------------------------------------------------------------------------------------
 UNION ALL           
           -- دريافت اوراق بهاداري - حوله- -گروهي

            SELECT  --NVL(SUM(Q.FRPD_AMOUNT),0)
            FTRS_DESC,  --c
             FRPD_DATE, FRPD_AMOUNT,
            CO_FNC_GET_CGREF_DESC('ACTION_TYPE',FRPD_TYPE) FRPD_TYPE_DESC,
            FPRC_CODE,FRPD_ROW_NUM,FPRC_TYPE,
            (SELECT FCON_DESC FROM FCONP WHERE FCON_CON = FRPD_FCON_CON) FCON_DESC,
            FTRS_TRS,(SELECT DISTINCT FTAF_DESC FROM FTAFA WHERE FRPD_FTAF_TAF_PERSON=FTAF_TAF ) FRPD_FTAF_TAF_PERSON,1,
            to_char(FRPD_DESC),
            NULL FPRT_DATE,
            NULL FPRT_FOLLOWUP_ID,
             to_char(FSTC_STC_NO),TO_CHAR(FSTC_DATE,'yyyy/MM/dd','nls_calendar=persian') FCHQ_DATE,
                NULL SH_GHABZ ,
                NULL SHENASE_P,
                FSTC_IN_MONEY SHAKHS_DAR_VAJH,
                 FSTC_DESC DESC_CHQ_FSTC
            FROM    FSTCP Q, FRPDP R, FTRSP T, FPRCP P
            WHERE   Q.FSTC_FPRC_PRC = P.FPRC_PRC
            AND     P.FPRC_PRC = R.FRPD_FPRC_PRC
            AND     R.FRPD_FTAF_TAF_GETTER =  T.FTRS_FTAF_TAF
            AND     T.FTRS_TRS = :P102_BANK
            AND     FPRC_STATE <> 4
            AND     R.FRPD_STOCK_DOC = 3
            and     FSTC_GUARANTY=2  ---kashani
            --AND     R.FRPD_STATE = P_FRPD_STATE
            AND     P.FPRC_PAY_RCV_TYPE = 2
            AND     R.FRPD_TYPE IN (3,7)                 
 -------------------------------------------------------------------------------------------------------------
 union all
    --پرداخت
           --پرداخت نقد و حالتي که مرکز خزانه مذکور پرداخت کننده است.  --تکي  ---انتقالي بدون چک
           SELECT  --NVL(SUM((-1)*FRPD_AMOUNT),0)
               FTRS_DESC, FRPD_DATE,(-1)*frpd_amount,CO_FNC_GET_CGREF_DESC('ACTION_TYPE',FRPD_TYPE) FRPD_TYPE_DESC,
               FPRC_CODE,FRPD_ROW_NUM,FPRC_TYPE,
               (SELECT FCON_DESC FROM FCONP WHERE FCON_CON = FRPD_FCON_CON) FCON_DESC,
               FTRS_TRS,(SELECT DISTINCT FTAF_DESC FROM FTAFA WHERE FRPD_FTAF_TAF_PERSON=FTAF_TAF ) FRPD_FTAF_TAF_PERSON,1,
               to_char(FRPD_DESC),NULL,NULL,NULL,NULL FCHQ_DATE,
                NULL SH_GHABZ ,
                NULL SHENASE_P,
                NULL SHAKHS_DAR_VAJH,
                 NULL DESC_CHQ_FSTC   
            FROM    FRPDP, FTRSP, FPRCP
            WHERE   FPRC_PRC = FRPD_FPRC_PRC
            AND     FPRC_PAY_RCV_TYPE = 1
            AND     FRPD_TYPE IN (2,7)
            AND     FRPD_STATE <> 4
            AND     FRPD_FTAF_TAF_PEYER =  FTRS_FTAF_TAF   ---کاشاني ---پاييني رو کامنت کردم و بالايي رو از کامنت در اوردم
            --AND     FRPD_FTRS_TRS_PEYER = FTRS_TRS
            AND     FRPD_STOCK_DOC IS NULL
            AND     (FTRS_TRS =:P102_BANK /*P_FTAF_TAF*/ OR :P102_BANK/*P_FTAF_TAF*/ IS NULL)
----------------------------------------------------------------------------------------------------------------------------
union all
               -- پرداخت نقد و انتقالي نقد - گروهي --منفي مبالغ مثبت
               SELECT  --NVL(SUM((-1)*FRPD_AMOUNT),0)
               FTRS_DESC, FRPD_DATE,(-1)*frpd_amount,CO_FNC_GET_CGREF_DESC('ACTION_TYPE',FRPD_TYPE) FRPD_TYPE_DESC,
               FPRC_CODE,FRPD_ROW_NUM,FPRC_TYPE,
               (SELECT FCON_DESC FROM FCONP WHERE FCON_CON = FRPD_FCON_CON) FCON_DESC,
               FTRS_TRS,(SELECT FTAF_DESC FROM FTAFA WHERE FRPD_FTAF_TAF_PERSON=FTAF_TAF) FRPD_FTAF_TAF_PERSON,1,
               to_char(FRPD_DESC),NULL,NULL,NULL,NULL FCHQ_DATE,
                NULL SH_GHABZ ,
                NULL SHENASE_P,
                NULL SHAKHS_DAR_VAJH  ,
               NULL DESC_CHQ_FSTC
               FROM    FRPDP, FTRSP, FPRCP
            WHERE   FPRC_PRC = FRPD_FPRC_PRC
            --AND     FRPD_STATE = P_FRPD_STATE
            AND     FPRC_PAY_RCV_TYPE = 2
            --AND     FRPD_BED_OR_BES = 2
            AND     FRPD_TYPE IN (2,7)
            AND     FRPD_STATE <> 4
            AND     FRPD_FTAF_TAF_PEYER =  FTRS_FTAF_TAF
            --and     FPRC_STOCK_DOC <> 1 
            AND     FRPD_STOCK_DOC IS NULL
            AND     (FTRS_TRS =:P102_BANK /*P_FTAF_TAF*/ OR :P102_BANK/*P_FTAF_TAF*/ IS NULL)
--------------------------------------------------------------------------------------------------------------------------------
 union all     
          -- پرداخت اوراق بهادار - چک روز و چک مدت دار زمان پرداخت آن فرا رسيده- تکي
          SELECT  --NVL(SUM((-1)*FRPD_AMOUNT),0)
               FTRS_DESC,
               CASE 
                WHEN Q.FCHQ_WITH_TIME = 2 THEN   FCHQ_DATE
                WHEN Q.FCHQ_WITH_TIME = 1 THEN  (SELECT FPRT_DATE
                FROM FPRTP WHERE FPRT_PRT =(SELECT MAX(FPRT_PRT)FROM FPRTP WHERE FPRT_FCHQ_CHQ = Q.FCHQ_CHQ AND FPRT_FRPD_RPD = FRPD_RPD AND FPRT_TYPE = 4 ))
                END FRPD_DATE,
               (-1)*FCHQ_FRPD_AMOUNT,CO_FNC_GET_CGREF_DESC('ACTION_TYPE',FRPD_TYPE) FRPD_TYPE_DESC,
               FPRC_CODE,FRPD_ROW_NUM,FPRC_TYPE,
               (SELECT FCON_DESC FROM FCONP WHERE FCON_CON = FRPD_FCON_CON) FCON_DESC,
               FTRS_TRS,(SELECT FTAF_DESC FROM FTAFA WHERE FRPD_FTAF_TAF_PERSON=FTAF_TAF ) FRPD_FTAF_TAF_PERSON,1,
               to_char(FRPD_DESC),NULL,NULL,to_char(FCHQ_CHEQUE_NO), TO_CHAR(FCHQ_DATE,'yyyy/MM/dd','nls_calendar=persian') FCHQ_DATE,
                NULL SH_GHABZ ,
                NULL SHENASE_P,
                 FCHQ_IN_MONEY SHAKHS_DAR_VAJH,
                 FCHQ_DESC DESC_CHQ_FSTC
            FROM    FRCPP C, FCHQP Q, FRPDP R, FTRSP T, FPRCP P
            WHERE   C.FRCP_FCHQ_CHQ = Q.FCHQ_CHQ
            AND     C.FRCP_FRPD_RPD = R.FRPD_RPD
            AND     P.FPRC_PRC = R.FRPD_FPRC_PRC
            AND     R.FRPD_FTAF_TAF_PEYER =  T.FTRS_FTAF_TAF
            AND     T.FTRS_TRS =:P102_BANK-- P_FTAF_TAF
            AND     R.FRPD_STOCK_DOC IN( 1,7)
            --AND     R.FRPD_STATE = P_FRPD_STATE
            AND     P.FPRC_PAY_RCV_TYPE = 1
            AND     R.FRPD_TYPE IN (4,7)
            and     FCHQ_GUARANTY=2 --by kashani
            and     fprc_state <> 6  --by kashani  990908
            AND     FCHQ_CANCEL = 2 -- غير ابطال
        AND    (Q.FCHQ_WITH_TIME = 2 OR
               (Q.FCHQ_WITH_TIME = 1 AND
                    Q.FCHQ_CHQ = (SELECT FPRT_FCHQ_CHQ
                                  FROM   FPRTP
                      WHERE  FPRT_PRT =  (SELECT MAX(FPRT_PRT)FROM FPRTP WHERE FPRT_FCHQ_CHQ = Q.FCHQ_CHQ AND FPRT_FRPD_RPD = R.FRPD_RPD AND FPRT_TYPE =4 AND FPRT_DATE between  TO_DATE(:P102_FR_DATE,'yyyy/MM/dd','nls_calendar=persian')
       and 
       TO_DATE(:P102_TO_DATE,'yyyy/MM/dd','nls_calendar=persian') ))
               ))   
 ------------------------------------------------------------------------------------------------------------------------------------
     union all        
          -- پرداخت اوراق بهادار - چک روز و چک مدت دار  زمان پرداخت آن فرا رسيده- گروهي
          SELECT  DISTINCT --NVL(SUM((-1)*FRPD_AMOUNT),0)
               FTRS_DESC, 
                CASE 
                WHEN Q.FCHQ_WITH_TIME = 2 THEN   FCHQ_DATE
                WHEN Q.FCHQ_WITH_TIME = 1 THEN  (SELECT FPRT_DATE
                FROM FPRTP WHERE FPRT_PRT =(SELECT MAX(FPRT_PRT)FROM FPRTP WHERE FPRT_FCHQ_CHQ = Q.FCHQ_CHQ AND FPRT_FRPD_RPD = FRPD_RPD AND FPRT_TYPE = 4 ))
                --END FRPD_DATE,---JANIPOUR
                 END FPRC_DATE,--JANIPOUR
               --(-1)*FRPD_AMOUNT,--JANIPOUR
             (SELECT -1*SUM(FRPD_AMOUNT) FROM FRPDP a WHERE a.FRPD_FPRC_PRC=P.FPRC_PRc AND  a.FRPD_FTAF_TAF_PEYER =  T.FTRS_FTAF_TAF and a.frpd_fcon_con=r.frpd_fcon_con) ,----JANIPOUR
               -- CO_FNC_GET_CGREF_DESC('ACTION_TYPE',FRPD_TYPE) FRPD_TYPE_DESC,---JANIPOUR
               CASE WHEN FPRC_TYPE=1 THEN 'دریافت'   WHEN  FPRC_TYPE=2 THEN 'پرداخت' END  FRPD_TYPE_DESC,---JANIPOUR
               FPRC_CODE,
              NULL FRPD_ROW_NUM,---JANIPOUR
              FPRC_TYPE,
               (SELECT FCON_DESC FROM FCONP WHERE FCON_CON = FRPD_FCON_CON) FCON_DESC,
               FTRS_TRS,
              ----(SELECT FTAF_DESC FROM FTAFA WHERE FRPD_FTAF_TAF_PERSON=FTAF_TAF ) FRPD_FTAF_TAF_PERSON,---JANIPOUR
           NULL  FRPD_FTAF_TAF_PERSON,---JANIPOUR
                     1,
               --to_char(FRPD_DESC),---JANIPOUR
                 to_char(FPRC_DESC),---JANIPOUR
                NULL,NULL,to_char(FCHQ_CHEQUE_NO),TO_CHAR(FCHQ_DATE,'yyyy/MM/dd','nls_calendar=persian') FCHQ_DATE,
                NULL SH_GHABZ ,
                NULL SHENASE_P,
                 FCHQ_IN_MONEY   SHAKHS_DAR_VAJH,
                 FCHQ_DESC DESC_CHQ_FSTC
            --INTO    V_AMOUNT_2  --22847894357
            FROM    FCHQP Q, FRPDP R, FTRSP T, FPRCP P
            WHERE   Q.FCHQ_FPRC_PRC = P.FPRC_PRC
            AND     P.FPRC_PRC = R.FRPD_FPRC_PRC
            AND     R.FRPD_FTAF_TAF_PEYER =  T.FTRS_FTAF_TAF
            AND     T.FTRS_TRS =:P102_BANK-- P_FTAF_TAF
            AND     R.FRPD_STOCK_DOC IN( 1,7)
            --AND     R.FRPD_STATE = P_FRPD_STATE
            AND     P.FPRC_PAY_RCV_TYPE = 2
            AND     R.FRPD_TYPE IN (4,7)
            and     FCHQ_GUARANTY=2 --by kashani
            and     fprc_state <> 6  --by kashani  990908
            AND     FCHQ_CANCEL = 2 -- غير ابطال
        AND    (Q.FCHQ_WITH_TIME = 2 OR
               (Q.FCHQ_WITH_TIME = 1 AND
                    Q.FCHQ_CHQ = (SELECT FPRT_FCHQ_CHQ
                                  FROM   FPRTP
                      WHERE  FPRT_PRT =  (SELECT MAX(FPRT_PRT)FROM FPRTP WHERE FPRT_FCHQ_CHQ = Q.FCHQ_CHQ AND FPRT_FRPD_RPD = R.FRPD_RPD AND FPRT_TYPE =4 AND FPRT_DATE between  TO_DATE(:P102_FR_DATE,'yyyy/MM/dd','nls_calendar=persian')
       and 
       TO_DATE(:P102_TO_DATE,'yyyy/MM/dd','nls_calendar=persian') ))
               ))
    ---FOROOZAN
 ------------------------------------------------------------------------------------------------------------
union all              -- پرداخت اوراق بهاداري - حوله- تکي
            SELECT  --NVL(SUM((-1)*Q.FSTC_FRPD_AMOUNT),0)
            FTRS_DESC,  --c
            FRPD_DATE,(-1)*FSTC_FRPD_AMOUNT,
            CO_FNC_GET_CGREF_DESC('ACTION_TYPE',FRPD_TYPE) FRPD_TYPE_DESC,
            FPRC_CODE,FRPD_ROW_NUM,FPRC_TYPE,
            (SELECT FCON_DESC FROM FCONP WHERE FCON_CON = FRPD_FCON_CON) FCON_DESC,
            FTRS_TRS,(SELECT DISTINCT FTAF_DESC FROM FTAFA WHERE FRPD_FTAF_TAF_PERSON=FTAF_TAF ) FRPD_FTAF_TAF_PERSON,1,
            to_char(FRPD_DESC),
            NULL FPRT_DATE,
            NULL FPRT_FOLLOWUP_ID,
             to_char(FSTC_STC_NO),TO_CHAR(FSTC_DATE,'yyyy/MM/dd','nls_calendar=persian') FCHQ_DATE,
                NULL SH_GHABZ ,
                NULL SHENASE_P,
                 FSTC_IN_MONEY   SHAKHS_DAR_VAJH,
                FSTC_DESC DESC_CHQ_FSTC
            FROM    FSTRP C, FSTCP Q, FRPDP R, FTRSP T, FPRCP P
            WHERE   C.FSTR_FSTC_STC = Q.FSTC_STC
            AND     C.FSTR_FRPD_RPD = R.FRPD_RPD
            AND     P.FPRC_PRC = R.FRPD_FPRC_PRC
            AND     R.FRPD_FTAF_TAF_PEYER =  T.FTRS_FTAF_TAF
            AND     T.FTRS_TRS = :P102_BANK
            AND     R.FRPD_STOCK_DOC = 3
            and     FSTC_GUARANTY=2  ---kashani
            --AND     R.FRPD_STATE = P_FRPD_STATE
            AND     P.FPRC_PAY_RCV_TYPE = 1
            AND     R.FRPD_TYPE IN (4,7)
            /*AND     FRPD_DATE between P_FR_DATE AND P_TO_DATE*/
--------------------------------------------------------------------------------
union all   
            -- پرداخت اوراق بهاداري - حوله- -گروهي
            SELECT  --NVL(SUM((-1)*FRPD_AMOUNT),0)
            FTRS_DESC,  --c
            FRPD_DATE, (-1)*FRPD_AMOUNT,
            CO_FNC_GET_CGREF_DESC('ACTION_TYPE',FRPD_TYPE) FRPD_TYPE_DESC,
            FPRC_CODE,FRPD_ROW_NUM,FPRC_TYPE,
            (SELECT FCON_DESC FROM FCONP WHERE FCON_CON = FRPD_FCON_CON) FCON_DESC,
            FTRS_TRS,(SELECT DISTINCT FTAF_DESC FROM FTAFA WHERE FRPD_FTAF_TAF_PERSON=FTAF_TAF ) FRPD_FTAF_TAF_PERSON,1,
            to_char(FRPD_DESC),
            NULL FPRT_DATE,
            NULL FPRT_FOLLOWUP_ID,
             to_char(FSTC_STC_NO),TO_CHAR(FSTC_DATE,'yyyy/MM/dd','nls_calendar=persian') FCHQ_DATE,
                NULL SH_GHABZ ,
                NULL SHENASE_P,
               FSTC_IN_MONEY   SHAKHS_DAR_VAJH,
               FSTC_DESC DESC_CHQ_FSTC
            --INTO    V_AMOUNT_2
            FROM    FSTCP Q, FRPDP R, FTRSP T, FPRCP P
            WHERE   Q.FSTC_FPRC_PRC = P.FPRC_PRC
            AND     P.FPRC_PRC = R.FRPD_FPRC_PRC
            AND     R.FRPD_FTAF_TAF_PEYER =  T.FTRS_FTAF_TAF
            AND     T.FTRS_TRS = :P102_BANK
            AND     R.FRPD_STOCK_DOC = 3
            and     FSTC_GUARANTY=2  ---kashani
            --AND     R.FRPD_STATE = P_FRPD_STATE
            AND     P.FPRC_PAY_RCV_TYPE = 2
            AND     R.FRPD_TYPE IN (4,7)
 -----------------------------------------------------------------------------------------------           
)  --22
   WHERE    FRPD_DATE  between
   TO_DATE(:P102_FR_DATE,'yyyy/MM/dd','nls_calendar=persian')
   and 
   TO_DATE(:P102_TO_DATE,'yyyy/MM/dd','nls_calendar=persian')
  
--********************************************************************************************************
UNION ALL       
                SELECT  --NVL(SUM(FTRS_FIRST_STOCK),0)
                FTRS_DESC ,
                NULL FRPD_DATE,
                --FTRS_FIRST_STOCK,   
                (select FTRS_FIRST_STOCK+
                FIN.FP_FUN_REM_DATE(:P102_BANK,TO_DATE(:P102_FR_DATE,'yyyy/MM/dd','nls_calendar=persian')-99999,TO_DATE(:P102_FR_DATE,'yyyy/MM/dd','nls_calendar=persian')-1)
                from ftrsp
                where FTRS_TRS =:P102_BANK and FTRS_TYPE=6) FRPD_AMOUNT,
                NULL FRPD_TYPE_DESC,
                NULL FPRC_CODE,
                NULL FRPD_ROW_NUM,
                NULL FPRC_TYPE,
                NULL FCON_DESC,
                FTRS_TRS,
                NULL FRPD_FTAF_TAF_PERSON,
                NULL,
                'موجودی اول دوره' FRPD_DESC ,
                NULL FBRC_DATE,
                NULL B,
                NULL A,
                NULL  FCHQ_DATE,
                NULL SH_GHABZ ,
                NULL SHENASE_P,
                NULL SHAKHS_DAR_VAJH,
                NULL DESC_CHQ_FSTC
            FROM    FTRSP
            WHERE   (FTRS_TRS =:P102_BANK  OR :P102_BANK  IS NULL)  
)     --11

 WHERE     :P102_SET_V=2 ;  
