---
title: Struttura delle definizioni di Criteri di Azure | Microsoft Docs
description: Descrizione di come la definizione dei criteri delle risorse viene usata da Criteri di Azure per stabilire le convenzioni per le risorse all'interno dell'organizzazione grazie alla definizione di quando i criteri vengono applicati e dell'azione da eseguire.
services: azure-policy
keywords: 
author: bandersmsft
ms.author: banders
ms.date: 01/17/2018
ms.topic: article
ms.service: azure-policy
ms.custom: 
ms.openlocfilehash: ffff4a663b64342142f42a662905a290044e2dfb
ms.sourcegitcommit: 95500c068100d9c9415e8368bdffb1f1fd53714e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/14/2018
---
# <a name="azure-policy-definition-structure"></a>Struttura delle definizioni di criteri di Azure

La definizione dei criteri delle risorse usata da Criteri di Azure consente di stabilire le convenzioni per le risorse all'interno dell'organizzazione grazie alla definizione di quando i criteri vengono applicati e delle azioni da eseguire. Definendo le convenzioni, è possibile controllare i costi e gestire più facilmente le risorse. È ad esempio possibile specificare che vengano consentiti solo determinati tipi di macchine virtuali. In alternativa, è possibile richiedere che tutte le risorse abbiano un tag specifico. I criteri vengono ereditati da tutte le risorse figlio. Se quindi un criterio viene applicato a un gruppo di risorse, è applicabile a tutte le risorse in tale gruppo.

Per creare una definizione di criterio è possibile usare JSON. La definizione dei criteri contiene gli elementi per:

* mode
* Parametri
* nome visualizzato
* description
* regola dei criteri
  * valutazione logica
  * effetto

Ad esempio, la notazione JSON seguente illustra un criterio che limita i punti in cui vengono distribuite le risorse:

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

Tutti i modelli di Criteri di Azure di esempio sono disponibili nella pagina [Modelli per Criteri di Azure](json-samples.md).

## <a name="mode"></a>Mode

Il parametro **mode** (modalità) determina quali tipi di risorse verranno valutate per l'assegnazione dei criteri. Le modalità supportate sono:
* `all`: vengono valutati i gruppi di risorse e tutti i tipi di risorse 
* `indexed`: vengono valutati solo i tipi di risorse che supportano tag e il percorso

È consigliabile impostare il parametro **mode** su `all`. Tutte le definizioni di criteri create tramite il portale usano la modalità `all`. Se si usa PowerShell o l'interfaccia della riga di comando di Azure, è necessario specificare il parametro **mode** e impostarlo su `all`. 

## <a name="parameters"></a>Parametri

I parametri consentono di semplificare la gestione dei criteri, riducendone il numero di definizioni. I parametri possono essere paragonati ai campi di un modulo: `name`, `address`, `city`, `state`. Questi parametri rimangono sempre invariati, ma i loro valori cambiano a seconda dei dati immessi durante la compilazione del modulo da parte dei singoli utenti. I parametri funzionano nello stesso modo durante la creazione di criteri. L'inclusione dei parametri in una definizione dei criteri consente di riutilizzare i singoli criteri in vari scenari mediante l'uso di valori diversi.

Ad esempio, è possibile definire un criterio per la proprietà di una risorsa in modo da limitare le posizioni in cui le risorse possono essere distribuite. In questo caso, durante la creazione del criterio dichiarare i parametri seguenti:


```json
"parameters": {
  "allowedLocations": {
    "type": "array",
    "metadata": {
      "description": "The list of allowed locations for resources.",
      "displayName": "Allowed locations",
      "strongType": "location"
    }
  }
}
```

Il tipo di un parametro può essere stringa o matrice. La proprietà dei metadati viene usata per gli strumenti come il portale di Azure per visualizzare informazioni di tipo descrittivo.

Nella proprietà dei metadati è possibile usare **strongType** per fornire un elenco di opzioni di selezione multipla nel portale di Azure.  I valori consentiti per **strongType** attualmente includono:

* `"location"`
* `"resourceTypes"`
* `"storageSkus"`
* `"vmSKUs"`

Nella regola dei criteri, fare riferimento ai parametri con la sintassi seguente:

```json
{
    "field": "location",
    "in": "[parameters('allowedLocations')]"
}
```

## <a name="display-name-and-description"></a>Nome visualizzato e descrizione

È possibile usare **displayName** e **description** per identificare la definizione dei criteri e fornire il contesto d'uso.

## <a name="policy-rule"></a>Regola dei criteri

La regola dei criteri è costituita dai blocchi **If** e **Then** blocchi. Nel blocco **If**, si definiscono una o più condizioni che specificano quando i criteri vengono applicati. È possibile applicare gli operatori logici a queste condizioni per definire con precisione lo scenario di un criterio.

Nel blocco **Then** si definisce l'effetto che si verifica quando le condizioni **If** sono soddisfatte.

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

Una condizione valuta se un **campo** soddisfa determinati criteri. Le condizioni supportate sono:

* `"equals": "value"`
* `"like": "value"`
* `"match": "value"`
* `"contains": "value"`
* `"in": ["value1","value2"]`
* `"containsKey": "keyName"`
* `"exists": "bool"`

Quando si usa la condizione **like**, è possibile inserire un carattere jolly (*) nel valore.

Quando si usa la condizione **match**, specificare `#` per rappresentare una cifra, `?` per una lettera e qualsiasi altro carattere per rappresentare il carattere effettivo. Per alcuni esempi, vedere [Immagini di macchine virtuali approvate](scripts/allowed-custom-images.md).

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
Il criterio supporta i tipi di effetto seguenti:

* **Deny**: genera un evento nel log di controllo e nega la richiesta
* **Audit**: genera un evento di avviso nel log di controllo, ma non nega la richiesta
* **Append**: aggiunge il set di campi definiti alla richiesta
* **AuditIfNotExists**: abilita il controllo se una risorsa non esiste
* **DeployIfNotExists**: distribuisce una risorsa se non esiste già. Attualmente questo effetto è supportato solo tramite criteri predefiniti.

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

Con **AuditIfNotExists** e **DeployIfNotExists** è possibile valutare l'esistenza di una risorsa figlio e applicare una regola e un effetto corrispondente quando tale risorsa non esiste. È possibile ad esempio richiedere che venga distribuito un Network Watcher per tutte le reti virtuali.
Per un esempio di controllo quando non è stata distribuita un'estensione della macchina virtuale, vedere [Audit if extension does not exist](scripts/audit-ext-not-exist.md) (Controllare se l'estensione esiste).


## <a name="aliases"></a>Alias

Usare gli alias delle proprietà per accedere alle proprietà specifiche per un tipo di risorsa. Gli alias consentono di limitare le condizioni o i valori consentiti per una proprietà su una risorsa. Ogni alias esegue il mapping ai percorsi in versioni di API diverse per un tipo di risorsa specificato. Durante la valutazione dei criteri, il motore dei criteri ottiene il percorso della proprietà per la versione API specificata.

**Microsoft.Cache/Redis**

| Alias | DESCRIZIONE |
| ----- | ----------- |
| Microsoft.Cache/Redis/enableNonSslPort | Impostare se la porta non-ssl del server Redis (6379) è abilitata. |
| Microsoft.Cache/Redis/shardCount | Impostare il numero di partizioni da creare in una cache di cluster Premium.  |
| Microsoft.Cache/Redis/sku.capacity | Impostare la dimensione della cache Redis da distribuire.  |
| Microsoft.Cache/Redis/sku.family | Impostare la famiglia di SKU da usare. |
| Microsoft.Cache/Redis/sku.name | Impostare il tipo di cache Redis da distribuire. |

**Microsoft.Cdn/profiles**

| Alias | DESCRIZIONE |
| ----- | ----------- |
| Microsoft.CDN/profiles/sku.name | Impostare il nome del livello di prezzo. |

**Microsoft.Compute/disks**

| Alias | DESCRIZIONE |
| ----- | ----------- |
| Microsoft.Compute/imageOffer | Impostare l'offerta dell'immagine di piattaforma o immagine del marketplace utilizzata per creare la macchina virtuale. |
| Microsoft.Compute/imagePublisher | Impostare il server di pubblicazione dell'immagine di piattaforma o immagine del marketplace utilizzata per creare la macchina virtuale. |
| Microsoft.Compute/imageSku | Impostare lo SKU dell'immagine di piattaforma o immagine del marketplace utilizzata per creare la macchina virtuale. |
| Microsoft.Compute/imageVersion | Impostare la versione dell'immagine di piattaforma o immagine del marketplace utilizzata per creare la macchina virtuale. |


**Microsoft.Compute/virtualMachines**

| Alias | DESCRIZIONE |
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
| Microsoft.Compute/virtualMachines/availabilitySet.id | Imposta l'ID del set di disponibilità per la macchina virtuale. |

**Microsoft.Compute/virtualMachines/extensions**

| Alias | DESCRIZIONE |
| ----- | ----------- |
| Microsoft.Compute/virtualMachines/extensions/publisher | Impostare il nome del server di pubblicazione dell'estensione. |
| Microsoft.Compute/virtualMachines/extensions/type | Impostare il tipo di estensione. |
| Microsoft.Compute/virtualMachines/extensions/typeHandlerVersion | Impostare versione dell'estensione. |

**Microsoft.Compute/virtualMachineScaleSets**

| Alias | DESCRIZIONE |
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

| Alias | DESCRIZIONE |
| ----- | ----------- |
| Microsoft.Network/applicationGateways/sku.name | Impostare la dimensione del gateway. |

**Microsoft.Network/virtualNetworkGateways**

| Alias | DESCRIZIONE |
| ----- | ----------- |
| Microsoft.Network/virtualNetworkGateways/gatewayType | Impostare il tipo di questo gateway di rete virtuale. |
| Microsoft.Network/virtualNetworkGateways/sku.name | Impostare il nome dello SKU del gateway. |

**Microsoft.Sql/servers**

| Alias | DESCRIZIONE |
| ----- | ----------- |
| Microsoft.Sql/servers/version | Impostare la versione del server. |

**Microsoft.Sql/databases**

| Alias | DESCRIZIONE |
| ----- | ----------- |
| Microsoft.Sql/servers/databases/edition | Impostare l'edizione del database. |
| Microsoft.Sql/servers/databases/elasticPoolName | Impostare il nome del pool elastico in cui si trova in database. |
| Microsoft.Sql/servers/databases/requestedServiceObjectiveId | Impostare l'ID obiettivo del livello di servizio configurato del database. |
| Microsoft.Sql/servers/databases/requestedServiceObjectiveName | Impostare il nome dell'obiettivo del livello di servizio configurato del database.  |

**Microsoft.Sql/elasticpools**

| Alias | DESCRIZIONE |
| ----- | ----------- |
| servers/elasticpools | Microsoft.Sql/servers/elasticPools/dtu | Impostare il valore DTU totale condiviso per il pool elastico del database. |
| servers/elasticpools | Microsoft.Sql/servers/elasticPools/edition | Impostare l'edizione del pool elastico. |

**Microsoft.Storage/storageAccounts**

| Alias | DESCRIZIONE |
| ----- | ----------- |
| Microsoft.Storage/storageAccounts/accessTier | Impostare il livello di accesso utilizzato per la fatturazione. |
| Microsoft.Storage/storageAccounts/accountType | Impostare il nome di SKU. |
| Microsoft.Storage/storageAccounts/enableBlobEncryption | Specificare se il servizio crittografa i dati quando vengono archiviati nel servizio di archiviazione BLOB. |
| Microsoft.Storage/storageAccounts/enableFileEncryption | Specificare se il servizio crittografa i dati quando vengono archiviati nel servizio di archiviazione file. |
| Microsoft.Storage/storageAccounts/sku.name | Impostare il nome di SKU. |
| Microsoft.Storage/storageAccounts/supportsHttpsTrafficOnly | Impostato per consentire solo il traffico https al servizio di archiviazione. |
| Microsoft.Storage/storageAccounts/networkAcls.virtualNetworkRules[*].id | Controllare che l'endpoint del servizio di rete virtuale sia abilitato. |

## <a name="initiatives"></a>Iniziative

Le iniziative consentono di raggruppare più definizioni di criteri correlati per semplificare le assegnazioni e la gestione e quindi di usare un gruppo come se fosse un unico elemento. È possibile ad esempio raggruppare tutte le definizioni di criteri relativi ai tag in una singola iniziativa. Invece di assegnare ciascun criterio singolarmente, si applica l'iniziativa.

L'esempio seguente illustra come creare un'iniziativa per la gestione di due tag: `costCenter` e `productName`. Usa due criteri predefiniti per applicare il valore di tag predefinito.


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

## <a name="next-steps"></a>Passaggi successivi

- Rivedere tutti i modelli di Criteri di Azure di esempio disponibili nella pagina [Modelli per Criteri di Azure](json-samples.md).
