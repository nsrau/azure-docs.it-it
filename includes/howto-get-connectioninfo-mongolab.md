Quando si esegue il provisioning di un database MongoLab, MongoLab trasmette un URI di connessione ad Azure nel formato della stringa di connessione standard di MongoDB. Questo valore viene usato per avviare una connessione MongoDB tramite il driver MongoDB desiderato. Per ulteriori informazioni sulle stringhe di connessione, vedere l'argomento relativo alle [connessioni](http://www.mongodb.org/display/DOCS/Connections) all'indirizzo mongodb.org.

**Questo URI contiene il nome utente e la password per il database. Trattare queste informazioni come dati sensibili e non condividerle.**

Per recuperare l'URI nel portale di Azure, attenersi alla procedura seguente:

1. Selezionare **Add-ons**. ![AddonsButton][button-addons]
1. Individuare il servizio MongoLab nell'elenco dei componenti aggiuntivi. ![MongolabEntry][entry-mongolabaddon]
1. Fare clic sul nome del componente aggiuntivo per passare alla relativa pagina.
1. Fare clic su **Connection Info**. ![ConnectionInfoButton][button-connectioninfo] Verrà visualizzato l'URI MongoLab: ![ConnectionInfoScreen][screen-connectioninfo]  
1.  Fare clic sul pulsante con il simbolo degli Appunti a destra del valore MONGOLAB_URI per copiare il valore completo negli Appunti.

[entry-mongolabaddon]: ./media/howto-get-connectioninfo-mongolab/entry-mongolabaddon.png
[button-connectioninfo]: ./media/howto-get-connectioninfo-mongolab/button-connectioninfo.png
[screen-connectioninfo]: ./media/howto-get-connectioninfo-mongolab/dialog-mongolab_connectioninfo.png
[button-addons]: ./media/howto-get-connectioninfo-mongolab/button-addons.png

<!---HONumber=July15_HO4-->