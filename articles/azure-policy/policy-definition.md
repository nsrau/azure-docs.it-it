---
title: Struttura delle definizioni di criteri di Azure
description: Descrizione di come la definizione dei criteri delle risorse viene usata da Criteri di Azure per stabilire le convenzioni per le risorse all'interno dell'organizzazione grazie alla definizione di quando i criteri vengono applicati e dell'azione da eseguire.
services: azure-policy
keywords: ''
author: DCtheGeek
ms.author: dacoulte
ms.date: 05/07/2018
ms.topic: article
ms.service: azure-policy
ms.custom: ''
ms.openlocfilehash: a56fa61c6d77ab50dc1342c5a7feeaf1c579697d
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/11/2018
ms.locfileid: "34057251"
---
# <a name="azure-policy-definition-structure"></a>Struttura delle definizioni di criteri di Azure

La definizione dei criteri delle risorse usata da Criteri di Azure consente di stabilire le convenzioni per le risorse all'interno dell'organizzazione grazie alla definizione di quando i criteri vengono applicati e delle azioni da eseguire. Definendo le convenzioni, è possibile controllare i costi e gestire più facilmente le risorse. È ad esempio possibile specificare che vengano consentiti solo determinati tipi di macchine virtuali. In alternativa, è possibile richiedere che tutte le risorse abbiano un tag specifico. I criteri vengono ereditati da tutte le risorse figlio. Se quindi un criterio viene applicato a un gruppo di risorse, è applicabile a tutte le risorse in tale gruppo.

Lo schema usato dai Criteri di Azure è reperibile qui: [https://schema.management.azure.com/schemas/2016-12-01/policyDefinition.json](https://schema.management.azure.com/schemas/2016-12-01/policyDefinition.json)

Per creare una definizione di criterio è possibile usare JSON. La definizione dei criteri contiene gli elementi per:

- mode
- Parametri
- nome visualizzato
- description
- regola dei criteri
  - valutazione logica
  - effetto

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

- `all`: vengono valutati i gruppi di risorse e tutti i tipi di risorse
- `indexed`: vengono valutati solo i tipi di risorse che supportano tag e il percorso

Nella maggior parte dei casi, è consigliabile impostare il parametro **mode** su `all`. Tutte le definizioni di criteri create tramite il portale usano la modalità `all`. Se si usa PowerShell o l'interfaccia della riga di comando di Azure è necessario specificare il parametro **mode** manualmente. Se la definizione dei criteri non contiene un valore **mode**, come valore predefinito viene usato `all` in Azure PowerShell e `null` nell'interfaccia della riga di comando di Azure, che equivale a `indexed`, per compatibilità con le versioni precedenti.

`indexed` deve essere usato quando la creazione di criteri consente di applicare tag o percorsi. Anche se questo non è necessario, così facendo le risorse che non supportano tag e percorsi saranno visualizzate come non conformi nei risultati sulla conformità. L'unica eccezione è rappresentata dai **gruppi di risorse**. Per i criteri che tentano di applicare percorsi o tag a un gruppo di risorse, impostare il parametro **mode** su `all` e specificare una destinazione specifica per il tipo `Microsoft.Resources/subscriptions/resourceGroup`. Per un esempio, vedere [Applicare tag di gruppi di risorse](scripts/enforce-tag-rg.md).

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

- `"location"`
- `"resourceTypes"`
- `"storageSkus"`
- `"vmSKUs"`
- `"existingResourceGroups"`
- `"omsWorkspace"`

Nella regola dei criteri, fare riferimento ai parametri con la sintassi seguente:

```json
{
    "field": "location",
    "in": "[parameters('allowedLocations')]"
}
```

## <a name="definition-location"></a>Posizione della definizione

Durante la creazione di una definizione delle iniziative o dei criteri è importante specificare la posizione della definizione.

La posizione della definizione determina l'ambito al quale la definizione delle iniziative o dei criteri può essere assegnata. La posizione può essere specificata come un gruppo di gestione o una sottoscrizione.

> [!NOTE]
> Se si intende applicare questa definizione dei criteri a più sottoscrizioni, la posizione deve essere un gruppo di gestione che contiene le sottoscrizioni a cui si assegneranno i criteri o le iniziative.

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
        "effect": "deny | audit | append | auditIfNotExists | deployIfNotExists"
    }
}
```

### <a name="logical-operators"></a>Operatori logici

Gli operatori logici supportati sono:

- `"not": {condition  or operator}`
- `"allOf": [{condition or operator},{condition or operator}]`
- `"anyOf": [{condition or operator},{condition or operator}]`

La sintassi **not** inverte il risultato della condizione. La sintassi **allOf** (simile all'operazione logica **And**) richiede che tutte le condizioni siano vere. La sintassi **anyOf** (simile all'operazione logica **Or**) richiede che una o più condizioni siano vere.

È possibile annidare gli operatori logici. L'esempio seguente illustra un'operazione **not** nidificata in un'operazione **allOf**.

```json
"if": {
    "allOf": [{
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

- `"equals": "value"`
- `"notEquals": "value"`
- `"like": "value"`
- `"notLike": "value"`
- `"match": "value"`
- `"notMatch": "value"`
- `"contains": "value"`
- `"notContains": "value"`
- `"in": ["value1","value2"]`
- `"notIn": ["value1","value2"]`
- `"containsKey": "keyName"`
- `"notContainsKey": "keyName"`
- `"exists": "bool"`

Quando si usano le condizioni **like** e **notLike**, è possibile inserire un carattere jolly (*) nel valore.

Quando si usano le condizioni **match** e **notMatch**, specificare `#` per rappresentare una cifra, `?` per una lettera e qualsiasi altro carattere per rappresentare il carattere effettivo. Ad esempio, vedere [Consentire modelli nome multipli](scripts/allow-multiple-name-patterns.md).

### <a name="fields"></a>Fields

Le condizioni vengono formate usando i campi. Un campo rappresenta le proprietà nel payload delle richieste di risorse usato per descrivere lo stato della risorsa.  

Sono supportati i seguenti campi:

- `name`
- `fullName`
  - Restituisce il nome completo della risorsa, inclusi tutti gli elementi padre (ad esempio, "myServer/myDatabase")
- `kind`
- `type`
- `location`
- `tags`
- `tags.tagName`
- `tags[tagName]`
  - Questa sintassi tra parentesi quadre supporta nomi di tag contenenti periodi
- alias delle proprietà; per un elenco, vedere [alias](#aliases).

### <a name="alternative-accessors"></a>Funzioni di accesso alternative

**Campo** è la funzione di accesso primaria usata nelle regole dei criteri. Analizza direttamente la risorsa che viene valutata. Il criterio supporta tuttavia anche un'altra funzione, **origine**.

```json
"source": "action",
"equals": "Microsoft.Compute/virtualMachines/write"
```

**Origine** supporta solo un valore, **azione**. Azione restituisce l'azione di autorizzazione della richiesta che viene valutata. Le azioni di autorizzazione vengono esposte nella sezione autorizzazioni del [Log attività](../monitoring-and-diagnostics/monitoring-activity-log-schema.md).

Quando i criteri valutano le risorse esistenti in background, il valore **azione** viene impostato su un'azione di autorizzazione `/write` nel tipo di risorsa.

### <a name="effect"></a>Effetto

Il criterio supporta i tipi di effetto seguenti:

- **Deny**: genera un evento nel log di controllo e nega la richiesta
- **Audit**: genera un evento di avviso nel log di controllo, ma non nega la richiesta
- **Append**: aggiunge il set di campi definiti alla richiesta
- **AuditIfNotExists**: abilita il controllo se una risorsa non esiste
- **DeployIfNotExists**: distribuisce una risorsa se non esiste già. Attualmente questo effetto è supportato solo tramite criteri predefiniti.

In caso di **aggiunta**, è necessario specificare questi dettagli:

```json
"effect": "append",
"details": [{
    "field": "field name",
    "value": "value of the field"
}]
```

Il valore può essere una stringa o un oggetto formato JSON.

Con **AuditIfNotExists** e **DeployIfNotExists** è possibile valutare l'esistenza di una risorsa correlata e applicare una regola e un effetto corrispondente quando tale risorsa non esiste. È possibile ad esempio richiedere che venga distribuito un Network Watcher per tutte le reti virtuali.
Per un esempio di controllo quando non è stata distribuita un'estensione della macchina virtuale, vedere [Audit if extension does not exist](scripts/audit-ext-not-exist.md) (Controllare se l'estensione esiste).

## <a name="aliases"></a>Alias

Usare gli alias delle proprietà per accedere alle proprietà specifiche per un tipo di risorsa. Gli alias consentono di limitare le condizioni o i valori consentiti per una proprietà su una risorsa. Ogni alias esegue il mapping ai percorsi in versioni di API diverse per un tipo di risorsa specificato. Durante la valutazione dei criteri, il motore dei criteri ottiene il percorso della proprietà per la versione API specificata.

L'elenco degli alias è in costante crescita. Per scoprire quali alias sono attualmente supportati da Criteri di Azure usare uno dei metodi seguenti:

- Azure PowerShell

  ```azurepowershell-interactive
  # Login first with Connect-AzureRmAccount if not using Cloud Shell

  $azContext = Get-AzureRmContext
  $azProfile = [Microsoft.Azure.Commands.Common.Authentication.Abstractions.AzureRmProfileProvider]::Instance.Profile
  $profileClient = New-Object -TypeName Microsoft.Azure.Commands.ResourceManager.Common.RMProfileClient -ArgumentList ($azProfile)
  $token = $profileClient.AcquireAccessToken($azContext.Subscription.TenantId)
  $authHeader = @{
      'Content-Type'='application/json'
      'Authorization'='Bearer ' + $token.AccessToken
  }

  # Invoke the REST API
  $response = Invoke-RestMethod -Uri 'https://management.azure.com/providers/?api-version=2017-08-01&$expand=resourceTypes/aliases' -Method Get -Headers $authHeader

  # Create an Array List to hold discovered aliases
  $aliases = New-Object System.Collections.ArrayList

  foreach ($ns in $response.value) {
      foreach ($rT in $ns.resourceTypes) {
          if ($rT.aliases) {
              foreach ($obj in $rT.aliases) {
                  $alias = [PSCustomObject]@{
                      Namespace       = $ns.namespace
                      resourceType    = $rT.resourceType
                      alias           = $obj.name
                  }
                  $aliases.Add($alias) | Out-Null
              }
          }
      }
  }

  # Output the list, sort, and format. You can customize with Where-Object to limit as desired.
  $aliases | Sort-Object -Property Namespace, resourceType, alias | Format-Table
  ```

- Interfaccia della riga di comando di Azure

  ```azurecli-interactive
  # Login first with az login if not using Cloud Shell

  # Get Azure Policy aliases for a specific Namespace
  az provider show --namespace Microsoft.Automation --expand "resourceTypes/aliases" --query "resourceTypes[].aliases[].name"
  ```

- API REST / ARMClient

  ```http
  GET https://management.azure.com/providers/?api-version=2017-08-01&$expand=resourceTypes/aliases
  ```

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
        "policyDefinitions": [{
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