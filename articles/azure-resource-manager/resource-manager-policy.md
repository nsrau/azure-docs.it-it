---
title: Criteri delle risorse di Azure | Microsoft Docs
description: "Viene descritto come utilizzare i criteri di Azure Resource Manager per l&quot;impostazione di proprietà delle risorse coerenti durante la distribuzione. È possibile applicare i criteri alla sottoscrizione o a gruppi di risorse."
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
ms.date: 03/30/2017
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: d75088bd83b0b70c889388c95331bb56fe9ba15b
ms.lasthandoff: 04/03/2017


---
# <a name="resource-policy-overview"></a>Cenni preliminari sui criteri delle risorse
I criteri delle risorse consentono di definire le convenzioni per le risorse nell'organizzazione. Definendo le convenzioni, è possibile controllare i costi e gestire più facilmente le risorse. Ad esempio, è possibile specificare che sono consentiti solo determinati tipi di macchine virtuali oppure è possibile richiedere che tutte le risorse abbiano un tag specifico. I criteri vengono ereditati da tutte le risorse figlio. Se quindi un criterio viene applicato a un gruppo di risorse, è applicabile a tutte le risorse in tale gruppo.

È importante comprendere due concetti sui criteri:

* definizione dei criteri: l'utente descrive quando i criteri vengono applicati e quale azione intraprendere
* assegnazione di criteri: si applica la definizione di criteri a un ambito (sottoscrizione o gruppo di risorse)

In questo argomento viene illustrata la definizione di criteri. Per informazioni sull'assegnazione dei criteri, vedere [Use Azure portal to assign and manage resource policies](resource-manager-policy-portal.md) (Usare il portale di Azure per assegnare e gestire i criteri delle risorse) o [Assegnare e gestire i criteri tramite script](resource-manager-policy-create-assign.md).

Azure offre alcune definizioni di criteri predefiniti che possono ridurre il numero di criteri da definire. Se una definizione di criteri predefiniti funziona per lo scenario specifico, usare tale definizione per l'assegnazione a un ambito.

I criteri vengono valutati durante la creazione e l'aggiornamento delle risorse (operazioni PUT e PATCH).

> [!NOTE]
> I tipi di risorsa che non supportano tag, tipologia, percorso non vengono attualmente valutati dal criterio, come ad esempio il tipo di risorsa Microsoft.Resources/deployments. Il supporto di questo tipo di risorsa verrà aggiunto in futuro. Per evitare problemi di compatibilità con le versioni precedenti, è consigliabile specificare in modo esplicito il tipo durante la creazione di criteri. A tutti i tipi viene ad esempio applicato un criterio per i tag che non specifica i tipi, in modo che la distribuzione del modello non venga completata in presenza di una risorsa annidata che non supporta il tag, mentre il tipo di risorsa della distribuzione sarà aggiunto alla valutazione criteri. 
> 
> 

## <a name="how-is-it-different-from-rbac"></a>Quali sono le differenze rispetto al controllo degli accessi in base al ruolo?
Esistono differenze importanti tra i criteri e il controllo degli accessi in base al ruoli (RBAC). Il Controllo degli accessi in base al ruolo è incentrato sulle azioni dell'**utente** in ambiti diversi. Ad esempio, un utente viene aggiunto al ruolo di collaboratore per un gruppo di risorse nell'ambito desiderato, in modo che possa apportare modifiche a tale gruppo di risorse. I criteri si concentrano sulle proprietà delle **risorse** durante la distribuzione. Tramite i criteri, ad esempio, è possibile controllare i tipi di risorse di cui è possibile eseguire il provisioning o limitare le posizioni in cui è possibile eseguire il provisioning delle risorse. A differenza del controllo degli accessi in base al ruolo, i criteri rappresentano un sistema con autorizzazioni predefinite e negazione esplicita. 

Per usare i criteri, l'utente deve essere autenticato tramite il controllo degli accessi in base al ruolo. In particolare, per l'account sono necessari:

* `Microsoft.Authorization/policydefinitions/write` l'autorizzazione per definire un criterio
* `Microsoft.Authorization/policyassignments/write` l'autorizzazione per assegnare un criterio 

Queste autorizzazioni non sono incluse nel ruolo di **collaboratore**.

## <a name="policy-definition-structure"></a>Struttura delle definizioni di criteri
Per creare una definizione di criterio è possibile usare JSON. La definizione dei criteri contiene gli elementi per:

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

È possibile annidare gli operatori logici. Nell'esempio seguente viene illustrata un'operazione **Not** nidificata nell'operazione **And**. 

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
* `"contains": "value"`
* `"in": ["value1","value2"]`
* `"containsKey": "keyName"`
* `"exists": "bool"`

Quando si usa la condizione **like**, è possibile inserire un carattere jolly (*) nel valore.

### <a name="fields"></a>Fields
Le condizioni vengono formate usando i campi. Un campo rappresenta le proprietà nel payload delle richieste di risorse usato per descrivere lo stato della risorsa.  

Sono supportati i seguenti campi:

* `name`
* `kind`
* `type`
* `location`
* `tags`
* `tags.*` 
* alias delle proprietà

Usare gli alias delle proprietà per accedere alle proprietà specifiche per un tipo di risorsa. Gli alias supportati sono:

* Microsoft.CDN/profiles/sku.name
* Microsoft.Compute/virtualMachines/imageOffer
* Microsoft.Compute/virtualMachines/imagePublisher
* Microsoft.Compute/virtualMachines/sku.name
* Microsoft.Compute/virtualMachines/imageSku 
* Microsoft.Compute/virtualMachines/imageVersion
* Microsoft.SQL/servers/databases/edition
* Microsoft.SQL/servers/databases/elasticPoolName
* Microsoft.SQL/servers/databases/requestedServiceObjectiveId
* Microsoft.SQL/servers/databases/requestedServiceObjectiveName
* Microsoft.SQL/servers/elasticPools/dtu
* Microsoft.SQL/servers/elasticPools/edition
* Microsoft.SQL/servers/version
* Microsoft.Storage/storageAccounts/accessTier
* Microsoft.Storage/storageAccounts/enableBlobEncryption
* Microsoft.Storage/storageAccounts/sku.name
* Microsoft.Web/serverFarms/sku.name

### <a name="effect"></a>Effetto
Il criterio supporta tre tipi di effetto: `deny`, `audit` e `append`. 

* **Deny** genera un evento nel log di controllo e nega la richiesta
* **Audit** genera un evento di avviso nel log di controllo, ma non nega la richiesta
* **Append** aggiunge il set di campi definiti alla richiesta 

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

## <a name="policy-examples"></a>Esempi di criteri

I seguenti argomenti contengono esempi di criteri:

* Per gli esempi di criteri di tag, vedere [Applicare criteri delle risorse per i tag](resource-manager-policy-tags.md).
* Per gli esempi di criteri di archiviazione, vedere [Applicare i criteri delle risorse agli account di archiviazione](resource-manager-policy-storage.md).
* Per gli esempi di criteri di macchine virtuali, vedere [Applicare criteri delle risorse alle macchine virtuali di Linux](../virtual-machines/linux/policy.md?toc=%2fazure%2fazure-resource-manager%2ftoc.json) e [Applicare criteri delle risorse alle macchine virtuali di Windows](../virtual-machines/windows/policy.md?toc=%2fazure%2fazure-resource-manager%2ftoc.json)

### <a name="allowed-resource-locations"></a>Percorsi di risorse consentiti
Per specificare i percorsi consentiti, vedere l'esempio nella sezione [Struttura delle definizioni di criteri](#policy-definition-structure). Per assegnare la definizione dei criteri, usare i criteri predefiniti con l'ID risorsa `/providers/Microsoft.Authorization/policyDefinitions/e56962a6-4747-49cd-b67b-bf8b01975c4c`.

### <a name="not-allowed-resource-locations"></a>Percorsi di risorse non consentiti
Per specificare i percorsi non consentiti, usare la definizione dei criteri seguente:

```json
{
  "properties": {
    "parameters": {
      "notAllowedLocations": {
        "type": "array",
        "metadata": {
          "description": "The list of locations that are not allowed when deploying resources",
          "strongType": "location",
          "displayName": "Not allowed locations"
        }
      }
    },
    "displayName": "Not allowed locations",
    "description": "This policy enables you to block locations that your organization can specify when deploying resources.",
    "policyRule": {
      "if": {
        "field": "location",
        "in": "[parameters('notAllowedLocations')]"
      },
      "then": {
        "effect": "deny"
      }
    }
  }
}
```

### <a name="allowed-resource-types"></a>Tipi di risorse consentiti
L'esempio seguente mostra un criterio che consente la distribuzione solo nei tipi di risorsa Microsoft.Resources, Microsoft.Compute, Microsoft.Storage, Microsoft.Network. Tutti gli altri vengono rifiutati:

```json
{
  "if": {
    "not": {
      "anyOf": [
        {
          "field": "type",
          "like": "Microsoft.Resources/*"
        },
        {
          "field": "type",
          "like": "Microsoft.Compute/*"
        },
        {
          "field": "type",
          "like": "Microsoft.Storage/*"
        },
        {
          "field": "type",
          "like": "Microsoft.Network/*"
        }
      ]
    }
  },
  "then": {
    "effect": "deny"
  }
}
```

### <a name="set-naming-convention"></a>Impostare la convenzione di denominazione
L'esempio seguente illustra l'uso dei caratteri jolly supportato dalla condizione **like**. La condizione dichiara che la richiesta deve essere negata se il nome non corrisponde al modello indicato (namePrefix\*nameSuffix):

```json
{
  "if": {
    "not": {
      "field": "name",
      "like": "namePrefix*nameSuffix"
    }
  },
  "then": {
    "effect": "deny"
  }
}
```

## <a name="next-steps"></a>Passaggi successivi
* Dopo aver definito una regola dei criteri, assegnarla a un ambito. Per assegnare i criteri tramite il portale, vedere [Use Azure portal to assign and manage resource policies](resource-manager-policy-portal.md) (Usare il portale di Azure per assegnare e gestire i criteri delle risorse). Per assegnare i criteri tramite l'API REST, PowerShell o l'interfaccia della riga di comando di Azure, vedere [Assegnare e gestire i criteri tramite script](resource-manager-policy-create-assign.md).
* Per indicazioni su come le aziende possono usare Resource Manager per gestire efficacemente le sottoscrizioni, vedere [Azure enterprise scaffold - prescriptive subscription governance](resource-manager-subscription-governance.md) (Scaffolding aziendale Azure - Governance prescrittiva per le sottoscrizioni).
* Lo schema del criterio è pubblicato in [http://schema.management.azure.com/schemas/2015-10-01-preview/policyDefinition.json](http://schema.management.azure.com/schemas/2015-10-01-preview/policyDefinition.json). 


