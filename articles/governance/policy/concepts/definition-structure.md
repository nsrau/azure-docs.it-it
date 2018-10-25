---
title: Struttura delle definizioni di criteri di Azure
description: Descrizione di come la definizione dei criteri delle risorse viene usata da Criteri di Azure per stabilire le convenzioni per le risorse all'interno dell'organizzazione grazie alla definizione di quando i criteri vengono applicati e dell'azione da eseguire.
services: azure-policy
author: DCtheGeek
ms.author: dacoulte
ms.date: 09/18/2018
ms.topic: conceptual
ms.service: azure-policy
manager: carmonm
ms.openlocfilehash: e3770fe29d6f1073a0ca6507fdf57059cbd3727e
ms.sourcegitcommit: 7b0778a1488e8fd70ee57e55bde783a69521c912
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/10/2018
ms.locfileid: "49067537"
---
# <a name="azure-policy-definition-structure"></a>Struttura delle definizioni di criteri di Azure

La definizione dei criteri delle risorse usata da Criteri di Azure consente di stabilire le convenzioni per le risorse all'interno dell'organizzazione grazie alla definizione di quando i criteri vengono applicati e delle azioni da eseguire. Definendo le convenzioni, è possibile controllare i costi e gestire più facilmente le risorse. È ad esempio possibile specificare che vengano consentiti solo determinati tipi di macchine virtuali. In alternativa, è possibile richiedere che tutte le risorse abbiano un tag specifico. I criteri vengono ereditati da tutte le risorse figlio. Se quindi un criterio viene applicato a un gruppo di risorse, è applicabile a tutte le risorse in tale gruppo.

Lo schema usato dai Criteri di Azure è reperibile qui: [https://schema.management.azure.com/schemas/2018-05-01/policyDefinition.json](https://schema.management.azure.com/schemas/2018-05-01/policyDefinition.json)

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

Tutti gli esempi di Criteri di Azure sono disponibili in [Esempi di criteri](../samples/index.md).

## <a name="mode"></a>Mode

Il parametro **mode** (modalità) determina quali tipi di risorse verranno valutate per l'assegnazione dei criteri. Le modalità supportate sono:

- `all`: vengono valutati i gruppi di risorse e tutti i tipi di risorse
- `indexed`: vengono valutati solo i tipi di risorse che supportano tag e il percorso

Nella maggior parte dei casi, è consigliabile impostare il parametro **mode** su `all`. Tutte le definizioni di criteri create tramite il portale usano la modalità `all`. Se si usa PowerShell o l'interfaccia della riga di comando di Azure è necessario specificare il parametro **mode** manualmente. Se la definizione dei criteri non contiene un valore **mode**, come valore predefinito viene usato `all` in Azure PowerShell e `null` nell'interfaccia della riga di comando di Azure, che equivale a `indexed`, per compatibilità con le versioni precedenti.

`indexed` deve essere usato quando la creazione di criteri consente di applicare tag o percorsi. Anche se questo non è necessario, così facendo le risorse che non supportano tag e percorsi saranno visualizzate come non conformi nei risultati sulla conformità. L'unica eccezione è rappresentata dai **gruppi di risorse**. Per i criteri che tentano di applicare percorsi o tag a un gruppo di risorse, impostare il parametro **mode** su `all` e specificare una destinazione specifica per il tipo `Microsoft.Resources/subscriptions/resourceGroup`. Per un esempio, vedere [Applicare tag di gruppi di risorse](../samples/enforce-tag-rg.md).

## <a name="parameters"></a>Parametri

I parametri consentono di semplificare la gestione dei criteri, riducendone il numero di definizioni. I parametri possono essere paragonati ai campi di un modulo: `name`, `address`, `city`, `state`. Questi parametri rimangono sempre invariati, ma i loro valori cambiano a seconda dei dati immessi durante la compilazione del modulo da parte dei singoli utenti.
I parametri funzionano nello stesso modo durante la creazione di criteri. L'inclusione dei parametri in una definizione dei criteri consente di riutilizzare i singoli criteri in vari scenari mediante l'uso di valori diversi.

> [!NOTE]
> La definizione dei parametri per una definizione dei criteri o di un'iniziativa può essere configurata solo durante la creazione iniziale dei criteri o dell'iniziativa. La definizione dei parametri non può essere modificata in un secondo momento.
> In questo modo le assegnazioni esistenti dei criteri o dell'iniziativa non possono essere rese indirettamente non valide.

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

Nella proprietà dei metadati è possibile usare **strongType** per fornire un elenco di opzioni di selezione multipla nel portale di Azure. I valori consentiti per **strongType** attualmente includono:

- `"location"`
- `"resourceTypes"`
- `"storageSkus"`
- `"vmSKUs"`
- `"existingResourceGroups"`
- `"omsWorkspace"`

Nella regola dei criteri fare riferimento ai parametri con la sintassi della funzione del valore di distribuzione `parameters` seguente:

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

Quando si usano le condizioni **like** e **notLike**, è possibile inserire un carattere jolly `*` nel valore.
Il valore non deve contenere più di 1 carattere jolly `*`.

Quando si usano le condizioni **match** e **notMatch**, specificare `#` per rappresentare una cifra, `?` per una lettera e qualsiasi altro carattere per rappresentare il carattere effettivo. Ad esempio, vedere [Consentire modelli nome multipli](../samples/allow-multiple-name-patterns.md).

### <a name="fields"></a>Campi

Le condizioni vengono formate usando i campi. Un campo rappresenta le proprietà nel payload delle richieste di risorse usato per descrivere lo stato della risorsa.

Sono supportati i seguenti campi:

- `name`
- `fullName`
  - Restituisce il nome completo della risorsa. Il nome completo di una risorsa è il nome della risorsa con anteposti gli eventuali nomi delle risorse padre (ad esempio "myServer/myDatabase").
- `kind`
- `type`
- `location`
- `tags`
- `tags.<tagName>`
  - Dove **\<tagName\>** è il nome del tag per il quale convalidare la condizione.
  - Esempio: `tags.CostCenter` dove **CostCenter** è il nome del tag.
- `tags[<tagName>]`
  - Questa sintassi tra parentesi quadre supporta nomi di tag che contengono punti.
  - Dove **\<tagName\>** è il nome del tag per il quale convalidare la condizione.
  - Esempio: `tags[Acct.CostCenter]` dove **Acct.CostCenter** è il nome del tag.
- alias delle proprietà; per un elenco, vedere [alias](#aliases).

### <a name="effect"></a>Effetto

Il criterio supporta i tipi di effetto seguenti:

- **Deny**: genera un evento nel log di controllo e nega la richiesta
- **Audit**: genera un evento di avviso nel log di controllo, ma non nega la richiesta
- **Append**: aggiunge il set di campi definiti alla richiesta
- **AuditIfNotExists**: abilita il controllo se una risorsa non esiste
- **DeployIfNotExists**: distribuisce una risorsa se non esiste già.

In caso di **aggiunta**, è necessario specificare questi dettagli:

```json
"effect": "append",
"details": [{
    "field": "field name",
    "value": "value of the field"
}]
```

Il valore può essere una stringa o un oggetto formato JSON.

Con **AuditIfNotExists** e **DeployIfNotExists** è possibile valutare l'esistenza di una risorsa correlata e applicare una regola e un effetto corrispondente quando tale risorsa non esiste. È possibile ad esempio richiedere che venga distribuito un Network Watcher per tutte le reti virtuali. Per un esempio di controllo quando non è stata distribuita un'estensione della macchina virtuale, vedere [Audit if extension does not exist](../samples/audit-ext-not-exist.md) (Controllare se l'estensione esiste).

Per informazioni dettagliate su ogni effetto, ordine di valutazione, proprietà ed esempi, vedere [Informazioni sugli effetti di Criteri](effects.md).

### <a name="policy-functions"></a>Funzioni dei criteri

È disponibile un subset delle [funzioni del modello di Resource Manager](../../../azure-resource-manager/resource-group-template-functions.md) per l'utilizzo all'interno di una regola dei criteri. Le funzioni attualmente supportate sono:

- [parameters](../../../azure-resource-manager/resource-group-template-functions-deployment.md#parameters)
- [concat](../../../azure-resource-manager/resource-group-template-functions-array.md#concat)
- [resourceGroup](../../../azure-resource-manager/resource-group-template-functions-resource.md#resourcegroup)
- [sottoscrizione](../../../azure-resource-manager/resource-group-template-functions-resource.md#subscription)

Inoltre, la funzione `field` è disponibile per le regole dei criteri. Questa funzione viene principalmente usata con **AuditIfNotExists** e **DeployIfNotExists** per fare riferimento ai campi sulla risorsa che viene valutata. Un esempio può essere visualizzato nell'[esempio DeployIfNotExists](effects.md#deployifnotexists-example).

#### <a name="policy-function-examples"></a>Esempi di funzione dei criteri

Questo esempio di regola dei criteri usa la `resourceGroup` funzione risorsa per ottenere la proprietà **name**, in combinazione con la matrice `concat` e la funzione oggetto per creare una condizione `like` che fa in modo che il nome della risorsa inizi con il nome del gruppo di risorse.

```json
{
    "if": {
        "not": {
            "field": "name",
            "like": "[concat(resourceGroup().name,'*')]"
        }
    },
    "then": {
        "effect": "deny"
    }
}
```

Questo esempio di regola dei criteri usa la funzione risorse `resourceGroup` per ottenere il valore della matrice della proprietà **tags** nel tag **CostCenter** nel gruppo di risorse e aggiungerlo al tag **CostCenter**  nella nuova risorsa.

```json
{
    "if": {
        "field": "tags.CostCenter",
        "exists": "false"
    },
    "then": {
        "effect": "append",
        "details": [{
            "field": "tags.CostCenter",
            "value": "[resourceGroup().tags.CostCenter]"
        }]
    }
}
```

## <a name="aliases"></a>Alias

Usare gli alias delle proprietà per accedere alle proprietà specifiche per un tipo di risorsa. Gli alias consentono di limitare le condizioni o i valori consentiti per una proprietà su una risorsa. Ogni alias esegue il mapping ai percorsi in versioni di API diverse per un tipo di risorsa specificato. Durante la valutazione dei criteri, il motore dei criteri ottiene il percorso della proprietà per la versione API specificata.

L'elenco degli alias è in costante crescita. Per scoprire quali alias sono attualmente supportati da Criteri di Azure usare uno dei metodi seguenti:

- Azure PowerShell

  ```azurepowershell-interactive
  # Login first with Connect-AzureRmAccount if not using Cloud Shell

  # Use Get-AzureRmPolicyAlias to list available providers
  Get-AzureRmPolicyAlias -ListAvailable

  # Use Get-AzureRmPolicyAlias to list aliases for a Namespace (such as Azure Automation -- Microsoft.Automation)
  Get-AzureRmPolicyAlias -NamespaceMatch 'automation'
  ```

- Interfaccia della riga di comando di Azure

  ```azurecli-interactive
  # Login first with az login if not using Cloud Shell

  # List namespaces
  az provider list --query [*].namespace

  # Get Azure Policy aliases for a specific Namespace (such as Azure Automation -- Microsoft.Automation)
  az provider show --namespace Microsoft.Automation --expand "resourceTypes/aliases" --query "resourceTypes[].aliases[].name"
  ```

- API REST / ARMClient

  ```http
  GET https://management.azure.com/providers/?api-version=2017-08-01&$expand=resourceTypes/aliases
  ```

### <a name="understanding-the--alias"></a>Informazioni sull'alias [*]

Molti degli alias disponibili hanno una versione che viene visualizzata come un nome "normale" e un'altra a cui viene aggiunto **[\*]**. Ad esempio: 

- `Microsoft.Storage/storageAccounts/networkAcls.ipRules`
- `Microsoft.Storage/storageAccounts/networkAcls.ipRules[*]`

Il primo esempio viene usato per valutare l'intera matrice in cui l'alias **[\*]** valuta ciascun elemento della matrice.

Come esempio, si esaminerà una regola dei criteri. Questo criterio applica l'effetto **Deny** a un account di archiviazione con la matrice ipRules configurata e se **nessuna** delle matrici ipRules ha un valore pari a "127.0.0.1".

```json
"policyRule": {
    "if": {
        "allOf": [{
                "field": "Microsoft.Storage/storageAccounts/networkAcls.ipRules",
                "exists": "true"
            },
            {
                "field": "Microsoft.Storage/storageAccounts/networkAcls.ipRules[*].value",
                "notEquals": "127.0.0.1"
            }
        ]
    },
    "then": {
        "effect": "deny",
    }
}
```

La matrice **ipRules** per l'esempio è la seguente:

```json
"ipRules": [{
        "value": "127.0.0.1",
        "action": "Allow"
    },
    {
        "value": "192.168.1.1",
        "action": "Allow"
    }
]
```

Ecco come viene elaborato l'esempio:

- `networkAcls.ipRules`: verifica che il valore della matrice sia diverso da Null. Viene restituito un valore true, quindi la valutazione continua.

  ```json
  {
    "field": "Microsoft.Storage/storageAccounts/networkAcls.ipRules",
    "exists": "true"
  }
  ```

- `networkAcls.ipRules[*].value`: verifica ciascuna proprietà _value_ nella matrice **ipRules**.

  ```json
  {
    "field": "Microsoft.Storage/storageAccounts/networkAcls.ipRules[*].value",
    "notEquals": "127.0.0.1"
  }
  ```

  - Ogni elemento della matrice verrà elaborato.

    - "127.0.0.1" != "127.0.0.1" restituisce un valore false.
    - "127.0.0.1" != "192.168.1.1" restituisce un valore true.
    - Almeno una proprietà _value_ della matrice **ipRules** ha restituito il valore false e quindi la valutazione viene interrotta.

Dato che una condizione ha restituito il valore false, l'effetto **Deny** non viene attivato.

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

- Vedere gli esempi in [Esempi di Criteri di Azure](../samples/index.md)
- Vedere [Informazioni sugli effetti di Criteri](effects.md)
- Informazioni su come [creare criteri a livello di programmazione](../how-to/programmatically-create.md)
- Informazioni su come [ottenere dati sulla conformità](../how-to/getting-compliance-data.md)
- Informazioni su come [correggere le risorse non conformi](../how-to/remediate-resources.md)
- Scoprire le caratteristiche di un gruppo di gestione con [Organizzare le risorse con i gruppi di gestione di Azure](../../management-groups/overview.md)
