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
	ms.date="11/03/2015"
	ms.author="huvalo"/>

# Come usare la gestione dei servizi da Python

In questa guida verrà descritto come eseguire attività comuni di gestione dei servizi a livello di codice da Python. La classe **ServiceManagementService** disponibile in [Azure SDK per Python](../python-how-to-install.md) supporta l'accesso a livello di codice alla maggior parte delle funzionalità di gestione dei servizi disponibili tramite il [portale di Azure classico][management-portal] (ad esempio **creazione, aggiornamento ed eliminazione dei servizi cloud, distribuzioni, servizi di gestione dati e macchine virtuali**). Questa funzionalità può rivelarsi utile nella creazione di applicazioni che richiedono accesso a livello di codice alla gestione dei servizi.

> [AZURE.NOTE] L'API di gestione del servizio è stata sostituita con la nuova API di Gestione risorse, attualmente disponibile in una versione di anteprima. Vedere la [documentazione di Gestione risorse di Azure](http://azure-sdk-for-python.readthedocs.org/) per informazioni dettagliate su come usare la nuova API di Gestione risorse da Python.


## <a name="WhatIs"></a>Informazioni sulla gestione dei servizi
L'API di gestione dei servizi fornisce l'accesso a livello di codice alla maggior parte delle funzionalità di gestione dei servizi disponibili tramite il [portale di Azure classico][management-portal]. Azure SDK per Python consente di gestire i servizi cloud e gli account di archiviazione.

Per usare l'API di gestione dei servizi, sarà necessario [creare un account Azure](https://azure.microsoft.com/pricing/free-trial/).

## <a name="Concepts"> </a>Concetti
Azure SDK per Python include l'[API di Gestione servizi di Azure][svc-mgmt-rest-api], ovvero un'API REST. Tutte le operazioni dell'API vengono eseguite tramite SSL e autenticate reciprocamente con certificati X.509 v3. Il servizio di gestione è accessibile da un servizio in esecuzione in Azure o direttamente tramite Internet da qualsiasi applicazione in grado di inviare una richiesta HTTPS e ricevere una risposta HTTPS.

## <a name="Connect"></a>Procedura: Connettersi alla gestione dei servizi
Per connettersi all'endpoint di gestione dei servizi, sono necessari un ID sottoscrizione di Azure e un certificato di gestione valido. È possibile ottenere l'ID sottoscrizione tramite il [portale di Azure classico][management-portal].

> [AZURE.NOTE] A partire da Azure SDK per Python v0.8.0 è possibile usare i certificati creati con OpenSSL anche per l'esecuzione in Windows. Sarà necessario Python 2.7.4 o versioni successive. Per gli utenti è consigliabile usare i certificati OpenSSL anziché pfx, perché il supporto per i certificati pfx verrà probabilmente rimosso in futuro.

### Certificati di gestione in Windows/Mac/Linux (OpenSSL)
Per creare il certificato di gestione, è possibile usare [OpenSSL](http://www.openssl.org/). È in realtà necessario creare due certificati, uno per il server (un file `.cer`) e uno per il client (un file `.pem`). Per creare il file `.pem`, eseguire il comando seguente:

	`openssl req -x509 -nodes -days 365 -newkey rsa:1024 -keyout mycert.pem -out mycert.pem`

Per creare il certificato `.cer`, eseguire il comando seguente:

	`openssl x509 -inform pem -in mycert.pem -outform der -out mycert.cer`

Per altre informazioni sui certificati di Azure, vedere [Panoramica sui certificati per i servizi cloud di Azure](./cloud-services-certs-create.md). Per una descrizione completa dei parametri OpenSSL, vedere la documentazione disponibile all'indirizzo [http://www.openssl.org/docs/apps/openssl.html](http://www.openssl.org/docs/apps/openssl.html).

Dopo avere creato questi file sarà necessario caricare il file `.cer` in Azure selezionando l'opzione "Carica" della scheda "Impostazioni" del [portale di Azure classico][management-portal], e sarà inoltre necessario prendere nota del percorso di salvataggio del file `.pem`.

Dopo avere ottenuto l'ID sottoscrizione, avere creato un certificato e avere caricato il file `.cer` in Azure è possibile connettersi all'endpoint di gestione di Azure passando l'ID sottoscrizione e il percorso del file `.pem` a **ServiceManagementService**:

	from azure import *
	from azure.servicemanagement import *

	subscription_id = '<your_subscription_id>'
	certificate_path = '<path_to_.pem_certificate>'

	sms = ServiceManagementService(subscription_id, certificate_path)

Nell'esempio sopra riportato `sms` è un oggetto **ServiceManagementService**. La classe **ServiceManagementService** è la classe principale usata per gestire i servizi di Azure.

### Certificati di gestione in Windows (MakeCert)

È possibile creare nel computer un certificato di gestione autofirmato usando `makecert.exe`. Aprire un **prompt dei comandi di Visual Studio** come **amministratore** e usare il comando seguente, sostituendo *AzureCertificate* con il nome del certificato che si desidera usare.

    makecert -sky exchange -r -n "CN=AzureCertificate" -pe -a sha1 -len 2048 -ss My "AzureCertificate.cer"

Il comando permette di creare il file `.cer` e di installarlo nell'archivio certificati **Personale**. Per altre informazioni, vedere [Panoramica sui certificati per i servizi cloud di Azure](./cloud-services-certs-create.md).

Dopo avere creato il certificato sarà necessario caricare il file `.cer` in Azure selezionando l'opzione "Carica" della scheda "Impostazioni" del [portale di Azure classico][management-portal].

Dopo avere ottenuto l'ID sottoscrizione, avere creato un certificato e avere caricato il file `.cer` in Azure, è possibile connettersi all'endpoint di gestione di Azure passando l'ID sottoscrizione e il percorso del certificato nell'archivio certificati **Personale** a **ServiceManagementService**. Anche in questo caso, sostituire *AzureCertificate* con il nome del proprio certificato:

	from azure import *
	from azure.servicemanagement import *

	subscription_id = '<your_subscription_id>'
	certificate_path = 'CURRENT_USER\\my\\AzureCertificate'

	sms = ServiceManagementService(subscription_id, certificate_path)

Nell'esempio sopra riportato `sms` è un oggetto **ServiceManagementService**. La classe **ServiceManagementService** è la classe principale usata per gestire i servizi di Azure.

## <a name="ListAvailableLocations"> </a>Procedura: Creare un elenco delle località disponibili

Per elencare le località disponibili per i servizi di hosting, usare il metodo **list\_locations**:

	from azure import *
	from azure.servicemanagement import *

	sms = ServiceManagementService(subscription_id, certificate_path)

	result = sms.list_locations()
	for location in result:
		print(location.name)

Quando si crea un servizio cloud o un servizio di archiviazione, è necessario fornire una località valida. Il metodo **list\_locations** restituirà sempre un elenco aggiornato delle località attualmente disponibili. Al momento della stesura di questo articolo, le località disponibili sono le seguenti:

- Europa occidentale
- Europa settentrionale
- Asia sudorientale
- Asia orientale
- Stati Uniti centrali
- Stati Uniti centro-settentrionali
- Stati Uniti centro-meridionali
- Stati Uniti occidentali
- Stati Uniti Orientali
- Giappone orientale
- Giappone occidentale
- Brasile meridionale
- Australia orientale
- Australia sudorientale

## <a name="CreateCloudService"> </a>Procedura: Creare un servizio cloud

Quando si crea un'applicazione e la si esegue in Azure, la combinazione del codice e della configurazione costituisce il cosiddetto [servizio cloud] di Azure (noto come *servizio ospitato* in versioni precedenti di Azure). Il metodo **create\_hosted\_service** consente di creare un nuovo servizio ospitato specificando un nome di servizio ospitato (che deve essere univoco in Azure), un'etichetta (con codifica Base 64 automatica), una descrizione e una località.

	from azure import *
	from azure.servicemanagement import *

	sms = ServiceManagementService(subscription_id, certificate_path)

	name = 'myhostedservice'
	label = 'myhostedservice'
	desc = 'my hosted service'
	location = 'West US'

	sms.create_hosted_service(name, label, desc, location)

Per elencare tutti i servizi ospitati per la sottoscrizione, è possibile usare il metodo **list\_hosted\_services**:

	result = sms.list_hosted_services()

	for hosted_service in result:
		print('Service name: ' + hosted_service.service_name)
		print('Management URL: ' + hosted_service.url)
		print('Location: ' + hosted_service.hosted_service_properties.location)
		print('')

Per ottenere eventuali informazioni su un particolare servizio ospitato, passare il nome del servizio ospitato al metodo **get\_hosted\_service\_properties**:

	hosted_service = sms.get_hosted_service_properties('myhostedservice')

	print('Service name: ' + hosted_service.service_name)
	print('Management URL: ' + hosted_service.url)
	print('Location: ' + hosted_service.hosted_service_properties.location)

Dopo avere creato un servizio cloud è possibile distribuire il codice al servizio con il metodo **create\_deployment**.

## <a name="DeleteCloudService"> </a>Procedura: Eliminare un servizio cloud

È possibile eliminare un servizio cloud passando il nome del servizio al metodo **delete\_hosted\_service**:

	sms.delete_hosted_service('myhostedservice')

Si noti che prima di eliminare un servizio è necessario eliminare tutte le distribuzioni del servizio. Per informazioni dettagliate, vedere [Procedura: Eliminare una distribuzione](#DeleteDeployment).

## <a name="DeleteDeployment"></a>Procedura: Eliminare una distribuzione

Per eliminare una distribuzione, usare il metodo **delete\_deployment**. L'esempio seguente mostra come eliminare una distribuzione denominata `v1`.

	from azure import *
	from azure.servicemanagement import *

	sms = ServiceManagementService(subscription_id, certificate_path)

	sms.delete_deployment('myhostedservice', 'v1')

## <a name="CreateStorageService"> </a>Procedura: Creare un servizio di archiviazione

Un [servizio di archiviazione](../storage/storage-create-storage-account.md) offre l'accesso ai [BLOB](../storage/storage-python-how-to-use-blob-storage.md), alle [tabelle](../storage/storage-python-how-to-use-table-storage.md) e alle [code](../storage/storage-python-how-to-use-queue-storage.md) di Azure. Per creare un servizio di archiviazione, è necessario assegnare al servizio un nome di lunghezza compresa tra 3 e 24 caratteri minuscoli e univoco in Azure, nonché una descrizione, un'etichetta fino a 100 caratteri con codifica in Base64 automatica e una località. Nell'esempio seguente viene illustrato come creare un servizio di archiviazione specificando una località.

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
		print('Location: ' + account.storage_service_properties.location)
		print('')

## <a name="DeleteStorageService"></a>Procedura: Eliminare un servizio di archiviazione

È possibile eliminare un servizio di archiviazione passando il relativo nome al metodo **delete\_storage\_account**. L'eliminazione di un servizio di archiviazione comporta l'eliminazione di tutti i dati archiviati nel servizio (BLOB, tabelle e code).

	from azure import *
	from azure.servicemanagement import *

	sms = ServiceManagementService(subscription_id, certificate_path)

	sms.delete_storage_account('mystorageaccount')

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

In alternativa, è possibile usare il metodo **list\_operating\_system\_families** che consente di raggruppare i sistemi operativi in base alla famiglia:

	result = sms.list_operating_system_families()

	for family in result:
		print('Family: ' + family.label)
		for os in family.operating_systems:
			if os.is_active:
				print('OS: ' + os.label)
				print('Version: ' + os.version)
		print('')

## <a name="CreateVMImage"> </a>Procedura: Creare un'immagine del sistema operativo

Per aggiungere un'immagine del sistema operativo all'archivio di immagini, usare il metodo **add\_os\_image**:

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

Per creare una macchina virtuale, è necessario creare un [servizio cloud](#CreateCloudService) prima di creare la distribuzione della macchina virtuale tramite il metodo **create\_virtual\_machine\_deployment**:

	from azure import *
	from azure.servicemanagement import *

	sms = ServiceManagementService(subscription_id, certificate_path)

	name = 'myvm'
	location = 'West US'

	#Set the location
	sms.create_hosted_service(service_name=name,
		label=name,
		location=location)

	# Name of an os image as returned by list_os_images
	image_name = 'OpenLogic__OpenLogic-CentOS-62-20120531-it-IT-30GB.vhd'

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

##Procedura: Creare una macchina virtuale da un'immagine di macchina virtuale acquisita

Per acquisire un'immagine di VM, è necessario chiamare prima il metodo **capture\_vm\_image**:

	from azure import *
	from azure.servicemanagement import *

	sms = ServiceManagementService(subscription_id, certificate_path)

	# replace the below three parameters with actual values
	hosted_service_name = 'hs1'
	deployment_name = 'dep1'
	vm_name = 'vm1'

	image_name = vm_name + 'image'
	image = CaptureRoleAsVMImage	('Specialized',
		image_name,
		image_name + 'label',
		image_name + 'description',
		'english',
		'mygroup')

	result = sms.capture_vm_image(
			hosted_service_name,
			deployment_name,
			vm_name,
			image
		)

Per verificare quindi di avere acquisito l'immagine correttamente, usare l'API **list\_vm\_images** e accertarsi che l'immagine venga visualizzata nei risultati:

	images = sms.list_vm_images()

Per creare infine la macchina virtuale con l'immagine acquisita, usare il metodo **create\_virtual\_machine\_deployment** come fatto in precedenza, ma questa volta passare vm\_image\_name.

	from azure import *
	from azure.servicemanagement import *

	sms = ServiceManagementService(subscription_id, certificate_path)

	name = 'myvm'
	location = 'West US'

	#Set the location
	sms.create_hosted_service(service_name=name,
		label=name,
		location=location)

	sms.create_virtual_machine_deployment(service_name=name,
		deployment_name=name,
		deployment_slot='production',
		label=name,
		role_name=name,
		system_config=linux_config,
		os_virtual_hard_disk=None,
		role_size='Small',
		vm_image_name = image_name)

Per altre informazioni su come acquisire una macchina virtuale Linux, vedere [Come acquisire una macchina virtuale Linux.](../virtual-machines/virtual-machines-linux-capture-image.md)

Per altre informazioni su come acquisire una macchina virtuale Windows, vedere [Come acquisire una macchina virtuale Windows.](../virtual-machines/virtual-machines-capture-image-windows-server.md)

## <a name="What's Next"> </a>Passaggi successivi

A questo punto, dopo aver appreso le nozioni di base della gestione dei servizi, è possibile accedere alla [documentazione completa di riferimento all'API per Azure Python SDK](http://azure-sdk-for-python.readthedocs.org/) ed eseguire facilmente le complesse attività di gestione dell'applicazione Python.

Per ulteriori informazioni, vedere il [Centro per sviluppatori di Python](/develop/python/).

[What is Service Management]: #WhatIs
[Concepts]: #Concepts
[How to: Connect to service management]: #Connect
[How to: List available locations]: #ListAvailableLocations
[How to: Create a cloud service]: #CreateCloudService
[How to: Delete a cloud service]: #DeleteCloudService
[How to: Create a deployment]: #CreateDeployment
[How to: Update a deployment]: #UpdateDeployment
[How to: Move deployments between staging and production]: #MoveDeployments
[How to: Delete a deployment]: #DeleteDeployment
[How to: Create a storage service]: #CreateStorageService
[How to: Delete a storage service]: #DeleteStorageService
[How to: List available operating systems]: #ListOperatingSystems
[How to: Create an operating system image]: #CreateVMImage
[How to: Delete an operating system image]: #DeleteVMImage
[How to: Create a virtual machine]: #CreateVM
[How to: Delete a virtual machine]: #DeleteVM
[Next Steps]: #NextSteps
[management-portal]: https://manage.windowsazure.com/
[svc-mgmt-rest-api]: http://msdn.microsoft.com/library/windowsazure/ee460799.aspx


[servizio cloud]: https://azure.microsoft.com/it-IT/documentation/services/cloud-services/

<!---HONumber=AcomDC_0128_2016-->