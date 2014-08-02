<properties linkid="manage-services-sql-databases-premium" urlDisplayName="Premium SQL Database" pageTitle="Sign up for Azure Premium for SQL Database" metaKeywords="" description="Describes how to sign up for the Premium for SQL Database preview, request your Premium database quota, and then upgrade a database to Premium in Azure SQL Database." metaCanonical="" services="cloud-services" documentationCenter="" title="Sign up for the preview of Premium for Azure SQL Database" authors="karaman" solutions="" manager="" editor="tysonn" />

Iscrizione all'anteprima di Premium per database SQL di Azure
=============================================================

In questa esercitazione si apprenderanno i passaggi necessari per partecipare all'anteprima del servizio Premium per database SQL.

Con il database SQL di Azure è stata rilasciata un'anteprima limitata di un nuovo servizio denominato Premium per database SQL. I database Premium offrono risorse riservate che garantiscono prestazioni più prevedibili per le applicazioni cloud.

[La funzionalità descritta in questo argomento è disponibile unicamente come anteprima. Questo argomento è costituito da documentazione preliminare ed è soggetto a modifiche in occasione di rilasci futuri.]

Sommario
--------

-   [Passaggio 1: Effettuare l'iscrizione all'anteprima di Premium per database SQL](#SignUp)
-   [Passaggio 2: Richiedere una quota del database Premium](#Quota)
-   [Passaggio 3: Creare un database Premium](#Upgrade)

Passaggio 1: Effettuare l'iscrizione all'anteprima di Premium per database SQL
------------------------------------------------------------------------------

Il primo passaggio per avvalersi di questa funzionalità consiste nell'effettuare l'iscrizione all'anteprima di Premium per database SQL.

1.  Accedere alla pagina relativa all'[anteprima delle funzionalità di Azure](http://account.windowsazure.com/PreviewFeatures) utilizzando il proprio account Microsoft.

    **Nota** - Solo gli amministratori dell'account Azure possono accedere al portale degli account. Se non si è l'amministratore dell'account per la sottoscrizione in uso, contattarlo per completare il processo di accesso alla sottoscrizione. Per ulteriori informazioni sugli account e le sottoscrizioni di Azure, vedere le [opzioni di acquisto](http://account.windowsazure.com/PreviewFeatures).

    ![Immagine1](./media/sql-database-premium-sign-up/AccountSignup-Figure1.png)

2.  Trovare l'elemento **Premium for SQL Database** nell'elenco delle funzionalità in anteprima e fare clic sul pulsante **try it now** associato all'elemento.

    ![Immagine2](./media/sql-database-premium-sign-up/AccountSignupButton-Figure2.png)

3.  Scegliere la sottoscrizione che si desidera iscrivere per l'anteprima.

    ![Immagine3](./media/sql-database-premium-sign-up/Subscription-Figure3.png)

    Solo le sottoscrizioni di Azure attive e pagate sono idonee per l'anteprima. È possibile iscrivere più sottoscrizioni, ma ognuna di esse può essere iscritta una sola volta.

    L'iscrizione di una sottoscrizione all'anteprima non comporterà addebiti aggiuntivi, ma una volta attivata e ottenuta la quota Premium, la creazione o l'aggiornamento a un database Premium comporterà addebiti in base ai prezzi indicati nella pagina [Dettagli prezzi del database SQL](http://www.windowsazure.com/en-us/pricing/details/sql-database/).

    Lo stato attuale della richiesta di iscrizione è riflesso nell'elenco delle funzionalità in anteprima.

    ![Immagine4](./media/sql-database-premium-sign-up/Status-Figure4.png)

4.  Le richieste verranno approvate in base alla capacità e alla domanda del momento. Potrebbe essere necessario attendere fino a 2 giorni per l'attivazione della sottoscrizione, con attese più lunghe in caso di elevata domanda o saturazione della capacità di anteprima destinata al pubblico.

5.  Dopo l'attivazione della sottoscrizione per l'anteprima, si riceverà una notifica tramite posta elettronica.

Passaggio 2: Richiedere una quota del database Premium
------------------------------------------------------

Dopo l'attivazione della sottoscrizione per l'anteprima, è necessario richiedere una quota del database Premium per ogni server su cui si intende creare un database Premium. Poiché la capacità è limitata, richiedere una quota solo per server su cui si prevede di creare un database Premium, eliminando eventuali richieste in sospeso che non sono più necessarie.

1.  Accedere al [portale di gestione di Azure](https://manage.windowsazure.com) con il proprio account Microsoft.

    **Nota** - Gli amministratori dell'account, gli amministratori del servizio e i coamministratori della sottoscrizione possono richiedere una quota dopo che la sottoscrizione sarà stata iscritta per l'anteprima.

2.  Passare all'elenco **Servers** nell'estensione **SQL Databases**.
3.  Selezionare i server per i quali si prevede di richiedere una quota di database Premium.
4.  Passare alla sezione **Quick Start** per il server selezionato facendo clic sull'icona a forma di fulmine nella barra di spostamento superiore.
5.  Fare clic su **Request Premium Database Quota** nella sezione **Premium Database**.

    ![Immagine6](./media/sql-database-premium-sign-up/RequestQuota-Figure6.png)

    Dopo l'invio della richiesta, potrebbe essere necessario attendere fino a 5 giorni per la concessione di una quota. L'attesa potrebbe essere più lunga in caso di elevata domanda o saturazione della capacità di anteprima.

    Alcune note aggiuntive in merito alle richieste di quote di database Premium:

    -   Le quote non sono disponibili per clienti con sottoscrizioni per versioni di valutazione gratuita.
    -   Le quote sono inizialmente limitate e verranno concesse in base alla domanda e alla capacità del momento.
    -   Trascorso 15 giorni, Microsoft potrà recuperare le quote non utilizzate.
    -   È possibile inviare una sola richiesta di quota per ogni server logico nella sottoscrizione.
    -   La quota è inizialmente limitata a un database per server logico.
    -   La richiesta di quota è gratuita, tuttavia, la creazione di un database edizione Premium o l'aggiornamento di un database edizione Web o Business esistente a Premium comporterà l'aumento del costo del database stesso.

6.  È possibile visualizzare lo stato della richiesta di quota nella pagina **Quick Start** del server.

    ![Immagine7](./media/sql-database-premium-sign-up/PendingApproval-Figure7.png)

7.  Dopo la concessione della quota del database Premium e quando la quota è disponibile per l'utilizzo, verrà inviata una notifica tramite posta elettronica.
8.  Una volta ottenuta la quota, sarà possibile verificare la quota di database Premium residua nella scheda **Quick Start** o **Dashboard** del server.

    ![Immagine8](./media/sql-database-premium-sign-up/QuotaApproved-Figure8.png)

Passaggio 3: Creare un database Premium
---------------------------------------

Dopo avere ottenuto una quota, è possibile creare un nuovo database edizione Premium o aggiornare un database edizione Web o Business esistente a Premium per sfruttare la capacità riservata e ottenere prestazioni più prevedibili.

![Immagine9](./media/sql-database-premium-sign-up/SpecifyDBSettings-Figure9.png)

![Immagine10](./media/sql-database-premium-sign-up/PremiumDBSettings-Figure10.png)

Per ulteriori informazioni, vedere [Gestione di un database Premium](http://go.microsoft.com/fwlink/p/?LinkID=311927).

**Nota:** è possibile modificare lo stato Premium della dimensione riservata per il database solo una volta in ogni periodo di 24 ore.

Passaggi successivi
-------------------

Per ulteriori informazioni sui database Premium, vedere:

-   [Gestione di un database Premium](http://go.microsoft.com/fwlink/p/?LinkID=311927)
-   [Linee guida per Premium per database SQL](http://go.microsoft.com/fwlink/p/?LinkId=313650)

