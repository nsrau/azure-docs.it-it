<properties 
	pageTitle="Distribuzione di un'applicazione Web Ruby on Rails in una macchina virtuale di Azure tramite Capistrano - Esercitazione" 
	description="Informazioni su come distribuire un'applicazione Web Ruby on Rails in una macchina virtuale di Azure tramite Capistrano, Unicorn e Nginx." 
	authors="blackmist" 
	manager="wpickett" 
	editor="" 
	services="virtual-machines" 
	documentationCenter=""/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="web" 
	ms.tgt_pltfrm="vm-linux" 
	ms.devlang="ruby" 
	ms.topic="article" 
	ms.date="09/17/2014" 
	ms.author="larryfr"/>


#Distribuzione di un'applicazione Web Ruby on Rails in una macchina virtuale di Azure mediante Capistrano

In questa esercitazione viene illustrato come distribuire un sito Web Ruby on Rails in una macchina virtuale di Azure usando [Capistrano 3](https://github.com/capistrano/capistrano/). Dopo la distribuzione, si useranno [Nginx](http://nginx.org/) e [Unicorn](https://github.com/blog/517-unicorn) per ospitare il sito Web. [PostgreSQL](https://www.postgresql.org) archivierà i dati dell'applicazione distribuita.

In questa esercitazione si presuppone che l'utente non abbia mai usato Azure, ma che familiarità con Ruby, Rails, Git e Linux. Al termine dell'esercitazione, si disporrà di un'applicazione Web basata su Ruby on Rails in esecuzione nel cloud.

Si apprenderà come:

* Impostare gli ambienti di sviluppo e di produzione

* Installare Ruby e Ruby on Rails

* Installare Unicorn, Nginx e PostgreSQL

* Creare una nuova applicazione Rails

* Creare una distribuzione Capistrano e usarla per distribuire l'applicazione

Di seguito è riportata una schermata dell'applicazione completata:

![a browser displaying Listing Posts][blog-rails-cloud]

> [AZURE.NOTE] L'applicazione utilizzata per questa esercitazione include componenti binari nativi. Se l'ambiente di sviluppo non è basato su Linux, durante la distribuzione della VM possono verificarsi errori. Il file Gemfile.lock usato durante la distribuzione conterrà gemme specifiche della piattaforma, che potrebbero non includere voci per le versioni Linux native delle gemme necessarie nella VM.
> 
> Per l'uso di un ambiente di sviluppo Windows sono necessari passaggi specifici. Se tuttavia durante o dopo la distribuzione si verificano errori non illustrati in questo articolo, è consigliabile eseguire di nuovo questi passaggi in ambiente di sviluppo basato su Linux.

##Contenuto dell'articolo

* [Configurare l'ambiente di sviluppo](#setup)

* [Creare un'applicazione Rails](#create)

* [Testare l'applicazione](#test)

* [Creare un repository di origine](#repository)

* [Creare una macchina virtuale di Azure](#createvm)

* [Test di Nginx](#nginx)

* [Preparare la distribuzione](#capify)

* [Distribuire](#deploy)

* [Passaggi successivi](#next)

##<a id="setup"></a>Configurare l'ambiente di sviluppo

1. Installare Ruby nell'ambiente di sviluppo. La procedura può variare in base al sistema operativo.

	* **Apple OS X** - Sono disponibili diverse distribuzioni Ruby per OS X. Questa esercitazione è stata convalidata con OS X usando [Homebrew](http://brew.sh/) per installare **rbenv**, **ruby-build** e **Ruby 2.0.0-p451**. Le informazioni sull'installazione sono reperibili all'indirizzo [https://github.com/sstephenson/rbenv/](https://github.com/sstephenson/rbenv/).

	* **Linux** - Usare il sistema di gestione di pacchetti delle distribuzioni. Questa esercitazione è stata convalidata in Ubuntu 12.10 usando **rbenv**, **ruby-build** e **Ruby 2.0.0-p451**.

	* **Windows** - Sono disponibili diverse distribuzioni Ruby per Windows. Questa esercitazione è stata convalidata usando [RubyInstaller](http://RubyInstaller.org/) per installare **Ruby 2.0.0-p451**. I comandi sono stati eseguiti usando la riga di comando **GitBash** disponibile con [Git per Windows](http://git-scm.com/download/win).

2. Aprire una nuova riga di comando o una sessione terminal e immettere il comando seguente per installare Ruby on Rails:

		gem install rails --no-rdoc --no-ri

	> [AZURE.NOTE] In alcuni sistemi operativi possono essere necessari privilegi di amministratore o radice. Se si riceve un errore durante l'esecuzione di questo comando, provare a usare  'sudo' come indicato di seguito.
	> 
	> `sudo gem install rails`

	> [AZURE.NOTE] Per l'esercitazione è stata usata la versione 4.0.4 della gemma Rails.

3. È necessario installare anche un interprete JavaScript, che verrà usato da Rails per compilare le risorse CoffeeScript usate dall'applicazione Rails. L'elenco di interpreti supportati è disponibile all'indirizzo [https://github.com/sstephenson/execjs#readme](https://github.com/sstephenson/execjs#readme).
	
	> [AZURE.NOTE] In questa esercitazione è stato usato [Node.js](http://nodejs.org/), in quanto è disponibile per i sistemi operativi OS X, Linux e Windows.

##<a id="create"></a>Creare un'applicazione Rails

1. Dalla riga di comando o dalla sessione terminal creare una nuova applicazione Rails denominata "blog_app" immettendo il seguente comando:

		rails new blog_app

	Verrà creata una nuova directory denominata **blog_app**, che verrà popolata con i file e le sottodirectory richiesti da un'applicazione Rails.

	> [AZURE.NOTE] Il completamento di questo comando può richiedere almeno un minuto. Esegue un'installazione invisibile all'utente delle gemme necessarie per un'applicazione predefinita e durante questo intervallo di tempo non risponderà.

2. Passare alla directory **blog_app** e quindi immettere il seguente comando per creare uno scaffolding per blog di base:

		rails generate scaffold Post name:string title:string content:text

	Verranno create le migrazioni di controller, viste, modelli e database usate per mantenere i post nel blog. A ogni post saranno associati un nome dell'autore, un titolo e un contenuto di testo.

3. Per creare il database in cui verranno archiviati i post del blog, immettere il comando seguente:

		rake db:migrate

	Verrà creato lo schema del database per l'archiviazione dei post mediante il provider di database predefinito per Rails, il database [SQLite3][sqlite3].

4. Per visualizzare un indice dei post come home page, modificare il file **config/routes.rb** e aggiungere il seguente codice dopo la riga `resources :posts`.

		root 'posts#index'

	In questo modo, quando gli utenti visitano il sito Web verrà visualizzato un elenco dei post.

##<a id="test"></a>Testare l'applicazione

1. Passare alla directory **blog_app**, se non è già visualizzata, quindi avviare il server Rails usando il seguente comando.

		rails s

	L'output dovrebbe essere simile al seguente. Si noti la porta su cui è in ascolto il server Web. Nell'esempio seguente è la porta 3000.

		=> Booting WEBrick
		=> Rails 4.0.4 application starting in development on http://0.0.0.0:3000
		=> Call with -d to detach
		=> Ctrl-C to shutdown server
		[2013-03-12 19:11:31] INFO  WEBrick 1.3.1
		[2013-03-12 19:11:31] INFO  ruby 2.0.0 (2014-02-24) [x86_64-linux]
		[2013-03-12 19:11:31] INFO  WEBrick::HTTPServer#start: pid=9789 port=3000

2. Aprire il browser e passare all'indirizzo http://localhost:3000/. Verrà visualizzata una pagina simile alla seguente.

	![a page listing posts][blog-rails]

	Per interrompere il processo server, immettere CTRL+C nella riga di comando

##<a id="repository"></a>Creare un repository di origine

Durante la distribuzione di un'applicazione con Capistrano, i file vengono recuperati tramite pull da un archivio. In questa esercitazione verranno usati [Git](http://git-scm.com/) per il controllo delle versioni e [GitHub](https://github.com/) per l'archivio.

1.	Creare un nuovo archivio in [GitHub](https://github.com/). Se non si dispone di un account GitHub, è possibile registrarsi per ottenere un account gratuito. In questi passaggi si presuppone che il nome dell'archivio sia **blog_app**.

	> [AZURE.NOTE] Per supportare le distribuzioni automatizzate dell'applicazione, usare chiavi SSH per l'autenticazione a GitHub. Per altre informazioni, vedere la documentazione GitHub relativa alla [generazione delle chiavi SSH](https://help.github.com/articles/generating-ssh-keys).

2.	Dal prompt dei comandi passare alla directory **blog_app** e avviare i seguenti comandi per caricare l'applicazione nell'archivio GitHub. Sostituire **YourGitHubName** con il nome dell'account GitHub.

		git init
		git add .
		git commit -m "initial commit on azure"
		git remote add origin git@github.com:YourGitHubName/blog-azure.git
		git push -u origin master

Nella sezione seguente si creerà la macchina virtuale in cui verrà distribuita l'applicazione.

##<a id="createvm"></a>Creare una macchina virtuale di Azure

Seguire le istruzioni fornite [qui][vm-instructions] per creare una macchina virtuale di Azure che ospita Linux.

1. Accedere al [portale di gestione di Azure][management-portal]. Sulla barra dei comandi, selezionare **Nuovo**.

2. Selezionare **Macchina virtuale** e quindi **Da raccolta**.

3. In **Scegli un'immagine**, selezionare **Ubuntu** e quindi selezionare la versione **12.04 LTS**. Selezionare la freccia per continuare.

	> [AZURE.NOTE] I passaggi di questa esercitazione sono stati eseguiti in una macchina virtuale Azure che ospita Ubuntu 12.04 LTS. Se si usa una distribuzione Linux diversa, è può essere necessario eseguire passaggi diversi per completare le stesse attività.

4. In **Nome macchina virtuale** digitare il nome da usare. Il nome verrà usato per creare il nome di dominio della macchina virtuale.

5. In **Nuovo nome utente** digitare il nome dell'account amministratore per la VM.

	> [AZURE.NOTE] In questa esercitazione l'account amministratore verrà usato anche per distribuire l'applicazione. Per informazioni sulla creazione di un account separato per la distribuzione, vedere la documentazione di [Capistrano][capistrano].

6. In **Autenticazione** selezionare **Carica chiave SSH compatibile per l'autenticazione**, quindi individuare e selezionare il file **.pem** che contiene il certificato. Infine, selezionare la freccia per continuare.

	> [AZURE.NOTE] Se non si ha familiarità con la generazione o l'uso di una chiave SSH, vedere [Come usare SSH con Linux in Azure per istruzioni sulla creazione di chiavi SSH][ssh-on-azure].
	> 
	> È anche possibile abilitare l'autenticazione della password, tuttavia è necessario fornire anche la chiave SSH, in quanto viene usata per automatizzare la distribuzione.

7. In **Endpoint** usare l'elenco a discesa **Immettere o scegliere valore** per selezionare **HTTP**. Gli altri campi in questa pagina possono essere lasciati sul valore predefinito. Prendere nota del **Nome DNS del servizio cloud**, in quanto il valore verrà usato nei passaggi successivi. Infine, selezionare la freccia per continuare.

8. Nella pagina finale selezionare il segno di spunta per creare la macchina virtuale.

###Installare Git, Ruby e Nginx

Dopo la creazione della macchina virtuale, stabilire una connessione remota a quest'ultima mediante SSH e usare i comandi seguenti per preparare l'ambiente host per l'applicazione Ruby.

	sudo apt-get update
	sudo apt-get -y upgrade
	sudo apt-get -y install git build-essential libssl-dev curl nodejs nginx
	git clone git://github.com/sstephenson/rbenv.git ~/.rbenv
	echo 'export RBENV_ROOT=~/.rbenv' >> ~/.bash_profile
	echo 'export PATH="$RBENV_ROOT/bin:$PATH"' >> ~/.bash_profile
	echo 'eval "$(rbenv init -)"' >> ~/.bash_profile
	source ~/.bash_profile
	eval "$(rbenv init -)"
	git clone git://github.com/sstephenson/ruby-build.git
	cd ruby-build
	sudo ./install.sh
	~/.rbenv/bin/rbenv install 2.0.0-p451
	~/.rbenv/bin/rbenv global 2.0.0-p451
	gem install bundler
	~/.rbenv/bin/rbenv rehash

> [AZURE.NOTE] Può essere utile salvare il codice precedente in uno script (file .sh) per evitare errori di battitura durante l'esecuzione dei comandi.


> [AZURE.NOTE] Il completamento del comando **~/.rbenv/bin/rbenv install 2.0.0-p451** può richiedere alcuni minuti.

Lo script **rbenv-install.sh** esegue le seguenti azioni:
	
* Aggiorna i pacchetti attualmente installati
* Installa gli strumenti di compilazione
* Installa Git
* Installa Node.js
* Installa Nginx
* Installa gli strumenti di compilazione e altre utilità richieste da Ruby e Rails
* Configura una distribuzione locale (utente) di [rbenv]
* Installa Ruby 2.0.0-p451
* Installa la gemma [Bundler]

Al termine, immettere il comando seguente per verificare se l'installazione di Ruby è stata completata correttamente:

	ruby -v

La versione restituita dovrebbe essere `ruby 2.0.0p451`.

###Installare PostgreSQL

Il database predefinito usato da Rails per lo sviluppo è SQLite. In genere in produzione si userà un database diverso. I passaggi seguenti consentono di installare PostgreSQL nella macchina virtuale e quindi di creare un utente e un database. I passaggi successivi serviranno a configurare l'applicazione Rails per l'uso di PostgreSQL durante la distribuzione.

1. Installare PostgreSQL e i bit di sviluppo usando il comando seguente.

		sudo apt-get -y install postgresql postgresql-contrib libpq-dev

6. Usare i comandi seguenti per creare un utente e un database per l'account di accesso. Sostituire **my_username** e **my_database** con il nome dell'account di accesso. Ad esempio, se l'account di accesso della VM è **deploy**, sostituire **my_username** e **my_database** con **deploy**.

		sudo -u postgres createuser -D -A -P my_username
		sudo -u postgres createdb -O my_username my_database

	> [AZURE.NOTE] Usare il nome utente anche come nome del database. Questa impostazione è necessaria per la gemma capistrano-postgresql usata dall'applicazione.

	Quando richiesto, immettere una password per l'utente. Quando viene richiesto se consentire all'utente di creare nuovi ruoli, selezionare **y** perché questo utente verrà usato durante la distribuzione per creare il database e l'account di accesso usati dall'applicazione Rails.

7. Per verificare che sia possibile connettersi al database mediante l'account utente, usare il comando seguente. Sostituire **my_username** e **my_database** con i valori usati in precedenza.

		psql -U my_username -W my_database

	Viene visualizzato un prompt `database=>`. Per uscire dall'utilità psql, immettere `\q` al prompt.

###<a id="nginx"></a>Test di Nginx

L'endpoint HTTP aggiunto durante la creazione della macchina virtuale consentirà di accettare richieste HTTP sulla porta 80. Per verificarlo, usare la procedura seguente per verificare se è possibile accedere al sito predefinito creato da Nginx.

2.	Avviare il servizio Nginx dalla sessione di SSH con la macchina virtuale:

		sudo service nginx start

	Verrà avviato il servizio Nginx, che rimane in attesa di traffico in ingresso sulla porta 80.

6. Testare l'applicazione passando al nome DNS della macchina virtuale. L'aspetto del sito Web dovrebbe essere simile al seguente:

	![nginx welcome page][nginx-welcome]

	> [AZURE.NOTE] Gli script di distribuzione eseguiti più avanti in questa esercitazione imposteranno blog_app come sito Web predefinito distribuito da Nginx.

A questo punto si dispone di una macchina virtuale di Azure con Ruby, Nginx e PostgreSQL, pronta per la distribuzione. Nella sezione seguente si modificherà l'applicazione Rails per aggiungere gli script e le informazioni per l'esecuzione della distribuzione.

##<a id="capify"></a>Preparare la distribuzione

Nell'ambiente di sviluppo modificare l'applicazione in modo che usi il server Web Unicorn e PostgreSQL, abilitare Capistrano per la distribuzione e creare gli script usati per la distribuzione e l'avvio dell'applicazione.

1. Nel computer di sviluppo modificare **Gemfile** e aggiungere le seguenti righe per aggiungere all'applicazione Rails le gemme per Unicorn, PostgreSQL e Capistrano e le gemme correlate.

		# Use Unicorn
		gem 'unicorn'
		# Use PostgreSQL
		gem 'pg', group: :production

		group :development do
		  # Use Capistrano for deployment
		  gem 'capistrano', '~> 3.1'
		  gem 'capistrano-rails', '~> 1.1.1'
		  gem 'capistrano-bundler'
		  gem 'capistrano-rbenv', '~> 2.0'
		  gem 'capistrano-unicorn-nginx', '~> 2.0'
		  gem 'capistrano-postgresql', '~> 3.0'
		end

	> [AZURE.NOTE] Unicorn non è disponibile in Windows. Se si usa Windows come ambiente di sviluppo, modificare __Gemfile__ per assicurarsi che il tentativo di installazione di Unicorn venga eseguito solo dopo la distribuzione nella macchina virtuale, usando il seguente codice durante la specifica della gemma Unicorn.
	> 
	> `platforms :ruby do`
	> `  gem 'unicorn'`
	> `end`

	Le gemme capistrano-* sono per la maggior parte helper che funzionano con specifici elementi usati nel server di produzione (rbenv,) o nel framework (rails).

	La gemma capistrano-unicorn-nginx crea automaticamente gli script usati con Unicorn e Nginx durante la distribuzione, dunque non è necessario crearli manualmente. Capistrano-postgresql genera automaticamente un database, un utente e una password in PostgreSQL per l'applicazione. Anche se non è necessario usarle, le gemme semplificano notevolmente il processo di distribuzione.
 
5.	Usare i comandi seguenti per installare le nuove gemme.
		
		bundle

6. Usare il comando seguente per creare i file di distribuzione predefiniti usati da Capistrano.

		cap install

	Dopo il completamento del comando `cap install`, all'applicazione saranno stati aggiunti i file e le directory seguenti.

		├── Capfile
		├── config
		│   ├── deploy
		│   │   ├── production.rb
		│   │   └── staging.rb
		│   └── deploy.rb
		└── lib
		    └── capistrano
		            └── tasks

	Il file **deploy.rb** fornisce configurazioni e azioni generiche per tutti i tipi di distribuzione, mentre **production.rb** e **staging.rb** contengono configurazioni aggiuntive per le distribuzioni di produzione e gestione temporanea.

	La cartella **capistrano** contiene attività e altri file usati nell'ambito del processo di distribuzione.

5. Modificare il file **Capfile** nella radice dell'applicazione e rimuovere simboli di commento dalle seguenti righe rimuovendo il carattere __#__ dall'inizio della riga.

		require 'capistrano/rbenv'
		require 'capistrano/bundler'
		require 'capistrano/rails/assets'
		require 'capistrano/rails/migrations'

	Dopo aver rimosso i simboli di commento dalle righe precedenti, aggiungere le righe seguenti.

		require 'capistrano/unicorn_nginx'
		require 'capistrano/postgresql'

	Queste righe indicano a Capistrano di usare le gemme precedentemente aggiunte al file Gemfile.

	Dopo aver apportato le modifiche, salvare il file.

6.  Modificare il file **config/deploy.rb** e sostituirne il contenuto con il seguente. Sostituire **YourApplicationName** con il nome dell'applicazione e **https://github.com/YourGitHubName/YourRepoName.git** con l'URL dell'archivio GitHub del progetto.

		lock '3.1.0'
		# application name and the github repository
		set :application, 'YourApplicationName'
		set :repo_url, 'git@github.com:YourGitHubName/YourRepoName.git'
		
		# describe the rbenv environment we are deploying into
		set :rbenv_type, :user
		set :rbenv_ruby, '2.0.0-p451'
		set :rbenv_prefix, "RBENV_ROOT=#{fetch(:rbenv_path)} RBENV_VERSION=#{fetch(:rbenv_ruby)} #{fetch(:rbenv_path)}/bin/rbenv exec"
		set :rbenv_map_bins, %w{rake gem bundle ruby rails}
		
		# dirs we want symlinked to the shared folder
		# during deployment
		set :linked_dirs, %w{bin log tmp/pids tmp/cache tmp/sockets vendor/bundle public/system}
		
		namespace :deploy do

		  desc 'Restart application'
		  task :restart do
		    on roles(:app), in: :sequence, wait: 5 do
		      # Your restart mechanism here, for example:
		      # execute :touch, release_path.join('tmp/restart.txt')
              #
			  # The capistrano-unicorn-nginx gem handles all this
			  # for this example
		    end
		  end
		
		  after :publishing, :restart
		
		  after :restart, :clear_cache do
		    on roles(:web), in: :groups, limit: 3, wait: 10 do
		      # Here we can do anything such as:
		      # within release_path do
		      #   execute :rake, 'cache:clear'
		      # end
		    end
		  end
		
		end

	Questo file fornisce informazioni e attività generiche comuni per tutti i tipi di distribuzioni.

5.  Modificare il file **config/deploy/production.rb** e sostituirne il contenuto con il seguente. Sostituire **YourVm.cloudapp.net** con il nome di dominio della VM. Sostituire **YourAzureVMUserName** con l'account di accesso creato in precedenza per la VM di Azure.

		# production deployment
		set :stage, :production
		# use the master branch of the repository
		set :branch, "master"
		# the user login on the remote server
		# used to connect and deploy
		set :deploy_user, "YourAzureVMUserName"
		# the 'full name' of the application
		set :full_app_name, "#{fetch(:application)}_#{fetch(:stage)}"
		# the server(s) to deploy to
		server 'YourVM.cloudapp.net', user: 'YourAzureVMUserName', roles: %w{web app db}, primary: true
		# the path to deploy to
		set :deploy_to, "/home/#{fetch(:deploy_user)}/apps/#{fetch(:full_app_name)}"
        # set to production for Rails
		set :rails_env, :production

	Questo file fornisce informazioni specifiche per le distribuzioni di produzione.

8.	Eseguire i comandi seguenti per eseguire il commit delle modifiche ai file apportate nei passaggi precedenti e quindi caricare le modifiche in GitHub.

		git add .
		git commit -m "adding config files"
		git push

A questo punto l'applicazione dovrebbe essere pronta per la distribuzione.

> [AZURE.NOTE] Per un'applicazione più complessa o per un database o un server applicazioni diverso possono essere necessari script di configurazione o distribuzione aggiuntivi.

##<a id="deploy"></a>Distribuire

2.	Nel computer di sviluppo locale usare il comando seguente per distribuire i file di configurazione usati dall'applicazione nella VM.

		bundle exec cap production setup

	Capistrano si connetterà alla VM usando SSH e quindi creerà la directory (~/apps) in cui distribuire l'applicazione. Se si tratta della prima distribuzione, la gemma capistrano-postgresql creerà anche un ruolo e un database in PostgreSQL nel server. Creerà inoltre un file di configurazione database.yml che verrà usato da Rails per la connessione al database.

	> [AZURE.NOTE] Se durante la distribuzione viene visualizzato un errore relativo alla **lettura della lunghezza della risposta da un socket di autenticazione**, può essere necessario avviare l'agente SSH nell'ambiente di sviluppo usando il comando `ssh-agent`. Ad esempio, aggiungere `eval $(ssh-agent)` al file ~/.bash\_profile.
	> 
	> Può anche essere necessario aggiungere la chiave SSH alla cache dell'agente usando il comando `ssh-add`.

4.	Eseguire una distribuzione di produzione usando il comando seguente. L'applicazione verrà distribuita nella macchina virtuale, il servizio Unicorn verrà avviato e Nginx verrà configurato in modo da instradare il traffico a Unicorn.

		bundle exec cap production deploy

	Questo comando consente di distribuire l'applicazione nella VM, installare le gemme necessarie e quindi avviare/riavviare Unicorn e Nginx.

	> [AZURE.NOTE] Durante l'elaborazione, il processo può essere sospeso per diversi minuti.

	> [AZURE.NOTE] Alcune parti della distribuzione potrebbero restituire lo 'stato di uscita 1 (operazione non riuscita)'. Questi errori in genere possono essere ignorati, purché la distribuzione venga completata correttamente.

	> [AZURE.NOTE] In alcuni sistemi può verificarsi una situazione in cui l'agente SSH non può inoltrare le credenziali alla VM remota durante l'autenticazione a GitHub. In questo caso, è possibile risolvere il problema modificando il file **config/deploy.rb** e modificando la riga  `set :repo_url` in modo da usare HTTPS per l'accesso a Github. Quando si usa HTTPS è necessario specificare il nome utente e la password (o il token di autenticazione) di GitHub all'interno dell'URL. Ad esempio:
	> 
	> `set :repo_url, 'https://you:yourpassword@github.com/You/yourrepository.git'
	> 
	> Anche se consente di risolvere l'errore e completare l'esercitazione, questa procedura non è consigliata per le distribuzioni di produzione, in quanto archivia le credenziali di autenticazione in testo normale come parte dell'applicazione. Per informazioni sull'uso dell'inoltro con l'agente SSH, consultare la documentazione del sistema operativo in uso.

A questo punto, l'applicazione Ruby on Rails dovrebbe essere in esecuzione nella macchina virtuale di Azure. Per verificarlo, immettere il nome DNS della macchina virtuale nel Web browser. Ad esempio, http://railsvm.cloudapp.net. Dovrebbe essere visualizzato l'indice dei post e dovrebbe essere possibile creare, modificare ed eliminare post.

##<a id="next"></a>Passaggi successivi

In questo articolo è stato illustrato come creare e pubblicare una semplice applicazione Rails in una macchina virtuale di Azure usando Capistrano. L'uso di un'applicazione di base come questa, tuttavia, offre solo un'idea delle possibilità offerte da Capistrano per la distribuzione. Per altre informazioni sull'uso di Capistrano, vedere:

* [Capistranorb.com](http://capistranorb.com) - Il sito Capistrano.
* [Azure, Ruby on Rails, Capistrano 3 e PostgreSQL](http://wootstudio.ca/articles/tutorial-windows-azure-ruby-on-rails-capistrano-3-postgresql) - Un approccio alternativo alla distribuzione in Azure mediante script di distribuzione personalizzati.
* [Esercitazione su Capistrano 3](http://www.talkingquickly.co.uk/2014/01/deploying-rails-apps-to-a-vps-with-capistrano-v3/) - Un'esercitazione sull'uso di Capistrano 3.

Per un esempio più semplice della creazione e distribuzione di un'applicazione Rails in una macchina virtuale di Azure solo tramite SSH, vedere [Ospitare un'app Web Ruby on Rails usando una macchina virtuale Linux][ruby-vm].

Per altre informazioni su Ruby on Rails, vedere le [Guide di Ruby on Rails][rails-guides].

Per altre informazioni sull'uso di Azure SDK per Ruby per l'accesso ai servizi di Azure dall'applicazione Ruby, vedere:

* [Archiviare dati non strutturati mediante BLOB][blobs]

* [Archiviare coppie chiave-valore mediante tabelle][tables]

* [Distribuire contenuti ad ampia larghezza di banda con la rete per la distribuzione di contenuti][cdn-howto]

[vm-instructions]: /it-it/manage/linux/tutorials/virtual-machine-from-gallery/


[rails-guides]: http://guides.rubyonrails.org/
[blobs]: /it-it/develop/ruby/how-to-guides/blob-storage/
[tables]: /it-it/develop/ruby/how-to-guides/table-service/
[cdn-howto]: /it-it/develop/ruby/app-services/
[ruby-vm]: /it-it/develop/ruby/tutorials/web-app-with-linux-vm/
 
[blog-rails]: ./media/virtual-machines-ruby-deploy-capistrano-host-nginx-unicorn/blograilslocal.png
[blog-rails-cloud]: ./media/virtual-machines-ruby-deploy-capistrano-host-nginx-unicorn/blograilscloud.png 
[default-rails]: ./media/virtual-machines-ruby-deploy-capistrano-host-nginx-unicorn/basicrailslocal.png
[default-rails-cloud]: ./media/virtual-machines-ruby-deploy-capistrano-host-nginx-unicorn/basicrailscloud.png
[vmlist]: ./media/virtual-machines-ruby-deploy-capistrano-host-nginx-unicorn/vmlist.png
[endpoints]: ./media/virtual-machines-ruby-deploy-capistrano-host-nginx-unicorn/endpoints.png
[new-endpoint]: ./media/virtual-machines-ruby-deploy-capistrano-host-nginx-unicorn/newendpoint80.png
[nginx-welcome]: ./media/virtual-machines-ruby-deploy-capistrano-host-nginx-unicorn/welcomenginx.png

[management-portal]: https://manage.windowsazure.com/
[sqlite3]: http://www.sqlite.org/
[ssh-on-azure]: http://azure.microsoft.com/documentation/articles/linux-use-ssh-key/
[capistrano]: http://capistranorb.com


<!--HONumber=42-->
