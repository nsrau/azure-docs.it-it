---
title: Informazioni sul blocco delle risorse
description: Informazioni sulle opzioni di blocco in Azure Blueprints per proteggere le risorse durante l'assegnazione di un blueprint.
ms.date: 03/25/2020
ms.topic: conceptual
ms.openlocfilehash: 9c4e2f4c6fd8f5fb574002217ca71d1e7d130ff7
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2020
ms.locfileid: "80676757"
---
# <a name="understand-resource-locking-in-azure-blueprints"></a>Comprendere il blocco risorse di Azure Blueprint

La creazione di ambienti coerenti su larga scala è davvero efficace solo se esiste un meccanismo che mantenga tale coerenza. Questo articolo spiega il funzionamento del blocco risorse di Azure Blueprint. Per un esempio di blocco delle risorse e applicazione di assegnazioni di _negazione,_ vedere l'esercitazione [sulla protezione delle nuove risorse.](../tutorials/protect-new-resources.md)

> [!NOTE]
> I blocchi delle risorse distribuiti dai blueprint di Azure vengono applicati solo alle risorse distribuite dall'assegnazione del blueprint. Le risorse esistenti, ad esempio quelle nei gruppi di risorse già esistenti, non dispongono di blocchi aggiunti.

## <a name="locking-modes-and-states"></a>Modalità di blocco e stati

La modalità di blocco si applica all'assegnazione del blueprint e dispone di tre opzioni: **Non bloccare**, **Sola lettura**o Non **eliminare**. La modalità di blocco viene configurata durante la distribuzione degli artefatti nel corso dell'assegnazione di un progetto. È possibile impostare una modalità di blocco diversa aggiornando l'assegnazione del progetto.
Le modalità di blocco, tuttavia, non possono essere modificate al di fuori dei blueprint di Azure.Locking modes, however, can't be changed outside of Azure Blueprints.

Le risorse create dagli elementi in un'assegnazione di blueprint hanno quattro stati: **Non bloccato**, **Sola lettura**, **Impossibile modificare/eliminare**o **Non eliminare**. Ciascun tipo di artefatto può essere in stato **Non bloccato**. La tabella seguente può essere usata per determinare lo stato di una risorsa:

|Mode|Tipo di risorsa artefatto|State|Descrizione|
|-|-|-|-|
|Non bloccare|*|Non bloccato|Le risorse non sono protette da Azure Blueprints.Resources aren't protected by Azure Blueprints. Questo stato viene usato anche per le risorse aggiunte a un artefatto del gruppo di risorse **Sola lettura** o **Non eliminare** all'esterno dell'assegnazione di un progetto.|
|Sola lettura|Resource group|Impossibile modificare/eliminare|Il gruppo di risorse è di sola lettura e i relativi tag non possono essere modificati. Le risorse con stato **Non bloccato** possono essere aggiunte, spostate, modificate o eliminate da questo gruppo.|
|Sola lettura|Diverso da gruppo di risorse|Sola lettura|Non è possibile modificare la risorsa in alcun modo: non sono consentite modifiche e la risorsa non può essere eliminata.|
|Non eliminare|*|Impossibile eliminare|Le risorse possono essere modificate, ma non possono essere eliminate. Le risorse con stato **Non bloccato** possono essere aggiunte, spostate, modificate o eliminate da questo gruppo.|

## <a name="overriding-locking-states"></a>Sostituzione degli stati di blocco

È in genere possibile che a un utente con [controllo degli accessi in base al ruolo](../../../role-based-access-control/overview.md) (RBAC) appropriato per la sottoscrizione, ad esempio il ruolo "Proprietario", sia consentito di modificare o eliminare qualsiasi risorsa. Questo accesso non è il caso in cui Azure Blueprints applica il blocco come parte di un'assegnazione distribuita. Se l'assegnazione è stata impostata con l'opzione **Sola lettura ** o **Non eliminare**, nemmeno il proprietario della sottoscrizione può eseguire l'azione bloccata sulla risorsa protetta.

Questa misura di sicurezza salvaguarda la coerenza del progetto definito e l'ambiente che è stato progettato per creare a partire da eliminazioni accidentali o programmatiche.

### <a name="assign-at-management-group"></a>Assegna al gruppo di gestioneAssign at management group

Un'opzione aggiuntiva per impedire ai proprietari delle sottoscrizioni di rimuovere un'assegnazione di blueprint consiste nell'assegnare il blueprint a un gruppo di gestione. In questo scenario, solo **i proprietari** del gruppo di gestione dispongono delle autorizzazioni necessarie per rimuovere l'assegnazione del blueprint.

Per assegnare il blueprint a un gruppo di gestione anziché a una sottoscrizione, la chiamata all'API REST cambia in modo simile alla seguente:To assign the blueprint to a management group instead of a subscription, the REST API call changes to look like this:

```http
PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{assignmentMG}/providers/Microsoft.Blueprint/blueprintAssignments/{assignmentName}?api-version=2018-11-01-preview
```

Il gruppo di `{assignmentMG}` gestione definito da deve essere all'interno della gerarchia del gruppo di gestione o essere lo stesso gruppo di gestione in cui viene salvata la definizione del blueprint.

Il corpo della richiesta dell'assegnazione del blueprint è simile al seguente:

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

La differenza principale nel corpo della richiesta e `properties.scope` un assegnato a una sottoscrizione è la proprietà. Questa proprietà obbligatoria deve essere impostata sulla sottoscrizione a cui si applica l'assegnazione del blueprint. La sottoscrizione deve essere un elemento figlio diretto della gerarchia del gruppo di gestione in cui è archiviata l'assegnazione del blueprint.

> [!NOTE]
> Un blueprint assegnato all'ambito del gruppo di gestione funziona ancora come assegnazione di blueprint a livello di sottoscrizione. L'unica differenza è dove viene archiviata l'assegnazione del blueprint per impedire ai proprietari della sottoscrizione di rimuovere l'assegnazione e i blocchi associati.

## <a name="removing-locking-states"></a>Eliminazione degli stati di blocco

Se si rende necessario modificare o eliminare una risorsa protetta da un'assegnazione, è possibile procedere in due modi.

- Aggiornare l'assegnazione del progetto impostando **Non bloccare** come modalità di blocco
- Eliminare l'assegnazione del progetto

Quando l'assegnazione viene rimossa, i blocchi creati da Azure Blueprints vengono rimossi. La risorsa viene tuttavia tralasciata e dovrà essere eliminati in modo normale.

## <a name="how-blueprint-locks-work"></a>Funzionamento dei blocchi progetto

In virtù del controllo degli accessi in base al ruolo, alle risorse artefatto viene applicata un'azione di [negazione assegnazioni](../../../role-based-access-control/deny-assignments.md) durante l'assegnazione di un progetto se per l'assegnazione è stata selezionata l'opzione **Sola lettura** o **Non eliminare**. L'azione di negazione viene aggiunta dall'identità gestita dell'assegnazione del progetto e può essere rimossa dalle risorse artefatto solo dalla stessa identità gestita. Questa misura di sicurezza applica il meccanismo di blocco e impedisce la rimozione del blocco del blueprint all'esterno di Azure Blueprints.This security measure enforces the locking mechanism and prevents removing the blueprint lock outside Azure Blueprints.

![Blueprint rifiuta assegnazione nel gruppo di risorse](../media/resource-locking/blueprint-deny-assignment.png)

Le proprietà di [negazione assegnazione](../../../role-based-access-control/deny-assignments.md#deny-assignment-properties) di ogni modalità sono le seguenti:

|Mode |Permissions.Azioni |Permissions.NotActions |Principali[i]. digitare |ExcludePrincipals[i]. Id | DoNotApplyToChildScopes |
|-|-|-|-|-|-|
|Sola lettura |**\*** |**\*/read (lettura)** |Definito da sistema (tutti) |assegnazione del blueprint e definita dall'utente in **excludedPrincipals** |Gruppo di risorse - _true_; Risorsa - _false_ |
|Non eliminare |**\*/delete** | |Definito da sistema (tutti) |assegnazione del blueprint e definita dall'utente in **excludedPrincipals** |Gruppo di risorse - _true_; Risorsa - _false_ |

> [!IMPORTANT]
> Azure Resource Manager memorizza nella cache i dettagli di assegnazione di ruolo per un massimo di 30 minuti. Di conseguenza, le azioni di negazione assegnazioni per le risorse del progetto potrebbero non essere completamente attive con effetto immediato. Durante questo periodo di tempo, potrebbe essere possibile eliminare una risorsa che deve essere protetta da blocchi di progetto.

## <a name="exclude-a-principal-from-a-deny-assignment"></a>Escludere un'entità da un'assegnazione di negazioneExclude a principal from a deny assignment

In alcuni scenari di progettazione o sicurezza, potrebbe essere necessario escludere un'entità [dall'assegnazione](../../../role-based-access-control/deny-assignments.md) di negazione creata dall'assegnazione del blueprint. Questo passaggio viene eseguito nell'API REST aggiungendo fino a cinque valori alla matrice **excludedPrincipals** nella proprietà **locks** durante [la creazione dell'assegnazione](/rest/api/blueprints/assignments/createorupdate). La definizione di assegnazione seguente è un esempio di corpo della richiesta che include **excludedPrincipals:**

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

## <a name="exclude-an-action-from-a-deny-assignment"></a>Escludere un'azione da un'assegnazione di negazioneExclude an action from a deny assignment

Analogamente [all'esclusione di un'entità](#exclude-a-principal-from-a-deny-assignment) in [un'assegnazione di negazione](../../../role-based-access-control/deny-assignments.md) in un'assegnazione di blueprint, è possibile escludere [specifiche operazioni RBAC.](../../../role-based-access-control/resource-provider-operations.md) All'interno del blocco **properties.locks,** nella stessa posizione in cui **è excludedPrincipals,** è possibile aggiungere **un excludedActions:**

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

Mentre **excludedPrincipals** deve essere esplicito, le `*` voci **excludedActions** possono utilizzare per la corrispondenza con caratteri jolly delle operazioni RBAC.

## <a name="next-steps"></a>Passaggi successivi

- Seguire [l'esercitazione Proteggere le nuove risorse.](../tutorials/protect-new-resources.md)
- Informazioni sul ciclo di vita del [blueprint.](lifecycle.md)
- Informazioni su come usare [parametri statici e dinamici](parameters.md).
- Informazioni su come personalizzare l'[ordine di sequenziazione del progetto](sequencing-order.md).
- Informazioni su come [aggiornare assegnazioni esistenti](../how-to/update-existing-assignments.md).
- Risolvere i problemi durante l'assegnazione di un progetto con la [risoluzione generale dei problemi](../troubleshoot/general.md).