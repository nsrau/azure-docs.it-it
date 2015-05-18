<properties
   pageTitle="Risoluzione dei problemi relativi alle distribuzioni di gruppi di risorse in Azure"
   description="Descrive i problemi frequenti di distribuzione delle risorse in Azure e spiega come usare il portale di Azure, l'interfaccia della riga di comando di Azure per Mac, Linux e Windows e PowerShell per esaminare le distribuzioni e rilevare eventuali problemi."
   services="virtual-machines"
   documentationCenter=""
   authors="squillace"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="command-line-interface"
   ms.workload="infrastructure"
   ms.date="04/25/2015"
   ms.author="rasquill"/>

# Risoluzione dei problemi relativi alle distribuzioni di gruppi di risorse in Azure

Per evitare errori è molto più semplice controllare in anticipo alcuni aspetti delle distribuzioni, anche se a volte possono non riuscire per vari motivi. Questo documento descrive gli strumenti e le operazioni che consentono di evitare gli errori semplici, scaricare i file di modello ed esaminare i log di distribuzione. Illustra anche le aree principali da considerare durante l'analisi dei log di distribuzione per gli errori.

## Strumenti utili per interagire con Azure
Il modulo AzureResourceManager include cmdlet utili a raccogliere strumenti utili per lavorare con le risorse di Azure dalla riga di comando. I modelli di gruppi di risorse di Azure sono documenti JSON e l'API di gestione delle risorse di Azure accetta e restituisce JSON, quindi gli strumenti di analisi di JSON sono tra gli strumenti più importanti per cercare informazioni sulle risorse e progettare o interagire con i modelli e i file dei parametri di modello.

### Strumenti Mac, Linux e Windows
Se si usa l'interfaccia della riga di comando di Azure per Mac, Linux e Windows, è probabile che si conoscano già strumenti di download standard come **[curl](http://curl.haxx.se/)** e **[wget](https://www.gnu.org/software/wget/)** oppure **[Resty](https://github.com/beders/Resty)** e utilità JSON come **[jq](http://stedolan.github.io/jq/download/)**, **[jsawk](https://github.com/micha/jsawk)** e le librerie di linguaggi per la gestione di JSON. Molti di questi strumenti hanno anche porte per Windows, ad esempio [wget](http://gnuwin32.sourceforge.net/packages/wget.htm). In realtà esistono diversi modi per eseguire Linux e altri strumenti software open source anche in Windows.

Questo argomento include alcuni comandi dell'interfaccia della riga di comando di Azure che è possibile usare con **jq** per ottenere le informazioni specifiche desiderate in modo più efficiente. È necessario scegliere un set di strumenti familiare che consenta di comprendere come usare le risorse di Azure.

### Windows PowerShell

Windows PowerShell ha diversi comandi di base che consentono di eseguire le stesse procedure.

- Usare il cmdlet **[Invoke-WebRequest](https://technet.microsoft.com/library/hh849901%28v=wps.640%29)** per scaricare file quali i modelli di gruppi di risorse o i file dei parametri JSON.
- Usare il cmdlet **[ConvertFrom-Json](https://technet.microsoft.com/library/hh849898%28v=wps.640%29.aspx)** per convertire una stringa JSON in un oggetto personalizzato \([PSCustomObject](https://msdn.microsoft.com/library/windows/desktop/system.management.automation.pscustomobject%28v=vs.85%29.aspx)\) con una proprietà per ogni campo nella stringa JSON.

## Evitare gli errori nell'interfaccia della riga di comando di Azure per Mac, Linux e Windows

L'interfaccia della riga di comando di Azure fornisce diversi comandi per evitare gli errori o, se si verificano, individuarne la causa.

- **azure location list**. Questo comando ottiene i percorsi che supportano ogni tipo di risorsa, ad esempio il provider per le macchine virtuali. Prima di immettere un percorso per una risorsa, usare questo comando per verificare che il percorso supporti il tipo di risorsa.

    Poiché l'elenco dei percorsi può essere lungo e il numero di provider è elevato, è possibile usare gli strumenti per esaminare i provider e i percorsi prima di usare un percorso non ancora disponibile. Lo script seguente usa **jq** per individuare i percorsi in cui è disponibile il provider di risorse per le macchine virtuali Azure. \(\)

        azure location list --json | jq '.[] | select(.name == "Microsoft.Compute/virtualMachines")'
        {
          "name": "Microsoft.Compute/virtualMachines",
          "location": "East US,West US,West Europe,East Asia,Southeast Asia,North Europe"
        }

- **azure group template validate <resource group>**. Questo comando convalida il modello e il parametro di modello prima di usarli. Immettere un modello personalizzato o di una raccolta e i valori dei parametri del modello che si intende usare. Questo cmdlet verifica se il modello è coerente al suo interno e se il valore impostato per il parametro corrisponde al modello.

    L'esempio seguente mostra come convalidare un modello e i parametri richiesti. L'interfaccia della riga di comando di Azure richiede i valori del parametro necessari.

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

## Informazioni per risolvere i problemi di distribuzione con l'interfaccia della riga di comando di Azure

- **azure group log show <resource group>**: questo comando ottiene le voci del log per ogni distribuzione del gruppo di risorse. Se si verificano problemi, esaminare innanzitutto i log di distribuzione.

        info:    Executing command group log show
        info:    Getting group logs
        data:    ----------
        data:    EventId:              <guid>
        data:    Authorization:
        data:                          action: Microsoft.Network/networkInterfaces/write
        data:                          role:   Subscription Admin
        data:                          scope:  /subscriptions/xxxxxxxxxxx/resourcegroups/templates/
                                               providers/Microsoft.Network/
                                               networkInterfaces/myNic
        data:    ResourceUri:          /subscriptions/xxxxxxxxxxxx/resourcegroups/templates/providers/
                                       Microsoft.Network/networkInterfaces/myNic
        data:    SubscriptionId:       <guid>
        data:    EventTimestamp (UTC): Wed Apr 22 2015 05:53:31 GMT+0000 (UTC)
        data:    OperationName:        Microsoft.Network/networkInterfaces/write
        data:    OperationId:          <guid>
        data:    Status:               Started
        data:    SubStatus:
        data:    Caller:
        data:    CorrelationId:        <guid>
        data:    Description:
        data:    HttpRequest:          clientRequestId: <guid>
                                       clientIpAddress: 000.000.00.000
                                       method:          PUT

        data:    Level:                Informational
        data:    ResourceGroup:        templates
        data:    ResourceProvider:     Microsoft.Network
        data:    EventSource:          Microsoft Resources
        data:    Properties:           requestbody: {"location":"West US","properties
                                       ":{"ipConfigurations":[{"
                                       name":"ipconfig1","properties":{"
                                       privateIPAllocationMethod

                                       ":"Dynamic","publicIPAddress":{"id":"/
                                       subscriptions/
                                       <guid>/
                                       resourceGroups/
                                       templates/providers/Microsoft.Network/
                                       publicIPAddresses/
                                       myPublicIP"},"subnet":{"idThe AzureResourceManager module includes cmdlets that ":"/subscriptions/
                                       <guid>/resourceGroups/templates/
                                       providers/
                                       Microsoft.Network/virtualNetworks/myVNET/subnets/
                                       Subnet-1
                                       "}}}]}}

        Use the **--last-deployment** option to retrieve only the log for the most recent deployment. The following script uses the **--json** option and **jq** to search the log for deployment failures.

        azure group log show templates --json | jq '.[] | select(.status.value == "Failed")'

        {
          "claims": {
            "aud": "https://management.core.windows.net/",
            "iss": "https://sts.windows.net/<guid>/",
            "iat": "1429678549",
            "nbf": "1429678549",
            "exp": "1429682449",
            "ver": "1.0",
            "http://schemas.microsoft.com/identity/claims/tenantid": "<guid>",
            "http://schemas.microsoft.com/identity/claims/objectidentifier": "<guid>",
            "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "ahmet@contoso.onmicrosoft.com",
            "puid": "XXXXXXXXXXXXXX",
            "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier": "<hash string>",
            "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname": "ahmet",
            "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname": "",
            "name": "Friendly Name",
            "http://schemas.microsoft.com/claims/authnmethodsreferences": "pwd",
            "groups": "<guid>",
            "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name": "ahmet@contoso.onmicrosoft.com",
            "appid": "<guid>",
            "appidacr": "0",
            "http://schemas.microsoft.com/identity/claims/scope": "user_impersonation",
            "http://schemas.microsoft.com/claims/authnclassreference": "1"
          },
          "properties": {},
          "authorization": {
            "action": "Microsoft.Resources/subscriptions/resourcegroups/deployments/write",
            "role": "Subscription Admin",
            "scope": "/subscriptions/<guid>/resourcegroups/templates/deployments/basic-vm-version-0.1"
          },
          "eventChannels": "Operation",
          "eventDataId": "<guid>",
          "correlationId": "<guid>",
          "eventName": {
            "value": "EndRequest",
            "localizedValue": "End request"
          },
          "eventSource": {
            "value": "Microsoft.Resources",
            "localizedValue": "Microsoft Resources"
          },
          "level": "Error",
          "resourceGroupName": "templates",
          "resourceProviderName": {
            "value": "Microsoft.Resources",
            "localizedValue": "Microsoft Resources"
          },
          "resourceUri": "/subscriptions/<guid>/resourcegroups/templates/deployments/basic-vm-version-0.1",
          "operationId": "<guid>",
          "operationName": {
            "value": "Microsoft.Resources/subscriptions/resourcegroups/deployments/write",
            "localizedValue": "Update deployment"
          },
          "status": {
            "value": "Failed",
            "localizedValue": "Failed"
          },
          "subStatus": {},
          "eventTimestamp": "2015-04-22T05:53:40.8150293Z",
          "submissionTimestamp": "2015-04-22T05:54:00.6728843Z",10037FFE8E80BB65
          "subscriptionId": "<guid>"
        }


- **opzioni --verbose e -vv**: usare l'opzione **--verbose** per impostare la modalità su verbose per visualizzare i passaggi eseguiti dalle operazioni in `stdout`. Per la cronologia completa della richiesta, inclusi i passaggi abilitati da **-verbose**, usare l'opzione **- vv**. I messaggi spesso forniscono indicazioni importanti sulla causa degli errori.

- **Le credenziali di Azure non sono state configurate o sono scadute:**: per aggiornare le credenziali nella sessione dell'interfaccia della riga di comando di Azure, digitare `azure login`. Per informazioni sugli errori di autenticazione, assicurarsi di avere [configurato correttamente l'interfaccia della riga di comando di Azure](xplat-cli-connect.md).

## Evitare gli errori in Windows PowerShell

Il modulo AzureResourceManager include cmdlet che aiutano a evitare gli errori.


- **Get-AzureLocation**: questo cmdlet ottiene i percorsi che supportano ogni tipo di risorsa. Prima di specificare una posizione per una risorsa, usare questo cmdlet per verificare che la posizione supporti il tipo di risorsa.


- **Test-AzureResourceGroupTemplate**: testare il modello e il parametro di modello prima di usarli. Immettere un modello personalizzato o di una raccolta e i valori dei parametri del modello che si intende usare. Questo cmdlet verifica se il modello è coerente al suo interno e se il valore impostato per il parametro corrisponde al modello.

## Informazioni per risolvere i problemi di distribuzione in Windows PowerShell

- **Get-AzureResourceGroupLog**: questo cmdlet ottiene le voci del log per ogni distribuzione del gruppo di risorse. Se si verificano problemi, esaminare innanzitutto i log di distribuzione.

- **Verbose e Debug**: i cmdlet nel modulo AzureResourceManager chiamano le API REST che eseguono effettivamente le operazioni. Per visualizzare i messaggi restituiti dalle API, impostare la variabile $DebugPreference su "Continue" e usare il parametro comune Verbose nei propri comandi. I messaggi spesso forniscono indicazioni importanti sulla causa degli errori.

- **Le credenziali di Azure non sono state configurate o sono scadute**: per aggiornare le credenziali nella sessione di Windows PowerShell, usare il cmdlet Add-AzureAccount. Le credenziali di un file di impostazioni di pubblicazione non sono sufficienti per i cmdlet del modulo AzureResourceManager.

## Problemi di autenticazione, sottoscrizione, ruolo e quota

Una corretta distribuzione può essere ostacolata da numerosi problemi, tra cui quelli relativi ad autenticazione, autorizzazione e Azure Active Directory. Indipendentemente da come vengono gestiti i gruppi di risorse di Azure, l'identità usata per accedere al proprio account deve essere un oggetto di Azure Active Directory o un'entità servizio, denominati anche ID aziendale o dell'istituto di istruzione o ID aziendale.

Tuttavia, Azure Active Directory consente all'utente o all'amministratore di controllare con un'elevata precisione quali identità possono accedere e a quali risorse. Se le distribuzioni non riescono, esaminare le richieste per individuare eventuali problemi di autenticazione o di autorizzazione ed esaminare anche i log di distribuzione per il gruppo di risorse. È possibile che non si disponga delle autorizzazioni per tutte le risorse. Con l'interfaccia della riga di comando di Azure è possibile esaminare i tenant di Azure Active Directory e gli utenti che usano i comandi `azure ad`. Per un elenco completo di comandi dell'interfaccia della riga di comando di Azure, vedere [Uso dell'interfaccia della riga di comando di Azure per Mac, Linux e Windows con Gestione delle risorse di Azure](azure-cli-arm-commands.md).

Alcuni problemi potrebbero verificarsi anche quando una distribuzione raggiunge una quota predefinita, per ogni gruppo di risorse, sottoscrizioni, account o per altri ambiti. Confermare di disporre delle risorse necessarie per una corretta distribuzione. Per informazioni complete sulle quote, vedere [Sottoscrizione di Azure e limiti dei servizi, quote e vincoli](azure-subscription-service-limits.md).


## Problemi relativi alla modalità dell'interfaccia della riga di comando di Azure e PowerShell

È possibile che le risorse di Azure distribuite tramite l'API di gestione dei servizi o il portale classico non siano visibili usando l'API di gestione delle risorse o il portale di Azure. È importante gestire le risorse con la stessa API di gestione o lo stesso portale usati per crearle. Se una risorsa non viene più visualizzata, verificare che sia disponibile usando l'altra API di gestione o l'altro portale.

## Problemi di registrazione del provider di risorse di Azure

Le risorse vengono gestite dal provider di risorse ed è possibile abilitare un account o una sottoscrizione per usare un provider specifico. Se è abilitato l'uso di un provider, è anche necessario registrarlo. La maggior parte dei provider, ma non tutti, vengono registrati automaticamente dal portale di Azure o dall'interfaccia della riga di comando che si sta usando.

Per vedere se il provider è registrato per l'uso dell'interfaccia della riga di comando di Azure, usare il comando `azure provider list` \(di seguito è riportato un esempio troncato dell'output\).

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

    Again, if you want more information about providers, including their regional availability, type `azure provider list --json`. The following selects only the first one in the list to view:

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

## Informazioni su una distribuzione eseguita correttamente per i modelli personalizzati

Se si usano i modelli creati, è importante comprendere che il sistema di gestione delle risorse di Azure indica la corretta esecuzione di una distribuzione quando tutti i provider vengono restituiti correttamente dalla distribuzione. Ciò significa che tutti gli elementi del modello sono stati distribuiti per l'utilizzo.

Tuttavia, questo non implica necessariamente che il gruppo di risorse è **attivo e disponibile per gli utenti**. Ad esempio, la maggior parte delle distribuzioni richiede la distribuzione per scaricare gli aggiornamenti, attendere altre risorse esterne al modello oppure installare script complessi o altre attività eseguibili che Azure non rileva perché non sono attività registrate da un provider. In questi casi potrebbe trascorrere altro tempo prima che le risorse siano effettivamente pronte per l'uso. Di conseguenza, è necessario che lo stato di distribuzione venga completato correttamente prima di poter usare la distribuzione.

Per impedire che Azure segnali lo stato di completamento della distribuzione, tuttavia, è possibile creare uno script personalizzato per il modello personalizzato usando, ad esempio, [CustomScriptExtension](http://azure.microsoft.com/blog/2014/08/20/automate-linux-vm-customization-tasks-using-customscript-extension/), che consente di monitorare la conformità dell'intera distribuzione a livello di sistema e di restituire lo stato di completamento corretto solo quando gli utenti possono interagire con l'intera distribuzione. Per assicurarsi che l'estensione specificata sia l'ultima a essere eseguita, usare la proprietà **dependsOn** nel modello. Per un esempio, vedere [qui](https://msdn.microsoft.com/library/azure/dn790564.aspx).

## Unione di modelli

In alcuni casi potrebbe essere necessario unire due modelli o avviare un modello figlio da un modello padre. Per farlo, usare una risorsa di distribuzione all'interno del modello master per distribuire un modello figlio.


    {
            "name": "instance01",
            "type": "Microsoft.Resources/deployments",
            "apiVersion": "2015-01-01",
            "properties": {
                "mode": "Incremental",
                "templateLink": {
                    "uri": "https://mystore.blob.windows.net/azurermtemplates/my-child-template.json",
                    "contentVersion": "1.0.0.0"
                },
                "parameters": {
                    "storageAccountName": { "value": "[variables('stgAcctName1')]" },
                    "adminUsername": { "value": "[parameters('adminUsername')]" },
                    "adminPassword": { "value": "[parameters('adminPassword')]" }
                }
            }
    }


## Gruppi di risorse incrociati

È possibile usare una risorsa esterna al gruppo di risorse corrente in cui viene distribuito un modello. Nel caso più comune viene usato un account di archiviazione o una rete virtuale in un gruppo di risorse alternativo. Questo passaggio in genere è necessario per evitare che l'eliminazione del gruppo di risorse che contiene le macchine virtuali causi l'eliminazione dei BLOB del disco rigido virtuale o di una rete virtuale usati da più gruppi di risorse. L'esempio seguente mostra come usare facilmente una risorsa di un gruppo di risorse esterno:


    {
      "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json",
      "contentVersion": "1.0.0.0",
      "parameters": {
          "virtualNetworkName": {
              "type": "string"
          },
          "virtualNetworkResourceGroup": {
              "type": "string"
          },
          "subnet1Name": {
              "type": "string"
          },
          "nicName": {
              "type": "string"
          }
      },
      "variables": {
          "vnetID": "[resourceId(parameters('virtualNetworkResourceGroup'), 'Microsoft.Network/virtualNetworks', parameters('virtualNetworkName'))]",
          "subnet1Ref": "[concat(variables('vnetID'),'/subnets/', parameters('subnet1Name'))]"
      },
      "resources": [
      {
          "apiVersion": "2015-05-01-preview",
          "type": "Microsoft.Network/networkInterfaces",
          "name": "[parameters('nicName')]",
          "location": "[parameters('location')]",
          "properties": {
              "ipConfigurations": [{
                  "name": "ipconfig1",
                  "properties": {
                      "privateIPAllocationMethod": "Dynamic",
                      "subnet": {
                          "id": "[variables('subnet1Ref')]"
                      }
                  }
              }]
           }
      }]

    }



## Passaggi successivi

Per informazioni su come creare i modelli, leggere [Creazione di modelli di Gestione risorse di Azure](./resource-group-authoring-templates.md) e andare alla sezione [Repository AzureRMTemplates](https://github.com/azurermtemplates/azurermtemplates) per esempi distribuibili. Un esempio di proprietà **dependsOn** è il [modello Bilanciamento del carico con regola NAT in ingresso](https://github.com/azurermtemplates/azurermtemplates/blob/master/101-create-internal-loadbalancer/azuredeploy.json).

<!--Image references-->
[5]: ./media/markdown-template-for-new-articles/octocats.png
[6]: ./media/markdown-template-for-new-articles/pretty49.png
[7]: ./media/markdown-template-for-new-articles/channel-9.png
[8]: ./media/markdown-template-for-new-articles/copytemplate.png

<!--Reference style links - using these makes the source content way more readable than using inline links-->
[gog]: http://google.com/
[yah]: http://search.yahoo.com/
[msn]: http://search.msn.com/

<!--HONumber=52-->
