---
title: Ristabilire la protezione delle macchine virtuali di Azure in un sito locale durante il ripristino di emergenza di macchine virtuali VMware e server fisici | Microsoft Docs
description: Informazioni su come eseguire il failback nel sito locale dopo il failover in Azure durante il ripristino di emergenza di macchine virtuali VMware e server fisici in Azure.
author: rajani-janaki-ram
manager: gauravd
ms.service: site-recovery
ms.topic: conceptual
ms.date: 12/17/2018
ms.author: rajanaki
ms.openlocfilehash: 06337e205c472d26024289222dc8876d23b4184f
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/27/2018
ms.locfileid: "53791881"
---
# <a name="reprotect-and-fail-back-machines-to-an-on-premises-site-after-failover-to-azure"></a>Ristabilire la protezione ed eseguire il failback su macchine in un sito locale dopo il failover in Azure

Dopo il [failover](site-recovery-failover.md) di macchine virtuali VMware locali o server fisici in Azure, il primo passaggio dell'esecuzione di un failback nel sito locale consiste nel riapplicare la protezione alle macchine virtuali di Azure create durante il failover. Questo articolo illustra come farlo. 

Per una rapida panoramica, guardare il video seguente che illustra come eseguire il failover da Azure a un sito locale.<br /><br />
> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/VMware-to-Azure-with-ASR-Video5-Failback-from-Azure-to-On-premises/player]


## <a name="before-you-begin"></a>Prima di iniziare

Se è stato usato un modello per creare le macchine virtuali, assicurarsi che ogni macchina virtuale abbia un UUID univoco per i dischi. Se l'UUID della macchina virtuale locale è in conflitto con quello del server di destinazione master perché entrambi sono stati creati a partire dallo stesso modello, la riprotezione non riesce. Distribuire un altro server di destinazione master che non sia stato creato dallo stesso modello. Tenere presente quanto segue:
- Se si sta tentando di eseguire il failback a un vCenter alternativo, assicurarsi che il nuovo vCenter e il server di destinazione master vengano rilevati. Come sintomo tipico, gli archivi dati non sono accessibili o visibili nella finestra di dialogo **Riproteggi**.
- Per eseguire la replica locale, sono necessari criteri di failback. Questi criteri vengono creati automaticamente quando si creano i criteri di direzione in avanti. Tenere presente quanto segue:
    - Questi criteri vengo associati automaticamente al server di configurazione durante la creazione.
    - Questi criteri non sono modificabili.
    - I valori impostati dei criteri sono: Soglia RPO = 15 minuti, Conservazione del punto di ripristino = 24 ore, Frequenza snapshot per la coerenza con l'app = 60 minuti.
- Durante la riprotezione e il failback, il server di configurazione locale deve essere in esecuzione e connesso.
- Se le macchine virtuali in cui si vuole eseguire il failback sono gestite da un server vCenter, verificare di avere le [autorizzazioni necessarie](vmware-azure-tutorial-prepare-on-premises.md#prepare-an-account-for-automatic-discovery) per l'individuazione di macchine virtuali nei server vCenter.
- Eliminare gli snapshot nel server di destinazione master prima di abilitare la riprotezione. Se nella destinazione master o nella macchina virtuale locale sono presenti snapshot, la riprotezione avrà esito negativo. Durante il processo di riprotezione, gli snapshot nella macchina virtuale vengono uniti automaticamente.
- Le macchine virtuali di un gruppo di replica devono avere tutte lo stesso tipo di sistema operativo, ovvero devono essere tutte Windows o tutte Linux. Non è attualmente previsto il supporto per la riprotezione e il failback in locale di un gruppo di replica con sistemi operativi misti. Questo perché la destinazione master deve avere lo stesso sistema operativo della macchina virtuale. Tutte le macchine virtuali di un gruppo di replica devono avere la stessa destinazione master. 
- Quando si esegue un failback, è necessario un server di configurazione locale. Durante il failback, la macchina virtuale deve esistere nel database del server di configurazione. In caso contrario, il failback ha esito negativo. Assicurarsi di pianificare backup regolari del server di configurazione. In caso di emergenza, ripristinare il server con lo stesso indirizzo IP in modo che il failback funzioni.
- La riprotezione e il failback richiedono una VPN da sito a sito (S2S) per la replica dei dati. La rete deve essere configurata per consentire alle macchine virtuali con failover in Azure di raggiungere (ping) il server di configurazione locale. Può anche essere necessario distribuire un server di elaborazione nella rete di Azure della macchina virtuale sottoposta a failover. Questo server di elaborazione deve anche poter comunicare con il server di configurazione locale.
- Assicurarsi di aprire le porte seguenti per il failover e il failback:

    ![Porte per il failover e il failback](./media/vmware-azure-reprotect/failover-failback.png)

- Leggere tutti i [prerequisiti per l'inserimento delle porte e degli URL nell'elenco elementi consentiti](vmware-azure-deploy-configuration-server.md#prerequisites).

## <a name="deploy-a-process-server-in-azure"></a>Distribuire un server di elaborazione in Azure

Potrebbe essere necessario un server di elaborazione in Azure prima di eseguire il failback del sito locale:
- Il server di elaborazione riceve i dati dalla macchina virtuale protetta in Azure e quindi invia i dati al sito locale.
- È necessaria una rete a bassa latenza tra il server di elaborazione e la macchina virtuale protetta. In generale, è necessario prendere in considerazione la latenza quando si decide se è necessario un server di elaborazione in Azure:
    - Se è stata configurata una connessione Azure ExpressRoute, è possibile usare un server di elaborazione locale per inviare i dati, perché la latenza tra la macchina virtuale e il server di elaborazione è bassa.
    - Se, tuttavia, è disponibile solo una rete VPN da sito a sito, è consigliabile distribuire il server di elaborazione in Azure.
    - È consigliabile usare un server di elaborazione basato su Azure durante il failback. Le prestazioni di replica sono migliori se il server di elaborazione è più vicino alla macchina virtuale di replica (computer su cui viene eseguito il failover in Azure). Per un modello di verifica è possibile usare il server di elaborazione locale ed ExpressRoute con peering privato.

Per distribuire un server di elaborazione in Azure:

1. Se è necessario distribuire un server di elaborazione in Azure, vedere [Configurare un server di elaborazione in Azure per il failback](vmware-azure-set-up-process-server-azure.md).
2. Le macchine virtuali di Azure inviano i dati di replica al server di elaborazione. Configurare le reti in modo che le macchine virtuali di Azure possano raggiungere il server di elaborazione.
3. Tenere presente che la replica da Azure a locale può avere luogo solo tramite la VPN da sito a sito o tramite il peering privato della rete ExpressRoute. Assicurarsi che sia presente una larghezza di banda sufficiente nel canale di rete.

## <a name="deploy-a-separate-master-target-server"></a>Distribuire un server di destinazione master distinto

Il server di destinazione master riceve i dati di failback. Per impostazione predefinita, il server di destinazione master viene eseguito nel server di configurazione locale. Tuttavia, a seconda del volume di traffico sottoposto a failback, potrebbe essere necessario creare un server di destinazione master separato per il failback. Di seguito viene illustrata la procedura di creazione:

* [Creare un server di destinazione master Linux](vmware-azure-install-linux-master-target.md) per il failback delle macchine virtuali Linux. È un'operazione obbligatoria. Si noti che il server master di destinazione su LVM non è supportato.
* Creare facoltativamente un server di destinazione master separato per il failback della macchina virtuale Windows. A tale scopo, eseguire nuovamente l'installazione unificata e selezionare l'opzione per creare un server di destinazione master. [Altre informazioni](site-recovery-plan-capacity-vmware.md#deploy-additional-master-target-servers) 

Dopo aver creato un server di destinazione master, eseguire le attività seguenti:

- Se la macchina virtuale è presente in locale nel server vCenter, il server di destinazione master deve poter accedere al file del disco della macchina virtuale (VMDK) nella macchina virtuale locale. L'accesso è necessario per scrivere i dati replicati nei dischi della macchina virtuale. Assicurarsi che l'archivio dati della macchina virtuale locale sia montato nell'host del server di destinazione master con accesso in lettura/scrittura.
- Se la macchina virtuale non è presente in locale nel server vCenter, il servizio Site Recovery deve creare una nuova macchina virtuale durante la riprotezione. Questa macchina virtuale viene creata nell'host ESX in cui si crea il server di destinazione master. Scegliere con attenzione l'host ESX in modo che la macchina virtuale di failback venga creata nell'host desiderato.
- Non è possibile usare Storage vMotion per il server di destinazione master. L'uso di Storage vMotion per il server di destinazione master può causare l'esito negativo del failback. La macchina virtuale non viene avviata perché i dischi non risultano disponibili. Per evitare questo problema, escludere i server di destinazione master dall'elenco di vMotion.
- Se una destinazione master viene sottoposta a un'attività di Storage vMotion dopo la riprotezione, per i dischi delle macchine virtuali protette collegati alla destinazione master viene eseguita la migrazione alla destinazione dell'attività di vMotion. Se dopo questa operazione si prova a eseguire failback, lo scollegamento del disco non riesce perché non sono stati trovati i dischi. Diventa quindi molto difficile trovare i dischi negli account di archiviazione: sarà necessario trovarli manualmente e collegarli alla macchina virtuale. Dopo aver eseguito questa operazione, è possibile riavviare la macchina virtuale locale.
- Aggiungere un'unità di conservazione al server di destinazione master Windows esistente. Aggiungere un nuovo disco e formattare l'unità. L'unità di conservazione viene usata per arrestare i punti nel tempo quando la macchina virtuale esegue di nuovo la replica nel sito locale. Di seguito sono elencati alcuni criteri relativi a un'unità di conservazione, Se questi criteri non vengono soddisfatti, l'unità non viene elencata per il server di destinazione master:
    - Il volume non viene usato per altri scopi, ad esempio come destinazione della replica.
    - Il volume non è in modalità di blocco.
    - Il volume non è un volume della cache. Nel volume non può esistere un'installazione di destinazione master. Il volume di installazione personalizzata per il server di elaborazione e il server di destinazione master non è idoneo per un volume di conservazione. Quando il server di elaborazione e il server di destinazione master vengono installati in un volume, si tratta di un volume della cache del server di destinazione master.
    - Il file system del volume non è di tipo FAT o FAT32.
    - La capacità del volume è diversa da zero.
    - Il volume di conservazione predefinito per Windows è il volume R.
    - Il volume di conservazione predefinito per Linux è /mnt/retention/.
- È necessario aggiungere una nuova unità se si usa un server di elaborazione/server di configurazione esistente o un computer di scala o un server di elaborazione/server di destinazione master. La nuova unità deve soddisfare i requisiti sopra indicati. Se l'unità di conservazione non è presente, non verrà inclusa nell'elenco a discesa di selezione nel portale. Dopo aver aggiunto un'unità alla destinazione master locale, sono necessari fino a 15 minuti perché l'unità appaia nell'elenco di selezione nel portale. È anche possibile aggiornare il server di configurazione se l'unità non appare dopo 15 minuti.
- Installare gli strumenti VMware oppure open-vm-tools nel server master di destinazione. Senza gli strumenti, non è possibile rilevare gli archivi dati nell'host ESXi del server di destinazione master.
- Configurare l'impostazione `disk.EnableUUID=true` nei parametri di configurazione della macchina virtuale di destinazione master in VMware. Se questa riga non esiste, aggiungerla. Questa impostazione è necessaria per fornire un valore UUID coerente al file VMDK in modo che venga montato correttamente.
- L'host ESX in cui viene creata la destinazione master deve avere almeno un archivio dati VMFS (Virtual Machine File System) collegato a esso. In assenza di archivi dati VMFS collegati, l'input **Datastore** nella pagina di riprotezione è vuoto e non è possibile procedere.
- Il server di destinazione master non può avere snapshot nei dischi. Se sono presenti snapshot, la riprotezione e il failback non riusciranno.
- Il server di destinazione master non può avere un controller SCSI Paravirtual. Il controller può essere solo un controller LSI Logic. Senza un controller LSI Logic, la riprotezione non riuscirà.
- Per qualsiasi istanza, al server di destinazione master possono essere collegati al massimo 60 dischi. Se le macchine virtuali da riproteggere nel server di destinazione master locale hanno in totale un numero di dischi superiore a 60, la riprotezione nel server di destinazione master avrà esito negativo. Verificare di avere slot di disco sufficienti nel server di destinazione master o distribuire altri server.
    

## <a name="enable-reprotection"></a>Abilitare la riprotezione

Dopo l'avvio di una macchina virtuale in Azure, registrare di nuovo l'agente nel server di configurazione può richiedere fino a 15 minuti. Durante questo intervallo di tempo non sarà possibile abilitare la riprotezione e un messaggio di errore indica che l'agente non è installato. In questo caso, attendere alcuni minuti e ritentare l'operazione di riprotezione:


1. Selezionare **Insieme di credenziali** > **Elementi replicati**. Fare clic con il pulsante destro del mouse sulla macchina virtuale di cui è stato eseguito il failover e scegliere **Riproteggi**. In alternativa, dai pulsanti di comando selezionare la macchina virtuale e quindi selezionare **Riproteggi**.
2. Verificare che sia selezionata la direzione di protezione **Da Azure a locale**.
3. In **Server di destinazione master** e **Server di elaborazione** selezionare il server di destinazione master locale e il server di elaborazione.  
4. Per **Archivio dati**, selezionare l'archivio dati in cui ripristinare i dischi in locale. Questa opzione viene usata quando la macchina virtuale locale viene eliminata e devono essere creati nuovi dischi. Questa opzione viene ignorata se i dischi esistono già. È comunque necessario specificare un valore.
5. Selezionare l'unità di conservazione.
6. I criteri di failback vengono selezionati automaticamente.
7. Selezionare **OK** per avviare la riprotezione. Avrà inizio un processo di replica della macchina virtuale da Azure al sito locale. È possibile monitorare l'avanzamento nella scheda **Processi** . In caso di esito positivo della riprotezione, la macchina virtuale entra in uno stato protetto.

Tenere presente quanto segue:
- Per eseguire il ripristino in un percorso alternativo (quando viene eliminata la macchina virtuale locale), selezionare l'unità di conservazione e l'archivio dati configurati per il server di destinazione master. Quando si esegue il failback al sito locale, le macchine virtuali VMware nel piano di protezione di failback usano lo stesso archivio dati del server di destinazione master. Una nuova macchina virtuale viene quindi creata in vCenter.
- Per ripristinare la macchina virtuale di Azure in una macchina virtuale locale esistente, montare gli archivi dati della macchina virtuale locale con accesso in lettura/scrittura sull'host ESXi del server di destinazione master.

    ![Finestra di dialogo di riprotezione](./media/vmware-azure-reprotect/reprotectinputs.png)

- È anche possibile eseguire la riprotezione al livello di un piano di ripristino. Un gruppo di replica può essere riprotetto solo tramite un piano di ripristino. In questo caso, è necessario specificare i valori per ogni computer protetto.
- Usare lo stesso server di destinazione master per riproteggere un gruppo di replica. Se invece si usa un server di destinazione master diverso, il server non può fornire una temporizzazione comune.
- La macchina virtuale locale viene spenta durante la riprotezione, in modo da garantire la coerenza dei dati durante la replica. Non accendere la macchina virtuale al termine della riprotezione.



## <a name="common-issues"></a>Problemi comuni

- Site Recovery attualmente supporta il failback solo in un archivio dati VMFS o vSAN. Non è supportato un archivio dati NFS. A causa di questa limitazione, l'input di selezione dell'archivio dati nella schermata di riprotezione risulta vuoto per gli archivi dati NFS o mostra l'archivio dati vSAN ma ha esito negativo durante il processo. Se si prevede di eseguire il failback, è possibile creare un archivio dati VMFS in locale ed eseguire il failback in tale archivio. Questo failback determina un download completo di VMDK.
- Se si esegue l'individuazione di vCenter con utente di sola lettura e la protezione delle macchine virtuali, la protezione funziona e il failover viene eseguito. Durante la riprotezione, il failover ha esito negativo perché non è possibile individuare gli archivi dati. Se durante la riprotezione gli archivi dati non vengono elencati, è possibile che si sia verificato questo problema. Per risolverlo, è possibile aggiornare le credenziali di vCenter usando un account appropriato con le dovute autorizzazioni e quindi ripetere il processo. 
- Quando si esegue il failback di una macchina virtuale Linux e l'esecuzione è locale, si nota che il pacchetto di gestione reti viene disinstallato dal computer. Tale installazione si verifica perché, quando la macchina virtuale viene ripristinata in Azure, il pacchetto di gestione reti viene rimosso.
- Se per la configurazione di una macchina virtuale Linux viene usato un indirizzo IP statico e viene eseguito il failover in Azure, l'indirizzo IP viene acquisito da DHCP. Se il failover viene eseguito in locale, la macchina virtuale continua a usare il protocollo DHCP per acquisire l'indirizzo IP. Se necessario, accedere manualmente al computer e quindi impostare di nuovo l'indirizzo IP su un indirizzo statico. Una macchina virtuale Windows può acquisire ancora l'indirizzo IP statico.
- Se si usa l'edizione gratuita di ESXi 5.5 o di vSphere 6 Hypervisor, è possibile effettuare il failover, ma il failback non riesce. Per abilitare il failback, scegliere l'aggiornamento alla licenza di valutazione del programma.
- Se non è possibile raggiungere il server di configurazione dal server di elaborazione, usare Telnet per verificare la connettività al server di configurazione sulla porta 443. È anche possibile provare a eseguire il ping del server di configurazione dal server di elaborazione. Un server di elaborazione deve avere anche un heartbeat quando è connesso al server di configurazione.
- Non è possibile eseguire il failback da Azure a un sito locale di un server Windows Server 2008 R2 SP1 protetto come server locale fisico.
- Non è possibile eseguire il failback nelle circostanze seguenti:
    - È stata eseguita la migrazione di macchine virtuali ad Azure. [Altre informazioni](migrate-overview.md#what-do-we-mean-by-migration)
    - Una macchina virtuale è stata spostata in un altro gruppo di risorse.
    - La macchina virtuale Azure è stata eliminata.
    - È stata disabilitata la protezione della macchina virtuale.
    - La macchina virtuale è stata creata manualmente in Azure. Prima della riprotezione è necessario che la macchina virtuale sia stata inizialmente protetta in locale e che ne sia stato eseguito il failover in Azure.
    - È possibile eseguire il failback solo a un host ESXi. Non è possibile eseguire il failback di macchine virtuali VMware o server fisici a host Hyper-V, computer fisici o workstation VMware.


## <a name="next-steps"></a>Passaggi successivi

È possibile [avviare un failback](vmware-azure-failback.md) solo dopo che la macchina virtuale è entrata in uno stato protetto. Il failback arresta la macchina virtuale in Azure e avvia la macchina virtuale locale. Per l'applicazione, quindi, è necessario prevedere un breve tempo di inattività. Selezionare un'ora di failback in cui l'inattività dell'applicazione non provocherà conseguenze.


