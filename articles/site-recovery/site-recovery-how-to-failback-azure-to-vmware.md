---
title: Come eseguire il failback da Azure a Hyper-V | Microsoft Docs
description: "Dopo il failover delle macchine virtuali in Azure, è possibile avviare il failback per riportarle in locale. Informazioni sui passaggi del failback."
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
ms.sourcegitcommit: bedb59414ed328fe2fcca2b09f3360a98c98ff60
ms.openlocfilehash: b4bdd27db343bfb4a0e47c432f726ff454f7f55a
ms.lasthandoff: 02/23/2017


---
# <a name="failback-from-azure-to-on-premises"></a>Failback da Azure a locale
Questo articolo descrive come eseguire il failback di macchine virtuali di Azure da Azure al sito locale. Seguire le istruzioni riportate in questo articolo per eseguire il failback delle macchine virtuali VMware o dei server fisici Windows/Linux dopo aver eseguito il failover dal sito locale in Azure seguendo questa [esercitazione](site-recovery-vmware-to-azure-classic.md).

## <a name="overview-of-failback"></a>Panoramica del failback
Ecco come funziona il failback. Dopo aver eseguito il failover in Azure, eseguire il failback nel sito locale procedendo come segue:

1. [Abilitare la riprotezione](site-recovery-how-to-reprotect.md) delle VM di Azure in modo che inizino la replica nelle VM di VMware in esecuzione nel sito locale. Per farlo è anche necessario 
    1. Configurare una destinazione master locale: una destinazione master Windows per le VM Windows e una [destinazione master Linux](site-recovery-how-to-install-linux-master-target.md) per le VM Linux
    2. Configurare un [server di elaborazione](site-recovery-vmware-setup-azure-ps-resource-manager.md)
    3. Avviare la [riprotezione](site-recovery-how-to-reprotect.md)
5. Dopo la replica delle VM di Azure nel sito locale, avviare un failover da Azure a locale.
  
Dopo aver eseguito il failback dei dati, abilitare la riprotezione delle macchine virtuali locali in cui è stato eseguito il failback, in modo che inizino la replica in Azure.

Per una rapida panoramica video, guardare il video seguente.
> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/VMware-to-Azure-with-ASR-Video5-Failback-from-Azure-to-On-premises/player]

### <a name="failback-to-the-original-or-alternate-location"></a>Eseguire il failback nella posizione originaria o in una posizione alternativa
    
Se è stato eseguito il failover di una macchina virtuale VMware è possibile eseguire il failback nella stessa macchina virtuale di origine, se è ancora presente in locale. In questo scenario viene eseguita la replica delle sole modifiche differenziali. Questo scenario è noto come ripristino nel percorso originale. Se la VM locale non esiste, è un ripristino del percorso alternativo.

#### <a name="original-location-recovery"></a>Ripristino del percorso originale

Se si esegue il failback nella macchina virtuale originaria è necessario rispettare le condizioni seguenti:
* Se la macchina virtuale è gestita da un server vCenter, l'host ESX della destinazione master deve avere accesso all'archivio dati delle macchine virtuali.
* Se la macchina virtuale si trova in un host ESX ma non è gestita da vCenter, il disco rigido della macchina virtuale deve trovarsi in un archivio dati accessibile da parte dell'host della destinazione master.
* Se la macchina virtuale si trova in un host ESX e non usa vCenter, è necessario completare l'individuazione dell'host ESX della destinazione master prima di abilitare la riprotezione. Questa opzione è valida anche per il failback di server fisici.
* È possibile eseguire il failback in una rete vSAN o in dischi basati su RDM se i dischi esistono già e sono connessi a una VM locale.

#### <a name="alternate-location-recovery"></a>Ripristino del percorso alternativo
Se la macchina virtuale locale non esiste prima della riprotezione, lo scenario è definito ripristino nel percorso alternativo. In questo caso, il flusso di lavoro della riprotezione crea nuovamente la macchina virtuale locale. Verrà anche eseguito un download completo dei dati.

* Quando si esegue il failback in una posizione alternativa la macchina virtuale viene ripristinata nello stesso host ESX in cui è distribuito il server di destinazione master. L'archivio dati usato per creare il disco sarà lo stesso archivio selezionato durante la riprotezione della macchina virtuale.
* È possibile eseguire il failback solo in un archivio dati VMFS. Se si dispone di vSAN o RDM, la riprotezione e il failback avranno esito negativo.
* La riprotezione comporta un trasferimento di dati iniziale di grandi dimensioni, a cui fanno seguito le modifiche delta. Questo perché la macchina virtuale non esiste in locale e perché deve essere eseguita nuovamente la replica dei dati completi. La riprotezione, inoltre, impiegherà più tempo rispetto al ripristino nel percorso originale.
* Non è possibile il failback in una rete vSAN o in dischi basati su RDM. In un archivio dati VMFS si possono creare solo nuovi dischi VMDK.

In seguito al failover in Azure, è possibile eseguire il failback di una macchina fisica solo come macchina virtuale VMware, un'operazione chiamata anche P2A2V. Questo flusso di lavoro è considerato come ripristino in un percorso alternativo.

* Non è possibile eseguire il failback di un computer Windows Server 2008 R2 SP1 protetto e sottoposto a failover in Azure.
* È necessario individuare almeno un server di destinazione master e gli host ESX/ESXi necessari per il failback.

## <a name="have-you-completed-reprotection"></a>Verificare se la riprotezione è stata completata
Prima di proseguire, completare i passaggi di riprotezione in modo che le macchine virtuali siano nello stato replicato e sia possibile avviare un failover in locale.
[Come eseguire la riprotezione da Azure al sito locale](site-recovery-how-to-reprotect.md).

## <a name="pre-requisites"></a>Prerequisiti
 
* Quando si esegue un failback, è necessario un server di configurazione locale. Durante il failback, la macchina virtuale deve esistere nel database del server di configurazione, altrimenti il failback non avrà esito positivo. Assicurarsi pertanto di pianificare backup regolari del server. In caso di emergenza, è necessario ripristinare il server con lo stesso indirizzo IP in modo che il failback funzioni.
* Il server di destinazione master non deve avere snapshot prima di attivare il failback.

## <a name="steps-to-failback"></a>Passaggi per il failback

Prima di avviare il failback, **assicurarsi di aver completato la riprotezione delle macchine virtuali**. Le macchine virtuali devono essere in stato protetto e integre. Per riproteggere le macchine virtuali, leggere [queste ulteriori informazioni](site-recovery-how-to-reprotect.md).

1. Nella pagina degli elementi replicati selezionare la macchina virtuale e con il pulsante destro del mouse selezionare **Failover non pianificato**.
2. In **Conferma failover** verificare la direzione del failover (da Azure) e selezionare il punto di ripristino da usare per il failover. Usare il più recente o quello coerente con l'app più recente. Il punto coerente con l'app deve essere antecedente all'ultimo punto di temporizzazione e causerà la perdita di alcuni dati.
3. Durante il failover, Site Recovery arresta le macchine virtuali di Azure. Assicurarsi che il failback sia stato completato come previsto e quindi verificare che le macchine virtuali di Azure siano state arrestate.

### <a name="to-what-recovery-point-can-i-failback-the-vms-to"></a>Punto di ripristino in cui è possibile eseguire il failback delle VM

Durante il failback, sono disponibili due opzioni per il failback delle VM/piano di ripristino.

Se si seleziona l'ultimo periodo di tempo elaborato, verrà eseguito il failover di tutte le macchine virtuali nell'ultimo periodo di tempo disponibile. Se è presente un gruppo di replica nel piano di ripristino, verrà eseguito il failover di ogni VM del gruppo di replica nell'ultimo periodo di tempo indipendente.

Non è possibile eseguire il failback di una VM finché non ha almeno un punto di ripristino. Non è possibile eseguire il failback di un piano di ripristino finché tutte le relative VM non hanno almeno un punto di ripristino.

> [!NOTE]
> Il punto di ripristino più recente è un punto di ripristino coerente con l'arresto anomalo.

Se si seleziona il punto di ripristino coerente con l'applicazione, un singolo failback di VM ripristinerà l'ultimo punto di ripristino coerente con l'applicazione disponibile. In caso di piano di ripristino con un gruppo di replica, ogni gruppo di replica ripristinerà il punto di ripristino disponibile comune.
Si noti che i punti di ripristino coerenti con l'applicazione possono essere indietro nel tempo e potrebbero verificarsi perdite di dati.

## <a name="next-steps"></a>Passaggi successivi

Al termine del failback, è necessario eseguire il commit della macchina virtuale per garantire che vengano eliminate le macchine virtuali ripristinate in Azure.

### <a name="commit"></a>Commit
Il commit è necessario per rimuovere la macchina virtuale di cui è stato eseguito il failover da Azure.
Fare clic con il pulsante destro del mouse sull'elemento protetto e quindi su Commit. Verrà attivato un processo che rimuoverà da Azure le macchine virtuali di cui è stato eseguito il failover in precedenza.

### <a name="reprotect-from-on-premises-to-azure"></a>Abilitare la riprotezione dall'ambiente locale ad Azure

Al termine del commit la macchina virtuale si trova nuovamente nel sito locale, ma non è protetta. Per avviare nuovamente la replica in Azure, eseguire queste operazioni:

1. In Insieme di credenziali > Impostazioni > Elementi replicati selezionare le VM di cui è stato eseguito il failback e fare clic su **Riproteggi**.
2. Specificare il valore del server di elaborazione da usare per inviare i dati ad Azure.
3. Fare clic su OK per avviare il processo di riprotezione.

> [!NOTE]
> Dopo l'avvio di una VM in locale, registrare di nuovo l'agente nel server di configurazione può richiedere tempo (fino a 15 minuti). Durante questo intervallo di tempo, la riprotezione avrà esito negativo e verrà visualizzato un messaggio di errore che informa che l'agente non è installato. Attendere alcuni minuti e ripetere l'operazione di riprotezione.

Al termine del processo di riprotezione, la macchina virtuale viene replicata in Azure e sarà possibile eseguire il failover.

## <a name="common-issues"></a>Problemi comuni
* Verificare che vCenter sia connesso prima di eseguire un failback, altrimenti le operazioni di disconnessione dei dischi e di ricollegamento alla VM avranno esito negativo.


