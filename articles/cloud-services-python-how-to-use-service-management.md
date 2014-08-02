<properties linkid="develop-python-service-management" urlDisplayName="Service Management" pageTitle="How to use the service management API (Python) - feature guide" metaKeywords="" description="Learn how to programmatically perform common service management tasks from Python." metaCanonical="" services="cloud-services" documentationCenter="Python" title="How to use Service Management from Python" authors="" solutions="" manager="" editor="" />

Come utilizzare la gestione dei servizi da Python
=================================================

In questa guida verrà descritto come eseguire attività comuni di gestione dei servizi a livello di codice da Python. La classe **ServiceManagementService** disponibile in [Azure SDK per Python](https://www.windowsazure.com/en-us/develop/python/common-tasks/install-python/) supporta l'accesso a livello di codice alla maggior parte delle funzionalità di gestione dei servizi disponibili tramite il [portale di gestione](https://manage.windowsazure.com/), ad esempio **creazione, aggiornamento ed eliminazione dei servizi cloud, distribuzioni, servizi di gestione dati, macchine virtuali e gruppi di affinità**. Questa funzionalità può rivelarsi utile nella creazione di applicazioni che richiedono accesso a livello di codice alla gestione dei servizi.

Sommario
--------

-   [Informazioni sulla gestione dei servizi](#WhatIs)
-   [Concetti](#Concepts)
-   [Procedura: Connettersi alla gestione dei servizi](#Connect)
-   [Procedura: Creare un elenco delle località disponibili](#ListAvailableLocations)
-   [Procedura: Creare un servizio cloud](#CreateCloudService)
-   [Procedura: Eliminare un servizio cloud](#DeleteCloudService)
-   [Procedura: Creare una distribuzione](#CreateDeployment)
-   [Procedura: Aggiornare una distribuzione](#UpdateDeployment)
-   [Procedura: Spostare distribuzioni tra gestione temporanea e produzione](#MoveDeployments)
-   [Procedura: Eliminare una distribuzione](#DeleteDeployment)
-   [Procedura: Creare un servizio di archiviazione](#CreateStorageService)
-   [Procedura: Eliminare un servizio di archiviazione](#DeleteStorageService)
-   [Procedura: Creare un gruppo di affinità](#CreateAffinityGroup)
-   [Procedura: Eliminare un gruppo di affinità](#DeleteAffinityGroup)
-   [Procedura: Elencare i sistemi operativi disponibili](#ListOperatingSystems)
-   [Procedura: Creare un'immagine del sistema operativo](#CreateVMImage)
-   [Procedura: Eliminare un'immagine del sistema operativo](#DeleteVMImage)
-   [Procedura: Creare una macchina virtuale](#CreateVM)
-   [Procedura: Eliminare una macchina virtuale](#DeleteVM)
-   [Passaggi successivi](#NextSteps)

Informazioni sulla gestione dei servizi
---------------------------------------

L'API di gestione dei servizi fornisce l'accesso a livello di codice alla maggior parte delle funzionalità di gestione dei servizi disponibili tramite il [portale di gestione](https://manage.windowsazure.com/). Azure SDK per Python consente di gestire i servizi cloud, gli account di archiviazione e i gruppi di affinità.

Per utilizzare l'API di gestione dei servizi, sarà necessario [creare un account Azure](http://www.windowsazure.com/en-us/pricing/free-trial/).

Concetti
--------

Azure SDK per Python include l'[API di Gestione servizi di Azure](http://msdn.microsoft.com/en-us/library/windowsazure/ee460799.aspx), ovvero un'API REST. Tutte le operazioni dell'API vengono eseguite tramite SSL e autenticate reciprocamente con certificati X.509 v3. Il servizio di gestione è accessibile da un servizio in esecuzione in Azure o direttamente tramite Internet da qualsiasi applicazione in grado di inviare una richiesta HTTPS e ricevere una risposta HTTPS.

Procedura: Connettersi alla gestione dei servizi
------------------------------------------------

Per connettersi all'endpoint di gestione dei servizi, sono necessari un ID sottoscrizione di Azure e un certificato di gestione valido. È possibile ottenere l'ID sottoscrizione tramite il [portale di gestione](https://manage.windowsazure.com/).

### Certificati di gestione in Windows

È possibile creare nel computer un certificato di gestione autofirmato utilizzando `makecert.exe`. Aprire un **prompt dei comandi di Visual Studio** come **amministratore** e utilizzare il comando seguente, sostituendo *AzureCertificate* con il nome del certificato che si desidera utilizzare.

    makecert -sky exchange -r -n "CN=AzureCertificate" -pe -a sha1 -len 2048 -ss My "AzureCertificate.cer"

Il comando consente di creare il file `CER` e di installarlo nell'archivio certificati **Personal**. Per ulteriori informazioni, vedere [Creare e caricare un certificato di gestione per Azure](http://msdn.microsoft.com/en-us/library/windowsazure/gg551722.aspx).

Dopo avere creato il certificato sarà necessario caricare il file `CER` in Azure selezionando l'opzione "Upload" della scheda "Settings" del [portale di gestione](https://manage.windowsazure.com/).

Dopo avere ottenuto l'ID sottoscrizione, avere creato un certificato e avere caricato il file `CER` in Azure è possibile connettersi all'endpoint di gestione di Azure passando a **ServiceManagementService** l'ID sottoscrizione e il percorso del certificato nell'archivio certificati **Personal**. Anche in questo caso, sostituire *AzureCertificate* con il nome del proprio certificato:

    from azure import *
    from azure.servicemanagement import *

    subscription_id = '<your_subscription_id>'
    certificate_path = 'CURRENT_USER\my\AzureCertificate'

    sms = ServiceManagementService(subscription_id, certificate_path)

Nell'esempio sopra riportato `sms` è un oggetto **ServiceManagementService**. La classe **ServiceManagementService** è la classe principale utilizzata per gestire i servizi di Azure.

### Certificati di gestione in Mac/Linux

Per creare il certificato di gestione, è possibile utilizzare [OpenSSL](http://www.openssl.org/). È in realtà necessario creare due certificati, uno per il server (un file `CER`) e uno per il client (un file `PEM`). Per creare il file `PEM`, eseguire il comando seguente:

    `openssl req -x509 -nodes -days 365 -newkey rsa:1024 -keyout mycert.pem -out mycert.pem`

Per creare il certificato `CER`, eseguire il comando seguente:

    `openssl x509 -inform pem -in mycert.pem -outform der -out mycert.cer`

Per ulteriori informazioni sui certificati Azure, vedere [Gestione dei certificati in Azure](http://msdn.microsoft.com/en-us/library/windowsazure/gg981929.aspx). Per una descrizione completa dei parametri OpenSSL, vedere la documentazione disponibile all'indirizzo <http://www.openssl.org/docs/apps/openssl.html>.

Dopo avere creato questi file sarà necessario caricare il file `CER` in Azure selezionando l'opzione "Upload" della scheda "Settings" del [portale di gestione](https://manage.windowsazure.com/). Sarà inoltre necessario prendere nota del percorso di salvataggio del file `PEM`.

Dopo avere ottenuto l'ID sottoscrizione, avere creato un certificato e avere caricato il file `CER` in Azure è possibile connettersi all'endpoint di gestione di Azure passando a **ServiceManagementService** l'ID sottoscrizione e il percorso del file `PEM`:

    from azure import *
    from azure.servicemanagement import *

    subscription_id = '<your_subscription_id>'
    certificate_path = '<path_to_.pem_certificate>'

    sms = ServiceManagementService(subscription_id, certificate_path)

Nell'esempio sopra riportato `sms` è un oggetto **ServiceManagementService**. La classe **ServiceManagementService** è la classe principale utilizzata per gestire i servizi di Azure.

Procedura: Creare un elenco delle località disponibili
------------------------------------------------------

Per elencare le località disponibili per i servizi di hosting, utilizzare il metodo **list\_locations**:

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    result = sms.list_locations()
    for location in result:
        print(location.name)

Quando si crea un servizio cloud, un servizio di archiviazione o un gruppo di affinità, è necessario fornire una località valida. Il metodo **list\_locations** restituirà sempre un elenco aggiornato delle località attualmente disponibili. Al momento della stesura di questo articolo, le località disponibili sono le seguenti:

-   Europa occidentale
-   Asia sudorientale
-   Asia orientale
-   Stati Uniti centro-settentrionali
-   Europa settentrionale
-   Stati Uniti centro-meridionali
-   Stati Uniti occidentali
-   Stati Uniti orientali

Procedura: Creare un servizio cloud
-----------------------------------

Quando si crea un'applicazione e la si esegue in Azure, la combinazione del codice e della configurazione costituisce il cosiddetto [servizio cloud](http://windowsazure.com/en-us/documentation/articles/cloud-services-what-is) di Azure (noto come *servizio ospitato* in versioni precedenti di Azure). Il metodo **create\_hosted\_service** consente di creare un nuovo servizio ospitato specificando un nome di servizio ospitato (che deve essere univoco in Azure), un'etichetta (con codifica Base 64 automatica), una descrizione e una località. È possibile specificare un gruppo di affinità anziché una località per il servizio.

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    name = 'myhostedservice'
    label = 'myhostedservice'
    desc = 'my hosted service'
    location = 'West US'

    # You can either set the location or an affinity_group
    sms.create_hosted_service(name, label, desc, location)

Per elencare tutti i servizi ospitati per la sottoscrizione, è possibile utilizzare il metodo **list\_hosted\_services**:

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

Procedura: Eliminare un servizio cloud
--------------------------------------

È possibile eliminare un servizio cloud passando il nome del servizio al metodo **delete\_hosted\_service**:

    sms.delete_hosted_service('myhostedservice')

Si noti che prima di eliminare un servizio è necessario eliminare tutte le distribuzioni del servizio. Per informazioni dettagliate, vedere [Procedura: Eliminare una distribuzione](#DeleteDeployment).

Procedura: Creare una distribuzione
-----------------------------------

Il metodo **create\_Deployment** consente di caricare un nuovo [pacchetto del servizio](http://msdn.microsoft.com/en-us/library/windowsazure/jj155995.aspx) e di creare una nuova distribuzione nell'ambiente di gestione temporanea o di produzione. I parametri per questo metodo sono i seguenti:

-   **name**: nome del servizio ospitato.
-   **deployment\_name**: nome della distribuzione.
-   **slot**: stringa indicante lo slot di `gestione temporanea` o di `produzione`.
-   **package\_url**: URL del pacchetto di distribuzione (file con estensione cspgk). Il file del pacchetto deve essere archiviato in un account di archiviazione BLOB di Azure nella stessa sottoscrizione del servizio ospitato nel quale viene caricato il pacchetto. È possibile creare un pacchetto di distribuzione con i [cmdlet di Azure PowerShell](https://www.windowsazure.com/en-us/develop/php/how-to-guides/powershell-cmdlets/) oppure con lo [strumento da riga di comando cspack](http://msdn.microsoft.com/en-us/library/windowsazure/gg432988.aspx).
-   **configuration**: file di configurazione del servizio (con estensione cscfg) con codifica Base 64.
-   **label**: etichetta del nome del servizio ospitato (con codifica Base 64 automatica).

Nell'esempio seguente viene creata una nuova distribuzione `v1` per un servizio ospitato denominato `myhostedservice`:

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

È possibile accedere alle proprietà di distribuzione con il metodo **get\_deployment\_by\_slot** o **get\_deployment\_by\_name**. Nell'esempio seguente viene recuperata una distribuzione specificando lo slot di distribuzione. Nell'esempio viene eseguita l'iterazione in tutte le istanze per la distribuzione:

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

Procedura: Aggiornare una distribuzione
---------------------------------------

È possibile aggiornare una distribuzione utilizzando il metodo **change\_deployment\_configuration** o **update\_deployment\_status**.

Il metodo **change\_deployment\_configuration** consente di caricare un nuovo file di configurazione del servizio (`cscfg`). Ciò comporterà la modifica di tutte le impostazioni del servizio desiderate, incluso il numero di istanze in una distribuzione. Per ulteriori informazioni, vedere [Schema di configurazione dei servizi di Azure (con estensione cscfg)](http://msdn.microsoft.com/en-us/library/windowsazure/ee758710.aspx). Nell'esempio seguente viene illustrato come caricare un nuovo file di configurazione del servizio:

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

Il metodo **update\_deployment\_status** consente di impostare uno stato di distribuzione su RUNNING o SUSPENDED. Nell'esempio seguente viene illustrato come impostare su RUNNING lo stato di una distribuzione denominata `v1` di un servizio ospitato denominato `myhostedservice`:

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    name = 'myhostedservice'
    deployment_name = 'v1'

    result = update_deployment_status(name, deployment_name, 'Running')

Procedura: Spostare distribuzioni tra gestione temporanea e produzione
----------------------------------------------------------------------

Azure offre due ambienti di distribuzione: gestione temporanea e produzione. In genere un servizio viene distribuito nell'ambiente di gestione temporanea per testarlo prima di distribuirlo nell'ambiente di produzione. L'operazione di innalzamento di livello del servizio dall'ambiente di gestione temporanea a quello di produzione può essere effettuata senza ridistribuire il servizio, ma semplicemente scambiando le distribuzioni. Per ulteriori informazioni sullo scambio di distribuzioni, vedere [Gestire le distribuzioni in Azure](http://msdn.microsoft.com/en-us/library/windowsazure/gg433027.aspx).

Nell'esempio seguente viene illustrato come utilizzare il metodo **swap\_deployment** per scambiare due distribuzioni i cui nomi sono `v1` e `v2`. Nell'esempio, prima di chiamare il metodo **swap\_deployment**, la distribuzione `v1` si trova nello slot di produzione, mentre la distribuzione `v2` si trova nello slot di gestione temporanea. Dopo la chiamata a **swap\_deployment** la distribuzione `v2` risulterà in produzione e la distribuzione `v1` in gestione temporanea.

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    result = sms.swap_deployment('myhostedservice', 'v1', 'v2')

Procedura: Eliminare una distribuzione
--------------------------------------

Per eliminare una distribuzione, utilizzare il metodo **delete\_deployment**. Nell'esempio seguente viene illustrato come eliminare una distribuzione denominata `v1`.

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    sms.delete_deployment('myhostedservice', 'v1')

Procedura: Creare un servizio di archiviazione
----------------------------------------------

Un [servizio di archiviazione](https://www.windowsazure.com/en-us/manage/services/storage/what-is-a-storage-account/) offre l'accesso ai [BLOB](https://www.windowsazure.com/en-us/develop/python/how-to-guides/blob-service/), alle [tabelle](https://www.windowsazure.com/en-us/develop/python/how-to-guides/table-service/) e alle [code](https://www.windowsazure.com/en-us/develop/python/how-to-guides/queue-service/) di Azure. Per creare un servizio di archiviazione, è necessario assegnare al servizio un nome di lunghezza compresa tra 3 e 24 caratteri minuscoli e univoco in Azure, nonché una descrizione, un'etichetta fino a 100 caratteri con codifica in Base 64 automatica, e infine una località o un gruppo di affinità. Nell'esempio seguente viene illustrato come creare un servizio di archiviazione specificando una località. Se si desidera utilizzare un gruppo di affinità, è necessario prima crearlo (vedere [Procedura: Creare un gruppo di affinità](#CreateAffinityGroup)) e quindi configurarlo con il parametro **affinity\_group**.

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

Procedura: Eliminare un servizio di archiviazione
-------------------------------------------------

È possibile eliminare un servizio di archiviazione passando il relativo nome al metodo **delete\_storage\_account**. L'eliminazione di un servizio di archiviazione comporta l'eliminazione di tutti i dati archiviati nel servizio (BLOB, tabelle e code).

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    sms.delete_storage_account('mystorageaccount')

Procedura: Creare un gruppo di affinità
---------------------------------------

Un gruppo di affinità è un raggruppamento logico di servizi di Azure che consente di individuare i servizi per ottimizzare le prestazioni. È ad esempio possibile creare un gruppo di affinità nella località "West US" e quindi creare un [servizio cloud](#CreateCloudService) in tale gruppo. Se successivamente si crea un servizio di archiviazione nello stesso gruppo di affinità, questo verrà inserito nella località "West US" e ottimizzato per il data center al fine di garantire le migliori prestazioni con i servizi cloud nello stesso gruppo di affinità.

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

Procedura: Eliminare un gruppo di affinità
------------------------------------------

È possibile eliminare un gruppo di affinità passando il relativo nome al metodo **delete\_affinity\_group**. Si noti che per eliminare un gruppo di affinità, è prima necessario annullare la relativa associazione da qualsiasi servizio oppure eliminare i servizi che lo utilizzano.

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    sms.delete_affinity_group('myAffinityGroup')

Procedura: Elencare i sistemi operativi disponibili
---------------------------------------------------

Per elencare i sistemi operativi disponibili per i servizi di hosting, utilizzare il metodo **list\_operating\_systems**:

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    result = sms.list_operating_systems()

    for os in result:
        print('OS: ' + os.label)
        print('Family: ' + os.family_label)
        print('Active: ' + str(os.is_active))

In alternativa, è possibile utilizzare il metodo **list\_operating\_system\_families** che consente di raggruppare i sistemi operativi in base alla famiglia:

    result = sms.list_operating_system_families()

    for family in result:
        print('Family: ' + family.label)
        for os in family.operating_systems:
            if os.is_active:
                print('OS: ' + os.label)
                print('Version: ' + os.version)
        print('')

Procedura: Creare un'immagine del sistema operativo
---------------------------------------------------

Per aggiungere un'immagine del sistema operativo all'archivio di immagini, utilizzare il metodo **add\_os\_image**:

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

Per elencare le immagini del sistema operativo disponibili, utilizzare il metodo **list\_os\_images**. Verranno incluse tutte le immagini di piattaforma e utente:

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

Procedura: Eliminare un'immagine del sistema operativo
------------------------------------------------------

Per eliminare un'immagine dell'utente, utilizzare il metodo **delete\_os\_image**:

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    result = sms.delete_os_image('mycentos')

    operation_result = sms.get_operation_status(result.request_id)
    print('Operation status: ' + operation_result.status)

Procedura: Creare una macchina virtuale
---------------------------------------

Per creare una macchina virtuale, è necessario creare un [servizio cloud](#CreateCloudService) prima di creare la distribuzione della macchina virtuale tramite il metodo **create\_virtual\_machine\_deployment**:

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
    image_name = 'OpenLogic__OpenLogic-CentOS-62-20120531-en-us-30GB.vhd'

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

Procedura: Eliminare una macchina virtuale
------------------------------------------

Per eliminare una macchina virtuale, è prima necessario eliminare la distribuzione tramite il metodo **delete\_deployment**:

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    sms.delete_deployment(service_name='myvm',
        deployment_name='myvm')

Sarà quindi possibile eliminare il servizio cloud utilizzando il metodo **delete\_hosted\_service**:

    sms.delete_hosted_service(service_name='myvm')

Passaggi successivi
-------------------

A questo punto, dopo aver appreso le nozioni di base della gestione dei servizi, visitare i collegamenti seguenti per informazioni su come eseguire attività più complesse.

-   Riferimento in MSDN: [Servizi cloud](http://msdn.microsoft.com/en-us/library/windowsazure/jj155995.aspx)
-   Riferimento in MSDN: [Macchine virtuali](http://msdn.microsoft.com/en-us/library/windowsazure/jj156003.aspx)

