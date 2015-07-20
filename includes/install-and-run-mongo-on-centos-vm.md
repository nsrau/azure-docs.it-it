Attenersi alla procedura seguente per installare ed eseguire MongoDB in una macchina virtuale che esegue CentOS Linux.

> [AZURE.WARNING]Le funzionalità di sicurezza MongoDB, ad esempio l'autenticazione e l'associazione di indirizzi IP, non sono abilitate per impostazione predefinita. Dovranno essere abilitate prima di distribuire MongoDB in un ambiente di produzione. Per altre informazioni, vedere l'argomento relativo a [sicurezza e amministrazione](http://www.mongodb.org/display/DOCS/Security+and+Authentication).

1. Configurare il sistema di gestione dei pacchetti (YUM) in modo da poter installare MongoDB. Creare un file */etc/yum.repos.d/10gen.repo* per conservare le informazioni relative all'archivio e aggiungere quanto segue:

		[10gen]
		name=10gen Repository
		baseurl=http://downloads-distro.mongodb.org/repo/redhat/os/x86_64
		gpgcheck=0
		enabled=1

2. Salvare il file repo e quindi eseguire il comando seguente per aggiornare il database del pacchetto locale:

		$ sudo yum update

3. Per installare il pacchetto, eseguire il comando seguente per installare la versione stabile più recente di MongoDB e gli strumenti associati:

		$ sudo yum install mongo-10gen mongo-10gen-server

	Attendere fino al termine del download e dell'installazione di MongoDB.

4. Creare una directory dati. Per impostazione predefinita, Mongo DB archivia i dati nella directory */data/db*, ma è innanzitutto necessario crearla. Per crearla, eseguire:

		$ sudo mkdir -p /srv/datadrive/data
		$ sudo chown `id -u` /srv/datadrive/data

	Per altre informazioni sull'installazione di MongoDB su Linux, vedere [Avvio rapido in Unix][QuickstartUnix].

5. Per avviare il database, eseguire:

		$ mongod --dbpath /srv/datadrive/data --logpath /srv/datadrive/data/mongod.log

	Tutti i messaggi di log vengono indirizzati al file */srv/datadrive/data/mongod.log* appena viene avviato il server MongoDB e vengono preallocati i file journal. Possono essere necessari diversi minuti per la preallocazione dei file journal di MongoDB e l'inizio dell'attesa delle connessioni.

6. Per avviare la shell di amministrazione di MongoDB, aprire una un'altra finestra SSH o PuTTY ed eseguire:

		$ mongo
		> db.foo.save ( { a:1 } )
		> db.foo.find()
		{ _id : ..., a : 1 }
		> show dbs  
		...
		> show collections  
		...  
		> help  

	Il database viene creato dall'istruzione insert.

7. Dopo l'installazione di MongoDB, è necessario configurare un endpoint in modo da potervi accedere in remoto. Nel portale di gestione fare clic su **Virtual Machines**, quindi sul nome della nuova macchina virtuale e infine su **Endpoints**.
	
	![Endpoint][Image7]

8. Fare clic su **Add Endpoint** nella parte inferiore della pagina.
	
	![Endpoint][Image8]

9. Aggiungere un endpoint con le impostazioni seguenti:

 - **Nome**: Mongo
 - **Protocollo**: TCP
 - **Porta pubblica**: 27017
 - **Porta privata**: 27017
 
 In questo modo sarà possibile accedere a MongoDB in remoto.



[QuickStartUnix]: http://www.mongodb.org/display/DOCS/Quickstart+Unix


[Image7]: ./media/install-and-run-mongo-on-centos-vm/LinuxVmAddEndpoint.png
[Image8]: ./media/install-and-run-mongo-on-centos-vm/LinuxVmAddEndpoint2.png

<!---HONumber=July15_HO2-->