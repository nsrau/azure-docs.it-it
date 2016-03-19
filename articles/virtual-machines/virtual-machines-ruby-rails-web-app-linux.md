<properties
	pageTitle="Ospitare un sito Web Rails in una macchina virtuale di Linux su Ruby | Microsoft Azure"
	description="Impostare e ospitare un sito Web basato su Ruby on Rails in Azure usando una macchina virtuale Linux."
	services="virtual-machines"
	documentationCenter="ruby"
	authors="rmcmurray"
	manager="wpickett"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="web"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="ruby"
	ms.topic="article"
	ms.date="01/09/2016"
	ms.author="robmcm"/>





#Applicazione Web Ruby on Rails in una macchina virtuale di Azure

Questa esercitazione mostra come ospitare un sito Web Ruby on Rails usando una macchina virtuale Linux.

Questa esercitazione è stata convalidata usando Ubuntu Server 14.04 LTS. Se si usa una distribuzione Linux diversa, è necessario modificare la procedura per installare Rails.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Modello Gestione risorse.


## Creare una macchina virtuale di Azure

Iniziare creando una macchina virtuale di Azure con un'immagine Linux.

Per creare la VM, è possibile usare il portale di Azure classico o l'interfaccia della riga di comando (CLI) di Azure.

### Portale di gestione di Azure

1. Accedere al [portale di Azure classico](http://manage.windowsazure.com).
2. Fare clic su **Nuovo** > **Calcolo** > **Macchina virtuale** > **Creazione rapida**. Selezionare un'immagine Linux.
3. Immettere una password.

Dopo che viene eseguito il provisioning della macchina virtuale, fare clic sul nome della macchina virtuale e quindi fare clic su **Dashboard**. Trovare l'endpoint SSH, elencato in **Dettagli su SSH**.

### Interfaccia della riga di comando di Azure

Seguire i passaggi in [Creazione rapida di una macchina virtuale che esegue Linux in Azure][vm-instructions].

Al termine del provisioning della macchina virtuale, è possibile ottenere l'endpoint SSH eseguendo il comando seguente:

	azure vm endpoint list <vm-name>  

## Installare Ruby on Rails

1. Usare SSH per connettersi alla macchina virtuale.

2. Dalla sessione SSH usare i comandi seguenti per installare Ruby nella macchina virtuale:

		sudo apt-get update -y
		sudo apt-get upgrade -y
		sudo apt-get install ruby ruby-dev build-essential libsqlite3-dev zlib1g-dev nodejs -y

	L'installazione potrebbe richiedere alcuni minuti. Al termine, immettere il comando seguente per verificare se l'installazione di Ruby è stata completata:

		ruby -v

	Restituisce la versione di Ruby che è stata installata.

3. Immettere il comando seguente per installare Rails:

		sudo gem install rails --no-rdoc --no-ri

	Per velocizzare l'operazione, usare i flag --no-rdoc e --no-ri per ignorare l'installazione della documentazione.

## Creare ed eseguire un'applicazione

Mentre si è ancora connessi con SSH, eseguire i comandi seguenti:

	rails new myapp
	cd myapp
	rails server -b 0.0.0.0 -p 3000

Il comando [new](http://guides.rubyonrails.org/command_line.html#rails-new) crea una nuova applicazione Rails. Il comando [server](http://guides.rubyonrails.org/command_line.html#rails-server) avvia il server Web WEBrick fornito con Rails (per l'uso in un ambiente di produzione sarà opportuno usare un server differente, ad esempio Unicorn o Passenger).

L'output dovrebbe essere simile al seguente.

	=> Booting WEBrick
	=> Rails 4.2.1 application starting in development on http://0.0.0.0:3000
	=> Run `rails server -h` for more startup options
	=> Ctrl-C to shutdown server
	[2015-06-09 23:34:23] INFO  WEBrick 1.3.1
	[2015-06-09 23:34:23] INFO  ruby 1.9.3 (2013-11-22) [x86_64-linux]
	[2015-06-09 23:34:23] INFO  WEBrick::HTTPServer#start: pid=27766 port=3000


## Aggiungere un endpoint

1. Accedere al [portale di Azure classico][management-portal] e selezionare la VM.

	![Elenco di macchine virtuali][vmlist]

2. Selezionare **ENDPOINT** nella parte superiore della pagina, quindi fare clic su **+ AGGIUNGI ENDPOINT** nella parte inferiore.

	![Pagina di endpoint][endpoints]

3. Nella finestra di dialogo **AGGIUNGI ENDPOINT** selezionare "Aggiungi un endpoint autonomo", quindi fare clic su **Avanti**.

	![Finestra di dialogo New endpoint][new-endpoint1]

3. Nella finestra di dialogo successiva, immettere le informazioni seguenti:

	* **NOME**: HTTP

	* **PROTOCOLLO**: TCP

	* **PORTA PUBBLICA**: 80

	* **PORTA PRIVATA**: 3000

	Verrà creata una porta pubblica 80 che instraderà il traffico alla porta privata 3000, dove resta in ascolto il server Rails.

	![Finestra di dialogo New endpoint][new-endpoint]

4. Fare clic sul segno di spunta per salvare l'endpoint.

5. Dovrebbe essere visualizzato il messaggio **AGGIORNAMENTO IN CORSO**. Quando questo messaggio scompare, significa che l'endpoint è attivo. A questo punto è possibile testare l'applicazione passando al nome DNS della macchina virtuale. L'aspetto del sito Web dovrebbe essere simile al seguente:

	![Pagina predefinita Rails][default-rails-cloud]


##<a id="next"></a>Passaggi successivi

In questa esercitazione è stata eseguita la maggior parte dei passaggi manualmente. In un ambiente di produzione, si potrebbe scrivere l'applicazione in un computer di sviluppo e distribuirla nella macchina virtuale di Azure. Inoltre, la maggior parte degli ambienti di produzione ospita l'applicazione Rails insieme a un altro processo server, ad esempio Apache o NginX, che gestisce il routing delle richieste a più istanze dell'applicazione Rails e la distribuzione di risorse statiche. Per altre informazioni, vedere http://rubyonrails.org/deploy/.

Per altre informazioni su Ruby on Rails, vedere le [Guide di Ruby on Rails][rails-guides].

Per usare servizi di Azure dall'applicazione Ruby, vedere:

* [Archiviazione di dati non strutturati mediante BLOB][blobs]

* [Archiviazione di coppie chiave-valore mediante tabelle][tables]

* [Distribuzione di contenuti ad ampia larghezza di banda con la rete per la distribuzione di contenuti][cdn-howto]



<!-- WA.com links -->
[blobs]: ../storage-ruby-how-to-use-blob-storage.md

[cdn-howto]: /develop/ruby/app-services/

[management-portal]: https://manage.windowsazure.com/

[tables]: /develop/ruby/how-to-guides/table-service/

[vm-instructions]: virtual-machines-linux-tutorial.md


<!-- External Links -->
[rails-guides]: http://guides.rubyonrails.org/

[sqlite3]: http://www.sqlite.org/

<!-- Images -->

[default-rails-cloud]: ./media/virtual-machines-ruby-rails-web-app-linux/basicrailscloud.png

[vmlist]: ./media/virtual-machines-ruby-rails-web-app-linux/vmlist.png

[endpoints]: ./media/virtual-machines-ruby-rails-web-app-linux/endpoints.png

[new-endpoint]: ./media/virtual-machines-ruby-rails-web-app-linux/newendpoint.png

[new-endpoint1]: ./media/virtual-machines-ruby-rails-web-app-linux/newendpoint1.png

<!---HONumber=AcomDC_0114_2016-->