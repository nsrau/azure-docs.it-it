---
title: Riusare i modelli in cloud diversi - Azure Resource Manager
description: Sviluppare modelli di Azure Resource Manager che funzionano in modo coerente per ambienti cloud diversi. È possibile creare nuovi modelli o aggiornare quelli esistenti per Azure Stack.
services: azure-resource-manager
documentationcenter: na
author: marcvaneijk
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/09/2018
ms.author: mavane
ms.custom: seodec18
ms.openlocfilehash: 38da6d39d095ce27cdd26719d9b8b752d2921bc0
ms.sourcegitcommit: 19a821fc95da830437873d9d8e6626ffc5e0e9d6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/29/2019
ms.locfileid: "70164757"
---
# <a name="develop-azure-resource-manager-templates-for-cloud-consistency"></a>I modelli di Azure Resource Manager possono essere sviluppati per la coerenza cloud

[!INCLUDE [requires-azurerm](../../includes/requires-azurerm.md)]

Uno dei vantaggi principali di Azure è la coerenza. Gli investimenti per lo sviluppo di una posizione sono riutilizzabili in un'altra. Un modello rende le distribuzioni coerenti e ripetibili in tutti gli ambienti, inclusi i cloud sovrani Azure, Azure e Azure Stack. Per riutilizzare i modelli nei cloud, tuttavia, è necessario considerare le dipendenze specifiche del cloud, come spiega questa guida.

Microsoft offre servizi cloud intelligenti e pronti per l'uso per l'azienda in molte posizioni, tra cui:

* La piattaforma globale Azure supportata dalla rete in crescita dei data center gestiti da Microsoft nelle regioni di tutto il mondo.
* Cloud sovrani isolati, ad esempio Azure Germania, Azure per enti pubblici e Azure Cina (Azure gestito da 21Vianet). I cloud sovrani offrono una piattaforma coerente con molte delle stesse funzionalità a cui i clienti di tutto il mondo Azure hanno accesso.
* Azure Stack, piattaforma su cloud ibrido che consente di distribuire i servizi di Azure dal data center dell'organizzazione. Le aziende possono impostare Azure Stack nei propri data center o utilizzare i servizi Azure dei fornitori di servizi, eseguendo Azure Stack nelle proprie strutture (a volte note come aree ospitate).

Alla base di tutti i cloud, Azure Resource Manager fornisce un'API che consente a un'ampia varietà di interfacce utente di comunicare con la piattaforma Azure. Questa API ti offre potenti funzionalità di infrastruttura come codice. Qualsiasi tipo di risorsa disponibile sulla piattaforma cloud Azure può essere distribuito e configurato con Azure Resource Manager. Con un unico modello, è possibile distribuire e configurare l'applicazione completa in uno stato finale operativo.

![Ambienti Azure](./media/templates-cloud-consistency/environments.png)

La coerenza di Azure globale, dei cloud sovrani, dei cloud ospitati e di una cloud nel data center consente di trarre vantaggio da Azure Resource Manager. È possibile riutilizzare gli investimenti di sviluppo in questi cloud quando si configurano la distribuzione e la configurazione delle risorse basate su modelli.

Tuttavia, anche se i cloud globali, sovrani, ospitati e ibridi forniscono servizi coerenti, non tutti i cloud sono identici. Di conseguenza, è possibile creare un modello con dipendenze da funzionalità disponibili solo in un cloud specifico.

Il resto di questa guida descrive le aree da considerare quando si pianifica di sviluppare nuovi modelli di Azure Resource Manager o di aggiornare quelli esistenti per Azure Stack. In generale, la checklist di controllo deve includere quanto segue:

* Verificare che le funzioni, gli endpoint, i servizi e le altre risorse del modello siano disponibili nelle posizioni di distribuzione di destinazione.
* Memorizzare modelli annidati e artefatti di configurazione in posizioni accessibili per garantire l'accesso attraverso i cloud.
* Utilizzare riferimenti dinamici al posto dei collegamenti e degli elementi di codifica rigida.
* Assicurarsi che i parametri del modello utilizzati lavorino nei cloud di destinazione.
* Verificare che siano disponibili le proprietà specifiche delle risorse nei cloud di destinazione.

Per un'introduzione ai modelli di Azure Resource Manger, vedere [Distribuzione modelli](template-deployment-overview.md).

## <a name="ensure-template-functions-work"></a>Assicurarsi che le funzioni di modello vengano eseguite correttamente

La sintassi di base di un modello di Resource Manager è JSON. I modelli usano un superset di JSON, estendendo la sintassi con espressioni e funzioni. Il processore del linguaggio del modello viene spesso aggiornato per supportare le funzioni di modello aggiuntive. Per una spiegazione dettagliata delle funzioni di modello disponibili, vedere [Funzioni del modello di Gestione risorse di Azure](resource-group-template-functions.md).

Le nuove funzioni di modello che vengono introdotte in Azure Resource Manager non sono immediatamente disponibili nei cloud sovrani o nello Stack di Azure. Per distribuire un modello con successo, tutte le funzioni a cui si fa riferimento nel modello devono essere disponibili nel cloud di destinazione. 

Le funzionalità di Azure Resource Manager saranno sempre introdotte prima di tutto in Azure globale. È possibile utilizzare il seguente script PowerShell per verificare se le nuove funzioni dei template sono disponibili anche in Azure Stack: 

1. Creare un clone del repository GitHub: [https://github.com/marcvaneijk/arm-template-functions](https://github.com/marcvaneijk/arm-template-functions).

1. Dopo aver creato un clone locale del repository, connettersi all’Azure Resource Manager della destinazione con PowerShell.

1. Importare il modulo psm1 ed eseguire il cmdlet Test-AzureRmureRmTemplateFunctions:

   ```powershell
   # Import the module
   Import-module <path to local clone>\AzTemplateFunctions.psm1

   # Execute the Test-AzureRmTemplateFunctions cmdlet
   Test-AzureRmTemplateFunctions -path <path to local clone>
   ```

Lo script distribuisce più modelli ridotti a icona, ognuno dei quali contiene solo funzioni di modello univoche. L'output dello script riporta le funzioni del modello supportate e non disponibili.

## <a name="working-with-linked-artifacts"></a>Utilizzo di artefatti collegati

Un modello può contenere riferimenti ad artefatti collegati e contenere una risorsa di distribuzione che si collega a un altro modello. I modelli collegati (detti anche modelli annidati) vengono recuperati dal Resource Manager in fase di runtime. Un modello può anche contenere riferimenti ad artefatti per estensioni di macchine virtuali (VM). Questi elementi vengono recuperati dall'estensione della macchina virtuale in esecuzione all'interno della macchina virtuale per la configurazione dell'estensione della macchina virtuale durante la distribuzione del modello. 

Le sezioni seguenti descrivono considerazioni sulla coerenza del cloud nello sviluppo di modelli che includono artefatti esterni al modello di distribuzione principale.

### <a name="use-nested-templates-across-regions"></a>Utilizzo di modelli annidati in diverse regioni

I modelli possono essere scomposti in modelli piccoli e riutilizzabili, ognuno dei quali ha uno scopo specifico e può essere riutilizzato negli scenari di distribuzione. Per eseguire una distribuzione, specificare un singolo modello noto come modello principale o master. Questo specifica le risorse da distribuire, ad esempio le reti virtuali, le macchine virtuali e le app Web. Il modello principale può anche contenere un collegamento a un altro modello, il che significa che è possibile nidificare i modelli. Analogamente, un modello annidato può contenere collegamenti ad altri modelli. È possibile nidificare fino a cinque livelli.

Il codice seguente mostra come il parametro templateLink si riferisca a un modello annidato:

```json
"resources": [
  {
     "apiVersion": "2017-05-10",
     "name": "linkedTemplate",
     "type": "Microsoft.Resources/deployments",
     "properties": {
       "mode": "incremental",
       "templateLink": {
          "uri":"https://mystorageaccount.blob.core.windows.net/AzureTemplates/vNet.json",
          "contentVersion":"1.0.0.0"
       }
     }
  }
]
```

Azure Resource Manager valuta il modello principale in fase di runtime e recupera e valuta ogni modello annidato. Dopo che tutti i modelli annidati sono stati recuperati, il modello viene reso bidimensionale e viene avviata un'ulteriore elaborazione.

### <a name="make-linked-templates-accessible-across-clouds"></a>Rendere accessibili i modelli collegati in diversi cloud

Considerare dove e come memorizzare i modelli collegati utilizzati. In fase di runtime, Azure Resource Manager recupera (e quindi richiede l'accesso diretto a) tutti i modelli collegati. Una procedura comune consiste nell'usare GitHub per archiviare i modelli annidati. Un repository GitHub può contenere file che sono accessibili pubblicamente tramite un URL. Sebbene questa tecnica funzioni bene per il cloud pubblico e i cloud sovrani, un ambiente Azure Stack potrebbe trovarsi in una rete aziendale o in un percorso remoto disconnesso, senza alcun accesso Internet in uscita. In questi casi, Azure Resource Manager non riuscirà a recuperare i modelli annidati. 

Una procedura consigliata per le implementazioni tra cloud è quella di archiviare i modelli collegati in una posizione accessibile per il cloud di destinazione. In teoria tutti gli artefatti di distribuzione vengono gestiti in e distribuiti da una pipeline di sviluppo/integrazione continua (CI/CD). In alternativa, è possibile archiviare i modelli annidati in un contenitore di archiviazione BLOB, da cui Azure Resource Manager può recuperarli. 

Poiché l'archiviazione BLOB su ogni cloud utilizza un nome di dominio (FQDN) di endpoint diverso, è possibile configurare il modello con la posizione dei modelli collegati con due parametri. I parametri possono accettare l'input dell'utente al momento della distribuzione. I modelli sono in genere creati e condivisi da più utenti, una procedura consigliata consiste nell'usare un nome standard per questi parametri. Le convenzioni di denominazione contribuiscono a rendere i modelli ancora più riutilizzabili tra regioni, cloud e autori.

Nel codice seguente, `_artifactsLocation` viene usato per puntare a un'unica posizione, che contiene tutti gli elementi correlati alla distribuzione. Si noti che viene fornito un valore predefinito. Al momento della distribuzione, se per `_artifactsLocation` non è specificato alcun valore di input, viene utilizzato il valore predefinito. `_artifactsLocationSasToken` viene usato come input per `sasToken`. Il valore predefinito dovrebbe essere una stringa vuota per gli scenari in cui `_artifactsLocation` non è protetto, ad esempio, un repository GitHub pubblico.

```json
"parameters": {
  "_artifactsLocation": {
    "type": "string",
    "metadata": {
      "description": "The base URI where artifacts required by this template are located."
    },
    "defaultValue": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-custom-script-windows/"
  },
  "_artifactsLocationSasToken": {
    "type": "securestring",
    "metadata": {
      "description": "The sasToken required to access _artifactsLocation."
    },
    "defaultValue": ""
  }
}
```

In tutto il modello, i collegamenti sono generati combinando l'URI di base (dal parametro `_artifactsLocation`) con un percorso relativo all'artefatto e il `_artifactsLocationSasToken`. Il codice seguente mostra come specificare il collegamento per il modello annidato usando la funzione del modello uri:

```json
"resources": [
  {
    "name": "shared",
    "type": "Microsoft.Resources/deployments",
    "apiVersion": "2015-01-01",
    "properties": {
      "mode": "Incremental",
      "templateLink": {
        "uri": "[uri(parameters('_artifactsLocation'), concat('nested/vnet.json', parameters('_artifactsLocationSasToken')))]",
        "contentVersion": "1.0.0.0"
      }
    }
  }
]
```

Usando questo approccio, viene utilizzato il valore predefinito per il parametro `_artifactsLocation`. Se i modelli collegati devono essere recuperati da un percorso diverso, il parametro di input può essere utilizzato in fase di distribuzione per sostituire il valore predefinito, non è necessaria alcuna modifica al modello stesso.

### <a name="use-_artifactslocation-instead-of-hardcoding-links"></a>Usare _artifactsLocation al posto dei link per la codifica rigida

Oltre ad essere usato per i modelli annidati, l'URL nel parametro `_artifactsLocation` è usato come base per tutti gli artefatti correlati di un modello di distribuzione. Alcune estensioni di macchina virtuale includono un collegamento a uno script archiviato all'esterno del modello. Per queste estensioni, non è consigliabile codificare i link in modo rigido. Ad esempio, le estensioni Custom Script e PowerShell DSC possono essere collegate a uno script esterno su GitHub come mostrato: 

```json
"properties": {
  "publisher": "Microsoft.Compute",
  "type": "CustomScriptExtension",
  "typeHandlerVersion": "1.9",
  "autoUpgradeMinorVersion": true,
  "settings": {
    "fileUris": [
      "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-windows/scripts/configure-music-app.ps1"
    ]
  }
}
```

La codifica rigida dei collegamenti allo script impedisce potenzialmente che il modello venga distribuito correttamente in un'altra posizione. Durante la configurazione della risorsa macchina virtuale, l'agente della macchina virtuale in esecuzione all'interno della macchina virtuale avvia il download di tutti gli script collegati all'interno dell'estensione macchina virtuale, quindi li memorizza sul disco locale della macchina virtuale. Questo approccio funziona come i collegamenti ai modelli annidati spiegati in precedenza nella sezione "Utilizzo di modelli annidati in diverse regioni".

Resource Manager recupera i modelli annidati in fase di runtime. Per le estensioni di macchina virtuale, eseguire il recupero degli artefatti esterni dall'agente di macchina virtuale. Oltre al diverso iniziatore del recupero degli artefatti, la soluzione nella definizione del modello è la stessa. Usare il parametro _artifactsLocation con un valore predefinito del percorso base in cui sono memorizzati tutti gli artefatti (inclusi gli script di estensione della macchina virtuale) e il parametro `_artifactsLocationSasToken` per l'input di sasToken.

```json
"parameters": {
  "_artifactsLocation": {
    "type": "string",
    "metadata": {
      "description": "The base URI where artifacts required by this template are located."
    },
    "defaultValue": "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-windows/"
  },
  "_artifactsLocationSasToken": {
    "type": "securestring",
    "metadata": {
      "description": "The sasToken required to access _artifactsLocation."
    },
    "defaultValue": ""
  }
}
```

Per costruire l'URI assoluto di un artefatto, il metodo preferito consiste nell'usare la funzione di modello di uri, anziché la funzione di modello concat. Sostituendo i collegamenti codificati in modo rigido agli script nell'estensione di macchina virtuale con la funzione di modello uri, questa funzionalità nel template è configurata per la coerenza cloud.

```json
"properties": {
  "publisher": "Microsoft.Compute",
  "type": "CustomScriptExtension",
  "typeHandlerVersion": "1.9",
  "autoUpgradeMinorVersion": true,
  "settings": {
    "fileUris": [
      "[uri(parameters('_artifactsLocation'), concat('scripts/configure-music-app.ps1', parameters('_artifactsLocationSasToken')))]"
    ]
  }
}
```

Con questo approccio, tutti gli artefatti dell'installazione, inclusi gli script di configurazione, possono essere memorizzati nella stessa posizione del modello stesso. Per modificare la posizione di tutti i collegamenti, è sufficiente specificare un URL di base diverso per i _parametri artifactsLocation_.

## <a name="factor-in-differing-regional-capabilities"></a>Tenere in considerazione diverse funzionalità a livello di area

Con lo sviluppo agile e il flusso continuo di aggiornamenti e nuovi servizi introdotti in Azure, [le aree possono differire](https://azure.microsoft.com/regions/services/) per quanto riguarda la disponibilità di servizi o aggiornamenti. Dopo rigorosi test interni, i nuovi servizi o gli aggiornamenti ai servizi esistenti vengono di solito introdotti a un ristretto pubblico di clienti che partecipano a un programma di convalida. Dopo la convalida da parte dei clienti, i servizi o gli aggiornamenti sono resi disponibili all'interno di un subset di aree di Azure, quindi introdotti in più aree, distribuiti sui cloud sovrani e potenzialmente resi disponibili anche per i clienti di Azure Stack.

Sapendo che le aree e i cloud di Azure possono differire in base ai servizi disponibili, è possibile prendere alcune decisioni proattive sui modelli. Un buon punto di partenza è esaminare i provider di risorse disponibili per un cloud. Un provider di risorse indica il set di risorse e operazioni disponibili per un servizio di Azure.

Un modello distribuisce e configura le risorse. Un tipo di risorsa viene fornito da un provider di risorse. Ad esempio, il provider di risorse di calcolo (Microsoft. Compute), fornisce più tipi di risorsa, ad esempio virtualMachines e availabilitySets. Ogni provider di risorse fornisce un'API ad Azure Resource Manager definita da un contratto comune, che consente un'esperienza di creazione coerente e unificata tra tutti i provider di risorse. Tuttavia, un fornitore di risorse disponibile in Azure globale potrebbe non essere disponibile in un cloud sovrano o in un’area Azure Stack.

![Provider di risorse](./media/templates-cloud-consistency/resource-providers.png) 

Per verificare il provider di risorse che sono disponibili in un dato cloud, eseguire lo script seguente nell'interfaccia della riga di comando Azure ([CLI](/cli/azure/install-azure-cli)):

```azurecli-interactive
az provider list --query "[].{Provider:namespace, Status:registrationState}" --out table
```

È anche possibile usare il cmdlet di PowerShell seguente per visualizzare i provider di risorse disponibili:

```azurepowershell-interactive
Get-AzureRmResourceProvider -ListAvailable | Select-Object ProviderNamespace, RegistrationState
```

### <a name="verify-the-version-of-all-resource-types"></a>Verificare la versione di tutti i tipi di risorse

Un set di proprietà è comune per tutti i tipi di risorsa, ma ogni risorsa ha anche le proprie proprietà specifiche. Nuove funzionalità e le relative proprietà vengono aggiunti ai tipi di risorse esistenti in alcuni casi tramite una nuova versione dell'API. Una risorsa in un modello dispone della relativa proprietà di versione API - `apiVersion`. Il controllo delle versioni garantisce che una configurazione di risorse esistente in un modello non sia interessata dalle modifiche sulla piattaforma.

Le nuove versioni delle API introdotte nei tipi di risorse esistenti in Azure globale potrebbero non essere immediatamente disponibili in tutte le regioni, nei cloud sovrani o in Azure Stack. Per visualizzare un elenco dei fornitori di risorse disponibili, dei tipi di risorse e delle versioni delle API per un cloud, è possibile utilizzare Esplora risorse nel portale Azure. Nel menu Tutti i servizi cercare Esplora risorse. Espandere il nodo del provider in Esplora risorse per restituire tutti i fornitori di risorse disponibili, i loro tipi di risorse e le versioni API in quel cloud.

Per elencare la versione disponibile delle API per tutti i tipi di risorse in un dato cloud nell’interfaccia della riga di comando di Azure, eseguire lo script seguente:

```azurecli-interactive
az provider list --query "[].{namespace:namespace, resourceType:resourceType[]}"
```

In alternativa, è possibile usare il cmdlet PowerShell seguente:

```azurepowershell-interactive
Get-AzureRmResourceProvider | select-object ProviderNamespace -ExpandProperty ResourceTypes | ft ProviderNamespace, ResourceTypeName, ApiVersions
```

### <a name="refer-to-resource-locations-with-a-parameter"></a>Fare riferimento alla posizione delle risorse con un parametro

Un modello viene sempre distribuito in un gruppo di risorse che si trova in un'area. Oltre la distribuzione stessa, ogni risorsa in un modello include anche una proprietà di posizione che consente di specificare l'area in cui eseguire la distribuzione. Per sviluppare il modello per la coerenza del cloud, è necessario un modo dinamico per fare riferimento alle posizioni delle risorse, in quanto ogni Azure Stack può contenere nomi di posizioni univoci. In genere le risorse vengono distribuite nella stessa area del gruppo di risorse, ma per supportare scenari, ad esempio la disponibilità dell'applicazione tra più aree, può essere utile distribuire le risorse tra le aree.

Anche se è possibile codificare in modo rigido i nomi delle aree quando si specificano le proprietà delle risorse in un modello, questo approccio non garantisce che il modello possa essere distribuito in altri ambienti Azure Stack, perché il nome dell’area molto probabilmente non è disponibile in quel punto.

Per adattarsi alle diverse aree, aggiungere una posizione del parametro di input al modello con un valore predefinito. Se non viene specificato alcun valore durante la distribuzione, verrà utilizzato il valore predefinito. 

La funzione modello `[resourceGroup()]` restituisce un oggetto che contiene le coppie chiave/valore seguenti:

```json
{
  "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}",
  "name": "{resourceGroupName}",
  "location": "{resourceGroupLocation}",
  "tags": {
  },
  "properties": {
    "provisioningState": "{status}"
  }
}
```

Facendo riferimento alla chiave di localizzazione dell'oggetto nel valore predefinito del parametro di input, Azure Resource Manager, in fase di esecuzione, sostituirà la funzione del modello `[resourceGroup().location]` con il nome della posizione del gruppo di risorse su cui il modello viene distribuito.

```json
"parameters": {
  "location": {
    "type": "string",
    "metadata": {
      "description": "Location the resources will be deployed to."
    },
    "defaultValue": "[resourceGroup().location]"
  }
},
"resources": [
  {
    "name": "storageaccount1",
    "type": "Microsoft.Storage/storageAccounts",
    "apiVersion": "2015-06-15",
    "location": "[parameters('location')]",
    ...
```

Con questa funzione di modello, è possibile distribuire il modello in qualsiasi cloud senza conoscere anche i nomi delle aree in anticipo. Inoltre, una posizione per una risorsa specifica nel modello può differire dalla posizione del gruppo di risorse. In questo caso, è possibile configurarlo utilizzando parametri di input aggiuntivi per quella specifica risorsa, mentre le altre risorse nello stesso modello utilizzano ancora il parametro di input della posizione iniziale.

### <a name="track-versions-using-api-profiles"></a>Tenere traccia delle versioni tramite i profili delle API

Può essere molto difficile tenere traccia di tutti i provider di risorse disponibili e delle versioni delle API correlate presenti in Azure Stack. Ad esempio, al momento della scrittura, l'ultima versione di API per **Microsoft.Compute/availabilitySets** in Azure è `2018-04-01`, mentre la versione disponibile di API comune ad Azure e Azure Stack è `2016-03-30`. La versione API comune per **Microsoft.Storage/StoccaggioAccount** condivisa tra tutte le posizioni di Azure e Azure Stack è `2016-01-01`, mentre l'ultima versione di API in Azure è `2018-02-01`.

Per questo motivo, Resource Manager ha introdotto il concetto di profili di API per i modelli. Senza profili API, ogni risorsa in un modello è configurata con un elemento `apiVersion` che descrive la versione API per quella specifica risorsa.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
            "type": "string",
            "metadata": {
                "description": "Location the resources will be deployed to."
            },
            "defaultValue": "[resourceGroup().location]"
        }
    },
    "variables": {},
    "resources": [
        {
            "name": "mystorageaccount",
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2016-01-01",
            "location": "[parameters('location')]",
            "properties": {
                "accountType": "Standard_LRS"
            }
        },
        {
            "name": "myavailabilityset",
            "type": "Microsoft.Compute/availabilitySets",
            "apiVersion": "2016-03-30",
            "location": "[parameters('location')]",
            "properties": {
                "platformFaultDomainCount": 2,
                "platformUpdateDomainCount": 2
            }
        }
    ],
    "outputs": {}
}
```

Una versione di profilo API funge da alias per una singola versione API per tipo di risorsa comune ad Azure e Azure Stack. Invece di specificare una versione di API per ogni risorsa in un modello, si specifica solo la versione del profilo API in un nuovo elemento root chiamato `apiProfile` e si omette l'elemento `apiVersion` per le singole risorse.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "apiProfile": "2018–03-01-hybrid",
    "parameters": {
        "location": {
            "type": "string",
            "metadata": {
                "description": "Location the resources will be deployed to."
            },
            "defaultValue": "[resourceGroup().location]"
        }
    },
    "variables": {},
    "resources": [
        {
            "name": "mystorageaccount",
            "type": "Microsoft.Storage/storageAccounts",
            "location": "[parameters('location')]",
            "properties": {
                "accountType": "Standard_LRS"
            }
        },
        {
            "name": "myavailabilityset",
            "type": "Microsoft.Compute/availabilitySets",
            "location": "[parameters('location')]",
            "properties": {
                "platformFaultDomainCount": 2,
                "platformUpdateDomainCount": 2
            }
        }
    ],
    "outputs": {}
}
```

Il profilo API assicura che le versioni API siano disponibili in tutte le posizioni, quindi non è necessario verificare manualmente le apiVersions che sono disponibili in una posizione specifica. Per garantire che le versioni API a cui si fa riferimento nel profilo API siano presenti in un ambiente Azure Stack, gli operatori Azure Stack devono mantenere aggiornata la soluzione in base alla policy di supporto. Se un sistema è obsoleto da più di sei mesi, è considerato non conforme e l'ambiente deve essere aggiornato.

Il profilo di API non è un elemento obbligatorio in un modello. Anche se si aggiunge l'elemento, questo verrà utilizzato solo per le risorse per le quali non è specificato `apiVersion`. Questo elemento consente di apportare modifiche graduali ma non richiede modifiche ai modelli esistenti.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "apiProfile": "2018–03-01-hybrid",
    "parameters": {
        "location": {
            "type": "string",
            "metadata": {
                "description": "Location the resources will be deployed to."
            },
            "defaultValue": "[resourceGroup().location]"
        }
    },
    "variables": {},
    "resources": [
        {
            "name": "mystorageaccount",
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2016-01-01",
            "location": "[parameters('location')]",
            "properties": {
                "accountType": "Standard_LRS"
            }
        },
        {
            "name": "myavailabilityset",
            "type": "Microsoft.Compute/availabilitySets",
            "location": "[parameters('location')]",
            "properties": {
                "platformFaultDomainCount": 2,
                "platformUpdateDomainCount": 2
            }
        }
    ],
    "outputs": {}
}
```

## <a name="check-endpoint-references"></a>Controllare i riferimenti degli endpoint

Le risorse possono avere riferimenti ad altri servizi nella piattaforma. Ad esempio, un indirizzo IP pubblico può avere un nome DNS pubblico assegnato a esso. Il cloud pubblico, i cloud sovrani e le soluzioni di Azure Stack hanno i propri spazi dei nomi di endpoint distinti. Nella maggior parte dei casi, una risorsa richiede solo un prefisso come input nel modello. Durante la fase di runtime, Azure Resource Manager aggiunge il valore dell'endpoint a esso. Alcuni valori dell'endpoint devono essere specificati in modo esplicito nel modello. 

> [!NOTE]
> Per sviluppare modelli per la coerenza del cloud, non codificare in modo rigido gli spazi dei nomi dell'endpoint.

I due esempi seguenti sono spazi dei nomi degli endpoint comuni che devono essere specificati esplicitamente quando si crea una risorsa:

* Account di archiviazione (BLOB, code, tabelle e file)
* Stringhe di connessione per i database e Cache di Azure per Redis

Gli spazi dei nomi degli endpoint possono anche essere utilizzati nell'output di un modello come informazioni per l'utente al termine della distribuzione. Di seguito sono riportati alcuni esempi comuni:

* Account di archiviazione (BLOB, code, tabelle e file)
* Stringhe di connessione (MySql, SQLServer, SQLAzure, Custom, NotificationHub, ServiceBus, EventHub, ApiHub, DocDb, RedisCache, PostgreSQL)
* Gestione traffico
* domainNameLabel di un indirizzo IP pubblico
* Servizi cloud

In generale, evitare di codificare in modo rigido gli endpoint in un modello. La procedura consigliata consiste nell'utilizzare la funzione di modello di riferimento per recuperare gli endpoint in modo dinamico. Ad esempio, l'endpoint codificato in modo rigido più di frequente è lo spazio dei nomi di endpoint per gli account di archiviazione. Ogni account di archiviazione dispone di un FQDN unico che viene creato concatenando il nome dell'account di archiviazione con lo spazio dei nomi dell'endpoint. Un account di archiviazione BLOB denominato mystorageaccount1 genera FQDN diversi a seconda del cloud:

* **mystorageaccount1.blob.core.windows.net** quando creato nel cloud di Azure globale.
* **mystorageaccount1.blob.core.chinacloudapi.cn** quando creato nel cloud di Azure Cina.

La seguente funzione del modello di riferimento recupera lo spazio dei nomi degli endpoint dal provider di risorse di memorizzazione:

```json
"diskUri":"[concat(reference(concat('Microsoft.Storage/storageAccounts/', variables('storageAccountName')), '2015-06-15').primaryEndpoints.blob, 'container/myosdisk.vhd')]"
```

Sostituendo il valore codificato in modo rigido dell'endpoint dell'account di archiviazione con la funzione di modello `reference`, è possibile utilizzare lo stesso modello per distribuirlo correttamente in ambienti diversi senza apportare modifiche al riferimento dell'endpoint.

### <a name="refer-to-existing-resources-by-unique-id"></a>Fare riferimento alle risorse esistenti dall'ID univoco

È anche possibile fare riferimento a una risorsa esistente dello stesso o di un altro gruppo di risorse, e all'interno della stessa sottoscrizione o di un’altra sottoscrizione, all'interno dello stesso tenant nello stesso cloud. Per recuperare le proprietà delle risorse, è necessario usare l'identificatore univoco per la risorsa stessa. La funzione del modello `resourceId` recupera l'ID univoco di una risorsa come SQL Server come mostrato dal codice seguente: 

```json
"outputs": {
  "resourceId":{
    "type": "string",
    "value": "[resourceId('otherResourceGroup', 'Microsoft.Sql/servers', parameters('serverName'))]"
  }
}
```

È quindi possibile utilizzare la funzione `resourceId` all'interno della funzione del modello `reference` per recuperare le proprietà di un database. L'oggetto restituito contiene la proprietà `fullyQualifiedDomainName` che contiene il valore completo dell'endpoint. Questo valore viene recuperato in fase di runtime e fornisce lo spazio dei nomi degli endpoint specifico per l'ambiente cloud. Per definire la stringa di connessione senza codifica rigida dello spazio dei nomi degli endpoint, si può fare riferimento alla proprietà dell'oggetto restituito direttamente nella stringa di connessione come mostrato:

```json
"[concat('Server=tcp:', reference(resourceId('sql', 'Microsoft.Sql/servers', parameters('test')), '2015-05-01-preview').fullyQualifiedDomainName, ',1433;Initial Catalog=', parameters('database'),';User ID=', parameters('username'), ';Password=', parameters('pass'), ';Encrypt=True;')]"
```

## <a name="consider-resource-properties"></a>Prendere in considerazione le proprietà delle risorse

Le risorse specifiche all'interno degli ambienti Azure Stack hanno proprietà univoche da considerare nel modello.

### <a name="ensure-vm-images-are-available"></a>Verificare che siano disponibili immagini per macchina virtuale

Azure offre una selezione completa di immagini per macchina virtuale. Queste immagini vengono create e preparate per la distribuzione da Microsoft e dai propri partner. Le immagini costituiscono la base per le macchine virtuali nella piattaforma. Tuttavia, un modello cloud coerente deve fare riferimento a solo i parametri disponibili, in particolare, l’editore, l’offerta e lo SKU di immagini per macchina virtuale disponibili per una soluzione di Azure Stack, cloud sovrani di Azure o Azure globale.

Per recuperare un elenco delle immagini per macchina virtuale disponibili in un percorso, eseguire il comando dell’Interfaccia della riga di comando di Azure:

```azurecli-interactive
az vm image list -all
```

È possibile recuperare l'elenco stesso con il cmdlet di Azure PowerShell [Get-AzureRmVMImagePublisher](/powershell/module/az.compute/get-azvmimagepublisher) e specificare il percorso desiderato con il parametro `-Location`. Esempio:

```azurepowershell-interactive
Get-AzureRmVMImagePublisher -Location "West Europe" | Get-AzureRmVMImageOffer | Get-AzureRmVMImageSku | Get-AzureRmVMImage
```

Questo comando richiede un paio di minuti per restituire tutte le immagini disponibili nella regione dell'Europa occidentale del cloud di Azure globale.

Se si rendono disponibili queste immagini per macchina virtuale ad Azure Stack, verrà consumato tutto lo spazio di archiviazione disponibile. Per adattarsi anche alle unità di scala più piccole, Azure Stack consente di selezionare le immagini che si desidera aggiungere a un ambiente.

Il seguente esempio di codice mostra un approccio coerente per fare riferimento ai parametri dell’editore, dell'offerta e dello SKU nei modelli di Azure Resource Manager:

```json
"storageProfile": {
    "imageReference": {
    "publisher": "MicrosoftWindowsServer",
    "offer": "WindowsServer",
    "sku": "2016-Datacenter",
    "version": "latest"
    }
}
```

### <a name="check-local-vm-sizes"></a>Controllare le dimensioni delle macchine virtuali locali

Per sviluppare il modello per la coerenza del cloud, è necessario assicurarsi che la dimensione della macchina virtuale desiderata sia disponibile in tutti gli ambienti di destinazione. Le dimensioni delle macchine virtuali sono un raggruppamento di funzionalità e caratteristiche di prestazioni. Alcune dimensioni delle macchine virtuali dipendono dall'hardware sul quale viene eseguita la macchina virtuale. Ad esempio, se si vuole distribuire una macchina virtuale ottimizzata per GPU, l'hardware che esegue l'hypervisor deve disporre delle GPU dell'hardware.

Quando Microsoft introduce una nuova dimensione di macchina virtuale che ha determinate dipendenze hardware, la dimensione della macchina virtuale viene solitamente resa disponibile in primis in un piccolo sottoinsieme di regioni nel cloud di Azure. In seguito, viene reso disponibile ad altre aree e cloud. Per assicurarsi che le dimensioni della macchina virtuale siano presenti in ogni cloud distribuito, è possibile recuperare le dimensioni disponibili con il comando dell’interfaccia della riga di comando di Azure:

```azurecli-interactive
az vm list-sizes --location "West Europe"
```

Per Azure PowerShell usare:

```azurepowershell-interactive
Get-AzureRmVMSize -Location "West Europe"
```

Per un elenco completo dei servizi disponibili correnti, vedere [Prodotti disponibili in base all'area](https://azure.microsoft.com/global-infrastructure/services/?cdn=disable).

### <a name="check-use-of-azure-managed-disks-in-azure-stack"></a>Controllare l'uso di Azure Managed Disks in Azure Stack

Managed Disks gestisce lo spazio di archiviazione di un tenant di Azure. Invece di creare esplicitamente un account di storage e specificare l'URI per un disco rigido virtuale (VHD), è possibile utilizzare dischi gestiti per eseguire implicitamente queste azioni quando si distribuisce una macchina virtuale. I dischi gestiti migliorano la disponibilità collocando tutti i dischi delle macchine virtuali nello stesso set di disponibilità su unità di archiviazione diverse. Inoltre, i dischi rigidi virtuali esistenti possono essere convertiti dal livello di archiviazione Standard al livello di archiviazione Premium con tempi di inattività notevolmente inferiori.

Anche se i dischi gestiti sono la roadmap per Azure Stack, attualmente non sono supportati. Fino a quando non lo sono, è possibile sviluppare modelli coerenti con il cloud per Azure Stack specificando esplicitamente i dischi rigidi virtuali utilizzando l'elemento `vhd` nel modello per la risorsa di macchina virtuale, come mostrato:

```json
"storageProfile": {
  "imageReference": {
    "publisher": "MicrosoftWindowsServer",
    "offer": "WindowsServer",
    "sku": "[parameters('windowsOSVersion')]",
    "version": "latest"
  },
  "osDisk": {
    "name": "osdisk",
    "vhd": {
      "uri": "[concat(reference(resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName')), '2015-06-15').primaryEndpoints.blob, 'vhds/osdisk.vhd')]"
    },
    "caching": "ReadWrite",
    "createOption": "FromImage"
  }
}
```

Al contrario, per specificare una configurazione del disco gestito in un modello, rimuovere l’elemento `vhd` dalla configurazione del disco.

```json
"storageProfile": {
  "imageReference": {
    "publisher": "MicrosoftWindowsServer",
    "offer": "WindowsServer",
    "sku": "[parameters('windowsOSVersion')]",
    "version": "latest"
  },
  "osDisk": {
    "caching": "ReadWrite",
    "createOption": "FromImage"
  }
}
```

Le stesse modifiche si applicano anche ai [dischi di dati](../virtual-machines/windows/using-managed-disks-template-deployments.md).

### <a name="verify-that-vm-extensions-are-available-in-azure-stack"></a>Verificare che le estensioni di macchina virtuale siano disponibili in Azure Stack

Un'altra considerazione per la coerenza del cloud è l'uso di [estensioni virtuali della macchina](../virtual-machines/windows/extensions-features.md) per configurare le risorse all'interno di una macchina virtuale. Verificare che le estensioni di macchina virtuale siano disponibili in Azure Stack. Un modello può specificare le risorse dedicate per l'estensione di macchina virtuale, la creazione di dipendenze e le condizioni all'interno del modello.

Ad esempio, se si desidera configurare una macchina virtuale che esegue Microsoft SQL Server, l'estensione di macchina virtuale può configurare SQL Server come parte della distribuzione del modello. Considerare cosa succede se il modello di installazione client contiene anche un server applicativo configurato per creare un database sulla macchina virtuale con SQL Server in esecuzione. Oltre a utilizzare un'estensione di macchina virtuale per i server applicativi, è possibile configurare la dipendenza del server applicativo dal ritorno corretto della risorsa di estensione di macchina virtuale di SQL Server. Questo approccio assicura che la macchina virtuale che esegue SQL Server sia configurata e disponibile quando al server di applicazioni viene richiesto di creare il database.

L'approccio dichiarativo del modello permette di definire lo stato finale delle risorse e delle loro interdipendenze, mentre la piattaforma si occupa della logica richiesta per le dipendenze.

#### <a name="check-that-vm-extensions-are-available"></a>Controllare che siano disponibili le estensioni di macchina virtuale

Esistono molti tipi di estensioni di macchina virtuale. Quando si sviluppano modelli per la coerenza del cloud, assicurarsi di usare solo le estensioni che sono disponibili in tutte le aree a cui il modello è destinato.

Per recuperare un elenco di estensioni di macchina virtuale disponibili per una regione specifica (in questo esempio, `myLocation`), eseguire il seguente comando di interfaccia della riga di comando di Azure:

```azurecli-interactive
az vm extension image list --location myLocation
```

È anche possibile eseguire il cmdlet Azure PowerShell [Get-AzureRmVmImagePublisher](/powershell/module/az.compute/get-azvmimagepublisher) e utilizzare `-Location` per specificare la posizione dell'immagine della macchina virtuale. Ad esempio:

```azurepowershell-interactive
Get-AzureRmVmImagePublisher -Location myLocation | Get-AzureRmVMExtensionImageType | Get-AzureRmVMExtensionImage | Select Type, Version
```

#### <a name="ensure-that-versions-are-available"></a>Assicurarsi che siano disponibili le versioni

Poiché le estensioni di macchina virtuale sono risorse di Resource Manager di Microsoft, dispongono delle proprie versioni dell'API. Come illustrato nel codice seguente, il tipo di estensione della macchina virtuale è una risorsa annidata nel provider di risorse Microsoft.Compute.

```json
{
    "apiVersion": "2015-06-15",
    "type": "Microsoft.Compute/virtualMachines/extensions",
    "name": "myExtension",
    "location": "[parameters('location')]",
    ...
```

La versione API della risorsa di estensione di macchina virtuale deve essere presente in tutte le posizioni che si prevede di raggiungere con il modello. La dipendenza dalla posizione funziona come la disponibilità della versione API del provider di risorse discussa in precedenza nella sezione "Verificare la versione di tutti i tipi di risorse".

Per recuperare un elenco delle versioni API disponibili per la risorsa dell'estensione di macchina virtuale, usare il cmdlet [Get-AzureRmResourceProvider](/powershell/module/az.resources/get-azresourceprovider) con il provider di risorse **Microsoft.Compute**, come illustrato:

```azurepowershell-interactive
Get-AzureRmResourceProvider -ProviderNamespace "Microsoft.Compute" | Select-Object -ExpandProperty ResourceTypes | Select ResourceTypeName, Locations, ApiVersions | where {$_.ResourceTypeName -eq "virtualMachines/extensions"}
```

È possibile usare anche le estensioni di macchina virtuale nel set di scalabilità di macchine virtuali. Si applicano le stesse condizioni di posizione. Per sviluppare il modello per la coerenza del cloud, assicurarsi che le versioni delle API siano disponibili in tutte le posizioni pianificate durante la distribuzione. Per recuperare le versioni dell'API della risorsa di estensione di macchina virtuale per i set di scalabilità, usare lo stesso cmdlet di prima, ma specificare il tipo di risorsa per i set di scalabilità della macchina virtuale come mostrato:

```azurepowershell-interactive
Get-AzureRmResourceProvider -ProviderNamespace "Microsoft.Compute" | Select-Object -ExpandProperty ResourceTypes | Select ResourceTypeName, Locations, ApiVersions | where {$_.ResourceTypeName -eq "virtualMachineScaleSets/extensions"}
```

Ogni estensione specifica è dotata di una versione. Questa versione è mostrata nella proprietà `typeHandlerVersion` dell'estensione di macchina virtuale. Assicurarsi che la versione specificata nell'elemento `typeHandlerVersion` delle estensioni di macchina virtuale del modello sia disponibile nelle posizioni in cui si intende distribuire il modello. Ad esempio, il seguente codice specifica la versione 1.7:

```json
{
    "name": "MyCustomScriptExtension",
    "type": "extensions",
    "apiVersion": "2016-03-30",
    "location": "[parameters('location')]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/myVM', copyindex())]"
    ],
    "properties": {
        "publisher": "Microsoft.Compute",
        "type": "CustomScriptExtension",
        "typeHandlerVersion": "1.7",
        ...   
```

Per recuperare un elenco delle versioni disponibili per un'estensione di macchina virtuale specifica, usare il cmdlet [Get-AzureRmVMExtensionImage](/powershell/module/az.compute/get-azvmextensionimage). Nell'esempio seguente vengono recuperate le versioni disponibili per l'estensione PowerShell DSC (Configurazione dello stato desiderato) della macchina virtuale da **myLocation**:

```azurepowershell-interactive
Get-AzureRmVMExtensionImage -Location myLocation -PublisherName Microsoft.PowerShell -Type DSC | FT
```

Per ottenere un elenco di editori, usare il comando [Get-AzureRmVmImagePublisher](/powershell/module/az.compute/get-azvmimagepublisher). Per richiedere il tipo, utilizzare il comando [Get-AzureRmVMExtensionImageType](/powershell/module/az.compute/get-azvmextensionimagetype).

## <a name="tips-for-testing-and-automation"></a>Suggerimenti per il test e l’automazione

Tenere traccia di tutte le impostazioni, capacità e limitazioni correlate durante la creazione di un modello è una vera sfida. L'approccio comune è sviluppare e testare i modelli in un unico cloud prima di utilizzare altre posizioni come destinazione. Tuttavia, prima vengono eseguiti i test nel processo di creazione, meno problemi ci saranno da risolvere e meno sarà necessario riscrivere il codice da parte del team di sviluppo. Le distribuzioni che non riescono a causa delle dipendenze dalla posizione possono richiedere molto tempo per la risoluzione dei problemi. Ecco perché è consigliabile l'esecuzione di test automatizzati non appena possibile nel ciclo di creazione. In definitiva, sarà necessario meno tempo di sviluppo e meno risorse e gli artefatti coerenti con il cloud diventeranno ancora più importanti.

L'immagine seguente mostra un esempio tipico di processo di sviluppo per un team che utilizza un ambiente di sviluppo integrato (IDE). In diverse fasi della sequenza temporale, vengono eseguiti diversi tipi di test. In questo caso, due sviluppatori stanno lavorando sulla stessa soluzione, ma questo scenario si applica allo stesso modo a un singolo sviluppatore o a un team di grandi dimensioni. Ogni sviluppatore tipicamente crea una copia locale di un repository centrale, permettendo a ciascuno di lavorare sulla copia locale senza influire sugli altri che possono lavorare sugli stessi file.

![Flusso di lavoro](./media/templates-cloud-consistency/workflow.png) 

Prendere in considerazione i suggerimenti seguenti per il test e l’automazione:

* Assicurarsi di usare gli strumenti di test. Ad esempio, Visual Studio Code e Visual Studio includono IntelliSense e altre funzioni che possono essere di aiuto nella convalida dei modelli.
* Per migliorare la qualità del codice durante lo sviluppo sull'IDE locale, eseguire un'analisi statica del codice con unit test e test di integrazione. 
* Per un'esperienza migliore durante lo sviluppo iniziale, unit test e test di integrazione devono inviare un avviso solo quando viene rilevato un problema e procedere con i test. In questo modo, è possibile identificare i problemi da affrontare e stabilire la priorità dell'ordine delle modifiche, definito anche sviluppo basato su test (TDD).
* Tenere presente che alcuni test possono essere eseguiti senza essere collegati ad Azure Resource Manager. Altri, come il test della distribuzione dei modelli, richiedono al Resource Manager di eseguire alcune azioni che non possono essere eseguite offline.
* Il test di un modello di distribuzione con l'API di convalida non è uguale a una distribuzione effettiva. Inoltre, anche se si distribuisce un modello da un file locale, tutti i riferimenti ai modelli annidati nel modello vengono recuperati direttamente dal Resource Manager, e gli artefatti a cui si riferiscono le estensioni della macchina virtuale vengono recuperati dall'agente della macchina virtuale in esecuzione all'interno della macchina virtuale distribuita.

## <a name="next-steps"></a>Passaggi successivi

* [Considerazioni sui modelli di Azure Resource Manager](/azure-stack/user/azure-stack-develop-templates)
* [Procedure consigliate per i modelli di Azure Resource Manager](resource-group-authoring-templates.md)
