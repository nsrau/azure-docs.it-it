<properties linkid="manage-services-identity-multi-factor-authentication" urlDisplayName="What is Azure Multi-Factor Authentication?" pageTitle="What is Azure Multi-Factor Authentication?" metaKeywords="" description="Learn more about Azure Multi-Factor Authentication, a method of authentication that requires the use of more than one verification method and adds a critical second layer of security to user sign-ins and transactions." metaCanonical="" services="active-directory,multi-factor-authentication" documentationCenter="" title="How to Manage Azure Virtual Machines using Ruby" authors="larryfr" solutions="" manager="" editor="" />

Come gestire le macchine virtuali di Azure con Ruby
===================================================

In questa guida verrà descritto come eseguire attività di gestione comuni a livello di codice per le macchine virtuali di Azure, ad esempio la creazione e la configurazione di VM e l'aggiunta di dischi dati. Azure SDK for Ruby fornisce l'accesso alla funzionalità di gestione servizi per una serie di Servizi di Azure, incluse le macchine virtuali di Azure.

Sommario
--------

-   [Informazioni sulla gestione dei servizi](#what-is)
-   [Concetti](#concepts)
-   [Creazione di un certificato di gestione](#setup-certificate)
-   [Creazione di un'applicazione Ruby](#create-app)
-   [Configurazione dell'applicazione per l'utilizzo dell'SDK](#configure-access)
-   [Configurazione di una connessione di gestione di Azure](#setup-connection)
-   [Procedura: Lavorare con Macchine virtuali](#virtual-machine)
-   [Procedura: Lavorare con immagini e dischi](#vm-images)
-   [Procedura: Lavorare con i servizi cloud](#cloud-services)
-   [Procedura: Lavorare con i servizi di archiviazione](#storage-services)
-   [Passaggi successivi](#next-steps)

Informazioni sulla gestione dei servizi
---------------------------------------

Azure fornisce le [API REST per le operazioni di gestione dei servizi](http://msdn.microsoft.com/it-it/library/windowsazure/ee460799.aspx), inclusa la gestione di Macchine virtuali di Azure. Azure SDK for Ruby espone le operazioni di gestione per Macchine virtuali attraverso la classe **Azure::VirtualMachineSerivce**. L'utilizzo di questa classe consente di accedere a gran parte delle funzionalità di gestione della macchina virtuale disponibili sul [portale di gestione di Azure](https://manage.windowsazure.com).

Benché sia possibile utilizzare le API di gestione servizi per gestire una serie di servizi ospitati su Azure, in questo documento sono disponibili informazioni solo sulla gestione di Macchine virtuali di Azure.

Concetti
--------

Le macchine virtuali di Azure vengono implementate come "ruoli" nell'ambito di un servizio cloud. Ciascun servizio cloud può contenere uno o più ruoli, che sono raggruppati logicamente in distribuzioni. Il ruolo definisce le caratteristiche fisiche generali della VM, ad esempio la quantità di memoria disponibile, il numero di core CPU ecc.

Ogni VM è inoltre dotata di un disco del sistema operativo, che contiene il sistema operativo di avvio. Una VM può essere dotata di uno o più dischi, cioè dischi aggiuntivi da utilizzare prevalentemente per archiviare i dati dell'applicazione. I dischi vengono implementati come dischi rigidi virtuali (VHD) memorizzati nell'Archiviazione BLOB di Azure. È inoltre possibile esportare i VHD come "immagini", ossia modelli che sono utilizzati per creare dischi adoperati da una VM durante la sua creazione. Ad esempio, la creazione di una nuova VM utilizza un'immagine Ubuntu che consentirà di creare un nuovo disco del sistema operativo.

La maggior parte delle immagini è fornita da Microsoft o dai suoi partner, tuttavia è possibile creare immagini personali oppure un'immagine da una VM ospitata su Azure.

Creazione di un certificato di gestione Azure
---------------------------------------------

Quando si eseguono operazioni di gestione dei servizi, ad esempio quelle esposte tramite la classe **Azure::VirtualMachineService**, è necessario indicare l'ID sottoscrizione di Azure e un file contenente un certificato di gestione per la sottoscrizione. Entrambi verranno utilizzati dall'SDK durante l'autenticazione dell'API REST di Azure.

È possibile ottenere l'ID sottoscrizione e un certificato di gestione utilizzando l'interfaccia della riga di comando multipiattaforma di Azure (xplat-cli). Per informazioni sull'installazione e la configurazione dell'interfaccia xplat-cli vedere [Installazione e configurazione dell'interfaccia della riga di comando multipiattaforma di Azure](http://www.windowsazure.com/it-it/manage/install-and-configure-cli/).

Dopo aver configurato l'interfaccia xplat-cli sarà possibile eseguire la procedura seguente per recuperare l'ID sottoscrizione di Azure ed esportare un certificato di gestione:

1.  Per recuperare l'ID sottoscrizione, utilizzare il comando seguente:

         azure account list

2.  Per esportare il certificato di gestione, utilizzare il comando seguente:

         azure account cert export

    Al termine dell'esecuzione del comando, il certificato verrà esportato in un file denominato &lt;azure-subscription-name\>.pem. Ad esempio, se la sottoscrizione è denominata **sottoscrizionepersonale**, il file creato verrà denominato **sottoscrizionepersonale.pem**.

Prendere nota dell'ID sottoscrizione e del percorso del file PEM contenente il certificato esportato, in quanto verranno utilizzati più avanti in questo documento.

Creazione di un'applicazione Ruby
---------------------------------

Creare una nuova applicazione Ruby. È possibile implementare gli esempi utilizzati in questo documento in un unico file **.rb**.

Configurazione dell'applicazione
--------------------------------

Per gestire i Servizi di Azure è necessario scaricare e utilizzare la gemma di Azure, che contiene Azure SDK for Ruby.

### Utilizzare il comando della gemma per installare il pacchetto

1.  Utilizzare un'interfaccia della riga di comando come **PowerShell** (Windows), **Terminal** (Mac) o **Bash** (UNIX) e spostarsi nella cartella in cui è stata creata l'applicazione di esempio.

2.  Per installare la gemma di Azure utilizzare il comando seguente:

         gem install azure

    L'output dovrebbe essere simile al seguente:

         Fetching: mini_portile-0.5.1.gem (100%)
         Fetching: nokogiri-1.6.0-x86-mingw32.gem (100%)
         Fetching: mime-types-1.25.gem (100%)
         Fetching: systemu-2.5.2.gem (100%)
         Fetching: macaddr-1.6.1.gem (100%)
         Fetching: uuid-2.3.7.gem (100%)
         Fetching: azure-0.5.0.gem (100%)
         Successfully installed mini_portile-0.5.1
         Successfully installed nokogiri-1.6.0-x86-mingw32
         Successfully installed mime-types-1.25
         Successfully installed systemu-2.5.2
         Successfully installed macaddr-1.6.1
         Successfully installed uuid-2.3.7
         Successfully installed azure-0.5.0
         7 gems installed

    **Nota**

    Se viene visualizzato un errore relativo alle autorizzazioni, utilizzare invece `sudo gem install azure`.

### Richiedere la gemma

Utilizzando un editor di testo, aggiungere quanto segue nella parte superiore del file dell'applicazione Ruby: Verrà caricata la gemma di Azure, che renderà Azure SDK for Ruby disponibile nell'applicazione:

    require 'azure'

Procedura: Connettersi alla gestione dei servizi
------------------------------------------------

Per eseguire correttamente le operazioni di gestione dei servizi con Azure è necessario specificare l'ID sottoscrizione e il certificato ottenuti nella sezione [Creazione di un certificato di gestione Azure](#setup-certificate). Il modo più semplice consiste nello specificare l'ID e il percorso del file del certificato utilizzando le variabili di ambiente seguenti:

-   AZURE\_MANAGEMENT\_CERTIFICATE: il percorso del file PEM contenente il certificato di gestione.

-   AZURE\_SUBSCRIPTION\_ID: l'ID sottoscrizione della sottoscrizione di Azure.

È inoltre possibile impostare questi valori a livello di codice nell'applicazione utilizzando il comando seguente:

    Azure.configure do |config|
      config.management_certificate = 'path/to/certificate'
      config.subscription_id = 'subscription ID'
    end

Procedura: Lavorare con Macchine virtuali
-----------------------------------------

Le operazioni di gestione per Macchine virtuali di Azure vengono eseguite utilizzando la classe **Azure::VirtualMachineService**.

### Procedura: Creare una nuova macchina virtuale

Per creare una nuova macchina virtuale utilizzare il metodo **create\_virtual\_machine**. Questo metodo accetta un hash contenente i parametri seguenti e restituisce un'istanza di **Azure::VirtualMachineManagement::VirtualMachine** che descrive la VM che è stata creata:

**Parametri**

-   **:vm\_name**: nome della macchina virtuale

-   **:vm\_user**: nome utente dell'utente ROOT o dell'amministratore

-   **:password**: password da utilizzare per l'utente ROOT o l'amministratore

-   **:image**: immagine del sistema operativo che verrà utilizzata per creare il disco per questa VM Il disco del sistema operativo verrà archiviato in un VHD creato nell'archiviazione BLOB

-   **:location**: l'area in cui verrà creata la VM. Dovrà trattarsi della stessa area in cui si trova l'account di archiviazione che contiene i dischi utilizzati da questa VM.

Di seguito è riportato un esempio di codice per la creazione di una nuova macchina virtuale utilizzando questi parametri:

    vm_params = {
      :vm_name => 'mygreatvm',
      :vm_user => 'myuser',
      :password => 'mypassword',
      :image => 'b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-13_04-amd64-server-20130824-en-us-30GB',
      :location = 'East US'
    }
      
    vm_mgr = Azure::VirtualMachineService.new
    vm = vm_mgr.create_virtual_machine(vm_params)

    puts "A VM named #{vm.vm_name} was created in a cloud service named #{vm.cloud_service_name}."
    puts "It uses a disk named #{vm.disk_name}, which was created from a #{vm.os_type}-based image."
    puts "The virtual IP address of the machine is #{vm.ipaddress}."
    puts "The fully qualified domain name is #{vm.cloud_service_name}.cloudapp.net."

**Opzioni**

È possibile fornire un hash di parametri facoltativi che consentono di ignorare il comportamento predefinito della VM creata, ad esempio specificando un account di archiviazione esistente invece di crearne uno nuovo.

Di seguito sono riportate le opzioni disponibili quando si utilizza il metodo **create\_virtual\_machine**:

-   **:storage account name**: il nome dell'account di archiviazione da utilizzare per l'archiviazione delle immagini del disco. Se non esiste un account di archiviazione, ne verrà creato uno nuovo. Se omesso, verrà creato un nuovo account di archiviazione con un nome basato sul parametro :vm\_name.

-   **:cloud\_service\_name**: il nome del servizio cloud da utilizzare per l'hosting della macchina virtuale. Se non esiste un servizio cloud, ne verrà creato uno nuovo. Se omesso, verrà creato un nuovo servizio cloud con un nome basato sul parametro :vm\_name.

-   **:deployment\_name**: il nome della distribuzione da utilizzare per la distribuzione della configurazione della macchina virtuale

-   **:tcp\_endpoints** - : le porte TCP da esporre pubblicamente per questa VM. Non è necessario specificare l'endpoint SSH (per VM basate su Linux) e l'endpoint WinRM (per VM basate su Windows), che verranno creati automaticamente. È possibile specificare più porte, separate da una virgola. Per associare una porta interna a una porta pubblica utilizzando un nome di porta diverso, utilizzare il formato **porta pubblica:porta interna**. Ad esempio, 80:8080 espone la porta interna 8080 come porta pubblica 80.

-   **:service\_location**: il percorso dell'archivio del certificato di destinazione sulla VM. Si applica solo alle VM basate su Windows.

-   **:ssh\_private\_key\_file**: il file contenente la chiave privata, che verrà utilizzato per proteggere l'accesso SSH alle per VM basate su Linux. Viene altresì utilizzato per specificare il certificato di protezione di WinRM se si seleziona il trasporto HTTPS. Se si omettono i valori **:ssh\_private\_key\_file** e **:ssh\_certificate\_file**, per SSH verrà utilizzata unicamente l'autenticazione della password

-   **:ssh\_certificate\_file**: il file contenente il certificato, che verrà utilizzato per proteggere l'accesso SSH alle per VM basate su Linux. Viene altresì utilizzato per specificare il certificato di protezione di WinRM se si seleziona il trasporto HTTPS. Se si omettono i valori **:ssh\_private\_key\_file** e **:ssh\_certificate\_file**, per SSH verrà utilizzata unicamente l'autenticazione della password

-   **:ssh\_port**: la porta pubblica che verrà utilizzata per la comunicazione SSH. Se omessa, la porta SSH predefinita è la 22.

-   **:vm\_size**: le dimensioni della VM. Questo valore determina le dimensioni della memoria, il numero di core, la larghezza di banda e altre caratteristiche fisiche della VM. Per informazioni dettagliate sulle dimensioni e le caratteristiche fisiche disponibili per le macchine virtuali, vedere l'articolo relativo alle [dimensioni della macchina virtuale e dei Servizi cloud per Azure](http://msdn.microsoft.com/it-it/library/windowsazure/dn197896.aspx).

-   **:winrm\_transport**: una matrice dei trasporti disponibili per l'uso con WinRM. I trasporti validi sono 'http' e 'https'. Se si specifica 'https' come trasporto, sarà inoltre necessario utilizzare **:ssh\_private\_key\_file** e **:ssh\_certificate\_file** al fine di specificare il certificato utilizzato per proteggere le comunicazioni HTTPS.

Di seguito è riportato un esempio di creazione di una macchina virtuale che utilizza una piccola istanza di calcolo, espone pubblicamente le porte per il traffico HTTP (porta locale 8080, porta pubblica 80) e HTTPS (443) e consente di eseguire l'autenticazione del certificato per le sessioni SSH utilizzando i file di certificato specificati:

    vm_params = {
      :vm_name => 'myvm',
      :vm_user => 'myuser',
      :password => 'mypassword',
      :image => 'b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-13_04-amd64-server-20130824-en-us-30GB',
      :location = 'East US'
    }
      
    vm_opts = {
      :tcp_endpoints => '80:8080,443',
      :vm_size => 'Small',
      :ssh_private_key_file => '../sshkey/mykey.key',
      :ssh_certificate_file => '../sshkey/mykey.pem'
    }
      
    vm_mgr = Azure::VirtualMachineService.new
    vm = vm_mgr.create_virtual_machine(vm_params, vm_opts)

### Procedura: Elencare le macchine virtuali

Per elencare le macchine virtuali per la sottoscrizione di Azure utilizzare il metodo **list\_virtual\_machines**. Questo metodo restituisce una matrice di oggetti **Azure::VirtualMachineManagement::VirtualMachine**:

    vm_mgr = Azure::VirtualMachineService.new
    virtual_machines = vm_mgr.list_virtual_machines

### Procedura: Ottenere informazioni su una macchina virtuale

Per ottenere un'istanza di **Azure::VirtualMachineManagement::VirtualMachine** per una specifica macchina virtuale utilizzare il metodo **get\_virtual\_machine** e indicare i nomi della macchina virtuale e del servizio cloud:

    vm_mgr = Azure::VirtualMachineService.new
    vm = vm_mgr.get_virtual_machine('myvm', 'mycloudservice')

### Procedura: Eliminare una macchina virtuale

Per eliminare una macchina virtuale utilizzare il metodo **delete\_virtual\_machine** e indicare i nomi della macchina virtuale e del servizio cloud:

    vm_mgr = Azure::VirtualMachineService.new
    vm = vm_mgr.delete_virtual_machine('myvm', 'mycloudservice')

**Avviso**

Il metodo **delete\_virtual\_machine** consente di eliminare il servizio cloud ed eventuali dischi associato alla macchina virtuale.

### Procedura: Arrestare una macchina virtuale

Per arrestare una macchina virtuale utilizzare il metodo **shutdow\_virtual\_machine** e indicare i nomi della macchina virtuale e del servizio cloud:

    vm_mgr = Azure::VirtualMachineService.new
    vm = vm_mgr.shutdown_virtual_machine('myvm', 'mycloudservice')

### Procedura: Avviare una macchina virtuale

Per avviare una macchina virtuale utilizzare il metodo **start\_virtual\_machine** e indicare i nomi della macchina virtuale e del servizio cloud:

    vm_mgr = Azure::VirtualMachineService.new
    vm = vm_mgr.start_virtual_machine('myvm', 'mycloudservice')

Procedura: Lavorare con le immagini e i dischi delle macchine virtuali
----------------------------------------------------------------------

Le operazioni sulle immagini delle macchine virtuali vengono eseguite utilizzando la classe **Azure::VirtualMachineImageService**. Le operazioni sui dischi vengono eseguite utilizzando la classe **Azure::VirtualMachineImageManagement::VirtualMachineDiskManagementService**.

### Procedura: Elencare le immagini delle macchine virtuali

Per elencare le immagini della macchina virtuale disponibili utilizzare il metodo **list\_virtual\_machine\_images**. Questo metodo restituisce una matrice di oggetti **Azure::VirtualMachineImageService**.

    image_mgr = Azure::VirtualMachineImageService.new
    images = image_mgr.list_virtual_machine_images

### Procedura: Elencare i dischi

Per elencare i dischi per la sottoscrizione di Azure utilizzare il metodo **list\_virtual\_machine\_disks**. Questo metodo restituisce una matrice di oggetti **Azure::VirtualMachineImageManagement::VirtualMachineDisk**.

    disk_mgr = Azure::VirtualMachineImageManagement::VirtualMachineDiskManagementService.new
    disks = disk_mgr.list_virtual_machine_disks

### Procedura: Eliminare un disco

Per eliminare un disco utilizzare il metodo **delete\_virtual\_machine\_disk** e specificare il nome del disco da eliminare:

    disk_mgr = Azure::VirtualMachineImageManagement::VirtualMachineDiskManagementService.new
    disk_mgr.delete_virtual_machine_disk

Procedura: Lavorare con i servizi cloud
---------------------------------------

Le operazioni di gestione per Servizi cloud di Azure vengono eseguite utilizzando la classe **Azure::CloudService**.

### Procedura: Creare un servizio cloud

Per creare un nuovo servizio cloud utilizzare il metodo **create\_cloud\_service** e indicare un nome e un hash di opzioni. Le opzioni valide sono le seguenti:

-   **:location**: *obbligatoria*. L'area in cui verrà creato il servizio cloud.

-   **:description**: descrizione del servizio cloud.

Il codice seguente consente di creare un nuovo servizio cloud nell'area est degli Stati Uniti:

    cs_mgr = Azure::CloudService.new
    cs_mgr.create_cloud_service('mycloudservice', { :location => 'East US' })

### Procedura: Elencare i servizi cloud

Per elencare i servizi cloud per la sottoscrizione di Azure utilizzare il metodo **list\_cloud\_services**. Questo metodo restituisce una matrice di oggetti **Azure::CloudServiceManagement::CloudService**:

    cs_mgr = Azure::CloudService.new
    cloud_services = cs_mgr.list_cloud_services

### Procedura: Verificare l'esistenza di un servizio cloud

Per verificare se uno specifico servizio cloud esiste già utilizzare il metodo **get\_cloud\_service** e indicare il nome del servizio cloud. Il metodo restituisce **true** se un servizio cloud con il nome specificato è esistente, altrimenti restituirà **false**.

    cs_mgr = Azure::CloudService.new
    cs_exists = cs_mgr.get_cloud_service('mycloudservice')

### Procedura: Eliminare un servizio cloud

Per eliminare un servizio cloud utilizzare il metodo **delete\_cloud\_service** e indicare il nome del servizio cloud:

    cs_mgr = Azure::CloudService.new
    cs_mgr.delete_cloud_service('mycloudservice')

### Procedura: Eliminare una distribuzione

Per eliminare una distribuzione utilizzare il metodo **delete\_cloud\_service\_deployment** e indicare il nome del servizio cloud:

    cs_mgr = Azure::CloudService.new
    cs_mgr.delete_cloud_service_deployment('mycloudservice')

Procedura: Lavorare con i servizi di archiviazione
--------------------------------------------------

Le operazioni di gestione per Servizi cloud di Azure vengono eseguite utilizzando la classe **Azure::StorageService**.

### Procedura: Creare un account di archiviazione

Per creare un nuovo account di archiviazione utilizzare il metodo **create\_storage\_account** e indicare un nome e un hash di opzioni. Le opzioni valide sono le seguenti:

-   **:location**: *obbligatoria*. L'area in cui verrà creato l'account di archiviazione.

-   **:description**: descrizione dell'account di archiviazione.

Il codice seguente consente di creare un nuovo account di archiviazione nell'area est degli Stati Uniti:

    storage_mgr = Azure::StorageService.new
    storage_mgr.create_storage_account('mystorage', { :location => 'East US' })

### Procedura: Elencare account di archiviazione

Per ottenere un elenco di account di archiviazione per la sottoscrizione di Azure utilizzare il metodo **list\_storage\_accounts**. Questo metodo restituisce una matrice di oggetti **Azure::StorageManagement::StorageAccount**.

    storage_mgr = Azure::StorageService.new
    accounts = storage_mgr.list_storage_accounts

### Procedura: Verificare l'esistenza di un account di archiviazione

Per verificare l'esistenza di uno specifico account di archiviazione utilizzare il metodo **get\_storage\_account** e specificare il nome dell'account di archiviazione. Il metodo restituisce **true** se l'account di archiviazione è esistente, altrimenti restituirà **false**.

    storage_mgr = Azure::StorageService.new
    store_exists = storage_mgr.get_storage_account('mystorage')

### Procedura: Eliminare un account di archiviazione

Per eliminare un account di archiviazione utilizzare il metodo **delete\_storage\_account** e specificare il nome dell'account di archiviazione:

    storage_mgr = Azure::StorageService.new
    storage_mgr.delete_storage_account('mystorage')

Passaggi successivi
-------------------

A questo punto, dopo aver appreso le nozioni di base a livello di codice della creazione delle Macchine virtuali di Azure, visitare i collegamenti seguenti per ulteriori informazioni sull'utilizzo delle VM.

-   Pagina delle funzionalità relativa alle [macchine virtuali](http://www.windowsazure.com/it-it/documentation/services/virtual-machines/)
-   Riferimento in MSDN: [Macchine virtuali](http://msdn.microsoft.com/it-it/library/windowsazure/jj156003.aspx)
-   Ulteriori informazioni su come ospitare l'[applicazione Ruby on Rails su una macchina virtuale](http://www.windowsazure.com/it-it/develop/ruby/tutorials/web-app-with-linux-vm/)

