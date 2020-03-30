---
title: Abilitare la replica per le macchine virtuali di Azure crittografate in Azure Site RecoveryEnable replication for encrypted Azure VMs in Azure Site Recovery
description: In questo articolo viene descritto come configurare la replica per le macchine virtuali con la chiave gestita dal cliente (CMK) dischi abilitati da un'area di Azure a un'altra utilizzando Site Recovery.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 01/10/2020
ms.author: mayg
ms.openlocfilehash: 367f29237a3f2a634f209026df47b0cbd6ffc97c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75897962"
---
# <a name="replicate-machines-with-customer-managed-keys-cmk-enabled-disks"></a>Replicare i computer con i dischi abilitati per le chiavi gestite dal cliente (CMK)

Questo articolo descrive come replicare le macchine virtuali di Azure con le chiavi gestite dal cliente (CMK) abilitate per i dischi gestiti, da un'area di Azure a un'altra.

## <a name="prerequisite"></a>Prerequisito
È necessario creare i set di crittografia del disco nell'area di destinazione per la sottoscrizione di destinazione prima di abilitare la replica per le macchine virtuali che dispongono di dischi gestiti abilitati per CMK.

## <a name="enable-replication"></a>Abilitare la replica

Per questo esempio, l'area primaria di Azure è l'Asia orientale e l'area secondaria è Asia sudorientale.

1. Nell'insieme di credenziali, **selezionare .**
2. Tenere presente i campi seguenti.
    - **Origine:** punto di origine delle macchine virtuali, in questo caso **Azure**.
    - **Percorso di origine:** l'area di Azure in cui si vogliono proteggere le macchine virtuali. Per questo esempio, il percorso di origine è "Asia orientale".
    - **Modello di distribuzione:** modello di distribuzione di Azure delle macchine di origine.
    - **Sottoscrizione di origine**: sottoscrizione a cui appartengono le macchine virtuali di origine. Può trattarsi di qualsiasi sottoscrizione presente nello stesso tenant di Azure Active Directory dell'insieme di credenziali dei servizi di ripristino.
    - **Gruppo di risorse:** gruppo di risorse a cui appartengono le macchine virtuali di origine. Tutte le macchine virtuali nel gruppo di risorse selezionato vengono elencate per la protezione nel passaggio successivo.

3. In **Macchine** > virtuali**Selezionare le macchine virtuali selezionare**ogni macchina virtuale che si vuole replicare. È possibile selezionare solo i computer per cui è possibile abilitare la replica. Selezionare quindi **OK**.

4. In **Impostazioni**è possibile configurare le seguenti impostazioni del sito di destinazione.

    - **Percorso di destinazione:** il percorso in cui verranno replicati i dati della macchina virtuale di origine. Site Recovery fornisce un elenco di aree di destinazione adatte in base alla posizione della macchina selezionata. Si consiglia di utilizzare la stessa posizione della posizione dell'insieme di credenziali dei servizi di ripristino.
    - **Sottoscrizione di**destinazione: sottoscrizione di destinazione usata per il ripristino di emergenza. Per impostazione predefinita, la sottoscrizione di destinazione è uguale a quella di origine.
    - **Gruppo di risorse di destinazione:** gruppo di risorse a cui tutte le macchine virtuali replicate appartengono. Per impostazione predefinita, Site Recovery crea un nuovo gruppo di risorse nell'area di destinazione. Il nome `asr` ottiene il suffisso. Se esiste già un gruppo di risorse creato da Azure Site Recovery, viene riutilizzato. È inoltre possibile scegliere di personalizzarlo, come illustrato nella sezione seguente. La posizione del gruppo di risorse di destinazione può essere qualsiasi area di Azure, ad eccezione dell'area in cui sono ospitate le macchine virtuali di origine.
    - **Rete virtuale di destinazione:** per impostazione predefinita, Site Recovery crea una nuova rete virtuale nell'area di destinazione. Il nome `asr` ottiene il suffisso. Viene mappato alla rete di origine e utilizzato per qualsiasi protezione futura. [Altre informazioni](site-recovery-network-mapping-azure-to-azure.md) sul mapping di rete.
    - Account di archiviazione di destinazione (se la macchina virtuale di **origine non usa dischi gestiti):** per impostazione predefinita, Site Recovery crea un nuovo account di archiviazione di destinazione inviando un'imitazione della configurazione di archiviazione della macchina virtuale di origine. Se esiste già un account di archiviazione, viene riutilizzato.
    - **Dischi gestiti di replica (se la macchina virtuale di origine utilizza dischi gestiti):** Site Recovery crea nuovi dischi gestiti di replica nell'area di destinazione per eseguire il mirroring dei dischi gestiti della macchina virtuale di origine dello stesso tipo di archiviazione (standard o premium) dei dischi gestiti della macchina virtuale di origine.
    - **Account di archiviazione cache:** Site Recovery richiede un account di archiviazione aggiuntivo denominato *cache storage* nell'area di origine. Tutte le modifiche nelle macchine virtuali di origine vengono rilevate e inviate all'account di archiviazione della cache. Vengono quindi replicati nel percorso di destinazione.
    - **Set di disponibilità:** per impostazione predefinita, Site Recovery crea un nuovo set di disponibilità nell'area di destinazione. Il nome `asr` ha il suffisso. Se esiste già un set di disponibilità creato da Site Recovery, viene riutilizzato.
    - Set di **crittografia del disco (DES):** Site Recovery richiede l'utilizzo dei set di crittografia del disco per i dischi gestiti di replica e di destinazione. È necessario pre-creare DES nella sottoscrizione di destinazione e nell'area di destinazione prima di abilitare la replica. Per impostazione predefinita, un DES non è selezionato. È necessario fare clic su 'Personalizza' per scegliere un DES per disco di origine.
    - **Criteri di replica:** definisce le impostazioni per la cronologia di conservazione dei punti di ripristino e la frequenza degli snapshot coerente con le app. Per impostazione predefinita, Site Recovery crea un nuovo criterio di replica con impostazioni predefinite di *24 ore* per la conservazione dei punti di ripristino e *60 minuti* per la frequenza degli snapshot coerente con l'app.

    ![Abilitare la replica per il computer con dischi abilitati CMKEnable Replication for machine with CMK enabled disks](./media/azure-to-azure-how-to-enable-replication-cmk-disks/cmk-enable-dr.png)

## <a name="customize-target-resources"></a>Personalizzare le risorse di destinazione

Seguire questi passaggi per modificare le impostazioni di destinazione predefinite di Site Recovery.

1. Selezionare **Personalizza** accanto a "Sottoscrizione di destinazione" per modificare la sottoscrizione di destinazione predefinita. Selezionare la sottoscrizione dall'elenco delle sottoscrizioni disponibili nel tenant di Azure AD.

2. Selezionare Personalizza accanto a "Gruppo di risorse, Rete, Archiviazione e Set di disponibilità" per modificare le impostazioni predefinite seguenti:Select **Customize** next to "Resource group, Network, Storage, and Availability sets" to modify the following default settings:
    - In Gruppo di **risorse di destinazione**selezionare il gruppo di risorse dall'elenco dei gruppi di risorse nel percorso di destinazione della sottoscrizione.
    - In **Rete virtuale**di destinazione selezionare la rete da un elenco di reti virtuali nel percorso di destinazione.
    - Per **Set di disponibilità**, è possibile aggiungere le impostazioni del set di disponibilità alla macchina virtuale, se fanno parte di un set di disponibilità nell'area di origine.
    - In **Account di archiviazione di destinazione**selezionare l'account da utilizzare.

3. Selezionare **Personalizza** accanto a "Impostazioni di crittografia archiviazione" per selezionare il DES di destinazione per ogni disco gestito di origine abilitato per la chiave gestita dal cliente (CMK). Al momento della selezione, sarà anche possibile vedere a quale insieme di credenziali di chiave di destinazione è associato il DES.

4. Selezionare **Crea risorsa** > di destinazione**Abilita replica**.
5. Dopo aver abilitato le macchine virtuali per la replica, è possibile controllare lo stato di integrità delle macchine virtuali in **Elementi replicati**.

![Abilitare la replica per il computer con dischi abilitati CMKEnable Replication for machine with CMK enabled disks](./media/azure-to-azure-how-to-enable-replication-cmk-disks/cmk-customize-target-disk-properties.png)

>[!NOTE]
>Durante la replica iniziale, l'aggiornamento dello stato potrebbe richiedere del tempo, senza alcun progresso apparente. Fare clic su **Aggiorna** per ottenere lo stato più recente.

## <a name="faqs"></a>Domande frequenti

* Ho abilitato CMK su un elemento replicato esistente, come posso garantire che CMK venga applicato anche all'area di destinazione?

    È possibile individuare il nome del disco gestito della replica (creato da Azure Site Recovery nell'area di destinazione) e collegare DES a questo disco di replica. Tuttavia, non sarà possibile visualizzare i dettagli DES nel pannello Dischi una volta collegato. In alternativa, è possibile scegliere di disabilitare la replica della macchina virtuale e abilitarla nuovamente. Verrà visualizzato i dettagli DES e dell'insieme di credenziali delle chiavi nel pannello Dischi per l'elemento replicato.

* Ho aggiunto un nuovo disco abilitato CMK all'elemento replicato. Come è possibile replicare questo disco con Azure Site Recovery?

    L'aggiunta di un nuovo disco abilitato CMK a un elemento replicato esistente non è supportata. Disabilitare la replica e abilitarla nuovamente per la macchina virtuale.

