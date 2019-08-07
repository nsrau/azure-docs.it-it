---
title: Aggiornamenti e aggiornamenti di componenti in Azure Site Recovery
description: Viene fornita una panoramica degli aggiornamenti del servizio Azure Site Recovery e degli aggiornamenti dei componenti.
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 07/31/2019
ms.author: rajanaki
ms.openlocfilehash: e06cd77a1d46208fe0f7aa166be3ccd3b9b7dbb4
ms.sourcegitcommit: 3073581d81253558f89ef560ffdf71db7e0b592b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/06/2019
ms.locfileid: "68828607"
---
# <a name="service-updates-in-site-recovery"></a>Aggiornamenti del servizio in Site Recovery

In questo articolo viene fornita una panoramica degli aggiornamenti di [Azure Site Recovery](site-recovery-overview.md) e viene descritto come aggiornare i componenti di Site Recovery.

Site Recovery pubblica gli aggiornamenti del servizio a intervalli regolari. Gli aggiornamenti includono nuove funzionalità, miglioramenti del supporto, aggiornamenti dei componenti e correzioni di bug. Per sfruttare le funzionalità e le correzioni più recenti, è consigliabile eseguire le versioni più recenti dei componenti Site Recovery. 
 
 
## <a name="updates-support"></a>Supporto aggiornamenti

### <a name="support-statement-for-azure-site-recovery"></a>Dichiarazione relativa al supporto per Azure Site Recovery

È consigliabile eseguire sempre l'aggiornamento alle versioni più recenti dei componenti:

**Con ogni nuova versione ' n'di un componente Azure Site Recovery rilasciata, tutte le versioni seguenti di ' n-4' sono considerate non supportate**. 

> [!IMPORTANT]
> Il supporto ufficiale è per l'aggiornamento da > versione N-4 a N versione. Se, ad esempio, si esegue l'aggiornamento a n-6, è necessario eseguire prima l'aggiornamento a N-4 e quindi eseguire l'aggiornamento a N.


### <a name="links-to-currently-supported-update-rollups"></a>Collegamenti agli aggiornamenti cumulativi attualmente supportati

 Esaminare l'aggiornamento cumulativo più recente (versione N) in [questo articolo](site-recovery-whats-new.md). Tenere presente che Site Recovery fornisce supporto per le versioni N-4.



## <a name="component-expiry"></a>Scadenza componente

Site Recovery notifica all'utente i componenti scaduti (o prossimi alla scadenza) tramite posta elettronica (se è stata effettuata la sottoscrizione alle notifiche tramite posta elettronica) o nel dashboard dell'insieme di credenziali nel portale.

- Inoltre, quando sono disponibili aggiornamenti, nella visualizzazione dell'infrastruttura per lo scenario nel portale viene visualizzato un pulsante **Aggiorna disponibile** accanto al componente. Questo pulsante reindirizza a un collegamento per scaricare la versione più recente del componente.
-  Le notifiche del dashboard degli insiemi di credenziali non sono disponibili se si esegue la replica di VM Hyper-V. 

Le notifiche tramite posta elettronica vengono inviate come indicato di seguito.

**Ora** | **Frequenza**
--- | ---
60 giorni prima della scadenza del componente | Una volta bi-settimanale
Prossimi 53 giorni | Una volta alla settimana
Ultimi 7 giorni | Una volta al giorno
Dopo la scadenza | Una volta bi-settimanale


### <a name="upgrading-outside-official-support"></a>Aggiornamento all'esterno del supporto ufficiale

Se la differenza tra la versione del componente e la versione di rilascio più recente è maggiore di quattro, questo aspetto è considerato non supportato. In questo caso, eseguire l'aggiornamento come segue: 

1. Aggiornare il componente attualmente installato alla versione corrente più quattro. Ad esempio, se la versione è 9,16, eseguire l'aggiornamento a 9,20.
2. Eseguire quindi l'aggiornamento alla versione compatibile successiva. Quindi, in questo esempio, dopo l'aggiornamento da 9,16 a 9,20, eseguire l'aggiornamento a 9,24. 

Seguire lo stesso processo per tutti i componenti pertinenti.

### <a name="support-for-latest-operating-systemskernels"></a>Supporto per i sistemi operativi e i kernel più recenti

> [!NOTE]
> Se si dispone di una finestra di manutenzione pianificata e viene incluso un riavvio, è consigliabile eseguire prima l'aggiornamento Site Recovery componenti, quindi procedere con le altre attività pianificate nella finestra di manutenzione.

1. Prima di aggiornare il sistema operativo o le versioni del kernel, verificare se la versione di destinazione è supportata Site Recovery. 

    - Supporto per le [macchine virtuali di Azure](azure-to-azure-support-matrix.md#replicated-machine-operating-systems) .
    - Supporto di [VMware/server fisici](vmware-physical-azure-support-matrix.md#replicated-machines)
    - Supporto [di Hyper-V](hyper-v-azure-support-matrix.md#replicated-vms) .
2. Esaminare [gli aggiornamenti disponibili](site-recovery-whats-new.md) per scoprire cosa si vuole aggiornare.
3. Eseguire l'aggiornamento alla versione Site Recovery più recente.
4. Aggiornare il sistema operativo/kernel alle versioni richieste.
5. Riavvio.


Questo processo garantisce che il sistema operativo o il kernel del computer venga aggiornato alla versione più recente e che le modifiche Site Recovery più recenti necessarie per supportare la nuova versione vengano caricate nel computer.

## <a name="azure-vm-disaster-recovery-to-azure"></a>Ripristino di emergenza di VM di Azure in Azure

In questo scenario si consiglia vivamente di [abilitare gli aggiornamenti automatici](azure-to-azure-autoupdate.md). È possibile consentire Site Recovery di gestire gli aggiornamenti come segue:

- Durante il processo di abilitazione della replica.
- Impostando le impostazioni di aggiornamento dell'estensione all'interno dell'insieme di credenziali.

Se si desidera gestire manualmente gli aggiornamenti, eseguire le operazioni seguenti:

1. Nell'insieme di credenziali > **elementi replicati**fare clic su questa notifica nella parte superiore della schermata: 
    
    **È disponibile un nuovo Site Recovery aggiornamento dell'agente di replica. Fare clic per installare->**

4. Selezionare le macchine virtuali per cui si desidera applicare l'aggiornamento, quindi fare clic su **OK**.


## <a name="vmware-vmphysical-server-disaster-recovery-to-azure"></a>Ripristino di emergenza di macchine virtuali VMware/server fisici in Azure

1. A seconda della versione corrente e dell' [istruzione di supporto](#support-statement-for-azure-site-recovery), installare prima l'aggiornamento nel server di configurazione locale usando [queste istruzioni](vmware-azure-deploy-configuration-server.md#upgrade-the-configuration-server). 
2. Se sono presenti server di elaborazione con scalabilità orizzontale, aggiornarli successivamente, seguendo [queste istruzioni](vmware-azure-manage-process-server.md#upgrade-a-process-server).
3. Per aggiornare l'agente di mobilità in ogni computer protetto, aprire elementi **protetti** > elementi**replicati**.
4. Selezionare la macchina virtuale e selezionare il pulsante **Aggiorna agente** visualizzato nella parte inferiore della pagina per ogni macchina virtuale. Questo Aggiorna l'agente del servizio Mobility in tutte le macchine virtuali protette.

### <a name="reboot-after-mobility-service-upgrade"></a>Riavvia dopo l'aggiornamento del servizio Mobility

È consigliabile riavviare il sistema dopo ogni aggiornamento del servizio Mobility, per assicurarsi che tutte le modifiche più recenti vengano caricate nel computer di origine.

Un riavvio non è obbligatorio, a meno che la differenza tra la versione dell'agente durante l'ultimo riavvio e la versione corrente sia maggiore di quattro.

Nell'esempio riportato nella tabella viene illustrato il funzionamento.

|**Versione agente (ultimo riavvio)** | **Eseguire l'aggiornamento a** | **Riavvio obbligatorio?**|
|---------|---------|---------|
|9.16 |  9.18 | Non obbligatorio|
|9.16 | 9.19 | Non obbligatorio|
| 9.16 | 9.20 | Non obbligatorio
 | 9.16 | 9.21 | Obbligatorio.<br/><br/> Eseguire l'aggiornamento a 9,20, quindi riavviare il computer prima di eseguire l'aggiornamento a 9,21.

## <a name="hyper-v-vm-disaster-recovery-to-azure"></a>Ripristino di emergenza di VM Hyper-V in Azure

### <a name="between-a-hyper-v-site-and-azure"></a>Tra un sito Hyper-V e Azure

1. Scaricare l'aggiornamento per il provider di Site Recovery Microsoft Azure.
2. Installare il provider in ogni server Hyper-V registrato in Site Recovery. Se si sta eseguendo un cluster, eseguire l'aggiornamento su tutti i nodi del cluster.


## <a name="between-an-on-premises-vmm-site-and-azure"></a>Tra un sito VMM locale e Azure
1. Scaricare l'aggiornamento per il provider di Site Recovery Microsoft Azure.
2. Installare il provider nel server VMM. Se VMM viene distribuito in un cluster, installare il provider in tutti i nodi del cluster.
3. Installare la versione più recente dell'agente di Servizi di ripristino di Microsoft Azure in tutti gli host Hyper-V o i nodi del cluster.


## <a name="between-two-on-premises-vmm-sites"></a>Tra due siti VMM locali
1. Scaricare l'aggiornamento più recente per il provider di Site Recovery Microsoft Azure.
2. Installare il provider più recente nel server VMM che gestisce il sito di ripristino secondario. Se VMM viene distribuito in un cluster, installare il provider in tutti i nodi del cluster.
3. Al termine dell'aggiornamento del sito di ripristino, installare il provider nel server VMM che gestisce il sito primario.

## <a name="next-steps"></a>Passaggi successivi

Segui la pagina degli [aggiornamenti di Azure](https://azure.microsoft.com/updates/?product=site-recovery) per tenere traccia di nuovi aggiornamenti e versioni.
