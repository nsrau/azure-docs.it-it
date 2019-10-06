---
title: Informazioni sul blocco delle risorse
description: Informazioni sulle opzioni di blocco per proteggere le risorse durante l'assegnazione di un progetto.
author: DCtheGeek
ms.author: dacoulte
ms.date: 04/24/2019
ms.topic: conceptual
ms.service: blueprints
ms.openlocfilehash: 5c62fdb698dddf293d339904fd0c854052d636eb
ms.sourcegitcommit: d7689ff43ef1395e61101b718501bab181aca1fa
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/06/2019
ms.locfileid: "71981054"
---
# <a name="understand-resource-locking-in-azure-blueprints"></a>Comprendere il blocco risorse di Azure Blueprint

La creazione di ambienti coerenti su larga scala è davvero efficace solo se esiste un meccanismo che mantenga tale coerenza. Questo articolo spiega il funzionamento del blocco risorse di Azure Blueprint. Per un esempio di blocco delle risorse e dell'applicazione delle _assegnazioni di rifiuto_, vedere l'esercitazione [protezione di nuove risorse](../tutorials/protect-new-resources.md) .

## <a name="locking-modes-and-states"></a>Modalità di blocco e stati

La modalità di blocco si applica all'assegnazione del progetto e presenta tre opzioni: **Non bloccare**, **Sola lettura** e **Non eliminare**. La modalità di blocco viene configurata durante la distribuzione degli artefatti nel corso dell'assegnazione di un progetto. È possibile impostare una modalità di blocco diversa aggiornando l'assegnazione del progetto.
Le modalità di blocco non possono tuttavia essere modificate al di fuori di Blueprints.

Le risorse create dagli artefatti nell'assegnazione di un progetto hanno quattro stati: **Non bloccato**, **Sola lettura**, **Impossibile modificare/eliminare** e **Impossibile eliminare**. Ciascun tipo di artefatto può essere in stato **Non bloccato**. La tabella seguente può essere usata per determinare lo stato di una risorsa:

|Modalità|Tipo di risorsa artefatto|State|Descrizione|
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

![Assegnazione di negazione progetto per il gruppo di risorse](../media/resource-locking/blueprint-deny-assignment.png)

Le [proprietà di assegnazione Deny](../../../role-based-access-control/deny-assignments.md#deny-assignment-properties) di ogni modalità sono le seguenti:

|Modalità |Autorizzazioni. azioni |Permissions. notacts |Principals[i].Type |ExcludePrincipals[i].Id | DoNotApplyToChildScopes |
|-|-|-|-|-|-|
|Sola lettura |**\*** |**\*/read** |SystemDefined (Everyone) |assegnazione di progetto e definito dall'utente in **excludedPrincipals** |Gruppo di risorse- _true_; Risorsa- _false_ |
|Non eliminare |**\*/Delete** | |SystemDefined (Everyone) |assegnazione di progetto e definito dall'utente in **excludedPrincipals** |Gruppo di risorse- _true_; Risorsa- _false_ |

> [!IMPORTANT]
> Azure Resource Manager memorizza nella cache i dettagli di assegnazione di ruolo per un massimo di 30 minuti. Di conseguenza, le azioni di negazione assegnazioni per le risorse del progetto potrebbero non essere completamente attive con effetto immediato. Durante questo periodo di tempo, potrebbe essere possibile eliminare una risorsa che deve essere protetta da blocchi di progetto.

## <a name="exclude-a-principal-from-a-deny-assignment"></a>Escludere un'entità da un'assegnazione di negazione

In alcuni scenari di progettazione o di sicurezza, potrebbe essere necessario escludere un'entità dall' [assegnazione di negazione](../../../role-based-access-control/deny-assignments.md) creata dall'assegnazione del progetto. Questa operazione viene eseguita nell'API REST aggiungendo fino a cinque valori alla matrice **excludedPrincipals** nella proprietà **Locks** durante la [creazione dell'assegnazione](/rest/api/blueprints/assignments/createorupdate).
Questo è un esempio di corpo della richiesta che include **excludedPrincipals**:

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

- Segui l'esercitazione [Proteggi nuove risorse](../tutorials/protect-new-resources.md) .
- Informazioni sul [ciclo di vita del progetto](lifecycle.md).
- Informazioni su come usare [parametri statici e dinamici](parameters.md).
- Informazioni su come personalizzare l'[ordine di sequenziazione del progetto](sequencing-order.md).
- Informazioni su come [aggiornare assegnazioni esistenti](../how-to/update-existing-assignments.md).
- Risolvere i problemi durante l'assegnazione di un progetto con la [risoluzione generale dei problemi](../troubleshoot/general.md).