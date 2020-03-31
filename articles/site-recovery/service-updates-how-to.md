---
title: Aggiornamenti e aggiornamenti dei componenti in Azure Site Recovery
description: Fornisce una panoramica degli aggiornamenti del servizio Azure Site Recovery e degli aggiornamenti dei componenti.
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 07/31/2019
ms.author: rajanaki
ms.openlocfilehash: 3489f7f812798504d0c57a265a04e57344105419
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79257823"
---
# <a name="service-updates-in-site-recovery"></a>Aggiornamenti del servizio in Site Recovery

Questo articolo offre una panoramica degli aggiornamenti di Azure Site Recovery e descrive come aggiornare i componenti di Site Recovery.This article provides an overview of [Azure Site Recovery](site-recovery-overview.md) updates, and describes how to upgrade Site Recovery components.

Site Recovery pubblica regolarmente gli aggiornamenti del servizio. Gli aggiornamenti includono nuove funzionalità, miglioramenti del supporto, aggiornamenti dei componenti e correzioni di bug. Per sfruttare le funzionalità e le correzioni più recenti, è consigliabile eseguire le versioni più recenti dei componenti di Site Recovery. 
 
 
## <a name="updates-support"></a>Supporto per gli aggiornamenti

### <a name="support-statement-for-azure-site-recovery"></a>Dichiarazione relativa al supporto per Azure Site Recovery

Si consiglia di eseguire sempre l'aggiornamento alle versioni più recenti dei componenti:

**Con ogni nuova versione 'N' di un componente di Azure Site Recovery rilasciato, tutte le versioni sotto 'N-4' sono considerate non supportate.** 

> [!IMPORTANT]
> Il supporto ufficiale è per l'aggiornamento dalla versione N-4 > alla versione N. Ad esempio, se si esegue L'opzione N-6, è necessario prima eseguire l'aggiornamento a N-4 e quindi eseguire l'aggiornamento a N.


### <a name="links-to-currently-supported-update-rollups"></a>Collegamenti agli aggiornamenti cumulativi attualmente supportati

 Leggere l'aggiornamento cumulativo più recente (versione N) in [questo articolo](site-recovery-whats-new.md). Tenere presente che Site Recovery fornisce il supporto per le versioni N-4.



## <a name="component-expiry"></a>Scadenza dei componenti

Site Recovery invia una notifica ai componenti scaduti (o prossimi alla scadenza) tramite posta elettronica (se è stata effettuata la sottoscrizione alle notifiche tramite posta elettronica) o nel dashboard dell'insieme di credenziali nel portale.

- Inoltre, quando sono disponibili aggiornamenti, nella visualizzazione dell'infrastruttura per lo scenario nel portale, viene visualizzato un pulsante **Aggiorna disponibile** accanto al componente. Questo pulsante consente di reindirizzare l'utente a un collegamento per il download della versione più recente del componente.
-  Le notifiche del dashboard di Vault non sono disponibili se si replicano le macchine virtuali Hyper-V.Vaults dashboard notifications aren't available if you're replicating Hyper-VMs. 

Le notifiche e-mail vengono inviate come segue.

**Tempo** | **Frequenza**
--- | ---
60 giorni prima della scadenza del componente | Una volta bisettimanale
Prossimi 53 giorni | Una volta alla settimana
Ultimi 7 giorni | Una volta al giorno
Dopo la scadenza | Una volta bisettimanale


### <a name="upgrading-outside-official-support"></a>Aggiornamento del supporto ufficiale esterno

Se la differenza tra la versione del componente e la versione più recente è maggiore di quattro, questa operazione viene considerata non supportata. In questo caso, eseguire l'aggiornamento come segue:In this case, upgrade as follows: 

1. Aggiornare il componente attualmente installato alla versione corrente più quattro. Ad esempio, se la versione è 9.16, eseguire l'aggiornamento a 9.20.For example, if your version if 9.16, then upgrade to 9.20.
2. Quindi, eseguire l'aggiornamento alla versione compatibile successiva. Quindi, nel nostro esempio, dopo l'aggiornamento 9.16 a 9.20, eseguire l'aggiornamento a 9.24. 

Seguire la stessa procedura per tutti i componenti pertinenti.

### <a name="support-for-latest-operating-systemskernels"></a>Supporto per sistemi operativi/kernel più recenti

> [!NOTE]
> Se si dispone di una finestra di manutenzione pianificata e vi è incluso un riavvio, è consigliabile aggiornare prima i componenti di Site Recovery e quindi procedere con le altre attività pianificate nella finestra di manutenzione.

1. Prima di aggiornare le versioni del sistema operativo/kernel, verificare se la versione di destinazione è supportata per Site Recovery. 

    - Supporto [delle macchine virtuali di Azure.Azure VM](azure-to-azure-support-matrix.md#replicated-machine-operating-systems) support.
    - Supporto [per VMware/server fisico](vmware-physical-azure-support-matrix.md#replicated-machines)
    - Supporto [Hyper-V.](hyper-v-azure-support-matrix.md#replicated-vms)
2. Esaminare [gli aggiornamenti disponibili](site-recovery-whats-new.md) per scoprire cosa si desidera aggiornare.
3. Eseguire l'aggiornamento alla versione più recente di Site Recovery.
4. Aggiornare il sistema operativo/kernel alle versioni richieste.
5. Riavviare il computer.


Questo processo garantisce che il sistema operativo/il kernel del computer venga aggiornato alla versione più recente e che le modifiche più recenti di Site Recovery necessarie per supportare la nuova versione vengano caricate nel computer.

## <a name="azure-vm-disaster-recovery-to-azure"></a>Ripristino di emergenza di VM di Azure in Azure

In questo scenario, è [consigliabile abilitare gli aggiornamenti automatici](azure-to-azure-autoupdate.md). È possibile consentire a Site Recovery di gestire gli aggiornamenti come segue:

- Durante il processo di abilitazione della replica.
- Impostando le impostazioni di aggiornamento dell'estensione all'interno del vault.

Se si desidera gestire manualmente gli aggiornamenti, eseguire le operazioni seguenti:

1. Nell'insieme di credenziali > **elementi replicati**, fare clic su questa notifica nella parte superiore dello schermo: 
    
    **È disponibile un nuovo aggiornamento dell'agente di replica di Site Recovery. Fare clic per installare ->**

4. Selezionare le macchine virtuali per cui si desidera applicare l'aggiornamento e quindi fare clic su **OK**.


## <a name="vmware-vmphysical-server-disaster-recovery-to-azure"></a>VMware VM/physical server disaster recovery to Azure

1. In base alla versione corrente e [all'istruzione](#support-statement-for-azure-site-recovery)di supporto , installare prima l'aggiornamento nel server di configurazione locale, seguendo [queste istruzioni.](vmware-azure-deploy-configuration-server.md#upgrade-the-configuration-server) 
2. Se si dispone di server di elaborazione con scalabilità orizzontale, aggiornarli successivamente, seguendo [queste istruzioni](vmware-azure-manage-process-server.md#upgrade-a-process-server).
3. Per aggiornare l'agente Mobility in ogni computer protetto, fare riferimento a [questo](vmware-physical-manage-mobility-service.md#update-mobility-service-from-azure-portal) articolo.

### <a name="reboot-after-mobility-service-upgrade"></a>Riavviare dopo l'aggiornamento del servizio Mobility

Dopo ogni aggiornamento del servizio Mobility è consigliabile eseguire un riavvio, per assicurarsi che tutte le modifiche più recenti vengano caricate nel computer di origine.

Un riavvio non è obbligatorio, a meno che la differenza tra la versione dell'agente durante l'ultimo riavvio e la versione corrente sia maggiore di quattro.

L'esempio nella tabella mostra come funziona.

|**Versione agente (ultimo riavvio)** | **Eseguire l'aggiornamento a** | **Riavvio obbligatorio?**|
|---------|---------|---------|
|9.16 |  9.18 | Non obbligatorio|
|9.16 | 9.19 | Non obbligatorio|
| 9.16 | 9.20 | Non obbligatorio
 | 9.16 | 9.21 | Mandatory.<br/><br/> Eseguire l'aggiornamento alle 9.20, quindi riavviare prima dell'aggiornamento alla 9.21.

## <a name="hyper-v-vm-disaster-recovery-to-azure"></a>Ripristino di emergenza di VM Hyper-V in Azure

### <a name="between-a-hyper-v-site-and-azure"></a>Tra un sito Hyper-V e Azure

1. Scaricare l'aggiornamento per il provider di database del sito di Microsoft Azure.
2. Installare il provider in ogni server Hyper-V registrato in Site Recovery. Se si esegue un cluster, eseguire l'aggiornamento su tutti i nodi del cluster.


## <a name="between-an-on-premises-vmm-site-and-azure"></a>Tra un sito VMM locale e Azure
1. Scaricare l'aggiornamento per il provider di database del sito di Microsoft Azure.
2. Installare il provider nel server VMM. Se VMM è distribuito in un cluster, installare il provider in tutti i nodi del cluster.
3. Installare l'agente Servizi di ripristino di Microsoft Azure più recente in tutti gli host Hyper-V o nei nodi del cluster.


## <a name="between-two-on-premises-vmm-sites"></a>Tra due siti VMM locali
1. Scaricare l'aggiornamento più recente per il provider di siti di Microsoft Azure.Download the latest update for the Microsoft Azure Site Recovery Provider.
2. Installare il provider più recente nel server VMM che gestisce il sito di ripristino secondario. Se VMM è distribuito in un cluster, installare il provider in tutti i nodi del cluster.
3. Dopo aver aggiornato il sito di ripristino, installare il provider nel server VMM che gestisce il sito primario.

## <a name="next-steps"></a>Passaggi successivi

Seguire la pagina Aggiornamenti di [Azure](https://azure.microsoft.com/updates/?product=site-recovery) per tenere traccia dei nuovi aggiornamenti e versioni.
