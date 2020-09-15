---
title: Eseguire un failover durante il ripristino di emergenza con Azure Site Recovery
description: Come eseguire il failover di VM/server fisici in Azure con Azure Site Recovery.
ms.service: site-recovery
ms.topic: article
ms.date: 12/10/2019
ms.openlocfilehash: d2f7fed25955d6a34c6162b87b82bfae5e58ff41
ms.sourcegitcommit: 6e1124fc25c3ddb3053b482b0ed33900f46464b3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/15/2020
ms.locfileid: "90563982"
---
# <a name="run-a-failover-from-on-premises-to-azure"></a>Eseguire un failover dal sito locale in Azure

Questo articolo descrive come eseguire il failover dei computer locali in Azure in [Azure Site Recovery](site-recovery-overview.md)

## <a name="before-you-start"></a>Prima di iniziare

- [Informazioni sul processo](failover-failback-overview.md) di failover in ripristino di emergenza.
- Se si desidera eseguire il failover di più computer [, vedere la pagina](recovery-plan-overview.md) relativa alla raccolta di macchine virtuali in un piano di ripristino.
- Prima di eseguire un failover completo, eseguire un' [esercitazione sul ripristino di emergenza](site-recovery-test-failover-to-azure.md) per verificare che tutto funzioni come previsto.

## <a name="prepare-to-connect-after-failover"></a>Preparare la connessione dopo il failover

Per assicurarsi che sia possibile connettersi alle macchine virtuali di Azure create dopo il failover, di seguito sono riportate alcune operazioni che è necessario eseguire in locale prima del failover.


### <a name="prepare-on-premises-to-connect-after-failover"></a>Preparare l'ambiente locale per la connessione dopo il failover

Per connettersi alle macchine virtuali di Azure tramite RDP/SSH dopo il failover, è necessario eseguire una serie di operazioni in locale prima del failover.

**Dopo il failover** | **Posizione** | **Actions**
--- | --- | ---
**VM di Azure che esegue Windows** | Computer locale prima del failover | Per accedere alla VM di Azure tramite Internet, abilitare RDP e assicurarsi che vengano aggiunte regole TCP e UDP per **Pubblico** e che il protocollo RDP sia consentito in **Windows Firewall** > **App consentite** per tutti i profili.<br/><br/> Per accedere alla VM di Azure tramite una connessione da sito a sito, abilitare RDP nel computer e assicurarsi che il protocollo RDP sia consentito nella **Windows Firewall**  ->  **app e funzionalità consentite**per le reti di **dominio e private** .<br/><br/> <br/><br/> Rimuovere le route persistenti statiche e il proxy WinHTTP. Verificare che il criterio SAN del sistema operativo sia impostato su **OnlineAll**. [Altre informazioni](https://support.microsoft.com/kb/3031135)<br/><br/> Quando si attiva un failover, verificare che nella macchina virtuale non siano in sospeso aggiornamenti di Windows. L'aggiornamento di Windows potrebbe essere avviato durante il failover e non si potrà accedere alla macchina virtuale finché non sarà completato.
**VM di Azure che esegue Linux** | Computer locale prima del failover | Assicurarsi che il servizio Secure Shell nella macchina virtuale sia impostato per l'avvio automatico all'avvio del sistema.<br/><br/> Verificare che le regole firewall accettino la connessione SSH.


## <a name="run-a-failover"></a>Eseguire un failover

Questa procedura descrive come eseguire un failover per un [piano di ripristino](site-recovery-create-recovery-plans.md). Se si vuole eseguire un failover per una singola macchina virtuale, seguire le istruzioni per una VM [VMware](vmware-azure-tutorial-failover-failback.md), un [server fisico](physical-to-azure-failover-failback.md)o una [macchina virtuale Hyper-V](hyper-v-azure-failover-failback-tutorial.md).


Eseguire il failover del piano di ripristino come segue:

1. Nell'insieme di credenziali Site Recovery selezionare **piani di ripristino**  >  *recoveryplan_name*.
2. Fare clic su **Failover**.

    ![Screenshot mostra il riquadro ADRP con failover selezionato dal menu altro.](./media/site-recovery-failover/Failover.png)

3. Nella **Failover**  >  **direzione del failover**di failover lasciare l'impostazione predefinita se si esegue la replica in Azure.
4. In **failover**selezionare un **punto di ripristino** in cui eseguire il failover.

    - **Più recente**: usare il punto più recente. Vengono elaborati tutti i dati inviati al servizio Site Recovery e viene creato un punto di ripristino per ogni computer. Questa opzione fornisce il RPO più basso (obiettivo del punto di ripristino) perché la macchina virtuale creata dopo il failover include tutti i dati che sono stati replicati per Site Recovery quando è stato attivato il failover.
    Si noti che quando l'area di origine diventa inattiva, non è possibile eseguire altre operazioni di elaborazione dei log. Sarà quindi necessario eseguire il failover al punto di ripristino elaborato più recente. Vedere il punto successivo per altre informazioni.
   - **Elaborato più recente**: usare questa opzione per eseguire il failover delle macchine virtuali nel punto di ripristino più recente già elaborato da Site Recovery. È possibile visualizzare il punto di ripristino elaborato più recente nei **punti di ripristino più recenti**della macchina virtuale. Questa opzione offre un RTO basso in quanto non viene impiegato alcun tempo per l'elaborazione dei dati non elaborati
   - **Coerente con l'app più recente**: usare questa opzione per eseguire il failover delle VM nell'ultimo punto di ripristino coerente con l'applicazione elaborato da Site Recovery.
   - **Elaborato più recente di più macchine virtuali**: con questa opzione le VM che fanno parte di un gruppo di replica eseguono il failover al punto di ripristino coerente tra più macchine virtuali più recente. Il failover di altre macchine virtuali viene eseguito sul punto di ripristino elaborato più recente. Questa opzione è disponibile solo per i piani di ripristino con almeno una VM con la coerenza tra più macchine virtuali abilitata.
   - **Coerente con l'app più recente**: con questa opzione le VM che fanno parte di un gruppo di replica eseguono il failover nel punto di ripristino più recente coerente con l'applicazione di più macchine virtuali. Le altre macchine virtuali eseguono il failover nel relativo punto di recupero più recente coerente con l'applicazione. Solo per i piani di ripristino con almeno una VM con la coerenza tra più macchine virtuali abilitata.
   - **Personalizzata**: non disponibile per i piani di ripristino. Questa opzione è valida solo per il failover di singole macchine virtuali.

5. Selezionare **Arresta la macchina prima di iniziare il failover** se si vuole Site Recovery arrestare le VM di origine prima di avviare il failover. Il failover continua anche se l'arresto ha esito negativo.  

    > [!NOTE]
    > Se si esegue il failover di macchine virtuali Hyper-V, Shutdown tenta di sincronizzare e replicare i dati locali che non sono ancora stati inviati al servizio, prima di attivare il failover. 

6. Seguire lo stato di avanzamento del failover nella pagina **processi** . Anche se si verificano errori, il piano di ripristino viene eseguito fino al completamento.
7. Dopo il failover, accedere alla macchina virtuale per convalidarla. 
8. Se si desidera passare a un punto di ripristino diverso da utilizzare per il failover, utilizzare **modifica punto di ripristino**.
9. Quando si è pronti, è possibile eseguire il commit del failover. L'azione di **commit** Elimina tutti i punti di ripristino disponibili con il servizio. L'opzione **modifica punto di ripristino** non sarà più disponibile.

## <a name="run-a-planned-failover-hyper-v"></a>Eseguire un failover pianificato (Hyper-V)

È possibile eseguire un failover pianificato per le macchine virtuali Hyper-V.

- Un failover pianificato è un'opzione di failover senza perdita di dati.
- Quando viene attivato un failover pianificato, prima vengono arrestate le macchine virtuali di origine, vengono sincronizzati i dati più recenti e quindi viene attivato il failover.
- Per eseguire un failover pianificato, è possibile usare l'opzione **failover pianificato** . Viene eseguito in modo analogo a un normale failover.
 
## <a name="track-failovers"></a>Rilevare i failover

Sono disponibili diversi processi associati al failover.

![Failover](./media/site-recovery-failover/FailoverJob.png)

- **Controllo dei prerequisiti**: garantisce che siano soddisfatte tutte le condizioni necessarie per il failover.
- **Failover**: elabora i dati in modo da poter creare una macchina virtuale di Azure. Se è stato scelto il punto di ripristino **più recente** , viene creato un punto di ripristino dai dati che sono stati inviati al servizio.
- **Start**: crea una macchina virtuale di Azure usando i dati elaborati nel passaggio precedente.

> [!WARNING]
> **Non annullare un failover in corso**: prima dell'avvio del failover, la replica è stata arrestata per la macchina virtuale. Se si annulla un processo in corso, il failover viene arrestato, ma la macchina virtuale non inizierà a essere replicata. Non è possibile avviare di nuovo la replica.


### <a name="extra-failover-time"></a>Tempo di failover aggiuntivo

In alcuni casi, il failover della macchina virtuale richiede un passaggio intermedio che in genere richiede da 8 a 10 minuti per il completamento. Si tratta dei computer interessati da questo passaggio/ora aggiuntivo:

* Macchine virtuali VMware che eseguono una versione del servizio Mobility precedente alla 9,8.
* Server fisici e macchine virtuali Hyper-V protette come server fisici.
* Macchine virtuali Linux VMware.
* Macchine virtuali VMware in cui questi driver non sono presenti come driver di avvio:
    * storvsc
    * vmbus
    * storflt
    * intelide
    * atapi
* Macchine virtuali VMware in cui non è abilitato DHCP, indipendentemente dal fatto che usino indirizzi IP statici o DHCP.


## <a name="automate-actions-during-failover"></a>Automatizzare le azioni durante il failover

Potrebbe essere necessario automatizzare le azioni durante il failover. A tale scopo, è possibile usare gli script o manuali operativi di automazione di Azure nei piani di ripristino.

- [Informazioni sulla creazione](site-recovery-create-recovery-plans.md) e la personalizzazione dei piani di ripristino, inclusa l'aggiunta di script.
- [Informazioni sull'aggiunta](site-recovery-runbook-automation.md) di manuali operativi di automazione di Azure ai piani di ripristino.


## <a name="configure-settings-after-failover"></a>Configura impostazioni dopo il failover

### <a name="retain-drive-letters-after-failover"></a>Mantieni lettere di unità dopo il failover

Site Recovery gestisce la conservazione delle lettere di unità. Se si escludono dischi durante la replica della macchina virtuale, [vedere un esempio](exclude-disks-replication.md#example-1-exclude-the-sql-server-tempdb-disk) del funzionamento.

### <a name="prepare-in-azure-to-connect-after-failover"></a>Preparare in Azure per la connessione dopo il failover

Per connettersi alle macchine virtuali di Azure create dopo il failover tramite RDP o SSH, attenersi ai requisiti riepilogati nella tabella.

**Failover** | **Posizione** | **Actions**
--- | --- | ---
**VM di Azure che esegue Windows** | VM di Azure dopo il failover |  [Aggiungere un indirizzo IP pubblico](https://aka.ms/addpublicip) per la macchina virtuale.<br/><br/> Le regole del gruppo di sicurezza di rete nella VM sottoposta a failover e nella subnet di Azure a cui è connessa devono consentire le connessioni in ingresso alla porta RDP.<br/><br/> Selezionare **Diagnostica di avvio** per visualizzare uno screenshot della macchina virtuale.<br/><br/> Se non è possibile connettersi, verificare che la macchina virtuale sia in esecuzione e rivedere i suggerimenti per la [risoluzione dei problemi](https://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx).
**VM di Azure che esegue Linux** | VM di Azure dopo il failover | Le regole del gruppo di sicurezza di rete nella macchina virtuale sottoposta a failover e nella subnet di Azure a cui è connessa devono consentire le connessioni in ingresso alla porta SSH.<br/><br/> [Aggiungere un indirizzo IP pubblico](https://aka.ms/addpublicip) per la macchina virtuale.<br/><br/> Selezionare **Diagnostica di avvio** per visualizzare uno screenshot della macchina virtuale.<br/><br/>

Seguire i passaggi descritti [qui](site-recovery-failover-to-azure-troubleshoot.md) per risolvere i problemi di connettività dopo il failover.

## <a name="set-up-ip-addressing"></a>Configurare gli indirizzi IP

- **Indirizzi IP interni**: per impostare l'indirizzo IP interno di una macchina virtuale di Azure dopo il failover, sono disponibili due opzioni:
    - Mantenere lo stesso indirizzo IP: è possibile usare lo stesso indirizzo IP nella macchina virtuale di Azure come quello allocato al computer locale.
    - Usare un indirizzo IP diverso: è possibile usare un indirizzo IP diverso per la macchina virtuale di Azure.
    - [Altre](concepts-on-premises-to-azure-networking.md#assign-an-internal-address) informazioni sulla configurazione degli indirizzi IP interni.
- **Indirizzi IP esterni**: è possibile mantenere gli indirizzi IP pubblici in seguito al failover. Alle macchine virtuali di Azure create come parte del processo di failover deve essere assegnato un indirizzo IP pubblico di Azure disponibile nell'area di Azure. È possibile assegnare un indirizzo IP pubblico manualmente o automatizzando il processo con un piano di ripristino. [Altre informazioni](concepts-public-ip-address-with-site-recovery.md)


## <a name="next-steps"></a>Passaggi successivi

Dopo aver eseguito il failover, è necessario riproteggere per avviare la replica delle VM di Azure nel sito locale. Quando la replica è attiva e in esecuzione, è possibile eseguire il failback in locale quando si è pronti.

- [Altre](failover-failback-overview.md#reprotectionfailback) informazioni sulla riprotezione e il failback.
- [Preparare](vmware-azure-reprotect.md) la riprotezione e il failback di VMware.
- [Esegui failback](hyper-v-azure-failback.md) Macchine virtuali Hyper-V.
- Informazioni [sul](physical-to-azure-failover-failback.md) processo di failover e failback per i server fisici.

