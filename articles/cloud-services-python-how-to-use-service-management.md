<properties 
	pageTitle="Come usare l'API di gestione dei servizi (Python) - Guida alle funzionalità" 
	description="Informazioni su come eseguire attività comuni di gestione dei servizi a livello di codice da Python." 
	services="cloud-services" 
	documentationCenter="python" 
	authors="huguesv" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="cloud-services" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="python" 
	ms.topic="article" 
	ms.date="09/25/2014" 
	ms.author="huvalo"/>




# Come usare la gestione dei servizi da Python

In questa guida verrà descritto come eseguire attività comuni di gestione dei servizi a livello di codice da Python. La classe **ServiceManagementService** in [Azure SDK per Python][download-SDK-Python] supporta l'accesso a livello di codice alla maggior parte delle funzionalità di gestione dei servizi disponibili tramite il [portale di gestione][management-portal], ad esempio **creazione, aggiornamento ed eliminazione dei servizi cloud, distribuzioni, servizi di gestione dati, macchine virtuali e gruppi di affinità**. Questa funzionalità può rivelarsi utile nella creazione di applicazioni che richiedono accesso a livello di codice alla gestione dei servizi. 

## Sommario

* [Informazioni sulla gestione dei servizi][]
* [Concetti][]
* [Procedura: Connettersi alla gestione dei servizi][]
* [Procedura: Creare un elenco delle località disponibili][]
* [Procedura: Creare un servizio cloud][]
* [Procedura: Eliminare un servizio cloud][]
* [Procedura: Creare una distribuzione][]
* [Procedura: Aggiornare una distribuzione][]
* [Procedura: Spostare distribuzioni tra gestione temporanea e produzione][]
* [Procedura: Eliminare una distribuzione][]
* [Procedura: Creare un servizio di archiviazione][]
* [Procedura: Eliminare un servizio di archiviazione][]
* [Procedura: Creare un gruppo di affinità][]
* [Procedura: Eliminare un gruppo di affinità][]
* [Procedura: Elencare i sistemi operativi disponibili][]
* [Procedura: Creare un'immagine del sistema operativo][]
* [Procedura: Eliminare un'immagine del sistema operativo][]
* [Procedura: Creare una macchina virtuale][]
* [Procedura: Eliminare una macchina virtuale][]
* [Passaggi successivi][]

## <a name="WhatIs"> </a>Informazioni sulla gestione dei servizi
L'API di gestione del servizio fornisce accesso a livello di codice alla maggior parte delle funzionalità di gestione dei servizi disponibili tramite il [portale di gestione][management-portal]. Azure SDK per Python consente di gestire i servizi cloud, gli account di archiviazione e i gruppi di affinità.

Per usare l'API di gestione del servizio, sarà necessario [creare un account Azure](http://azure.microsoft.com/pricing/free-trial/). 

## <a name="Concepts"> </a>Concetti
Azure SDK per Python include l'[API di gestione del servizio Azure][svc-mgmt-rest-api], ossia un'API REST. Tutte le operazioni dell'API vengono eseguite tramite SSL e autenticate reciprocamente con certificati X.509 v3. Il servizio di gestione è accessibile da un servizio in esecuzione in Azure o direttamente tramite Internet da qualsiasi applicazione in grado di inviare una richiesta HTTPS e ricevere una risposta HTTPS.

## <a name="Connect"> </a>Procedura: Connettersi alla gestione dei servizi
Per connettersi all'endpoint di gestione dei servizi, sono necessari un ID sottoscrizione di Azure e un certificato di gestione valido. È possibile ottenere l'ID sottoscrizione tramite il [portale di gestione][management-portal].

> [AZURE.NOTE] A partire da Azure SDK per Python v0.8.0 è possibile usare i certificati creati con OpenSSL anche per l'esecuzione in Windows.  Sarà necessario Python 2.7.4 o versioni successive.

### Certificati di gestione in Windows (MakeCert)

È possibile creare nel computer un certificato di gestione autofirmato usando `makecert.exe`.  Aprire un **prompt dei comandi di Visual Studio** come **amministratore** e usare il seguente comando, sostituendo *AzureCertificate* con il nome del certificato che si desidera usare.

    makecert -sky exchange -r -n "CN=AzureCertificate" -pe -a sha1 -len 2048 -ss My "AzureCertificate.cer"

Il comando consente di creare il file `.cer` e di installarlo nell'archivio certificati **Personale**. Per altri dettagli, vedere l'articolo relativo alla [creazione e al caricamento di un certificato di gestione per Azure](http://msdn.microsoft.com/library/windowsazure/gg551722.aspx).

Dopo avere creato il certificato sarà necessario caricare il file `.cer` in Azure selezionando l'opzione "Carica" della scheda "Impostazioni" del [portale di gestione][management-portal].

Dopo avere ottenuto l'ID sottoscrizione, avere creato un certificato e avere caricato il file `.cer` in Azure, è possibile connettersi all'endpoint di gestione di Azure passando l'ID sottoscrizione e il percorso del certificato nell'archivio certificati **Personale** all'oggetto **ServiceManagementService** (sostituire di nuobo *AzureCertificate* con il nome del certificato):

	from azure import *
	from azure.servicemanagement import *

	subscription_id = '<your_subscription_id>'
	certificate_path = 'CURRENT_USER\\my\\AzureCertificate'

	sms = ServiceManagementService(subscription_id, certificate_path)

Nel precedente esempio, `sms` è un oggetto **ServiceManagementService**. La classe **ServiceManagementService** è la classe principale usata per la gestione dei servizi Azure. 

### Certificati di gestione in Windows/Mac/Linux (OpenSSL)
È possibile usare [OpenSSL](http://www.openssl.org/) per creare il certificato di gestione del servizio.  È in realtà necessario creare due certificati, uno per il server (un file `.cer`) e uno per il client (un file `.pem`). Per creare il file `.pem`, eseguire questo codice:

	`openssl req -x509 -nodes -days 365 -newkey rsa:1024 -keyout mycert.pem -out mycert.pem`

Per creare il certificato `.cer`, eseguire questo codice:

	`openssl x509 -inform pem -in mycert.pem -outform der -out mycert.cer`

Per altre informazioni sui certificati Azure, vedere [Gestione dei certificati in Azure](http://msdn.microsoft.com/library/windowsazure/gg981929.aspx). Per una descrizione completa dei parametri OpenSSL, vedere la documentazione disponibile all'indirizzo [http://www.openssl.org/docs/apps/openssl.html](http://www.openssl.org/docs/apps/openssl.html).

Dopo aver creato questi file sarà necessario caricare il file `.cer` in Azure selezionando l'opzione "Carica" della scheda "Impostazioni" del [portale di gestione][management-portal]. Sarà inoltre necessario prendere nota del percorso di salvataggio del file `.pem`.

Dopo avere ottenuto l'ID sottoscrizione, avere creato un certificato e avere caricato il file `.cer` in Azure, è possibile connettersi all'endpoint di gestione di Azure passando l'ID sottoscrizione e il percorso del file `.pem` a **ServiceManagementService**:

	from azure import *
	from azure.servicemanagement import *

	subscription_id = '<your_subscription_id>'
	certificate_path = '<path_to_.pem_certificate>'
	
	sms = ServiceManagementService(subscription_id, certificate_path)

Nel precedente esempio, `sms` è un oggetto **ServiceManagementService**. La classe **ServiceManagementService** è la classe principale usata per la gestione dei servizi Azure. 

## <a name="ListAvailableLocations"> </a>Procedura: Creare un elenco delle località disponibili

Per elencare le località disponibili per i servizi di hosting, usare il metodo **list\_locations**:

	from azure import *
	from azure.servicemanagement import *

	sms = ServiceManagementService(subscription_id, certificate_path)

	result = sms.list_locations()
	for location in result:
		print(location.name)

Quando si crea un servizio cloud, un servizio di archiviazione o un gruppo di affinità, è necessario fornire una località valida. Il metodo **list\_locations** restituirà sempre un elenco aggiornato delle località attualmente disponibili. Al momento della stesura di questo articolo, le località disponibili sono le seguenti:

- Europa occidentale 
- Asia sudorientale 
- East Asia 
- Stati Uniti centro-settentrionali 
- Europa settentrionale 
- Stati Uniti centro-meridionali 
- Stati Uniti occidentali 
- Stati Uniti Orientali

## <a name="CreateCloudService"> </a>Procedura: Creare un servizio cloud

Quando si crea un'applicazione e la si esegue in Azure, la combinazione del codice e della configurazione costituisce il cosiddetto [servizio cloud] (noto come *hosted service* nelle versioni precedenti di Azure). Il metodo **create\_hosted\_service** consente di creare un nuovo servizio ospitato specificando un nome di servizio ospitato (che deve essere univoco in Azure), un'etichetta (con codifica Base 64 automatica), una descrizione e una località. È possibile specificare un gruppo di affinità anziché una località per il servizio. 

	from azure import *
	from azure.servicemanagement import *

	sms = ServiceManagementService(subscription_id, certificate_path)

	name = 'myhostedservice'
	label = 'myhostedservice'
	desc = 'my hosted service'
	location = 'West US'

	# You can either set the location or an affinity_group
	sms.create_hosted_service(name, label, desc, location)

Per elencare tutti i servizi ospitati per la sottoscrizione, è possibile usare il metodo **list\_hosted\_services**:

	result = sms.list_hosted_services()

	for hosted_service in result:
		print('Service name: ' + hosted_service.service_name)
		print('Management URL: ' + hosted_service.url)
		print('Affinity group: ' + hosted_service.hosted_service_properties.affinity_group)
		print('Location: ' + hosted_service.hosted_service_properties.location)
		print('')

Per ottenere eventuali informazioni su un particolare servizio ospitato, passare il nome del servizio ospitato al metodo **get\_hosted\_service\_properties**:

	hosted_service = sms.get_hosted_service_properties('myhostedservice')

	print('Service name: ' + hosted_service.service_name)
	print('Management URL: ' + hosted_service.url)
	print('Affinity group: ' + hosted_service.hosted_service_properties.affinity_group)
	print('Location: ' + hosted_service.hosted_service_properties.location)
			
Dopo avere creato un servizio cloud è possibile distribuire il codice al servizio con il metodo **create\_deployment**.

## <a name="DeleteCloudService"> </a>Procedura: Eliminare un servizio cloud

È possibile eliminare un servizio cloud passando il nome del servizio al metodo **delete\_hosted\_service**:

	sms.delete_hosted_service('myhostedservice')

Si noti che prima di eliminare un servizio è necessario eliminare tutte le distribuzioni del servizio. Per informazioni dettagliate, vedere [Procedura: Eliminare una distribuzione](#DeleteDeployment) per informazioni dettagliate.

## <a name="CreateDeployment"> </a>Procedura: Creare una distribuzione

Il metodo **create\_deployment** consente di caricare un nuovo [pacchetto del servizio] e di creare una nuova distribuzione nell'ambiente di gestione temporanea o di produzione. I parametri per questo metodo sono i seguenti:

* **name**: nome del servizio ospitato.
* **deployment\_name**: nome della distribuzione.
* **slot**: stringa che indica lo slot `staging` o `production`.
* **package_url**: URL del pacchetto di distribuzione (file con estensione cspgk). Il file del pacchetto deve essere archiviato in un account di archiviazione BLOB di Azure nella stessa sottoscrizione del servizio ospitato nel quale viene caricato il pacchetto. È possibile creare un pacchetto di distribuzione con i [cmdlet di Azure PowerShell] oppure con lo [strumento da riga di comando cspack].
* **configuration**: file di configurazione del servizio (con estensione cscfg) con codifica Base 64.
* **label**: etichetta del nome del servizio ospitato (con codifica Base 64 automatica).

Il seguente esempio crea una nuova distribuzione `v1` for a hosted service called `myhostedservice`:

	from azure import *
	from azure.servicemanagement import *
	import base64

	sms = ServiceManagementService(subscription_id, certificate_path)

	name = 'myhostedservice'
	deployment_name = 'v1'
	slot = 'production'
	package_url = 'URL_for_.cspkg_file'
	configuration = base64.b64encode(open('path_to_cscfg', 'rb'))
	label = 'myhostedservice'

	result = sms.create_deployment(name, slot, deployment_name, package_url, label, configuration)

	operation_result = sms.get_operation_status(result.request_id)
	print('Operation status: ' + operation_result.status)

Si noti nell'esempio precedente che è possibile recuperare lo stato dell'operazione **create\_deployment** passando il risultato restituito da **create\_deployment** al metodo **get\_operation\_status**.

È possibile accedere alle proprietà di distribuzione con il metodo **get\_deployment\_by\_slot** o **get\_deployment\_by\_name**. Il seguente esempio recupera una distribuzione specificando lo slot di distribuzione. Il seguente esempio esegue l'iterazione in tutte le istanze per la distribuzione:

	result = sms.get_deployment_by_slot('myhostedservice', 'production')

	print('Name: ' + result.name)
	print('Slot: ' + result.deployment_slot)
	print('Private ID: ' + result.private_id)
	print('Status: ' + result.status)
	print('Instances:')
	for instance in result.role_instance_list:
		print('Instance name: ' + instance.instance_name)
		print('Instance status: ' + instance.instance_status)
		print('Instance size: ' + instance.instance_size)

## <a name="UpdateDeployment"> </a>Procedura: Aggiornare una distribuzione

È possibile aggiornare una distribuzione usando il metodo **change\_deployment\_configuration** o **update\_deployment\_status**.

Il metodo **change\_deployment\_configuration** consente di caricare un nuovo file di configurazione del servizio (`.cscfg`). Ciò comporterà la modifica di tutte le impostazioni del servizio desiderate, incluso il numero di istanze in una distribuzione. Per altre informazioni, vedere [Schema di configurazione dei servizi di Azure (file .cscfg)]. Il seguente esempio illustra come caricare un nuovo file di configurazione del servizio:

	from azure import *
	from azure.servicemanagement import *
	import base64

	sms = ServiceManagementService(subscription_id, certificate_path)

	name = 'myhostedservice'
	deployment_name = 'v1'
	configuration = base64.b64encode(open('path_to_cscfg', 'rb'))

	result = sms.change_deployment_configuration(name, deployment_name, configuration)

	operation_result = sms.get_operation_status(result.request_id)
	print('Operation status: ' + operation_result.status)


Si noti nell'esempio precedente che è possibile recuperare lo stato dell'operazione **change\_deployment\_configuration** passando il risultato restituito da **change\_deployment\_configuration** al metodo **get\_operation\_status**.

Il metodo **update\_deployment\_status** consente di impostare uno stato di distribuzione su IN ESECUZIONE o SOSPESO. Il seguente esempio dimostra come impostare lo stato su IN ESECUZIONE per una distribuzione denominata `v1` of a hosted service called `myhostedservice`:

	from azure import *
	from azure.servicemanagement import *

	sms = ServiceManagementService(subscription_id, certificate_path)

	name = 'myhostedservice'
	deployment_name = 'v1'

	result = update_deployment_status(name, deployment_name, 'Running')

## <a name="MoveDeployments"> </a>Procedura: Spostare distribuzioni tra gestione temporanea e produzione

Azure fornisce due ambienti di distribuzione: gestione temporanea e produzione. In genere un servizio viene distribuito nell'ambiente di gestione temporanea per testarlo prima di distribuirlo nell'ambiente di produzione. L'operazione di innalzamento di livello del servizio dall'ambiente di gestione temporanea a quello di produzione può essere effettuata senza ridistribuire il servizio, ma semplicemente scambiando le distribuzioni. (Per altre informazioni sullo scambio di distribuzioni, vedere l'argomento relativo alla [distribuzione di un servizio Azure].)

Il seguente esempio illustra come usare il metodo **swap\_deployment** per scambiare due distribuzioni i cui nomi sono `v1` e `v2`. Nell'esempio, prima di chiamare il metodo **swap\_deployment**, la distribuzione `v1` si trova nello slot di produzione, mentre la distribuzione `v2` si trova nello slot di gestione temporanea. Dopo la chiamata a **swap\_deployment**, la distribuzione `v2` risulta in produzione e la distribuzione `v1` in gestione temporanea.  

	from azure import *
	from azure.servicemanagement import *

	sms = ServiceManagementService(subscription_id, certificate_path)

	result = sms.swap_deployment('myhostedservice', 'v1', 'v2')

## <a name="DeleteDeployment"> </a>Procedura: Eliminare una distribuzione

Per eliminare una distribuzione, usare il metodo **delete\_deployment**. Il seguente esempio illustra come eliminare una distribuzione denominata `v1`.

	from azure import *
	from azure.servicemanagement import *

	sms = ServiceManagementService(subscription_id, certificate_path)

	sms.delete_deployment('myhostedservice', 'v1')

## <a name="CreateStorageService"> </a>Procedura: Creare un servizio di archiviazione

Un [servizio di archiviazione] offre l'accesso a [BLOB][azure-blobs], [tabelle][azure-tables] e [code][azure-queues] di Azure. Per creare un servizio di archiviazione, è necessario assegnare al servizio un nome di lunghezza compresa tra 3 e 24 caratteri minuscoli e univoco in Azure, nonché una descrizione, un'etichetta fino a 100 caratteri con codifica in Base 64 automatica, e infine una località o un gruppo di affinità. Il seguente esempio illustra come creare un servizio di archiviazione specificando una località. Se si desidera usare un gruppo di affinità, è necessario prima crearlo (vedere [Procedura: Creare un gruppo di affinità](#CreateAffinityGroup)) e configurarlo con il parametro **affinity\_group**.

	from azure import *
	from azure.servicemanagement import *

	sms = ServiceManagementService(subscription_id, certificate_path)

	name = 'mystorageaccount'
	label = 'mystorageaccount'
	location = 'West US'
	desc = 'My storage account description.'

	result = sms.create_storage_account(name, desc, label, location=location)

	operation_result = sms.get_operation_status(result.request_id)
	print('Operation status: ' + operation_result.status)

Si noti nell'esempio precedente che è possibile recuperare lo stato dell'operazione **create\_storage\_account** passando il risultato restituito da **create\_storage\_account** al metodo **get\_operation\_status**.  

È possibile elencare gli account di archiviazione e le relative proprietà con il metodo **list\_storage\_accounts**:

	from azure import *
	from azure.servicemanagement import *

	sms = ServiceManagementService(subscription_id, certificate_path)

	result = sms.list_storage_accounts()
	for account in result:
		print('Service name: ' + account.service_name)
		print('Affinity group: ' + account.storage_service_properties.affinity_group)
		print('Location: ' + account.storage_service_properties.location)
		print('')

## <a name="DeleteStorageService"> </a>Procedura: Eliminare un servizio di archiviazione

È possibile eliminare un servizio di archiviazione passando il relativo nome al metodo **delete\_storage\_account**. L'eliminazione di un servizio di archiviazione comporta l'eliminazione di tutti i dati archiviati nel servizio (BLOB, tabelle e code).

	from azure import *
	from azure.servicemanagement import *

	sms = ServiceManagementService(subscription_id, certificate_path)

	sms.delete_storage_account('mystorageaccount')

## <a name="CreateAffinityGroup"> </a>Procedura: Creare un gruppo di affinità

Un gruppo di affinità è un raggruppamento logico di servizi di Azure che consente di individuare i servizi per ottimizzare le prestazioni. È ad esempio possibile creare un gruppo di affinità nella località "Stati Uniti occidentali" e quindi creare un [servizio cloud](#CreateCloudService) in tale gruppo. Se successivamente si crea un servizio di archiviazione nello stesso gruppo di affinità, questo verrà inserito nella località "West US" e ottimizzato per il data center al fine di garantire le migliori prestazioni con i servizi cloud nello stesso gruppo di affinità.

Per creare un gruppo di affinità, sono necessari un nome, un'etichetta (con codifica in Base 64 automatica) e una località. Facoltativamente, è possibile specificare una descrizione:

	from azure import *
	from azure.servicemanagement import *

	sms = ServiceManagementService(subscription_id, certificate_path)

	name = 'myAffinityGroup'
	label = 'myAffinityGroup'
	location = 'West US'
	desc = 'my affinity group'

	sms.create_affinity_group(name, label, location, desc)

Dopo avere creato un gruppo di affinità è possibile specificare il gruppo (anziché una località) durante la [creazione di un gruppo di archiviazione](#CreateStorageService).

È possibile elencare i gruppi di affinità e ispezionarne le proprietà chiamando il metodo **list\_affinity\_groups**:

	result = sms.list_affinity_groups()

	for group in result:
		print('Name: ' + group.name)
		print('Description: ' + group.description)
		print('Location: ' + group.location)
		print('')

## <a name="DeleteAffinityGroup"> </a>Procedura: Eliminare un gruppo di affinità
	
È possibile eliminare un gruppo di affinità passando il relativo nome al metodo **delete\_affinity\_group**. Si noti che per eliminare un gruppo di affinità, è prima necessario annullare la relativa associazione da qualsiasi servizio oppure eliminare i servizi che lo usano.

	from azure import *
	from azure.servicemanagement import *

	sms = ServiceManagementService(subscription_id, certificate_path)

	sms.delete_affinity_group('myAffinityGroup')

## <a name="ListOperatingSystems"> </a>Procedura: Elencare i sistemi operativi disponibili

Per elencare i sistemi operativi disponibili per i servizi di hosting, usare il metodo **list\_operating\_systems**:

	from azure import *
	from azure.servicemanagement import *

	sms = ServiceManagementService(subscription_id, certificate_path)

	result = sms.list_operating_systems()

	for os in result:
		print('OS: ' + os.label)
		print('Family: ' + os.family_label)
		print('Active: ' + str(os.is_active))

In alternativa, è possibile usare il metodo **list\_operating\_system\_families**, che consente di raggruppare i sistemi operativi in base alla famiglia:

	result = sms.list_operating_system_families()

	for family in result:
		print('Family: ' + family.label)
		for os in family.operating_systems:
			if os.is_active:
				print('OS: ' + os.label)
				print('Version: ' + os.version)
		print('')

## <a name="CreateVMImage"> </a>Procedura: Creare un'immagine del sistema operativo

Per aggiungere un'immagine del sistema operativo al repository di immagini, usare il metodo **add\_os\_image**:

	from azure import *
	from azure.servicemanagement import *

	sms = ServiceManagementService(subscription_id, certificate_path)

	name = 'mycentos'
	label = 'mycentos'
	os = 'Linux' # Linux or Windows
	media_link = 'url_to_storage_blob_for_source_image_vhd'

	result = sms.add_os_image(label, media_link, name, os)

	operation_result = sms.get_operation_status(result.request_id)
	print('Operation status: ' + operation_result.status)

Per elencare le immagini del sistema operativo disponibili, usare il metodo **list\_os\_images**. Verranno incluse tutte le immagini di piattaforma e utente:

	result = sms.list_os_images()

	for image in result:
		print('Name: ' + image.name)
		print('Label: ' + image.label)
		print('OS: ' + image.os)
		print('Category: ' + image.category)
		print('Description: ' + image.description)
		print('Location: ' + image.location)
		print('Affinity group: ' + image.affinity_group)
		print('Media link: ' + image.media_link)
		print('')

## <a name="DeleteVMImage"> </a>Procedura: Eliminare un'immagine del sistema operativo

Per eliminare un'immagine dell'utente, usare il metodo **delete\_os\_image**:

	from azure import *
	from azure.servicemanagement import *

	sms = ServiceManagementService(subscription_id, certificate_path)

	result = sms.delete_os_image('mycentos')

	operation_result = sms.get_operation_status(result.request_id)
	print('Operation status: ' + operation_result.status)

## <a name="CreateVM"> </a>Procedura: Creare una macchina virtuale

Per creare una macchina virtuale, è necessario innanzitutto creare un [servizio cloud](#CreateCloudService).  In seguito è necessario creare la distribuzione della macchina virtuale usando il metodo **create\_virtual\_machine\_deployment**:

	from azure import *
	from azure.servicemanagement import *

	sms = ServiceManagementService(subscription_id, certificate_path)

	name = 'myvm'
	location = 'West US'

	# You can either set the location or an affinity_group
	sms.create_hosted_service(service_name=name,
		label=name,
		location=location)

	# Name of an os image as returned by list_os_images
	image_name = 'OpenLogic__OpenLogic-CentOS-62-20120531-it-it-30GB.vhd'

	# Destination storage account container/blob where the VM disk
	# will be created
	media_link = 'url_to_target_storage_blob_for_vm_hd'

	# Linux VM configuration, you can use WindowsConfigurationSet
	# for a Windows VM instead
	linux_config = LinuxConfigurationSet('myhostname', 'myuser', 'mypassword', True)

	os_hd = OSVirtualHardDisk(image_name, media_link)

	sms.create_virtual_machine_deployment(service_name=name,
		deployment_name=name,
		deployment_slot='production',
		label=name,
		role_name=name,
		system_config=linux_config,
		os_virtual_hard_disk=os_hd,
		role_size='Small')

## <a name="DeleteVM"> </a>Procedura: Eliminare una macchina virtuale

Per eliminare una macchina virtuale, è prima necessario eliminare la distribuzione tramite il metodo **delete\_deployment**:

	from azure import *
	from azure.servicemanagement import *

	sms = ServiceManagementService(subscription_id, certificate_path)

	sms.delete_deployment(service_name='myvm',
		deployment_name='myvm')

Sarà quindi possibile eliminare il servizio cloud usando il metodo **delete\_hosted\_service**:

	sms.delete_hosted_service(service_name='myvm')

## <a name="NextSteps"> </a>Passaggi successivi

A questo punto, dopo aver appreso le nozioni di base della gestione dei servizi, visitare i seguenti collegamenti per informazioni su come eseguire attività più complesse.

-   Vedere le informazioni di riferimento in MSDN: [Servizi cloud][]
-   Vedere le informazioni di riferimento in MSDN: [Macchine virtuali][]

[Informazioni sulla gestione dei servizi]: #WhatIs
[Concetti]: #Concepts
[Procedura: Connettersi alla gestione dei servizi]: #Connect
[Procedura: Creare un elenco delle località disponibili]: #ListAvailableLocations
[Procedura: Creare un servizio cloud]: #CreateCloudService
[Procedura: Eliminare un servizio cloud]: #DeleteCloudService
[Procedura: Creare una distribuzione]: #CreateDeployment
[Procedura: Aggiornare una distribuzione]: #UpdateDeployment
[Procedura: Spostare distribuzioni tra gestione temporanea e produzione]: #MoveDeployments
[Procedura: Eliminare una distribuzione]: #DeleteDeployment
[Procedura: Creare un servizio di archiviazione]: #CreateStorageService
[Procedura: Eliminare un servizio di archiviazione]: #DeleteStorageService
[Procedura: Creare un gruppo di affinità]: #CreateAffinityGroup
[Procedura: Eliminare un gruppo di affinità]: #DeleteAffinityGroup
[Procedura: Elencare i sistemi operativi disponibili]: #ListOperatingSystems
[Procedura: Creare un'immagine del sistema operativo]: #CreateVMImage
[Procedura: Eliminare un'immagine del sistema operativo]: #DeleteVMImage
[Procedura: Creare una macchina virtuale]: #CreateVM
[Procedura: Eliminare una macchina virtuale]: #DeleteVM
[Passaggi successivi]: #NextSteps
[management-portal]: https://manage.windowsazure.com/
[svc-mgmt-rest-api]: http://msdn.microsoft.com/library/windowsazure/ee460799.aspx


[download-SDK-Python]: https://www.windowsazure.com/it-it/develop/python/common-tasks/install-python/
[servizio cloud]:http://windowsazure.com/it-it/documentation/articles/cloud-services-what-is
[pacchetto del servizio]: http://msdn.microsoft.com/library/windowsazure/jj155995.aspx
[Cmdlet di Azure PowerShell]: https://www.windowsazure.com/it-it/develop/php/how-to-guides/powershell-cmdlets/
[Strumento da riga di comando cspack]: http://msdn.microsoft.com/library/windowsazure/gg432988.aspx
[distribuzione di un servizio Azure]: http://msdn.microsoft.com/library/windowsazure/gg433027.aspx
[servizio di archiviazione]: https://www.windowsazure.com/it-it/manage/services/storage/what-is-a-storage-account/
[azure-blobs]: https://www.windowsazure.com/it-it/develop/python/how-to-guides/blob-service/
[azure-tables]: https://www.windowsazure.com/it-it/develop/python/how-to-guides/table-service/
[azure-queues]: https://www.windowsazure.com/it-it/develop/python/how-to-guides/queue-service/
[Schema di configurazione dei servizi di Azure (file .cscfg)]: http://msdn.microsoft.com/library/windowsazure/ee758710.aspx
[Servizi cloud]: http://msdn.microsoft.com/library/windowsazure/jj155995.aspx
[Macchine virtuali]: http://msdn.microsoft.com/library/windowsazure/jj156003.aspx

<!--HONumber=45--> 
