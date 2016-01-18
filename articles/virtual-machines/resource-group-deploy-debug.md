<properties
   pageTitle="Risoluzione dei problemi relativi alle distribuzioni di gruppi di risorse | Microsoft Azure"
   description="Vengono descritti i problemi comuni che possono verificarsi durante la distribuzione di risorse create usando il modello di distribuzione di Gestione risorse e viene illustrato come rilevare e risolvere questi problemi."
   services="azure-resource-manager,virtual-machines"
   documentationCenter=""
   tags="top-support-issue"
   authors="tfitzmac"
   manager="wpickett"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-multiple"
   ms.workload="infrastructure"
   ms.date="01/06/2016"
   ms.author="tomfitz;rasquill"/>

# Risoluzione dei problemi relativi alle distribuzioni di gruppi di risorse in Azure

In caso di problemi durante la distribuzione, è necessario scoprirne le cause. Gestione risorse offre due metodi per scoprire cosa è accaduto e perché. È possibile usare i comandi di distribuzione per recuperare le informazioni relative a determinate distribuzioni per un gruppo di risorse. In alternativa, è possibile usare i log di controllo per recuperare informazioni su tutte le operazioni eseguite su un gruppo di risorse. Con queste informazioni, è possibile risolvere il problema e riprendere le operazioni nella soluzione.

Questo argomento è incentrato principalmente sull'uso dei comandi di distribuzione per risolvere i problemi relativi alle distribuzioni. Per informazioni sull'uso dei log di controllo per tenere traccia di tutte le operazioni eseguire sulle risorse, vedere l'articolo relativo al [controllo delle operazioni con Gestione risorse](../resource-group-audit.md).

Questo argomento spiega come recuperare le informazioni per la risoluzione dei problemi mediante Azure PowerShell, l'interfaccia della riga di comando di Azure e l'API REST. Per informazioni sull'uso del portale di anteprima per risolvere i problemi relativi alle distribuzioni, vedere [Uso del portale di Azure per gestire le risorse di Azure](../azure-portal/resource-group-portal.md).

Questo argomento inoltre illustra le soluzioni relative agli errori comuni che possono verificarsi.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]Modello di distribuzione classica. Non è possibile creare gruppi di risorse con il modello di distribuzione classica.


## Eseguire la risoluzione dei problemi con PowerShell

[AZURE.INCLUDE [powershell-preview-inline-include](../../includes/powershell-preview-inline-include.md)]

Per ottenere lo stato complessivo di una distribuzione, è possibile usare il comando **Get-AzureRmResourceGroupDeployment**. Nell'esempio seguente la distribuzione ha avuto esito negativo.

    PS C:\> Get-AzureRmResourceGroupDeployment -ResourceGroupName ExampleGroup -DeploymentName ExampleDeployment

    DeploymentName    : ExampleDeployment
    ResourceGroupName : ExampleGroup
    ProvisioningState : Failed
    Timestamp         : 8/27/2015 8:03:34 PM
    Mode              : Incremental
    TemplateLink      :
    Parameters        :
                    Name             Type                       Value
                    ===============  =========================  ==========
                    siteName         String                     ExampleSite
                    hostingPlanName  String                     ExamplePlan
                    siteLocation     String                     West US
                    sku              String                     Free
                    workerSize       String                     0

    Outputs           :

Ciascuna distribuzione in genere è costituita da più operazioni, ognuna delle quali rappresenta un passaggio del processo di distribuzione. Per individuare eventuali problemi, solitamente è necessario visualizzare i dettagli relativi alle operazioni di distribuzione. Per visualizzare lo stato delle operazioni, usare il comando **Get-AzureRmResourceGroupDeploymentOperation**.

    PS C:\> Get-AzureRmResourceGroupDeploymentOperation -DeploymentName ExampleDeployment -ResourceGroupName ExampleGroup
    Id                        OperationId          Properties         
    -----------               ----------           -------------
    /subscriptions/xxxxx...   347A111792B648D8     @{ProvisioningState=Failed; Timestam...
    /subscriptions/xxxxx...   699776735EFC3D15     @{ProvisioningState=Succeeded; Times...

Tale comando mostra due operazioni nella distribuzione, una con stato di provisioning Failed e l'altra con stato di provisioning Succeeded.

È possibile recuperare il messaggio di stato con il comando seguente:

    PS C:\> (Get-AzureRmResourceGroupDeploymentOperation -DeploymentName ExampleDeployment -ResourceGroupName ExampleGroup).Properties.StatusMessage

    Code       : Conflict
    Message    : Website with given name mysite already exists.
    Target     :
    Details    : {@{Message=Website with given name mysite already exists.}, @{Code=Conflict}, @{ErrorEntity=}}
    Innererror :

## Eseguire la risoluzione dei problemi con l'interfaccia della riga di comando di Azure

Per ottenere lo stato complessivo di una distribuzione, è possibile usare il comando **azure group deployment show**. Nell'esempio seguente la distribuzione ha avuto esito negativo.

    azure group deployment show ExampleGroup ExampleDeployment

    info:    Executing command group deployment show
    + Getting deployments
    data:    DeploymentName     : ExampleDeployment
    data:    ResourceGroupName  : ExampleGroup
    data:    ProvisioningState  : Failed
    data:    Timestamp          : 2015-08-27T20:03:34.9178576Z
    data:    Mode               : Incremental
    data:    Name             Type    Value
    data:    ---------------  ------  ------------
    data:    siteName         String  ExampleSite
    data:    hostingPlanName  String  ExamplePlan
    data:    siteLocation     String  West US
    data:    sku              String  Free
    data:    workerSize       String  0
    info:    group deployment show command OK


Nei log di controllo è possibile trovare altre informazioni sui motivi per cui la distribuzione non è riuscita. Per visualizzare i log di controllo, eseguire il comando **azure group log show**. Includendo l'opzione **--last-deployment**, è possibile recuperare solo il log relativo alla distribuzione più recente.

    azure group log show ExampleGroup --last-deployment

Il comando **azure group log show** può restituire una notevole quantità di informazioni, ma per la risoluzione dei problemi in genere è necessario concentrarsi sulle operazioni che presentano errori. Lo script seguente usa l'opzione **--json** e **jq** per cercare nel log gli errori di distribuzione. Per informazioni su strumenti come **jq**, vedere [Strumenti utili per interagire con Azure](#useful-tools-to-interact-with-azure).

    azure group log show ExampleGroup --json | jq '.[] | select(.status.value == "Failed")'

    {
      "claims": {
        "aud": "https://management.core.windows.net/",
        "iss": "https://sts.windows.net/<guid>/",
        "iat": "1442510510",
        "nbf": "1442510510",
        "exp": "1442514410",
        "ver": "1.0",
        "http://schemas.microsoft.com/identity/claims/tenantid": "<guid>",
        "http://schemas.microsoft.com/identity/claims/objectidentifier": "<guid>",
        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress": "someone@example.com",
        "puid": "XXXXXXXXXXXXXXXX",
        "http://schemas.microsoft.com/identity/claims/identityprovider": "example.com",

        "altsecid": "1:example.com:XXXXXXXXXXX",
        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier": "<hash string>",
        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname": "Tom",
        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname": "FitzMacken",
        "name": "Tom FitzMacken",
        "http://schemas.microsoft.com/claims/authnmethodsreferences": "pwd",
        "groups": "<guid>",
        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name": "example.com#someone@example.com",
        "wids": "<guid>",
        "appid": "<guid>",
        "appidacr": "0",
        "http://schemas.microsoft.com/identity/claims/scope": "user_impersonation",
        "http://schemas.microsoft.com/claims/authnclassreference": "1",
        "ipaddr": "000.000.000.000"
      },
      "properties": {
        "statusCode": "Conflict",
        "statusMessage": "{"Code":"Conflict","Message":"Website with given name mysite already exists.","Target":null,"Details":[{"Message":"Website with given name
          mysite already exists."},{"Code":"Conflict"},{"ErrorEntity":{"Code":"Conflict","Message":"Website with given name mysite already exists.","ExtendedCode":
          "54001","MessageTemplate":"Website with given name {0} already exists.","Parameters":["mysite"],"InnerErrors":null}}],"Innererror":null}"
      },
    ...

In **properties** sono incluse informazioni in json sull'operazione non riuscita.

È possibile usare le opzioni **--verbose** e **-vv** per visualizzare altre informazioni contenute nei log. L'opzione **--verbose** consente di visualizzare i passaggi eseguiti dall'operazione in `stdout`. L'opzione **-vv** invece consente di visualizzare la cronologia completa delle richieste. I messaggi spesso forniscono indicazioni importanti sulla causa degli errori.

## Eseguire la risoluzione dei problemi con l'API REST

L'API REST di Gestione risorse fornisce URI che consentono di recuperare informazioni su una distribuzione, le relative operazioni e i dettagli riguardanti un'operazione specifica. Per una descrizione completa di questi comandi, vedere:

- [Ottenere informazioni su una distribuzione modello](https://msdn.microsoft.com/library/azure/dn790565.aspx)
- [Elencare tutte le operazioni della distribuzione modello](https://msdn.microsoft.com/library/azure/dn790518.aspx)
- [Ottenere informazioni su un'operazione di distribuzione modello](https://msdn.microsoft.com/library/azure/dn790519.aspx)


## Aggiornamento delle credenziali scadute

La distribuzione avrà esito negativo se le credenziali di Azure sono scadute o non si accede al proprio account Azure. Le credenziali possono scadere se la sessione rimane aperta troppo a lungo. Possono tuttavia essere aggiornate come segue:

- Per PowerShell, usare il cmdlet **Login-AzureRmAccount** (o **Add-AzureAccount** per le versioni di PowerShell precedenti all'anteprima 1.0). Le credenziali di un file di impostazioni di pubblicazione non sono sufficienti per i cmdlet del modulo AzureResourceManager.
- Per l'interfaccia della riga di comando di Azure, usare **azure login**. Per informazioni sugli errori di autenticazione, assicurarsi di avere [configurato correttamente l'interfaccia della riga di comando di Azure](../xplat-cli-connect.md).

## Verifica del formato dei modelli e dei parametri

La distribuzione avrà esito negativo se il file del modello o dei parametri non ha il formato corretto. Prima di eseguire una distribuzione, è pertanto possibile verificare la validità del modello e dei parametri.

### PowerShell

Per PowerShell, usare **Test-AzureRmResourceGroupDeployment** (o **Test-AzureResourceGroupTemplate** per le versioni di PowerShell precedenti all'anteprima 1.0).

    PS C:\> Test-AzureRmResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateFile c:\Azure\Templates\azuredeploy.json -TemplateParameterFile c:\Azure\Templates\azuredeploy.parameters.json
    VERBOSE: 12:55:32 PM - Template is valid.

### Interfaccia della riga di comando di Azure

Per l'interfaccia della riga di comando di Azure, usare **azure group template validate<resource group>**.

Nell'esempio seguente viene illustrato come convalidare un modello e gli eventuali parametri necessari. L’interfaccia della riga di comando di Azure richiede i valori dei parametri necessari.

        azure group template validate \
        > --template-uri "https://contoso.com/templates/azuredeploy.json" \
        > resource-group
        info:    Executing command group template validate
        info:    Supply values for the following parameters
        adminUserName: UserName
        adminPassword: PassWord
        + Initializing template configurations and parameters
        + Validating the template
        info:    group template validate command OK

### API REST

Per l'API REST, vedere [Convalidare una distribuzione modello](https://msdn.microsoft.com/library/azure/dn790547.aspx).

## Verifica dei percorsi che supportano la risorsa

Quando si specifica un percorso per una risorsa, è necessario usare uno dei percorsi in grado di supportarla. Prima di immettere un percorso per una risorsa, usare uno dei comandi seguenti per verificare che il percorso supporti il tipo di risorsa.

### PowerShell

Per le versioni di PowerShell precedenti all'anteprima 1.0, è possibile visualizzare l'elenco completo delle risorse e dei percorsi con il comando **Get-AzureLocation**.

    PS C:\> Get-AzureLocation

    Name                                    Locations                               LocationsString
    ----                                    ---------                               ---------------
    ResourceGroup                           {East Asia, South East Asia, East US... East Asia, South East Asia, East US,...
    Microsoft.ApiManagement/service         {Central US, East US, East US 2, Nor... Central US, East US, East US 2, Nort...
    Microsoft.AppService/apiapps            {East US, West US, South Central US,... East US, West US, South Central US, ...
    ...

È possibile specificare un tipo particolare di risorsa con il codice seguente:

    PS C:\> Get-AzureLocation | Where-Object Name -eq "Microsoft.Compute/virtualMachines" | Format-Table Name, LocationsString -Wrap

    Name                                                        LocationsString
    ----                                                        ---------------
    Microsoft.Compute/virtualMachines                           East US, East US 2, West US, Central US, South Central US,
                                                                North Europe, West Europe, East Asia, Southeast Asia,
                                                                Japan East, Japan West

Per l'anteprima di PowerShell 1.0, usare **Get-AzureRmResourceProvider** per ottenere i percorsi supportati.

    PS C:\> Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web

    ProviderNamespace RegistrationState ResourceTypes               Locations
    ----------------- ----------------- -------------               ---------
    Microsoft.Web     Registered        {sites/extensions}          {Brazil South, ...
    Microsoft.Web     Registered        {sites/slots/extensions}    {Brazil South, ...
    Microsoft.Web     Registered        {sites/instances}           {Brazil South, ...
    ...

È possibile specificare un tipo particolare di risorsa con il codice seguente:

    PS C:\> ((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).Locations

    Brazil South
    East Asia
    East US
    Japan East
    Japan West
    North Central US
    North Europe
    South Central US
    West Europe
    West US
    Southeast Asia

### Interfaccia della riga di comando di Azure

Per l'interfaccia della riga di comando di Azure, è possibile usare **azure location list**. Poiché l'elenco dei percorsi può essere lungo e il numero di provider è elevato, è possibile usare gli strumenti per esaminare i provider e i percorsi prima di usare un percorso non ancora disponibile. Lo script seguente usa **jq** per individuare i percorsi in cui è disponibile il provider di risorse per le macchine virtuali di Azure.

    azure location list --json | jq '.[] | select(.name == "Microsoft.Compute/virtualMachines")'
    {
      "name": "Microsoft.Compute/virtualMachines",
      "location": "East US,East US 2,West US,Central US,South Central US,North Europe,West Europe,East Asia,Southeast Asia,Japan East,Japan West"
    }

### API REST

Per l'API REST, vedere [Ottenere informazioni su un provider di risorse](https://msdn.microsoft.com/library/azure/dn790534.aspx).

## Creazione di nomi univoci per le risorse

Per alcune risorse, in particolare gli account di archiviazione, i server di database e i siti Web, è necessario specificare un nome che sia univoco all'interno di Azure. Attualmente non è possibile verificare in alcun modo se un nome è univoco. È pertanto consigliabile adottare una convenzione di denominazione che possa essere difficilmente usata da altre organizzazioni.

## Problemi di autenticazione, sottoscrizione, ruolo e quota

Una corretta distribuzione può essere ostacolata da numerosi problemi, tra cui quelli relativi ad autenticazione, autorizzazione e Azure Active Directory. Indipendentemente da come vengono gestiti i gruppi di risorse di Azure, l'identità usata per accedere all'account deve essere un oggetto di Azure Active Directory. Questa identità può essere un account aziendale o dell'istituto di istruzione creato dall'utente o assegnato a quest'ultimo oppure è possibile creare un'entità servizio per le applicazioni.

Tuttavia, Azure Active Directory consente all'utente o all'amministratore di controllare con un'elevata precisione quali identità possono accedere e a quali risorse. Se le distribuzioni non riescono, esaminare le richieste per individuare eventuali problemi di autenticazione o di autorizzazione ed esaminare anche i log di distribuzione per il gruppo di risorse. È possibile che non si disponga delle autorizzazioni per tutte le risorse. Con l'interfaccia della riga di comando di Azure è possibile esaminare i tenant di Azure Active Directory e gli utenti che usano i comandi `azure ad`. Per un elenco completo dei comandi dell'interfaccia della riga di comando di Azure, vedere [Uso dell'interfaccia della riga di comando di Azure per Mac, Linux e Windows con Gestione risorse di Azure](azure-cli-arm-commands.md).

Alcuni problemi potrebbero verificarsi anche quando una distribuzione raggiunge una quota predefinita, per ogni gruppo di risorse, sottoscrizioni, account o per altri ambiti. Confermare di disporre delle risorse necessarie per una corretta distribuzione. Per informazioni complete sulle quote, vedere [Sottoscrizione di Azure e limiti, quote e vincoli dei servizi](../azure-subscription-service-limits.md).

Per esaminare le quote della sottoscrizione per i core, usare il comando `azure vm list-usage` nell'interfaccia della riga di comando di Azure e il cmdlet **Get-AzureVMUsage** in PowerShell. Di seguito viene mostrato il comando nell'interfaccia della riga di comando di Azure, che indica che la quota di core per un account di valutazione gratuita è 4:

    azure vm list-usage
    info:    Executing command vm list-usage
    Location: westus
    data:    Name   Unit   CurrentValue  Limit
    data:    -----  -----  ------------  -----
    data:    Cores  Count  0             4
    info:    vm list-usage command OK

Se si tenta di distribuire un modello che crea più di 4 core nell'area Stati Uniti occidentali per la sottoscrizione precedente, si otterrà un errore di distribuzione che potrebbe essere simile alla seguente (nel portale o esaminando i log di distribuzione):

    statusCode:Conflict
    serviceRequestId:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
    statusMessage:{"error":{"code":"OperationNotAllowed","message":"Operation results in exceeding quota limits of Core. Maximum allowed: 4, Current in use: 4, Additional requested: 2."}}

In questi casi, si deve accedere al portale e rivolgersi all'assistenza per richiedere l'aumento della quota per l'area di destinazione della distribuzione.

> [AZURE.NOTE]Tenere presente che per i gruppi di risorse, la quota è riferita alle singole aree e non all'intera sottoscrizione. Se è necessario distribuire 30 core nell'area Stati Uniti occidentali, è necessario richiedere 30 core di gestione delle risorse per Stati Uniti occidentali. Se è necessario distribuire 30 core in qualsiasi area a cui si ha accesso, è necessario richiedere 30 core di gestione delle risorse per tutte le aree.
<!-- -->
Per essere precisi per i core, ad esempio, è possibile controllare le aree per cui è necessario richiedere la quantità appropriata di quote tramite il comando seguente, che invia pipe a **jq** per l'analisi json.
<!-- -->
        azure provider show Microsoft.Compute --json | jq '.resourceTypes[] | select(.name == "virtualMachines") | { name,apiVersions, locations}'
        {
          "name": "virtualMachines",
          "apiVersions": [
            "2015-05-01-preview",
            "2014-12-01-preview"
          ],
          "locations": [
            "East US",
            "West US",
            "West Europe",
            "East Asia",
            "Southeast Asia"
          ]
        }


## Verifica della registrazione del provider di risorse

Le risorse vengono gestite dai provider di risorse ed è possibile abilitare un account o una sottoscrizione per usare un provider specifico. Se è abilitato l'uso di un provider, è anche necessario registrarlo. La maggior parte dei provider, ma non tutti, vengono registrati automaticamente dal portale di Azure o dall'interfaccia della riga di comando che si sta usando.

### PowerShell

Per ottenere un elenco di provider di risorse e il proprio stato di registrazione, usare **Get-AzureProvider** per le versioni di PowerShell precedenti all'anteprima 1.0.

    PS C:\> Get-AzureProvider

    ProviderNamespace                       RegistrationState                       ResourceTypes
    -----------------                       -----------------                       -------------
    Microsoft.AppService                    Registered                              {apiapps, appIdentities, gateways, d...
    Microsoft.Batch                         Registered                              {batchAccounts}
    microsoft.cache                         Registered                              {Redis, checkNameAvailability, opera...
    ...

Per registrare un provider, usare **Register-AzureProvider**.

Per l'anteprima di PowerShell 1.0, usare **Get-AzureRmResourceProvider**.

    PS C:\> Get-AzureRmResourceProvider -ListAvailable

    ProviderNamespace               RegistrationState ResourceTypes
    -----------------               ----------------- -------------
    Microsoft.ApiManagement         Unregistered      {service, validateServiceName, checkServiceNameAvailability}
    Microsoft.AppService            Registered        {apiapps, appIdentities, gateways, deploymenttemplates...}
    Microsoft.Batch                 Registered        {batchAccounts}

Per registrare un provider, usare **Register-AzureRmResourceProvider**.

### Interfaccia della riga di comando di Azure

Per vedere se il provider è registrato per l'uso dell'interfaccia della riga di comando di Azure, usare il comando `azure provider list` (di seguito è riportato un esempio troncato dell'output).

        azure provider list
        info:    Executing command provider list
        + Getting ARM registered providers
        data:    Namespace                        Registered
        data:    -------------------------------  -------------
        data:    Microsoft.Compute                Registered
        data:    Microsoft.Network                Registered  
        data:    Microsoft.Storage                Registered
        data:    microsoft.visualstudio           Registered
        data:    Microsoft.Authorization          Registered
        data:    Microsoft.Automation             NotRegistered
        data:    Microsoft.Backup                 NotRegistered
        data:    Microsoft.BizTalkServices        NotRegistered
        data:    Microsoft.Features               Registered
        data:    Microsoft.Search                 NotRegistered
        data:    Microsoft.ServiceBus             NotRegistered
        data:    Microsoft.Sql                    Registered
        info:    provider list command OK

Anche in questo caso, se si desiderano altre informazioni sui provider, inclusa la loro disponibilità in determinate aree, digitare `azure provider list --json`. Il codice seguente consente di selezionare soltanto il primo nell'elenco per la visualizzazione:

        azure provider list --json | jq '.[0]'
        {
          "resourceTypes": [
            {
              "apiVersions": [
                "2014-02-14"
              ],
              "locations": [
                "North Central US",
                "East US",
                "West US",
                "North Europe",
                "West Europe",
                "East Asia"
              ],
              "properties": {},
              "name": "service"
            }
          ],
          "id": "/subscriptions/<guid>/providers/Microsoft.ApiManagement",
          "namespace": "Microsoft.ApiManagement",
          "registrationState": "Registered"
        }

Se un provider richiede la registrazione, usare il comando `azure provider register <namespace>`, dove il valore *namespace* viene acquisito dall'elenco precedente.

### API REST

Per ottenere lo stato di registrazione, vedere [Ottenere informazioni su un provider di risorse](https://msdn.microsoft.com/library/azure/dn790534.aspx).

Per registrare un provider, vedere [Registrare una sottoscrizione con un provider di risorse](https://msdn.microsoft.com/library/azure/dn790548.aspx).


## Informazioni su una distribuzione eseguita correttamente per i modelli personalizzati

Se si usano i modelli creati, è importante comprendere che il sistema Gestione risorse di Azure indica la corretta esecuzione di una distribuzione quando tutti i provider vengono restituiti correttamente dalla distribuzione. Ciò significa che tutti gli elementi del modello sono stati distribuiti per l'utilizzo.

Tuttavia, questo non implica necessariamente che il gruppo di risorse è “attivo e disponibile per gli utenti”. Ad esempio, la maggior parte delle distribuzioni richiede la distribuzione per scaricare gli aggiornamenti, attendere altre risorse esterne al modello oppure installare script complessi o altre attività eseguibili che Azure non rileva perché non sono attività registrate da un provider. In questi casi potrebbe trascorrere altro tempo prima che le risorse siano effettivamente pronte per l'uso. Di conseguenza, è necessario che lo stato di distribuzione venga completato correttamente prima di poter usare la distribuzione.

Per impedire che Azure segnali lo stato di completamento della distribuzione, tuttavia, è possibile creare uno script personalizzato per il modello personalizzato usando, ad esempio, [CustomScriptExtension](http://azure.microsoft.com/blog/2014/08/20/automate-linux-vm-customization-tasks-using-customscript-extension/), che consente di monitorare la conformità dell'intera distribuzione a livello di sistema e di restituire lo stato di completamento corretto solo quando gli utenti possono interagire con l'intera distribuzione. Per assicurarsi che l'estensione specificata sia l'ultima a essere eseguita, usare la proprietà **dependsOn** nel modello. È possibile visualizzare un esempio durante [la creazione di distribuzioni modello](https://msdn.microsoft.com/library/azure/dn790564.aspx).

## Strumenti utili per interagire con Azure
Quando si utilizzano le risorse di Azure dalla riga di comando, si raccolgono gli strumenti che consentono di svolgere il lavoro. I modelli di gruppi di risorse di Azure sono documenti JSON e l'API Gestione risorse di Azure accetta e restituisce JSON, quindi gli strumenti di analisi di JSON sono tra gli strumenti più importanti per cercare informazioni sulle risorse e progettare o interagire con i modelli e i file dei parametri di modello.

### Strumenti Mac, Linux e Windows
Se si usa l'interfaccia della riga di comando di Azure per Mac, Linux e Windows, è probabile che si conoscano già strumenti di download standard come **[curl](http://curl.haxx.se/)** e **[wget](https://www.gnu.org/software/wget/)** oppure **[Resty](https://github.com/beders/Resty)** e utilità JSON come **[jq](http://stedolan.github.io/jq/download/)**, **[jsawk](https://github.com/micha/jsawk)** e le librerie di linguaggi per la gestione di JSON. Molti di questi strumenti hanno anche porte per Windows, ad esempio [wget](http://gnuwin32.sourceforge.net/packages/wget.htm). In realtà esistono diversi modi per eseguire Linux e altri strumenti software open source anche in Windows.

Questo argomento include alcuni comandi dell'interfaccia della riga di comando di Azure che è possibile usare con **jq** per ottenere le informazioni specifiche desiderate in modo più efficiente. È necessario scegliere un set di strumenti familiare che consenta di comprendere come usare le risorse di Azure.

### PowerShell

PowerShell ha diversi comandi di base che consentono di eseguire le stesse procedure.

- Usare il cmdlet **[Invoke-WebRequest](https://technet.microsoft.com/library/hh849901%28v=wps.640%29)** per scaricare file quali i modelli di gruppi di risorse o i file dei parametri JSON.
- Usare il cmdlet **[ConvertFrom-Json](https://technet.microsoft.com/library/hh849898%28v=wps.640%29.aspx)** per convertire una stringa JSON in un oggetto personalizzato ([PSCustomObject](https://msdn.microsoft.com/library/windows/desktop/system.management.automation.pscustomobject%28v=vs.85%29.aspx)) con una proprietà per ogni campo nella stringa JSON.


## Passaggi successivi

Per informazioni su come creare i modelli, leggere [Creazione di modelli di Gestione risorse di Azure](../resource-group-authoring-templates.md) e scorrere il [repository dei modelli di avvio rapido di Azure](https://github.com/Azure/azure-quickstart-templates) per trovare esempi distribuibili. Un esempio della proprietà **dependsOn** è disponibile nel modello per la [creazione di una macchina virtuale con più NIC e RDP accessibili](https://github.com/Azure/azure-quickstart-templates/tree/master/201-1-vm-loadbalancer-2-nics).

<!--Image references-->

<!--Reference style links - using these makes the source content way more readable than using inline links-->

<!---HONumber=AcomDC_0107_2016-->