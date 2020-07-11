---
title: Abilitare la replica per le VM di Azure crittografate in Azure Site Recovery
description: Questo articolo descrive come configurare la replica per le macchine virtuali con dischi abilitati per la chiave gestita dal cliente (CMK) da un'area di Azure a un'altra usando Site Recovery.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 07/10/2020
ms.author: mayg
ms.openlocfilehash: 92b35284fd7bbb3d4f1196ee0d9bae4ce42d7c9e
ms.sourcegitcommit: f844603f2f7900a64291c2253f79b6d65fcbbb0c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/10/2020
ms.locfileid: "86224112"
---
# <a name="replicate-machines-with-customer-managed-keys-cmk-enabled-disks"></a>Replicare i computer con i dischi abilitati per le chiavi gestite dal cliente (CMK)

Questo articolo descrive come replicare le VM di Azure con i dischi gestiti abilitati per le chiavi gestite dal cliente (CMK), da un'area di Azure a un'altra.

## <a name="prerequisite"></a>Prerequisito
Prima di abilitare la replica per le macchine virtuali con dischi gestiti abilitati per CMK, è necessario creare i set di crittografia dei dischi nell'area di destinazione per la sottoscrizione di destinazione.

## <a name="enable-replication"></a>Abilitare la replica

Per questo esempio, l'area di Azure primaria è Asia orientale e l'area secondaria è Asia orientale sud.

1. Nell'insieme di credenziali selezionare **+ replica**.
2. Si notino i campi seguenti.
    - **Origine:** punto di origine delle macchine virtuali, in questo caso **Azure**.
    - **Percorso di origine**: l'area di Azure in cui si desidera proteggere le macchine virtuali. Per questo esempio, il percorso di origine è "Asia orientale".
    - **Modello di distribuzione**: il modello di distribuzione di Azure dei computer di origine.
    - **Sottoscrizione di origine**: sottoscrizione a cui appartengono le macchine virtuali di origine. Può trattarsi di qualsiasi sottoscrizione che si trovi nello stesso tenant di Azure Active Directory dell'insieme di credenziali di servizi di ripristino.
    - **Gruppo di risorse:** gruppo di risorse a cui appartengono le macchine virtuali di origine. Tutte le macchine virtuali nel gruppo di risorse selezionato sono elencate per la protezione nel passaggio successivo.

3. In **macchine virtuali**  >  **selezionare macchine virtuali**, selezionare tutte le VM che si desidera replicare. È possibile selezionare solo i computer per cui è possibile abilitare la replica. Quindi selezionare **OK**.

4. In **Impostazioni**è possibile configurare le seguenti impostazioni del sito di destinazione.

    - **Percorso di destinazione**: il percorso in cui verranno replicati i dati delle macchine virtuali di origine. Site Recovery fornisce un elenco di aree di destinazione appropriate in base alla posizione del computer selezionato. Si consiglia di usare la stessa località del percorso dell'insieme di credenziali di servizi di ripristino.
    - **Sottoscrizione di destinazione**: sottoscrizione di destinazione usata per il ripristino di emergenza. Per impostazione predefinita, la sottoscrizione di destinazione è uguale a quella di origine.
    - **Gruppo di risorse di destinazione:** gruppo di risorse a cui tutte le macchine virtuali replicate appartengono. Per impostazione predefinita, Site Recovery crea un nuovo gruppo di risorse nell'area di destinazione. Il nome ottiene il `asr` suffisso. Se esiste già un gruppo di risorse creato da Azure Site Recovery, viene riutilizzato. È anche possibile scegliere di personalizzarlo, come illustrato nella sezione seguente. Il percorso del gruppo di risorse di destinazione può essere qualsiasi area di Azure, ad eccezione dell'area in cui sono ospitate le macchine virtuali di origine.
    - **Rete virtuale di destinazione**: per impostazione predefinita, Site Recovery crea una nuova rete virtuale nell'area di destinazione. Il nome ottiene il `asr` suffisso. Viene mappato alla rete di origine e usato per la protezione futura. [Altre informazioni](./azure-to-azure-network-mapping.md) sul mapping di rete.
    - **Account di archiviazione di destinazione (se la VM di origine non usa dischi gestiti)**: per impostazione predefinita, Site Recovery crea un nuovo account di archiviazione di destinazione simulando la configurazione dell'archiviazione della VM di origine. Se un account di archiviazione esiste già, viene riusato.
    - **Dischi gestiti di replica (se la VM di origine usa dischi gestiti)**: Site Recovery crea nuovi dischi gestiti di replica nell'area di destinazione per eseguire il mirroring dei dischi gestiti della VM di origine dello stesso tipo di archiviazione (standard o Premium) dei dischi gestiti della VM di origine.
    - **Account di archiviazione della cache**: Site Recovery necessita di un account di archiviazione aggiuntivo denominato *archiviazione della cache* nell'area di origine. Tutte le modifiche apportate alle VM di origine vengono registrate e inviate all'account di archiviazione della cache. Vengono quindi replicati nel percorso di destinazione.
    - **Set di disponibilità**: per impostazione predefinita, Site Recovery crea un nuovo set di disponibilità nell'area di destinazione. Il nome presenta il `asr` suffisso. Se un set di disponibilità creato da Site Recovery esiste già, viene riutilizzato.
    - **Set di crittografia dischi (des)**: Site Recovery necessita di set di crittografia del disco da usare per i dischi gestiti di replica e di destinazione. Prima di abilitare la replica, è necessario pre-creare DES nella sottoscrizione di destinazione e nell'area di destinazione. Per impostazione predefinita, un DES non è selezionato. È necessario fare clic su "Personalizza" per scegliere una DES per ogni disco di origine.
    - **Criteri di replica**: definisce le impostazioni per la cronologia di conservazione dei punti di ripristino e la frequenza degli snapshot coerenti con l'app. Per impostazione predefinita, Site Recovery crea un nuovo criterio di replica con impostazioni predefinite di *24 ore* per la conservazione del punto di ripristino e *60 minuti* per la frequenza degli snapshot coerenti con l'app.

    ![Abilitare la replica per il computer con dischi abilitati per CMK](./media/azure-to-azure-how-to-enable-replication-cmk-disks/cmk-enable-dr.png)

## <a name="customize-target-resources"></a>Personalizzare le risorse di destinazione

Per modificare le impostazioni di destinazione predefinite Site Recovery, attenersi alla seguente procedura.

1. Selezionare **Personalizza** accanto a "sottoscrizione di destinazione" per modificare la sottoscrizione di destinazione predefinita. Selezionare la sottoscrizione dall'elenco di sottoscrizioni disponibili nel tenant del Azure AD.

2. Selezionare **Personalizza** accanto a "gruppo di risorse, rete, archiviazione e set di disponibilità" per modificare le impostazioni predefinite seguenti:
    - Per **gruppo di risorse di destinazione**selezionare il gruppo di risorse dall'elenco dei gruppi di risorse nel percorso di destinazione della sottoscrizione.
    - In **rete virtuale di destinazione**selezionare la rete da un elenco di reti virtuali nel percorso di destinazione.
    - Per il **set di disponibilità**, è possibile aggiungere le impostazioni del set di disponibilità alla VM, se sono parte di un set di disponibilità nell'area di origine.
    - Per **account di archiviazione di destinazione**selezionare l'account da usare.

3. Selezionare **Personalizza** accanto a "impostazioni di crittografia di archiviazione" per selezionare le des di destinazione per ogni disco gestito di origine abilitato per la chiave gestita dal cliente (CMK). Al momento della selezione, sarà possibile vedere anche l'insieme di credenziali delle chiavi di destinazione a cui è associata la DES.

4. Selezionare **Crea risorsa di destinazione**  >  **Abilita replica**.
5. Quando le macchine virtuali sono abilitate per la replica, è possibile controllare lo stato di integrità delle macchine virtuali in **elementi replicati**.

![Abilitare la replica per il computer con dischi abilitati per CMK](./media/azure-to-azure-how-to-enable-replication-cmk-disks/cmk-customize-target-disk-properties.png)

>[!NOTE]
>Durante la replica iniziale, l'aggiornamento dello stato potrebbe richiedere del tempo, senza lo stato di avanzamento. Fare clic su **Aggiorna** per ottenere lo stato più recente.

## <a name="faqs"></a>Domande frequenti

* Ho abilitato CMK su un elemento replicato esistente. come posso verificare che CMK venga applicato anche nell'area di destinazione?

    È possibile trovare il nome del disco gestito di replica creato da Azure Site Recovery nell'area di destinazione e alleghi DES a questo disco di replica. Tuttavia, non sarà possibile visualizzare i dettagli DES nel pannello dischi una volta collegato. In alternativa, è possibile scegliere di disabilitare la replica della macchina virtuale e riabilitarla. Si assicurerà di visualizzare i dettagli di DES e Key Vault nel pannello dischi per l'elemento replicato.

* È stato aggiunto un nuovo disco abilitato per CMK all'elemento replicato. Come è possibile replicare questo disco con Azure Site Recovery?

    L'aggiunta di un nuovo disco abilitato per CMK a un elemento replicato esistente non è supportata. Disabilitare la replica e abilitare di nuovo la replica per la macchina virtuale.

* Sono state abilitate sia la piattaforma che le chiavi gestite dal cliente, come è possibile proteggere i dischi?

    L'abilitazione della crittografia doppia con chiavi gestite dalla piattaforma e dal cliente è suppprted per Site Recovery. Per proteggere il computer, seguire le istruzioni riportate in questo articolo. Prima di tutto, è necessario creare un DES con crittografia doppia abilitata nell'area di destinazione. Al momento dell'abilitazione della replica per una macchina virtuale di questo tipo, è possibile fornire questa DES per Site Recovery.

