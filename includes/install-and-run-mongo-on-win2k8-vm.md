Seguire questa procedura per installare ed eseguire MongoDB in una macchina virtuale che esegue Windows Server.

> [!IMPORTANT]
> Le funzionalità di sicurezza MongoDB, ad esempio l'autenticazione e l'associazione di indirizzi IP, non sono abilitate per impostazione predefinita. Dovranno essere abilitate prima di distribuire MongoDB in un ambiente di produzione.  Per altre informazioni, vedere [Security and Authentication](http://www.mongodb.org/display/DOCS/Security+and+Authentication)Sicurezza e autenticazione.
> 
> 

1. Dopo avere eseguito la connessione alla macchina virtuale tramite Desktop remoto, aprire Internet Explorer dal menu **Start** sulla macchina virtuale.
2. Nell'angolo superiore destro fare clic sul pulsante **Strumenti** .  In **Opzioni Internet** selezionare la scheda **Sicurezza**, quindi l'icona **Siti attendibili** e infine fare clic sul pulsante **Siti**. Aggiungere *https://\*.mongodb.org* all'elenco dei siti attendibili.
3. Passare alla pagina [MongoDB Download Center](https://www.mongodb.com/download-center#community) (Area download di MongoDB).
4. Trovare la **versione stabile corrente** di **Community Server**selezionare la versione più recente a **64 bit** nella colonna di Windows. Scaricare ed eseguire il programma di installazione MSI.
5. MongoDB viene in genere installato in C:\Programmi\MongoDB. Cercare le variabili di ambiente sul desktop e aggiungere il percorso di file binari MongoDB alla variabile PATH. Ad esempio, si potrebbero individuare i file binari in C:\Program Files\MongoDB\Server\3.2\bin on your machine.
6. Creare le directory dei dati e dei log di MongoDB nel disco dati, ad esempio l'unità **F:**, creato nei passaggi precedenti. Dal menu **Start** scegliere **Prompt dei comandi** per aprire una finestra del prompt dei comandi.  Digitare:
   
        C:\> F:
        F:\> mkdir \MongoData
        F:\> mkdir \MongoLogs
7. Per eseguire il database:
   
        F:\> C:
        C:\> mongod --dbpath F:\MongoData\ --logpath F:\MongoLogs\mongolog.log
   
    Tutti i messaggi di log vengono indirizzati al file *F:\MongoLogs\mongolog.log* non appena il server mongod.exe viene avviato e vengono preallocati i file journal. Possono essere necessari diversi minuti per la preallocazione dei file journal di MongoDB e l'inizio dell'attesa delle connessioni. Il prompt dei comandi continua con questa attività durante l'esecuzione dell'istanza di MongoDB.
8. Per avviare la shell di amministrazione di MongoDB, aprire un'altra finestra del prompt dei comandi dal menu **Start** e digitare i comandi seguenti:
   
        C:\> cd \my_mongo_dir\bin  
        C:\my_mongo_dir\bin> mongo  
        >db  
        test
        > db.foo.insert( { a : 1 } )  
        > db.foo.find()  
        { _id : ..., a : 1 }  
        > show dbs  
        ...  
        > show collections  
        ...  
        > help  
   
    Il database viene creato dall'istruzione insert.
9. In alternativa, è possibile installare mongod.exe come servizio:
   
        C:\> mongod --dbpath F:\MongoData\ --logpath F:\MongoLogs\mongolog.log --logappend  --install
   
    Viene installato un servizio denominato MongoDB con la descrizione "MongoDB". Per specificare un file di log è necessario usare l'opzione `--logpath`, perché il servizio in esecuzione non ha una finestra di comando in cui visualizzare l'output.  L'opzione `--logappend` specifica che a seguito di un riavvio del servizio l'output viene aggiunto al file di log esistente.  L'opzione `--dbpath` specifica il percorso della directory dei dati. Per altre opzioni della riga di comando relative ai servizi, vedere le [opzioni della riga di comando relative ai servizi][MongoWindowsSvcOptions].
   
    Per avviare il servizio, eseguire questo comando:
   
        C:\> net start MongoDB
10. Ora che MongoDB è stato installato ed è in esecuzione, è necessario aprire una porta in Windows Firewall per poter eseguire la connessione remota a MongoDB.  Dal menu **Start** scegliere **Strumenti di amministrazione** e quindi **Windows Firewall con sicurezza avanzata**.
11. a) Nel riquadro a sinistra selezionare **Regole connessioni in entrata**.  Nel riquadro **Azioni** a destra selezionare **Nuova regola**.
    
    ![Windows Firewall][Image1]
    
    b) In **Creazione guidata nuova regola connessioni in entrata** selezionare **Porta** e quindi fare clic su **Avanti**.
    
    ![Windows Firewall][Image2]
    
    c) Selezionare **TCP** e quindi **Porte locali specifiche**.  Specificare la porta "27017" (ovvero la porta su cui MongoDB rimane in ascolto) e fare clic su **Avanti**.
    
    ![Windows Firewall][Image3]
    
    d) Selezionare **Consenti la connessione** e fare clic su **Avanti**.
    
    ![Windows Firewall][Image4]
    
    e) Fare di nuovo clic su **Avanti**.
    
    ![Windows Firewall][Image5]
    
    f) Specificare un nome per la regola, ad esempio "PortaMongo" e quindi fare clic su **Fine**.
    
    ![Windows Firewall][Image6]
12. A questo punto è possibile configurare un endpoint per MongoDB se non è stato già fatto durante la creazione della macchina virtuale. Per la connessione a MongoDB in modalità remota, sono necessari sia la regola firewall che l'endpoint. Nel portale di gestione fare clic su **Macchine virtuali**, quindi sul nome della nuova macchina virtuale e infine su **Endpoint**.
    
    ![Endpoint][Image7]
13. Fare clic su **Add** nella parte inferiore della pagina. Selezionare **Aggiungi un endpoint autonomo** e fare clic su **Avanti**.
    
    ![Endpoint][Image8]
14. Aggiungere un endpoint denominato "Mongo", con il protocollo **TCP** e con entrambe le porte **pubblica** e **privata** impostate su "27017". L'apertura di questa porta consente l'accesso in remoto a MongoDB.
    
    ![Endpoint][Image9]

> [!NOTE]
> La porta 27017 è la porta predefinita utilizzata da MongoDB. È possibile modificare la porta predefinita specificando il parametro `--port` all'avvio del server mongod.exe. Assicurarsi di assegnare lo stesso numero di porta al firewall e all'endpoint "Mongo" nei passaggi precedenti.
> 
> 

[MongoDownloads]: http://www.mongodb.org/downloads

[MongoWindowsSvcOptions]: http://www.mongodb.org/display/DOCS/Windows+Service


[Image1]: ./media/install-and-run-mongo-on-win2k8-vm/WinFirewall1.png
[Image2]: ./media/install-and-run-mongo-on-win2k8-vm/WinFirewall2.png
[Image3]: ./media/install-and-run-mongo-on-win2k8-vm/WinFirewall3.png
[Image4]: ./media/install-and-run-mongo-on-win2k8-vm/WinFirewall4.png
[Image5]: ./media/install-and-run-mongo-on-win2k8-vm/WinFirewall5.png
[Image6]: ./media/install-and-run-mongo-on-win2k8-vm/WinFirewall6.png
[Image7]: ./media/install-and-run-mongo-on-win2k8-vm/WinVmAddEndpoint.png
[Image8]: ./media/install-and-run-mongo-on-win2k8-vm/WinVmAddEndpoint2.png
[Image9]: ./media/install-and-run-mongo-on-win2k8-vm/WinVmAddEndpoint3.png


<!--HONumber=Oct16_HO2-->


