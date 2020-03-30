---
title: Abilitare automaticamente il backup durante la creazione di macchine virtuali con Criteri di Azure
description: Un articolo che descrive come usare criteri di Azure per abilitare automaticamente il backup per tutte le macchine virtuali create in un determinato ambito
ms.topic: conceptual
ms.date: 11/08/2019
ms.openlocfilehash: 1d423371788f87155328e8f5c9334e47349a68e8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77584269"
---
# <a name="auto-enable-backup-on-vm-creation-using-azure-policy"></a>Abilitare automaticamente il backup durante la creazione di macchine virtuali con Criteri di Azure

Una delle responsabilità principali di un amministratore di backup o conformità in un'organizzazione consiste nel garantire che venga eseguito il backup di tutti i computer critici per l'azienda con la conservazione appropriata.

Oggi, Backup di Azure offre un criterio predefinito (usando criteri di Azure) che possono essere assegnati a tutte le macchine virtuali di **Azure in una posizione specificata all'interno**di una sottoscrizione o di un gruppo di risorse. Quando questo criterio viene assegnato a un determinato ambito, tutte le nuove macchine virtuali create in tale ambito vengono configurate automaticamente per il backup in un **insieme di credenziali esistente nella stessa posizione e sottoscrizione.** L'utente può specificare l'insieme di credenziali e i criteri di conservazione a cui devono essere associate le macchine virtuali di cui è stato eseguito il backup.

## <a name="supported-scenarios"></a>Scenari Supportati

* I criteri predefiniti sono attualmente supportati solo per le macchine virtuali di Azure.The built-in policy is currently supported only for Azure VMs. Gli utenti devono prestare attenzione a garantire che il criterio di conservazione specificato durante l'assegnazione sia un criterio di conservazione della macchina virtuale. Fare riferimento a [questo](https://docs.microsoft.com/azure/backup/backup-azure-policy-supported-skus) documento per visualizzare tutti gli SKU delle macchine virtuali supportati da questo criterio.

* Il criterio può essere assegnato a una singola posizione e sottoscrizione alla volta. Per abilitare il backup per le macchine virtuali tra percorsi e sottoscrizioni, è necessario creare più istanze dell'assegnazione dei criteri, una per ogni combinazione di posizione e sottoscrizione.

* L'insieme di credenziali specificato e le macchine virtuali configurate per il backup possono trovarsi in gruppi di risorse diversi.

* L'ambito del gruppo di gestione non è attualmente supportato.

* La politica integrata non è attualmente disponibile nei cloud nazionali.

## <a name="using-the-built-in-policy"></a>Utilizzo del criterio predefinito

Per assegnare il criterio all'ambito richiesto, attenersi alla seguente procedura:

1. Accedere al portale di Azure e passare al dashboard **dei criteri.**
2. Selezionare **Definizioni** nel menu a sinistra per ottenere un elenco di tutti i criteri predefiniti tra le risorse di Azure.Select Definitions in the left menu to get a list of all built-in policies across Azure Resources.
3. Filtrare l'elenco per **Categoria-Backup**. L'elenco verrà filtrato in base a un singolo criterio denominato "Configurare il backup nelle macchine virtuali di una posizione in un vault centrale esistente nella stessa posizione".
![Dashboard dei criteri](./media/backup-azure-auto-enable-backup/policy-dashboard.png)
4. Fare clic sul nome del criterio. Si verrà reindirizzati alla definizione dettagliata di questo criterio.
![Blade definizione criteri](./media/backup-azure-auto-enable-backup/policy-definition-blade.png)
5. Fare clic sul pulsante **Assegna** nella parte superiore del pannello. In questo modo si reindirizza al pannello **Assegna criterio.**
6. In **Nozioni di base**fare clic sui tre puntini accanto al campo **Ambito.** Verrà aperto un pannello di contesto corretto in cui è possibile selezionare la sottoscrizione per il criterio da applicare. È anche possibile selezionare facoltativamente un gruppo di risorse, in modo che il criterio venga applicato solo alle macchine virtuali in un determinato gruppo di risorse.
![Nozioni di base sulle assegnazioni dei criteri](./media/backup-azure-auto-enable-backup/policy-assignment-basics.png)
7. Nella scheda **Parametri,** scegliere una posizione dall'elenco a discesa e selezionare l'insieme di credenziali e i criteri di backup a cui devono essere associate le macchine virtuali nell'ambito.
![Parametri di assegnazione dei criteri](./media/backup-azure-auto-enable-backup/policy-assignment-parameters.png)
8. Assicurarsi che **Effect** sia impostato su deployIfNotExists.
9. Passare a **Revisione e creazione** e fare clic su **Crea**.

> [!NOTE]
>
> I criteri di Azure possono essere usati anche nelle macchine virtuali esistenti, usando [la correzione.](https://docs.microsoft.com/azure/governance/policy/how-to/remediate-resources)

> [!NOTE]
>
> È consigliabile che questo criterio non sia assegnato a più di 200 macchine virtuali alla volta. Se il criterio è assegnato a più di 200 macchine virtuali, il backup può essere attivato poche ore dopo rispetto a quello specificato dalla pianificazione.

## <a name="next-steps"></a>Passaggi successivi

[Altre informazioni su Criteri di AzureLearn more about Azure Policy](https://docs.microsoft.com/azure/governance/policy/overview)
