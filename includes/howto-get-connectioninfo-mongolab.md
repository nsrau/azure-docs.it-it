Quando si esegue il provisioning di un database MongoLab, MongoLab trasmette un URI di connessione ad Azure nel formato della stringa di connessione standard di MongoDB. Questo valore viene utilizzato per avviare una connessione MongoDB tramite il driver MongoDB desiderato. Per ulteriori informazioni sulle stringhe di connessione, vedere l'argomento relativo alle [connessioni][1] all'indirizzo mongodb.org.

**Questo URI contiene il nome utente e la password per il database. Trattare queste informazioni come dati sensibili e non condividerle.**

Per recuperare l'URI nel portale di Azure, attenersi alla procedura seguente:

1.  Selezionare **Add-ons**.  
     ![AddonsButton](./media/howto-get-connectioninfo-mongolab/button-addons.png)
2.  Individuare il servizio MongoLab nell'elenco dei componenti aggiuntivi.  
     ![MongolabEntry](./media/howto-get-connectioninfo-mongolab/entry-mongolabaddon.png)
3.  Fare clic sul nome del componente aggiuntivo per passare alla relativa pagina.
4.  Fare clic su **Connection Info**.  
     ![ConnectionInfoButton](./media/howto-get-connectioninfo-mongolab/button-connectioninfo.png)
    
     Verrà visualizzato l'URI MongoLab:  
     ![ConnectionInfoScreen](./media/howto-get-connectioninfo-mongolab/dialog-mongolab_connectioninfo.png)
5.  Fare clic sul pulsante con il simbolo degli Appunti a destra del valore MONGOLAB\_URI per copiare il valore completo negli Appunti.



[1]: http://www.mongodb.org/display/DOCS/Connections