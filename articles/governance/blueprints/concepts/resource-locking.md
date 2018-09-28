---
title: Comprendere il blocco risorse di Azure Blueprint
description: Informazioni sulle opzioni di blocco per proteggere le risorse durante l'assegnazione di un progetto.
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 09/18/2018
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: 718c23b806da5058c042b961077e51ba0d4b6245
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/24/2018
ms.locfileid: "46973253"
---
# <a name="understand-resource-locking-in-azure-blueprints"></a>Comprendere il blocco risorse di Azure Blueprint

La creazione di ambienti coerenti su larga scala è davvero efficace solo se esiste un meccanismo che garantisca che la coerenza sia persistente. Questo articolo spiega il funzionamento del blocco risorse di Azure Blueprint.

## <a name="locking-modes-and-states"></a>Modalità di blocco e stati

Modalità di blocco si applica all'assegnazione del progetto e presenta solo due opzioni: **Nessuna** oppure **Tutte le risorse**. Viene configurato durante l'assegnazione di progetto e non può essere modificato una volta che l'assegnazione è stata correttamente applicata alla sottoscrizione.

Le risorse create da definizioni artefatte all'interno del progetto assegnato alla sottoscrizione hanno tre stati: **Non bloccato**, **Sola lettura** o **Impossibile modificare/eliminare**. Qualsiasi tipo di artefatto può avere lo stato **Non bloccato**. Tuttavia, gli artefatti di gruppi non corrispondenti a risorsa hanno lo stato **Sola lettura** e i gruppi di risorse lo stato **Impossibile modificare/eliminare**. Questa è una distinzione importante per la modalità di gestione di queste risorse.

Lo stato **Sola lettura** è perfettamente descritto dalla sua definizione : non è possibile modificare la risorsa in alcun modo, non è consentita nessuna modifica e non può essere eliminata. Lo stato **Impossibile modificare/eliminare** presenta più sfumature a causa della natura a "contenitore" del gruppo di risorse. L'oggetto gruppo di risorse è di sola lettura, ma è possibile creare, aggiornare ed eliminare risorse all'interno del gruppo di risorse, purché non facciano parte di assegnazioni di progetto con lo stato di blocco **Sola lettura**.

## <a name="overriding-locking-states"></a>Sostituzione degli stati di blocco

Anche se in genere un utente con adeguato [controllo degli accessi in base al ruolo](../../../role-based-access-control/overview.md) (RBAC) nella sottoscrizione, ad esempio il ruolo "Proprietario", può modificare o eliminare qualsiasi risorsa, ciò non avviene quando Blueprint applica il blocco come parte di un'assegnazione distribuita. Se l'assegnazione è stata impostata con l'opzione **Blocca**, neanche la sottoscrizione di proprietario può modificare le risorse incluse.

In questo modo si salvaguarda la coerenza del progetto definito e l'ambiente che è stato progettato per creare a partire da eliminazioni accidentali o programmatiche.

## <a name="removing-locking-states"></a>Eliminazione degli stati di blocco

In caso di necessità di eliminazione delle risorse create da un'assegnazione, l'unico modo per eseguire questa operazione è rimuovere in primo luogo l'assegnazione. Quando viene rimossa l'assegnazione, vengono rimossi i blocchi creati da Blueprint. La risorsa, tuttavia, non viene tralasciata ed è quindi necessario che venga eliminata normalmente da un utente che dispone delle autorizzazioni appropriate.

## <a name="how-blueprint-locks-work"></a>Funzionamento dei blocchi progetto

Un ruolo RBAC `denyAssignments` viene applicato alle risorse artefatte durante l'assegnazione di un progetto, se l'assegnazione ha selezionato l'opzione **Blocca**. Il ruolo viene aggiunto dall'identità gestita dell'assegnazione di progetto e può essere eliminato dalle risorse artefatte solo dalla stessa identità gestita. Questo consente di applicare il meccanismo di blocco e impedisce tentativi di eliminazione del blocco di progetto al di fuori di Blueprint. L'eliminazione del ruolo e del blocco è possibile solo tramite l'eliminazione dell'assegnazione di progetto, che può essere eseguita solo da utenti con autorizzazioni appropriate.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni sul [ciclo di vita del progetto](lifecycle.md)
- Informazioni su come usare [parametri statici e dinamici](parameters.md)
- Informazioni sulla personalizzazione della [sequenziazione di progetto](sequencing-order.md)
- Informazioni su come [aggiornare le assegnazioni esistenti](../how-to/update-existing-assignments.md)
- Risolvere i problemi durante l'assegnazione di un progetto con la [risoluzione generale dei problemi](../troubleshoot/general.md)