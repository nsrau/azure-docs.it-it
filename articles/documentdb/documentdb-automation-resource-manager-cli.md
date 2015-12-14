<properties
	pageTitle="Automazione di DocumentDB - Gestione risorse - Interfaccia della riga di comando | Microsoft Azure"
	description="Utilizzare i modelli di Gestione risorse di Azure o l'interfaccia della riga di comando per distribuire un account del database di DocumentDB. DocumentDB è un database NoSQL basato su cloud per dati JSON."
	services="documentdb"
	authors="mimig1"
	manager="jhubbard"
	editor=""
    tags="azure-resource-manager"
	documentationCenter=""/>


<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/02/2015" 
	ms.author="mimig"/>

# Creazione automatizzata di un account del database DocumentDB mediante i modelli di Gestione risorse di Azure e l'interfaccia della riga di comando di Azure

> [AZURE.SELECTOR]
- [Azure Portal](documentdb-create-account.md)
- [Azure CLI and ARM](documentdb-automation-resource-manager-cli.md)

In questo articolo viene illustrato come creare un account DocumentDB utilizzando modelli di Gestione risorse di Azure o l'interfaccia della riga di comando di Azure (CLI).

- [Creazione di un account DocumentDB mediante interfaccia della riga di comando](#quick-create-documentdb-account)
- [Creazione di un account di DocumentDB tramite un modello ARM](#deploy-documentdb-from-a-template)

Gli account del database DocumentDB sono attualmente l'unica risorsa DocumentDB che può essere creata mediante i modelli ARM e l'interfaccia della riga di comando di Azure.

## Preparazione

Prima di poter usare l'interfaccia della riga di comando di Azure con i gruppi di risorse di Azure, è necessario disporre della versione corretta dell'interfaccia della riga di comando di Azure e di un account Azure. Se non si dispone dell'interfaccia della riga di comando di Azure, è necessario [installarla](../xplat-cli-install.md).

### Aggiornamento della versione dell'interfaccia della riga di comando di Azure

Nel prompt dei comandi, digitare `azure --version` per verificare se è già installata la versione 0.9.11 o una versione successiva.

	azure --version
    0.9.11 (node: 0.12.7)

Se la versione installata non è la 0.9.11 o una versione successiva, sarà necessario [installare l'interfaccia della riga di comando di Azure](../xplat-cli-install.md) o l'aggiornamento mediante uno dei programmi di installazione nativi o tramite **npm** digitando `npm update -g azure-cli` per effettuare l'aggiornamento o `npm install -g azure-cli` per l'installazione.

### Impostare l'account e la sottoscrizione di Azure

Se non si dispone già di una sottoscrizione ad Azure, ma si dispone di una a Visual Studio, è possibile attivare i [Benefici per sottoscrittori Visual Studio](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) oppure [iscriversi per una versione di valutazione gratuita di Azure](http://azure.microsoft.com/pricing/free-trial/).

Per utilizzare i modelli di gestione risorse di Azure, è necessario disporre di un account aziendale o dell'istituto di istruzione o di un'identità dell'account Microsoft. Se si dispone di uno di questi account, digitare il comando seguente.

	azure login

L'output ottenuto sarà il seguente:

    info:    Executing command login
    |info:    To sign in, use a web browser to open the page https://aka.ms/devicelogin. 
    Enter the code E1A2B3C4D to authenticate. If you're signing in as an Azure
    AD application, use the --username and --password parameters.

> [AZURE.NOTE]Se non è disponibile un account Azure, verrà visualizzato un messaggio di errore che indica la necessità di un diverso tipo di account. Per crearne uno dall'account Azure corrente, vedere [Creazione di un'identità aziendale o dell'istituto di istruzione in Azure Active Directory](../resource-group-create-work-id-from-personal.md).

Aprire [https://aka.ms/devicelogin](https://aka.ms/devicelogin) in un browser e immettere il codice indicato nell'output del comando.

![Schermata che illustra la finestra di accesso del dispositivo per l'interfaccia della riga di comando di Microsoft Azure](media/documentdb-automation-resource-manager-cli/azure-cli-login-code.png)

Dopo aver immesso il codice, selezionare l'identità da utilizzare nel browser e, se necessario, fornire il nome utente e la password.

![Schermata che illustra la selezione dell'account dell'identità Microsoft associato alla sottoscrizione Azure da utilizzare](media/documentdb-automation-resource-manager-cli/identity-cli-login.png)

Una volta effettuato l'accesso, verrà visualizza la schermata di conferma seguente e sarà quindi possibile chiudere la finestra del browser.

![Schermata che illustra la conferma dell'accesso all'interfaccia della riga di comando multipiattaforma di Microsoft Azure](media/documentdb-automation-resource-manager-cli/login-confirmation.png)

La shell dei comandi fornisce inoltre l'output seguente.

    -info:    Added subscription Visual Studio Ultimate with MSDN
	+
	info:    login command OK

Oltre al metodo di accesso interattivo descritto di seguito, sono disponibili altri metodi di accesso all'interfaccia della riga di comando di Azure. Per ulteriori informazioni sugli altri metodi e per informazioni sulla gestione di più sottoscrizioni, vedere [Connessione a una sottoscrizione Azure dall'interfaccia della riga di comando di Azure (Azure CLI)](../xplat-cli-connect.md).

### Passare alla modalità gruppo di risorse dell'interfaccia della riga di comando di Azure

Per impostazione predefinita, l'interfaccia della riga di comando di Azure viene avviata nella modalità di gestione dei servizi (modalità **asm**). Digitare quanto segue per passare alla modalità gruppo di risorse.

	azure config mode arm

L'output ottenuto sarà il seguente:

	info:    New mode is arm

È possibile passare nuovamente al set predefinito di comandi digitando `azure config mode asm`.

## <a id="quick-create-documentdb-account"></a>Attività: creazione di un account di DocumentDB tramite interfaccia della riga di comando

Le istruzioni in questa sezione consentono di creare un account DocumentDB mediante l'interfaccia della riga di comando di Azure.

### Passaggio 1: creare o recuperare il gruppo di risorse

Per creare un account DocumentDB, è necessario innanzitutto un gruppo di risorse. Se si conosce già il nome del gruppo di risorse da utilizzare, andare al [passaggio 2](#create-documentdb-account-cli).

Per esaminare un elenco di tutti i gruppi di risorse correnti, eseguire il comando seguente e prendere nota del nome del gruppo di risorse da utilizzare:

    azure group list

Per creare un nuovo gruppo di risorse, eseguire il comando seguente, specificare il nome del nuovo gruppo di risorse da creare e l'area in cui creare il gruppo di risorse:

	azure group create <resourcegroupname> <location>

Ad esempio:

	azure group create new_res_group westus

L'output ottenuto sarà il seguente:

    info:    Executing command group create
    + Getting resource group new_res_group
    + Creating resource group new_res_group
    info:    Created resource group new_res_group
    data:    Id:                  /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/new_res_group
    data:    Name:                new_res_group
    data:    Location:            westus
    data:    Provisioning State:  Succeeded
    data:    Tags: null
    data:
    info:    group create command OK

In caso di errori, vedere [Risoluzione dei problemi](#troubleshooting).

### <a id="create-documentdb-account-cli"></a>Passaggio 2: creare un account DocumentDB mediante l'interfaccia della riga di comando

Creare un account DocumentDB nel gruppo di risorse nuovo o esistente immettendo il comando seguente nel prompt dei comandi:

> [AZURE.TIP]Se si esegue questo comando in Azure PowerShell o Windows PowerShell si riceverà un errore relativo a un token imprevisto. Eseguire invece questo comando nel prompt dei comandi di Windows.

    azure resource create -g <resourceGroupName> -n <databaseaccountname> -r "Microsoft.DocumentDB/databaseAccounts" -o "2015-04-08" -l <databaseaccountlocation> -p "{"databaseAccountOfferType":"Standard"}" 

Ad esempio:

    azure resource create -g new_res_group -n samplecliacct -r "Microsoft.DocumentDB/databaseAccounts" -o 2015-04-08  -l westus -p "{"databaseAccountOfferType":"Standard"}"

Durante il provisioning del nuovo account, l'output ottenuto sarà il seguente:

    info:    Executing command resource create
    + Getting resource samplecliacct
    + Creating resource samplecliacct
    info:    Resource samplecliacct is updated
    data:
    data:    Id:        /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/new_res_group/providers/Microsoft.DocumentDB/databaseAccounts/samplecliacct
    data:    Name:      samplecliacct
    data:    Type:      Microsoft.DocumentDB/databaseAccounts
    data:    Parent:
    data:    Location:  West US
    data:    Tags:
    data:
    info:    resource create command OK

In caso di errori, vedere [Risoluzione dei problemi](#troubleshooting).

Dopo il completamento del comando, lo stato dell'account sarà **In creazione** per alcuni minuti, per poi cambiare in **Online** una volta pronto per l'utilizzo. È possibile controllare lo stato dell'account nel [portale di Azure](https://portal.azure.com), nel pannello **Account DocumentDB**.

## <a id="deploy-documentdb-from-a-template"></a>Attività: creazione di un account DocumentDB mediante un modello ARM

Le istruzioni in questa sezione consentono di creare un account DocumentDB con un modello di Gestione risorse di Azure (ARM) e un file dei parametri facoltativi, entrambi in formato JSON. Utilizzare un modello consente di descrivere esattamente cosa si desidera e ripeterlo senza errori.

### Informazioni sui modelli ARM e sui gruppi di risorse

La maggior parte delle applicazioni è costituita da un insieme di diversi tipi di risorse, ad esempio uno o più account DocumentDB, account di archiviazione, una rete virtuale o una rete per la distribuzione di contenuti. L'API di gestione del servizio Azure predefinita e il portale di Azure rappresentano questi elementi tramite un approccio servizio per servizio. Questo approccio prevede che i singoli servizi vengano distribuiti e gestiti individualmente (o tramite appositi strumenti) e non come una singola unità logica di distribuzione.

I *modelli di Gestione risorse di Azure* consentono di distribuire e gestire queste risorse diverse come una singola unità logica di distribuzione in modo dichiarativo. Anziché in modo imperativo indicando a Azure gli elementi da distribuire un comando dopo l'altro, è possibile descrivere l'intera distribuzione in un file JSON (tutte le risorse, la configurazione associata e i parametri di distribuzione) e indicare ad Azure di distribuire le risorse come un singolo gruppo.

Per ulteriori informazioni sui gruppi di risorse di Azure e su come utilizzarli, vedere [Panoramica di Gestione risorse di Azure](../resource-group-overview.md). Se si è interessati alla creazione di modelli, vedere [Creazione di modelli di Gestione risorse di Azure](../resource-group-authoring-templates.md).

### Passaggio 1: creare un file di modello e dei parametri

Creare un file di modello locale con il contenuto seguente. Denominare il file azuredeploy.json.

    {
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "databaseAccountName": {
                "type": "string"
            }
        },
        "variables": { },
        "resources": [
            {
                "apiVersion": "2015-04-08",
                "type": "Microsoft.DocumentDb/databaseAccounts",
                "name": "[parameters('databaseAccountName')]",
                "location": "[resourceGroup().location]",
                "properties": {
                    "name": "[parameters('databaseAccountName')]",
                    "databaseAccountOfferType": "Standard"
                }
            }
        ]
    }

Questo modello richiede un solo parametro, il nome account del database da creare. Il percorso del nuovo account del database viene impostato nello stesso percorso del gruppo di risorse.

Poiché il modello accetta solo un parametro, è possibile immettere il valore nella riga di comando o creare un file dei parametri per specificare il valore.

Per creare un file dei parametri, copiare il contenuto seguente in un nuovo file da denominare azuredeploy.parameters.json. Se si prevede di specificare il nome account del database nel prompt dei comandi, è possibile continuare senza creare il file.

    {
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "databaseAccountName": {
                "value": "samplearmacct"
            }
        }
    }

Nel file azuredeploy.parameters.json file, aggiornare il valore "samplearmacct" al nome del database da utilizzare, quindi salvare il file.

> [AZURE.TIP]I nomi account dei database possono contenere solo lettere minuscole, numeri, il carattere "-" e devono essere compresi fra 3 e 50 caratteri.

### Passaggio 2: creare o recuperare il gruppo di risorse

Per creare un account DocumentDB, è necessario innanzitutto un gruppo di risorse. Se si conosce già il nome del gruppo di risorse da utilizzare, andare al [passaggio 3](#create-account-from-template).

Per esaminare un elenco di tutti i gruppi di risorse correnti, eseguire il comando seguente e prendere nota del nome del gruppo di risorse da utilizzare:

    azure group list

Per creare un nuovo gruppo di risorse, eseguire il comando seguente, specificare il nome del nuovo gruppo di risorse da creare e l'area in cui creare il gruppo di risorse:

	azure group create <resourcegroupname> <location>

Ad esempio:

	azure group create new_res_group westus

L'output ottenuto sarà il seguente:

    info:    Executing command group create
    + Getting resource group new_res_group
    + Creating resource group new_res_group
    info:    Created resource group new_res_group
    data:    Id:                  /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/new_res_group
    data:    Name:                new_res_group
    data:    Location:            West US
    data:    Provisioning State:  Succeeded
    data:    Tags: null
    data:
    info:    group create command OK

In caso di errori, vedere [Risoluzione dei problemi](#troubleshooting).

### <a id="create-account-from-template"></a>Passaggio 3: creare l'account DocumentDB mediante un modello ARM

Per creare un account DocumentDB nel gruppo di risorse, eseguire il comando seguente e indicare il percorso del file di modello, il percorso del file dei parametri o il valore del parametro, il nome del gruppo di risorse in cui eseguire la distribuzione e un nome per la distribuzione (-n è facoltativo).

Per utilizzare un file dei parametri:

    azure group deployment create -f <PathToTemplate> -e <PathToParameterFile> -g <resourcegroupname> -n <deploymentname>

Ad esempio:

    azure group deployment create -f azuredeploy.json -e azuredeploy.parameters.json -g new_res_group -n azuredeploy

OPPURE per specificare il parametro del nome account del database senza un file dei parametri e ricevere una richiesta per il valore, eseguire il comando seguente:

    azure group deployment create -f <PathToTemplate> -g <resourcegroupname> -n <deploymentname>

Ad esempio (vengono visualizzate la richiesta e l'immissione di un account del database denominato new\_db\_acct):

    azure group deployment create -f azuredeploy.json -g new_res_group -n azuredeploy
    info:    Executing command group deployment create
    info:    Supply values for the following parameters
    databaseAccountName: newarmacct

Durante il provisioning dell'account, si riceveranno le informazioni seguenti:

    info:    Executing command group deployment create
    + Initializing template configurations and parameters
    + Creating a deployment
    info:    Created template deployment "azuredeploy"
    + Waiting for deployment to complete
    data:    DeploymentName     : azuredeploy
    data:    ResourceGroupName  : new_res_group
    data:    ProvisioningState  : Succeeded
    data:    Timestamp          : 2015-11-30T18:50:23.6300288Z
    data:    Mode               : Incremental
    data:    Name                 Type    Value
    data:    -------------------  ------  ------------------
    data:    databaseAccountName  String  newarmacct
    data:    location             String  West US
    info:    group deployment create command OK

In caso di errori, vedere [Risoluzione dei problemi](#troubleshooting).

Dopo il completamento del comando, lo stato dell'account sarà **In creazione** per alcuni minuti, per poi cambiare in **Online** una volta pronto per l'utilizzo. È possibile controllare lo stato dell'account nel [portale di Azure](https://portal.azure.com), nel pannello **Account DocumentDB**.

## Risoluzione dei problemi

Se, durante la creazione dell'account di database o del gruppo di risorse, si ricevono errori come `Deployment provisioning state was not successful`, utilizzare il comando seguente per visualizzare il log del gruppo di risorse.

    azure group log show <resourcegroupname> --last-deployment

Ad esempio:

    azure group log show new_res_group --last-deployment

Vedere [Risoluzione dei problemi relativi alle distribuzioni di gruppi di risorse in Azure](../resource-group-deploy-debug.md) per ulteriori informazioni.

Tenere presente che i nomi account dei database possono contenere solo lettere minuscole, numeri, il carattere "-" e devono essere compresi fra 3 e 50 caratteri.

Le informazioni sull'errore sono disponibili anche nel portale di Azure, come illustrato nella schermata seguente. Per passare alle informazioni sull'errore: fare clic su Gruppi di risorse nell'indice, selezionare il gruppo di risorse in cui si è verificato l'errore, quindi nell'area Informazioni di base del pannello del gruppo di risorse, selezionare la data dell'ultima distribuzione e, nel pannello Cronologia distribuzioni, selezionare la distribuzione non riuscita, infine nel pannello Distribuzione, fare clic su Dettagli operazione con un punto esclamativo rosso. Nel pannello Dettagli operazione, viene visualizzato il messaggio di stato per la distribuzione non riuscita.

![Schermata del portale di Azure che illustra come passare al messaggio di errore di distribuzione](media/documentdb-automation-resource-manager-cli/portal-troubleshooting-deploy.png)

## Passaggi successivi

Ora che avete un account di DocumentDB, il passaggio successivo consiste nel creare un database di DocumentDB. È possibile creare un database utilizzando uno dei seguenti:

- Il portale di Azure, come descritto in [Creazione di un database DocumentDB mediante il portale di Azure](documentdb-create-database.md).
- Esempi di C# .NET nel progetto [DatabaseManagement](https://github.com/Azure/azure-documentdb-net/tree/master/samples/code-samples/DatabaseManagement) del repository [azure-documentdb-dotnet](https://github.com/Azure/azure-documentdb-net/tree/master/samples/code-samples) su GitHub.
- Gli [SDK di DocumentDB](https://msdn.microsoft.com/library/azure/dn781482.aspx). DocumentDB dispone di .NET, Java, Python, Node. js e SDK di API JavaScript. 

Dopo aver creato il database, è necessario [aggiungere una o più raccolte](documentdb-create-collection.md) al database e quindi [aggiungere documenti](documentdb-view-json-document-explorer.md) alle raccolte.

Quando una raccolta contiene documenti, è possibile utilizzare [DocumentDB SQL](documentdb-sql-query.md) ed [eseguire query](documentdb-sql-query.md#executing-queries) rispetto ai documenti mediante [Esplora query](documentdb-query-collections-query-explorer.md) nel portale di anteprima, l'[API REST](https://msdn.microsoft.com/library/azure/dn781481.aspx) o uno degli [SDK](https://msdn.microsoft.com/library/azure/dn781482.aspx).

Per ulteriori informazioni su DocumentDB, esplorare queste risorse:

-	[Percorso di apprendimento per DocumentDB](https://azure.microsoft.com/documentation/learning-paths/documentdb/)
-	[Modello di risorse e concetti relativi a DocumentDB](documentdb-resources.md)

Per ulteriori modelli da poter utilizzare, vedere [Modelli di avvio rapido di Azure](http://azure.microsoft.com/documentation/templates/).

<!---HONumber=AcomDC_1203_2015-->