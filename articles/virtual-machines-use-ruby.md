<properties 
	pageTitle="Informazioni su Azure Multi-Factor Authentication" 
	description="Informazioni su Multi-Factor Authentication, un metodo di autenticazione che richiede più di un metodo di verifica e con il quale viene aggiunto un secondo livello di sicurezza critico agli accessi e alle transazioni degli utenti." 
	services="active-directory, multi-factor-authentication" 
	documentationCenter="" 
	authors="blackmist" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="ruby" 
	ms.topic="article" 
	ms.date="09/17/2014" 
	ms.author="larryfr"/>



#Come gestire le macchine virtuali di Azure con Ruby

In questa guida verrà descritto come eseguire attività di gestione comuni a livello di codice per le macchine virtuali di Azure, ad esempio la creazione e la configurazione di VM e l'aggiunta di dischi dati. Azure SDK for Ruby fornisce l'accesso alla funzionalità di gestione servizi per una serie di Servizi di Azure, incluse le macchine virtuali di Azure.

##Sommario

* [Informazioni sulla gestione dei servizi](#what-is)
* [Concetti](#concepts)
* [Creare un certificato di gestione](#setup-certificate)
* [Creare un'applicazione Ruby](#create-app)
* [Configurare l'applicazione per l'uso dell'SDK](#configure-access)
* [Configurare una connessione di gestione di Azure](#setup-connection)
* [Procedura: Lavorare con Macchine virtuali](#virtual-machine)
* [Procedura: Lavorare con immagini e dischi](#vm-images)
* [Procedura: Lavorare con i servizi cloud](#cloud-services)
* [Procedura: Lavorare con i servizi di archiviazione](#storage-services)
* [Passaggi successivi](#next-steps)

## <a name="what-is"> </a>Informazioni sulla gestione dei servizi

Azure fornisce le [API REST per le operazioni di gestione dei servizi](http://msdn.microsoft.com/library/windowsazure/ee460799.aspx), inclusa la gestione di Macchine virtuali di Azure. Azure SDK for Ruby espone le operazioni di gestione per Macchine virtuali attraverso la classe **Azure::VirtualMachineSerivce**. L'utilizzo di questa classe consente di accedere a gran parte delle funzionalità di gestione della macchina virtuale disponibili sul [portale di gestione di Azure](https://manage.windowsazure.com).

Benché sia possibile usare le API di gestione servizi per gestire una serie di servizi ospitati su Azure, in questo documento sono disponibili informazioni solo sulla gestione di Macchine virtuali di Azure.

## <a name="concepts"> </a>Concetti

Le macchine virtuali di Azure vengono implementate come 'ruoli' nell'ambito di un servizio cloud. Ciascun servizio cloud può contenere uno o più ruoli, che sono raggruppati logicamente in distribuzioni. Il ruolo definisce le caratteristiche fisiche generali della VM, ad esempio la quantità di memoria disponibile, il numero di core CPU ecc.

Ogni VM è inoltre dotata di un disco del sistema operativo, che contiene il sistema operativo di avvio. Una VM può essere dotata di uno o più dischi, cioè dischi aggiuntivi da usare prevalentemente per archiviare i dati dell'applicazione. I dischi vengono implementati come dischi rigidi virtuali (VHD) memorizzati nell'Archiviazione BLOB di Azure. È inoltre possibile esportare i VHD come 'immagini', ossia modelli che sono usati per creare dischi adoperati da una VM durante la sua creazione. Ad esempio, la creazione di una nuova VM usa un'immagine Ubuntu che consentirà di creare un nuovo disco del sistema operativo.

La maggior parte delle immagini è fornita da Microsoft o dai suoi partner, tuttavia è possibile creare immagini personali oppure un'immagine da una VM ospitata su Azure.

## <a name="setup-certificate"> </a>Creare un certificato di gestione Azure

Quando si eseguono operazioni di gestione dei servizi, ad esempio quelle esposte tramite la classe **Azure::VirtualMachineService**, è necessario indicare l'ID sottoscrizione di Azure e un file contenente un certificato di gestione per la sottoscrizione. Entrambi verranno usati dall'SDK durante l'autenticazione dell'API REST di Azure.

È possibile ottenere l'ID sottoscrizione e un certificato di gestione usando l'interfaccia della riga di comando multipiattaforma di Azure (xplat-cli). Per informazioni sull'installazione e la configurazione dell'interfaccia xplat-cli vedere [Installare e configurare l'interfaccia della riga di comando multipiattaforma di Azure](http://azure.microsoft.com/manage/install-and-configure-cli/).

Dopo aver configurato l'interfaccia xplat-cli sarà possibile eseguire la procedura seguente per recuperare l'ID sottoscrizione di Azure ed esportare un certificato di gestione:

1. Per recuperare l'ID sottoscrizione, usare il comando seguente:

		azure account list

2. Per esportare il certificato di gestione, usare il comando seguente:

		azure account cert export

	Al termine dell'esecuzione del comando, il certificato verrà esportato in un file denominato &lt;azure-subscription-name&gt;.pem. Ad esempio, se la sottoscrizione è denominata **sottoscrizionepersonale**, il file creato verrà denominato **sottoscrizionepersonale.pem**.

Prendere nota dell'ID sottoscrizione e del percorso del file PEM contenente il certificato esportato, in quanto verranno usati più avanti in questo documento.

## <a name="create-app"></a>Creare un'applicazione Ruby

Creare una nuova applicazione Ruby. È possibile implementare gli esempi usati in questo documento in un unico file **.rb**.

## <a name="configure-access"></a>Configurare l'applicazione

Per gestire i Servizi di Azure è necessario scaricare e usare la gemma di Azure, che contiene Azure SDK for Ruby.

### Usare il comando della gemma per installare il pacchetto

1.  Usare un'interfaccia della riga di comando come **PowerShell** (Windows), **Terminale** (Mac) o **Bash** (UNIX) e passare alla cartella in cui è stata creata l'applicazione di esempio.

2. Per installare la gemma di Azure usare il comando seguente:

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

	> [AZURE.NOTE] Se viene visualizzato un errore relativo alle autorizzazioni, usare in alternativa <code>sudo gem install azure</code>.

### Richiedere la gemma

Usando un editor di testo, aggiungere quanto segue nella parte superiore del file dell'applicazione Ruby: Verrà caricata la gemma di Azure, che renderà Azure SDK for Ruby disponibile nell'applicazione:

	require 'azure'

## <a name="setup-connection"> </a>Procedura: Connettersi alla gestione dei servizi

Per eseguire correttamente le operazioni di gestione dei servizi con Azure è necessario specificare l'ID sottoscrizione e il certificato ottenuti nella sezione [Creare un certificato di gestione Azure](#setup-certificate) . Il modo più semplice consiste nello specificare l'ID e il percorso del file del certificato usando le variabili di ambiente seguenti:

* AZURE\_MANAGEMENT\_CERTIFICATE - Il percorso del file PEM contenente il certificato di gestione.

* AZURE\_SUBSCRIPTION\_ID - L'ID sottoscrizione della sottoscrizione di Azure.

È inoltre possibile impostare questi valori a livello di codice nell'applicazione usando il comando seguente:

	Azure.configure do |config|
	  config.management_certificate = 'path/to/certificate'
	  config.subscription_id = 'subscription ID'
	end

##<a name="virtual-machine"> </a>Procedura: Lavorare con Macchine virtuali

Le operazioni di gestione per Macchine virtuali di Azure vengono eseguite usando la classe **Azure::VirtualMachineService**.

###Procedura: Creare una nuova macchina virtuale

Per creare una nuova macchina virtuale usare il metodo **create\_virtual\_machine**. Questo metodo accetta un hash contenente i parametri seguenti e restituisce un'istanza di **Azure::VirtualMachineManagement::VirtualMachine** che descrive la VM che è stata creata:

**Parametri**

* **:vm\_name** - Nome della macchina virtuale

* **:vm\_user** - Nome utente dell'utente ROOT o dell'amministratore

* **:password** - Password da usare per l'utente ROOT o l'amministratore

* **:image** - Immagine del sistema operativo che verrà usata per creare il disco per questa VM. Il disco del sistema operativo verrà archiviato in un VHD creato nell'archiviazione BLOB

* **:location** - L'area in cui verrà creata la VM. Dovrà trattarsi della stessa area in cui si trova l'account di archiviazione che contiene i dischi usati da questa VM.

Di seguito è riportato un esempio di codice per la creazione di una nuova macchina virtuale usando questi parametri:

	vm_params = {
	  :vm_name => 'mygreatvm',
	  :vm_user => 'myuser',
	  :password => 'mypassword',
	  :image => 'b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-13_04-amd64-server-20130824-it-it-30GB',
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

Di seguito sono riportate le opzioni disponibili quando si usa il metodo **create\_virtual\_machine**:

* **:storage\_account\_name** - Il nome dell'account di archiviazione da usare per l'archiviazione delle immagini del disco. Se non esiste un account di archiviazione, ne verrà creato uno nuovo. Se omesso, verrà creato un nuovo account di archiviazione con un nome basato sul parametro :vm\_name.

* **:cloud\_service\_name** - Il nome del servizio cloud da usare per l'hosting della macchina virtuale. Se non esiste un servizio cloud, ne verrà creato uno nuovo. Se omesso, verrà creato un nuovo servizio cloud con un nome basato sul parametro :vm\_name.

* **:deployment\_name** - Il nome della distribuzione da usare per la distribuzione della configurazione della macchina virtuale

* **:tcp\_endpoints** - Le porte TCP da esporre pubblicamente per questa VM. Non è necessario specificare l'endpoint SSH (per VM basate su Linux) e l'endpoint WinRM (per VM basate su Windows), che verranno creati automaticamente. È possibile specificare più porte, separate da una virgola. Per associare una porta interna a una porta pubblica usando un nome di porta diverso, usare il formato **porta pubblica:porta interna**. Ad esempio, 80:8080 espone la porta interna 8080 come porta pubblica 80.

* **:service\_location** - Il percorso dell'archivio del certificato di destinazione sulla VM. Si applica solo alle VM basate su Windows.

* **:ssh\_private\_key\_file** - Il file contenente la chiave privata, che verrà usato per proteggere l'accesso SSH alle per VM basate su Linux. Viene altresì usato per specificare il certificato di protezione di WinRM se si seleziona il trasporto HTTPS. Se si omettono i valori **:ssh\_private\_key\_file** e **:ssh\_certificate\_file**, per SSH verrà usata unicamente l'autenticazione della password.

* **:ssh\_certificate\_file** - Il file contenente il certificato, che verrà usato per proteggere l'accesso SSH alle per VM basate su Linux. Viene altresì usato per specificare il certificato di protezione di WinRM se si seleziona il trasporto HTTPS. Se si omettono i valori **:ssh\_private\_key\_file** e **:ssh\_certificate\_file**, per SSH verrà usata unicamente l'autenticazione della password.

* **:ssh\_port** - La porta pubblica che verrà usata per la comunicazione SSH. Se omessa, la porta SSH predefinita è la 22.

* **:vm\_size** - Le dimensioni della VM. Questo valore determina le dimensioni della memoria, il numero di core, la larghezza di banda e altre caratteristiche fisiche della VM. Per informazioni dettagliate sulle dimensioni e le caratteristiche fisiche disponibili per le macchine virtuali, vedere l'articolo relativo alle [dimensioni della macchina virtuale e dei Servizi cloud per Azure](http://msdn.microsoft.com/library/windowsazure/dn197896.aspx).

* **:winrm_transport** - Una matrice dei trasporti disponibili per l'uso con WinRM. I trasporti validi sono 'http' e 'https'. Se si specifica 'https' come trasporto, sarà inoltre necessario usare **:ssh\_private\_key\_file** e **:ssh\_certificate\_file** al fine di specificare il certificato usato per proteggere le comunicazioni HTTPS.

Di seguito è riportato un esempio di creazione di una macchina virtuale che usa una piccola istanza di calcolo, espone pubblicamente le porte per il traffico HTTP (porta locale 8080, porta pubblica 80) e HTTPS (443) e consente di eseguire l'autenticazione del certificato per le sessioni SSH usando i file di certificato specificati:

	vm_params = {
	  :vm_name => 'myvm',
	  :vm_user => 'myuser',
	  :password => 'mypassword',
	  :image => 'b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-13_04-amd64-server-20130824-it-it-30GB',
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

###Procedura: Elencare le macchine virtuali

Per elencare le macchine virtuali per la sottoscrizione di Azure usare il metodo **list\_virtual\_machines**. Questo metodo restituisce una matrice di oggetti **Azure::VirtualMachineManagement::VirtualMachine**:

	vm_mgr = Azure::VirtualMachineService.new
	virtual_machines = vm_mgr.list_virtual_machines

###Procedura: Ottenere informazioni su una macchina virtuale

Per ottenere un'istanza di **Azure::VirtualMachineManagement::VirtualMachine** per una specifica macchina virtuale usare il metodo **get\_virtual\_machine** e indicare i nomi della macchina virtuale e del servizio cloud:

	vm_mgr = Azure::VirtualMachineService.new
	vm = vm_mgr.get_virtual_machine('myvm', 'mycloudservice')

###Procedura: Eliminare una macchina virtuale

Per eliminare una macchina virtuale usare il metodo **delete\_virtual\_machine** e indicare i nomi della macchina virtuale e del servizio cloud:

	vm_mgr = Azure::VirtualMachineService.new
	vm = vm_mgr.delete_virtual_machine('myvm', 'mycloudservice')

> [AZURE.WARNING] Il metodo **delete_virtual_machine** consente di eliminare il servizio cloud ed eventuali dischi associato alla macchina virtuale.

###Procedura: Arrestare una macchina virtuale

Per arrestare una macchina virtuale usare il metodo **shutdown\_virtual\_machine** e indicare i nomi della macchina virtuale e del servizio cloud:

	vm_mgr = Azure::VirtualMachineService.new
	vm = vm_mgr.shutdown_virtual_machine('myvm', 'mycloudservice')

###Procedura: Avviare una macchina virtuale

Per avviare una macchina virtuale usare il metodo **start\_virtual\_machine** e indicare i nomi della macchina virtuale e del servizio cloud:

	vm_mgr = Azure::VirtualMachineService.new
	vm = vm_mgr.start_virtual_machine('myvm', 'mycloudservice')

##<a name="vm-images"> </a>Procedura: Lavorare con le immagini e i dischi delle macchine virtuali

Le operazioni sulle immagini delle macchine virtuali vengono eseguite usando la classe **Azure::VirtualMachineImageService**. Le operazioni sui dischi vengono eseguite usando la classe **Azure::VirtualMachineImageManagement::VirtualMachineDiskManagementService**.

###Procedura: Elencare le immagini delle macchine virtuali

Per elencare le immagini della macchina virtuale disponibili usare il metodo **list\_virtual\_machine\_images**. Questo metodo restituisce una matrice di oggetti **Azure::VirtualMachineImageService**.

	image_mgr = Azure::VirtualMachineImageService.new
	images = image_mgr.list_virtual_machine_images

###Procedura: Elencare i dischi

Per elencare i dischi per la sottoscrizione di Azure usare il metodo **list\_virtual\_machine\_disks**. Questo metodo restituisce una matrice di oggetti **Azure::VirtualMachineImageManagement::VirtualMachineDisk**.

	disk_mgr = Azure::VirtualMachineImageManagement::VirtualMachineDiskManagementService.new
	disks = disk_mgr.list_virtual_machine_disks

###Procedura: Eliminare un disco

Per eliminare un disco usare il metodo **delete\_virtual\_machine\_disk** e specificare il nome del disco da eliminare:

	disk_mgr = Azure::VirtualMachineImageManagement::VirtualMachineDiskManagementService.new
	disk_mgr.delete_virtual_machine_disk

##<a name="cloud-services"> </a>Procedura: Lavorare con i servizi cloud

Le operazioni di gestione per Servizi cloud di Azure vengono eseguite usando la classe **Azure::CloudService**.

###Procedura: Creare un servizio cloud

Per creare un nuovo servizio cloud usare il metodo **create\_cloud\_service** e indicare un nome e un hash di opzioni. Le opzioni valide sono le seguenti:

* **:location** - *Obbligatoria*. L'area in cui verrà creato il servizio cloud.

* **:description** - Descrizione del servizio cloud.

Il codice seguente consente di creare un nuovo servizio cloud nell'area est degli Stati Uniti:

	cs_mgr = Azure::CloudService.new
	cs_mgr.create_cloud_service('mycloudservice', { :location => 'East US' })

###Procedura: Elencare i servizi cloud

Per elencare i servizi cloud per la sottoscrizione di Azure usare il metodo **list\_cloud\_services**. Questo metodo restituisce una matrice di oggetti **Azure::CloudServiceManagement::CloudService**:

	cs_mgr = Azure::CloudService.new
	cloud_services = cs_mgr.list_cloud_services

###Procedura: Verificare l'esistenza di un servizio cloud

Per verificare se uno specifico servizio cloud esiste già usare il metodo **get\_cloud\_service** e indicare il nome del servizio cloud. Il metodo restituisce **true** se un servizio cloud con il nome specificato è esistente, altrimenti restituirà **false**.

	cs_mgr = Azure::CloudService.new
	cs_exists = cs_mgr.get_cloud_service('mycloudservice')

###Procedura: Eliminare un servizio cloud

Per eliminare un servizio cloud usare il metodo **delete\_cloud\_service** e indicare il nome del servizio cloud:

	cs_mgr = Azure::CloudService.new
	cs_mgr.delete_cloud_service('mycloudservice')

###Procedura: Eliminare una distribuzione

Per eliminare una distribuzione usare il metodo **delete\_cloud\_service\_deployment** e indicare il nome del servizio cloud:

	cs_mgr = Azure::CloudService.new
	cs_mgr.delete_cloud_service_deployment('mycloudservice')

##<a name="storage-services"> </a>Procedura: Lavorare con i servizi di archiviazione

Le operazioni di gestione per Servizi cloud di Azure vengono eseguite usando la classe **Azure::StorageService**.

###Procedura: Creare un account di archiviazione

Per creare un nuovo account di archiviazione usare il metodo **create\_storage\_account** e indicare un nome e un hash di opzioni. Le opzioni valide sono le seguenti:

* **:location** - *Obbligatoria*. L'area in cui verrà creato l'account di archiviazione.

* **:description** - Descrizione dell'account di archiviazione.

Il codice seguente consente di creare un nuovo account di archiviazione nell'area est degli Stati Uniti:

	storage_mgr = Azure::StorageService.new
	storage_mgr.create_storage_account('mystorage', { :location => 'East US' })

###Procedura: Elencare account di archiviazione

Per ottenere un elenco di account di archiviazione per la sottoscrizione di Azure usare il metodo **list\_storage\_accounts**. Questo metodo restituisce una matrice di oggetti **Azure::StorageManagement::StorageAccount**.

	storage_mgr = Azure::StorageService.new
	accounts = storage_mgr.list_storage_accounts

###Procedura: Verificare l'esistenza di un account di archiviazione

Per verificare l'esistenza di uno specifico account di archiviazione usare il metodo **get\_storage\_account** e specificare il nome dell'account di archiviazione. Il metodo restituisce **true** se l'account di archiviazione è esistente, altrimenti restituirà **false**.

	storage_mgr = Azure::StorageService.new
	store_exists = storage_mgr.get_storage_account('mystorage')

###Procedura: Eliminare un account di archiviazione

Per eliminare un account di archiviazione usare il metodo **delete\_storage\_account** e specificare il nome dell'account di archiviazione:

	storage_mgr = Azure::StorageService.new
	storage_mgr.delete_storage_account('mystorage')

##<a name="next-steps"> </a>Passaggi successivi

A questo punto, dopo aver appreso le nozioni di base a livello di codice della creazione delle Macchine virtuali di Azure, visitare i collegamenti seguenti per altre informazioni sull'utilizzo delle VM.

* Pagina delle funzionalità relative alle [macchine virtuali](http://azure.microsoft.com/documentation/services/virtual-machines/)
*  Riferimento in MSDN: [Macchine virtuali](http://msdn.microsoft.com/library/windowsazure/jj156003.aspx)
* Ulteriori informazioni su come ospitare l'[applicazione Ruby on Rails su una macchina virtuale](http://azure.microsoft.com/develop/ruby/tutorials/web-app-with-linux-vm/)


<!--HONumber=42-->
