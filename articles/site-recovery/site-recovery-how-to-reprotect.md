---
title: Abilitare la riprotezione da Azure a un sito locale | Microsoft Docs
description: "Dopo il failover delle macchine virtuali in Azure, è possibile avviare il failback per riportarle in locale. Informazioni su come abilitare la riprotezione prima di un failback."
services: site-recovery
documentationcenter: 
author: rajani-janaki-ram
manager: gauravd
editor: 
ms.assetid: 44813a48-c680-4581-a92e-cecc57cc3b1e
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 06/05/2017
ms.author: rajanaki
ms.openlocfilehash: 17a43de3faaa3a146fa9d8f43d36545d6d82b274
ms.sourcegitcommit: 651a6fa44431814a42407ef0df49ca0159db5b02
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/28/2017
---
# <a name="reprotect-from-azure-to-an-on-premises-site"></a>Abilitare la riprotezione da Azure a un sito locale



## <a name="overview"></a>Panoramica
Questo articolo descrive come eseguire la riprotezione di macchine virtuali di Azure da Azure a un sito locale. Seguire le istruzioni riportate in questo articolo quando si è pronti a eseguire il failback delle macchine virtuali VMware o dei server fisici Windows/Linux dopo aver eseguito il failover dal sito locale in Azure (come descritto in [Eseguire la replica di macchine virtuali VMware e server fisici in Azure con Azure Site Recovery](site-recovery-failover.md)).

> [!WARNING]
> Non è possibile eseguire il failback se è stata [completata la migrazione](site-recovery-migrate-to-azure.md#what-do-we-mean-by-migration), la macchina virtuale è stata spostata in un altro gruppo di risorse o la macchina virtuale di Azure è stata eliminata. Se si disabilita la protezione della macchina virtuale, non è possibile eseguire il failback. Se la macchina virtuale è stata creata in Azure, ovvero è nata nel cloud, non è possibile riproteggerla in locale. Prima della riprotezione è necessario che la macchina virtuale sia stata inizialmente protetta in locale e che ne sia stato eseguito il failover in Azure.


Al termine della riprotezione e dopo l'avvio della replica delle macchine virtuali protette, è possibile avviare un failback nelle macchine virtuali per spostarle nel sito locale.

> [!NOTE]
> È possibile solo eseguire la riprotezione e il failback in un host ESXi. Non è possibile eseguire il failback della macchina virtuale nell’host Hyper-v o nelle workstation VMware o in qualsiasi altra piattaforma di virtualizzazione.

Per inviare commenti o domande è possibile usare la parte inferiore di questo articolo oppure il [forum sui Servizi di ripristino di Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

Per una rapida panoramica, guardare il video seguente che illustra come eseguire il failover da Azure a un sito locale.
> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/VMware-to-Azure-with-ASR-Video5-Failback-from-Azure-to-On-premises/player]


## <a name="prerequisites"></a>Prerequisiti

> [!IMPORTANT]
> Durante il failover in Azure, il sito locale potrebbe non essere accessibile e pertanto il server di configurazione può essere non disponibile o venire arrestato. Durante la riprotezione e il failback, il server di configurazione locale deve essere in esecuzione e in stato OK connesso.


Quando ci si prepara per la riprotezione di macchine virtuali, eseguire o prendere in considerazione le azioni preliminari seguenti:

* Se le macchine virtuali in cui si vuole eseguire il failback sono gestite da un server vCenter, verificare di avere le [autorizzazioni necessarie](site-recovery-vmware-to-azure-classic.md) per l'individuazione di macchine virtuali nei server vCenter.

  > [!WARNING]
  > Se nella destinazione master o nella macchina virtuale locale sono presenti snapshot, la riprotezione avrà esito negativo. È possibile eliminare gli snapshot nella destinazione master prima di continuare con la protezione. Durante il processo di riprotezione, gli snapshot nella macchina virtuale vengono uniti automaticamente.

* Prima di eseguire il failback, creare due componenti aggiuntivi:

  * **Server di elaborazione**: il [server di elaborazione](site-recovery-vmware-setup-azure-ps-resource-manager.md) riceve dati dalla macchina virtuale protetta in Azure e invia dati al sito locale. È necessaria una rete a bassa latenza tra il server di elaborazione e la macchina virtuale protetta. Di conseguenza, è possibile avere un server di elaborazione locale se si usa una connessione Azure ExpressRoute o un server di elaborazione basato su Azure e una VPN.
  
  * **Server di destinazione master**: il server di destinazione riceve i dati di failback. Nel server di gestione locale creato è installato un server di destinazione master per impostazione predefinita. Tuttavia, a seconda del volume di traffico sottoposto a failback, potrebbe essere necessario creare un server di destinazione master separato per il failback.
    * [Per una macchina virtuale Linux è necessario un server di destinazione master Linux](site-recovery-how-to-install-linux-master-target.md).
    * Per una macchina virtuale Windows è necessario un server di destinazione master Windows. È possibile usare ancora il server di elaborazione locale e i computer di destinazione master.
    * La destinazione master ha altri prerequisiti elencati in [Elementi comuni da controllare dopo aver installato un server di destinazione master](site-recovery-how-to-reprotect.md#common-things-to-check-after-completing-installation-of-the-master-target-server).

> [!NOTE]
> Le macchine virtuali di un gruppo di replica devono avere tutte lo stesso tipo di sistema operativo, ovvero devono essere tutte Windows o tutte Linux. Non è attualmente previsto il supporto per la riprotezione e il failback in locale di un gruppo di replica con sistemi operativi misti. Ciò è dovuto al fatto che il server di destinazione master deve avere lo stesso sistema operativo della macchina virtuale e tutte le macchine virtuali di un gruppo di replica devono avere lo stesso server di destinazione master. 

    

* Quando si esegue un failback, è necessario un server di configurazione locale. Durante il failback, la macchina virtuale deve esistere nel database del server di configurazione. In caso contrario, il failback ha esito negativo. 

> [!IMPORTANT]
> Assicurarsi di pianificare backup regolari del server di configurazione. In caso di emergenza, ripristinare il server con lo stesso indirizzo IP in modo che il failback funzioni.

> [!WARNING]
> Un gruppo di replica deve avere macchine virtuali solo Windows o solo Linux e non una combinazione dei due sistemi operativi. Ciò è dovuto al fatto che tutte le macchine virtuali in un gruppo di replica usano lo stesso server di destinazione master, che deve essere Linux o Windows a seconda del sistema operativo delle macchine virtuali nel gruppo.

* Configurare l'impostazione `disk.EnableUUID=true` nei parametri di configurazione della macchina virtuale di destinazione master in VMware. Se questa riga non esiste, aggiungerla perché questa impostazione è necessaria per fornire un valore UUID coerente al disco della macchina virtuale (VMDK) in modo che venga installato correttamente.

* *Non è possibile usare Storage vMotion nel server di destinazione master*, perché può impedire il completamento del failback. La macchina virtuale non viene avviata perché i dischi non risultano disponibili. Per impedire questo problema, escludere i server di destinazione master dall'elenco di vMotion.

* Aggiungere una nuova unità al server di destinazione master: un'unità di conservazione. Aggiungere un nuovo disco e formattare l'unità.


### <a name="frequently-asked-questions"></a>Domande frequenti

#### <a name="why-do-i-need-a-s2s-vpn-or-an-expressroute-connection-to-replicate-data-back-to-the-on-premises-site"></a>Perché è necessaria una VPN S2S o una connessione ExpressRoute per replicare nuovamente i dati nel sito locale
La replica dal sito locale ad Azure può avere luogo tramite Internet o una connessione ExpressRoute con peering pubblico, ma la riprotezione e il failback richiedono una connessione VPN da sito a sito (S2S) per la replica dei dati. La rete deve essere configurata per consentire alle macchine virtuali con failover in Azure di raggiungere (ping) il server di configurazione locale. È possibile anche distribuire un server di elaborazione nella rete di Azure della macchina virtuale sottoposta a failover. Questo server di elaborazione deve anche poter comunicare con il server di configurazione locale.

#### <a name="when-should-i-install-a-process-server-in-azure"></a>Quando installare un server di elaborazione in Azure


Le macchine virtuali in Azure da riproteggere inviano i dati di replica a un server di elaborazione. Configurare la rete in modo che le macchine virtuali in Azure possano raggiungere il server di elaborazione.

È possibile distribuire un server di elaborazione in Azure o usare quello esistente usato per il failover. Il punto importante da considerare è la latenza per inviare i dati dalle macchine virtuali in Azure al server di elaborazione.

Se è stata configurata una connessione ExpressRoute, è possibile usare un server di elaborazione locale per inviare i dati, perché la latenza tra la macchina virtuale e il server di elaborazione è bassa.

 ![Diagramma dell'architettura per ExpressRoute](./media/site-recovery-failback-azure-to-vmware-classic/architecture.png)



Se, tuttavia, è disponibile solo una connessione VPN da sito a sito, è consigliabile distribuire il server di elaborazione in Azure.

 ![Diagramma dell'architettura per VPN](./media/site-recovery-failback-azure-to-vmware-classic/architecture2.png)


Tenere presente che la replica da Azure a locale può avere luogo solo tramite la VPN S2S o tramite il peering privato della rete ExpressRoute. Assicurarsi che sia presente una larghezza di banda sufficiente nel canale di rete.

Per informazioni sull'installazione di un server di elaborazione basato su Azure, vedere [Gestire un server di elaborazione in esecuzione in Azure](site-recovery-vmware-setup-azure-ps-resource-manager.md).

> [!TIP]
> È consigliabile usare un server di elaborazione basato su Azure durante il failback. Le prestazioni di replica sono migliori se il server di elaborazione è più vicino alla macchina virtuale di replica (computer su cui viene eseguito il failover in Azure). Durante le dimostrazioni o i modelli di verifica, tuttavia, è possibile usare il server di elaborazione locale insieme a ExpressRoute con peering privato per completare più rapidamente il modello di verifica.

> [!NOTE]
> La replica da locale a Azure può verificarsi solo tramite Internet o ExpressRoute con peering pubblico. La replica da Azure a locale può verificarsi solo tramite VPN S2S o ExpressRoute con peering privato


#### <a name="what-ports-should-i-open-on-different-components-so-that-reprotection-can-work"></a>Porte da aprire per i diversi componenti in modo che la riprotezione possa funzionare

![Porte per il failover e il failback](./media/site-recovery-failback-azure-to-vmware-classic/Failover-Failback.png)

#### <a name="which-master-target-server-should-i-use-for-reprotection"></a>Server di destinazione master da usare per la riprotezione
Un server di destinazione master locale è necessario per ricevere i dati dal server di elaborazione e quindi scriverli nel disco VMDK della macchina virtuale locale. Se si devono proteggere macchine virtuali Windows, è necessario un server di destinazione master Windows. È possibile usare ancora il server di elaborazione locale e il server di destinazione master <!-- !todo component -->. Per le macchine virtuali Linux, è necessario configurare un server di destinazione master Linux locale aggiuntivo.


Per informazioni sull'installazione di un server di destinazione master, vedere:

* [Come installare un server di destinazione master Windows](site-recovery-vmware-to-azure.md)
* [Come installare un server di destinazione master Linux](site-recovery-how-to-install-linux-master-target.md)


#### <a name="what-datastore-types-are-supported-on-the-on-premises-esxi-host-during-failback"></a>Tipi di archivio dati supportati nell'host ESXi locale durante il failback

Azure Site Recovery attualmente supporta il failback solo in un archivio dati VMFS (Virtual Machine File System) o vSAN. Un archivio dati NFS non è supportato. A causa di questa limitazione, l'input di selezione dell'archivio dati nella schermata di riprotezione risulterà vuoto in caso di database NFS o mostrerà l'archivio dati vSAN ma avrà esito negativo durante il processo. Se si prevede di eseguire il failback, è possibile creare un archivio dati VMFS in locale ed eseguire il failback in tale archivio. Questo failback determinerà un download completo di VMDK.

### <a name="common-things-to-check-after-completing-installation-of-the-master-target-server"></a>Elementi comuni da controllare dopo aver installato il server di destinazione master

* Se la macchina virtuale è presente in locale nel server vCenter, il server di destinazione master deve poter accedere al disco VMDK nella macchina virtuale locale. L'accesso è necessario per scrivere i dati replicati nei dischi della macchina virtuale. Assicurarsi che l'archivio dati della macchina virtuale locale sia montato nell'host del server di destinazione master con accesso in lettura/scrittura.

* Se la macchina virtuale non è presente in locale nel server vCenter, il servizio Site Recovery deve creare una nuova macchina virtuale durante la riprotezione. Questa macchina virtuale viene creata nell'host ESX in cui si crea il server di destinazione master. Scegliere con attenzione l'host ESX in modo che la macchina virtuale di failback venga creata nell'host desiderato.

* *Non è possibile usare Storage vMotion per il server di destinazione master*. perché può impedire il completamento del failback. La macchina virtuale non viene avviata perché i dischi non risultano disponibili.

  > [!WARNING]
  > Se una destinazione master viene sottoposta a un'attività di Storage vMotion dopo la riprotezione, i dischi delle macchine virtuali protette collegati alla destinazione master vengono migrati nella destinazione dell'attività di vMotion. Se dopo questa operazione si prova a eseguire failback, lo scollegamento del disco non riesce perché non sono stati trovati i dischi. Diventa quindi molto difficile trovare i dischi negli account di archiviazione: sarà necessario trovarli manualmente e collegarli alla macchina virtuale. Dopo aver eseguito questa operazione, è possibile riavviare la macchina virtuale locale.

* Aggiungere un'unità di conservazione al server di destinazione master Windows esistente. Aggiungere un nuovo disco e formattare l'unità. L'unità di conservazione viene usata per arrestare i punti nel tempo quando la macchina virtuale esegue di nuovo la replica nel sito locale. Di seguito sono elencati alcuni criteri relativi a un'unità di conservazione, senza i quali l'unità non sarà elencata per il server di destinazione master.

   * Il volume non viene usato per altri scopi, ad esempio per una destinazione di replica e così via.

   * Il volume non è in modalità di blocco.

   * Il volume non è un volume della cache. Nel volume non deve esistere un'installazione di destinazione master. Il volume di installazione personalizzata per il server di elaborazione e il server di destinazione master non è idoneo per un volume di conservazione. Quando il server di elaborazione e il server di destinazione master vengono installati in un volume, si tratta di un volume della cache del server di destinazione master.

   * Il file system del volume non è di tipo FAT o FAT32.

   * La capacità del volume è diversa da zero.

   * Il volume di conservazione predefinito per Windows è il volume R.

   * Il volume di conservazione predefinito per Linux è /mnt/retention/.

  > [!IMPORTANT]
  > È necessario aggiungere una nuova unità se si usa un server di elaborazione/server di configurazione esistente o un computer di scala o un server di elaborazione/server di destinazione master. La nuova unità deve soddisfare i requisiti sopra indicati. Se l'unità di conservazione non è presente, non verrà inclusa nell'elenco a discesa di selezione nel portale. Dopo aver aggiunto un'unità alla destinazione master locale, sono necessari fino a 15 minuti perché l'unità appaia nell'elenco di selezione nel portale. È anche possibile aggiornare il server di configurazione se l'unità non appare dopo 15 minuti.

* Per una macchina virtuale Linux sottoposta a failover è necessario un server di destinazione master Linux. Per una macchina virtuale Windows sottoposta a failover è necessario un server di destinazione master Windows.

* Installare gli strumenti VMware nel server di destinazione master. Senza gli strumenti VMware, non è possibile rilevare gli archivi dati nell'host ESXi del server di destinazione master.

* Abilitare il parametro `disk.EnableUUID=true` nella macchina virtuale del server di destinazione master usando le proprietà di vCenter. <!-- !todo Needs link. -->

* La destinazione master deve avere almeno un archivio dati VMFS collegato. Se non è disponibile, l'input dell'**archivio dati** nella pagina di riprotezione sarà vuoto e non sarà possibile continuare.

* Il server di destinazione master non può avere snapshot dei dischi. Se sono presenti snapshot, la riprotezione e il failback non riusciranno.

* Il server di destinazione master non può avere un controller SCSI Paravirtual. Il controller può essere solo un controller LSI Logic. Senza un controller LSI Logic, la riprotezione non riuscirà.

* In qualsiasi istanza, al server di destinazione master possono essere collegati al massimo 60 dischi. Se le macchine virtuali da riproteggere nel server di destinazione master locale hanno in totale un numero di dischi superiore a 60, la riprotezione nel server di destinazione master avrà esito negativo. Verificare di avere slot di disco sufficienti sul server di destinazione master o distribuire altri server.

<!--
### Failback policy
To replicate back to on-premises, you will need a failback policy. This policy get automatically created when you create a forward direction policy. Note that

1. This policy gets auto associated with the configuration server during creation.
2. This policy is not editable.
3. The set values of the policy are (RPO Threshold = 15 Mins, Recovery Point Retention = 24 Hours, App Consistency Snapshot Frequency = 60 Mins)
   ![](./media/site-recovery-failback-azure-to-vmware-new/failback-policy.png)

-->

## <a name="steps-to-reprotect"></a>Passaggi della riprotezione

> [!NOTE]
> Dopo l'avvio di una macchina virtuale in Azure, registrare di nuovo l'agente nel server di configurazione può richiedere fino a 15 minuti. Durante questo intervallo, la riprotezione non riesce e un messaggio di errore indica che l'agente non è installato. Attendere alcuni minuti e ripetere l'operazione di riprotezione.



1. In **Insieme di credenziali** > **Elementi replicati** fare clic con il pulsante destro del mouse sulla macchina virtuale di cui è stato eseguito il failover e scegliere **Riproteggi**. È anche possibile fare clic sulla macchina virtuale e selezionare **Riproteggi** dai pulsanti di comando.
2. Nel pannello si noti che la direzione della protezione, **Da Azure a locale**, è già selezionata.
3. In **Server di destinazione master** e **Server di elaborazione** selezionare il server di destinazione master locale e il server di elaborazione.
4. Per **Archivio dati**, selezionare l'archivio dati in cui ripristinare i dischi in locale. Questa opzione viene usata quando la macchina virtuale locale viene eliminata e devono essere creati nuovi dischi. Questa opzione viene ignorata se i dischi esistono già, ma è necessario specificare un valore.
5. Selezionare l'unità di conservazione.
6. I criteri di failback vengono selezionati automaticamente.
7. Fare clic su **OK** per avviare la riprotezione. Avrà inizio un processo di replica della macchina virtuale da Azure al sito locale. È possibile monitorare l'avanzamento nella scheda **Processi** .

Per eseguire il ripristino in un percorso alternativo (quando viene eliminata la macchina virtuale locale), selezionare l'unità di conservazione e l'archivio dati configurati per il server di destinazione master. Quando si esegue il failback al sito locale, le macchine virtuali VMware nel piano di protezione di failback usano lo stesso archivio dati del server di destinazione master. Una nuova macchina virtuale viene quindi creata in vCenter.

Per ripristinare la macchina virtuale di Azure in una macchina virtuale locale esistente, montare gli archivi dati della macchina virtuale locale con accesso in lettura/scrittura sull'host ESXi del server di destinazione master.
    ![Finestra di dialogo Riproteggi](./media/site-recovery-failback-azure-to-vmware-new/reprotectinputs.png)

È anche possibile eseguire la riprotezione al livello di un piano di ripristino. Un gruppo di replica può essere riprotetto solo tramite un piano di ripristino. In questo caso, è necessario specificare i valori per ogni computer protetto.

> [!NOTE]
> Usare lo stesso server di destinazione master per riproteggere un gruppo di replica. Se invece si usa un server di destinazione master diverso, il server non potrà fornire una temporizzazione comune.

> [!NOTE]
> La macchina virtuale locale viene spenta durante la riprotezione, in modo da garantire la coerenza dei dati durante la replica. Non accendere la macchina virtuale al termine della riprotezione.

Al termine della riprotezione, la macchina virtuale entrerà in uno stato protetto.

## <a name="next-steps"></a>Passaggi successivi

È possibile [avviare un failback](site-recovery-how-to-failback-azure-to-vmware.md#steps-to-fail-back) solo dopo che la macchina virtuale è entrata in uno stato protetto. 

Il failback arresterà la macchina virtuale in Azure e avvierà la macchina virtuale locale. Per l'applicazione, quindi, è necessario prevedere un breve tempo di inattività. Selezionare un'ora di failback in cui l'inattività dell'applicazione non provocherà conseguenze.

## <a name="common-problems"></a>Problemi frequenti

* Se è stato usato un modello per creare le macchine virtuali, assicurarsi che ogni macchina virtuale abbia un UUID univoco per i dischi. Se l'UUID della macchina virtuale locale è in conflitto con quello del server di destinazione master perché entrambi sono stati creati a partire dallo stesso modello, la riprotezione non riesce. Distribuire un altro server di destinazione master che non sia stato creato a partire dallo stesso modello.

* Se si esegue l'individuazione di vCenter con utente di sola lettura e la protezione delle macchine virtuali, la protezione funziona e il failover viene eseguito. Durante la riprotezione, failover ha esito negativo perché non è possibile individuare gli archivi dati. Se durante la riprotezione gli archivi dati non vengono elencati, è possibile che si sia verificato questo problema. Per risolverlo, è possibile aggiornare le credenziali di vCenter usando un account appropriato che abbia le autorizzazioni e quindi ripetere il processo. Per altre informazioni, vedere [Eseguire la replica di macchine virtuali VMware e server fisici in Azure con Azure Site Recovery](site-recovery-vmware-to-azure-classic.md).

* Quando si esegue il failback di una macchina virtuale Linux e l'esecuzione è locale, si nota che il pacchetto di gestione reti viene disinstallato dal computer. Tale installazione si verifica perché, quando la macchina virtuale viene ripristinata in Azure, il pacchetto di gestione reti viene rimosso.

* Se per la configurazione di una macchina virtuale Linux viene usato un indirizzo IP statico e viene eseguito il failover in Azure, l'indirizzo IP viene acquisito da DHCP. Se il failover viene eseguito in locale, la macchina virtuale continua a usare il protocollo DHCP per acquisire l'indirizzo IP. Se necessario, accedere manualmente al computer e impostare di nuovo l'indirizzo IP su un indirizzo statico. Una macchina virtuale Windows può acquisire ancora l'IP statico.

* Se si usa l'edizione gratuita di ESXi 5.5 o di vSphere 6 Hypervisor, è possibile effettuare il failover, ma non eseguire il failback. Per abilitare il failback, scegliere l'aggiornamento alla licenza di valutazione del programma.

* Se non è possibile raggiungere il server di configurazione dal server di elaborazione, usare Telnet per verificare la connettività al server di configurazione sulla porta 443. È anche possibile provare a eseguire il ping del server di configurazione dal server di elaborazione. Un server di elaborazione deve avere anche un heartbeat quando è connesso al server di configurazione.

* Se si sta tentando di eseguire il failback a un vCenter alternativo, assicurarsi che il nuovo vCenter e il server di destinazione master vengano rilevati. Come sintomo tipico, gli archivi dati non sono accessibili o visibili nella finestra di dialogo **Riproteggi**.

* Non è possibile eseguire il failback da Azure a un sito locale di un server Windows Server 2008 R2 SP1 protetto come server locale fisico.

### <a name="common-error-codes"></a>Codici errore comuni

#### <a name="error-code-95226"></a>Codice di errore 95226

*La riprotezione non è riuscita perché la macchina virtuale di Azure non è in grado di raggiungere il server di configurazione locale.*

Ciò si verifica quando 
1. La macchina virtuale di Azure non ha potuto raggiungere il server di configurazione locale e di conseguenza è stato impossibile individuarla e registrarla nel server di configurazione. 
2. Il servizio dell'applicazione InMage Scout nella macchina virtuale di Azure che deve essere in esecuzione per comunicare al server di configurazione locale potrebbe non eseguire il failover post.

Per risolvere il problema
1. È necessario assicurarsi che la rete della macchina virtuale di Azure sia configurata in modo che la macchina virtuale possa comunicare con il server di configurazione locale. A tale scopo, configurare una VPN da sito a sito nel data center locale o configurare una connessione ExpressRoute con peering privato sulla rete virtuale della macchina virtuale di Azure. 
2. Se si dispone già di una rete configurata in modo che la macchina virtuale di Azure possa comunicare con il server di configurazione locale, accedere alla macchina virtuale e verificare l'opzione 'InMage Scout Application Service'. Se si osserva che InMage Scout Application Service non è in esecuzione, avviare manualmente il servizio e verificare che il tipo di avvio sia impostato su Automatico.

### <a name="error-code-78052"></a>Codice di errore 78052
La riprotezione ha esito negativo e viene visualizzato il messaggio di errore: *Non è stato possibile completare l'abilitazione della protezione per la macchina virtuale*.

Questa situazione può verificarsi per due motivi
1. La macchina virtuale da riproteggere è Windows Server 2016. Attualmente questo sistema operativo non è supportato per il failback, ma sarà presto supportato.
2. Esiste già una macchina virtuale con lo stesso nome nel server di destinazione master.

Per risolvere questo problema è possibile selezionare un server di destinazione master diverso in un host diverso, per cui la riprotezione creerà la macchina in un host diverso, in cui i nomi non sono in conflitto. È anche possibile usare vMotion nel server di destinazione master in modo da reindirizzarlo verso un host diverso, in cui non si verificherà il conflitto di nomi. Se la macchina virtuale esistente è una macchina errata, è possibile rinominarla in modo che la nuova macchina virtuale possa essere creata nello stesso host ESXi.

### <a name="error-code-78093"></a>Codice di errore 78093

*La macchina virtuale non è in esecuzione, è bloccata o non è accessibile.*

Per la riprotezione di una macchina virtuale su cui è stato eseguito il failover di nuovo in locale, è necessario che la macchina virtuale di Azure sia in esecuzione, in modo che il servizio di mobilità sia registrato con il server di configurazione locale e possa avviare la replica mediante la comunicazione con il server di processo. Se il computer si trova su una rete non corretta o non è in esecuzione (bloccato o in arresto), il server di configurazione non riesce a raggiungere il servizio di mobilità nella macchina virtuale per avviare la riprotezione. È possibile riavviare la macchina virtuale in modo che questa riesca a riavviare la comunicazione locale. Riavviare il processo di riprotezione dopo l'avvio della macchina virtuale di Azure

### <a name="error-code-8061"></a>Codice di errore 8061

*L'archivio dati non è accessibile dall'host ESXi.*

Fare riferimento ai [prerequisiti di destinazione master](site-recovery-how-to-reprotect.md#common-things-to-check-after-completing-installation-of-the-master-target-server) e agli [archivi dati di supporto](site-recovery-how-to-reprotect.md#what-datastore-types-are-supported-on-the-on-premises-esxi-host-during-failback) per il failback

