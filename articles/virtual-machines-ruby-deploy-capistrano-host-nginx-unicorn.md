<properties linkid="dev-ruby-web-app-with-linux-vm-capistrano" urlDisplayName="Ruby on Rails Azure VM Capistrano" pageTitle="Deploying a Ruby on Rails Web application to an Azure Virtual Machine using Capistrano - tutorial" metaKeywords="ruby on rails, ruby on rails azure, rails azure, rails vm, capistrano azure vm, capistrano azure rails, unicorn azure vm, unicorn azure rails, unicorn nginx capistrano, unicorn nginx capistrano azure, nginx azure" description="Learn how to deploy a Ruby on Rails application to an Azure Virtual Machine using Capistrano, Unicorn and Nginx." metaCanonical="" disqusComments="1" umbracoNaviHide="1" title="Deploy a Ruby on Rails Web application to an Azure VM using Capistrano" authors="" />

Distribuzione di un'applicazione Web Ruby on Rails in una macchina virtuale di Azure mediante Capistrano
========================================================================================================

In questa esercitazione viene illustrato come distribuire un sito Web basato su Ruby on Rails in una macchina virtuale di Azure utilizzando [Capistrano](https://github.com/capistrano/capistrano/). Viene inoltre illustrato come utilizzare [Nginx](http://nginx.org/) e [Unicorn](https://github.com/blog/517-unicorn) per l'hosting dell'applicazione nella macchina virtuale.

In questa esercitazione si presuppone che l'utente non abbia mai utilizzato Azure. Al termine dell'esercitazione, si disporrà di un'applicazione Web basata su Ruby on Rails in esecuzione nel cloud.

Si apprenderà come:

-   Configurare l'ambiente di sviluppo

-   Installare Ruby e Ruby on Rails

-   Installare e configurare Nginx e Unicorn

-   Creare una nuova applicazione Rails

-   Distribuire un'applicazione Rails in una macchina virtuale di Azure mediante Capistrano

Di seguito è riportata una schermata dell'applicazione completata:

![Browser che visualizza Listing Posts](./media/virtual-machines-ruby-deploy-capistrano-host-nginx-unicorn/blograilscloud.png)

**Nota**

L'applicazione utilizzata per questa esercitazione include componenti binari nativi. È pertanto possibile che si verifichino problemi se l'ambiente di sviluppo non è basato su Linux, poiché il file Gemfile.lock prodotto nella macchina di sviluppo potrebbe non includere voci per la versione compatibile con Linux di gemme necessarie.

Per l'utilizzo di un ambiente di sviluppo Windows sono necessari passaggi specifici, poiché ciò rappresenta la differenza più significativa rispetto all'ambiente di distribuzione di destinazione. Se tuttavia durante o dopo la distribuzione si verificano errori non inclusi nei passaggi disponibili in questo articolo, è consigliabile eseguire di nuovo queste procedure utilizzando un ambiente di sviluppo basato su Linux.

Contenuto dell'articolo
-----------------------

-   [Configurazione dell'ambiente di sviluppo](#setup)

-   [Creazione di un'applicazione Rails](#create)

-   [Test dell'applicazione](#test)

-   [Creazione di un archivio di origine](#repository)

-   [Creazione di una macchina virtuale di Azure](#createvm)

-   [Test di Nginx](#nginx)

-   [Preparazione per la distribuzione](#capify)

-   [Distribuzione](#deploy)

-   [Passaggi successivi](#next)

Configurazione dell'ambiente di sviluppo
----------------------------------------

1.  Installare Ruby nell'ambiente di sviluppo. La procedura può variare in base al sistema operativo.

    -   **Apple OS X**: sono disponibili diverse distribuzioni di Ruby per OS X. Questa esercitazione è stata convalidata con OS X utilizzando [Homebrew](http://brew.sh/) per installare **rbenv** e **ruby-build**. Le informazioni sull'installazione sono reperibili all'indirizzo <https://github.com/sstephenson/rbenv/>.

    -   **Linux**: utilizzare il sistema di gestione di pacchetti delle distribuzioni. Questa esercitazione è stata convalidata con Ubuntu 12.10 utilizzando i pacchetti ruby1.9.1 e ruby1.9.1-dev.

    -   **Windows**: sono disponibili diverse distribuzioni Ruby per Windows. Questa esercitazione è stata convalidata utilizzando [RailsInstaller](http://railsinstaller.org/) 1.9.3-p392.

2.  Aprire una nuova riga di comando o una sessione terminal e immettere il comando seguente per installare Ruby on Rails:

         gem install rails --no-rdoc --no-ri

    **Nota**

    Per questo comando, in alcuni sistemi operativi, possono essere necessari privilegi di amministratore o radice. Se durante l'esecuzione del comando viene visualizzato un messaggio di errore, provare a utilizzare 'sudo' come segue:

    ``` {.prettyprint}
    sudo gem install rails
    ```

    **Nota**

    Per l'esercitazione è stata utilizzata la versione 3.2.12 della gemma Rails.

3.  È necessario installare anche un interprete JavaScript, che verrà utilizzato da Rails per compilare le risorse CoffeeScript utilizzate dall'applicazione Rails. L'elenco di interpreti supportati è disponibile all'indirizzo <https://github.com/sstephenson/execjs#readme>.

    Durante la convalida dell'esercitazione è stato utilizzato [Node.js](http://nodejs.org/), in quanto è disponibile per i sistemi operativi OS X, Linux e Windows.

Creazione di un'applicazione Rails
----------------------------------

1.  Dalla riga di comando o dalla sessione terminal creare una nuova applicazione Rails denominata "blog\_app" immettendo il comando seguente:

         rails new blog_app

    Questo comando crea una nuova directory denominata **blog\_app** e la popola con i file e le sottodirectory richiesti da un'applicazione Rails.

    **Nota**

    Il completamento di questo comando può richiedere almeno un minuto. Esegue un'installazione invisibile all'utente delle gemme necessarie per un'applicazione predefinita e durante questo intervallo di tempo potrebbe sembrare bloccata.

2.  Passare alla directory **blog\_app** e quindi immettere il comando seguente per creare uno scaffolding per blog di base:

         rails generate scaffold Post name:string title:string content:text

    Verranno create le migrazioni di controller, viste, modelli e database utilizzate per mantenere i post nel blog. A ogni post saranno associati un nome dell'autore, un titolo e un contenuto di testo.

3.  Per creare il database in cui verranno archiviati i post del blog, immettere il comando seguente:

         rake db:migrate

    In questo modo per Rails verrà utilizzato il provider di database predefinito, ovvero il [database SQLite3](http://www.sqlite.org/). Anche se si può scegliere di utilizzare un database diverso per un'applicazione di produzione, SQLite è sufficiente per gli scopi di questa esercitazione.

Test dell'applicazione
----------------------

Per avviare il server Rails nell'ambiente di sviluppo, eseguire la procedura seguente

1.  Passare alla directory **blog\_app**, se non è già visualizzata, quindi avviare il server Rails utilizzando il comando seguente:

         rails s

    L'output dovrebbe essere simile al seguente. Si noti la porta su cui è in ascolto il server Web. Nell'esempio seguente è la porta 3000.

         => Booting WEBrick
         => Rails 3.2.12 application starting in development on http://0.0.0.0:3000
         => Call with -d to detach
         => Ctrl-C to shutdown server
         [2013-03-12 19:11:31] INFO  WEBrick 1.3.1
         [2013-03-12 19:11:31] INFO  ruby 1.9.3 (2012-04-20) [x86_64-linux]
         [2013-03-12 19:11:31] INFO  WEBrick::HTTPServer#start: pid=9789 port=3000

2.  Aprire il browser e passare all'indirizzo http://localhost:3000/. Verrà visualizzata una pagina simile alla seguente:

    ![Pagina predefinita Rails](./media/virtual-machines-ruby-deploy-capistrano-host-nginx-unicorn/basicrailslocal.png)

    Si tratta di una pagina iniziale statica. Per visualizzare i form generati dal comando di scaffolding, passare a http://localhost:3000/posts. Verrà visualizzata una pagina simile alla seguente:

    ![Pagina con elenco di post](./media/virtual-machines-ruby-deploy-capistrano-host-nginx-unicorn/blograilslocal.png)

    Per interrompere il processo server, immettere CTRL+C nella riga di comando

Creazione di un archivio di origine
-----------------------------------

In questa esercitazione verranno utilizzati [Git](http://git-scm.com/) e [GitHub](https://github.com/) per il controllo delle versioni e come posizione centrale per il codice.

1.  Creare un nuovo archivio in [GitHub](https://github.com/). Se non si dispone di un account GitHub, è possibile ottenere un account gratuito. Per la procedura inclusa in questa esercitazione si presuppone che il nome dell'archivio sia **blog\_app**.

    **Nota**

    Gli script creati in sezioni successive di questo documento includeranno l'indirizzo della macchina virtuale e il nome utente utilizzato per la distribuzione dell'applicazione su SSH. È pertanto consigliabile utilizzare un archivio GitHub privato, se possibile.

2.  Dal prompt dei comandi passare alla directory **blog\_app** e quindi eseguire i comandi seguenti per caricare la versione iniziale dell'applicazione nell'archivio GitHub:

        git init
        git add .
        git commit -m "initial commit on azure"
        git remote add origin https://github.com/YOUR_GITHUB_ACCOUNT/blog-azure.git
        git push -u origin master

Creazione di una macchina virtuale di Azure
-------------------------------------------

Attenersi alle istruzioni riportate [qui](/en-us/manage/linux/tutorials/virtual-machine-from-gallery/) per creare una macchina virtuale di Azure che ospita Linux.

**Nota**

I passaggi di questa esercitazione sono stati eseguiti in una macchina virtuale Azure che ospita Ubuntu 12.10. Se si utilizza una distribuzione Linux diversa, è può essere necessario eseguire passaggi diversi per completare le stesse attività.

**Nota**

È necessario creare **solo** la macchina virtuale. Interrompere la procedura dopo aver appreso come connettersi alla macchina virtuale tramite SSH.

Dopo aver creato la macchina virtuale di Azure tramite SSH, eseguire i passaggi seguenti per installarvi Ruby and Rails:

1.  Effettuare la connessione alla macchina virtuale mediante SSH, quindi utilizzare i comandi seguenti per aggiornare i pacchetti esistenti e installare l'ambiente Ruby:

         sudo apt-get -y update
         sudo apt-get -y upgrade
         sudo apt-get -y install ruby1.9.1 ruby1.9.l-dev build-essential libsqlite3-dev nodejs curl git-core nginx

    Al termine, immettere il comando seguente per verificare se l'installazione di Ruby è stata completata correttamente:

         ruby -v

    Questo comando dovrebbe restituire la versione di Ruby installata nella macchina virtuale, che può essere diversa da 1.9.1, ad esempio **ruby 1.9.3p194 (2012-04-20 revision 35410) [x86\_64-linux]**.

2.  Immettere il comando seguente per installare Bundler:

         sudo gem install bundler

    Bundler verrà utilizzato per installare le gemme necessarie per l'applicazione Rails dopo la copia nel server.

Apertura della porta 80 e test di Nginx
---------------------------------------

Nginx offre un sito Web predefinito, che può essere utilizzato per verificare che la macchina virtuale accetti traffico Web. Per abilitare il traffico sulla porta 80 e testare il sito Web predefinito di Nginx, eseguire la procedura seguente.

1.  Avviare il servizio Nginx dalla sessione di SSH con la macchina virtuale:

        sudo service nginx start

    Verrà avviato il servizio Nginx, che rimane in attesa di traffico in ingresso sulla porta 80.

2.  Nel browser passare al [portale di gestione di Azure](https://manage.windowsazure.com/) e selezionare la macchina virtuale.

    ![Elenco di macchine virtuali](./media/virtual-machines-ruby-deploy-capistrano-host-nginx-unicorn/vmlist.png)

3.  Selezionare **ENDPOINTS** nella parte superiore della pagina, quindi fare clic su **+ ADD ENDPOINT** nella parte inferiore.

    ![Pagina di endpoint](./media/virtual-machines-ruby-deploy-capistrano-host-nginx-unicorn/endpoints.png)

4.  Nella finestra di dialogo **ADD ENDPOINT** fare clic sulla freccia in basso a sinistra per passare alla seconda pagina, quindi immettere le informazioni seguenti nel form:

    -   **NAME**: HTTP

    -   **PROTOCOL**: TCP

    -   **PUBLIC PORT**: 80

    -   **PRIVATE PORT**: 80

    Verrà creata una porta pubblica 80 che instraderà il traffico alla porta privata 80, dove resta in ascolto Nginx.

    ![Finestra di dialogo New endpoint](./media/virtual-machines-ruby-deploy-capistrano-host-nginx-unicorn/newendpoint80.png)

5.  Fare clic sul segno di spunta per salvare l'endpoint.

6.  Dovrebbe essere visualizzato il messaggio **UPDATE IN PROGRESS**. Quando questo messaggio scompare, significa che l'endpoint è attivo. A questo punto è possibile testare l'applicazione passando al nome DNS della macchina virtuale. L'aspetto del sito Web dovrebbe essere simile al seguente:

    ![Pagina di benvenuto di Nginx](./media/virtual-machines-ruby-deploy-capistrano-host-nginx-unicorn/welcomenginx.png)

7.  Arrestare e rimuovere il sito Web predefinito di Nginx utilizzando i comandi seguenti:

        sudo service nginx stop
        sudo rm /etc/nginx/sites-enabled/default

    Gli script di distribuzione eseguiti più avanti in questa esercitazione imposteranno blog\_app come sito Web predefinito distribuito da Nginx.

Preparazione per la distribuzione
---------------------------------

In questa sezione verranno eseguite le procedure per modificare l'applicazione in modo che utilizzi il server Web Unicorn, abilitare Capistrano per la distribuzione, abilitare l'accesso GitHub dalla macchina virtuale e creare gli script utilizzati per la distribuzione e l'avvio dell'applicazione.

1.  Autorizzare la macchina virtuale per l'autenticazione all'account GitHub tramite un certificato eseguendo i passaggi illustrati nella pagina [Generazione delle chiavi SSH](https://help.github.com/articles/generating-ssh-keys#platform-all). Tali chiavi verranno utilizzate per accedere all'archivio GitHub dagli script di distribuzione.

    **Nota**

    Benché sia necessario generare le chiavi SSH sulla macchina virtuale, è possibile aggiungere la chiave all'account GitHub utilizzando il browser nell'ambiente di sviluppo.

    Per visualizzare i contenuti del certificato SSH, in modo da poterli copiare e incollare in GitHub, utilizzare il comando seguente:

    ``` {}
    cat ~/.ssh/id_rsa.pub
    ```

2.  Nella macchina di sviluppo modificare **Gemfile** e rimuovere simboli di commento dalle righe relative a **Capistrano** e **Unicorn**, rimuovendo il carattere '\#' dall'inizio delle righe seguenti:

         # gem 'unicorn'
         # gem 'capistrano'

    **Nota**

    Unicorn non è disponibile in Windows. Se si utilizza Windows come ambiente di sviluppo, modificare **Gemfile** per assicurarsi che il tentativo di installazione di Unicorn venga eseguito solo dopo la distribuzione nella macchina virtuale:

    ``` {.prettyprint}
    platforms :ruby do  gem 'unicorn'end
    ```

3.  Eseguire i comandi seguenti per installare nuove gemme e configurare Capistrano per il progetto:

        bundle
        capify .

4.  Aggiungere i file seguenti alla directory **blog\_app/config** e popolare ogni file con i contenuti disponibili nei collegamenti seguenti:

    -   [nginx.conf](https://gist.github.com/cff582f4f970a95991e9): consente di configurare Nginx per il funzionamento con Unicorn e in modo da distribuire i file statici inclusi nell'applicazione Rails.
    -   [unicorn\_init.sh](https://gist.github.com/3272994): script della shell utilizzato per avviare il processo del server Unicorn.
    -   [unicorn.rb](https://gist.github.com/3273014): file di configurazione per Unicorn.

    Sostituire in ogni file la parola **blogger** con il nome utente utilizzato per l'accesso alla macchina virtuale. Tale utente sarà utilizzato per la distribuzione dell'applicazione.

5.  Nella directory **blog\_app/config** modificare il file **deploy.rb** e sostituire i contenuti esistenti con il codice seguente:

        require "bundler/capistrano"

        set :application, "blog_app"
        set :user, "blogger"

        set :scm, :git
        set :repository, "git@github.com:YourGitHubAccount/blog_app.git"
        set :branch, "master"
        set :use_sudo, true

        server "VMDNSName", :web, :app, :db, primary: true

        set :deploy_to, "/home/#{user}/apps/#{application}"
        default_run_options[:pty] = true
        ssh_options[:forward_agent] = true
        ssh_options[:port] = SSHPort

        namespace :deploy do
          desc "Fix permissions"
          task :fix_permissions, :roles => [ :app, :db, :web ] do
            run "chmod +x #{release_path}/config/unicorn_init.sh"
          end

          %w[start stop restart].each do |command|
            desc "#{command} unicorn server"
            task command, roles: :app, except: {no_release: true} do
              run "service unicorn_#{application} #{command}"
            end
          end

          task :setup_config, roles: :app do
            sudo "ln -nfs #{current_path}/config/nginx.conf /etc/nginx/sites-enabled/#{application}"
            sudo "ln -nfs #{current_path}/config/unicorn_init.sh /etc/init.d/unicorn_#{application}"
            sudo "mkdir -p #{shared_path}/config"
          end
          after "deploy:setup", "deploy:setup_config"

          task :symlink_config, roles: :app do
            # Add database config here
          end
          after "deploy:finalize_update", "deploy:fix_permissions"
          after "deploy:finalize_update", "deploy:symlink_config"
        end

    Nel testo precedente sostituire gli elementi seguenti:

    -   **YourGitHubAccount** con il nome dell'account GitHub
    -   **VMDNSName** con il nome DNS della macchina virtuale di Azure
    -   **blogger** con il nome utente utilizzato per l'accesso alla macchina virtuale
    -   **SSHPort** con la porta SSH esterna per la macchina virtuale

    **Nota**

    Se l'ambiente di sviluppo è un sistema Windows, sarà necessario aggiungere la riga seguente al file **deploy.rb**. È necessario posizionare tale riga insieme alle altre istruzioni **set** all'inizio del file:

    ``` {}
    set :bundle_flags, "--no-deployment --quiet"
    ```

    Questa non è una procedura consigliata, poiché provoca il caricamento delle gemme da Gemfile durante la distribuzione invece di Gemfile.lock, ma è una procedura necessaria perché il sistema di destinazione (Linux) è diverso dal sistema di sviluppo (Windows).

6.  Rimuovere simboli di commento dalle righe di asset nel file **Capfile** disponibile nella directory **blog\_app**.

        load 'deploy/assets'

7.  Eseguire i comandi seguenti per eseguire il commit delle modifiche al progetto e caricarle in GitHub.

        git add .
        git commit -m "adding config files"
        git push

Distribuzione
-------------

1.  Nella macchina di sviluppo locale utilizzare il comando seguente per configurare la macchina virtuale di Azure per la distribuzione.

        cap deploy:setup

    Quando richiesto, immettere la password per l'account utente della macchina virtuale. Capistrano effettuerà la connessione alla macchina virtuale e creerà una directory **apps** nella home directory dell'account utente.

2.  Da una connessione SSH alla macchina virtuale di Azure modificare le autorizzazioni della directory **app**, utilizzando il comando seguente:

        sudo chmod -R 777 apps

    **Nota**

    Questa procedura non è consigliata per gli ambienti di produzione e viene utilizzata ora solo a scopo dimostrativo.

3.  Eseguire una distribuzione a freddo, utilizzando il comando seguente nell'ambiente di sviluppo. L'applicazione verrà distribuita nella macchina virtuale e il servizio Unicorn verrà avviato.

        cap deploy:cold

4.  Avviare il servizio Nginx, che dovrebbe iniziare a indirizzare il traffico a Unicorn e a distribuire contenuto statico:

        sudo service nginx start

A questo punto, l'applicazione Ruby on Rails dovrebbe essere in esecuzione nella macchina virtuale di Azure. Per verificarlo, immettere il nome DNS della macchina virtuale nel Web browser. Ad esempio, http://railsvm.cloudapp.net. Dovrebbe essere visualizzata la schermata 'Welcome aboard':

![Pagina "Welcome aboard"](./media/virtual-machines-ruby-deploy-capistrano-host-nginx-unicorn/basicrailscloud.png)

Se si aggiunge '/posts' all'URL, dovrebbe essere visualizzato l'indice dei post e dovrebbe essere possibile creare, modificare ed eliminare post.

Passaggi successivi
-------------------

In questo articolo è stato illustrato come creare e pubblicare una semplice applicazione Rails basata su form in una macchina virtuale di Azure utilizzando Capistrano. La macchina virtuale ha utilizzato Unicorn e Nginx per la gestione delle richieste Web per l'applicazione.

Per un esempio più semplice della creazione e distribuzione di un'applicazione Rails in una macchina virtuale di Azure solo tramite SSH, vedere [Ospitare un'app Web Ruby on Rails utilizzando una macchina virtuale Linux](/en-us/develop/ruby/tutorials/web-app-with-linux-vm/).

Per ulteriori informazioni su Ruby on Rails, vedere le [Guide di Ruby on Rails](http://guides.rubyonrails.org/).

Per ulteriori informazioni sull'utilizzo di Azure SDK for Ruby per l'accesso ai servizi di Azure dall'applicazione Ruby, vedere:

-   [Archiviazione di dati non strutturati mediante BLOB](/en-us/develop/ruby/how-to-guides/blob-storage/)

-   [Archiviazione di coppie chiave-valore mediante tabelle](/en-us/develop/ruby/how-to-guides/table-service/)

-   [Distribuzione di contenuti ad ampia larghezza di banda con la rete per la distribuzione di contenuti](/en-us/develop/ruby/app-services/)


