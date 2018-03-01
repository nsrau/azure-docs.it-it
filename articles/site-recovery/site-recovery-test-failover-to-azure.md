---
title: Failover di test in Azure in Azure Site Recovery | Microsoft Docs
description: Informazioni su come eseguire un failover di test da un computer locale in Azure con il servizio Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 02/08/2018
ms.author: raynew
ms.openlocfilehash: c6a227ca78a1312fe315cc6838834ec956a08b01
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/09/2018
---
# <a name="test-failover-to-azure-in-site-recovery"></a>Failover di test in Azure in Site Recovery


In questo articolo viene illustrato come eseguire un'analisi di ripristino di emergenza in Azure tramite un failover di test di Site Recovery.  

Il failover di test viene eseguito per convalidare la strategia di replica e di ripristino di emergenza senza perdita di dati o tempi di inattività. Un failover di test non ha alcun impatto sulla replica in corso o sull'ambiente di produzione. È possibile eseguire un failover di test su una macchina virtuale (VM) specifica o all'interno di un [piano di ripristino](site-recovery-create-recovery-plans.md) che contiene più VM. 


## <a name="run-a-test-failover"></a>Eseguire un failover di test
Questa procedura descrive come eseguire un failover di test per un piano di ripristino. 

![Failover di test](./media/site-recovery-test-failover-to-azure/TestFailover.png)


1. Nel portale di Azure, in Site Recovery fare clic su **Piani di ripristino** > *recoveryplan_name* > **Failover di test**.
2. Selezionare un **Punto di ripristino** in cui eseguire il failover. È possibile usare una delle opzioni seguenti.
    - **Elaborato più recente**: questa opzione consente di eseguire il failover di tutte le macchine virtuali del piano nel punto di ripristino più recente elaborato da Site Recovery. Per vedere il punto di ripristino più recente per una macchina virtuale specifica, selezionare **Punti di ripristino più recenti** nelle impostazioni della macchina virtuale. Offre un RTO (Recovery Time Objective) basso poiché non viene impiegato tempo per elaborare dati non elaborati.
    - **Coerente con l'app più recente**: questa opzione esegue il failover di tutte le macchine virtuali del piano nel punto di ripristino coerente con l'app più recente elaborato da Site Recovery. Per vedere il punto di ripristino più recente per una macchina virtuale specifica, selezionare **Punti di ripristino più recenti** nelle impostazioni della macchina virtuale. 
    - **Più recente**: con questa opzione vengono prima elaborati tutti i dati inviati al servizio Site Recovery per creare un punto di ripristino per ogni VM e quindi viene eseguito il failover in tale punto di ripristino. Questa opzione offre il valore RPO (Recovery Point Objective) più basso perché la macchina virtuale creata dopo il failover conterrà tutti i dati che sono stati replicati in Site Recovery all'attivazione del failover.
    - **Elaborato più recente tra più macchine virtuali**: questa opzione è disponibile per i piani di ripristino con una o più macchine virtuali in cui è abilitata la coerenza tra più macchine virtuali. Le macchine virtuali in cui è abilitata l'impostazione eseguono il failover nel punto di ripristino coerente tra più macchine comune più recente. Le altre macchine virtuali eseguono il failover nel relativo punto di ripristino più recente elaborato.  
    - **Coerente con l'app più recente tra più macchine virtuali**: questa opzione è disponibile per i piani di ripristino con una o più macchine virtuali in cui è abilitata la coerenza tra più macchine virtuali. Le macchine virtuali che fanno parte di un gruppo di replica eseguono il failover nel punto di ripristino coerente a livello applicazione tra più macchine comune più recente. Le altre macchine virtuali eseguono il failover nel relativo punto di ripristino più recente coerente con l'applicazione. 
    - **Personalizzato**: questa opzione consente di eseguire il failover di una macchina virtuale specifica in un determinato punto di ripristino.
3. Selezionare una rete virtuale di Azure in cui verranno create le macchine virtuali di test.

    - Site Recovery tenta di creare le macchine virtuali di test in una subnet con lo stesso nome e con lo stesso indirizzo IP specificati nelle impostazioni **Calcolo e rete** della macchina virtuale.
    - Se nella rete virtuale di Azure specificata per il failover di test non è disponibile una subnet con lo stesso nome, la macchina virtuale di test verrà creata nella prima subnet in ordine alfabetico.
    - Se nella subnet non è disponibile lo stesso indirizzo IP, la macchina virtuale ottiene un altro indirizzo IP disponibile nella subnet. [Altre informazioni](#creating-a-network-for-test-failover).
4. Se si esegue il failover in Azure ed è abilitata la crittografia dei dati, in **Chiave di crittografia** selezionare il certificato rilasciato quando è stata abilitata la crittografia durante l'installazione del provider. Se la crittografia non è stata abilitata, è possibile ignorare questo passaggio.
5. Tenere traccia dello stato di avanzamento del failover nella scheda **Processi** . Nel portale di Azure dovrebbe anche essere possibile vedere la macchina di replica di test.
6. Per avviare una connessione RDP nella macchina virtuale di Azure, è necessario [aggiungere un indirizzo IP pubblico](https://aka.ms/addpublicip) nell'interfaccia di rete della macchina virtuale sottoposta a failover. 
7. Quando tutto funziona come previsto, fare clic su **Pulisci failover di test**. Verranno eliminate le VM create durante il failover di test.
8. Fare clic su **Note** per registrare e salvare eventuali osservazioni associate al failover di test. 


![Failover di test](./media/site-recovery-test-failover-to-azure/TestFailoverJob.png)

L'attivazione di un failover di test comporta l'esecuzione dei passaggi seguenti:

1. **Prerequisiti**: viene eseguito un controllo dei prerequisiti per verificare che tutte le condizioni necessarie per il failover siano in atto.
2. **Failover**: il failover elabora e prepara i dati in modo che sia possibile creare una macchina virtuale di Azure.
3. **Più recente**: se si è scelto il punto di ripristino più recente, viene creato un punto di ripristino dai dati che sono stati inviati al servizio.
4. **Avvio**: questo passaggio crea una macchina virtuale di Azure usando i dati elaborati nel passaggio precedente.

### <a name="failover-timing"></a>Intervallo di failover

Negli scenari seguenti, il failover richiede un passaggio aggiuntivo intermedio che in genere viene completato in circa 8-10 minuti:

* Macchine virtuali VMware che usano una versione del servizio Mobility precedente alla 9.8
* Server fisici
* Macchine virtuali Linux VMware
* Macchine virtuali Hyper-V protette come server fisici
* Macchine virtuali VMware in cui i driver seguenti non sono driver di avvio:
    * storvsc
    * vmbus
    * storflt
    * intelide
    * atapi
* Macchine virtuali VMware che non dispongono del servizio DHCP abilitato indipendentemente che usino indirizzi IP statici o DHCP.

In tutti gli altri casi questo passaggio intermedio non è necessario e il tempo impiegato per il failover è notevolmente inferiore.


## <a name="create-a-network-for-test-failover"></a>Creare una rete per il failover di test

Durante un failover di test, è consigliabile scegliere una rete isolata dalla rete del sito di ripristino di produzione specificata nelle impostazioni **Calcolo e rete** di ogni macchina virtuale. Quando si crea una rete virtuale di Azure, per impostazione predefinita è isolata dalle altre reti. La rete di test dovrà simulare la rete di produzione:

- La rete di test dovrà avere lo stesso numero di subnet della rete di produzione. Le subnet dovranno avere lo stesso nome.
- La rete di test dovrà usare lo stesso intervallo di indirizzi IP.
- Aggiornare il DNS della rete di test con l'indirizzo IP specificato per la macchina virtuale DNS nelle impostazioni **Calcolo e rete**. Per altri dettagli, vedere [Considerazioni sul failover di test per Active Directory](site-recovery-active-directory.md#test-failover-considerations).


## <a name="test-failover-to-a-production-network-in-the-recovery-site"></a>Failover di test in una rete di produzione nel sito di ripristino

Anche se è consigliabile usare una rete di test diversa dalla rete di produzione, se si vuole eseguire un'esercitazione sul ripristino di emergenza nella rete di produzione, tenere presente quanto segue: 

- Verificare che la macchina virtuale primaria sia arrestata quando si esegue il failover di test. In caso contrario si avranno due macchine virtuali con la stessa identità in esecuzione contemporaneamente sulla stessa rete. Questa situazione può provocare conseguenze impreviste.
- Le modifiche apportate alle macchine virtuali create per il failover di test andranno perdute durante la pulizia del failover. Le modifiche non vengono replicate nella macchina virtuale primaria.
- L'esecuzione dei test nell'ambiente di produzione determina un tempo di inattività dell'applicazione di produzione. Durante il failover di test gli utenti non dovranno usare le app che vengono eseguite nelle macchine virtuali.  



## <a name="prepare-active-directory-and-dns"></a>Preparare Active Directory e DNS

Per eseguire un failover di test per testare l'applicazione è necessaria una copia dell'ambiente Active Directory di produzione nell'ambiente di test. Per altre informazioni, vedere [Considerazioni sul failover di test per Active Directory](site-recovery-active-directory.md#test-failover-considerations).

## <a name="prepare-to-connect-to-azure-vms-after-failover"></a>Preparare la connessione alle macchine virtuali di Azure dopo il failover

Per connettersi alle macchine virtuali di Azure con RDP dopo il failover, seguire i requisiti riepilogati nella tabella.

**Failover** | **Posizione** | **Actions**
--- | --- | ---
**VM di Azure che esegue Windows** | Computer locale prima del failover | Per accedere alla VM di Azure tramite Internet, abilitare RDP e assicurarsi che vengano aggiunte regole TCP e UDP per **Pubblico** e che il protocollo RDP sia consentito in **Windows Firewall** > **App consentite** per tutti i profili.<br/><br/> Per l'accesso alla VM di Azure tramite una connessione da sito a sito, abilitare RDP nel computer e assicurarsi che il protocollo RDP sia consentito in **Windows Firewall** -> **App e funzionalità consentite** per le reti di **dominio e private**.<br/><br/>  Verificare che il criterio SAN del sistema operativo sia impostato su **OnlineAll**. [Altre informazioni](https://support.microsoft.com/kb/3031135).<br/><br/> Quando si attiva un failover, verificare che nella macchina virtuale non siano in sospeso aggiornamenti di Windows. L'aggiornamento di Windows potrebbe essere avviato durante il failover e non si potrà accedere alla macchina virtuale finché non sarà completato. 
**VM di Azure che esegue Windows** | VM di Azure dopo il failover |  [Aggiungere un indirizzo IP pubblico](https://aka.ms/addpublicip) per la macchina virtuale.<br/><br/> Le regole del gruppo di sicurezza di rete nella VM sottoposta a failover e nella subnet di Azure a cui è connessa devono consentire le connessioni in ingresso alla porta RDP.<br/><br/> Selezionare **Diagnostica di avvio** per visualizzare uno screenshot della macchina virtuale.<br/><br/> Se non è possibile connettersi, controllare che la macchina virtuale sia in esecuzione e rivedere i [suggerimenti per la risoluzione dei problemi](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx).
**VM di Azure che esegue Linux** | Computer locale prima del failover | Assicurarsi che il servizio Secure Shell nella macchina virtuale sia impostato per l'avvio automatico all'avvio del sistema.<br/><br/> Verificare che le regole firewall accettino la connessione SSH.
**VM di Azure che esegue Linux** | VM di Azure dopo il failover | Le regole del gruppo di sicurezza di rete nella macchina virtuale sottoposta a failover e nella subnet di Azure a cui è connessa devono consentire le connessioni in ingresso alla porta SSH.<br/><br/> [Aggiungere un indirizzo IP pubblico](https://aka.ms/addpublicip) per la macchina virtuale.<br/><br/> Selezionare **Diagnostica di avvio** per visualizzare uno screenshot della macchina virtuale.<br/><br/>



## <a name="next-steps"></a>Passaggi successivi
Dopo aver completato un'esercitazione sul ripristino di emergenza, leggere altre informazioni sugli altri tipi di [failover](site-recovery-failover.md).
