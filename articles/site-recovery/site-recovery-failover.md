---
title: Eseguire un failover durante il ripristino di emergenza con Azure Site RecoveryRun a failover during disaster recovery with Azure Site Recovery
description: Come eseguire il failover di macchine virtuali/server fisici in Azure con Azure Site Recovery.How to fail over VMs/physical servers to Azure with Azure Site Recovery.
ms.service: site-recovery
ms.topic: article
ms.date: 12/10/2019
ms.openlocfilehash: 99a197e8f5ebac8a3b0be1b567ee41b43a2c4476
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79471269"
---
# <a name="run-a-failover-from-on-premises-to-azure"></a>Eseguire un failover dal sito locale in Azure

Questo articolo descrive come eseguire il failover dei computer locali in Azure in Azure in [Site Recovery](site-recovery-overview.md) di Azure

## <a name="before-you-start"></a>Prima di iniziare

- [Informazioni](failover-failback-overview.md) sul processo di failover nel ripristino di emergenza.
- Se si desidera eseguire il failover su più computer, [imparare](recovery-plan-overview.md) a riunire le macchine in un piano di ripristino.
- Prima di eseguire un failover completo, eseguire [un'esercitazione](site-recovery-test-failover-to-azure.md) sul ripristino di emergenza per assicurarsi che tutto funzioni come previsto.

## <a name="prepare-to-connect-after-failover"></a>Preparare la connessione dopo il failoverPrepare to connect after failover

Per assicurarsi di potersi connettere alle macchine virtuali di Azure create dopo il failover, ecco alcune operazioni che è necessario eseguire in locale prima del failover.


### <a name="prepare-on-premises-to-connect-after-failover"></a>Preparare l'ambiente locale per la connessione dopo il failoverPrepare on-premises to connect after failover

Se si vuole connettersi alle macchine virtuali di Azure usando RDP/SSH dopo il failover, è necessario eseguire una serie di operazioni in locale prima del failover.

**Dopo il failover** | **Percorso** | **Azioni**
--- | --- | ---
**VM di Azure che esegue Windows** | Computer locale prima del failover | Per accedere alla VM di Azure tramite Internet, abilitare RDP e assicurarsi che vengano aggiunte regole TCP e UDP per **Pubblico** e che il protocollo RDP sia consentito in **Windows Firewall** > **App consentite** per tutti i profili.<br/><br/> Per accedere alla macchina virtuale di Azure tramite una connessione da sito a sito, abilitare RDP nel computer e verificare che RDP sia consentito nelle app e nelle**funzionalità consentite**di **Windows Firewall** -> per le reti **di dominio e private.**<br/><br/> <br/><br/> Rimuovere tutte le route permanenti statiche e il proxy WinHTTP. Verificare che il criterio SAN del sistema operativo sia impostato su **OnlineAll**. [Scopri di più](https://support.microsoft.com/kb/3031135).<br/><br/> Quando si attiva un failover, verificare che nella macchina virtuale non siano in sospeso aggiornamenti di Windows. L'aggiornamento di Windows potrebbe essere avviato durante il failover e non si potrà accedere alla macchina virtuale finché non sarà completato.
**VM di Azure che esegue Linux** | Computer locale prima del failover | Assicurarsi che il servizio Secure Shell nella macchina virtuale sia impostato per l'avvio automatico all'avvio del sistema.<br/><br/> Verificare che le regole firewall accettino la connessione SSH.


## <a name="run-a-failover"></a>Eseguire un failover

Questa procedura descrive come eseguire un failover per un [piano di ripristino](site-recovery-create-recovery-plans.md). Se si desidera eseguire un failover per una singola macchina virtuale, seguire le istruzioni per una [macchina virtuale VMware,](vmware-azure-tutorial-failover-failback.md)un [server fisico](physical-to-azure-failover-failback.md)o una macchina [virtuale Hyper-V](hyper-v-azure-failover-failback-tutorial.md).


Eseguire il failover del piano di ripristino come segue:

1. Nell'insieme di credenziali di Site Recovery selezionare **Plan** > *recoveryplan_name*.
2. Fare clic su **Failover**.

    ![Failover](./media/site-recovery-failover/Failover.png)

3. In **Direzione failover** > lasciare l'impostazione predefinita se si esegue la replica in Azure.In**Failover direction**, leave the default if you're replicating to Azure.
4. In **Failover**selezionare un **punto di ripristino** in cui eseguire il failover.

    - **Ultimo**: Utilizzare il punto più recente. In questo modo vengono elaborati tutti i dati inviati al servizio Site Recovery e viene creato un punto di ripristino per ogni computer. Questa opzione fornisce il RPO più basso (Obiettivo punto di ripristino) perché la macchina virtuale creata dopo il failover contiene tutti i dati replicati in Site Recovery quando è stato attivato il failover.
   - **Ultima elaborazione:** usare questa opzione per eseguire il failover delle macchine virtuali nel punto di ripristino più recente già elaborato da Site Recovery.Latest processed : Use this option to fail over VMs to the latest recovery point already processed by Site Recovery. È possibile visualizzare il punto di ripristino elaborato più recente nei punti di **ripristino più recenti**della macchina virtuale. Questa opzione fornisce un RTO basso in quanto non viene impiegato tempo per l'elaborazione dei dati non elaborati
   - **Coerente con l'app più recente:** usare questa opzione per eseguire il failover delle macchine virtuali al punto di ripristino coerente dell'applicazione più recente elaborato da Site Recovery.
   - **Elaborazione più recente**di più macchine virtuali: con questa opzione le macchine virtuali che fanno parte di un failover del gruppo di replica al punto di ripristino coerente con più macchine virtuali comune più recente. Altre macchine virtuali eseguono il failover al punto di ripristino elaborato più recente. Questa opzione è solo per i piani di ripristino con almeno una macchina virtuale con coerenza multi-VM abilitata.
   - Più recente compatibile **con l'app multi-VM:** con questa opzione, le macchine virtuali che fanno parte di un gruppo di replica eseguono il failover al punto di ripristino coerente coerente con l'applicazione multi-VM comune più recente. Le altre macchine virtuali eseguono il failover nel relativo punto di recupero più recente coerente con l'applicazione. Solo per i piani di ripristino con almeno una macchina virtuale con coerenza multi-VM abilitata.
   - **Personalizzato**: Non disponibile per i piani di ripristino. Questa opzione è solo per il failover di singole macchine virtuali.

5. Selezionare Arresta computer prima di **avviare il failover** se si desidera che Site Recovery arresti le macchine virtuali di origine prima di avviare il failover. Il failover continua anche se l'arresto ha esito negativo.  

    > [!NOTE]
    > Se si esegue il failover delle macchine virtuali Hyper-V, l'arresto tenta di sincronizzare e replicare i dati locali che non sono ancora stati inviati al servizio, prima di attivare il failover. 

6. Seguire l'avanzamento del failover nella pagina **Processi.Follow** failover progress on the Jobs page. Anche se si verificano errori, il piano di ripristino viene eseguito fino al completamento.
7. Dopo il failover, accedere alla macchina virtuale per convalidarla. 
8. Se si desidera passare a un punto di ripristino diverso da utilizzare per il failover, utilizzare **Cambia punto di ripristino**.
9. Quando si è pronti, è possibile eseguire il commit del failover. L'azione **Commit** elimina tutti i punti di ripristino disponibili con il servizio. L'opzione **Cambia punto** di ripristino non sarà più disponibile.

## <a name="run-a-planned-failover-hyper-v"></a>Eseguire un failover pianificato (Hyper-V)

È possibile eseguire un failover pianificato per le macchine virtuali Hyper-V.You can run a planned failover for Hyper-V V VMs.

- Un failover pianificato è un'opzione di failover per la perdita di dati zero.
- Quando viene attivato un failover pianificato, prima vengono arrestate le macchine virtuali di origine, vengono sincronizzati i dati più recenti e quindi viene attivato il failover.
- Si esegue un failover pianificato utilizzando l'opzione **Failover pianificato** . Viene eseguito in modo simile a un failover regolare.
 
## <a name="track-failovers"></a>Tenere traccia dei failoverTrack failovers

Esistono diversi processi associati al failover.

![Failover](./media/site-recovery-failover/FailoverJob.png)

- **Controllo dei prerequisiti**: verifica che siano soddisfatte tutte le condizioni necessarie per il failover.
- **Failover:** elabora i dati in modo che sia possibile crearne una macchina virtuale di Azure.Failover : Processes the data so that an Azure VM can be created from it. Se è stato scelto Punto di ripristino **più recente,** viene creato un punto di ripristino dai dati inviati al servizio.
- **Avvia:** crea una macchina virtuale di Azure usando i dati elaborati nel passaggio precedente.

> [!WARNING]
> **Non annullare un failover in corso:** prima dell'avvio del failover, la replica s interrotta per la macchina virtuale. Se si annulla un processo in corso, il failover viene interrotto, ma la macchina virtuale non inizierà a eseguire la replica. Impossibile riavviare la replica.


### <a name="extra-failover-time"></a>Tempo di failover aggiuntivo

In alcuni casi, il failover della macchina virtuale richiede un passaggio intermedio che richiede in genere da otto a 10 minuti per il completamento. Queste sono le macchine interessate da questo passaggio/tempo aggiuntivo:

* Macchine virtuali VMware che eseguono una versione del servizio Mobility precedente alla 9.8.
* Server fisici e macchine virtuali Hyper-V protette come server fisici.
* Macchine virtuali Linux VMware.
* Macchine virtuali VMware in cui questi driver non sono presenti come driver di avvio:
    * storvsc
    * vmbus
    * storflt
    * intelide
    * atapi
* Macchine virtuali VMware che non hanno DHCP abilitato, indipendentemente dal fatto che utilizzino DHCP o indirizzi IP statici.


## <a name="automate-actions-during-failover"></a>Automatizzare le azioni durante il failoverAutomate actions during failover

È possibile automatizzare le azioni durante il failover. A tale scopo, è possibile usare script o runbook di automazione di Azure nei piani di ripristino.

- [Informazioni](site-recovery-create-recovery-plans.md) sulla creazione e la personalizzazione di piani di ripristino, inclusa l'aggiunta di script.
- [Informazioni sull'aggiunta](site-recovery-runbook-automation.md) di runbook di Automazione di Azure ai piani di ripristino.


## <a name="configure-settings-after-failover"></a>Configurare le impostazioni dopo il failover

### <a name="retain-drive-letters-after-failover"></a>Conservare le lettere di unità dopo il failoverRetain drive letters after failover

Site Recovery gestisce la conservazione delle lettere di unità. Se si escludono dischi durante la replica della macchina virtuale, [esaminare un esempio](exclude-disks-replication.md#example-1-exclude-the-sql-server-tempdb-disk) di come funziona.

### <a name="prepare-in-azure-to-connect-after-failover"></a>Preparare in Azure la connessione dopo il failoverPrepare in Azure to connect after failover

Se si vuole connettersi alle macchine virtuali di Azure create dopo il failover tramite RDP o SSH, seguire i requisiti riepilogati nella tabella.

**Failover** | **Percorso** | **Azioni**
--- | --- | ---
**VM di Azure che esegue Windows** | VM di Azure dopo il failover |  [Aggiungere un indirizzo IP pubblico](https://aka.ms/addpublicip) per la macchina virtuale.<br/><br/> Le regole del gruppo di sicurezza di rete nella VM sottoposta a failover e nella subnet di Azure a cui è connessa devono consentire le connessioni in ingresso alla porta RDP.<br/><br/> Selezionare **Diagnostica di avvio** per visualizzare uno screenshot della macchina virtuale.<br/><br/> Se non è possibile connettersi, verificare che la macchina virtuale sia in esecuzione ed esaminare questi [suggerimenti per la risoluzione dei problemi.](https://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx)
**VM di Azure che esegue Linux** | VM di Azure dopo il failover | Le regole del gruppo di sicurezza di rete nella macchina virtuale sottoposta a failover e nella subnet di Azure a cui è connessa devono consentire le connessioni in ingresso alla porta SSH.<br/><br/> [Aggiungere un indirizzo IP pubblico](https://aka.ms/addpublicip) per la macchina virtuale.<br/><br/> Selezionare **Diagnostica di avvio** per visualizzare uno screenshot della macchina virtuale.<br/><br/>

Seguire i passaggi descritti [qui](site-recovery-failover-to-azure-troubleshoot.md) per risolvere i problemi di connettività dopo il failover.

## <a name="set-up-ip-addressing"></a>Configurare l'indirizzamento IP

- **Indirizzi IP interni:** per impostare l'indirizzo IP interno di una macchina virtuale di Azure dopo il failover, sono disponibili due opzioni:Internal IP addresses : To set the internal IP address of an Azure VM after failover, you have a couple of options:
    - Mantieni lo stesso indirizzo IP: è possibile usare lo stesso indirizzo IP nella macchina virtuale di Azure di quella allocata al computer locale.
    - Use different IP address: You can use a different IP address for the Azure VM.
    - [Ulteriori informazioni](concepts-on-premises-to-azure-networking.md#assign-an-internal-address) sulla configurazione degli indirizzi IP interni.
- **Indirizzi IP esterni**: È possibile mantenere gli indirizzi IP pubblici in caso di failover. Alle macchine virtuali di Azure create come parte del processo di failover deve essere assegnato un indirizzo IP pubblico di Azure disponibile nell'area di Azure.Azure VMs created as part of the failover process must be assigned an Azure public IP address available in the Azure region. È possibile assegnare un indirizzo IP pubblico manualmente o automatizzando il processo con un piano di ripristino. [Scopri di più](concepts-public-ip-address-with-site-recovery.md).


## <a name="next-steps"></a>Passaggi successivi

Dopo aver eseguito il failover, è necessario riproteggere per avviare la replica delle macchine virtuali di Azure nel sito locale. Dopo che la replica è attiva e in esecuzione, è possibile eseguire il failback in locale quando si è pronti.

- [Altre informazioni](failover-failback-overview.md#reprotectionfailback) sulla riprotezione e il failback.
- [Prepararsi](vmware-azure-reprotect.md) per la riprotezione e il failback di VMware.
- [Failback](hyper-v-azure-failback.md) Macchine virtuali Hyper-V.
- [Informazioni sul](physical-to-azure-failover-failback.md) processo di failover e failback per i server fisici.

