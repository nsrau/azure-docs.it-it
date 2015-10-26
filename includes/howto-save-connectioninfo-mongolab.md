Sebbene sia possibile incollare un URI MongoLab nel codice, per una migliore gestibilità è consigliabile configurarlo nell'ambiente. In questo modo, se l'URI cambia sarà possibile aggiornarlo tramite il portale di Azure senza dover intervenire sul codice.


1. Nel portale di Azure selezionare **App Web**.
1. Fare clic sul nome dell'app nell'elenco delle app Web. ![WebAppEntry][entry-website] Verrà visualizzato il dashboard dell'app Web.

1. Fare clic su **Configure** sulla barra dei menu. ![WebAppDashboardConfig][focus-mongolab-websitedashboard-config]

1. Scorrere verso il basso fino alla sezione Connection Strings. ![WebAppConnectionStrings][focus-mongolab-websiteconnectionstring]

1. In **Name** digitare MONGOLAB\_URI.
1. In **Value** incollare la stringa di connessione ottenuta nella sezione precedente.
1. Selezionare **Custom** nell'elenco a discesa **Type** (anziché la voce predefinita, **SQLAzure**).
1. Fare clic su **Save** sulla barra degli strumenti. ![SaveWebApp][button-website-save]

**Nota:** Azure aggiunge il prefisso **CUSTOMCONNSTR\_** a questa variabile, ed è per questo motivo che il codice sopra riportato fa riferimento a **CUSTOMCONNSTR\_MONGOLAB\_URI**.

[entry-website]: ./media/howto-save-connectioninfo-mongolab/entry-website.png
[focus-mongolab-websitedashboard-config]: ./media/howto-save-connectioninfo-mongolab/focus-mongolab-websitedashboard-config.png
[focus-mongolab-websiteconnectionstring]: ./media/howto-save-connectioninfo-mongolab/focus-mongolab-websiteconnectionstring.png
[button-website-save]: ./media/howto-save-connectioninfo-mongolab/button-website-save.png

<!---HONumber=Oct15_HO3-->