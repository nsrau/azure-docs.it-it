---
title: Eseguire la replica di applicazioni (da Azure ad Azure) | Microsoft Docs
description: Questo articolo descrive come configurare la replica di macchine virtuali in esecuzione in un'area di Azure verso un'altra area di Azure.
services: site-recovery
documentationcenter: 
author: asgang
manager: rochakm
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 5/22/2017
ms.author: asgang
ms.openlocfilehash: f9f97cf840b722c8cfee169dd1640e0682f287ff
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2017
---
# <a name="replicate-azure-virtual-machines-to-another-azure-region"></a>Eseguire la replica di macchine virtuali di Azure in un'altra area di Azure



>[!NOTE]
>
> La replica di Site Recovery per le macchine virtuali di Azure è attualmente in anteprima.

Questo articolo descrive come configurare la replica di macchine virtuali in esecuzione in un'area di Azure verso un'altra area di Azure.

## <a name="prerequisites"></a>Prerequisiti

* L'articolo presuppone una conoscenza di Site Recovery e dell'insieme di credenziali dei servizi di ripristino. È necessario che sia già stato creato un "insieme di credenziali dei servizi di ripristino".

    >[!NOTE]
    >
    > È consigliabile creare un "insieme di credenziali dei servizi di ripristino" nella posizione in cui si vuole che vengano replicate le VM. Ad esempio, se la posizione di destinazione è "Stati Uniti centrali", creare l'insieme di credenziali in "Stati Uniti centrali".

* Se si usano le regole dei gruppi di sicurezza di rete o un proxy firewall per controllare l'accesso alla connettività Internet in uscita nelle VM di Azure, assicurarsi di aggiungere all'elenco elementi consentiti gli URL o gli IP necessari. Per altri dettagli, vedere il [documento di indicazioni sulla rete](./site-recovery-azure-to-azure-networking-guidance.md).

* Se è presente una connessione ExpressRoute o VPN tra posizioni locali e di origine in Azure, vedere il documento [Site Recovery Considerations for Azure to on-premises ExpressRoute / VPN configuration](site-recovery-azure-to-azure-networking-guidance.md#guidelines-for-existing-azure-to-on-premises-expressroutevpn-configuration) (Considerazioni su Site Recovery per la configurazione da Azure a ExpressRoute/VPN in locale).

* L'account utente di Azure deve avere determinate [autorizzazioni](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines) per abilitare la replica di una macchina virtuale di Azure.

* È necessario che la sottoscrizione di Azure sia abilitata per la creazione di VM nella posizione di destinazione da usare come area per il ripristino di emergenza. È possibile contattare il supporto tecnico per abilitare la quota necessaria.

## <a name="enable-replication-from-azure-site-recovery-vault"></a>Abilitare la replica dall'insieme di credenziali di Azure Site Recovery
Per illustrare questo concetto, verrà eseguita la replica di VM in esecuzione nell'area "Asia orientale" di Azure verso l'area "Asia sud-orientale". Attenersi alla procedura seguente:

 Fare clic su **+Replica** nell'insieme di credenziali per abilitare la replica per le macchine virtuali.

1. **Origine:** indica il punto di origine delle macchine virtuali, in questo caso **Azure**.

2. **Percorso di origine:** indica l'area di Azure da cui si vogliono proteggere le macchine virtuali. Per questo esempio, il percorso di origine sarà "Asia orientale".

3. **Modello di distribuzione:** indica il modello di distribuzione di Azure per le macchine virtuali di origine. È possibile selezionare il modello di distribuzione classica o Resource Manager e le macchine virtuali appartenenti al modello specifico verranno elencate per la protezione nel passaggio successivo.

      >[!NOTE]
      >
      > È solo possibile eseguire la replica di una macchina virtuale classica e ripristinarla come macchina virtuale classica. Non è possibile ripristinarla come macchina virtuale Resource Manager.

4. **Gruppo di risorse:** indica il gruppo di risorse a cui appartengono le macchine virtuali di origine. Tutte le VM nel gruppo di risorse selezionato verranno elencate per la protezione nel passaggio successivo.

    ![Abilitare la replica](./media/site-recovery-replicate-azure-to-azure/enabledrwizard1.png)

In **Macchine virtuali > Seleziona macchine virtuali** fare clic per selezionare tutte le macchine virtuali da replicare. È possibile selezionare solo i computer per cui è possibile abilitare la replica. Fare quindi clic su OK.
    ![Abilitare la replica](./media/site-recovery-replicate-azure-to-azure/virtualmachine_selection.png)


Nella sezione Impostazioni è possibile configurare le proprietà del sito di destinazione.

1. **Percorso di destinazione:** indica la posizione in cui verrà eseguita la replica dei dati delle macchine virtuali di origine. In base al percorso selezionato per le macchine virtuali, Site Recovery fornirà l'elenco di aree di destinazione idonee.

    > [!TIP]
    > È consigliabile usare lo stesso percorso di destinazione dell'insieme di credenziali dei servizi di ripristino.

2. **Gruppo di risorse di destinazione:** indica il gruppo di risorse a cui apparterranno tutte le macchine virtuali replicate. Per impostazione predefinita, Azure Site Recovery creerà un nuovo gruppo di risorse nell'area di destinazione, con un nome con suffisso "asr". Nel caso in cui il gruppo di risorse creato da Azure Site Recovery esista già, verrà riutilizzato. È anche possibile scegliere di personalizzarlo, come illustrato nella sezione seguente.    
3. **Target Virtual Network (Rete virtuale di destinazione):** per impostazione predefinita, Azure Site Recovery creerà una nuova rete virtuale nell'area di destinazione con un nome con suffisso "asr". Questa rete verrà mappata alla rete di origine e verrà usata per la protezione futura.

    > [!NOTE]
    > [Controllare i dettagli sulla rete](site-recovery-network-mapping-azure-to-azure.md) per ottenere altre informazioni sul mapping di rete.

4. **Target Storage accounts (Account di archiviazione di destinazione):** per impostazione predefinita, Azure Site Recovery creerà un nuovo account di archiviazione di destinazione, che rispecchia la configurazione delle risorse di archiviazione della VM di origine. Nel caso in cui l'account di archiviazione creato da Azure Site Recovery esista già, verrà riutilizzato.

5. **Cache Storage accounts (Account di archiviazione della cache):** Azure Site Recovery necessita di un account di archiviazione aggiuntivo, definito account di archiviazione della cache, nell'area di origine. Tutte le modifiche apportate nelle VM di origine vengono registrate e inviate all'account di archiviazione della cache prima della replica nella posizione di destinazione.

6. **Set di disponibilità:** per impostazione predefinita, Azure Site Recovery creerà un nuovo set di disponibilità nell'area di destinazione, con un nome con suffisso "asr". Nel caso in cui il set di disponibilità creato da Azure Site Recovery esista già, verrà riutilizzato.

7.  **Criteri di replica:** definisce le impostazioni per la cronologia della conservazione del punto di ripristino e per la frequenza snapshot coerenti con l'app. Per impostazione predefinita, Azure Site Recovery creerà un nuovo criterio di replica con impostazioni predefiniti di "24 ore" per la conservazione del punto di ripristino e di "60 minuti" per la frequenza snapshot coerenti con l'app.

    ![Abilitare la replica](./media/site-recovery-replicate-azure-to-azure/enabledrwizard3.PNG)

## <a name="customize-target-resources"></a>Personalizzare le risorse di destinazione

Se si vogliono modificare i valori predefiniti usati da Azure Site Recovery, è possibile modificare le impostazioni in base alle esigenze specifiche.

1. **Personalizza:** fare clic sul pulsante per modificare le impostazioni predefinite usate da Azure Site Recovery.

2. **Gruppo di risorse di destinazione:** è possibile selezionare il gruppo di risorse dall'elenco di tutti i gruppi di risorse esistenti nel percorso di destinazione all'interno della sottoscrizione.

3. **Target Virtual Network (Rete virtuale di destinazione):** è possibile trovare l'elenco di tutte le reti virtuali nel percorso di destinazione.

4. **Set di disponibilità:** è possibile aggiungere impostazioni dei set di disponibilità solo alle macchine virtuali che appartengono a un set di disponibilità nell'area di origine.

5. **Target Storage accounts (Account di archiviazione di destinazione):**

![Abilitare la replica](./media/site-recovery-replicate-azure-to-azure/customize.PNG) Fare clic su **Create target resources** (Crea risorse di destinazione) e quindi su Abilita replica.


Dopo avere completato la protezione delle macchine virtuali, è possibile controllare lo stato dell'integrità delle VM in **Elementi replicati**.

>[!NOTE]
>Durante la replica iniziale è possibile che sia necessario attendere qualche minuto per l'aggiornamento dello stato e per la visualizzazione dell'avanzamento. È possibile fare clic sul pulsante Aggiorna nella parte superiore del pannello per visualizzare lo stato più aggiornato.
>

![Abilitare la replica](./media/site-recovery-replicate-azure-to-azure/replicateditems.PNG)


## <a name="next-steps"></a>Passaggi successivi
- [Altre informazioni](site-recovery-test-failover-to-azure.md) sull'esecuzione di un failover di test.
- [Altre informazioni](site-recovery-failover.md) sui diversi tipi di failover e su come eseguirli.
- Altre informazioni sull'[uso dei piani di ripristino](site-recovery-create-recovery-plans.md) per la riduzione di RTO.
- Altre informazioni sulla [riprotezione delle VM di Azure](site-recovery-how-to-reprotect.md) dopo il failover.
