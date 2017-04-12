---
title: Automazione di DocumentDB - Resource Manager - Interfaccia della riga di comando di Azure 1.0 | Documentazione Microsoft
description: "Utilizzare i modelli di Gestione risorse di Azure o l&quot;interfaccia della riga di comando per distribuire un account del database di DocumentDB. DocumentDB è un database NoSQL basato su cloud per dati JSON."
services: documentdb
author: mimig1
manager: jhubbard
editor: 
tags: azure-resource-manager
documentationcenter: 
ms.assetid: eae5eec6-0e27-442c-abfc-ef6b7fd3f8d2
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/27/2017
ms.author: mimig
translationtype: Human Translation
ms.sourcegitcommit: 197ebd6e37066cb4463d540284ec3f3b074d95e1
ms.openlocfilehash: 0959dace90686324b9fe4360f4696f8d85c3a9e7
ms.lasthandoff: 03/31/2017


---
# <a name="automate-documentdb-account-creation-using-azure-cli-10-and-azure-resource-manager-templates"></a>Automatizzare la creazione dell'account DocumentDB con i modelli di Azure Resource Manager e l'interfaccia della riga di comando di Azure 1.0
> [!div class="op_single_selector"]
> * [Portale di Azure](documentdb-create-account.md)
> * [Interfaccia della riga di comando di Azure 1.0](documentdb-automation-resource-manager-cli-nodejs.md)
> * [Interfaccia della riga di comando di Azure 2.0](documentdb-automation-resource-manager-cli.md)
> * [Azure PowerShell](documentdb-manage-account-with-powershell.md)

In questo articolo viene illustrato come creare un account Azure DocumentDB usando modelli di Azure Resource Manager o direttamente con l'interfaccia della riga di comando di Azure 1.0. Per creare un account DocumentDB nel portale di Azure, vedere [Creare un account database di DocumentDB nel portale di Azure](documentdb-create-account.md).

Gli account del database DocumentDB sono attualmente l'unica risorsa DocumentDB che può essere creata mediante i modelli di Resource Manager e l'interfaccia della riga di comando di Azure 1.0.

## <a name="getting-ready"></a>Preparazione
Prima di poter usare l'interfaccia della riga di comando di Azure 1.0 con i gruppi di risorse di Azure, è necessario disporre della versione corretta dell'interfaccia e di un account Azure. Se non è disponibile l'interfaccia della riga di comando di Azure 1.0 è necessario [installarla](../cli-install-nodejs.md).

### <a name="update-your-azure-cli-10-version"></a>Aggiornamento della versione dell'interfaccia della riga di comando di Azure 1.0
Al prompt dei comandi digitare `azure --version` per verificare se è già installata la versione 0.10.4 o una versione successiva. In questa fase è possibile che venga richiesto di partecipare alla raccolta dei dati dell’interfaccia della riga di comando di Microsoft Azure ed è possibile accettare o rifiutare.

    azure --version
    0.10.4 (node: 4.2.4)

Se la versione installata non è la 0.10.4 o una versione successiva, sarà necessario [installare l'interfaccia della riga di comando di Azure 1.0](../cli-install-nodejs.md) o l'aggiornamento usando uno dei programmi di installazione nativi o con **npm** digitando `npm update -g azure-cli` per effettuare l'aggiornamento o `npm install -g azure-cli` per l'installazione.

### <a name="set-your-azure-account-and-subscription"></a>Impostare l'account e la sottoscrizione di Azure
Se non si dispone ancora di una sottoscrizione Azure, ma si dispone di una sottoscrizione Visual Studio, è possibile attivare i [vantaggi per i titolari di sottoscrizioni Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) oppure iscriversi a una [versione di prova gratuita](https://azure.microsoft.com/pricing/free-trial/).

Per utilizzare i modelli di gestione risorse di Azure, è necessario disporre di un account aziendale o dell'istituto di istruzione o di un'identità dell'account Microsoft. Se si dispone di uno di questi account, digitare il comando seguente:

    azure login

L'output ottenuto sarà il seguente:

    info:    Executing command login
    |info:    To sign in, use a web browser to open the page https://aka.ms/devicelogin.
    Enter the code E1A2B3C4D to authenticate.

> [!NOTE]
> Se non è disponibile un account di Azure, viene visualizzato un messaggio di errore che indica la necessità di un diverso tipo di account. Per crearne uno dall'account Azure corrente, vedere [Creazione di un'identità aziendale o dell'istituto di istruzione in Azure Active Directory](../virtual-machines/windows/create-aad-work-id.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
>
>

Aprire [https://aka.ms/devicelogin](https://aka.ms/devicelogin) in un browser e immettere il codice indicato nell'output del comando.

![Screenshot che illustra la finestra di accesso del dispositivo per l'interfaccia della riga di comando di Microsoft Azure 1.0](media/documentdb-automation-resource-manager-cli/azure-cli-login-code.png)

Dopo aver immesso il codice, selezionare l'identità da utilizzare nel browser e, se necessario, fornire il nome utente e la password.

![Schermata che illustra la selezione dell'account dell'identità Microsoft associato alla sottoscrizione Azure da utilizzare](media/documentdb-automation-resource-manager-cli/identity-cli-login.png)

Una volta effettuato l'accesso, viene visualizza la schermata di conferma seguente ed è quindi possibile chiudere la finestra del browser.

![Schermata che illustra la conferma dell'accesso all'interfaccia della riga di comando multipiattaforma di Microsoft Azure](media/documentdb-automation-resource-manager-cli/login-confirmation.png)

La shell dei comandi fornisce inoltre l'output seguente:

    /info:    Added subscription Visual Studio Ultimate with MSDN
    info:    Setting subscription "Visual Studio Ultimate with MSDN" as default
    +
    info:    login command OK

Oltre al metodo di accesso interattivo descritto di seguito, sono disponibili altri metodi di accesso all'interfaccia della riga di comando di Azure 1.0. Per altre informazioni su altri metodi e per informazioni sulla gestione di più sottoscrizioni, vedere [Connettersi a una sottoscrizione di Azure dall'interfaccia della riga di comando di Azure 1.0](../xplat-cli-connect.md).

### <a name="switch-to-azure-cli-10-resource-group-mode"></a>Passare alla modalità gruppo di risorse dell'interfaccia della riga di comando di Azure 1.0
Per impostazione predefinita, l'interfaccia della riga di comando di Azure 1.0 viene avviata nella modalità di gestione dei servizi (modalità**Gestione dei servizi di Azure**). Digitare quanto segue per passare alla modalità gruppo di risorse.

    azure config mode arm

L'output ottenuto sarà il seguente:

    info:    Executing command config mode
    info:    New mode is arm
    info:    config mode command OK

Se necessario, è possibile passare nuovamente al set predefinito di comandi digitando `azure config mode asm`.

### <a name="create-or-retrieve-your-resource-group"></a>Creare o recuperare il gruppo di risorse
Per creare un account DocumentDB, è necessario innanzitutto un gruppo di risorse. Se si conosce già il nome del gruppo di risorse da usare, andare al [passaggio 2](#create-documentdb-account-cli).

Per esaminare un elenco di tutti i gruppi di risorse correnti, eseguire il comando seguente e prendere nota del nome del gruppo di risorse da usare:

    azure group list

Per creare un gruppo di risorse, eseguire il comando seguente, specificare il nome del nuovo gruppo di risorse da creare e l'area in cui creare il gruppo di risorse:

    azure group create <resourcegroupname> <resourcegrouplocation>

* `<resourcegroupname>` può usare solo caratteri alfanumerici, punti, caratteri di sottolineatura, il carattere '-' e parentesi e non può terminare con un punto.
* `<resourcegrouplocation>` deve essere una delle aree in cui DocumentDB è generalmente disponibile. L'elenco corrente delle aree geografiche è disponibile nella [pagina Aree di Azure](https://azure.microsoft.com/regions/#services).

Input di esempio:

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

## <a name="understanding-resource-manager-templates-and-resource-groups"></a>Informazioni sui modelli di Resource Manager e sui gruppi di risorse
La maggior parte delle applicazioni è costituita da un insieme di diversi tipi di risorse, ad esempio uno o più account DocumentDB, account di archiviazione, una rete virtuale o una rete per la distribuzione di contenuti. L'API di gestione del servizio Azure predefinita e il portale di Azure rappresentano questi elementi tramite un approccio servizio per servizio. Questo approccio prevede che i singoli servizi vengano distribuiti e gestiti individualmente (o tramite appositi strumenti) e non come una singola unità logica di distribuzione.

*modelli di Gestione risorse di Azure* consentono di distribuire e gestire queste risorse diverse come una singola unità logica di distribuzione in modo dichiarativo. Anziché in modo imperativo indicando ad Azure gli elementi da distribuire un comando dopo l'altro, è possibile descrivere l'intera distribuzione in un file JSON (tutte le risorse, la configurazione associata e i parametri di distribuzione) e indicare ad Azure di distribuire le risorse come un singolo gruppo.

Per altre informazioni sui gruppi di risorse di Azure e su come usarli, vedere [Panoramica di Azure Resource Manager](../azure-resource-manager/resource-group-overview.md). Se si è interessati alla creazione di modelli, vedere [Creazione di modelli di Gestione risorse di Azure](../azure-resource-manager/resource-group-authoring-templates.md).

## <a id="quick-create-documentdb-account"></a>Attività: creazione di un account DocumentDB per una singola area
Le istruzioni in questa sezione consentono di creare un account DocumentDB per una singola area. A questo scopo, è necessario usare l'interfaccia della riga di comando di Azure 1.0 con o senza modelli di Resource Manager.

### <a id="create-single-documentdb-account-cli-arm"></a> Creare un account DocumentDB per una singola area tramite l'interfaccia della riga di comando di Azure 1.0 senza modelli di Resource Manager
Creare un account DocumentDB nel gruppo di risorse nuovo o esistente immettendo il comando seguente nel prompt dei comandi:

> [!TIP]
> Se si esegue questo comando in Azure PowerShell o Windows PowerShell, si riceve un errore relativo a un token imprevisto. Eseguire invece questo comando nel prompt dei comandi di Windows.
>
>

    azure resource create -g <resourcegroupname> -n <databaseaccountname> -r "Microsoft.DocumentDB/databaseAccounts" -o 2015-04-08 -l <resourcegrouplocation> -p "{\"databaseAccountOfferType\":\"Standard\",\"ipRangeFilter\":\"<ip-range-filter>\",\"locations\":["{\"locationName\":\"<databaseaccountlocation>\",\"failoverPriority\":\"<failoverPriority>\"}"]}"

* `<resourcegroupname>` può usare solo caratteri alfanumerici, punti, caratteri di sottolineatura, il carattere '-' e parentesi e non può terminare con un punto.
* `<resourcegrouplocation>` rappresenta l'area del gruppo di risorse corrente.
* `<ip-range-filter>` Specifica il set di indirizzi IP e di intervalli di indirizzi IP nel formato CIDR da includere come elenco consentito di IP client per un determinato account di database. Gli intervalli di indirizzi IP o i singoli indirizzi IP devono essere delimitati da virgole e non devono contenere spazi. Per altre informazioni, vedere [Supporto del firewall di DocumentDB](documentdb-firewall-support.md).
* `<databaseaccountname>` possono contenere solo lettere minuscole, numeri, il carattere '-' e devono essere compresi fra 3 e 50 caratteri.
* `<databaseaccountlocation>` deve essere una delle aree in cui DocumentDB è generalmente disponibile. L'elenco corrente delle aree geografiche è disponibile nella [pagina Aree di Azure](https://azure.microsoft.com/regions/#services).

Input di esempio:

    azure resource create -g new_res_group -n samplecliacct -r "Microsoft.DocumentDB/databaseAccounts" -o 2015-04-08 -l westus -p "{\"databaseAccountOfferType\":\"Standard\",\"ipRangeFilter\":\"\",\"locations\":["{\"locationName\":\"westus\",\"failoverPriority\":\"0\"}"]}"

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

Dopo il completamento del comando, lo stato dell'account sarà **Creazione in corso** per alcuni minuti, poi passerà a **Online** quando sarà pronto per l'uso. È possibile controllare lo stato dell'account nel [portale di Azure](https://portal.azure.com)nel pannello **Account DocumentDB** .

### <a id="create-single-documentdb-account-cli-arm"></a> Creare un account DocumentDB per una singola area tramite l'interfaccia della riga di comando di Azure 1.0 con modelli di Resource Manager
Nelle istruzioni di questa sezione viene descritto come creare un account DocumentDB con un modello di Azure Resource Manager e un file dei parametri facoltativi, entrambi in formato JSON. Utilizzare un modello consente di descrivere esattamente cosa si desidera e ripeterlo senza errori.

Creare un file di modello locale con il contenuto seguente. Denominare il file azuredeploy.json.

    {
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "databaseAccountName": {
                "type": "string"
            },
            "locationName1": {
                "type": "string"
            }
        },
        "variables": {},
        "resources": [
            {
                "apiVersion": "2015-04-08",
                "type": "Microsoft.DocumentDb/databaseAccounts",
                "name": "[parameters('databaseAccountName')]",
                "location": "[resourceGroup().location]",
                "properties": {
                    "databaseAccountOfferType": "Standard",
                    "ipRangeFilter": "",
                    "locations": [
                        {
                            "failoverPriority": 0,
                            "locationName": "[parameters('locationName1')]"
                        }
                    ]
                }
            }
        ]
    }

Il valore di failoverPriority deve essere 0, poiché si tratta di un account per una singola area. Un valore di failoverPriority pari a 0 indica che questa area deve essere l'[area di scrittura per l'account DocumentDB][scaling-globally].
È possibile immettere il valore nella riga di comando o creare un file dei parametri per specificare il valore.

Per creare un file dei parametri, copiare il contenuto seguente in un nuovo file da denominare azuredeploy.parameters.json. Se si prevede di specificare il nome account del database nel prompt dei comandi, è possibile continuare senza creare il file.

    {
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "databaseAccountName": {
                "value": "samplearmacct"
            },
            "locationName1": {
                "value": "westus"
            }
        }
    }

Nel file azuredeploy.parameters.json aggiornare il campo del valore `"samplearmacct"` con il nome del database che si vuole usare, quindi salvare il file. `"databaseAccountName"` possono contenere solo lettere minuscole, numeri, il carattere '-' e devono essere compresi fra 3 e 50 caratteri. Aggiornare il campo del valore `"locationName1"` con l'area in cui si desidera creare l'account DocumentDB.

Per creare un account DocumentDB nel gruppo di risorse, eseguire il comando seguente e indicare il percorso del file di modello, il percorso del file dei parametri o il valore del parametro, il nome del gruppo di risorse in cui eseguire la distribuzione e un nome per la distribuzione (-n è facoltativo).

Per utilizzare un file dei parametri:

    azure group deployment create -f <PathToTemplate> -e <PathToParameterFile> -g <resourcegroupname> -n <deploymentname>

* `<PathToTemplate>` è il percorso del file azuredeploy.json creato nel passaggio 1. Se il nome del percorso include spazi, racchiudere il parametro tra virgolette doppie.
* `<PathToParameterFile>` è il percorso del file azuredeploy.parameters.json creato nel passaggio 1. Se il nome del percorso include spazi, racchiudere il parametro tra virgolette doppie.
* `<resourcegroupname>` è il nome del gruppo di risorse esistente in cui si desidera aggiungere un account database di DocumentDB.
* `<deploymentname>` è il nome facoltativo della distribuzione.

Input di esempio:

    azure group deployment create -f azuredeploy.json -e azuredeploy.parameters.json -g new_res_group -n azuredeploy

OPPURE per specificare il parametro del nome account del database senza un file dei parametri e ricevere una richiesta per il valore, eseguire il comando seguente:

    azure group deployment create -f <PathToTemplate> -g <resourcegroupname> -n <deploymentname>

Input di esempio che illustra la richiesta e una voce per un account di database denominato samplearmacct:

    azure group deployment create -f azuredeploy.json -g new_res_group -n azuredeploy
    info:    Executing command group deployment create
    info:    Supply values for the following parameters
    databaseAccountName: samplearmacct

Durante il provisioning dell'account, si ricevono le informazioni seguenti:

    info:    Executing command group deployment create
    + Initializing template configurations and parameters
    + Creating a deployment
    info:    Created template deployment "azuredeploy"
    + Waiting for deployment to complete
    +
    +
    info:    Resource 'new_res_group' of type 'Microsoft.DocumentDb/databaseAccounts' provisioning status is Running
    +
    info:    Resource 'new_res_group' of type 'Microsoft.DocumentDb/databaseAccounts' provisioning status is Succeeded
    data:    DeploymentName     : azuredeploy
    data:    ResourceGroupName  : new_res_group
    data:    ProvisioningState  : Succeeded
    data:    Timestamp          : 2015-11-30T18:50:23.6300288Z
    data:    Mode               : Incremental
    data:    CorrelationId      : 4a5d4049-c494-4053-bad4-cc804d454700
    data:    DeploymentParameters :
    data:    Name                 Type    Value
    data:    -------------------  ------  ------------------
    data:    databaseAccountName  String  samplearmacct
    data:    locationName1        String  westus
    info:    group deployment create command OK

In caso di errori, vedere [Risoluzione dei problemi](#troubleshooting).  

Dopo il completamento del comando, lo stato dell'account sarà **Creazione in corso** per alcuni minuti, poi passerà a **Online** quando sarà pronto per l'uso. È possibile controllare lo stato dell'account nel [portale di Azure](https://portal.azure.com)nel pannello **Account DocumentDB** .

## <a id="quick-create-documentdb-with-mongodb-api-account"></a>Attività: Creare un account DocumentDB per una singola area: API per l'account MongoDB
Le istruzioni in questa sezione consentono di creare un'API per una singola area per un account MongoDB. A questo scopo, è necessario usare l'interfaccia della riga di comando di Azure 1.0 con i modelli di Resource Manager.

### <a id="create-single-documentdb-with-mongodb-api-account-cli-arm"></a> Creare un account MongoDB per una singola area tramite l'interfaccia della riga di comando di Azure 1.0 con modelli di Resource Manager
Nelle istruzioni di questa sezione viene descritto come creare un'API per un account MongoDB con un modello di Azure Resource Manager e un file dei parametri facoltativi, entrambi in formato JSON. Utilizzare un modello consente di descrivere esattamente cosa si desidera e ripeterlo senza errori.

Creare un file di modello locale con il contenuto seguente. Denominare il file azuredeploy.json.

    {
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "databaseAccountName": {
                "type": "string"
            },
            "locationName1": {
                "type": "string"
            }
        },
        "variables": {},
        "resources": [
            {
                "apiVersion": "2015-04-08",
                "type": "Microsoft.DocumentDb/databaseAccounts",
                "name": "[parameters('databaseAccountName')]",
                "location": "[resourceGroup().location]",
                "kind": "MongoDB",
                "properties": {
                    "databaseAccountOfferType": "Standard",
                    "ipRangeFilter": "",
                    "locations": [
                        {
                            "failoverPriority": 0,
                            "locationName": "[parameters('locationName1')]"
                        }
                    ]
                }
            }
        ]
    }

Per specificare che questo account supporterà le API di MongoDB, è necessario impostare la tipologia su MongoDB. Se non viene specificata alcuna proprietà di tipologia, il valore predefinito sarà un account DocumentDB nativo.

Il valore di failoverPriority deve essere 0, poiché si tratta di un account per una singola area. Un valore di failoverPriority pari a 0 indica che questa area deve essere l'[area di scrittura per l'account DocumentDB][scaling-globally].
È possibile immettere il valore nella riga di comando o creare un file dei parametri per specificare il valore.

Per creare un file dei parametri, copiare il contenuto seguente in un nuovo file da denominare azuredeploy.parameters.json. Se si prevede di specificare il nome account del database nel prompt dei comandi, è possibile continuare senza creare il file.

    {
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "databaseAccountName": {
                "value": "samplearmacct"
            },
            "locationName1": {
                "value": "westus"
            }
        }
    }

Nel file azuredeploy.parameters.json aggiornare il campo del valore `"samplearmacct"` con il nome del database che si vuole usare, quindi salvare il file. `"databaseAccountName"` possono contenere solo lettere minuscole, numeri, il carattere '-' e devono essere compresi fra 3 e 50 caratteri. Aggiornare il campo del valore `"locationName1"` con l'area in cui si desidera creare l'account DocumentDB.

Per creare un account DocumentDB nel gruppo di risorse, eseguire il comando seguente e indicare il percorso del file di modello, il percorso del file dei parametri o il valore del parametro, il nome del gruppo di risorse in cui eseguire la distribuzione e un nome per la distribuzione (-n è facoltativo).

Per utilizzare un file dei parametri:

    azure group deployment create -f <PathToTemplate> -e <PathToParameterFile> -g <resourcegroupname> -n <deploymentname>

* `<PathToTemplate>` è il percorso del file azuredeploy.json creato nel passaggio 1. Se il nome del percorso include spazi, racchiudere il parametro tra virgolette doppie.
* `<PathToParameterFile>` è il percorso del file azuredeploy.parameters.json creato nel passaggio 1. Se il nome del percorso include spazi, racchiudere il parametro tra virgolette doppie.
* `<resourcegroupname>` è il nome del gruppo di risorse esistente in cui si desidera aggiungere un account database di DocumentDB.
* `<deploymentname>` è il nome facoltativo della distribuzione.

Input di esempio:

    azure group deployment create -f azuredeploy.json -e azuredeploy.parameters.json -g new_res_group -n azuredeploy

OPPURE per specificare il parametro del nome account del database senza un file dei parametri e ricevere una richiesta per il valore, eseguire il comando seguente:

    azure group deployment create -f <PathToTemplate> -g <resourcegroupname> -n <deploymentname>

Input di esempio che illustra la richiesta e una voce per un account di database denominato samplearmacct:

    azure group deployment create -f azuredeploy.json -g new_res_group -n azuredeploy
    info:    Executing command group deployment create
    info:    Supply values for the following parameters
    databaseAccountName: samplearmacct

Durante il provisioning dell'account, si ricevono le informazioni seguenti:

    info:    Executing command group deployment create
    + Initializing template configurations and parameters
    + Creating a deployment
    info:    Created template deployment "azuredeploy"
    + Waiting for deployment to complete
    +
    +
    info:    Resource 'new_res_group' of type 'Microsoft.DocumentDb/databaseAccounts' provisioning status is Running
    +
    info:    Resource 'new_res_group' of type 'Microsoft.DocumentDb/databaseAccounts' provisioning status is Succeeded
    data:    DeploymentName     : azuredeploy
    data:    ResourceGroupName  : new_res_group
    data:    ProvisioningState  : Succeeded
    data:    Timestamp          : 2015-11-30T18:50:23.6300288Z
    data:    Mode               : Incremental
    data:    CorrelationId      : 4a5d4049-c494-4053-bad4-cc804d454700
    data:    DeploymentParameters :
    data:    Name                 Type    Value
    data:    -------------------  ------  ------------------
    data:    databaseAccountName  String  samplearmacct
    data:    locationName1        String  westus
    info:    group deployment create command OK

In caso di errori, vedere [Risoluzione dei problemi](#troubleshooting).  

Dopo il completamento del comando, lo stato dell'account sarà **Creazione in corso** per alcuni minuti, poi passerà a **Online** quando sarà pronto per l'uso. È possibile controllare lo stato dell'account nel [portale di Azure](https://portal.azure.com)nel pannello **Account DocumentDB** .

## <a id="create-multi-documentdb-account"></a>Attività: creazione di un account DocumentDB per più aree
DocumentDB ha la funzionalità per [distribuire dati a livello globale][distribute-globally] in varie [aree di Azure](https://azure.microsoft.com/regions/#services). Quando si crea un account DocumentDB, è possibile specificare le aree in cui si desidera che esista il servizio. Le istruzioni in questa sezione consentono di creare un account DocumentDB per più aree. A questo scopo, è necessario usare l'interfaccia della riga di comando di Azure 1.0 con o senza modelli di Resource Manager.

### <a id="create-multi-documentdb-account-cli"></a> Creare un account DocumentDB per più aree tramite l'interfaccia della riga di comando di Azure 1.0 senza modelli di Resource Manager
Creare un account DocumentDB nel gruppo di risorse nuovo o esistente immettendo il comando seguente nel prompt dei comandi:

> [!TIP]
> Se si esegue questo comando in Azure PowerShell o Windows PowerShell, si riceve un errore relativo a un token imprevisto. Eseguire invece questo comando nel prompt dei comandi di Windows.
>
>

    azure resource create -g <resourcegroupname> -n <databaseaccountname> -r "Microsoft.DocumentDB/databaseAccounts" -o 2015-04-08 -l <resourcegrouplocation> -p "{\"databaseAccountOfferType\":\"Standard\",\"ipRangeFilter\":\"<ip-range-filter>\",\"locations\":["{\"locationName\":\"<databaseaccountlocation1>\",\"failoverPriority\":\"<failoverPriority1>\"},{\"locationName\":\"<databaseaccountlocation2>\",\"failoverPriority\":\"<failoverPriority2>\"}"]}"

* `<resourcegroupname>` può usare solo caratteri alfanumerici, punti, caratteri di sottolineatura, il carattere '-' e parentesi e non può terminare con un punto.
* `<resourcegrouplocation>` rappresenta l'area del gruppo di risorse corrente.
* `<ip-range-filter>` Specifica il set di indirizzi IP e di intervalli di indirizzi IP nel formato CIDR da includere come elenco consentito di IP client per un determinato account di database. Gli intervalli di indirizzi IP o i singoli indirizzi IP devono essere delimitati da virgole e non devono contenere spazi. Per altre informazioni, vedere [Supporto del firewall di DocumentDB](documentdb-firewall-support.md).
* `<databaseaccountname>` possono contenere solo lettere minuscole, numeri, il carattere '-' e devono essere compresi fra 3 e 50 caratteri.
* `<databaseaccountlocation1>` e `<databaseaccountlocation2>` devono essere aree in cui DocumentDB è generalmente disponibile. L'elenco corrente delle aree geografiche è disponibile nella [pagina Aree di Azure](https://azure.microsoft.com/regions/#services).

Input di esempio:

    azure resource create -g new_res_group -n samplecliacct -r "Microsoft.DocumentDB/databaseAccounts" -o 2015-04-08 -l westus -p "{\"databaseAccountOfferType\":\"Standard\",\"ipRangeFilter\":\"\",\"locations\":["{\"locationName\":\"westus\",\"failoverPriority\":\"0\"},{\"locationName\":\"eastus\",\"failoverPriority\":\"1\"}"]}"

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

Dopo il completamento del comando, lo stato dell'account sarà **Creazione in corso** per alcuni minuti, poi passerà a **Online** quando sarà pronto per l'uso. È possibile controllare lo stato dell'account nel [portale di Azure](https://portal.azure.com)nel pannello **Account DocumentDB** .

### <a id="create-multi-documentdb-account-cli-arm"></a> Creare un account DocumentDB per più aree tramite l'interfaccia della riga di comando di Azure 1.0 con modelli di Resource Manager
Nelle istruzioni di questa sezione viene descritto come creare un account DocumentDB con un modello di Azure Resource Manager e un file dei parametri facoltativi, entrambi in formato JSON. Utilizzare un modello consente di descrivere esattamente cosa si desidera e ripeterlo senza errori.

Creare un file di modello locale con il contenuto seguente. Denominare il file azuredeploy.json.

    {
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "databaseAccountName": {
                "type": "string"
            },
            "locationName1": {
                "type": "string"
            },
            "locationName2": {
                "type": "string"
            }
        },
        "variables": {},
        "resources": [
            {
                "apiVersion": "2015-04-08",
                "type": "Microsoft.DocumentDb/databaseAccounts",
                "name": "[parameters('databaseAccountName')]",
                "location": "[resourceGroup().location]",
                "properties": {
                    "databaseAccountOfferType": "Standard",
                    "ipRangeFilter": "",
                    "locations": [
                        {
                            "failoverPriority": 0,
                            "locationName": "[parameters('locationName1')]"
                        },
                        {
                            "failoverPriority": 1,
                            "locationName": "[parameters('locationName2')]"
                        }
                    ]
                }
            }
        ]
    }

Il file di modello precedente può essere usato per creare un account DocumentDB con due aree. Per creare l'account con altre aree, aggiungerlo alla matrice "località" e aggiungere i parametri corrispondenti.

Una delle aree deve avere un valore failoverPriority pari a 0, per indicare che questa area deve essere l'[area di scrittura per l'account DocumentDB][scaling-globally]. I valori di priorità di failover devono essere univoci nelle località e il valore di priorità di failover più elevato deve essere inferiore al numero totale di aree. È possibile immettere il valore nella riga di comando o creare un file dei parametri per specificare il valore.

Per creare un file dei parametri, copiare il contenuto seguente in un nuovo file da denominare azuredeploy.parameters.json. Se si prevede di specificare il nome account del database nel prompt dei comandi, è possibile continuare senza creare il file.

    {
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "databaseAccountName": {
                "value": "samplearmacct"
            },
            "locationName1": {
                "value": "westus"
            },
            "locationName2": {
                "value": "eastus"
            }
        }
    }

Nel file azuredeploy.parameters.json aggiornare il campo del valore `"samplearmacct"` con il nome del database che si vuole usare, quindi salvare il file. `"databaseAccountName"` possono contenere solo lettere minuscole, numeri, il carattere '-' e devono essere compresi fra 3 e 50 caratteri. Aggiornare il campo dei valori `"locationName1"` e `"locationName2"` con l'area in cui si desidera creare l'account DocumentDB.

Per creare un account DocumentDB nel gruppo di risorse, eseguire il comando seguente e indicare il percorso del file di modello, il percorso del file dei parametri o il valore del parametro, il nome del gruppo di risorse in cui eseguire la distribuzione e un nome per la distribuzione (-n è facoltativo).

Per utilizzare un file dei parametri:

    azure group deployment create -f <PathToTemplate> -e <PathToParameterFile> -g <resourcegroupname> -n <deploymentname>

* `<PathToTemplate>` è il percorso del file azuredeploy.json creato nel passaggio 1. Se il nome del percorso include spazi, racchiudere il parametro tra virgolette doppie.
* `<PathToParameterFile>` è il percorso del file azuredeploy.parameters.json creato nel passaggio 1. Se il nome del percorso include spazi, racchiudere il parametro tra virgolette doppie.
* `<resourcegroupname>` è il nome del gruppo di risorse esistente in cui si desidera aggiungere un account database di DocumentDB.
* `<deploymentname>` è il nome facoltativo della distribuzione.

Input di esempio:

    azure group deployment create -f azuredeploy.json -e azuredeploy.parameters.json -g new_res_group -n azuredeploy

OPPURE per specificare il parametro del nome account del database senza un file dei parametri e ricevere una richiesta per il valore, eseguire il comando seguente:

    azure group deployment create -f <PathToTemplate> -g <resourcegroupname> -n <deploymentname>

Input di esempio che illustra la richiesta e una voce per un account di database denominato samplearmacct:

    azure group deployment create -f azuredeploy.json -g new_res_group -n azuredeploy
    info:    Executing command group deployment create
    info:    Supply values for the following parameters
    databaseAccountName: samplearmacct

Durante il provisioning dell'account, si ricevono le informazioni seguenti:

    info:    Executing command group deployment create
    + Initializing template configurations and parameters
    + Creating a deployment
    info:    Created template deployment "azuredeploy"
    + Waiting for deployment to complete
    +
    +
    info:    Resource 'new_res_group' of type 'Microsoft.DocumentDb/databaseAccounts' provisioning status is Running
    +
    info:    Resource 'new_res_group' of type 'Microsoft.DocumentDb/databaseAccounts' provisioning status is Succeeded
    data:    DeploymentName     : azuredeploy
    data:    ResourceGroupName  : new_res_group
    data:    ProvisioningState  : Succeeded
    data:    Timestamp          : 2015-11-30T18:50:23.6300288Z
    data:    Mode               : Incremental
    data:    CorrelationId      : 4a5d4049-c494-4053-bad4-cc804d454700
    data:    DeploymentParameters :
    data:    Name                 Type    Value
    data:    -------------------  ------  ------------------
    data:    databaseAccountName  String  samplearmacct
    data:    locationName1        String  westus
    data:    locationName2        String  eastus
    info:    group deployment create command OK

In caso di errori, vedere [Risoluzione dei problemi](#troubleshooting).  

Dopo il completamento del comando, lo stato dell'account sarà **Creazione in corso** per alcuni minuti, poi passerà a **Online** quando sarà pronto per l'uso. È possibile controllare lo stato dell'account nel [portale di Azure](https://portal.azure.com)nel pannello **Account DocumentDB** .

## <a name="troubleshooting"></a>Risoluzione dei problemi
Se vengono visualizzati errori come `Deployment provisioning state was not successful` durante la creazione dell'account di database o del gruppo di risorse, sono disponibili alcune opzioni per la risoluzione dei problemi.

> [!NOTE]
> Se si forniscono caratteri non validi nel nome dell'account di database o si specifica una località in cui non è disponibile DocumentDB, possono verificarsi errori di distribuzione. I nomi account dei database possono contenere solo lettere minuscole, numeri, il carattere '-' e devono essere compresi fra 3 e 50 caratteri. Tutte le località degli account di database valide sono disponibili nella [pagina Aree di Azure](https://azure.microsoft.com/regions/#services).
>
>

* Se l'output contiene quanto segue `Error information has been recorded to C:\Users\wendy\.azure\azure.err`,  esaminare le informazioni sull'errore nel file azure.err.
* È possibile trovare informazioni utili nel file di log per il gruppo di risorse. Per visualizzare il file di registro, eseguire il comando seguente:

        azure group log show <resourcegroupname> --last-deployment

    Input di esempio:

        azure group log show new_res_group --last-deployment

    Vedere quindi [Risoluzione dei problemi relativi alle distribuzioni di gruppi di risorse in Azure](../azure-resource-manager/resource-manager-common-deployment-errors.md) per altre informazioni.
* Le informazioni sull'errore sono disponibili anche nel portale di Azure, come illustrato nello screenshot seguente. Per passare alle informazioni sull'errore: fare clic su Gruppi di risorse nell'indice, selezionare il gruppo di risorse in cui si è verificato l'errore, quindi nell'area Informazioni di base del pannello del gruppo di risorse, selezionare la data dell'ultima distribuzione e, nel pannello Cronologia distribuzioni, selezionare la distribuzione non riuscita, infine nel pannello Distribuzione, fare clic su Dettagli operazione con un punto esclamativo rosso. Nel pannello Dettagli operazione, viene visualizzato il messaggio di stato per la distribuzione non riuscita.

    ![Schermata del portale di Azure che illustra come passare al messaggio di errore di distribuzione](media/documentdb-automation-resource-manager-cli/portal-troubleshooting-deploy.png)

## <a name="next-steps"></a>Passaggi successivi
Ora che avete un account di DocumentDB, il passaggio successivo consiste nel creare un database di DocumentDB. È possibile creare un database utilizzando uno dei seguenti:

* Il portale di Azure come descritto in [Creare un database e una raccolta DocumentDB usando il portale di Azure](documentdb-create-collection.md).
* Campioni di C# .NET nel progetto [DatabaseManagement](https://github.com/Azure/azure-documentdb-net/tree/master/samples/code-samples/DatabaseManagement) del repository [azure-documentdb-dotnet](https://github.com/Azure/azure-documentdb-net/tree/master/samples/code-samples) su GitHub.
* Gli [SDK di DocumentDB](https://msdn.microsoft.com/library/azure/dn781482.aspx). DocumentDB dispone di .NET, Java, Python, Node. js e SDK di API JavaScript.

Dopo aver creato il database, è necessario [aggiungere una o più raccolte](documentdb-create-collection.md) al database e quindi [aggiungere documenti](documentdb-view-json-document-explorer.md) alle raccolte.

Quando sono presenti documenti in una raccolta, è possibile usare [DocumentDB SQL](documentdb-sql-query.md) per [eseguire query](documentdb-sql-query.md#ExecutingSqlQueries) nei documenti con [Esplora query](documentdb-query-collections-query-explorer.md) nel portale, l'[API REST](https://msdn.microsoft.com/library/azure/dn781481.aspx) o uno degli [SDK](https://msdn.microsoft.com/library/azure/dn781482.aspx).

Per altre informazioni su DocumentDB, vedere le risorse seguenti:

* [Percorso di apprendimento per DocumentDB](https://azure.microsoft.com/documentation/learning-paths/documentdb/)
* [Modello di risorse e concetti relativi a DocumentDB](documentdb-resources.md)

Per altri modelli da poter usare, vedere [Modelli di avvio rapido di Azure](https://azure.microsoft.com/documentation/templates/).

<!--Reference style links - using these makes the source content way more readable than using inline links-->
[distribute-globally]: https://azure.microsoft.com/en-us/documentation/articles/documentdb-distribute-data-globally
[scaling-globally]: https://azure.microsoft.com/en-us/documentation/articles/documentdb-distribute-data-globally/#scaling-across-the-planet

