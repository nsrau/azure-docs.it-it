---
title: Abilitare automaticamente il backup durante la creazione della macchina virtuale usando criteri di Azure
description: Articolo che descrive come usare i criteri di Azure per abilitare automaticamente il backup per tutte le macchine virtuali create in un determinato ambito
ms.topic: conceptual
ms.date: 11/08/2019
ms.openlocfilehash: ea3c0d217c8495078e91e171caef695bb32d129b
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/10/2019
ms.locfileid: "74980127"
---
# <a name="auto-enable-backup-on-vm-creation-using-azure-policy"></a>Abilitare automaticamente il backup durante la creazione della macchina virtuale usando criteri di Azure

Una delle responsabilità principali di un amministratore di backup o di conformità in un'organizzazione è garantire che tutti i computer cruciali per l'azienda vengano sottoposti a backup con il periodo di conservazione appropriato.

Attualmente, backup di Azure fornisce un criterio predefinito (usando criteri di Azure) che può essere assegnato a **tutte le macchine virtuali di Azure in una posizione specifica all'interno di una sottoscrizione o di un gruppo di risorse**. Quando questo criterio viene assegnato a un determinato ambito, tutte le nuove macchine virtuali create in tale ambito vengono automaticamente configurate per il backup in un insieme di credenziali **esistente nello stesso percorso e nella stessa sottoscrizione**. L'utente può specificare l'insieme di credenziali e i criteri di conservazione a cui devono essere associate le macchine virtuali di cui è stato eseguito il backup.

## <a name="supported-scenarios"></a>Scenari Supportati 

* I criteri predefiniti sono attualmente supportati solo per le macchine virtuali di Azure. Gli utenti devono prestare attenzione per assicurarsi che i criteri di conservazione specificati durante l'assegnazione siano criteri di conservazione delle macchine virtuali. Fare riferimento a [questo](https://aka.ms/PolicySupportedSKUs) documento per visualizzare tutti gli SKU di VM supportati da questo criterio.

* Il criterio può essere assegnato a una singola posizione e a una sottoscrizione alla volta. Per abilitare il backup per le macchine virtuali tra percorsi e sottoscrizioni, è necessario creare più istanze dell'assegnazione dei criteri, una per ogni combinazione di percorso e sottoscrizione.

* L'insieme di credenziali specificato e le macchine virtuali configurate per il backup possono trovarsi in gruppi di risorse diversi.

* Ambito del gruppo di gestione attualmente non supportato.

## <a name="using-the-built-in-policy"></a>Uso dei criteri predefiniti

Per assegnare i criteri all'ambito richiesto, attenersi alla procedura seguente:

1. Accedere al portale di Azure e passare al dashboard dei **criteri** .
2. Selezionare **definizioni** nel menu a sinistra per ottenere un elenco di tutti i criteri predefiniti tra le risorse di Azure.
3. Filtrare l'elenco per **Category = backup**. L'elenco viene filtrato in modo da ottenere un singolo criterio denominato ' Configure backup on VMS of a location to an existing Central Vault nello stesso percorso '.
Dashboard dei criteri di ![](./media/backup-azure-auto-enable-backup/policy-dashboard.png)
4. Fare clic sul nome del criterio. Si verrà reindirizzati alla definizione dettagliata per questo criterio.
pannello definizione criteri ![](./media/backup-azure-auto-enable-backup/policy-definition-blade.png)
5. Fare clic sul pulsante **assegna** nella parte superiore del pannello. In questo modo viene reindirizzato al pannello **assegna criterio** .
6. In **nozioni di base**fare clic sui tre puntini di sospensione accanto al campo **ambito** . Verrà aperto un pannello di contesto a destra in cui è possibile selezionare la sottoscrizione per il criterio da applicare. Facoltativamente, è anche possibile selezionare un gruppo di risorse, in modo che i criteri vengano applicati solo per le macchine virtuali in un determinato gruppo di risorse.
Nozioni di base sull'assegnazione di criteri ![](./media/backup-azure-auto-enable-backup/policy-assignment-basics.png)
7. Nella scheda **parametri** scegliere un percorso dall'elenco a discesa e selezionare l'insieme di credenziali e i criteri di backup a cui è necessario associare le VM nell'ambito.
![parametri di assegnazione dei criteri](./media/backup-azure-auto-enable-backup/policy-assignment-parameters.png)
8. Verificare che l' **effetto** sia impostato su deployIfNotExists.
9. Passare a **Verifica + crea** e fare clic su **Crea**.

> [!NOTE]
>
> Criteri di Azure può essere usato anche nelle macchine virtuali esistenti, usando la [correzione](https://docs.microsoft.com/azure/governance/policy/how-to/remediate-resources).

> [!NOTE]
>
> Si consiglia di non assegnare questo criterio a più di 200 macchine virtuali alla volta. Se il criterio è assegnato a più di 200 macchine virtuali, il backup potrebbe essere attivato alcune ore dopo il valore specificato dalla pianificazione.

## <a name="next-steps"></a>Fasi successive

[Altre informazioni su criteri di Azure](https://docs.microsoft.com/azure/governance/policy/overview)