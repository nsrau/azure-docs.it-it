<properties linkid="develop-php-how-to-guides-service-management" urlDisplayName="Service Management" pageTitle="How to use Azure service management APIs (PHP)" metaKeywords="" description="Learn how to use the Azure PHP Service Management APIs to manage cloud services and other Azure applications." metaCanonical="" services="" documentationCenter="PHP" title="How to use Service Management from PHP" authors="waltpo" solutions="" manager="bjsmith" editor="mollybos" videoId="" scriptId="" />

Come utilizzare la gestione dei servizi da PHP
==============================================

In questa guida verrà descritto come eseguire attività comuni di gestione dei servizi a livello di codice da PHP. La classe [ServiceManagementRestProxy](https://github.com/WindowsAzure/azure-sdk-for-php/blob/master/WindowsAzure/ServiceManagement/ServiceManagementRestProxy.php) disponibile in [Azure SDK per PHP](../php-download-sdk/) supporta l'accesso a livello di codice alla maggior parte delle funzionalità di gestione dei servizi disponibili tramite il [portale di gestione](https://manage.windowsazure.com/), ad esempio **creazione, aggiornamento ed eliminazione dei servizi cloud, distribuzioni, servizi di archiviazione e gruppi di affinità**. Questa funzionalità può rivelarsi utile nella creazione di applicazioni che richiedono accesso a livello di codice alla gestione dei servizi.

Sommario
--------

-   [Informazioni sulla gestione dei servizi](#WhatIs)
-   [Concetti](#Concepts)
-   [Creazione di un'applicazione PHP](#CreateApplication)
-   [Acquisizione delle librerie client di Azure](#GetClientLibraries)
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

Informazioni sulla gestione dei servizi
---------------------------------------

L'API di gestione dei servizi fornisce l'accesso a livello di codice alla maggior parte delle funzionalità di gestione dei servizi disponibili tramite il [portale di gestione](https://manage.windowsazure.com/). Azure SDK per PHP consente di gestire i servizi cloud, gli account di archiviazione e i gruppi di affinità.

Per utilizzare l'API di gestione dei servizi, sarà necessario [creare un account Azure](/en-us/pricing/free-trial/).

Concetti
--------

Azure SDK per PHP include l'[API di Gestione servizi di Azure](http://msdn.microsoft.com/it-it/library/windowsazure/ee460799.aspx), ovvero un'API REST. Tutte le operazioni dell'API vengono eseguite tramite SSL e autenticate reciprocamente con certificati X.509 v3. Il servizio di gestione è accessibile da un servizio in esecuzione in Azure o direttamente tramite Internet da qualsiasi applicazione in grado di inviare una richiesta HTTPS e ricevere una risposta HTTPS.

Creazione di un'applicazione PHP
--------------------------------

Per creare un'applicazione PHP che utilizza Gestione servizi di Azure, è sufficiente fare riferimento alle classi disponibili in Azure SDK per PHP dall'interno del codice. Per creare l'applicazione, è possibile utilizzare qualsiasi strumento di sviluppo, incluso il Blocco note.

In questa guida si utilizzeranno le funzionalità del servizio che possono essere chiamate in un'applicazione PHP in locale o nel codice in esecuzione in un ruolo Web, in un ruolo di lavoro o in un sito Web di Azure.

Acquisizione delle librerie client di Azure
-------------------------------------------

[WACOM.INCLUDE [get-client-libraries](../includes/get-client-libraries.md)]

Procedura: Connettersi alla gestione dei servizi
------------------------------------------------

Per connettersi all'endpoint di gestione dei servizi, sono necessari un ID sottoscrizione di Azure e il percorso di un certificato di gestione valido. È possibile ottenere l'ID sottoscrizione tramite il [portale di gestione](https://manage.windowsazure.com/), nonché creare certificati di gestione in diversi modi. In questa guida viene utilizzato [OpenSSL](http://www.openssl.org/), che è possibile [scaricare nella versione per Windows](http://www.openssl.org/related/binaries.html) ed eseguire in una console.

È in realtà necessario creare due certificati, uno per il server (un file `CER`) e uno per il client (un file `PEM`). Per creare il file `PEM`, eseguire il comando seguente:

    `openssl req -x509 -nodes -days 365 -newkey rsa:1024 -keyout mycert.pem -out mycert.pem`

Per creare il certificato `CER`, eseguire il comando seguente:

    `openssl x509 -inform pem -in mycert.pem -outform der -out mycert.cer`

Per ulteriori informazioni sui certificati Azure, vedere la pagina relativa alla [panoramica dei certificati in Azure](http://msdn.microsoft.com/it-it/library/windowsazure/gg981935.aspx). Per una descrizione completa dei parametri OpenSSL, vedere la documentazione disponibile all'indirizzo <http://www.openssl.org/docs/apps/openssl.html>.

Se il file delle impostazioni di pubblicazione è stato scaricato e importato tramite gli [strumenti da riga di comando di Azure](../command-line-tools/), è possibile utilizzare il file `PEM` creato dagli strumenti anziché crearne uno personalizzato. Il file `CER` creato dagli strumenti viene caricato in Azure e contestualmente il file `PEM` viene inserito nella sottodirectory `.azure` della directory utente del computer.

Dopo aver creato questi file, sarà necessario caricare il file `CER` in Azure tramite il [portale di gestione](https://manage.windowsazure.com/), nonché prendere nota del percorso in cui è stato salvato il file `PEM`.

Dopo avere ottenuto l'ID sottoscrizione, avere creato un certificato e avere caricato il file `CER` in Azure, è possibile connettersi all'endpoint di gestione di Azure creando una stringa di connessione che verrà passata al metodo **createServiceManagementService** nella classe **ServicesBuilder**:

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;

    $conn_string = "SubscriptionID=<your_subscription_id>;CertificatePath=<path_to_.pem_certificate>";

    $serviceManagementRestProxy = ServicesBuilder::getInstance()->createServiceManagementService($conn_string);

Nell'esempio precedente `$serviceManagementRestProxy` è un oggetto [ServiceManagementRestProxy](https://github.com/WindowsAzure/azure-sdk-for-php/blob/master/WindowsAzure/ServiceManagement/ServiceManagementRestProxy.php). La **ServiceManagementRestProxy** è la classe principale utilizzata per gestire i servizi di Azure.

Procedura: Creare un elenco delle località disponibili
------------------------------------------------------

Per elencare le località disponibili per i servizi di hosting, utilizzare il metodo **ServiceManagementRestProxy-\>listLocations**:

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\Common\ServiceException;

    try{
        $serviceManagementRestProxy = ServicesBuilder::getInstance()->createServiceManagementService($conn_string);

        $result = $serviceManagementRestProxy->listLocations();

        $locations = $result->getLocations();

        foreach($locations as $location){
              echo $location->getName()."<br />";
        }
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here: 
        // http://msdn.microsoft.com/it-it/library/windowsazure/ee460801
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

Quando si crea un servizio cloud, un servizio di archiviazione o un gruppo di affinità, è necessario fornire una località valida. Il metodo **listlocations** restituirà sempre un elenco aggiornato delle località attualmente disponibili. Al momento della stesura di questo articolo, le località disponibili sono le seguenti:

-   Ovunque negli Stati Uniti
-   Ovunque in Europa
-   Europa occidentale
-   Ovunque in Asia
-   Asia sudorientale
-   Asia orientale
-   Stati Uniti centro-settentrionali
-   Europa settentrionale
-   Stati Uniti centro-meridionali
-   Stati Uniti occidentali
-   Stati Uniti orientali

> [WACOM.NOTE]
> Negli esempi di codice seguenti le località vengono passate ai metodi sotto forma di stringhe. È tuttavia possibile passarle sotto forma di enumerazioni utilizzando la classe `WindowsAzure\ServiceManagement\Models\Locations`. Ad esempio, anziché passare "West US" a un metodo che accetta una località è possibile passare `Locations::WEST_US`.

Procedura: Creare un servizio cloud
-----------------------------------

Quando si crea un'applicazione e la si esegue in Azure, la combinazione del codice e della configurazione costituisce il cosiddetto [servizio cloud](../cloud-services-what-is/) di Azure (noto come *servizio ospitato* in versioni precedenti di Azure). Il metodo **createHostedServices** consente di creare un nuovo servizio ospitato specificando un nome di servizio ospitato (che deve essere univoco in Azure), un'etichetta (nome del servizio ospitato con codifica Base 64) e un oggetto **CreateServiceOptions**. L'oggetto [CreateServiceOptions](https://github.com/WindowsAzure/azure-sdk-for-php/blob/master/WindowsAzure/ServiceManagement/Models/CreateServiceOptions.php) consente di creare la località *o* il gruppo di affinità per il servizio.

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\ServiceManagement\Models\CreateServiceOptions;
    use WindowsAzure\Common\ServiceException;

    try{
        // Create REST proxy.
        $serviceManagementRestProxy = ServicesBuilder::getInstance()->createServiceManagementService($conn_string);
        
        $name = "myhostedservice";
        $label = base64_encode($name);
        $options = new CreateServiceOptions();
        $options->setLocation('West US');
        // Instead of setLocation, you can use setAffinityGroup
        // to set an affinity group.

        $result = $serviceManagementRestProxy->createHostedService($name, $label, $options);
        }
        catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here: 
        // http://msdn.microsoft.com/it-it/library/windowsazure/ee460801
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
        }

Per elencare tutti i servizi ospitati per la sottoscrizione, è possibile utilizzare il metodo **listHostedServices**, che restituisce un oggetto [ListHostedServicesResult](https://github.com/WindowsAzure/azure-sdk-for-php/blob/master/WindowsAzure/ServiceManagement/Models/GetDeploymentOptions.php). La chiamata al metodo **getHostedServices** consente quindi di eseguire il ciclo in una matrice di oggetti [HostedServices] e di recuperare le proprietà dei servizi:

    $listHostedServicesResult = $serviceManagementRestProxy->listHostedServices();

    $hosted_services = $listHostedServicesResult->getHostedServices();

    foreach($hosted_services as $hosted_service){
        echo "Service name: ".$hosted_service->getName()."<br />";
        echo "Management URL: ".$hosted_service->getUrl()."<br />";
        echo "Affinity group: ".$hosted_service->getAffinityGroup()."<br />";
        echo "Location: ".$hosted_service->getLocation()."<br />";
        echo "------<br />";
        }

Per ottenere eventuali informazioni su un particolare servizio ospitato, passare il nome del servizio ospitato al metodo **getHostedServiceProperties**:

    $getHostedServicePropertiesResult = $serviceManagementRestProxy->getHostedServiceProperties("myhostedservice");
        
    $hosted_service = $getHostedServicePropertiesResult->getHostedService();
        
    echo "Service name: ".$hosted_service->getName()."<br />";
    echo "Management URL: ".$hosted_service->getUrl()."<br />";
    echo "Affinity group: ".$hosted_service->getAffinityGroup()."<br />";
    echo "Location: ".$hosted_service->getLocation()."<br />";

Dopo avere creato un servizio cloud è possibile distribuire il codice al servizio con il metodo [createDeployment](#CreateDeployment).

Procedura: Eliminare un servizio cloud
--------------------------------------

È possibile eliminare un servizio cloud passando il nome del servizio al metodo **deleteHostedService**:

    $serviceManagementRestProxy->deleteHostedService("myhostedservice");

Si noti che prima di eliminare un servizio è necessario eliminare tutte le distribuzioni del servizio. Per informazioni dettagliate, vedere [Procedura: Eliminare una distribuzione](#DeleteDeployment).

Procedura: Creare una distribuzione
-----------------------------------

Il metodo **createDeployment** consente di caricare un nuovo [pacchetto del servizio](http://msdn.microsoft.com/it-it/library/windowsazure/gg433093) e di creare una nuova distribuzione nell'ambiente di gestione temporanea o di produzione. I parametri per questo metodo sono i seguenti:

-   **$name**: nome del servizio ospitato.
-   **$deploymentName**: nome della distribuzione.
-   **$slot**: enumerazione indicante lo slot di gestione temporanea o di produzione.
-   **$packageUrl**: URL del pacchetto di distribuzione (file con estensione cspgk). Il file del pacchetto deve essere archiviato in un account di archiviazione BLOB di Azure nella stessa sottoscrizione del servizio ospitato nel quale viene caricato il pacchetto. È possibile creare un pacchetto di distribuzione con i [cmdlet di Azure PowerShell](../install-configure-powershell/) oppure con lo [strumento da riga di comando cspack](http://msdn.microsoft.com/it-it/library/windowsazure/gg432988.aspx).
-   **$configuration**: file di configurazione del servizio con estensione cscfg.
-   **$label**: nome del servizio ospitato con codifica Base 64.

Nell'esempio seguente viene creata una nuova distribuzione nello slot di produzione di un servizio ospitato denominato `myhostedservice`:

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\ServiceManagement\Models\DeploymentSlot;
    use WindowsAzure\Common\ServiceException;

    try{
        // Create REST proxy.
        $serviceManagementRestProxy = ServicesBuilder::getInstance()->createServiceManagementService($conn_string);
        
        $name = "myhostedservice";
        $deploymentName = "v1";
        $slot = DeploymentSlot::PRODUCTION;
        $packageUrl = "URL_for_.cspkg_file";
        $configuration = base64_encode(file_get_contents('path_to_.cscfg_file'));
        $label = base64_encode($name);

        $result = $serviceManagementRestProxy->createDeployment($name,
                                                         $deploymentName,
                                                         $slot,
                                                         $packageUrl,
                                                         $configuration,
                                                         $label);
        
        $status = $serviceManagementRestProxy->getOperationStatus($result);
        echo "Operation status: ".$status->getStatus()."<br />";
        }
        catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here: 
        // http://msdn.microsoft.com/it-it/library/windowsazure/ee460801
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
        }

Si noti nell'esempio precedente che è possibile recuperare lo stato dell'operazione **createDeployment** passando il risultato restituito da **createDeployment** al metodo **getOperationStatus**.

È possibile accedere alle proprietà di distribuzione con il metodo **getDeployment**. Nell'esempio seguente viene recuperata una distribuzione specificando lo slot di distribuzione nell'oggetto [GetDeploymentOptions](https://github.com/WindowsAzure/azure-sdk-for-php/blob/master/WindowsAzure/ServiceManagement/Models/GetDeploymentOptions.php), tuttavia è anche possibile specificare il nome della distribuzione. Nell'esempio viene eseguita l'iterazione in tutte le istanze per la distribuzione:

    $options = new GetDeploymentOptions();
    $options->setSlot(DeploymentSlot::PRODUCTION);
        
    $getDeploymentResult = $serviceManagementRestProxy->getDeployment("myhostedservice", $options);
    $deployment = $getDeploymentResult->getDeployment();

    echo "Name: ".$deployment->getName()."<br />";
    echo "Slot: ".$deployment->getSlot()."<br />";
    echo "Private ID: ".$deployment->getPrivateId()."<br />";
    echo "Status: ".$deployment->getStatus()."<br />";
    echo "Instances: <br />";
    foreach($deployment->getroleInstanceList() as $roleInstance){
        echo "Instance name: ".$roleInstance->getInstanceName()."<br />";
        echo "Instance status: ".$roleInstance->getInstanceStatus()."<br />";
        echo "Instance size: ".$roleInstance->getInstanceSize()."<br />";
        }
        echo "------<br />";

Procedura: Aggiornare una distribuzione
---------------------------------------

È possibile aggiornare una distribuzione utilizzando il metodo **changeDeploymentConfiguration** o **updateDeploymentStatus**.

Il metodo **changeDeploymentConfiguration** consente di caricare un nuovo file di configurazione del servizio (`.cscfg`). Ciò comporterà la modifica di tutte le impostazioni del servizio desiderate, incluso il numero di istanze in una distribuzione. Per ulteriori informazioni, vedere [Schema di configurazione dei servizi di Azure (con estensione cscfg)](http://msdn.microsoft.com/it-it/library/windowsazure/ee758710.aspx). Nell'esempio seguente viene illustrato come caricare un nuovo file di configurazione del servizio:

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\ServiceManagement\Models\ChangeDeploymentConfigurationOptions;
    use WindowsAzure\ServiceManagement\Models\DeploymentSlot;
    use WindowsAzure\Common\ServiceException;

    try{
        // Create REST proxy.
        $serviceManagementRestProxy = ServicesBuilder::getInstance()->createServiceManagementService($conn_string);
        
        $name = "myhostedservice";
        $configuration = base64_encode(file_get_contents('path to .cscfg file'));
        $options = new ChangeDeploymentConfigurationOptions();
        $options->setSlot(DeploymentSlot::PRODUCTION);

        $result = $serviceManagementRestProxy->changeDeploymentConfiguration($name, $configuration, $options);
        
        $status = $serviceManagementRestProxy->getOperationStatus($result);
        echo "Operation status: ".$status->getStatus()."<br />";
        }
        catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here: 
        // http://msdn.microsoft.com/it-it/library/windowsazure/ee460801
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
        }

Si noti nell'esempio precedente che è possibile recuperare lo stato dell'operazione **changeDeploymentConfiguration** passando il risultato restituito da **changeDeploymentConfiguration** al metodo **getOperationStatus**.

Il metodo **updateDeploymentStatus** consente di impostare uno stato di distribuzione su RUNNING o SUSPENDED. Nell'esempio seguente viene illustrato come impostare su RUNNING lo stato di una distribuzione nello slot di produzione di un servizio ospitato denominato `myhostedservice`:

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\ServiceManagement\Models\DeploymentStatus;
    use WindowsAzure\ServiceManagement\Models\DeploymentSlot;
    use WindowsAzure\ServiceManagement\Models\GetDeploymentOptions;
    use WindowsAzure\Common\ServiceException;

    try{
        // Create REST proxy.
        $serviceManagementRestProxy = ServicesBuilder::getInstance()->createServiceManagementService($conn_string);
        
        $options = new GetDeploymentOptions();
        $options->setSlot(DeploymentSlot::PRODUCTION);
        
        $result = $serviceManagementRestProxy->updateDeploymentStatus("myhostedservice", DeploymentStatus::RUNNING, $options);
        }
        catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here: 
        // http://msdn.microsoft.com/it-it/library/windowsazure/ee460801
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
        }

Procedura: Spostare distribuzioni tra gestione temporanea e produzione
----------------------------------------------------------------------

Azure offre due ambienti di distribuzione: gestione temporanea e produzione. In genere un servizio viene distribuito nell'ambiente di gestione temporanea per testarlo prima di distribuirlo nell'ambiente di produzione. L'operazione di innalzamento di livello del servizio dall'ambiente di gestione temporanea a quello di produzione può essere effettuata senza ridistribuire il servizio, ma semplicemente scambiando le distribuzioni. Per ulteriori informazioni sullo scambio delle distribuzioni, vedere [Panoramica della gestione delle distribuzioni in Azure](http://msdn.microsoft.com/it-it/library/windowsazure/hh386336.aspx).)

Nell'esempio seguente viene illustrato come utilizzare il metodo **swapDeployment** per scambiare due distribuzioni i cui nomi sono `v1` e `v2`. Nell'esempio, prima di chiamare il metodo **swapDeployment**, la distribuzione `v1` si trova nello slot di produzione, mentre la distribuzione `v2` si trova nello slot di gestione temporanea. Dopo la chiamata a **swapDeployment** la distribuzione `v2` risulterà in produzione e la distribuzione `v1` in gestione temporanea.

    require_once 'vendor\autoload.php';  

    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\Common\ServiceException;

    try{
        // Create REST proxy.
        $serviceManagementRestProxy = ServicesBuilder::getInstance()->createServiceManagementService($conn_string);
        
        $result = $serviceManagementRestProxy->swapDeployment("myhostedservice", "v2", "v1");
        }
        catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here: 
        // http://msdn.microsoft.com/it-it/library/windowsazure/ee460801
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
        }

Procedura: Eliminare una distribuzione
--------------------------------------

Per eliminare una distribuzione, utilizzare il metodo **deleteDeployment**. Nell'esempio seguente viene illustrato come eliminare una distribuzione nell'ambiente di gestione temporanea utilizzando il metodo **setSlot** su un oggetto [GetDeploymentOptions](https://github.com/WindowsAzure/azure-sdk-for-php/blob/master/WindowsAzure/ServiceManagement/Models/GetDeploymentOptions.php) e quindi passandolo a **deleteDeployment**. Anziché specificare una distribuzione in base allo slot, è possibile utilizzare il metodo **setName** sulla classe [GetDepolymentOptions] per specificare una distribuzione in base al nome.

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\ServiceManagement\Models\GetDeploymentOptions;
    use WindowsAzure\ServiceManagement\Models\DeploymentSlot;
    use WindowsAzure\Common\ServiceException;

    try{
        // Create REST proxy.
        $serviceManagementRestProxy = ServicesBuilder::getInstance()->createServiceManagementService($conn_string);
        
        $options = new GetDeploymentOptions();
        $options->setSlot(DeploymentSlot::STAGING);
        
        $result = $serviceManagementRestProxy->deleteDeployment("myhostedservice", $options);
        }
        catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here: 
        // http://msdn.microsoft.com/it-it/library/windowsazure/ee460801
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
        }

Procedura: Creare un servizio di archiviazione
----------------------------------------------

Un [servizio di archiviazione](../storage-whatis-account/) offre l'accesso ai [BLOB](../storage-php-how-to-use-blobs/), alle [tabelle](../storage-php-how-to-use-table-storage/) e alle [code](../storage-php-how-to-use-queues/) di Azure. Per creare un servizio di archiviazione, è necessario assegnare al servizio un nome di lunghezza compresa tra 3 e 24 caratteri minuscoli e univoco in Azure, nonché un'etichetta, ovvero un nome con codifica in Base 64 composto da un massimo di 100 caratteri, e infine una località o un gruppo di affinità. Facoltativamente, è anche possibile specificare una descrizione. La località, il gruppo di affinità e la descrizione sono impostate in un oggetto [CreateServiceOptions](https://github.com/WindowsAzure/azure-sdk-for-php/blob/master/WindowsAzure/ServiceManagement/Models/CreateServiceOptions.php), che viene passato al metodo **createStorageService**. Nell'esempio seguente viene illustrato come creare un servizio di archiviazione specificando una località. Se si desidera utilizzare un gruppo di affinità, è necessario prima crearlo (vedere [Procedura: Creare un gruppo di affinità](#CreateAffinityGroup)) e quindi configurarlo con il metodo **CreateServiceOptions-\>setAffinityGroup**.

    require_once 'vendor\autoload.php';
     
    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\ServiceManagement\Models\CreateServiceOptions;
    use WindowsAzure\Common\ServiceException;
     
     
    try{
        // Create REST proxy.
        $serviceManagementRestProxy = ServicesBuilder::getInstance()->createServiceManagementService($conn_string);
        
        $name = "mystorageaccount";
        $label = base64_encode($name);
        $options = new CreateServiceOptions();
        $options->setLocation('West US');
        $options->setDescription("My storage account description.");

        $result = $serviceManagementRestProxy->createStorageService($name, $label, $options);

        $status = $serviceManagementRestProxy->getOperationStatus($result);
        echo "Operation status: ".$status->getStatus()."<br />";
     }
     catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here: 
        // http://msdn.microsoft.com/it-it/library/windowsazure/ee460801
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
     }

Si noti nell'esempio precedente che è possibile recuperare lo stato dell'operazione **createStorageService** passando il risultato restituito da **createStorageService** al metodo **getOperationStatus**.

È possibile elencare gli account di archiviazione e le relative proprietà con il metodo **listStorageServices**:

    // Create REST proxy.
    $serviceManagementRestProxy = ServicesBuilder::getInstance()->createServiceManagementService($conn_string);
    $getStorageServicesResult = $serviceManagementRestProxy->listStorageServices();
    $storageServices = $getStorageServicesResult->getStorageServices();

    foreach($storageServices as $storageService){
        echo "Service name: ".$storageService->getName()."<br />";
        echo "Service URL: ".$storageService->getUrl()."<br />";
        echo "Affinity group: ".$storageService->getAffinityGroup()."<br />";
        echo "Location: ".$storageService->getLocation()."<br />";
        echo "------<br />";
        }

Procedura: Eliminare un servizio di archiviazione
-------------------------------------------------

È possibile eliminare un servizio di archiviazione passando il relativo nome al metodo **deleteStorageService**. L'eliminazione di un servizio di archiviazione comporta l'eliminazione di tutti i dati archiviati nel servizio (BLOB, tabelle e code).

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\Common\ServiceException;

    try{
        // Create REST proxy.
        $serviceManagementRestProxy = ServicesBuilder::getInstance()->createServiceManagementService($conn_string);
        
        $serviceManagementRestProxy->deleteStorageService("mystorageservice");
        }
        catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here: 
        // http://msdn.microsoft.com/it-it/library/windowsazure/ee460801
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
        }

Procedura: Creare un gruppo di affinità
---------------------------------------

Un gruppo di affinità è un raggruppamento logico di servizi di Azure che consente di individuare i servizi per ottimizzare le prestazioni. È ad esempio possibile creare un gruppo di affinità nella località "West US" e quindi creare un [servizio cloud](#CreateCloudService) in tale gruppo. Se successivamente si crea un servizio di archiviazione nello stesso gruppo di affinità, questo verrà inserito nella località "West US" e ottimizzato per il data center al fine di garantire le migliori prestazioni con i servizi cloud nello stesso gruppo di affinità.

Per creare un gruppo di affinità, sono necessari un nome, un'etichetta (con codifica in Base 64) e una località. Facoltativamente, è possibile specificare una descrizione:

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\ServiceManagement\Models\CreateAffinityGroupOptions;
    use WindowsAzure\Common\ServiceException;
     
    try{
        // Create REST proxy.
        $serviceManagementRestProxy = ServicesBuilder::getInstance()->createServiceManagementService($conn_string);
        
        $name = "myAffinityGroup";
        $label = base64_encode($name);
        $location = "West US";

        $options = new CreateAffinityGroupOptions();
        $options->setDescription = "My affinity group description.";
        
        $serviceManagementRestProxy->createAffinityGroup($name, $label, $location, $options);
     }
     catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here: 
        // http://msdn.microsoft.com/it-it/library/windowsazure/ee460801
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
     }

Dopo avere creato un gruppo di affinità è possibile specificare il gruppo (anziché una località) durante la [creazione di un gruppo di archiviazione](#CreateStorageService).

È possibile elencare gruppi di affinità e ispezionarne le proprietà chiamando il metodo **listAffinityGroups** e quindi chiamando i metodi appropriati sulla classe [AffinityGroup](https://github.com/WindowsAzure/azure-sdk-for-php/blob/master/WindowsAzure/ServiceManagement/Models/AffinityGroup.php):

    $result = $serviceManagementRestProxy->listAffinityGroups();

    $groups = $result->getAffinityGroups();

    foreach($groups as $group){
        echo "Name: ".$group->getName()."<br />";
        echo "Description: ".$group->getDescription()."<br />";
        echo "Location: ".$group->getLocation()."<br />";
        echo "------<br />";
        }

Procedura: Eliminare un gruppo di affinità
------------------------------------------

È possibile eliminare un gruppo di affinità passando il relativo nome al metodo **deleteAffinityGroup**. Si noti che per eliminare un gruppo di affinità, è prima necessario annullare la relativa associazione da qualsiasi servizio oppure eliminare i servizi che lo utilizzano.

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\Common\ServiceException;

    try{
        // Create REST proxy.
        $serviceManagementRestProxy = ServicesBuilder::getInstance()->createServiceManagementService($conn_string);
        
        // An affinity group must be disassociated from all services 
        // before it can be deleted.
        $serviceManagementRestProxy->deleteAffinityGroup("myAffinityGroup");
        }
        catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here: 
        // http://msdn.microsoft.com/it-it/library/windowsazure/ee460801
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
        }
