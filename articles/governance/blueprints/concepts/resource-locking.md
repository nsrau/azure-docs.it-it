---
title: Informazioni sul blocco delle risorse
description: Informazioni sulle opzioni di blocco nei progetti di Azure per proteggere le risorse quando si assegna un progetto.
ms.date: 10/05/2020
ms.topic: conceptual
ms.openlocfilehash: 8ac5c918a3c370b9d8e88800e05f83e585550e3c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91744016"
---
# <a name="understand-resource-locking-in-azure-blueprints"></a>Comprendere il blocco risorse di Azure Blueprint

La creazione di ambienti coerenti su larga scala è davvero efficace solo se esiste un meccanismo che mantenga tale coerenza. Questo articolo spiega il funzionamento del blocco risorse di Azure Blueprint. Per un esempio di blocco delle risorse e dell'applicazione delle _assegnazioni di rifiuto_, vedere l'esercitazione [protezione di nuove risorse](../tutorials/protect-new-resources.md) .

> [!NOTE]
> I blocchi di risorse distribuiti dai progetti di Azure vengono applicati solo alle risorse distribuite dall'assegnazione del progetto. Per le risorse esistenti, ad esempio quelle nei gruppi di risorse già esistenti, non è stato aggiunto alcun blocco.

## <a name="locking-modes-and-states"></a>Modalità di blocco e stati

La modalità di blocco si applica all'assegnazione del progetto ed è costituita da tre opzioni: **non bloccare**, **sola lettura**o non **eliminare**. La modalità di blocco viene configurata durante la distribuzione degli artefatti nel corso dell'assegnazione di un progetto. È possibile impostare una modalità di blocco diversa aggiornando l'assegnazione del progetto.
Le modalità di blocco, tuttavia, non possono essere modificate all'esterno dei progetti di Azure.

Le risorse create da elementi in un'assegnazione di progetto hanno quattro stati: **non bloccato**, di **sola lettura**, **non è possibile modificare/eliminare**o **eliminare**. Ciascun tipo di artefatto può essere in stato **Non bloccato**. La tabella seguente può essere usata per determinare lo stato di una risorsa:

|Modalità|Tipo di risorsa artefatto|State|Descrizione|
|-|-|-|-|
|Non bloccare|*|Non bloccato|Le risorse non sono protette dai progetti di Azure. Questo stato viene usato anche per le risorse aggiunte a un artefatto del gruppo di risorse **Sola lettura** o **Non eliminare** all'esterno dell'assegnazione di un progetto.|
|Sola lettura|Resource group|Impossibile modificare/eliminare|Il gruppo di risorse è di sola lettura e i relativi tag non possono essere modificati. Le risorse con stato **Non bloccato** possono essere aggiunte, spostate, modificate o eliminate da questo gruppo.|
|Sola lettura|Diverso da gruppo di risorse|Sola lettura|La risorsa non può essere modificata in alcun modo. Non sono state apportate modifiche e non possono essere eliminate.|
|Non eliminare|*|Impossibile eliminare|Le risorse possono essere modificate, ma non possono essere eliminate. Le risorse con stato **Non bloccato** possono essere aggiunte, spostate, modificate o eliminate da questo gruppo.|

## <a name="overriding-locking-states"></a>Sostituzione degli stati di blocco

È in genere possibile che un utente con il [controllo degli accessi in base al ruolo di Azure (RBAC)](../../../role-based-access-control/overview.md) appropriato per la sottoscrizione, ad esempio il ruolo "proprietario", sia autorizzato a modificare o eliminare qualsiasi risorsa. Questo accesso non avviene quando i progetti di Azure applicano il blocco come parte di un'assegnazione distribuita. Se l'assegnazione è stata impostata con l'opzione **Sola lettura ** o **Non eliminare**, nemmeno il proprietario della sottoscrizione può eseguire l'azione bloccata sulla risorsa protetta.

Questa misura di sicurezza salvaguarda la coerenza del progetto definito e l'ambiente che è stato progettato per creare a partire da eliminazioni accidentali o programmatiche.

### <a name="assign-at-management-group"></a>Assegna al gruppo di gestione

Un'opzione aggiuntiva per impedire ai proprietari della sottoscrizione di rimuovere l'assegnazione di un progetto consiste nell'assegnare il progetto a un gruppo di gestione. In questo scenario solo i **proprietari** del gruppo di gestione dispongono delle autorizzazioni necessarie per rimuovere l'assegnazione del progetto.

Per assegnare il progetto a un gruppo di gestione invece che a una sottoscrizione, la chiamata dell'API REST cambia per essere simile alla seguente:

```http
PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{assignmentMG}/providers/Microsoft.Blueprint/blueprintAssignments/{assignmentName}?api-version=2018-11-01-preview
```

Il gruppo di gestione definito da `{assignmentMG}` deve essere all'interno della gerarchia del gruppo di gestione o essere lo stesso gruppo di gestione in cui viene salvata la definizione del progetto.

Il corpo della richiesta dell'assegnazione del progetto ha un aspetto simile al seguente:

```json
{
    "identity": {
        "type": "SystemAssigned"
    },
    "location": "eastus",
    "properties": {
        "description": "enforce pre-defined simpleBlueprint to this XXXXXXXX subscription.",
        "blueprintId": "/providers/Microsoft.Management/managementGroups/{blueprintMG}/providers/Microsoft.Blueprint/blueprints/simpleBlueprint",
        "scope": "/subscriptions/{targetSubscriptionId}",
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

La differenza principale tra questo corpo della richiesta e l'altra da assegnare a una sottoscrizione è la `properties.scope` Proprietà. Questa proprietà obbligatoria deve essere impostata sulla sottoscrizione a cui si applica l'assegnazione del progetto. La sottoscrizione deve essere un figlio diretto della gerarchia del gruppo di gestione in cui è archiviata l'assegnazione del progetto.

> [!NOTE]
> Un progetto assegnato all'ambito del gruppo di gestione funziona ancora come assegnazione di progetto a livello di sottoscrizione. L'unica differenza è rappresentata dalla posizione in cui viene archiviata l'assegnazione del progetto per impedire ai proprietari della sottoscrizione di rimuovere l'assegnazione e i blocchi associati.

## <a name="removing-locking-states"></a>Eliminazione degli stati di blocco

Se si rende necessario modificare o eliminare una risorsa protetta da un'assegnazione, è possibile procedere in due modi.

- Aggiornare l'assegnazione del progetto impostando **Non bloccare** come modalità di blocco
- Eliminare l'assegnazione del progetto

Quando l'assegnazione viene rimossa, i blocchi creati dai progetti di Azure vengono rimossi. La risorsa viene tuttavia tralasciata e dovrà essere eliminati in modo normale.

## <a name="how-blueprint-locks-work"></a>Funzionamento dei blocchi progetto

Un'azione di negazione delle [assegnazioni](../../../role-based-access-control/deny-assignments.md) del controllo degli accessi in base al ruolo di Azure viene applicata alle risorse artefatto durante l'assegnazione di un progetto se l'assegnazione ha selezionato l'opzione di sola **lettura** o non **eliminare** . L'azione di negazione viene aggiunta dall'identità gestita dell'assegnazione del progetto e può essere rimossa dalle risorse artefatto solo dalla stessa identità gestita. Questa misura di sicurezza impone il meccanismo di blocco e impedisce la rimozione del blocco del progetto al di fuori dei progetti di Azure.

:::image type="content" source="../media/resource-locking/blueprint-deny-assignment.png" alt-text="Screenshot della pagina controllo di accesso (I A M) e della scheda nega assegnazioni per un gruppo di risorse." border="false":::

Le [proprietà di assegnazione Deny](../../../role-based-access-control/deny-assignments.md#deny-assignment-properties) di ogni modalità sono le seguenti:

|Modalità |Autorizzazioni. azioni |Permissions. notacts |Entità [i]. Tipo |ExcludePrincipals [i]. ID | DoNotApplyToChildScopes |
|-|-|-|-|-|-|
|Sola lettura |**\*** |**\*/read**<br />**Microsoft.Authorization/locks/delete**<br />**Microsoft. Network/virtualNetwork/Subnets/join/Action** |SystemDefined (Everyone) |assegnazione di progetto e definito dall'utente in **excludedPrincipals** |Gruppo di risorse- _true_; Risorsa- _false_ |
|Non eliminare |**\*/Delete** | **Microsoft.Authorization/locks/delete**<br />**Microsoft. Network/virtualNetwork/Subnets/join/Action** |SystemDefined (Everyone) |assegnazione di progetto e definito dall'utente in **excludedPrincipals** |Gruppo di risorse- _true_; Risorsa- _false_ |

> [!IMPORTANT]
> Azure Resource Manager memorizza nella cache i dettagli di assegnazione di ruolo per un massimo di 30 minuti. Di conseguenza, le azioni di negazione assegnazioni per le risorse del progetto potrebbero non essere completamente attive con effetto immediato. Durante questo periodo di tempo, potrebbe essere possibile eliminare una risorsa che deve essere protetta da blocchi di progetto.

## <a name="exclude-a-principal-from-a-deny-assignment"></a>Escludere un'entità da un'assegnazione di negazione

In alcuni scenari di progettazione o di sicurezza, potrebbe essere necessario escludere un'entità dall' [assegnazione di negazione](../../../role-based-access-control/deny-assignments.md) creata dall'assegnazione del progetto. Questo passaggio viene eseguito nell'API REST aggiungendo fino a cinque valori alla matrice **excludedPrincipals** nella proprietà **Locks** durante la [creazione dell'assegnazione](/rest/api/blueprints/assignments/createorupdate). La definizione di assegnazione seguente è un esempio di corpo della richiesta che include **excludedPrincipals**:

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

## <a name="exclude-an-action-from-a-deny-assignment"></a>Escludere un'azione da un'assegnazione di negazione

Analogamente all' [esclusione di un'entità](#exclude-a-principal-from-a-deny-assignment) in un' [assegnazione Deny](../../../role-based-access-control/deny-assignments.md) in un'assegnazione di progetto, è possibile escludere operazioni specifiche del [provider di risorse di Azure](../../../role-based-access-control/resource-provider-operations.md). All'interno del blocco **Properties. Locks** , nella stessa posizione in cui si trova **excludedPrincipals** , è possibile aggiungere un **excludedActions** :

```json
"locks": {
    "mode": "AllResourcesDoNotDelete",
    "excludedPrincipals": [
        "7be2f100-3af5-4c15-bcb7-27ee43784a1f",
        "38833b56-194d-420b-90ce-cff578296714"
    ],
    "excludedActions": [
        "Microsoft.ContainerRegistry/registries/push/write",
        "Microsoft.Authorization/*/read"
    ]
},
```

Mentre **excludedPrincipals** deve essere esplicito, le voci **excludedActions** possono usare `*` per la corrispondenza con caratteri jolly delle operazioni del provider di risorse.

## <a name="next-steps"></a>Passaggi successivi

- Segui l'esercitazione [Proteggi nuove risorse](../tutorials/protect-new-resources.md) .
- Informazioni sul [ciclo di vita del progetto](./lifecycle.md).
- Informazioni su come usare [parametri statici e dinamici](./parameters.md).
- Informazioni su come personalizzare l'[ordine di sequenziazione del progetto](./sequencing-order.md).
- Informazioni su come [aggiornare assegnazioni esistenti](../how-to/update-existing-assignments.md).
- Risolvere i problemi durante l'assegnazione di un progetto con la [risoluzione generale dei problemi](../troubleshoot/general.md).