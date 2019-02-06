---
title: Aggiornamenti di Azure Site Recovery | Microsoft Docs
description: Fornisce una panoramica degli aggiornamenti del servizio e della procedura per aggiornare i componenti usati in Azure Site Recovery.
services: site-recovery
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 01/8/2019
ms.author: rajanaki
ms.openlocfilehash: 3e5f84a6f05e451b1eafa98c373f9d838421016e
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2019
ms.locfileid: "55229297"
---
# <a name="service-updates-in-azure-site-recovery"></a>Aggiornamenti del servizio in Azure Site Recovery
Un'organizzazione deve sapere come si intende proteggere i dati e preservare l'esecuzione di applicazioni e carichi di lavoro quando si verificano interruzioni pianificate e impreviste. Azure Site Recovery contribuisce al buon esito della strategia di continuità aziendale e ripristino di emergenza (BCDR) mantenendo le app in esecuzione nelle macchine virtuali e nei server fisici disponibili in caso di arresto di un sito. Site Recovery replica i carichi di lavoro in esecuzione in macchine virtuali e server fisici in modo che rimangano disponibili in una posizione secondaria se il sito primario non è raggiungibile. Ripristina i carichi di lavoro nel sito primario quando è di nuovo attivo.

Site Recovery può gestire la replica per:

- [Replica di macchine virtuali di Azure tra aree di Azure](azure-to-azure-tutorial-dr-drill.md).
- Replica di macchine virtuali locali o server fisici in Azure o in un sito secondario.
Per altre informazioni, vedere la documentazione [qui](https://docs.microsoft.com/azure/site-recovery).

Azure Site Recovery pubblica a intervalli regolari gli aggiornamenti del servizio, tra cui l'aggiunta di nuove funzionalità, i miglioramenti nella matrice di supporto e le eventuali correzioni di bug. Per rimanere aggiornati e poter sfruttare tutte le funzionalità, i miglioramenti e le correzioni di bug più recenti, gli utenti devono sempre eseguire l'aggiornamento alle ultime versioni dei componenti di Azure Site Recovery. 
 
## <a name="support-statement-for-azure-site-recovery"></a>Dichiarazione relativa al supporto per Azure Site Recovery 

> [!IMPORTANT]
> **Con ogni nuova versione 'N' di un componente di Azure Site Recovery rilasciata, tutte le versioni precedenti a 'N-4' sono considerate non supportate.** È pertanto sempre consigliabile eseguire l'aggiornamento alle versioni più recenti disponibili.

> [!IMPORTANT]
> Il supporto ufficiale per gli aggiornamenti è da > N-4 alla versione N (dove N è la versione più recente). Se si usa N-6, è necessario eseguire prima l'aggiornamento a N-4 e quindi eseguire l'aggiornamento a N.

### <a name="upgrading-when-the-difference-between-current-version-and-latest-released-version-is-greater-than-4"></a>Esecuzione dell'aggiornamento quando la differenza tra la versione corrente e quella rilasciata più di recente è superiore a 4

1. Come primo passaggio, aggiornare il componente attualmente installato ad esempio dalla versione N a N+4 e quindi passare alla successiva versione compatibile. Si supponga che la versione corrente sia la 9.24 e che al momento si disponga della versione 9.16. Eseguire prima l'aggiornamento alla 9.20 e quindi alla 9.24.
2. Seguire lo stesso processo per tutti i componenti a seconda dello scenario.

### <a name="support-for-latest-oskernel-versions"></a>Supporto per le versioni del sistema operativo/kernel più recenti

> [!NOTE]
> Se è stata pianificata una finestra di manutenzione in cui è incluso un riavvio, è consigliabile prima aggiornare i componenti di Site Recovery e quindi procedere con il resto delle attività pianificate.

1. Prima di aggiornare le versioni del kernel/sistema operativo, verificare se la versione di destinazione è supportata da Azure Site Recovery. È possibile trovare informazioni nella documentazione relativa alle macchine virtuali di Azure, alle [macchine virtuali VMware](vmware-physical-azure-support-matrix.md) e alle macchine virtuali Hyper-V.
2. Fare riferimento agli [aggiornamenti del servizio](https://azure.microsoft.com/updates/?product=site-recovery) per determinare quale versione dei componenti di Site Recovery sia in grado di supportare la versione specifica a cui si vuole eseguire l'aggiornamento.
3. Eseguire in primo luogo l'aggiornamento alla versione più recente di Site Recovery.
4. A questo punto, aggiornare il sistema operativo/kernel alle versioni desiderate.
5. Eseguire un riavvio.
6. Questo garantirà che la versione del sistema operativo/kernel nelle macchine venga aggiornata alla versione più recente e che anche le ultime modifiche di Site Recovery necessarie per supportare la nuova versione vengano caricate nella macchina di origine.



## <a name="azure-vm-disaster-recovery-to-azure"></a>Ripristino di emergenza di VM di Azure in Azure
In questo scenario è consigliabile [abilitare](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-autoupdate) gli aggiornamenti automatici. È possibile scegliere di consentire a Site Recovery di gestire gli aggiornamenti nei modi seguenti:

- Come parte del passaggio di abilitazione della replica
- Attivare o disattivare le impostazioni di aggiornamento dell'estensione nell'insieme di credenziali

Se si è scelto di gestire manualmente gli aggiornamenti, seguire questa procedura:

1. Accedere al portale di Azure e passare a "Insieme di credenziali di Servizi di ripristino".
2. Passare al riquadro "Elementi replicati" nel portale di Azure per "Insieme di credenziali di Servizi di ripristino".
3. Fare clic sulla notifica seguente nella parte superiore della schermata:
    
    *New Site Recovery replication agent update is available* (È disponibile un nuovo aggiornamento dell'agente di replica di Site Recovery)
    
    *Click to install ->* (Fare clic per installare ->)

4. Selezionare le macchine virtuali a cui si vuole applicare l'aggiornamento e quindi fare clic su **OK**.

## <a name="between-two-on-premises-vmm-sites"></a>Tra due siti VMM locali
1. Scaricare l'aggiornamento cumulativo più recente per il provider di Microsoft Azure Site Recovery.
2. In primo luogo, installare l'aggiornamento cumulativo nel server VMM locale che gestisce il sito di ripristino.
3. Dopo aver aggiornato il sito di ripristino, installare l'aggiornamento cumulativo nel server VMM che gestisce il sito primario.

> [!NOTE]
> Se VMM è altamente disponibile VMM (VMM in cluster), assicurarsi di installare l'aggiornamento in tutti i nodi del cluster in cui è installato il servizio VMM.

## <a name="between-an-on-premises-vmm-site-and-azure"></a>Tra un sito VMM locale e Azure
1. Scaricare l'aggiornamento cumulativo per il provider di Microsoft Azure Site Recovery.
2. Installare l'aggiornamento cumulativo nel server VMM locale.
3. Installare l'agente MARS più recente dell'agente in tutti gli host Hyper-V.

> [!NOTE]
> Se VMM è altamente disponibile VMM (VMM in cluster), assicurarsi di installare l'aggiornamento in tutti i nodi del cluster in cui è installato il servizio VMM.

## <a name="between-an-on-premises-hyper-v-site-and-azure"></a>Tra un sito Hyper-V locale e Azure

1. Scaricare l'aggiornamento cumulativo per il provider di Microsoft Azure Site Recovery.
2. Installare il provider in ogni nodo dei server Hyper-V di cui è stata effettuata la registrazione in Azure Site Recovery.

> [!NOTE]
> Se Hyper-V è un server Hyper-V in cluster host, assicurarsi di installare l'aggiornamento in tutti i nodi del cluster.

## <a name="between-an-on-premises-vmware-or-physical-site-to-azure"></a>Tra un sito fisico o VMware locale e Azure

1. In primo luogo, installare l'aggiornamento nel server di gestione locale. Si tratta del server con i ruoli di server di configurazione e di server di elaborazione. 
2. Se si dispone di server di elaborazione con scalabilità orizzontale (scale-out), aggiornarli successivamente.
3. Passare al portale di Azure e quindi alla pagina **Elementi protetti** > **Elementi replicati**.
Selezionare una macchina virtuale in questa pagina. Selezionare il pulsante **Update Agent** (Aggiorna agente) che viene visualizzato nella parte inferiore della pagina per ogni macchina virtuale. In questo modo l'agente del servizio Mobility viene aggiornato in tutte le macchine virtuali protette.

È consigliabile eseguire un riavvio dopo ogni aggiornamento dell'agente di Mobility per essere certi che tutte le modifiche più recenti vengano caricate nella macchina di origine. È tuttavia **non obbligatorio**. Se la differenza tra la versione dell'agente durante l'ultimo riavvio e la versione corrente è superiore a 4, è obbligatorio eseguire un riavvio. Per una spiegazione dettagliata, fare riferimento alla tabella seguente.

|**Versione dell'agente durante l'ultimo riavvio** | **Aggiornamento a** | **Il riavvio è obbligatorio?**|
|---------|---------|---------|--------|
|9.16 |  9.18 | Non obbligatorio|
|9.16 | 9.19 | Non obbligatorio|
| 9.16 | 9.20 | Non obbligatorio
 | 9.16 | 9.21 | Sì. Eseguire prima l'aggiornamento alla versione 9.20 e quindi effettuare il riavvio prima di eseguire l'aggiornamento alla versione 9.21 perché la differenza tra le versioni (la versione 9.16 dove è stato eseguito l'ultimo riavvio e la versione di destinazione 9.21) è >4.



## <a name="links-to-currently-supported-update-rollups"></a>Collegamenti agli aggiornamenti cumulativi attualmente supportati


|Aggiornamento cumulativo  |Provider  |Installazione unificata| OVF  |MARS|
|---------|---------|---------|---------|--------|
|[Aggiornamento cumulativo 32](https://support.microsoft.com/help/4478871/update-rollup-31-for-azure-site-recovery)     |   5.1.3800.0  |  9.21.5091.1   |  5.1.3800.0  |2.0.9144.0
|[Aggiornamento cumulativo 31](https://support.microsoft.com/help/4478871/update-rollup-31-for-azure-site-recovery)     |     5.1.3700.0      |   9.20.5051.1      |     5.1.3700.0    |2.0.9144.0
|[Aggiornamento cumulativo 30](https://support.microsoft.com/help/4468181/azure-site-recovery-update-rollup-30)     |    5.1.3650.0   |   9.19.5007.1    |     5.1.3650.0    |2.0.9139.0
|[Aggiornamento cumulativo 29](https://support.microsoft.com/help/4466466/update-rollup-29-for-azure-site-recovery)     |   5.1.3650.0      |   9.19.4973.1     |     5.1.3700.0    |2.0.9131.0
|[Aggiornamento cumulativo 28](https://support.microsoft.com/help/4460079/update-rollup-28-for-azure-site-recovery)     |  5.1.3600 .0      |    9.19.4973.1     |  5.1.3600.0       |2.0.9131.0
| [Aggiornamento cumulativo 27](https://support.microsoft.com/help/4055712/update-rollup-27-for-azure-site-recovery)       |   5.1.3550.0      |    9.18.4946.1     | 5.1.3550.0         |2.0.9125.0


## <a name="previous-update-rollups"></a>Aggiornamenti cumulativi precedenti
- [Aggiornamento cumulativo 26](https://support.microsoft.com/help/4344054/update-rollup-26-for-azure-site-recovery)  
- [Aggiornamento cumulativo 25](https://support.microsoft.com/help/4278275/update-rollup-25-for-azure-site-recovery) 
- [Aggiornamento cumulativo 23](https://support.microsoft.com/help/4091311/update-rollup-23-for-azure-site-recovery) 
- [Aggiornamento cumulativo 22](https://support.microsoft.com/help/4072852/update-rollup-22-for-azure-site-recovery) 
- [Aggiornamento cumulativo 21](https://support.microsoft.com/help/4051380/update-rollup-21-for-azure-site-recovery) 
- [Aggiornamento cumulativo 20](https://support.microsoft.com/help/4041105/update-rollup-20-for-azure-site-recovery) 
- [Aggiornamento cumulativo 19](https://support.microsoft.com/help/4034599/update-rollup-19-for-azure-site-recovery) 
