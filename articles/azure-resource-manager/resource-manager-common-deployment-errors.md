---
title: Risolvere errori comuni durante la distribuzione di risorse in Azure | Microsoft Docs
description: Descrive come risolvere errori comuni durante la distribuzione di risorse in Azure con Azure Resource Manager.
services: azure-resource-manager
documentationcenter: 
tags: top-support-issue
author: tfitzmac
manager: timlt
editor: tysonn
keywords: errore di distribuzione, distribuzione di azure, distribuire in azure
ms.assetid: c002a9be-4de5-4963-bd14-b54aa3d8fa59
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/18/2017
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: 5aa0677e6028c58b7a639f0aee87b04e7bd233a0
ms.openlocfilehash: 2093c6220ea01a83b7e43b3084d13b719feca3ca


---
# <a name="troubleshoot-common-azure-deployment-errors-with-azure-resource-manager"></a>Risolvere errori comuni durante la distribuzione di risorse in Azure con Azure Resource Manager
Questo argomento illustra come risolvere alcuni errori comuni che possono verificarsi durante la distribuzione di risorse in Azure.

## <a name="two-types-of-errors"></a>Due tipi di errori
I possibili errori sono di due tipi:

* errori di convalida
* errori di distribuzione

L'immagine seguente mostra il registro attività di una sottoscrizione. In due distribuzioni si sono verificate tre operazioni. Nella prima distribuzione il modello ha superato la convalida, ma non ha permesso di creare risorse (**scrittura di distribuzioni**). Nella seconda distribuzione il modello non ha superato la convalida e non ha permesso di procedere alla **scrittura di distribuzioni**.

![mostra codice di errore](./media/resource-manager-common-deployment-errors/show-activity-log.png)

Gli errori di convalida sono causati da scenari già predisposti a creare problemi. Tra di essi figurano gli errori di sintassi presenti in un modello oppure il tentativo di distribuire risorse superiori alle quote di sottoscrizione. Gli errori di distribuzione sorgono da condizioni che si verificano durante il processo di distribuzione. Ad esempio, un errore di distribuzione può verificarsi quando si tenta di accedere a una risorsa distribuita in parallelo.

Entrambi i tipi di errore restituiscono un codice necessario per risolvere i problemi della distribuzione. Entrambi i tipi di errori vengono visualizzati nel [log attività](resource-group-audit.md). Gli errori di convalida non sono invece visualizzati nella cronologia della distribuzione, poiché quest'ultima non è mai stata avviata.


## <a name="error-codes"></a>Codici di errore

In questo argomento sono descritti i codici di errore seguenti:

* [AccountNameInvalid](#accountnameinvalid)
* [Authorization failed](#authorization-failed)
* [BadRequest](#badrequest)
* [DeploymentFailed](#deploymentfailed)
* [InvalidContentLink](#invalidcontentlink)
* [InvalidTemplate](#invalidtemplate)
* [MissingSubscriptionRegistration](#noregisteredproviderfound)
* [NotFound](#notfound)
* [NoRegisteredProviderFound](#noregisteredproviderfound)
* [OperationNotAllowed](#quotaexceeded)
* [ParentResourceNotFound](#parentresourcenotfound)
* [QuotaExceeded](#quotaexceeded)
* [RequestDisallowedByPolicy](#requestdisallowedbypolicy)
* [ResourceNotFound](#notfound)
* [SkuNotAvailable](#skunotavailable)
* [StorageAccountAlreadyExists](#storagenamenotunique)
* [StorageAccountAlreadyTaken](#storagenamenotunique)

### <a name="deploymentfailed"></a>DeploymentFailed

Questo codice di errore indica un errore di distribuzione generale, ma non consente di avviare la risoluzione dei problemi. Il codice di errore effettivamente utile per risolvere il problema si trova in genere un livello sotto tale errore. L'immagine seguente illustra ad esempio il codice di errore **RequestDisallowedByPolicy** sotto l'errore di distribuzione.

![mostra codice di errore](./media/resource-manager-common-deployment-errors/error-code.png)

### <a name="skunotavailable"></a>SkuNotAvailable

Quando si distribuisce una risorsa, in genere una macchina virtuale, è possibile che venga visualizzato il codice di errore e il messaggio di errore seguenti:

```
Code: SkuNotAvailable
Message: The requested tier for resource '<resource>' is currently not available in location '<location>' 
for subscription '<subscriptionID>'. Please try another tier or deploy to a different location.
```

Questo errore viene visualizzato quando lo SKU della risorsa selezionato, ad esempio le dimensioni della macchina virtuale, non è disponibile per il percorso selezionato. Per risolvere questo problema, è necessario determinare gli SKU disponibili in un'area. Per trovare gli SKU disponibili è possibile usare il portale o un'operazione REST.

- Per usare il portale, accedere al [portale](https://portal.azure.com) e aggiungere una risorsa tramite l'interfaccia. Quando si impostano i valori, vengono visualizzati gli SKU disponibili per tale risorsa. Non è necessario completare la distribuzione.

    ![SKU disponibili](./media/resource-manager-common-deployment-errors/view-sku.png)

- Per usare l'API REST per le macchine virtuali, inviare la richiesta seguente:

  ```HTTP 
  GET
  https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Compute/skus?api-version=2016-03-30
  ```

  Le aree e gli SKU disponibili vengono restituiti nel formato seguente:

  ```json
  {
    "value": [
      {
        "resourceType": "virtualMachines",
        "name": "Standard_A0",
        "tier": "Standard",
        "size": "A0",
        "locations": [
          "eastus"
        ],
        "restrictions": []
      },
      {
        "resourceType": "virtualMachines",
        "name": "Standard_A1",
        "tier": "Standard",
        "size": "A1",
        "locations": [
          "eastus"
        ],
        "restrictions": []
      },
      ...
    ]
  }    
  ```

Se non si riesce a trovare uno SKU appropriato in tale area o un'area alternativa che soddisfi le esigenze aziendali, contattare il [supporto di Azure](https://portal.azure.com/#create/Microsoft.Support).

### <a name="invalidtemplate"></a>InvalidTemplate
Questo errore può essere causato da diversi tipi di errori.

- Errore di sintassi

   Se viene visualizzato un messaggio di errore che indica che la convalida del modello non è riuscita, potrebbe trattarsi di un problema di sintassi nel modello.

  ```
  Code=InvalidTemplate
  Message=Deployment template validation failed
  ```

   Non è insolito commettere questo errore, perché le espressioni del modello possono essere complesse. Ad esempio, l'assegnazione di nome seguente per un account di archiviazione contiene un set di parentesi, tre funzioni, tre set di parentesi, un set di virgolette singole e una proprietà:

  ```json
  "name": "[concat('storage', uniqueString(resourceGroup().id))]",
  ```

   Se non si specifica la sintassi corrispondente, il modello produce un valore diverso dal previsto.

   Quando viene visualizzato questo tipo di errore, esaminare attentamente la sintassi dell'espressione. È consigliabile usare un editor JSON come [Visual Studio](vs-azure-tools-resource-groups-deployment-projects-create-deploy.md) o [Visual Studio Code](resource-manager-vs-code.md) che può segnalare gli errori di sintassi.

- Lunghezze di segmenti non valide

   Un altro errore di modello non valido si verifica quando il nome della risorsa non è nel formato corretto.

  ```
  Code=InvalidTemplate
  Message=Deployment template validation failed: 'The template resource {resource-name}'
  for type {resource-type} has incorrect segment lengths.
  ```

   Il nome di una risorsa a livello di radice deve contenere un segmento in meno rispetto al tipo di risorsa. Ogni segmento si differenzia mediante una barra. Nell'esempio seguente, il tipo ha due segmenti e il nome un segmento, quindi è un **nome valido**.

  ```json
  {
    "type": "Microsoft.Web/serverfarms",
    "name": "myHostingPlanName",
    ...
  }
  ```

   L'esempio successivo invece **non è un nome valido** , perché ha lo stesso numero di segmenti del tipo.

  ```json
  {
    "type": "Microsoft.Web/serverfarms",
    "name": "appPlan/myHostingPlanName",
    ...
  }
  ```

   Per le risorse figlio, il tipo e il nome devono avere lo stesso numero di segmenti. Questo è dovuto al fatto che il nome completo e il tipo dell'elemento figlio includono il nome e il tipo dell'elemento padre. Di conseguenza, il nome completo ha comunque un segmento in meno rispetto al tipo completo.

  ```json
  "resources": [
      {
          "type": "Microsoft.KeyVault/vaults",
          "name": "contosokeyvault",
          ...
          "resources": [
              {
                  "type": "secrets",
                  "name": "appPassword",
                  ...
              }
          ]
      }
  ]
  ```

   Ottenere il numero di segmenti corretto può essere difficile con i tipi di Resource Manager applicati ai provider di risorse. Per applicare un blocco di risorsa a un sito Web, ad esempio, è necessario un tipo con quattro segmenti. Il nome è quindi costituito da tre segmenti:

  ```json
  {
      "type": "Microsoft.Web/sites/providers/locks",
      "name": "[concat(variables('siteName'),'/Microsoft.Authorization/MySiteLock')]",
      ...
  }
  ```

- Indice di copia non previsto

   Questo errore **InvalidTemplate** viene visualizzato quando è stato applicato l'elemento **copy** a una parte del modello che non supporta questo elemento. È possibile applicare l'elemento copy solo a un tipo di risorsa. Copia non è possibile applicare copy a una proprietà all'interno di un tipo di risorsa. Ad esempio, si applica copy a una macchina virtuale, ma non è possibile applicarlo ai dischi del sistema operativo per una macchina virtuale. In alcuni casi, è possibile convertire una risorsa figlio in una risorsa padre per creare un ciclo di copy. Per altre informazioni sull'uso di copy, vedere [Creare più istanze di risorse in Azure Resource Manager](resource-group-create-multiple.md).

- Parametro non valido

   Se il modello specifica i valori consentiti per un parametro e si fornisce un valore diverso da tali valori, verrà visualizzato un messaggio simile all'errore seguente:

  ```
  Code=InvalidTemplate;
  Message=Deployment template validation failed: 'The provided value {parameter value}
  for the template parameter {parameter name} is not valid. The parameter value is not
  part of the allowed values
  ``` 

   Ricontrollare i valori consentiti nel modello e specificarne uno durante la distribuzione.

- È stata rilevata una dipendenza circolare

   Questo errore viene visualizzato quando le dipendenze tra le risorse impediscono l'avvio della distribuzione. A causa di una combinazione di interdipendenze, due o più risorse attendono altre risorse che sono a propria volta in attesa. Ad esempio, la risorsa&1; dipende dalla risorsa&3;, la risorsa&2; dipende dalla risorsa&1; e la risorsa&3; dipende dalla risorsa&2;. È in genere possibile risolvere questo problema rimuovendo le dipendenze non necessarie. Per suggerimenti sulla risoluzione degli errori relativi alle dipendenze, vedere [Controllare la sequenza di distribuzione](#check-deployment-sequence).

<a id="notfound" />
### <a name="notfound-and-resourcenotfound"></a>NotFound and ResourceNotFound
Quando il modello include il nome di una risorsa che non può essere risolta, verrà visualizzato un errore simile al seguente:

```
Code=NotFound;
Message=Cannot find ServerFarm with name exampleplan.
```

Se si sta provando a distribuire la risorsa mancante nel modello, verificare se sia necessario aggiungere una dipendenza. Azure Resource Manager consente di ottimizzare la distribuzione creando risorse in parallelo, quando ciò è possibile. Se una risorsa deve essere distribuita dopo un'altra, è necessario usare l'elemento **dependsOn** nel modello per creare una dipendenza dall'altra risorsa. Ad esempio, quando si distribuisce un'app Web deve esistere il piano di servizio app. Se non è stato specificato che l'app Web dipende dal piano di servizio app, Azure Resource Manager crea entrambe le risorse contemporaneamente. Verrà visualizzato un messaggio di errore che informa che la risorsa del piano di servizio app non è stata trovata, perché non esiste ancora al momento del tentativo di impostare una proprietà nell'app Web. È possibile prevenire questo errore impostando la dipendenza nell'app Web.

```json
{
  "apiVersion": "2015-08-01",
  "type": "Microsoft.Web/sites",
  "dependsOn": [
    "[variables('hostingPlanName')]"
  ],
  ...
}
```

Per suggerimenti sulla risoluzione degli errori relativi alle dipendenze, vedere [Controllare la sequenza di distribuzione](#check-deployment-sequence).

Questo errore viene visualizzato anche quando la risorsa si trova in un gruppo di risorse diverso rispetto a quello in cui viene distribuita. In tal caso, usare la [funzione resourceId](resource-group-template-functions.md#resourceid) per ottenere il nome completo della risorsa.

```json
"properties": {
    "name": "[parameters('siteName')]",
    "serverFarmId": "[resourceId('plangroup', 'Microsoft.Web/serverfarms', parameters('hostingPlanName'))]"
}
```

Se si prova a usare la funzione [reference](resource-group-template-functions.md#reference) o la funzione [listKeys](resource-group-template-functions.md#listkeys) con una risorsa che non può essere risolta, verrà visualizzato l'errore seguente:

```
Code=ResourceNotFound;
Message=The Resource 'Microsoft.Storage/storageAccounts/{storage name}' under resource
group {resource group name} was not found.
```

Cercare un'espressione contenente la funzione **reference**. Verificare che i valori dei parametri siano corretti.

### <a name="parentresourcenotfound"></a>ParentResourceNotFound

Quando una risorsa è l'elemento padre di un'altra risorsa, deve già esistere prima di creare la risorsa figlio. Se non esiste ancora, viene visualizzato l'errore seguente:

```
Code=ParentResourceNotFound;
Message=Can not perform requested operation on nested resource. Parent resource 'exampleserver' not found."
```

Il nome della risorsa figlio include il nome della risorsa padre. Ad esempio, un database SQL può essere definito come segue:

```json
{
  "type": "Microsoft.Sql/servers/databases",
  "name": "[concat(variables('databaseServerName'), '/', parameters('databaseName'))]",
  ...
```

Tuttavia, se non si specifica una dipendenza dalla risorsa padre, la risorsa figlio può essere distribuita prima dell'elemento padre. Per risolvere questo errore, includere una dipendenza.

```json
"dependsOn": [
    "[variables('databaseServerName')]"
]
```

<a id="storagenamenotunique" />
### <a name="storageaccountalreadyexists-and-storageaccountalreadytaken"></a>StorageAccountAlreadyExists and StorageAccountAlreadyTaken
Per gli account di archiviazione, il nome della risorsa specificato deve essere univoco in Azure. Se non si specifica un nome univoco, verrà visualizzato un errore come il seguente:

```
Code=StorageAccountAlreadyTaken
Message=The storage account named mystorage is already taken.
```

È possibile creare un nome univoco concatenando la convenzione di denominazione con il risultato della funzione [uniqueString](resource-group-template-functions.md#uniquestring) .

```json
"name": "[concat('storage', uniqueString(resourceGroup().id))]",
"type": "Microsoft.Storage/storageAccounts",
```

Se si distribuisce un account di archiviazione con lo steso nome di un account di archiviazione esistente nella sottoscrizione specificando però una diversa località, verrà visualizzato un errore che indica che l'account di archiviazione esiste già in un'altra località. Eliminare l'account di archiviazione esistente oppure specificare la stessa località di tale account.

### <a name="accountnameinvalid"></a>AccountNameInvalid
L'errore **AccountNameInvalid** viene visualizzato quando si prova ad assegnare a un account di archiviazione un nome contenente caratteri non consentiti. I nomi degli account di archiviazione devono essere di lunghezza compresa tra 3 e 24 caratteri e utilizzare solo numeri e lettere minuscole. La funzione [uniqueString](resource-group-template-functions.md#uniquestring) restituisce 13 caratteri. Se si concatena un prefisso al risultato di **uniqueString**, specificare un prefisso composto al massimo da 11 caratteri.

### <a name="badrequest"></a>RichiestaNonValida

Quando per una proprietà si specifica un valore non valido, può comparire lo stato BadRequest. Ad esempio, se per un account di archiviazione si fornisce un valore SKU non corretto, la distribuzione ha esito negativo. Per determinare i valori validi per la proprietà, vedere l'articolo relativo all'[API REST](/rest/api) in relazione al tipo di risorsa distribuito.

<a id="noregisteredproviderfound" />
### <a name="noregisteredproviderfound-and-missingsubscriptionregistration"></a>NoRegisteredProviderFound e MissingSubscriptionRegistration
Quando si distribuisce una risorsa, è possibile che venga visualizzato il codice di errore e il messaggio seguenti:

```
Code: NoRegisteredProviderFound
Message: No registered resource provider found for location {location}
and API version {api-version} for type {resource-type}.
```

In alternativa, si potrebbe ricevere un messaggio analogo che indica:

```
Code: MissingSubscriptionRegistration
Message: The subscription is not registered to use namespace {resource-provider-namespace}
```

Questi errori vengono visualizzati per uno di questi tre motivi:

1. Il provider di risorse non è stato registrato per la sottoscrizione
2. La versione dell'API non è supportata per il tipo di risorsa
3. Il percorso non è supportato per il tipo di risorsa

Il messaggio di errore dovrebbe fornire suggerimenti per le versioni di API e i percorsi supportati. È possibile modificare il modello impostando uno dei valori suggeriti. La maggior parte dei provider, ma non tutti, vengono registrati automaticamente dal portale di Azure o dall'interfaccia della riga di comando che si sta usando. Se non è mai stato usato un provider di risorse specifico, potrebbe essere necessario registrarlo. È possibile ottenere altre informazioni sui provider di risorse tramite PowerShell o l'interfaccia della riga di comando di Azure.

**Portale**

È possibile visualizzare lo stato di registrazione e registrare uno spazio dei nomi del provider di risorse tramite il portale.

1. Selezionare **Provider di risorse** per la propria sottoscrizione.

   ![selezionare i provider di risorse](./media/resource-manager-common-deployment-errors/select-resource-provider.png)

2. Esaminare l'elenco dei provider di risorse e, se necessario, selezionare il link **Registra** per registrare il provider di risorse del tipo che si intende distribuire.

   ![Elenco di provider di risorse](./media/resource-manager-common-deployment-errors/list-resource-providers.png)

**PowerShell**

Per visualizzare lo stato della registrazione, usare **Get-AzureRmResourceProvider**.

```powershell
Get-AzureRmResourceProvider -ListAvailable
```

Per registrare un provider, usare **Register-AzureRmResourceProvider** e specificare il nome del provider di risorse da registrare.

```powershell
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Cdn
```

Per ottenere i percorsi supportati per un tipo di risorsa particolare è possibile usare:

```powershell
((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).Locations
```

Per ottenere le versioni di API supportate per un tipo di risorsa particolare è possibile usare:

```powershell
((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).ApiVersions
```

**Interfaccia della riga di comando di Azure**

Per vedere se il provider è registrato, usare il comando `azure provider list` .

```azurecli
azure provider list
```

Per registrare un provider di risorse, usare il comando `azure provider register` e specificare lo *spazio dei nomi* per la registrazione.

```azurecli
azure provider register Microsoft.Cdn
```

Per visualizzare le versioni di API e i percorsi supportati per un provider di risorse, usare:

```azurecli
azure provider show -n Microsoft.Compute --json > compute.json
```

<a id="quotaexceeded" />
### <a name="quotaexceeded-and-operationnotallowed"></a>QuotaExceeded e OperationNotAllowed
Alcuni problemi potrebbero verificarsi quando una distribuzione supera una quota specifica per un gruppo di risorse, le sottoscrizioni, gli account o per altri ambiti. Ad esempio, la sottoscrizione potrebbe essere configurata in modo da limitare il numero di core per un'area. Se si prova a distribuire una macchina virtuale con un numero di core superiore alla quantità consentita, verrà visualizzato un messaggio di errore che informa che la quota è stata superata.
Per informazioni complete sulle quote, vedere [Sottoscrizione di Azure e limiti, quote e vincoli dei servizi](../azure-subscription-service-limits.md).

Per esaminare le quote per i core della sottoscrizione, è possibile usare il comando `azure vm list-usage` nell'interfaccia della riga di comando di Azure. L'esempio seguente mostra che la quota di core per un account della versione di valutazione gratuita è quattro:

```azurecli
azure vm list-usage
```

Che restituisce:

```azurecli
info:    Executing command vm list-usage
Location: westus
data:    Name   Unit   CurrentValue  Limit
data:    -----  -----  ------------  -----
data:    Cores  Count  0             4
info:    vm list-usage command OK
```

Se si distribuisce un modello che crea più di quattro core nell'area Stati Uniti occidentali, verrà visualizzato un errore di distribuzione come il seguente:

```
Code=OperationNotAllowed
Message=Operation results in exceeding quota limits of Core.
Maximum allowed: 4, Current in use: 4, Additional requested: 2.
```

In alternativa, in PowerShell è possibile usare il cmdlet **Get-AzureRmVMUsage** .

```powershell
Get-AzureRmVMUsage
```

Che restituisce:

```powershell
...
CurrentValue : 0
Limit        : 4
Name         : {
                 "value": "cores",
                 "localizedValue": "Total Regional Cores"
               }
Unit         : null
...
```

In questi casi, si deve accedere al portale e rivolgersi all'assistenza per richiedere l'aumento della quota per l'area di destinazione della distribuzione.

> [!NOTE]
> Tenere presente che per i gruppi di risorse, la quota è riferita alle singole aree e non all'intera sottoscrizione. Se è necessario distribuire 30 core nell'area Stati Uniti occidentali, è necessario richiedere 30 core di gestione delle risorse per Stati Uniti occidentali. Se è necessario distribuire 30 core in qualsiasi area a cui si ha accesso, è consigliabile richiedere 30 core di Resource Manager in tutte le aree.
>
>

### <a name="invalidcontentlink"></a>InvalidContentLink
Quando viene visualizzato il messaggio di errore seguente:

```
Code=InvalidContentLink
Message=Unable to download deployment content from ...
```

È probabile che si sia tentato il collegamento a un modello annidato non disponibile. Ricontrollare l'URI specificato per il modello annidato. Se il modello si trova in un account di archiviazione, verificare che l'URI sia accessibile. Potrebbe essere necessario passare un token di firma di accesso condiviso. Per altre informazioni, vedere [Uso di modelli collegati con Gestione risorse di Azure](resource-group-linked-templates.md).

### <a name="requestdisallowedbypolicy"></a>RequestDisallowedByPolicy
Questo errore viene visualizzato quando la sottoscrizione include un criterio che impedisce di eseguire un'azione desiderata in fase distribuzione. Cercare l'identificatore del criterio nel messaggio di errore

```
Policy identifier(s): '/subscriptions/{guid}/providers/Microsoft.Authorization/policyDefinitions/regionPolicyDefinition'
```

e specificarlo in **PowerShell** come parametro **Id** per recuperare i dettagli relativi al criterio che ha bloccato la distribuzione.

```powershell
(Get-AzureRmPolicyAssignment -Id "/subscriptions/{guid}/providers/Microsoft.Authorization/policyDefinitions/regionPolicyDefinition").Properties.policyRule | ConvertTo-Json
```

Nell'**interfaccia della riga di comando di Azure** specificare il nome della definizione del criterio:

```azurecli
azure policy definition show regionPolicyDefinition --json
```

Per altre informazioni sui criteri, vedere [Usare i criteri per gestire le risorse e controllare l'accesso](resource-manager-policy.md).

### <a name="authorization-failed"></a>L'autorizzazione non è riuscita
Si può ricevere un messaggio di errore durante la distribuzione perché l'account o l'entità servizio che prova a distribuire le risorse non ha l'accesso necessario per eseguire tali azioni. Azure Active Directory consente all'utente o all'amministratore di controllare con un'elevata precisione quali identità possono accedere e a quali risorse. Se l'account è assegnato al ruolo Lettore, ad esempio, non è possibile creare nuove risorse. In tal caso verrà visualizzato un messaggio di errore che indica che l'autorizzazione non è riuscita.

Per altre informazioni sul controllo degli accessi in base al ruolo, vedere [Controllo degli accessi in base al ruolo di Azure](../active-directory/role-based-access-control-configure.md).

## <a name="troubleshooting-tricks-and-tips"></a>Suggerimenti per la risoluzione dei problemi

### <a name="enable-debug-logging"></a>Abilitazione della registrazione di debug
Per informazioni importanti sulle modalità di elaborazione della distribuzione, è possibile registrare la richiesta, la risposta o entrambe.

- PowerShell

   In PowerShell impostare il parametro **DeploymentDebugLogLevel** su All, ResponseContent o RequestContent.

  ```powershell
  New-AzureRmResourceGroupDeployment -ResourceGroupName examplegroup -TemplateFile c:\Azure\Templates\storage.json -DeploymentDebugLogLevel All
  ```

   Esaminare il contenuto della richiesta con il cmdlet seguente:

  ```powershell
  (Get-AzureRmResourceGroupDeploymentOperation -DeploymentName storageonly -ResourceGroupName startgroup).Properties.request | ConvertTo-Json
  ```

   In alternativa, esaminare il contenuto della risposta con:

  ```powershell
  (Get-AzureRmResourceGroupDeploymentOperation -DeploymentName storageonly -ResourceGroupName startgroup).Properties.response | ConvertTo-Json
  ```

   Queste informazioni consentono di stabilire se nel modello sia impostato un valore errato.

- Interfaccia della riga di comando di Azure

   Nell'interfaccia della riga di comando di Azure impostare il parametro **--debug-setting** su All, ResponseContent o RequestContent.

  ```azurecli
  azure group deployment create --debug-setting All -f c:\Azure\Templates\storage.json -g examplegroup -n ExampleDeployment
  ```

   Esaminare il contenuto della richiesta e della risposta registrate con il comando seguente:

  ```azurecli
  azure group deployment operation list --resource-group examplegroup --name ExampleDeployment --json
  ```

   Queste informazioni consentono di stabilire se nel modello sia impostato un valore errato.

- Modello annidato

   Per registrare le informazioni di debug relative a un modello nidificato, usare l'elemento **debugSetting**.

  ```json
  {
      "apiVersion": "2016-09-01",
      "name": "nestedTemplate",
      "type": "Microsoft.Resources/deployments",
      "properties": {
          "mode": "Incremental",
          "templateLink": {
              "uri": "{template-uri}",
              "contentVersion": "1.0.0.0"
          },
          "debugSetting": {
             "detailLevel": "requestContent, responseContent"
          }
      }
  }
  ```


### <a name="create-a-troubleshooting-template"></a>Creare un modello per la risoluzione dei problemi
Talvolta il modo più semplice per risolvere i problemi del modello è testarne alcune parti. A tale scopo, è possibile creare un modello semplificato che consente di concentrarsi sulla parte da cui si ritiene abbia origine l'errore. Si supponga, ad esempio, di ricevere un errore quando si fa riferimento a una risorsa. Anziché gestire un intero modello, crearne uno che restituisca la parte potenzialmente problematica. Ciò può aiutare a capire se i parametri trasmessi siano corretti, a usare correttamente le funzioni del modello e a recuperare le risorse desiderate.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageName": {
        "type": "string"
    },
    "storageResourceGroup": {
        "type": "string"
    }
  },
  "variables": {},
  "resources": [],
  "outputs": {
    "exampleOutput": {
        "value": "[reference(resourceId(parameters('storageResourceGroup'), 'Microsoft.Storage/storageAccounts', parameters('storageName')), '2016-05-01')]",
        "type" : "object"
    }
  }
}
```

In alternativa, si supponga di incontrare errori di distribuzione presumibilmente correlati a dipendenze impostate in modo errato. Testare il modello suddividendolo in modelli semplificati. Creare innanzitutto un modello che distribuisce una sola risorsa (ad esempio SQL Server). Quando si è certi che la risorsa sia definita correttamente, aggiungere una risorsa che dipende da essa (ad esempio un database SQL). Una volta definite correttamente queste due risorse, aggiungere altre risorse che dipendono da esse (ad esempio criteri di controllo). Tra una distribuzione di test e l'altra, eliminare il gruppo di risorse per assicurarsi di testare le dipendenze in modo adeguato. 

### <a name="check-deployment-sequence"></a>Controllare la sequenza di distribuzione

Molti errori di distribuzione si verificano quando le risorse vengono distribuite secondo una sequenza imprevista. Questi errori vengono generati quando le dipendenze non sono impostate correttamente. Quando manca una dipendenza necessaria, una risorsa tenta di usare un valore per un'altra risorsa che tuttavia non esiste ancora e viene visualizzato un errore che indica che non è stata trovata una risorsa. Questo tipo di errore può verificarsi in modo intermittente perché i tempi di distribuzione delle singole risorse possono variare. È ad esempio possibile che il primo tentativo di distribuire le risorse abbia esito positivo perché una risorsa obbligatoria viene casualmente completata in tempo, ma che ciò non avvenga al secondo tentativo e che questo abbia quindi esito negativo. 

È tuttavia opportuno evitare di impostare dipendenze non necessarie. La presenza di dipendenze non necessarie prolunga la durata della distribuzione impedendo di distribuire in parallelo risorse che non sono interdipendenti. Si potrebbero anche creare dipendenze circolari che bloccano la distribuzione. La funzione [reference](resource-group-template-functions.md#reference) crea una dipendenza implicita dalla risorsa specificata come parametro nella funzione, se tale risorsa viene distribuita nello stesso modello. Si potrebbero quindi avere dipendenze aggiuntive rispetto a quelle specificate nella proprietà **dependsOn**. La funzione [resourceId](resource-group-template-functions.md#resourceid) non crea una dipendenza implicita né esegue la convalida dell'esistenza della risorsa.

In caso di problemi relativi alle dipendenze, è necessario approfondire l'ordine di distribuzione delle risorse. Per visualizzare l'ordine delle operazioni di distribuzione:

1. Selezionare la cronologia delle distribuzioni per il gruppo di risorse.

   ![selezionare la cronologia delle distribuzioni](./media/resource-manager-common-deployment-errors/select-deployment.png)

2. Selezionare una distribuzione dalla cronologia e fare clic su **Eventi**.

   ![selezionare gli eventi di distribuzione](./media/resource-manager-common-deployment-errors/select-deployment-events.png)

3. Esaminare la sequenza degli eventi per ogni risorsa. Prestare attenzione allo stato di ciascuna operazione. Ad esempio, l'immagine seguente mostra tre account di archiviazione distribuiti in parallelo. Si noti che i tre account di archiviazione vengono avviati contemporaneamente.

   ![distribuzione parallela](./media/resource-manager-common-deployment-errors/deployment-events-parallel.png)

   L'immagine successiva mostra tre account di archiviazione che non vengono distribuiti in parallelo. Il secondo account di archiviazione dipende dal primo e il terzo account di archiviazione dipende dal secondo. Pertanto, il primo account di archiviazione viene avviato, accettato e completato prima che sia avviato il successivo.

   ![distribuzione sequenziale](./media/resource-manager-common-deployment-errors/deployment-events-sequence.png)

Gli scenari reali possono essere molto più complessi, ma è possibile usare la stessa tecnica per individuare quando è stata avviata e completata la distribuzione per ogni risorsa. Esaminare gli eventi di distribuzione per verificare se la sequenza è diversa dal previsto. In tal caso, valutare nuovamente le dipendenze per la risorsa.

Resource Manager identifica le dipendenze circolari durante la convalida del modello. Restituisce quindi un messaggio di errore che indica specificamente la presenza di una dipendenza circolare. Per risolvere una dipendenza circolare:

1. Nel modello trovare la risorsa identificata nella dipendenza circolare. 
2. Esaminare la proprietà **dependsOn** e le occorrenze della funzione **reference** per tale risorsa per verificare le risorse da cui dipende. 
3. Esaminare tali risorse per verificare da quali risorse dipendono. Seguire le dipendenze finché non si rileva una risorsa che dipende dalla risorsa originale.
5. Per le risorse coinvolte nella dipendenza circolare, esaminare attentamente tutte le occorrenze della proprietà **dependsOn** per identificare eventuali dipendenze non necessarie. Rimuovere tali dipendenze. Se non si è certi che una dipendenza sia necessaria, provare a rimuoverla. 
6. Ridistribuire il modello.

La rimozione di valori dalla proprietà **dependsOn** può causare errori durante la distribuzione del modello. Se si verifica un errore, riaggiungere la dipendenza al modello. 

Se con questo approccio non si rivolve la dipendenza circolare, provare a spostare parte della logica di distribuzione in risorse figlio, come estensioni o impostazioni di configurazione. Configurare tali risorse figlio in modo che vengano distribuite dopo le risorse coinvolte nella dipendenza circolare. Si supponga, ad esempio, di distribuire due macchine virtuali e che sia necessario impostare in ognuna proprietà che fanno riferimento all'altra. È possibile eseguire la distribuzione nell'ordine seguente:

1. VM&1;
2. VM&2;
3. L'estensione in VM&1; dipende da VM&1; e VM&2;. L'estensione imposta in VM&1; valori ottenuti da VM&2;.
4. L'estensione in VM&2; dipende da VM&1; e VM&2;. L'estensione imposta in VM&2; valori ottenuti da VM&1;.

Lo stesso approccio è applicabile alle app del servizio app. Provare a spostare i valori di configurazione in una risorsa figlio della risorsa app. È possibile distribuire due app Web nell'ordine seguente:

1. App Web&1;
2. App Web&2;
3. La configurazione per App Web&1; dipende da App Web&1; e App Web&2;. Contiene impostazioni dell'app con valori di App Web&2;.
4. La configurazione per App Web&2; dipende da App Web&1; e App Web&2;. Contiene impostazioni dell'app con valori di App Web&1;.

## <a name="troubleshooting-other-services"></a>Risoluzione dei problemi di altri servizi
Se i codici degli errori di distribuzione precedenti non consentono di risolvere il problema, è possibile cercare indicazioni più dettagliate per la risoluzione dei problemi per ogni servizio di Azure.

La tabella seguente elenca gli argomenti sulla risoluzione dei problemi delle macchine virtuali.

| Errore | Articoli |
| --- | --- |
| Errori dell'estensione script personalizzata |[Errori delle estensioni macchina virtuale Windows](../virtual-machines/virtual-machines-windows-extensions-troubleshoot.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)<br />oppure<br />[Risoluzione degli errori delle estensioni della macchina virtuale Linux di Azure](../virtual-machines/virtual-machines-linux-extensions-troubleshoot.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) |
| Errori di provisioning dell'immagine del sistema operativo |[Errori delle nuove VM Windows](../virtual-machines/virtual-machines-windows-troubleshoot-deployment-new-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)<br />oppure<br />[Errori delle nuove VM Linux](../virtual-machines/virtual-machines-linux-troubleshoot-deployment-new-vm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) |
| Errori di allocazione |[Errori di allocazione delle VM Windows](../virtual-machines/virtual-machines-windows-allocation-failure.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)<br />oppure<br />[Risolvere i problemi relativi a errori di allocazione quando si crea, riavvia o ridimensiona una macchina virtuale Linux in Azure](../virtual-machines/virtual-machines-linux-allocation-failure.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) |
| Errori SSH (Secure Shell) errori durante il tentativo di connessione |[Risolvere i problemi relativi alle connessioni Secure Shell a una macchina virtuale di Azure basata su Linux](../virtual-machines/virtual-machines-linux-troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) |
| Errori di connessione all'applicazione in esecuzione nella macchina virtuale |[Applicazione in esecuzione in una VM Windows](../virtual-machines/virtual-machines-windows-troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)<br />oppure<br />[Risoluzione dei problemi di accesso a un'applicazione in esecuzione su una macchina virtuale di Azure](../virtual-machines/virtual-machines-linux-troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) |
| Errori di connessione Desktop remoto |[Risolvere i problemi di connessioni Desktop remoto a una macchina virtuale di Azure che esegue Windows](../virtual-machines/virtual-machines-windows-troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) |
| Errori di connessione risolti con la ridistribuzione |[Ridistribuzione della macchina virtuale su un nuovo nodo di Azure](../virtual-machines/virtual-machines-windows-redeploy-to-new-node.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) |
| Errori del servizio cloud |[Risolvere eventuali problemi di distribuzione dei servizi cloud](../cloud-services/cloud-services-troubleshoot-deployment-problems.md) |

La tabella seguente elenca gli argomenti sulla risoluzione dei problemi di altri servizi di Azure, in particolare i problemi relativi alla distribuzione o alla configurazione delle risorse. Se occorre assistenza per la risoluzione dei problemi in fase di esecuzione di una risorsa, vedere la documentazione relativa al servizio di Azure specifico.

| Service | Articolo |
| --- | --- |
| Automazione |[Suggerimenti sulla risoluzione dei problemi relativi agli errori comuni in Automazione di Azure](../automation/automation-troubleshooting-automation-errors.md) |
| Azure Stack |[Microsoft Azure Stack troubleshooting (Risoluzione dei problemi di Microsoft Azure Stack)](../azure-stack/azure-stack-troubleshooting.md) |
| Data factory |[Risolvere i problemi di Data factory](../data-factory/data-factory-troubleshoot.md) |
| Service Fabric |[Risolvere i problemi comuni quando si distribuiscono servizi in Azure Service Fabric](../service-fabric/service-fabric-diagnostics-troubleshoot-common-scenarios.md) |
| Site Recovery |[Monitorare e risolvere i problemi di protezione per le macchine virtuali e i server fisici](../site-recovery/site-recovery-monitoring-and-troubleshooting.md) |
| Archiviazione |[Monitoraggio, diagnosi e risoluzione dei problemi del servizio di archiviazione di Microsoft Azure](../storage/storage-monitoring-diagnosing-troubleshooting.md) |
| StorSimple |[Risoluzione dei problemi di distribuzione del dispositivo StorSimple](../storsimple/storsimple-troubleshoot-deployment.md) |
| Database SQL |[Risoluzione dei problemi di connessione al database SQL di Azure](../sql-database/sql-database-troubleshoot-common-connection-issues.md) |
| SQL Data Warehouse |[Risoluzione dei problemi relativi a SQL Data Warehouse di Azure](../sql-data-warehouse/sql-data-warehouse-troubleshoot.md) |

## <a name="next-steps"></a>Passaggi successivi
* Per altre informazioni sulle azioni di controllo, vedere [Operazioni di controllo con Resource Manager](resource-group-audit.md).
* Per altre informazioni sulle azioni che consentono di determinare gli errori di distribuzione, vedere [Visualizzare le operazioni di distribuzione con il portale di Azure](resource-manager-deployment-operations.md).



<!--HONumber=Jan17_HO3-->


