---
title: Creare una definizione di criteri personalizzata
description: Creare una definizione di criteri personalizzata per Criteri di Azure per imporre regole di business personalizzate.
services: azure-policy
author: DCtheGeek
ms.author: dacoulte
ms.date: 02/12/2019
ms.topic: tutorial
ms.service: azure-policy
manager: carmonm
ms.openlocfilehash: ddda2a8bf1fab4e4c48c647237617d8f705f0561
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/12/2019
ms.locfileid: "56112088"
---
# <a name="create-a-custom-policy-definition"></a>Creare una definizione di criteri personalizzata

La definizione di criteri personalizzata consente ai clienti di definire le proprie regole per l'uso di Azure. Queste regole consentono in genere di imporre:

- Procedure di sicurezza
- Gestione dei costi
- Regole specifiche dell'organizzazione (come denominazioni o posizioni)

Indipendentemente dal driver di business, la procedura per definire nuovi criteri personalizzati è sempre la stessa.

Prima di creare un criterio personalizzato, consultare gli [esempi di criteri](../samples/index.md) per verificare se ne esiste già uno che soddisfa le proprie esigenze.

L'approccio per creare criteri personalizzati segue questi passaggi:

> [!div class="checklist"]
> - Identificare i requisiti aziendali
> - Mappare ogni requisito a una proprietà delle risorse di Azure
> - Mappare la proprietà a un alias
> - Determinare quale effetto usare
> - Comporre la definizione del criterio

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="identify-requirements"></a>Identificare i requisiti

Prima di creare la definizione del criterio, è importante identificarne la finalità. Per questa esercitazione, come obiettivo verrà usato un comune requisito di sicurezza aziendale per illustrare i passaggi necessari:

- Ogni account di archiviazione deve essere abilitato per HTTPS
- Ogni account di archiviazione deve essere disabilitato per HTTP

I requisiti devono identificare chiaramente gli stati delle risorse "essere" e "non essere".

A questo punto è stato definito lo stato previsto della risorsa, ma non l'operazione che dovrà essere eseguita con le risorse non conformi. Il servizio Criteri supporta diversi [effetti](../concepts/effects.md). Per questa esercitazione, verrà definito il requisito aziendale di impedire la creazione di risorse non conformi alle regole di business. Per soddisfare questo obiettivo, verrà usato l'effetto [Nega](../concepts/effects.md#deny). Verrà inoltre aggiunta l'opzione per sospendere il criterio per specifiche assegnazioni. A questo scopo, verrà usato l'effetto [Disabilitato](../concepts/effects.md#disabled), che verrà impostato come [parametro](../concepts/definition-structure.md#parameters) nella definizione del criterio.

## <a name="determine-resource-properties"></a>Determinare le proprietà della risorsa

In base al requisito aziendale, la risorsa di Azure da controllare con Criteri è un account di archiviazione.
Tuttavia, le proprietà da usare nella definizione del criterio non sono note. I criteri vengono valutati rispetto alla rappresentazione JSON della risorsa, quindi è necessario individuare le proprietà disponibili per tale risorsa.

Per determinare le proprietà delle risorse di Azure, sono disponibili varie opzioni, che verranno descritte in questa esercitazione:

- Modelli di Resource Manager
  - Esportazione di una risorsa esistente
  - Esperienza di creazione
  - Modelli di avvio rapido (GitHub)
  - Documentazione di riferimento sui modelli
- Azure Resource Explorer

### <a name="resource-manager-templates"></a>Modelli di Resource Manager

È possibile esaminare in vari modi un [modello di Resource Manager](../../../azure-resource-manager/resource-manager-tutorial-create-encrypted-storage-accounts.md) che include la proprietà da gestire.

#### <a name="existing-resource-in-the-portal"></a>Risorsa esistente nel portale

Il modo più semplice per trovare proprietà consiste nell'esaminare una risorsa esistente dello stesso tipo. Le risorse già configurate con l'impostazione da applicare forniscono anche il valore da confrontare.
Esaminare la pagina **Script di automazione** in **Impostazioni** nel portale di Azure per questa specifica risorsa.

![Pagina Script di automazione](../media/create-custom-policy-definition/automation-script.png)

Per un account di archiviazione questa pagina visualizza un modello simile a questo esempio:

```json
...
"resources": [{
    "comments": "Generalized from resource: '/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount'.",
    "type": "Microsoft.Storage/storageAccounts",
    "sku": {
        "name": "Standard_LRS",
        "tier": "Standard"
    },
    "kind": "Storage",
    "name": "[parameters('storageAccounts_mystorageaccount_name')]",
    "apiVersion": "2018-07-01",
    "location": "westus",
    "tags": {
        "ms-resource-usage": "azure-cloud-shell"
    },
    "scale": null,
    "properties": {
        "networkAcls": {
            "bypass": "AzureServices",
            "virtualNetworkRules": [],
            "ipRules": [],
            "defaultAction": "Allow"
        },
        "supportsHttpsTrafficOnly": false,
        "encryption": {
            "services": {
                "file": {
                    "enabled": true
                },
                "blob": {
                    "enabled": true
                }
            },
            "keySource": "Microsoft.Storage"
        }
    },
    "dependsOn": []
}]
...
```

Sotto **properties** è presente un valore denominato **supportsHttpsTrafficOnly** impostato su **false**. Questa proprietà sembra essere quella che si sta cercando. Inoltre il valore **type** della risorsa è **Microsoft.Storage/storageAccounts**. Questo valore consente di limitare il criterio applicandolo solo alle risorse di questo tipo.

#### <a name="create-a-resource-in-the-portal"></a>Creare una risorsa nel portale

Un'altra opzione del portale è l'esperienza di creazione di risorse. Durante la creazione di un account di archiviazione tramite il portale, una delle opzioni della scheda **Avanzate** è **Trasferimento sicuro obbligatorio**.
Questa proprietà prevede le opzioni _Disabilitato_ e _Abilitato_. L'icona di informazioni include testo aggiuntivo che conferma che questa opzione corrisponde alla proprietà giusta. Tuttavia, il nome della proprietà non è indicato in questa schermata del portale.

Nella parte inferiore della scheda **Rivedi e crea** è disponibile un collegamento all'opzione **Scaricare un modello per l'automazione**. Selezionando questo collegamento si aprirà il modello che consente di creare la risorsa configurata. In questo caso, vengono visualizzate due informazioni:

```json
...
"supportsHttpsTrafficOnly": {
    "type": "bool"
}
...
"properties": {
    "accessTier": "[parameters('accessTier')]",
    "supportsHttpsTrafficOnly": "[parameters('supportsHttpsTrafficOnly')]"
}
...
```

Queste informazioni indicano il tipo di proprietà e confermano inoltre che **supportsHttpsTrafficOnly** è la proprietà corretta.

#### <a name="quickstart-templates-on-github"></a>Modelli di avvio rapido in GitHub

I [modelli di avvio rapido di Azure](https://github.com/Azure/azure-quickstart-templates) in GitHub includono centinaia di modelli di Resource Manager creati per diverse risorse. Questi modelli possono risultare utili per trovare la proprietà della risorsa corretta. Alcune proprietà potrebbero sembrare quelle giuste, ma è opportuno fare altri controlli.

#### <a name="resource-reference-docs"></a>Documenti di riferimento per le risorse

Per verificare se **supportsHttpsTrafficOnly** è la proprietà corretta, controllare le informazioni di riferimento del modello di Resource Manager relativo alla [risorsa account di archiviazione](/azure/templates/microsoft.storage/2018-07-01/storageaccounts) nel provider di archiviazione.
L'oggetto proprietà include un elenco di parametri validi. Selezionando il collegamento [StorageAccountPropertiesCreateParameters-object](/azure/templates/microsoft.storage/2018-07-01/storageaccounts#storageaccountpropertiescreateparameters-object) viene visualizzata una tabella di proprietà accettabili. La proprietà **supportsHttpsTrafficOnly** è presente e la descrizione corrisponde alla risorsa necessaria per soddisfare i requisiti aziendali.

### <a name="azure-resource-explorer"></a>Azure Resource Explorer

È possibile esplorare le risorse di Azure anche in un altro modo, ossia tramite [Azure Resource Explorer](https://resources.azure.com) (anteprima). Questo strumento usa il contesto della sottoscrizione, quindi è necessario eseguire l'autenticazione per il sito Web con le credenziali di Azure. Dopo aver completato l'autenticazione, è possibile esplorare le risorse in base a provider, sottoscrizioni, gruppi di risorse e singole risorse.

Individuare una risorsa account di archiviazione ed esaminare le proprietà. Anche qui è presente la proprietà **supportsHttpsTrafficOnly**. Selezionando la scheda **Documentazione** si può verificare che la descrizione della proprietà corrisponde a quella trovata nei documenti di riferimento in precedenza.

## <a name="find-the-property-alias"></a>Trovare l'alias della proprietà

Dopo aver identificato la proprietà della risorsa, è necessario mapparla a un [alias](../concepts/definition-structure.md#aliases).

Per determinare gli alias per una risorsa di Azure, sono disponibili varie opzioni, che verranno descritte in questa esercitazione:

- Interfaccia della riga di comando di Azure
- Azure PowerShell
- Azure Resource Graph

### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

Nell'interfaccia della riga di comando di Azure il gruppo di comandi `az provider` consente di cercare gli alias delle risorse. Verrà applicato un filtro per trovare lo spazio dei nomi **Microsoft.Storage** in base ai dettagli recuperati in precedenza sulla risorsa di Azure.

```azurecli-interactive
# Login first with az login if not using Cloud Shell

# Get Azure Policy aliases for type Microsoft.Storage
az provider show --namespace Microsoft.Storage --expand "resourceTypes/aliases" --query "resourceTypes[].aliases[].name"
```

I risultati includono un alias supportato dagli account di archiviazione denominati **supportsHttpsTrafficOnly**. L'esistenza di questo alias significa che è possibile scrivere il criterio per applicare i requisiti aziendali.

### <a name="azure-powershell"></a>Azure PowerShell

In Azure PowerShell il cmdlet `Get-AzPolicyAlias` consente di cercare gli alias delle risorse.
Verrà applicato un filtro per trovare lo spazio dei nomi **Microsoft.Storage** in base ai dettagli recuperati in precedenza sulla risorsa di Azure.

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Use Get-AzPolicyAlias to list aliases for Microsoft.Storage
(Get-AzPolicyAlias -NamespaceMatch 'Microsoft.Storage').Aliases
```

Anche in questo caso, come con l'interfaccia della riga di comando di Azure, i risultati includono un alias supportato dagli account di archiviazione denominati **supportsHttpsTrafficOnly**.

### <a name="azure-resource-graph"></a>Azure Resource Graph

[Azure Resource Graph](../../resource-graph/overview.md) è un nuovo servizio disponibile in anteprima. Rende disponibile un altro metodo per trovare le proprietà delle risorse di Azure. Ecco una query di esempio per esaminare un singolo account di archiviazione con Resource Graph:

```Query
where type=~'microsoft.storage/storageaccounts' | limit 1
```

```azurecli-interactive
az graph query -q "where type=~'microsoft.storage/storageaccounts' | limit 1"
```

```azurepowershell-interactive
Search-AzGraph -Query "where type=~'microsoft.storage/storageaccounts' | limit 1"
```

I risultati sono simili a quelli visti nei modelli di Resource Manager e tramite Azure Resource Explorer. Tuttavia, i risultati di Azure Resource Graph includono anche dettagli sugli [alias](../concepts/definition-structure.md#aliases). Ecco un esempio di output di un account di archiviazione per gli alias:

```json
"aliases": {
    "Microsoft.Storage/storageAccounts/accessTier": null,
    "Microsoft.Storage/storageAccounts/accountType": "Standard_LRS",
    "Microsoft.Storage/storageAccounts/enableBlobEncryption": true,
    "Microsoft.Storage/storageAccounts/enableFileEncryption": true,
    "Microsoft.Storage/storageAccounts/encryption": {
        "keySource": "Microsoft.Storage",
        "services": {
            "blob": {
                "enabled": true,
                "lastEnabledTime": "2018-06-04T17:59:14.4970000Z"
            },
            "file": {
                "enabled": true,
                "lastEnabledTime": "2018-06-04T17:59:14.4970000Z"
            }
        }
    },
    "Microsoft.Storage/storageAccounts/encryption.keySource": "Microsoft.Storage",
    "Microsoft.Storage/storageAccounts/encryption.keyvaultproperties.keyname": null,
    "Microsoft.Storage/storageAccounts/encryption.keyvaultproperties.keyvaulturi": null,
    "Microsoft.Storage/storageAccounts/encryption.keyvaultproperties.keyversion": null,
    "Microsoft.Storage/storageAccounts/encryption.services": {
        "blob": {
            "enabled": true,
            "lastEnabledTime": "2018-06-04T17:59:14.4970000Z"
        },
        "file": {
            "enabled": true,
            "lastEnabledTime": "2018-06-04T17:59:14.4970000Z"
        }
    },
    "Microsoft.Storage/storageAccounts/encryption.services.blob": {
        "enabled": true,
        "lastEnabledTime": "2018-06-04T17:59:14.4970000Z"
    },
    "Microsoft.Storage/storageAccounts/encryption.services.blob.enabled": true,
    "Microsoft.Storage/storageAccounts/encryption.services.file": {
        "enabled": true,
        "lastEnabledTime": "2018-06-04T17:59:14.4970000Z"
    },
    "Microsoft.Storage/storageAccounts/encryption.services.file.enabled": true,
    "Microsoft.Storage/storageAccounts/networkAcls": {
        "bypass": "AzureServices",
        "defaultAction": "Allow",
        "ipRules": [],
        "virtualNetworkRules": []
    },
    "Microsoft.Storage/storageAccounts/networkAcls.bypass": "AzureServices",
    "Microsoft.Storage/storageAccounts/networkAcls.defaultAction": "Allow",
    "Microsoft.Storage/storageAccounts/networkAcls.ipRules": [],
    "Microsoft.Storage/storageAccounts/networkAcls.ipRules[*]": [],
    "Microsoft.Storage/storageAccounts/networkAcls.ipRules[*].action": [],
    "Microsoft.Storage/storageAccounts/networkAcls.ipRules[*].value": [],
    "Microsoft.Storage/storageAccounts/networkAcls.virtualNetworkRules": [],
    "Microsoft.Storage/storageAccounts/networkAcls.virtualNetworkRules[*]": [],
    "Microsoft.Storage/storageAccounts/networkAcls.virtualNetworkRules[*].action": [],
    "Microsoft.Storage/storageAccounts/networkAcls.virtualNetworkRules[*].id": [],
    "Microsoft.Storage/storageAccounts/networkAcls.virtualNetworkRules[*].state": [],
    "Microsoft.Storage/storageAccounts/primaryEndpoints": {
        "blob": "https://mystorageaccount.blob.core.windows.net/",
        "file": "https://mystorageaccount.file.core.windows.net/",
        "queue": "https://mystorageaccount.queue.core.windows.net/",
        "table": "https://mystorageaccount.table.core.windows.net/"
    },
    "Microsoft.Storage/storageAccounts/primaryEndpoints.blob": "https://mystorageaccount.blob.core.windows.net/",
    "Microsoft.Storage/storageAccounts/primaryEndpoints.file": "https://mystorageaccount.file.core.windows.net/",
    "Microsoft.Storage/storageAccounts/primaryEndpoints.queue": "https://mystorageaccount.queue.core.windows.net/",
    "Microsoft.Storage/storageAccounts/primaryEndpoints.table": "https://mystorageaccount.table.core.windows.net/",
    "Microsoft.Storage/storageAccounts/primaryEndpoints.web": null,
    "Microsoft.Storage/storageAccounts/primaryLocation": "eastus2",
    "Microsoft.Storage/storageAccounts/provisioningState": "Succeeded",
    "Microsoft.Storage/storageAccounts/sku.name": "Standard_LRS",
    "Microsoft.Storage/storageAccounts/sku.tier": "Standard",
    "Microsoft.Storage/storageAccounts/statusOfPrimary": "available",
    "Microsoft.Storage/storageAccounts/supportsHttpsTrafficOnly": false
}
```

Il servizio Azure Resource Graph (anteprima) può essere usato tramite [Cloud Shell](https://shell.azure.com), offrendo un metodo semplice e rapido per esplorare le proprietà delle risorse.

## <a name="determine-the-effect-to-use"></a>Determinare quale effetto usare

La decisione in merito all'operazione da eseguire in caso di risorse non conformi è quasi altrettanto importante di quella relativa alle valutazioni da effettuare. Ogni possibile risposta a una risorsa non conforme si chiama [effetto](../concepts/effects.md). L'effetto controlla se la risorsa non conforme viene registrata, bloccata, include dati aggiunti o è associata a una distribuzione per riportarla a uno stato conforme.

Per questo esempio, l'effetto da scegliere è Nega, perché nell'ambiente di Azure non dovranno essere create risorse non conformi. L'effetto Controllo è una prima scelta valida per determinare l'impatto di un criterio prima di impostarlo su Nega. Per semplificare la modifica dell'effetto in base all'assegnazione, è possibile parametrizzare l'effetto. Per informazioni, vedere [Parametri](#parameters) di seguito.

## <a name="compose-the-definition"></a>Comporre la definizione

A questo punto sono disponibili i dettagli e l'alias della proprietà per lo scenario da gestire. Quindi, è necessario comporre la regola del criterio. Se non si ha familiarità con il linguaggio dei criteri, vedere [Struttura delle definizioni di criteri](../concepts/definition-structure.md) per informazioni. Ecco un modello vuoto di una possibile definizione di criteri:

```json
{
    "properties": {
        "displayName": "<displayName>",
        "description": "<description>",
        "mode": "<mode>",
        "parameters": {
                <parameters>
        },
        "policyRule": {
            "if": {
                <rule>
            },
            "then": {
                "effect": "<effect>"
            }
        }
    }
}
```

### <a name="metadata"></a>Metadati

I primi tre componenti sono metadati dei criteri. Per questi componenti è facile fornire i valori, perché il motivo per cui viene creata la regola è noto. L'opzione [mode](../concepts/definition-structure.md#mode) riguarda prevalentemente i tag e la posizione delle risorse. Poiché non è necessario limitare la valutazione alle risorse che supportano i tag, verrà usato il valore _all_ per **mode**.

```json
"displayName": "Deny storage accounts not using only HTTPS",
"description": "Deny storage accounts not using only HTTPS. Checks the supportsHttpsTrafficOnly property on StorageAccounts.",
"mode": "all",
```

### <a name="parameters"></a>Parametri

Anche se per cambiare la valutazione non è stato usato un parametro, è necessario usarne uno per consentire la modifica dell'**effetto** ai fini della risoluzione dei problemi. Verrà definito un parametro **effectType** con le sole opzioni **Nega** e **Disabilitato**. Queste due opzioni soddisfano i requisiti aziendali dell'esempio. Il blocco di parametri finali sarà come indicato in questo esempio:

```json
"parameters": {
    "effectType": {
        "type": "string",
        "defaultValue": "Deny",
        "allowedValues": [
            "Deny",
            "Disabled"
        ],
        "metadata": {
            "displayName": "Effect",
            "description": "Enable or disable the execution of the policy"
        }
    }
},
```

### <a name="policy-rule"></a>Regola dei criteri

Il passaggio finale della definizione del criterio personalizzato consiste nella composizione della [regola dei criteri](../concepts/definition-structure.md#policy-rule). Esistono due affermazioni da verificare:

- Che il **tipo** di account di archiviazione sia **Microsoft.Storage/storageAccounts**
- Che l'account di archiviazione **supportsHttpsTrafficOnly** non sia **true**

Poiché è necessario che entrambe queste affermazioni siano vere, verrà usato l'[operatore logico](../concepts/definition-structure.md#logical-operators) **allOf**. Invece di creare una dichiarazione statica, verrà passato il parametro **effectType** all'effetto. La regola completa è riportata in questo esempio:

```json
"if": {
    "allOf": [
        {
            "field": "type",
            "equals": "Microsoft.Storage/storageAccounts"
        },
        {
            "field": "Microsoft.Storage/storageAccounts/supportsHttpsTrafficOnly",
            "notEquals": "true"
        }
    ]
},
"then": {
    "effect": "[parameters('effectType')]"
}
```

### <a name="completed-definition"></a>Definizione completata

Con tutte e tre le parti del criterio definite, ecco la definizione completata:

```json
{
    "properties": {
        "displayName": "Deny storage accounts not using only HTTPS",
        "description": "Deny storage accounts not using only HTTPS. Checks the supportsHttpsTrafficOnly property on StorageAccounts.",
        "mode": "all",
        "parameters": {
            "effectType": {
                "type": "string",
                "defaultValue": "Deny",
                "allowedValues": [
                    "Deny",
                    "Disabled"
                ],
                "metadata": {
                    "displayName": "Effect",
                    "description": "Enable or disable the execution of the policy"
                }
            }
        },
        "policyRule": {
            "if": {
                "allOf": [
                    {
                        "field": "type",
                        "equals": "Microsoft.Storage/storageAccounts"
                    },
                    {
                        "field": "Microsoft.Storage/storageAccounts/supportsHttpsTrafficOnly",
                        "notEquals": "true"
                    }
                ]
            },
            "then": {
                "effect": "[parameters('effectType')]"
            }
        }
    }
}
```

La definizione completata può essere usata per creare un nuovo criterio. Il portale e ogni SDK (interfaccia della riga di comando di Azure, Azure PowerShell e API REST) accettano la definizione in modi diversi, per cui esaminare i comandi per ogni componente per verificare l'utilizzo corretto. Quindi assegnarla usando l'effetto con parametri alle risorse appropriate per gestire la sicurezza degli account di archiviazione.

## <a name="review"></a>Revisione

In questa esercitazione sono state eseguite le attività seguenti:

> [!div class="checklist"]
> - Sono stati identificati i requisiti aziendali
> - Ogni requisito è stato mappato a una proprietà delle risorse di Azure
> - La proprietà è stata mappata a un alias
> - È stato determinato l'effetto da usare
> - È stata composta la definizione del criterio

## <a name="next-steps"></a>Passaggi successivi

In seguito, usare la definizione del criterio personalizzato per creare e assegnare un criterio:

> [!div class="nextstepaction"]
> [Creare e assegnare una definizione di criteri](../how-to/programmatically-create.md#create-and-assign-a-policy-definition)