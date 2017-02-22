---
title: 'Procedura: eseguire il failback da Azure al sito locale | Microsoft Docs'
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
ms.date: 12/15/2016
ms.author: ruturajd
translationtype: Human Translation
ms.sourcegitcommit: 1a6dfb60f5db8b842af27b4c88bc3e5927331622
ms.openlocfilehash: 20446aadbb7bc3d13c5a38de5c4040f5898d4f6b


---
# <a name="failback-from-azure-to-on-premises"></a>Failback da Azure al sito locale
Questo articolo descrive come eseguire il failback di macchine virtuali di Azure da Azure al sito locale. Seguire le istruzioni riportate in questo articolo per eseguire il failback delle macchine virtuali VMware o dei server fisici Windows/Linux dopo aver eseguito il failover dal sito locale in Azure seguendo questa [esercitazione](site-recovery-vmware-to-azure-classic.md).

## <a name="overview"></a>Panoramica
Funzionamento del failback:

* Dopo aver eseguito il failover in Azure, eseguire il failback nel sito locale procedendo come segue:
  * **Fase 1**: abilitare la riprotezione delle VM di Azure in modo che inizino la replica nelle VM di VMware in esecuzione nel sito locale. Per farlo è anche necessario 
        1. Impostare una destinazione master locale
        2. Impostare un server di elaborazione
        3. E quindi avviare la riprotezione
  * **Fase 2**: dopo la replica delle VM di Azure nel sito locale, effettuare un failover per il failback da Azure.
  * **Fase 3**: dopo aver eseguito il failback dei dati, abilitare la riprotezione delle macchine virtuali locali in cui è stato eseguito il failback, in modo che inizino la replica in Azure.

### <a name="failback-to-the-original-or-alternate-location"></a>Eseguire il failback nella posizione originaria o in una posizione alternativa

    
Se è stato eseguito il failover di una macchina virtuale VMware è possibile eseguire il failback nella stessa macchina virtuale di origine, se è ancora presente in locale. In questo scenario viene eseguita la replica delle sole modifiche differenziali. Questo scenario è noto come ripristino nel percorso originale.

* Se si esegue il failback nella macchina virtuale originaria è necessario rispettare le condizioni seguenti:
  * Se la macchina virtuale è gestita da un server vCenter, l'host ESX della destinazione master deve avere accesso all'archivio dati delle macchine virtuali.
  * Se la macchina virtuale si trova in un host ESX ma non è gestita da vCenter, il disco rigido della macchina virtuale deve trovarsi in un archivio dati accessibile da parte dell'host della destinazione master.
  * Se la macchina virtuale si trova in un host ESX e non usa vCenter, è necessario completare l'individuazione dell'host ESX della destinazione master prima di abilitare la riprotezione. Questa opzione è valida anche per il failback di server fisici.

  
Se la macchina virtuale locale non esiste prima della riprotezione, lo scenario è definito ripristino nel percorso alternativo. In questo caso, il flusso di lavoro della riprotezione crea nuovamente la macchina virtuale locale.

* Quando si esegue il failback in una posizione alternativa la macchina virtuale viene ripristinata nello stesso host ESX in cui è distribuito il server di destinazione master. L'archivio dati usato per creare il disco sarà lo stesso archivio selezionato durante la riprotezione della macchina virtuale.
* È possibile eseguire il failback solo in un archivio dati VMFS. Se si dispone di vSAN o RDM, la riprotezione e il failback avranno esito negativo.
* La riprotezione comporta un trasferimento di dati iniziale di grandi dimensioni, a cui fanno seguito le modifiche delta. Questo perché la macchina virtuale non esiste in locale e perché deve essere eseguita nuovamente la replica dei dati completi. La riprotezione, inoltre, impiegherà più tempo rispetto al ripristino nel percorso originale.

In seguito al failover in Azure, è possibile eseguire il failback di una macchina fisica solo come macchina virtuale VMware, un'operazione chiamata anche P2A2V. Questo flusso di lavoro è considerato come ripristino in un percorso alternativo.

* Non è possibile eseguire il failback di un computer Windows Server 2008 R2 SP1 protetto e sottoposto a failover in Azure.
* È necessario individuare almeno un server di destinazione master e gli host ESX/ESXi necessari per il failback.


## <a name="pre-requisites"></a>Prerequisiti

* Se le macchine virtuali in cui si vuole eseguire il failback sono gestite da un server vCenter, assicurarsi di avere le autorizzazioni necessarie per l'individuazione di macchine virtuali nei server vCenter. [Altre informazioni](site-recovery-vmware-to-azure-classic.md#vmware-permissions-for-vcenter-access).
* Se in una macchina virtuale locale sono presenti snapshot la riprotezione avrà esito negativo. È possibile eliminare gli snapshot prima di continuare con la riprotezione.
* Prima di eseguire il failback è necessario creare due componenti aggiuntivi:
  * **Creare un server di elaborazione**. Il server di elaborazione viene usato per ricevere i dati dalla macchina virtuale protetta in Azure e inviarli in locale. Deve quindi trovarsi in una rete a bassa latenza tra il server di elaborazione e la macchina virtuale protetta. Pertanto, il server di elaborazione può essere locale se si usa una connessione ExpressRoute o in Azure si usa una VPN.
  * **Creare un server di destinazione master**: il server di destinazione riceve i dati di failback. Nel server di gestione creato in locale è installato un server di destinazione master per impostazione predefinita. Tuttavia, a seconda del volume di traffico sottoposto a failback, potrebbe essere necessario creare un server di destinazione master separato per il failback. 
        * Una macchina virtuale Linux necessita di un server di destinazione master Linux. 
        * Una macchina virtuale Windows richiede una destinazione master Windows.
* Quando si esegue un failback, è necessario un server di configurazione locale. Durante il failback, la macchina virtuale deve esistere nel database del server di configurazione, altrimenti il failback non avrà esito positivo. Assicurarsi pertanto di pianificare backup regolari del server. In caso di emergenza, è necessario ripristinare il server con lo stesso indirizzo IP in modo che il failback funzioni.
* Assicurarsi di configurare l'impostazione disk.enableUUID=true nei parametri di configurazione della VM di destinazione master in VMware. Se questa riga non esiste, aggiungerla perché è necessaria per fornire un valore UUID coerente al file VMDK in modo che venga installato correttamente.
* **Al server di destinazione master non può essere applicato Storage vMotion**. perché può impedire il completamento del failback. La VM non verrà attivata perché i dischi non risulteranno disponibili.
* È necessaria una nuova unità aggiunta al server di destinazione master. Questa unità è denominata unità di conservazione. Aggiungere un nuovo disco e formattare l'unità.


## <a name="steps-to-failback"></a>Passaggi per il failback

Prima di avviare il failback, **assicurarsi di aver completato la riprotezione delle macchine virtuali**. Le macchine virtuali devono essere in stato protetto e integre. Per riproteggere le macchine virtuali, leggere [queste ulteriori informazioni](site-recovery-how-to-reprotect.md).


1. Nella pagina degli elementi replicati selezionare la macchina virtuale e con il pulsante destro del mouse selezionare **Failover non pianificato**.
2. In **Conferma failover** verificare la direzione del failover (da Azure) e selezionare il punto di ripristino da usare per il failover. Usare il più recente o quello coerente con l'app più recente. Il punto coerente con l'app deve essere antecedente all'ultimo punto di temporizzazione e causerà la perdita di alcuni dati.
3. Durante il failover, Site Recovery arresta le macchine virtuali di Azure. Assicurarsi che il failback sia stato completato come previsto e quindi verificare che le macchine virtuali di Azure siano state arrestate.

## <a name="next-steps"></a>Passaggi successivi

Al termine del failback, è necessario eseguire il commit della macchina virtuale per garantire che vengano eliminate le macchine virtuali ripristinate in Azure.

1. Fare clic con il pulsante destro del mouse sull'elemento protetto e quindi su Commit. Verrà attivato un processo che rimuoverà da Azure le macchine virtuali di cui è stato eseguito il failover in precedenza.

Al termine del commit la macchina virtuale si trova nuovamente nel sito locale, ma non è protetta. Per avviare nuovamente la replica in Azure, eseguire queste operazioni:

1. In Insieme di credenziali > Impostazioni > Elementi replicati selezionare le VM di cui è stato eseguito il failback e fare clic su **Riproteggi**.
2. Specificare il valore del server di elaborazione da usare per inviare i dati ad Azure.
3. Fare clic su OK per avviare il processo di riprotezione.

Al termine del processo di riprotezione, la macchina virtuale viene replicata in Azure e sarà possibile eseguire il failover.

## <a name="common-issues"></a>Problemi comuni





<!--HONumber=Feb17_HO2-->


