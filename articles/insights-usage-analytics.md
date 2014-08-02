<properties title="How to use end user analytics" pageTitle="How to use end user analytics" description="Learn about end user analytics in Azure." authors="vladj" />

Analisi di siti Web
===================

Ci si può domandare quanti utenti abbiano visitato un sito. E quale sia il tempo di caricamento medio delle pagine o quale browser venga utilizzato. Mediante l'inserimento di poche righe di script nelle pagine di un sito Web è possibile raccogliere dati sull'utilizzo del sito da parte dei clienti.

   ![Analisi dell'utente finale](./media/insights-usage-analytics/Insights_ConfiguredExperience.png)

Come configurare l'analisi dell'utente finale
---------------------------------------------

1.  Fare clic sulla parte **End user analytics** nel blade **Web site**.
2.  Nel blade **Configuration**, selezionare e copiare l'intero script di strumentazione.

   ![Configurazione](./media/insights-usage-analytics/Insights_CopyCode.png)

3.  Incollare lo script in tutte le pagine Web poco prima della chiusura del &lt;/head\> tag. È consigliabile inserire lo script in tutte le pagine del sito Web. Se si utilizza ASP.NET, è possibile inserire lo script nella pagina master dell'applicazione.

4.  Distribuire e utilizzare l'applicazione Web. Le analisi sull'utilizzo del sito verranno visualizzate dopo circa 5-10 minuti.

Esame dei dati
--------------

La parte Browsers session consente di effettuare analisi relative ai diversi browser e alle loro diverse versioni.

   ![Browser](./media/insights-usage-analytics/Insights_Browsers.png)

Nella parte Analytics vengono visualizzate: 
- Una suddivisione dei diversi tipi di dispositivi, compresi Desktop e dispositivi mobili. 
- Le 5 pagine più visitate e grafici relativi al tempo di caricamento delle pagine nell'ultima settimana. Sono disponibili anche il numero di sessioni e visualizzazioni.

   ![Pagine più visitate](./media/insights-usage-analytics/Insights_TopPages.png)

-   Vengono visualizzate anche le pagine più lente nella settimana precedente, per consentire di porre rimedio e soddisfare gli obiettivi aziendali.

