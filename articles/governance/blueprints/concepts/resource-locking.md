---
title: Informazioni sul blocco delle risorse
description: Learn about the locking options in Azure Blueprints to protect resources when assigning a blueprint.
ms.date: 04/24/2019
ms.topic: conceptual
ms.openlocfilehash: 50f506cc57f67ca2ae2b07e342750d6c5099e739
ms.sourcegitcommit: dd0304e3a17ab36e02cf9148d5fe22deaac18118
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74406398"
---
# <a name="understand-resource-locking-in-azure-blueprints"></a>Comprendere il blocco risorse di Azure Blueprint

La creazione di ambienti coerenti su larga scala è davvero efficace solo se esiste un meccanismo che mantenga tale coerenza. Questo articolo spiega il funzionamento del blocco risorse di Azure Blueprint. To see an example of resource locking and application of _deny assignments_, see the [protecting new resources](../tutorials/protect-new-resources.md) tutorial.

## <a name="locking-modes-and-states"></a>Modalità di blocco e stati

Locking Mode applies to the blueprint assignment and it has three options: **Don't Lock**, **Read Only**, or **Do Not Delete**. La modalità di blocco viene configurata durante la distribuzione degli artefatti nel corso dell'assegnazione di un progetto. È possibile impostare una modalità di blocco diversa aggiornando l'assegnazione del progetto.
Le modalità di blocco non possono tuttavia essere modificate al di fuori di Blueprints.

Resources created by artifacts in a blueprint assignment have four states: **Not Locked**, **Read Only**, **Cannot Edit / Delete**, or **Cannot Delete**. Ciascun tipo di artefatto può essere in stato **Non bloccato**. La tabella seguente può essere usata per determinare lo stato di una risorsa:

|Mode|Tipo di risorsa artefatto|Statale|Description|
|-|-|-|-|
|Non bloccare|*|Non bloccato|Le risorse non sono protette da Blueprints. Questo stato viene usato anche per le risorse aggiunte a un artefatto del gruppo di risorse **Sola lettura** o **Non eliminare** all'esterno dell'assegnazione di un progetto.|
|Sola lettura|Gruppo di risorse|Impossibile modificare/eliminare|Il gruppo di risorse è di sola lettura e i relativi tag non possono essere modificati. Le risorse con stato **Non bloccato** possono essere aggiunte, spostate, modificate o eliminate da questo gruppo.|
|Sola lettura|Diverso da gruppo di risorse|Sola lettura|Non è possibile modificare la risorsa in alcun modo: non sono consentite modifiche e la risorsa non può essere eliminata.|
|Non eliminare|*|Impossibile eliminare|Le risorse possono essere modificate, ma non possono essere eliminate. Le risorse con stato **Non bloccato** possono essere aggiunte, spostate, modificate o eliminate da questo gruppo.|

## <a name="overriding-locking-states"></a>Sostituzione degli stati di blocco

È in genere possibile che a un utente con [controllo degli accessi in base al ruolo](../../../role-based-access-control/overview.md) (RBAC) appropriato per la sottoscrizione, ad esempio il ruolo "Proprietario", sia consentito di modificare o eliminare qualsiasi risorsa. Questo tipo di accesso non è appropriato quando si applica il blocco ai progetti come parte di un'assegnazione distribuita. Se l'assegnazione è stata impostata con l'opzione **Sola lettura**  o **Non eliminare**, nemmeno il proprietario della sottoscrizione può eseguire l'azione bloccata sulla risorsa protetta.

Questa misura di sicurezza salvaguarda la coerenza del progetto definito e l'ambiente che è stato progettato per creare a partire da eliminazioni accidentali o programmatiche.

## <a name="removing-locking-states"></a>Eliminazione degli stati di blocco

Se si rende necessario modificare o eliminare una risorsa protetta da un'assegnazione, è possibile procedere in due modi.

- Aggiornare l'assegnazione del progetto impostando **Non bloccare** come modalità di blocco
- Eliminare l'assegnazione del progetto

Quando viene rimossa l'assegnazione, vengono rimossi i blocchi creati da Blueprint. La risorsa viene tuttavia tralasciata e dovrà essere eliminati in modo normale.

## <a name="how-blueprint-locks-work"></a>Funzionamento dei blocchi progetto

In virtù del controllo degli accessi in base al ruolo, alle risorse artefatto viene applicata un'azione di [negazione assegnazioni](../../../role-based-access-control/deny-assignments.md) durante l'assegnazione di un progetto se per l'assegnazione è stata selezionata l'opzione **Sola lettura** o **Non eliminare**. L'azione di negazione viene aggiunta dall'identità gestita dell'assegnazione del progetto e può essere rimossa dalle risorse artefatto solo dalla stessa identità gestita. Questa misura di sicurezza consente di applicare il meccanismo di blocco e impedisce di eliminare il blocco di progetto al di fuori di Blueprint.

![Blueprint deny assignment on resource group](../media/resource-locking/blueprint-deny-assignment.png)

The [deny assignment properties](../../../role-based-access-control/deny-assignments.md#deny-assignment-properties) of each mode are as follows:

|Mode |Permissions.Actions |Permissions.NotActions |Principals[i].Type |ExcludePrincipals[i].Id | DoNotApplyToChildScopes |
|-|-|-|-|-|-|
|Sola lettura |**\*** |**\*/read** |SystemDefined (Everyone) |blueprint assignment and user-defined in **excludedPrincipals** |Resource group - _true_; Resource - _false_ |
|Non eliminare |**\*/delete** | |SystemDefined (Everyone) |blueprint assignment and user-defined in **excludedPrincipals** |Resource group - _true_; Resource - _false_ |

> [!IMPORTANT]
> Azure Resource Manager memorizza nella cache i dettagli di assegnazione di ruolo per un massimo di 30 minuti. Di conseguenza, le azioni di negazione assegnazioni per le risorse del progetto potrebbero non essere completamente attive con effetto immediato. Durante questo periodo di tempo, potrebbe essere possibile eliminare una risorsa che deve essere protetta da blocchi di progetto.

## <a name="exclude-a-principal-from-a-deny-assignment"></a>Exclude a principal from a deny assignment

In some design or security scenarios, it may be necessary to exclude a principal from the [deny assignment](../../../role-based-access-control/deny-assignments.md) the blueprint assignment creates. This is done in REST API by adding up to five values to the **excludedPrincipals** array in the **locks** property when [creating the assignment](/rest/api/blueprints/assignments/createorupdate). This is an example of a request body that includes **excludedPrincipals**:

```json
{
  "identity": {
    "type": "SystemAssigned"
  },
  "location": "eastus",
  "properties": {
    "description": "enforce pre-defined simpleBlueprint to this XXXXXXXX subscription.",
    "blueprintId": "/providers/Microsoft.Management/managementGroups/{mgId}/providers/Microsoft.Blueprint/blueprints/simpleBlueprint",
    "locks": {
        "mode": "AllResourcesDoNotDelete",
        "excludedPrincipals": [
            "7be2f100-3af5-4c15-bcb7-27ee43784a1f",
            "38833b56-194d-420b-90ce-cff578296714"
        ]
    },
    "parameters": {
      "storageAccountType": {
        "value": "Standard_LRS"
      },
      "costCenter": {
        "value": "Contoso/Online/Shopping/Production"
      },
      "owners": {
        "value": [
          "johnDoe@contoso.com",
          "johnsteam@contoso.com"
        ]
      }
    },
    "resourceGroups": {
      "storageRG": {
        "name": "defaultRG",
        "location": "eastus"
      }
    }
  }
}
```

## <a name="next-steps"></a>Passaggi successivi

- Follow the [protect new resources](../tutorials/protect-new-resources.md) tutorial.
- Informazioni sul [ciclo di vita del progetto](lifecycle.md).
- Informazioni su come usare [parametri statici e dinamici](parameters.md).
- Informazioni su come personalizzare l'[ordine di sequenziazione del progetto](sequencing-order.md).
- Informazioni su come [aggiornare assegnazioni esistenti](../how-to/update-existing-assignments.md).
- Risolvere i problemi durante l'assegnazione di un progetto con la [risoluzione generale dei problemi](../troubleshoot/general.md).