Attenersi alla procedura seguente per installare ed eseguire MongoDB in una macchina virtuale che esegue Windows Server.

<div class="dev-callout">
<b>Importante</b>
<p>Le funzionalità di sicurezza MongoDB, ad esempio l'autenticazione e il binding di indirizzi IP, non sono abilitate per impostazione predefinita. Dovranno essere abilitate prima di distribuire MongoDB in un ambiente di produzione.  Vedere <a href="http://www.mongodb.org/display/DOCS/Security+and+Authentication">sicurezza e autenticazione</a> per altre informazioni.</p>
</div>

1. Dopo avere eseguito la connessione alla macchina virtuale tramite Desktop remoto, aprire Internet Explorer dal menu **Start**.
2. Nell'angolo superiore destro fare clic sul pulsante **Strumenti**.  In **Opzioni Internet** selezionare la scheda **Sicurezza**, quindi l'icona **Siti attendibili** e infine fare clic sul pulsante **Siti**. Aggiungere *http://\*.mongodb.org* all'elenco dei siti attendibili.
3. Passare alla pagina dei [download di MongoDB] [MongoDownloads].
4. Individuare la versione più recente nella sezione **Production Release (Recommended)** e fare clic sul collegamento ***2008+** nella colonna relativa a Windows a 64 bit.  Fare clic su **Salva con nome** e salvare il file con estensione zip sul desktop.
5. Fare clic con il pulsante destro del mouse sul file zip e scegliere **Estrai tutto**.  Specificare "C:\" e fare clic su **Estrai**.  Dopo avere estratto i file, è possibile rinominare la cartella di installazione assegnandole un nome più intuitivo,  come ad esempio "MongoDB".
6. Creare le directory dei dati e dei log di MongoDB nel disco dati (unità **F:**, ad esempio) creato nei passaggi precedenti. Nel menu **Start** scegliere **Prompt dei comandi** per aprire una finestra del prompt dei comandi.  Digitare:

		C:\> F:
		F:\> mkdir \MongoData
		F:\> mkdir \MongoLogs

7. Per eseguire il database: 

		F:\> C:
		C:\> cd \MongoDB\bin
		C:\my_mongo_dir\bin> mongod --dbpath F:\MongoData\ --logpath F:\MongoLogs\mongolog.log

	Tutti i messaggi di log verranno indirizzati al file *F:\MongoLogs\mongolog.log* non appena viene avviato il server mongod.exe e vengono preallocati i file journal. Possono essere necessari diversi minuti per la preallocazione dei file journal di MongoDB e l'inizio dell'attesa delle connessioni.

8. Per avviare la shell di amministrazione di MongoDB, aprire un'altra finestra del prompt dei comandi dal menu **Start** e digitare le informazioni seguenti:

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
9. (Facoltativo) In mongod.exe è disponibile il supporto per l'installazione e l'esecuzione come servizio di Windows. Per installare mongod.exe come servizio, dal prompt dei comandi eseguire i comandi seguenti:

		C:\mongodb\bin>mongod --logpath "c:\mongodb\logs\logfile.log" --logappend --dbpath "c:\data" --install 

	Verrà creato un servizio denominato "Mongo DB" con la descrizione "Mongo DB". È necessario usare l'opzione **--logpath** per specificare un file di log, poiché il servizio in esecuzione non disporrà di una finestra di comando in cui visualizzare l'output.  L'opzione **--logpath** specifica che dopo il riavvio del servizio l'output verrà aggiunto al file di log esistente.  L'opzione **--dbpath** specifica il percorso della directory dati. Per altre opzioni della riga di comando relative ai servizi, vedere [Opzioni della riga di comando relative ai servizi][MongoWindowsSvcOptions].
10. Ora che MongoDB è stato installato ed è in esecuzione, è necessario aprire una porta in Windows Firewall per poter eseguire la connessione remota a MongoDB.  Dal menu **Start** scegliere **Strumenti di amministrazione** e quindi **Windows Firewall con protezione avanzata**. 

11. Nel riquadro sinistro selezionare **Regole connessioni in entrata**.  Nel riquadro **Azioni** a destra selezionare **Nuova regola**.
	
	![Windows Firewall][Image1]

	In **Creazione guidata nuova regola connessioni in entrata** selezionare **Porta** e quindi fare clic su **Avanti**.
	
	![Windows Firewall][Image2]

	Selezionare **TCP** e quindi **Porte locali specifiche**.  Specificare la porta "27017" (ovvero la porta su cui MongoDB rimane in ascolto) e fare clic su **Avanti**.

	![Windows Firewall][Image3]

	Selezionare **Consenti la connessione** e fare clic su **Avanti**.

	![Windows Firewall][Image4]

	Fare di nuovo clic su **Avanti**.
	
	![Windows Firewall][Image5]

	Specificare un nome per la regola, ad esempio "PortaMongo" e quindi fare clic su **Fine**.

	![Windows Firewall][Image6]
	
12. A questo punto è possibile configurare un endpoint per MongoDB se non è stato già fatto durante la creazione della macchina virtuale. Per la connessione a MongoDB in modalità remota, sono necessari sia la regola firewall che l'endpoint. Nel portale di gestione fare clic su **Macchine virtuali**, quindi sul nome della nuova macchina virtuale e infine su **Endpoint**.

	![Endpoints][Image7]
13. Fare clic su **Aggiungi endpoint** nella parte inferiore della pagina. Selezionare **Aggiungi endpoint** e quindi fare clic su **Avanti**.
	
	![Endpoints][Image8]

14. Aggiungere un endpoint denominato "Mongo", con protocollo **TCP** e con entrambe le porte **pubblica** e **privata** impostate su "27017". In questo modo sarà possibile accedere a MongoDB in remoto.

	![Endpoints][Image9]

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

<!--HONumber=35_1-->
