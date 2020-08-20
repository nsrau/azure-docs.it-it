---
title: Abilitare automaticamente il backup durante la creazione di macchine virtuali con Criteri di Azure
description: Articolo che descrive come usare i criteri di Azure per abilitare automaticamente il backup per tutte le macchine virtuali create in un determinato ambito
ms.topic: conceptual
ms.date: 11/08/2019
ms.openlocfilehash: 19985ebc51fe713ee0392800e2791ea1891ff3cd
ms.sourcegitcommit: cd0a1ae644b95dbd3aac4be295eb4ef811be9aaa
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/19/2020
ms.locfileid: "88612674"
---
# <a name="auto-enable-backup-on-vm-creation-using-azure-policy"></a>Abilitare automaticamente il backup durante la creazione di macchine virtuali con Criteri di Azure

Una delle responsabilità principali di un amministratore di backup o di conformità in un'organizzazione è garantire che tutti i computer cruciali per l'azienda vengano sottoposti a backup con il periodo di conservazione appropriato.

Attualmente, backup di Azure fornisce un criterio predefinito (usando criteri di Azure) che può essere assegnato a **tutte le macchine virtuali di Azure in una posizione specifica all'interno di una sottoscrizione o di un gruppo di risorse**. Quando questo criterio viene assegnato a un determinato ambito, tutte le nuove macchine virtuali create in tale ambito vengono automaticamente configurate per il backup in un insieme di credenziali **esistente nello stesso percorso e nella stessa sottoscrizione**. L'utente può specificare l'insieme di credenziali e i criteri di conservazione a cui devono essere associate le macchine virtuali di cui è stato eseguito il backup.

## <a name="supported-scenarios"></a>Scenari Supportati

* I criteri predefiniti sono attualmente supportati solo per le macchine virtuali di Azure. Gli utenti devono prestare attenzione per assicurarsi che i criteri di conservazione specificati durante l'assegnazione siano criteri di conservazione delle macchine virtuali. Fare riferimento a [questo](./backup-azure-policy-supported-skus.md) documento per visualizzare tutti gli SKU di VM supportati da questo criterio.

* Il criterio può essere assegnato a una singola posizione e a una sottoscrizione alla volta. Per abilitare il backup per le macchine virtuali tra percorsi e sottoscrizioni, è necessario creare più istanze dell'assegnazione dei criteri, una per ogni combinazione di percorso e sottoscrizione.

* L'insieme di credenziali specificato e le macchine virtuali configurate per il backup possono trovarsi in gruppi di risorse diversi.

* Ambito del gruppo di gestione attualmente non supportato.

* Il criterio predefinito non è attualmente disponibile nei cloud nazionali.

## <a name="using-the-built-in-policy"></a>Uso dei criteri predefiniti

Per assegnare i criteri all'ambito richiesto, attenersi alla procedura seguente:

1. Accedere al portale di Azure e passare al dashboard dei **criteri** .
1. Selezionare **definizioni** nel menu a sinistra per ottenere un elenco di tutti i criteri predefiniti tra le risorse di Azure.
1. Filtrare l'elenco per **Category = backup**. L'elenco verrà filtrato in modo da visualizzare un singolo criterio denominato ' Configure backup on VMS of a location to an existing Central Vault nello stesso percorso '.
![Dashboard dei criteri](./media/backup-azure-auto-enable-backup/policy-dashboard.png)
1. Selezionare il nome del criterio. Si verrà reindirizzati alla definizione dettagliata per questo criterio.
![Riquadro definizione criteri](./media/backup-azure-auto-enable-backup/policy-definition-blade.png)
1. Selezionare il pulsante **assegna** nella parte superiore del riquadro. Viene quindi reindirizzati al riquadro **assegna criterio** .
1. In **nozioni di base**selezionare i tre puntini di sospensione accanto al campo **ambito** . Verrà aperto un riquadro di contesto a destra in cui è possibile selezionare la sottoscrizione per il criterio da applicare. Facoltativamente, è anche possibile selezionare un gruppo di risorse, in modo che i criteri vengano applicati solo per le macchine virtuali in un determinato gruppo di risorse.
![Nozioni fondamentali sull'assegnazione di criteri](./media/backup-azure-auto-enable-backup/policy-assignment-basics.png)
1. Nella scheda **parametri** scegliere un percorso dall'elenco a discesa e selezionare l'insieme di credenziali e i criteri di backup a cui è necessario associare le VM nell'ambito.
![Parametri di assegnazione dei criteri](./media/backup-azure-auto-enable-backup/policy-assignment-parameters.png)
1. Verificare che l' **effetto** sia impostato su deployIfNotExists.
1. Passare a **Verifica + crea** e selezionare **Crea**.

> [!NOTE]
>
> Criteri di Azure può essere usato anche nelle macchine virtuali esistenti, usando la [correzione](../governance/policy/how-to/remediate-resources.md).

> [!NOTE]
>
> Si consiglia di non assegnare questo criterio a più di 200 macchine virtuali alla volta. Se il criterio è assegnato a più di 200 macchine virtuali, il backup potrebbe essere attivato alcune ore dopo il valore specificato dalla pianificazione.

## <a name="next-steps"></a>Passaggi successivi

[Altre informazioni su criteri di Azure](../governance/policy/overview.md)
