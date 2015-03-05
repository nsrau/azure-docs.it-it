<properties 
	pageTitle="Applicazione Web Ruby on Rails in una macchina virtuale di Azure che esegue Linux" 
	description="Ospitare un sito Web basato su Ruby on Rails in Azure usando una macchina virtuale Linux." 
	services="virtual-machines" 
	documentationCenter="ruby" 
	authors="blackmist" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="web" 
	ms.tgt_pltfrm="vm-linux" 
	ms.devlang="ruby" 
	ms.topic="article" 
	ms.date="09/17/2014" 
	ms.author="larryfr"/>





# Applicazione Web Ruby on Rails in una macchina virtuale di Azure

Questa esercitazione illustra come ospitare un sito Web basato su Ruby on Rails in Azure usando una macchina virtuale Linux. In questa esercitazione si presuppone che l'utente non abbia mai usato Azure. Al termine dell'esercitazione, si disporrà di un'applicazione Web basata su Ruby on Rails in esecuzione nel cloud.

Si apprenderà come:

* Configurare l'ambiente di sviluppo

* Configurare una macchina virtuale di Azure per l'hosting di Ruby on Rails.

* Creare una nuova applicazione Rails

* Copiare i file nella macchina virtuale mediante scp 

Di seguito è riportata una schermata dell'applicazione completata:

![a browser displaying Listing Posts][blog-rails-cloud]

## Contenuto dell'articolo

* [Configurare l'ambiente di sviluppo](#setup)

* [Creare un'applicazione Rails](#create)

* [Testare l'applicazione](#test)

* [Creare una macchina virtuale di Azure](#createvm)

* [Copiare l'applicazione nella macchina virtuale](#copy)

* [Installare le gemme e avviare l'applicazione](#start)

* [Passaggi successivi](#next)

## <a id="setup"></a>Configurare l'ambiente di sviluppo

1. Installare Ruby nell'ambiente di sviluppo. La procedura può variare in base al sistema operativo.

	* **Apple OS X**: sono disponibili diverse distribuzioni di Ruby per OS X. Questa esercitazione è stata convalidata con OS X usando [Homebrew](http://brew.sh/) per installare **rbenv** e **ruby-build**. Le informazioni sull'installazione sono reperibili all'indirizzo [https://github.com/sstephenson/rbenv/](https://github.com/sstephenson/rbenv/).

	* **Linux**: usare il sistema di gestione di pacchetti delle distribuzioni. Questa esercitazione è stata convalidata con Ubuntu 12.10 usando i pacchetti ruby1.9.1 e ruby1.9.1-dev.

	* **Windows**: sono disponibili diverse distribuzioni Ruby per Windows. Questa esercitazione è stata convalidata usando [RailsInstaller](http://railsinstaller.org/) 1.9.3-p392.

2. Aprire una nuova riga di comando o una sessione terminal e immettere il comando seguente per installare Ruby on Rails:

		gem install rails --no-rdoc --no-ri

	> [AZURE.NOTE] Per questo comando, in alcuni sistemi operativi, possono essere necessari privilegi di amministratore o radice. Se si riceve un errore durante l'esecuzione di questo comando, provare a usare 'sudo' come indicato di seguito:
	>
	>````` 
	sudo gem install rails
	`````

	> [AZURE.NOTE] Per l'esercitazione è stata usata la versione 3.2.12 della gemma Rails.

3. È necessario installare anche un interprete JavaScript, che verrà usato da Rails per compilare le risorse CoffeeScript usate dall'applicazione Rails. L'elenco di interpreti supportati è disponibile all'indirizzo [https://github.com/sstephenson/execjs#readme](https://github.com/sstephenson/execjs#readme).
	
	[Node.js](http://nodejs.org/) è stato usato durante la convalida di questa esercitazione perché è disponibile per i sistemi operativi OS X, Linux e Windows.

## <a id="create"></a>Creare un'applicazione Rails

1. Dalla riga di comando o dalla sessione terminal creare una nuova applicazione Rails denominata "blog_app" immettendo il seguente comando:

		rails new blog_app

	Questo comando crea una nuova directory denominata **blog_app** e la popola con i file e le sottodirectory richiesti da un'applicazione Rails.

	> [AZURE.NOTE] Il completamento di questo comando può richiedere almeno un minuto. Esegue un'installazione invisibile all'utente delle gemme necessarie per un'applicazione predefinita e durante questo intervallo di tempo potrebbe sembrare bloccata.

2. Passare alla directory **blog_app** e quindi immettere il seguente comando per creare uno scaffolding per blog di base:

		rails generate scaffold Post name:string title:string content:text

	Verranno create le migrazioni di controller, viste, modelli e database usate per mantenere i post nel blog. A ogni post saranno associati un nome dell'autore, un titolo e un contenuto di testo.

3. Per creare il database in cui verranno archiviati i post del blog, immettere il comando seguente:

		rake db:migrate

	In questo modo per Rails verrà usato il provider di database predefinito, ovvero il [database SQLite3][sqlite3]. Anche se si può scegliere di usare un database diverso per un'applicazione di produzione, SQLite è sufficiente per gli scopi di questa esercitazione.

## <a id="test"></a>Testare l'applicazione

Per avviare il server Rails nell'ambiente di sviluppo, eseguire la procedura seguente

1. Dalla riga di comando o da una sessione terminal avviare il server Rails immettendo il comando seguente:

		rails s

	L'output dovrebbe essere simile al seguente. Si noti la porta su cui è in ascolto il server Web. Nell'esempio seguente è la porta 3000.

		=> Booting WEBrick
		=> Rails 3.2.12 application starting in development on http://0.0.0.0:3000
		=> Call with -d to detach
		=> Ctrl-C to shutdown server
		[2013-03-12 19:11:31] INFO  WEBrick 1.3.1
		[2013-03-12 19:11:31] INFO  ruby 1.9.3 (2012-04-20) [x86_64-linux]
		[2013-03-12 19:11:31] INFO  WEBrick::HTTPServer#start: pid=9789 port=3000

2. Aprire il browser e passare all'indirizzo http://localhost:3000/. Verrà visualizzata una pagina simile alla seguente:

	![default rails page][default-rails]

	Si tratta di una pagina iniziale statica. Per visualizzare i form generati dal comando di scaffolding, passare a http://localhost:3000/posts. Verrà visualizzata una pagina simile alla seguente:

	![a page listing posts][blog-rails]

	Per interrompere il processo server, immettere CTRL+C nella riga di comando

## <a id="createvm"></a>Creare una macchina virtuale di Azure

Seguire le istruzioni fornite [qui][vm-instructions] per creare una macchina virtuale di Azure che ospita Linux.

> [AZURE.NOTE] I passaggi di questa esercitazione sono stati eseguiti in una macchina virtuale Azure che ospita Ubuntu 12.10. Se si usa una distribuzione Linux diversa, è può essere necessario eseguire passaggi diversi per completare le stesse attività.

> [AZURE.IMPORTANT] È necessario creare **solo** la macchina virtuale. Interrompere la procedura dopo aver appreso come connettersi alla macchina virtuale tramite SSH.

Dopo aver creato la macchina virtuale di Azure, eseguire i passaggi seguenti per installarvi Ruby and Rails:

1. Dalla riga di comando o da una sessione terminal immettere il comando seguente per connettersi alla macchina virtuale tramite SSH:

		ssh username@vmdns -p port

	Sostituire il nome utente specificato durante la creazione della macchina virtuale, l'indirizzo DNS della VM e la porta dell'endpoint SSH. Ad esempio:

		ssh railsdev@railsvm.cloudapp.net -p 61830

	> [AZURE.NOTE] Se si usa Windows come ambiente di distribuzione è possibile eseguire un'utility, ad esempio **PuTTY**, per ottenere funzionalità SSH. PuTTY è disponibile nella [pagina di download corrispondente](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

2. Dalla sessione SSH usare i comandi seguenti per installare Ruby nella macchina virtuale:

		sudo apt-get update -y
		sudo apt-get upgrade -y
		sudo apt-get install ruby1.9.1 ruby1.9.1-dev build-essential libsqlite3-dev nodejs -y

	Al termine, immettere il comando seguente per verificare se l'installazione di Ruby è stata completata correttamente:

		ruby -v

	Questo comando dovrebbe restituire la versione di Ruby installata nella macchina virtuale, che può essere diversa da 1.9.1. Ad esempio, **ruby 1.9.3p194 (2012-04-20 revision 35410) [x86_64-linux]**.

2. Immettere il comando seguente per installare Bundler:

		sudo gem install bundler --no-rdoc --no-ri

	Bundler verrà usato per installare le gemme necessarie per l'applicazione Rails dopo la copia nel server.

## <a id="copy"></a>Copiare l'applicazione nella macchina virtuale

Dall'ambiente di sviluppo aprire una nuova riga di comando o una sessione terminal e immettere il comando **scp** per copiare la directory **blog_app** nella macchina virtuale. Il formato del comando è il seguente:

	scp -r -P 54822 -C directory-to-copy user@vmdns:

Ad esempio:

	scp -r -P 54822 -C ~/blog_app railsdev@railsvm.cloudapp.net:

> [AZURE.NOTE] Se si usa Windows come ambiente di sviluppo è possibile eseguire un'utility, ad esempio **pscp**, per ottenere funzionalità scp. Pscp è disponibile nella [pagina di download di PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

I parametri usati per questo comando hanno l'effetto seguente:

* **-r**: Copia in modo ricorsivo il contenuto della directory specificata e di tutte le sottodirectory

* **-P**: Usa la porta specificata per le comunicazioni SSH

* **-C**: Abilita la compressione

* **directory-to-copy**: La directory locale da copiare

* **user@vmdns**: L'indirizzo del computer in cui copiare i file, nonché l'account utente con cui accedervi

Dopo l'operazione di copia, la directory **blog_app** si troverà nella home directory users. Immettere i comandi seguenti nella sessione SSH con la macchina virtuale per visualizzare i file copiati:

	cd ~/blog_app
	ls

L'elenco di file restituito dovrebbe corrispondere ai file contenuti nella directory **blog_app** dell'ambiente di sviluppo.

## <a id="start"></a>Installare le gemme e avviare Rails

1. Nella macchina virtuale passare alla directory **blog_app** e immettere il comando seguente per installare le gemme specificate in **Gemfile**:

		sudo bundle install

2. Per creare il database, immettere il comando seguente:

		rake db:migrate

3. Per avviare il server, immettere il comando seguente:
	
		rails s

	L'output dovrebbe essere simile al seguente. Si noti la porta su cui è in ascolto il server Web. Nell'esempio seguente è la porta 3000.

		=> Booting WEBrick
		=> Rails 3.2.12 application starting in development on http://0.0.0.0:3000
		=> Call with -d to detach
		=> Ctrl-C to shutdown server
		[2013-03-12 19:11:31] INFO  WEBrick 1.3.1
		[2013-03-12 19:11:31] INFO  ruby 1.9.3 (2012-04-20) [x86_64-linux]
		[2013-03-12 19:11:31] INFO  WEBrick::HTTPServer#start: pid=9789 port=3000

2. Nel browser passare al [portale di gestione di Azure][management-portal] e selezionare la macchina virtuale.

	![virtual machine list][vmlist]

3. Selezionare **ENDPOINT** nella parte superiore della pagina, quindi fare clic su **+ AGGIUNGI ENDPOINT** nella parte inferiore.

	![endpoints page][endpoints]

4. Nella finestra di dialogo **AGGIUNGI ENDPOINT** fare clic sulla freccia in basso a sinistra per passare alla seconda pagina, quindi immettere le seguenti informazioni nel form:

	* **NOME**: HTTP

	* **PROTOCOLLO**: TCP

	* **PORTA PUBBLICA**: 80

	* **PORTA PRIVATA**: &lt;informazioni sulla porta dal passaggio 3 precedente&gt;

	Verrà creata una porta pubblica 80 che instraderà il traffico alla porta privata 3000, dove resta in ascolto il server Rails.

	![new endpoint dialog][new-endpoint]

5. Fare clic sul segno di spunta per salvare l'endpoint.

6. Dovrebbe essere visualizzato il messaggio **AGGIORNAMENTO IN CORSO**. Quando questo messaggio scompare, significa che l'endpoint è attivo. A questo punto è possibile testare l'applicazione passando al nome DNS della macchina virtuale. L'aspetto del sito Web dovrebbe essere simile al seguente:

	![default rails page][default-rails-cloud]

	Aggiungendo **/posts** alla fine dell'URL dovrebbero essere visualizzate le pagine generate dal comando scaffolding.

	![posts page][blog-rails-cloud]

## <a id="next"></a>Passaggi successivi

In questo articolo è stato illustrato come creare e pubblicare una semplice applicazione Rails basata su form in una macchina virtuale di Azure. La maggior parte delle azioni eseguite sono manuali, mentre in un ambiente di produzione è consigliabile automatizzarle. Inoltre, la maggior parte degli ambienti di produzione ospita l'applicazione Rails insieme a un altro processo server, ad esempio Apache o NginX, che gestisce il routing delle richieste a più istanze dell'applicazione Rails e la distribuzione di risorse statiche.

Per informazioni sull'automazione della distribuzione dell'applicazione Rails, oltre che sull'utilizzo del server Web Unicorn e di NginX, vedere [Unicorn, NginX e Capistrano con una macchina virtuale di Azure][unicorn-nginx-capistrano].

Per altre informazioni su Ruby on Rails, vedere le [Guide di Ruby on Rails][rails-guides].

Per altre informazioni sull'uso di Azure SDK per Ruby per l'accesso ai servizi di Azure dall'applicazione Ruby, vedere:

* [Archiviare dati non strutturati mediante BLOB][blobs]

* [Archiviare coppie chiave-valore mediante tabelle][tables]

* [Distribuire contenuti ad ampia larghezza di banda con la rete per la distribuzione di contenuti][cdn-howto]



<!-- WA.com links -->
[blobs]: /it-it/documentation/articles/storage-ruby-how-to-use-blob-storage

[cdn-howto]: /it-it/develop/ruby/app-services/

[management-portal]: https://manage.windowsazure.com/

[tables]: /it-it/develop/ruby/how-to-guides/table-service/

[unicorn-nginx-capistrano]: /it-it/documentation/articles/virtual-machines-ruby-deploy-capistrano-host-nginx-unicorn/

[vm-instructions]: /it-it/documentation/articles/virtual-machines-linux-tutorial


<!-- External Links -->
[rails-guides]: http://guides.rubyonrails.org/

[sqlite3]: http://www.sqlite.org/

<!-- Images -->
[blog-rails]: ./media/virtual-machines-ruby-rails-web-app-linux/blograilslocal.png

[blog-rails-cloud]: ./media/virtual-machines-ruby-rails-web-app-linux/blograilscloud.png 

[default-rails]: ./media/virtual-machines-ruby-rails-web-app-linux/basicrailslocal.png

[default-rails-cloud]: ./media/virtual-machines-ruby-rails-web-app-linux/basicrailscloud.png

[vmlist]: ./media/virtual-machines-ruby-rails-web-app-linux/vmlist.png

[endpoints]: ./media/virtual-machines-ruby-rails-web-app-linux/endpoints.png

[new-endpoint]: ./media/virtual-machines-ruby-rails-web-app-linux/newendpoint.png


<!--HONumber=45--> 
