Sebbene sia possibile incollare un URI MongoLab nel codice, per una migliore gestibilità è consigliabile configurarlo nell'ambiente. In questo modo, se l'URI cambia sarà possibile aggiornarlo tramite il portale di Azure senza dover intervenire sul codice.

1.  Nel portale di Azure selezionare **Siti Web**.
2.  Fare clic sul nome del sito Web nell'elenco di siti Web.
    ![WebSiteEntry][WebSiteEntry]
    Sarà visualizzato il dashboard del sito Web.

3.  Fare clic su **Configura** sulla barra dei menu.
    ![WebSiteDashboardConfig][WebSiteDashboardConfig]

4.  Scorrere verso il basso fino alla sezione Stringhe di connessione.
    ![WebSiteConnectionStrings][WebSiteConnectionStrings]

5.  In **Name** digitare MONGOLAB\_URI.
6.  In **Value** incollare la stringa di connessione ottenuta nella sezione precedente.
7.  Selezionare **Custom** nell'elenco a discesa **Type** (anziché la voce predefinita, **SQLAzure**).
8.  Sulla barra degli strumenti fare clic su **Salva**.  
    ![SaveWebSite][SaveWebSite]

**Nota:** Azure aggiunge il prefisso **CUSTOMCONNSTR\_** a questa variabile ed è per questo motivo che il codice riportato sopra fa riferimento a **CUSTOMCONNSTR\_MONGOLAB\_URI.**

  [WebSiteEntry]: ./media/howto-save-connectioninfo-mongolab/entry-website.png
  [WebSiteDashboardConfig]: ./media/howto-save-connectioninfo-mongolab/focus-mongolab-websitedashboard-config.png
  [WebSiteConnectionStrings]: ./media/howto-save-connectioninfo-mongolab/focus-mongolab-websiteconnectionstring.png
  [SaveWebSite]: ./media/howto-save-connectioninfo-mongolab/button-website-save.png
