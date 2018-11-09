---
title: Comprendere il blocco risorse di Azure Blueprint
description: Informazioni sulle opzioni di blocco per proteggere le risorse durante l'assegnazione di un progetto.
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 10/25/2018
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: 4e71797837927fe5f5233bcf88d35fef98f504e9
ms.sourcegitcommit: 0f54b9dbcf82346417ad69cbef266bc7804a5f0e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/26/2018
ms.locfileid: "50139443"
---
# <a name="understand-resource-locking-in-azure-blueprints"></a>Comprendere il blocco risorse di Azure Blueprint

La creazione di ambienti coerenti su larga scala è davvero efficace solo se esiste un meccanismo che mantenga tale coerenza. Questo articolo spiega il funzionamento del blocco risorse di Azure Blueprint.

## <a name="locking-modes-and-states"></a>Modalità di blocco e stati

Modalità di blocco si applica all'assegnazione del progetto e presenta solo due opzioni: **Nessuna** oppure **Tutte le risorse**. La modalità di blocco viene configurata durante l'assegnazione di progetto e non può essere modificato una volta che l'assegnazione è stata correttamente applicata alla sottoscrizione.

Le risorse create da artefatti in un'assegnazione progetto hanno tre stati: **Non bloccato**, **Sola lettura** o **Impossibile modificare/eliminare**. Ogni elemento può essere nello stato **Non bloccato**. Tuttavia, gli artefatti di gruppi non corrispondenti a risorsa hanno lo stato **Sola lettura** e i gruppi di risorse gli stati **Impossibile modificare/eliminare**. Questa differenza è una distinzione importante per la modalità di gestione di queste risorse.

Lo stato **Sola lettura** è perfettamente descritto dalla sua definizione : non è possibile modificare la risorsa in alcun modo, non è consentita nessuna modifica e non può essere eliminata. Lo stato **Impossibile modificare/eliminare** presenta più sfumature a causa della natura a "contenitore" del gruppo di risorse. L'oggetto gruppo di risorse è di sola lettura, ma è possibile apportare modifiche alle risorse non bloccate all'interno del gruppo di risorse.

## <a name="overriding-locking-states"></a>Sostituzione degli stati di blocco

È in genere possibile che a un utente con [controllo degli accessi in base al ruolo](../../../role-based-access-control/overview.md) (RBAC) appropriato per la sottoscrizione, ad esempio il ruolo "Proprietario", sia consentito di modificare o eliminare qualsiasi risorsa. Questo tipo di accesso non è appropriato quando si applica il blocco ai progetti come parte di un'assegnazione distribuita. Se l'assegnazione è stata impostata con l'opzione **Blocca**, neanche la sottoscrizione di proprietario può modificare le risorse incluse.

Questa misura di sicurezza salvaguarda la coerenza del progetto definito e l'ambiente che è stato progettato per creare a partire da eliminazioni accidentali o programmatiche.

## <a name="removing-locking-states"></a>Eliminazione degli stati di blocco

In caso di necessità di eliminazione delle risorse create da un'assegnazione, un modo per eseguire questa operazione è rimuovere in primo luogo l'assegnazione. Quando viene rimossa l'assegnazione, vengono rimossi i blocchi creati da Blueprint. La risorsa viene tuttavia tralasciata e dovrà essere eliminati in modo normale.

## <a name="how-blueprint-locks-work"></a>Funzionamento dei blocchi progetto

Un ruolo RBAC `denyAssignments` viene applicato alle risorse artefatte durante l'assegnazione di un progetto, se l'assegnazione ha selezionato l'opzione **Blocca**. Il ruolo viene aggiunto dall'identità gestita dell'assegnazione di progetto e può essere eliminato dalle risorse artefatte solo dalla stessa identità gestita. Questa misura di sicurezza consente di applicare il meccanismo di blocco e impedisce di eliminare il blocco di progetto al di fuori di Blueprint. L'eliminazione del ruolo e del blocco è possibile solo tramite l'eliminazione dell'assegnazione di progetto, che può essere eseguita solo da utenti con autorizzazioni appropriate.

> [!IMPORTANT]
> Azure Resource Manager memorizza nella cache i dettagli di assegnazione di ruolo per un massimo di 30 minuti. Di conseguenza, `denyAssignments` nelle risorse del progetto può non essere attivo completamente attivo con effetto immediato. Durante questo periodo di tempo, potrebbe essere possibile eliminare una risorsa che deve essere protetta da blocchi di progetto.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni sul [ciclo di vita del progetto](lifecycle.md)
- Informazioni su come usare [parametri statici e dinamici](parameters.md)
- Informazioni sulla personalizzazione della [sequenziazione di progetto](sequencing-order.md)
- Informazioni su come [aggiornare le assegnazioni esistenti](../how-to/update-existing-assignments.md)
- Risolvere i problemi durante l'assegnazione di un progetto con la [risoluzione generale dei problemi](../troubleshoot/general.md)