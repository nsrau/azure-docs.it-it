---
title: Criteri delle risorse di Azure | Microsoft Docs
description: "Viene descritto come utilizzare i criteri di Azure Resource Manager per l'impostazione di proprietà delle risorse coerenti durante la distribuzione. È possibile applicare i criteri alla sottoscrizione o a gruppi di risorse."
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: abde0f73-c0fe-4e6d-a1ee-32a6fce52a2d
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/09/2017
ms.author: tomfitz
ms.openlocfilehash: 6faa8b4d259eddb1b26345d8900e829f6ca4dba8
ms.sourcegitcommit: 6a6e14fdd9388333d3ededc02b1fb2fb3f8d56e5
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/07/2017
---
# <a name="resource-policy-overview"></a>Cenni preliminari sui criteri delle risorse
I criteri delle risorse consentono di definire le convenzioni per le risorse nell'organizzazione. Definendo le convenzioni, è possibile controllare i costi e gestire più facilmente le risorse. È ad esempio possibile specificare che vengano consentiti solo determinati tipi di macchine virtuali. In alternativa, è possibile richiedere che tutte le risorse abbiano un tag specifico. I criteri vengono ereditati da tutte le risorse figlio. Se quindi un criterio viene applicato a un gruppo di risorse, è applicabile a tutte le risorse in tale gruppo.

È importante comprendere due concetti sui criteri:

* definizione dei criteri: l'utente descrive quando i criteri vengono applicati e quale azione intraprendere
* assegnazione di criteri: si applica la definizione di criteri a un ambito (sottoscrizione o gruppo di risorse)

In questo argomento viene illustrata la definizione di criteri. Per informazioni sull'assegnazione dei criteri, vedere [Use Azure portal to assign and manage resource policies](resource-manager-policy-portal.md) (Usare il portale di Azure per assegnare e gestire i criteri delle risorse) o [Assegnare e gestire i criteri tramite script](resource-manager-policy-create-assign.md).

I criteri vengono valutati durante la creazione e l'aggiornamento delle risorse (operazioni PUT e PATCH).

## <a name="how-is-it-different-from-rbac"></a>Quali sono le differenze rispetto al controllo degli accessi in base al ruolo?
Esistono differenze importanti tra i criteri e il controllo degli accessi in base al ruoli (RBAC). Il Controllo degli accessi in base al ruolo è incentrato sulle azioni dell'**utente** in ambiti diversi. Ad esempio, un utente viene aggiunto al ruolo di collaboratore per un gruppo di risorse nell'ambito desiderato, in modo che possa apportare modifiche a tale gruppo di risorse. I criteri si concentrano sulle proprietà delle **risorse** durante la distribuzione. Tramite i criteri, ad esempio, si possono controllare i tipi di risorse di cui è possibile effettuare il provisioning. In alternativa, si possono limitare le posizioni in cui è possibile effettuare il provisioning delle risorse. A differenza del controllo degli accessi in base al ruolo, i criteri rappresentano un sistema con autorizzazioni predefinite e negazione esplicita. 

Per usare i criteri, l'utente deve essere autenticato tramite il controllo degli accessi in base al ruolo. In particolare, per l'account sono necessari:

* `Microsoft.Authorization/policydefinitions/write` l'autorizzazione per definire un criterio
* `Microsoft.Authorization/policyassignments/write` l'autorizzazione per assegnare un criterio 

Queste autorizzazioni non sono incluse nel ruolo di **collaboratore**.

## <a name="built-in-policies"></a>Criteri predefiniti

Azure offre alcune definizioni di criteri predefiniti che possono ridurre il numero di criteri da definire. Prima di procedere con le definizioni dei criteri, è necessario considerare se un criterio predefinito fornisce già la definizione. Le definizioni di criteri predefinite sono:

* Percorsi consentiti
* Tipi di risorse consentiti
* SKU degli account di archiviazione consentiti
* SKU delle macchine virtuali consentiti
* Applicare il tag e il valore predefinito
* Applicare il tag e il valore
* Tipi di risorse non consentiti
* Richiedere SQL Server versione 12.0
* Richiedere la crittografia dell'account di archiviazione

## <a name="policy-definition-structure"></a>Struttura delle definizioni di criteri
Per creare una definizione di criterio è possibile usare JSON. La definizione dei criteri contiene gli elementi per:

* mode
* parameters
* nome visualizzato
* description
* regola dei criteri
  * valutazione logica
  * effetto

L'esempio seguente illustra un criterio che limita i punti in cui vengono distribuite le risorse:

```json
{
  "properties": {
    "mode": "all",
    "parameters": {
      "allowedLocations": {
        "type": "array",
        "metadata": {
          "description": "The list of locations that can be specified when deploying resources",
          "strongType": "location",
          "displayName": "Allowed locations"
        }
      }
    },
    "displayName": "Allowed locations",
    "description": "This policy enables you to restrict the locations your organization can specify when deploying resources.",
    "policyRule": {
      "if": {
        "not": {
          "field": "location",
          "in": "[parameters('allowedLocations')]"
        }
      },
      "then": {
        "effect": "deny"
      }
    }
  }
}
```

## <a name="mode"></a>Mode

È consigliabile impostare `mode` su `all`. Quando l'impostazione è **tutti** i gruppi di risorse e tutti i tipi di risorse vengono valutati per il criterio. Il portale usa **tutti** per tutti i criteri. Se si usa PowerShell o l'interfaccia della riga di comando di Azure, è necessario specificare il parametro `mode` e impostarlo su **tutti**.
 
In precedenza, i criteri sono stati valutati solo sui tipi di risorse che supportavano i tag e il percorso. La modalità `indexed` mantiene questo comportamento. Se si usa la modalità **tutti** i criteri vengono valutati anche sui tipi di risorse che non supportano i tag e il percorso. [Subnet della rete virtuale](https://github.com/Azure/azure-policy-samples/tree/master/samples/Network/enforce-nsg-on-subnet) è un esempio di un tipo aggiunto di recente. I gruppi di risorse vengono inoltre valutati quando la modalità è impostata su **tutti**. Ad esempio, è possibile [applicare tag in un gruppo di risorse](https://github.com/Azure/azure-policy-samples/tree/master/samples/ResourceGroup/enforce-resourceGroup-tags). 

## <a name="parameters"></a>Parametri
L'uso dei parametri consente di semplificare la gestione dei criteri, riducendone il numero di definizioni. Definire un criterio per una proprietà della risorsa (ad esempio limitando i percorsi in cui le risorse possono essere distribuite) e includere i parametri nella definizione. Quindi, riutilizzare tale definizione dei criteri per diversi scenari passando per valori diversi (ad esempio specificando un insieme di percorsi per una sottoscrizione) quando viene assegnato il criterio.

I parametri vengono dichiarati quando si creano le definizioni dei criteri.

```json
"parameters": {
  "allowedLocations": {
    "type": "array",
    "metadata": {
      "description": "The list of allowed locations for resources.",
      "displayName": "Allowed locations"
    }
  }
}
```

Il tipo di un parametro può essere stringa o matrice. La proprietà dei metadati viene usata per gli strumenti come il portale di Azure per visualizzare informazioni di tipo descrittivo. 

Nella regola dei criteri, fare riferimento ai parametri con la sintassi seguente: 

```json
{ 
    "field": "location",
    "in": "[parameters('allowedLocations')]"
}
```

## <a name="display-name-and-description"></a>Nome visualizzato e descrizione

Usare **displayName** e **descrizione** per identificare la definizione dei criteri e fornire il contesto d'uso.

## <a name="policy-rule"></a>Regola dei criteri

La regola dei criteri è costituita dai blocchi **If** e **Then** blocchi. Nel blocco **If**, si definiscono una o più condizioni che specificano quando i criteri vengono applicati. È possibile applicare gli operatori logici a queste condizioni per definire con precisione lo scenario di un criterio. Nel blocco **Then** si definisce l'effetto che si verifica quando le condizioni **If** sono soddisfatte.

```json
{
  "if": {
    <condition> | <logical operator>
  },
  "then": {
    "effect": "deny | audit | append"
  }
}
```

### <a name="logical-operators"></a>Operatori logici
Gli operatori logici supportati sono:

* `"not": {condition  or operator}`
* `"allOf": [{condition or operator},{condition or operator}]`
* `"anyOf": [{condition or operator},{condition or operator}]`

La sintassi **not** inverte il risultato della condizione. La sintassi **allOf** (simile all'operazione logica **And**) richiede che tutte le condizioni siano vere. La sintassi **anyOf** (simile all'operazione logica **Or**) richiede che una o più condizioni siano vere.

È possibile annidare gli operatori logici. L'esempio seguente illustra un'operazione **not** nidificata in un'operazione **allOf**. 

```json
"if": {
  "allOf": [
    {
      "not": {
        "field": "tags",
        "containsKey": "application"
      }
    },
    {
      "field": "type",
      "equals": "Microsoft.Storage/storageAccounts"
    }
  ]
},
```

### <a name="conditions"></a>Condizioni
La condizione valuta se un **campo** soddisfa determinati criteri. Le condizioni supportate sono:

* `"equals": "value"`
* `"like": "value"`
* `"match": "value"`
* `"contains": "value"`
* `"in": ["value1","value2"]`
* `"containsKey": "keyName"`
* `"exists": "bool"`

Quando si usa la condizione **like**, è possibile inserire un carattere jolly (*) nel valore.

Quando si usa la condizione **match**, specificare `#` per rappresentare una cifra, `?` per una lettera e qualsiasi altro carattere per rappresentare il carattere effettivo. Per gli esempi, vedere [Applicare criteri delle risorse per nomi e testo](resource-manager-policy-naming-convention.md).

### <a name="fields"></a>Fields
Le condizioni vengono formate usando i campi. Un campo rappresenta le proprietà nel payload delle richieste di risorse usato per descrivere lo stato della risorsa.  

Sono supportati i seguenti campi:

* `name`
* `kind`
* `type`
* `location`
* `tags`
* `tags.*` 
* alias delle proprietà; per un elenco, vedere [alias](#aliases).

### <a name="effect"></a>Effetto
Il criterio supporta i tipi di effetto seguenti: `deny`, `audit`, `append`, `AuditIfNotExists` e `DeployIfNotExists`. 

* **Deny** genera un evento nel log di controllo e nega la richiesta
* **Audit** genera un evento di avviso nel log di controllo, ma non nega la richiesta
* **Append** aggiunge il set di campi definiti alla richiesta 
* **AuditIfNotExists** - abilita il controllo se una risorsa non esiste
* **DeployIfNotExists** - distribuisce una risorsa se non esiste già. Attualmente questo effetto è supportato solo tramite criteri predefiniti.

In caso di **aggiunta**, è necessario specificare questi dettagli:

```json
"effect": "append",
"details": [
  {
    "field": "field name",
    "value": "value of the field"
  }
]
```

Il valore può essere una stringa o un oggetto formato JSON. 

Con **AuditIfNotExists** e **DeployIfNotExists** è possibile valutare l'esistenza di una risorsa figlio e applicare una regola quando tale risorsa non esiste. È possibile ad esempio richiedere che venga distribuito un Network Watcher per tutte le reti virtuali.

Per un esempio di controllo quando non è stata distribuita un'estensione della macchina virtuale, vedere [Audit VM extensions](https://github.com/Azure/azure-policy-samples/blob/master/samples/Compute/audit-vm-extension/azurepolicy.json) (Controllo delle estensioni VM).

## <a name="aliases"></a>Alias

Usare gli alias delle proprietà per accedere alle proprietà specifiche per un tipo di risorsa. Gli alias consentono di limitare le condizioni o i valori consentiti per una proprietà su una risorsa. Ogni alias esegue il mapping ai percorsi in versioni di API diverse per un tipo di risorsa specificato. Durante la valutazione dei criteri, il motore dei criteri ottiene il percorso della proprietà per la versione API specificata.

**Microsoft.Cache/Redis**

| Alias | Descrizione |
| ----- | ----------- |
| Microsoft.Cache/Redis/enableNonSslPort | Impostare se la porta non-ssl del server Redis (6379) è abilitata. |
| Microsoft.Cache/Redis/shardCount | Impostare il numero di partizioni da creare in una cache di cluster Premium.  |
| Microsoft.Cache/Redis/sku.capacity | Impostare la dimensione della cache Redis da distribuire.  |
| Microsoft.Cache/Redis/sku.family | Impostare la famiglia di SKU da usare. |
| Microsoft.Cache/Redis/sku.name | Impostare il tipo di cache Redis da distribuire. |

**Microsoft.Cdn/profiles**

| Alias | Descrizione |
| ----- | ----------- |
| Microsoft.CDN/profiles/sku.name | Impostare il nome del livello di prezzo. |

**Microsoft.Compute/disks**

| Alias | Descrizione |
| ----- | ----------- |
| Microsoft.Compute/imageOffer | Impostare l'offerta dell'immagine di piattaforma o immagine del marketplace utilizzata per creare la macchina virtuale. |
| Microsoft.Compute/imagePublisher | Impostare il server di pubblicazione dell'immagine di piattaforma o immagine del marketplace utilizzata per creare la macchina virtuale. |
| Microsoft.Compute/imageSku | Impostare lo SKU dell'immagine di piattaforma o immagine del marketplace utilizzata per creare la macchina virtuale. |
| Microsoft.Compute/imageVersion | Impostare la versione dell'immagine di piattaforma o immagine del marketplace utilizzata per creare la macchina virtuale. |


**Microsoft.Compute/virtualMachines**

| Alias | Descrizione |
| ----- | ----------- |
| Microsoft.Compute/imageId | Impostare l'identificatore dell'immagine utilizzata per creare la macchina virtuale. |
| Microsoft.Compute/imageOffer | Impostare l'offerta dell'immagine di piattaforma o immagine del marketplace utilizzata per creare la macchina virtuale. |
| Microsoft.Compute/imagePublisher | Impostare il server di pubblicazione dell'immagine di piattaforma o immagine del marketplace utilizzata per creare la macchina virtuale. |
| Microsoft.Compute/imageSku | Impostare lo SKU dell'immagine di piattaforma o immagine del marketplace utilizzata per creare la macchina virtuale. |
| Microsoft.Compute/imageVersion | Impostare la versione dell'immagine di piattaforma o immagine del marketplace utilizzata per creare la macchina virtuale. |
| Microsoft.Compute/licenseType | Impostare che l'immagine o il disco è concesso in licenza in locale. Questo valore viene usato solo per le immagini che contengono il sistema operativo Windows Server.  |
| Microsoft.Compute/virtualMachines/imageOffer | Impostare l'offerta dell'immagine di piattaforma o immagine del marketplace utilizzata per creare la macchina virtuale. |
| Microsoft.Compute/virtualMachines/imagePublisher | Impostare il server di pubblicazione dell'immagine di piattaforma o immagine del marketplace utilizzata per creare la macchina virtuale. |
| Microsoft.Compute/virtualMachines/imageSku | Impostare lo SKU dell'immagine di piattaforma o immagine del marketplace utilizzata per creare la macchina virtuale. |
| Microsoft.Compute/virtualMachines/imageVersion | Impostare la versione dell'immagine di piattaforma o immagine del marketplace utilizzata per creare la macchina virtuale. |
| Microsoft.Compute/virtualMachines/osDisk.Uri | Impostare l'URI del disco rigido virtuale. |
| Microsoft.Compute/virtualMachines/sku.name | Impostare le dimensioni della macchina virtuale. |

**Microsoft.Compute/virtualMachines/extensions**

| Alias | Descrizione |
| ----- | ----------- |
| Microsoft.Compute/virtualMachines/extensions/publisher | Impostare il nome del server di pubblicazione dell'estensione. |
| Microsoft.Compute/virtualMachines/extensions/type | Impostare il tipo di estensione. |
| Microsoft.Compute/virtualMachines/extensions/typeHandlerVersion | Impostare versione dell'estensione. |

**Microsoft.Compute/virtualMachineScaleSets**

| Alias | Descrizione |
| ----- | ----------- |
| Microsoft.Compute/imageId | Impostare l'identificatore dell'immagine utilizzata per creare la macchina virtuale. |
| Microsoft.Compute/imageOffer | Impostare l'offerta dell'immagine di piattaforma o immagine del marketplace utilizzata per creare la macchina virtuale. |
| Microsoft.Compute/imagePublisher | Impostare il server di pubblicazione dell'immagine di piattaforma o immagine del marketplace utilizzata per creare la macchina virtuale. |
| Microsoft.Compute/imageSku | Impostare lo SKU dell'immagine di piattaforma o immagine del marketplace utilizzata per creare la macchina virtuale. |
| Microsoft.Compute/imageVersion | Impostare la versione dell'immagine di piattaforma o immagine del marketplace utilizzata per creare la macchina virtuale. |
| Microsoft.Compute/licenseType | Impostare che l'immagine o il disco è concesso in licenza in locale. Questo valore viene usato solo per le immagini che contengono il sistema operativo Windows Server. |
| Microsoft.Compute/VirtualMachineScaleSets/computerNamePrefix | Impostare il prefisso del nome del computer per tutte le macchine virtuali nel set di scalabilità. |
| Microsoft.Compute/VirtualMachineScaleSets/osdisk.imageUrl | Impostare l'URI del BLOB per l'immagine utente. |
| Microsoft.Compute/VirtualMachineScaleSets/osdisk.vhdContainers | Impostare gli URL del contenitore che vengono usati per archiviare i dischi del sistema operativo per il set di scalabilità. |
| Microsoft.Compute/VirtualMachineScaleSets/sku.name | Impostare la dimensione delle macchine virtuali in un set di scalabilità. |
| Microsoft.Compute/VirtualMachineScaleSets/sku.tier | Impostare il livello delle macchine virtuali in un set di scalabilità. |
  
**Microsoft.Network/applicationGateways**

| Alias | Descrizione |
| ----- | ----------- |
| Microsoft.Network/applicationGateways/sku.name | Impostare la dimensione del gateway. |

**Microsoft.Network/virtualNetworkGateways**

| Alias | Descrizione |
| ----- | ----------- |
| Microsoft.Network/virtualNetworkGateways/gatewayType | Impostare il tipo di questo gateway di rete virtuale. |
| Microsoft.Network/virtualNetworkGateways/sku.name | Impostare il nome dello SKU del gateway. |

**Microsoft.Sql/servers**

| Alias | Descrizione |
| ----- | ----------- |
| Microsoft.Sql/servers/version | Impostare la versione del server. |

**Microsoft.Sql/databases**

| Alias | Descrizione |
| ----- | ----------- |
| Microsoft.Sql/servers/databases/edition | Impostare l'edizione del database. |
| Microsoft.Sql/servers/databases/elasticPoolName | Impostare il nome del pool elastico in cui si trova in database. |
| Microsoft.Sql/servers/databases/requestedServiceObjectiveId | Impostare l'ID obiettivo del livello di servizio configurato del database. |
| Microsoft.Sql/servers/databases/requestedServiceObjectiveName | Impostare il nome dell'obiettivo del livello di servizio configurato del database.  |

**Microsoft.Sql/elasticpools**

| Alias | Descrizione |
| ----- | ----------- |
| servers/elasticpools | Microsoft.Sql/servers/elasticPools/dtu | Impostare il valore DTU totale condiviso per il pool elastico del database. |
| servers/elasticpools | Microsoft.Sql/servers/elasticPools/edition | Impostare l'edizione del pool elastico. |

**Microsoft.Storage/storageAccounts**

| Alias | Descrizione |
| ----- | ----------- |
| Microsoft.Storage/storageAccounts/accessTier | Impostare il livello di accesso utilizzato per la fatturazione. |
| Microsoft.Storage/storageAccounts/accountType | Impostare il nome di SKU. |
| Microsoft.Storage/storageAccounts/enableBlobEncryption | Specificare se il servizio crittografa i dati quando vengono archiviati nel servizio di archiviazione BLOB. |
| Microsoft.Storage/storageAccounts/enableFileEncryption | Specificare se il servizio crittografa i dati quando vengono archiviati nel servizio di archiviazione file. |
| Microsoft.Storage/storageAccounts/sku.name | Impostare il nome di SKU. |
| Microsoft.Storage/storageAccounts/supportsHttpsTrafficOnly | Impostato per consentire solo il traffico https al servizio di archiviazione. |

## <a name="policy-sets"></a>Set di criteri

I set di criteri consentono di raggruppare diverse definizioni di criteri correlate. I set di criteri semplificano l'assegnazione e la gestione in quanto si lavora con i gruppi come un singolo elemento. È possibile ad esempio raggruppare tutti i criteri relativi ai tag in un singolo set di criteri. Invece di assegnare ciascun criterio singolarmente, si applica il set di criteri.
 
L'esempio seguente illustra come creare un set di criteri per la gestione di due tag (costCenter e productName). L'esempio usa due criteri predefiniti per l'applicazione del valore di tag predefinito e per l'applicazione di tale valore. Il set di criteri dichiara due parametri, costCenterValue e productNameValue per la riusabilità. Fa riferimento a due definizioni di criteri predefinite più volte con parametri diversi. Per ogni parametro è possibile fornire un valore fisso, come illustrato per tagName, oppure un parametro dal set di criteri, come illustrato per tagValue.

```json
{
    "properties": {
        "displayName": "Billing Tags Policy",
        "policyType": "Custom",
        "description": "Specify cost Center tag and product name tag",
        "parameters": {
            "costCenterValue": {
                "type": "String",
                "metadata": {
                    "description": "required value for Cost Center tag"
                }
            },
            "productNameValue": {
                "type": "String",
                "metadata": {
                    "description": "required value for product Name tag"
                }
            }
        },
        "policyDefinitions": [
            {
                "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62",
                "parameters": {
                    "tagName": {
                        "value": "costCenter"
                    },
                    "tagValue": {
                        "value": "[parameters('costCenterValue')]"
                    }
                }
            },
            {
                "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/2a0e14a6-b0a6-4fab-991a-187a4f81c498",
                "parameters": {
                    "tagName": {
                        "value": "costCenter"
                    },
                    "tagValue": {
                        "value": "[parameters('costCenterValue')]"
                    }
                }
            },
            {
                "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62",
                "parameters": {
                    "tagName": {
                        "value": "productName"
                    },
                    "tagValue": {
                        "value": "[parameters('productNameValue')]"
                    }
                }
            },
            {
                "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/2a0e14a6-b0a6-4fab-991a-187a4f81c498",
                "parameters": {
                    "tagName": {
                        "value": "productName"
                    },
                    "tagValue": {
                        "value": "[parameters('productNameValue')]"
                    }
                }
            }
        ]
    },
    "id": "/subscriptions/<subscription-id>/providers/Microsoft.Authorization/policySetDefinitions/billingTagsPolicy",
    "type": "Microsoft.Authorization/policySetDefinitions",
    "name": "billingTagsPolicy"
}
```

Si aggiunge un set di criteri con il comando **New-AzureRMPolicySetDefinition** di PowerShell.

Per le operazioni REST usare la versione API **2017-06-01-preview** come illustrato nell'esempio seguente:

```
PUT /subscriptions/<subId>/providers/Microsoft.Authorization/policySetDefinitions/billingTagsPolicySet?api-version=2017-06-01-preview
```

## <a name="next-steps"></a>Passaggi successivi
* Dopo aver definito una regola dei criteri, assegnarla a un ambito. Per assegnare i criteri tramite il portale, vedere [Use Azure portal to assign and manage resource policies](resource-manager-policy-portal.md) (Usare il portale di Azure per assegnare e gestire i criteri delle risorse). Per assegnare i criteri tramite l'API REST, PowerShell o l'interfaccia della riga di comando di Azure, vedere [Assegnare e gestire i criteri tramite script](resource-manager-policy-create-assign.md).
* Per dei criteri di esempio vedere [Azure resource policy GitHub repository](https://github.com/Azure/azure-policy-samples).
* Per indicazioni su come le aziende possono usare Resource Manager per gestire efficacemente le sottoscrizioni, vedere [Azure enterprise scaffold - prescriptive subscription governance](resource-manager-subscription-governance.md) (Scaffolding aziendale Azure - Governance prescrittiva per le sottoscrizioni).
* Lo schema dei criteri è pubblicato su [http://schema.management.azure.com/schemas/2016-12-01/policyDefinition.json](http://schema.management.azure.com/schemas/2016-12-01/policyDefinition.json). 

