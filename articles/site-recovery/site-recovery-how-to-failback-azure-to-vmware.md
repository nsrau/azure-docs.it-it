---
title: Come eseguire il failback da Azure in Hyper-V | Documentazione Microsoft
description: "Dopo il failover delle macchine virtuali in Azure, è possibile avviare il failback per riportarle in locale. Informazioni sui passaggi per il failback."
services: site-recovery
documentationcenter: 
author: ruturaj
manager: gauravd
editor: 
ms.assetid: 44813a48-c680-4581-a92e-cecc57cc3b1e
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: 
ms.date: 02/13/2017
ms.author: ruturajd
translationtype: Human Translation
ms.sourcegitcommit: 424d8654a047a28ef6e32b73952cf98d28547f4f
ms.openlocfilehash: 28fd433676046ef474ac602c1c9e9829378bfddb
ms.lasthandoff: 03/22/2017


---
# <a name="fail-back-from-azure-to-an-on-premises-site"></a>Eseguire il failback da Azure a un sito locale
Questo articolo descrive come eseguire il failback da Macchine virtuali di Azure al sito locale. Seguire le istruzioni riportate in questo articolo per eseguire il failback delle macchine virtuali VMware o dei server fisici Windows/Linux dopo aver eseguito il failover dal sito locale in Azure usando l'esercitazione [Eseguire la replica di macchine virtuali VMware e server fisici in Azure con Azure Site Recovery](site-recovery-vmware-to-azure-classic.md).

## <a name="overview-of-failback"></a>Panoramica del failback
Ecco come funziona il failback. Dopo aver eseguito il failover in Azure, eseguire il failback nel sito locale procedendo come segue:

1. [Riproteggere](site-recovery-how-to-reprotect.md) le macchine virtuali in Azure in modo che inizino a replicare le macchine virtuali VMware nel sito locale. Come parte di questo processo, è necessario:
    1. Configurare una destinazione master locale: la destinazione master Windows per le macchine virtuali Windows e la [destinazione master Linux](site-recovery-how-to-install-linux-master-target.md) per le macchine virtuali Linux.
    2. Configurare un [server di elaborazione](site-recovery-vmware-setup-azure-ps-resource-manager.md).
    3. Avviare la [riprotezione](site-recovery-how-to-reprotect.md).
5. Dopo la replica delle macchine virtuali in Azure nel sito locale, avviare un failover da Azure al sito locale.

Dopo aver eseguito il failback dei dati, abilitare la riprotezione delle macchine virtuali locali in cui è stato eseguito il failback, in modo che inizino la replica in Azure.

Per una rapida panoramica, guardare il video seguente che illustra come eseguire il failover da Azure a un sito locale.
> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/VMware-to-Azure-with-ASR-Video5-Failback-from-Azure-to-On-premises/player]

### <a name="fail-back-to-the-original-or-alternate-location"></a>Eseguire il failback nella posizione originaria o in una posizione alternativa

Se è stato eseguito il failover di una macchina virtuale VMware è possibile eseguire il failback nella stessa macchina virtuale di origine locale, se è ancora presente. In questo scenario viene eseguita la replica delle sole modifiche. Questo scenario è noto come ripristino nel percorso originale. Se la macchina virtuale locale non esiste, lo scenario è un ripristino del percorso alternativo.

#### <a name="original-location-recovery"></a>Ripristino del percorso originale

Se si esegue il failback nella macchina virtuale originaria è necessario rispettare le condizioni seguenti:
* Se la macchina virtuale è gestita da un server vCenter, l'host ESX della destinazione master deve avere accesso all'archivio dati della macchina virtuale.
* Se la macchina virtuale si trova in un host ESX, ma non è gestita da vCenter, il relativo disco rigido deve trovarsi in un archivio dati accessibile da parte dell'host della destinazione master.
* Se la macchina virtuale si trova in un host ESX e non usa vCenter, è necessario completare l'individuazione dell'host ESX della destinazione master prima di abilitare la riprotezione. Questa opzione è valida anche per il failback di server fisici.
* È possibile eseguire il failback in una rete virtuale (vSAN) o in un disco basato su Raw Device Mapping (RDM), se i dischi esistono già e sono connessi alla macchina virtuale locale.

#### <a name="alternate-location-recovery"></a>Ripristino del percorso alternativo
Se la macchina virtuale locale non esiste prima della riprotezione della macchina virtuale, lo scenario è denominato ripristino del percorso alternativo. Il flusso di lavoro della riprotezione crea nuovamente la macchina virtuale locale. Verrà anche eseguito un download completo dei dati.

* Quando si esegue il failback in una posizione alternativa, la macchina virtuale verrà ripristinata nello stesso host ESX in cui è stato distribuito il server di destinazione master. L'archivio dati usato per creare il disco sarà lo stesso archivio selezionato durante la riprotezione della macchina virtuale.
* È possibile eseguire il failback solo in un archivio dati VMFS. Con vSAN o RDM, la riprotezione e il failback avranno esito negativo.
* La riprotezione comporta un trasferimento di dati iniziale di grandi dimensioni, a cui fanno seguito le modifiche. Questo processo avviene perché la macchina virtuale non esiste in locale. È necessario eseguire di nuovo la replica dei dati completi. La riprotezione impiegherà quindi più tempo rispetto al ripristino nel percorso originale.
* Non è possibile eseguire il failback in una rete vSAN o in dischi basati su RDM. In un archivio dati VMFS è possibile creare solo nuovi dischi VMDK.

In seguito al failover in Azure, è possibile eseguire il failback di una macchina fisica solo come macchina virtuale VMware, un'operazione chiamata anche P2A2V. Questo flusso di lavoro è considerato come ripristino in un percorso alternativo.

* Non è possibile eseguire il failback di un server Windows Server 2008 R2 SP1 protetto e sottoposto a failover in Azure.
* È necessario individuare almeno un server di destinazione master e gli host ESX/ESXi necessari per il failback.

## <a name="have-you-completed-reprotection"></a>Verificare se la riprotezione è stata completata.
Prima di proseguire, completare i passaggi di riprotezione in modo che le macchine virtuali siano nello stato replicato e sia possibile avviare un failover nel sito locale. Per altre informazioni, vedere [Come eseguire la riprotezione da Azure al sito locale](site-recovery-how-to-reprotect.md).

## <a name="prerequisites"></a>Prerequisiti

* Quando si esegue un failback, è necessario un server di configurazione locale. Durante il failback, la macchina virtuale deve esistere nel database del server di configurazione; in caso contrario il failback non ha esisto negativo. Assicurarsi quindi di pianificare backup regolari del server. In caso di emergenza, è necessario ripristinare il server con lo stesso indirizzo IP in modo che il failback funzioni.
* Il server di destinazione master non deve avere snapshot prima di attivare il failback.

## <a name="steps-to-fail-back"></a>Passaggi per eseguire il failback

> [!IMPORTANT]
Prima di avviare il failback, assicurarsi di aver completato la riprotezione delle macchine virtuali. Le macchine virtuali devono essere in stato protetto e l'integrità deve essere **OK**. Per riproteggere le macchine virtuali, leggere [come eseguire la riprotezione](site-recovery-how-to-reprotect.md).

1. Nella pagina degli elementi replicati selezionare la macchina virtuale e con il pulsante destro del mouse selezionare **Failover non pianificato**.
2. In **Conferma failover** verificare la direzione del failover (da Azure) e selezionare il punto di recupero (più recente o quello coerente con l'app più recente) da usare per il failover. Il punto coerente con l'app deve essere antecedente all'ultimo punto di temporizzazione e causerà la perdita di alcuni dati.
3. Durante il failover, Site Recovery arresta le macchine virtuali in Azure. Assicurarsi che il failback sia stato completato come previsto e quindi verificare che le macchine virtuali di Azure siano state arrestate.

### <a name="to-what-recovery-point-can-i-fail-back-the-virtual-machines"></a>Punto di recupero in cui è possibile eseguire il failback delle macchine virtuali

Durante il failback sono disponibili due opzioni per il failback di macchine virtuali/piano di ripristino.

Se si seleziona l'ultimo periodo di tempo elaborato, verrà eseguito il failover di tutte le macchine virtuali nell'ultimo periodo di tempo disponibile. Se è presente un gruppo di replica nel piano di ripristino, verrà eseguito il failover di ogni macchina virtuale del gruppo di replica nell'ultimo periodo di tempo indipendente.

Non è possibile eseguire il failback di una macchina virtuale finché non ha almeno un punto di recupero. Non è possibile eseguire il failback di un piano di ripristino finché tutte le macchine virtuali non hanno almeno un punto di recupero.

> [!NOTE]
> Il punto di recupero più recente è un punto di recupero coerente con l'arresto anomalo.

Se si seleziona il punto di recupero coerente con l'applicazione, un singolo failback della macchina virtuale ripristinerà l'ultimo punto di recupero coerente con l'applicazione più recente disponibile. In caso di piano di ripristino con un gruppo di replica, ogni gruppo di replica ripristinerà il punto di recupero disponibile comune.
Si noti che i punti di recupero coerenti con l'applicazione possono essere indietro nel tempo e potrebbero verificarsi perdite di dati.

## <a name="next-steps"></a>Passaggi successivi

Al termine del failback, è necessario eseguire il commit della macchina virtuale per garantire che vengano eliminate le macchine virtuali ripristinate in Azure.

### <a name="commit"></a>Commit
Il commit è necessario per rimuovere la macchina virtuale di cui è stato eseguito il failover da Azure.
Fare clic con il pulsante destro del mouse sull'elemento protetto e quindi su **Commit**. Un processo rimuoverà le macchine virtuali di cui è stato eseguito il failover in Azure.

### <a name="reprotect-from-on-premises-to-azure"></a>Abilitare la riprotezione dall'ambiente locale ad Azure

Al termine del commit la macchina virtuale si trova nuovamente nel sito locale, ma non è protetta. Per avviare nuovamente la replica in Azure, eseguire queste operazioni:

1. In **Insieme di credenziali** > **Impostazioni** > **Elementi replicati** selezionare le macchine virtuali di cui è stato eseguito il failback e quindi fare clic su **Riproteggi**.
2. Specificare il valore del server di elaborazione da usare per inviare i dati ad Azure.
3. Fare clic su **OK** per avviare il processo di riprotezione.

> [!NOTE]
> Dopo l'avvio di una macchina virtuale in locale, registrare di nuovo l'agente nel server di configurazione può richiedere tempo, fino a 15 minuti. Durante questo intervallo di tempo la riprotezione non riuscirà e sarà visualizzato un messaggio di errore con l'avviso che l'agente non è installato. Attendere alcuni minuti e ripetere l'operazione di riprotezione.

Al termine del processo di riprotezione, la macchina virtuale viene replicata in Azure e sarà possibile eseguire il failover.

## <a name="common-issues"></a>Problemi comuni
Assicurarsi che vCenter sia in uno stato connesso prima di eseguire un failback. In caso contrario, la disconnessione dei dischi e il ricollegamento alla macchina virtuale avrà esito negativo.

