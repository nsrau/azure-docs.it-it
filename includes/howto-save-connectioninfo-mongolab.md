Sebbene sia possibile incollare un URI MongoLab nel codice, per una migliore gestibilità è consigliabile configurarlo nell'ambiente. In questo modo, se l'URI cambia sarà possibile aggiornarlo tramite il portale di Azure senza dover intervenire sul codice.

1. Nel portale di Azure selezionare **App Web**.
2. Fare clic sul nome dell'app nell'elenco delle app Web.  
   ![WebAppEntry][entry-website]  
   Verrà visualizzato il dashboard dell'app Web.
3. Fare clic su **Configure** sulla barra dei menu.  
   ![WebAppDashboardConfig][focus-mongolab-websitedashboard-config]
4. Scorrere verso il basso fino alla sezione Connection Strings.  
   ![WebAppConnectionStrings][focus-mongolab-websiteconnectionstring]
5. In **Nome** digitare MONGOLAB_URI.
6. In **Value**incollare la stringa di connessione ottenuta nella sezione precedente.
7. Selezionare **Personalizzata** nell'elenco a discesa **Tipo** anziché la voce predefinita **SQLAzure**.
8. Fare clic su **Save** sulla barra degli strumenti.  
   ![SaveWebApp][button-website-save]

**Nota:** Azure aggiunge il prefisso **CUSTOMCONNSTR\_** a questa variabile ed è per questo motivo che il codice precedente fa riferimento a **CUSTOMCONNSTR\_MONGOLAB_URI**.

[entry-website]: ./media/howto-save-connectioninfo-mongolab/entry-website.png
[focus-mongolab-websitedashboard-config]: ./media/howto-save-connectioninfo-mongolab/focus-mongolab-websitedashboard-config.png
[focus-mongolab-websiteconnectionstring]: ./media/howto-save-connectioninfo-mongolab/focus-mongolab-websiteconnectionstring.png
[button-website-save]: ./media/howto-save-connectioninfo-mongolab/button-website-save.png


<!--HONumber=Jan17_HO3-->


