---
title: Informazioni sull'integrità delle macchine virtuali di Azure | Microsoft Docs
description: Questo articolo descrive come comprendere l'integrità delle macchine virtuali e dei sistemi operativi sottostanti con Monitoraggio di Azure per le macchine virtuali.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 11/14/2019
ms.openlocfilehash: 5fd5295e52f0fef5e1432fdb2f81d2ba0e1717e8
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/15/2019
ms.locfileid: "74109772"
---
# <a name="understand-the-health-of-your-azure-virtual-machines"></a>Informazioni sull'integrità delle macchine virtuali di Azure

Azure include servizi per ruoli specifici o attività nell'area di monitoraggio, ma non fornisce prospettive di integrità approfondite dei sistemi operativi ospitati in macchine virtuali di Azure (VM). Sebbene sia possibile usare monitoraggio di Azure per condizioni diverse, non è progettato per modellare e rappresentare l'integrità dei componenti di base o l'integrità complessiva delle macchine virtuali.

Utilizzando Monitoraggio di Azure per le macchine virtuali integrità, è possibile monitorare attivamente la disponibilità e le prestazioni di un sistema operativo guest Windows o Linux. La funzionalità di integrità utilizza un modello che rappresenta i componenti chiave e le relative relazioni, fornisce criteri che specificano come misurare l'integrità dei componenti e invia un avviso quando rileva una condizione di non integrità.

La visualizzazione dello stato di integrità generale di una macchina virtuale di Azure e del sistema operativo sottostante può essere osservata da due prospettive: direttamente da una macchina virtuale o in tutte le macchine virtuali in un gruppo di risorse da monitoraggio di Azure.

Questo articolo illustra come valutare, analizzare e risolvere rapidamente i problemi di integrità quando vengono rilevati dalla funzionalità di integrità Monitoraggio di Azure per le macchine virtuali.

Per informazioni sulla configurazione di Monitoraggio di Azure per le macchine virtuali, vedere [Enable Azure Monitor for VMs](vminsights-enable-overview.md) (Abilitare Monitoraggio di Azure per le macchine virtuali).

>[!NOTE]
>Microsoft [ha recentemente annunciato le modifiche apportate](https://azure.microsoft.com/updates/updates-to-azure-monitor-for-virtual-machines-preview-before-general-availability-release/
) alla funzionalità di integrità in base ai commenti ricevuti dai clienti dell'anteprima pubblica. Dato il numero di modifiche che verranno apportate, si smette di offrire la funzionalità di integrità per i nuovi clienti. I clienti esistenti possono continuare a utilizzare la funzionalità di integrità. Per altri dettagli, vedere le [domande frequenti sulla disponibilità generale](vminsights-ga-release-faq.md). 

## <a name="monitoring-configuration-details"></a>Dettagli di configurazione di monitoraggio

Questa sezione descrive i criteri di integrità predefiniti per monitorare le VM Windows e Linux di Azure. Tutti i criteri di integrità sono preconfigurati per l'invio di un avviso quando rilevano una condizione di tipo non integro.

| Nome monitoraggio | Frequenza (min) | Lookback durata (min) | Operatore | Soglia | Avviso per stato | Gravità | Categoria carico di lavoro | 
|--------------|-----------|----------|----------|-----------|----------------|----------|-------------------|
| Disco logico online | 5 | 15 | <> | 1 (true) | Critico | Sev1 | Linux | 
| Spazio disponibile su disco logico | 5 | 15 | < | 200 MB (avviso)<br> 100 MB (critico) | Avviso | Sev1<br> Sev2 | Linux | 
| % inode disponibili su disco logico | 5 | 15 | < | 5% | Critico | Sev1 | Linux | 
| % spazio disponibile su disco logico | 5 | 15 | < | 5% | Critico | Sev1 | Linux | 
| Stato scheda di rete | 5 | 15 | <> | 1 (true) | Avviso | Sev2 | Linux | 
| Memoria disponibile in megabyte del sistema operativo | 5 | 10 | < | 2,5 MB | Critico | Sev1 | Linux | 
| Media letture disco/sec | 5 | 25 | > | 0,05 s | Critico | Sev1 | Linux | 
| Media sec/trasferimento disco | 5 | 25 | > | 0,05 s | Critico | Sev1 | Linux | 
| Media scritture disco/sec | 5 | 25 | > | 0,05 s | Critico | Sev1 | Linux | 
| Stato del disco | 5 | 25 | <> | 1 (true) | Critico | Sev1 | Linux | 
| Percentuale tempo processore totale sistema operativo | 5 | 10 | >= | 95% | Critico | Sev1 | Linux | 
| Percentuale di utilizzo di CPU totale | 5 | 10 | >= | 95% | Critico | Sev1 | Windows | 
| Errore o danneggiamento del file system | 60 | 60 | <> | 4 | Critico | Sev1 | Windows | 
| Media lettura disco logico/secondi | 1 | 15 | > | 0.04 s | Avviso | Sev2 | Windows | 
| Media trasferimento disco logico/secondi | 1 | 15 | > | 0.04 s | Avviso | Sev2 | Windows | 
| Media di secondi per scrittura su disco logico (disco logico) | 1 | 15 | > | 0.04 s | Avviso | Sev2 | Windows | 
| Lunghezza corrente coda su disco (disco logico) | 5 | 60 | >= | 32 | Avviso | Sev2 | Windows | 
| Spazio disponibile su disco logico (MB) | 15 | 60 | > | 500 MB di avviso<br> 300 MB critico | Critico | Sev1<br> Sev2 | Windows | 
| Spazio disponibile su disco logico (%) | 15 | 60 | > | avviso 10%<br> critico 5% | Critico | Sev1<br> Sev2 | Windows |
| Percentuale tempo di inattività del disco logico | 15 | 360 | <= | 20% | Avviso | Sev2 | Windows | 
| Percentuale di larghezza di banda utilizzata per lettura | 5 | 60 | >= | 60% | Avviso | Sev2 | Windows | 
| Percentuale di larghezza di banda utilizzata in totale | 5 | 60 | >= | 75% | Avviso | Sev2 | Windows | 
| Percentuale di larghezza di banda utilizzata per scrittura | 5 | 60 | >= | 60% | Avviso | Sev2 | Windows | 
| Integrità del servizio di client DHCP | 5 | 12 | <> | 4 (in esecuzione) | Critico | Sev1 | Windows | 
| Integrità del servizio di client DNS | 5 | 12 | <> | 4 (in esecuzione) | Critico | Sev1 | Windows | 
| Integrità del servizio Registro eventi di Windows | 5 | 12 | <> | 4 (in esecuzione) | Critico | Sev1 | Windows | 
| Integrità del servizio Windows Firewall | 5 | 12 | <> | 4 (in esecuzione) | Critico | Sev1 | Windows | 
| Integrità del servizio RPC | 5 | 12 | <> | 4 (in esecuzione) | Critico | Sev1 | Windows | 
| Integrità del servizio di server | 5 | 12 | <> | 4 (in esecuzione) | Critico | Sev1 | Windows | 
| Integrità del servizio Gestione remota Windows | 5 | 12 | <> | 4 (in esecuzione) | Critico | Sev1 | Windows | 
| Megabyte disponibili per la memoria | 5 | 10 | < | 100 MB | Critico | Sev1 | Windows | 
| Voci della tabella delle pagine di sistema gratuite | 5 | 10 | <= | 5000 | Critico | Sev1 | Windows | 
| Pagine di memoria al secondo | 5 | 10 | >= | 5000/s | Avviso | Sev1 | Windows | 
| Percentuale di memoria in uso di cui è stato eseguito il commit | 5 | 10 | > | 80% | Critico | Sev1 | Windows | 
| Media trasferimento disco/secondi | 1 | 15 | > | 0.04 s | Avviso | Sev2 | Windows | 
| Media di secondi per scrittura su disco | 1 | 15 | > | 0.04 s | Avviso | Sev2 | Windows | 
| Lunghezza corrente coda su disco | 5 | 60 | >= | 32 | Avviso | Sev2 | Windows | 
| Percentuale tempo di inattività del disco | 5 | 60 | >= | 20% | Avviso | Sev2 | Windows | 

>[!NOTE]
>La durata lookback rappresenta la frequenza con cui la finestra di ricerca controlla i valori delle metriche, ad esempio negli ultimi cinque minuti.  

>[!NOTE]
>Frequency rappresenta la frequenza con cui l'avviso della metrica controlla se le condizioni vengono soddisfatte, ad esempio ogni minuto.  Si tratta della frequenza con cui viene eseguito il criterio di integrità e lookback è la durata della valutazione del criterio di integrità. Il criterio di integrità, ad esempio, sta valutando se la condizione di **utilizzo della CPU** è superiore al 95% con una frequenza di 5 minuti e rimane maggiore del 95% per 15 minuti (3 cicli di valutazione consecutivi), lo stato viene aggiornato alla gravità critica se non è già stato fatto.

## <a name="sign-in-to-the-azure-portal"></a>Accedere al portale di Azure

Per accedere, passare alla [portale di Azure](https://portal.azure.com).

## <a name="introduction-to-azure-monitor-for-vms-health"></a>Introduzione all'integrità Monitoraggio di Azure per le macchine virtuali

Prima di usare la funzionalità di integrità per una singola macchina virtuale o un gruppo di macchine virtuali, è importante comprendere come vengono presentate le informazioni e come vengono rappresentate le visualizzazioni.

### <a name="view-health-directly-from-a-vm"></a>Visualizzare lo stato di integrità direttamente da una macchina virtuale

Per visualizzare l'integrità di una macchina virtuale di Azure, selezionare **Insights (anteprima)** nel riquadro sinistro della macchina virtuale. Nella pagina VM Insights la scheda **integrità** è aperta per impostazione predefinita e Mostra la visualizzazione dell'integrità della macchina virtuale.

![Panoramica dell'integrità di Monitoraggio di Azure per macchine virtuali per una macchina virutale di Azure selezionata](./media/vminsights-health/vminsights-directvm-health-01.png)

Nella sezione relativa all' **integrità della macchina virtuale Guest** la tabella mostra il rollup dello stato dei componenti delle prestazioni monitorati dai criteri di integrità per la macchina virtuale e il numero totale di avvisi di integrità della macchina virtuale generati da componenti non integri. Questi componenti includono **CPU**, **memoria**, **disco**e **rete**. Espandere la freccia di espansione accanto a stato VM guest per visualizzare l'integrità dei relativi componenti.

![Monitoraggio di Azure per le macchine virtuali lo stato di integrità del componente di una macchina virtuale di Azure selezionata](./media/vminsights-health/vminsights-directvm-health-02.png)

Selezionando lo stato accanto al componente si apre l'esperienza di diagnostica dell'integrità nel contesto del componente selezionato. Mostra la composizione dello stato di tale componente, descrivendo i criteri di integrità usati per calcolare l'integrità. Per altre informazioni, vedere [diagnostica dell'integrità e uso dei criteri di integrità](#health-diagnostics). Per ulteriori informazioni sugli avvisi, vedere [avvisi](#alerts).

La tabella seguente descrive gli stati di integrità definiti per una macchina virtuale:

|Icona |Stato di integrità |Significato |
|-----|-------------|---------------|
| |Healthy |La macchina virtuale rientra nelle condizioni di integrità definite. Questo stato indica che non sono stati rilevati problemi e che la macchina virtuale funziona normalmente. Con un monitoraggio rollup padre viene eseguito il rollup dello stato e viene riflesso lo stato migliore o peggiore del case figlio.|
| |Critico |Lo stato non rientra nella condizione di integrità definita, a indicare che sono stati rilevati uno o più problemi critici. È necessario risolvere questi problemi per ripristinare le funzionalità normali. Con un monitor di rollup padre, lo stato di integrità viene rollup e riflette lo stato migliore o peggiore del case figlio.|
| |Avviso |Lo stato è compreso tra due soglie per la condizione di integrità definita, dove uno indica uno stato di avviso e l'altro indica uno stato critico (è possibile configurare tre soglie di stato di integrità) o quando un problema non critico può causare problemi critici se irrisolti. Con un monitoraggio rollup padre, se uno o più elementi figlio sono in uno stato di avviso, l'elemento padre rifletterà uno stato di avviso. Se un elemento figlio si trova in uno stato critico e un altro figlio in uno stato di avviso, il rollup padre visualizzerà lo stato di integrità critico.|
| |Sconosciuto |Non è possibile calcolare lo stato per diversi motivi. Nella sezione seguente vengono forniti dettagli aggiuntivi e possibili soluzioni. |

Uno stato di integrità sconosciuto può essere causato dai seguenti problemi:

- L'agente è stato riconfigurato e non segnala più l'area di lavoro specificata quando è stato abilitato Monitoraggio di Azure per le macchine virtuali. Per configurare l'agente per la segnalazione all'area di lavoro, vedere l' [aggiunta o la rimozione di un'area di lavoro](../platform/agent-manage.md#adding-or-removing-a-workspace).
- La macchina virtuale è stata eliminata.
- L'area di lavoro associata a Monitoraggio di Azure per le macchine virtuali è stata eliminata. È possibile ripristinare l'area di lavoro se si dispone di vantaggi del supporto Premier. Passare a [Premier](https://premier.microsoft.com/) e aprire una richiesta di supporto.
- Le dipendenze della soluzione sono state eliminate. Per abilitare nuovamente le soluzioni ServiceMap e InfrastructureInsights nell'area di lavoro Log Analytics, reinstallare la soluzione ServiceMap usando il [modello di Azure Resource Manager](vminsights-enable-at-scale-powershell.md#install-the-servicemap-solution). Per reinstallare la soluzione InfastructureInsights, inviare un messaggio di posta elettronica vminsights@microsoft.com. 
- La macchina virtuale è stata arrestata.
- Il servizio VM di Azure non è disponibile oppure è in corso l'esecuzione della manutenzione.
- È stato raggiunto il [limite di conservazione o i dati giornalieri](../platform/manage-cost-storage.md) dell'area di lavoro.

Selezionare **Visualizza diagnostica integrità** per aprire una pagina che Mostra tutti i componenti di una macchina virtuale, i criteri di integrità associati, le modifiche di stato e altri problemi rilevati dal monitoraggio dei componenti correlati alla VM.

Per altre informazioni, vedere [Diagnostica integrità](#health-diagnostics).

Nella sezione relativa all' **integrità** , una tabella mostra il rollup dello stato dei componenti delle prestazioni monitorati dai criteri di integrità. Questi componenti includono **CPU**, **memoria**, **disco**e **rete**. Selezionando un componente viene visualizzata una pagina in cui sono elencati tutti i criteri di monitoraggio e lo stato di integrità del componente.

Quando si accede allo stato di integrità da una macchina virtuale di Azure che esegue Windows, lo stato di integrità dei primi cinque servizi principali di Windows viene visualizzato in **integrità servizi di base**. Selezionando uno dei servizi verrà aperta una pagina che elenca il monitoraggio dei criteri di integrità per il componente insieme al relativo stato di integrità.

Selezionando il nome dei criteri di integrità verrà aperto il riquadro delle proprietà. In questo riquadro è possibile esaminare i dettagli di configurazione, ad esempio se i criteri di integrità hanno un avviso corrispondente di monitoraggio di Azure.

Per altre informazioni, vedere [diagnostica dell'integrità e uso dei criteri di integrità](#health-diagnostics).

### <a name="aggregate-vm-perspective"></a>Prospettiva aggregata VM

Per visualizzare la raccolta di integrità per tutte le macchine virtuali in un gruppo di risorse, selezionare **monitoraggio di Azure** dall'elenco di navigazione nel portale, quindi selezionare **macchine virtuali (anteprima)** .

![Visualizzazione del monitoraggio di Informazioni dettagliate macchina virtuale da Monitoraggio di Azure](./media/vminsights-health/vminsights-aggregate-health.png)

Negli elenchi a discesa **sottoscrizione** e **gruppo di risorse** Selezionare il gruppo di risorse appropriato che include le macchine virtuali correlate al gruppo, per visualizzare lo stato di integrità segnalato. La selezione si applica solo alla funzionalità di integrità e non viene riportata nelle schede **prestazioni** o **mappa** .

La scheda **integrità** fornisce le seguenti informazioni:

* Il numero di macchine virtuali in uno stato critico o non integro, rispetto a quante sono integre o non inviano dati (noto come stato sconosciuto).
* Quali e quante macchine virtuali per sistema operativo segnalano uno stato di tipo non integro.
* Il numero di macchine virtuali non integre a causa di un problema rilevato con un processore, un disco, una memoria o una scheda di rete, classificati in base allo stato di integrità.
* Il numero di macchine virtuali non integre a causa di un problema rilevato con un servizio del sistema operativo core, categorizzato in base allo stato di integrità.

Nella scheda **integrità** è possibile identificare i problemi critici rilevati dal criterio di integrità monitoraggio della macchina virtuale ed esaminare i dettagli dell'avviso e gli articoli della Knowledge base associati. Questi articoli possono contribuire alla diagnosi e alla correzione dei problemi. Selezionare uno dei valori di gravità per aprire la pagina [Tutti gli avvisi](../../azure-monitor/platform/alerts-overview.md#all-alerts-page) filtrata in base al livello di gravità specificato.

L'elenco **Distribuzione delle macchine virtuali - Sistema operativo** mostra le macchine virtuali elencate in base alla versione di Windows o alla distribuzione di Linux, insieme alla relativa versione. In ogni categoria del sistema operativo, le macchine virtuali vengono suddivise ulteriormente in base all'integrità della macchina virtuale.

![Prospettiva della distribuzione delle macchine virtuali in Informazioni dettagliate macchina virtuale](./media/vminsights-health/vminsights-vmdistribution-by-os.png)

Selezionare una colonna che includa **numero di VM**, **critico**, **avviso**, **integro**o **sconosciuto**. Visualizzare l'elenco dei risultati filtrati nella pagina **macchine virtuali** che corrispondono alla colonna selezionata.

Ad esempio, per esaminare tutte le macchine virtuali che eseguono Red Hat Enterprise Linux versione 7,5, selezionare il valore del **numero di VM** per quel sistema operativo e elencare le macchine virtuali corrispondenti a tale filtro e al relativo stato di integrità corrente.

![Esempio di rollup delle macchine virtuali Red Hat Linux](./media/vminsights-health/vminsights-rollup-vm-rehl-01.png)

Si fa clic sulla casella di controllo **Mostra integrità** e viene restituito lo stato di integrità per i risultati filtrati nella tabella.  

![Stato di integrità di esempio delle VM Red Hat Linux](./media/vminsights-health/vminsights-rollup-vm-rehl-02.png)

Per uno degli elementi dell'elenco, è possibile fare clic sullo stato di integrità corrispondente per avviare la diagnostica dell'integrità, che Mostra come viene valutata l'integrità per la macchina virtuale selezionata. 

Nella pagina **macchine virtuali** , se si seleziona il nome di una macchina virtuale sotto la colonna **nome VM**, si viene indirizzati alla pagina dell' **istanza di VM** . Questa pagina contiene informazioni dettagliate sugli avvisi e i problemi relativi ai criteri di integrità che interessano la macchina virtuale selezionata. Filtrare i dettagli sullo stato di integrità selezionando l'icona **dello stato di integrità** nell'angolo superiore sinistro della pagina per visualizzare i componenti non integri. È anche possibile visualizzare gli avvisi di integrità della macchina virtuale generati da un componente non integro categorizzato in base alla gravità dell'avviso.

Dalla visualizzazione **elenco VM** selezionare il nome di una macchina virtuale per aprire la pagina di **integrità** per tale macchina virtuale, in modo analogo a quando si seleziona **Insights (anteprima)** direttamente dalla macchina virtuale.

![Informazioni dettagliate macchina virtuale di una macchina virtuale di Azure selezionata](./media/vminsights-health/vminsights-directvm-health.png)

La pagina **macchine virtuali (anteprima) nella pagina Monitoraggio di Azure** Mostra lo stato di integrità rollup per la macchina virtuale e gli avvisi. Questo stato di integrità è categorizzato in base alla gravità, che rappresenta gli avvisi di integrità della macchina virtuale generati quando lo stato di integrità è stato modificato da integro a non integro, in base ai criteri. Selezionando **VM in condizione critica** viene visualizzata una pagina con un elenco di una o più macchine virtuali in uno stato di integrità critico.

Selezionando lo stato di integrità di una delle VM, viene visualizzata la visualizzazione **diagnostica dell'integrità** della macchina virtuale. In questa visualizzazione è possibile determinare i criteri di integrità che riflettono un problema relativo allo stato di integrità. Quando viene visualizzata la pagina **diagnostica dell'integrità** , vengono visualizzati tutti i componenti della macchina virtuale e i criteri di integrità associati con lo stato di integrità corrente.

Per altre informazioni, vedere [Diagnostica integrità](#health-diagnostics).

Selezionando **Visualizza tutti i criteri di integrità** si apre una pagina con un elenco di tutti i criteri di integrità disponibili con questa funzionalità. Le informazioni possono essere ulteriormente filtrate in base alle opzioni seguenti:

* **Digitare**. Esistono tre tipi di criteri di integrità per valutare le condizioni ed eseguire il rollup dello stato di integrità generale di una macchina virtuale monitorata:
    - **Unità**. Misura alcuni aspetti di una macchina virtuale. Questo tipo di criteri di integrità potrebbe controllare un contatore delle prestazioni per determinare le prestazioni del componente, eseguire uno script per eseguire una transazione sintetica o controllare un evento che indica un errore. Per impostazione predefinita, il filtro è impostato su unità.
    - **Dipendenza**. Fornisce un rollup dello stato tra entità diverse. Questo criterio di integrità consente all'integrità di un'entità di dipendere dall'integrità di un altro tipo di entità su cui si basa per eseguire correttamente l'operazione.
    - **Aggregazione**. Fornisce uno stato di integrità combinato di criteri di integrità simili. Il criterio di integrità di unità e dipendenza viene in genere configurato in base a un criterio di integrità aggregato. Oltre a fornire una migliore organizzazione generale dei diversi criteri di integrità destinati a un'entità, il criterio di integrità aggregato fornisce uno stato di integrità univoco per categorie distinte di entità.

* **Categoria**. Tipo di criteri di integrità utilizzato per raggruppare criteri simili per la creazione di report. Queste categorie sono **disponibilità** e **prestazioni**.

Per visualizzare le istanze non integre, selezionare un valore nella colonna **componente non integro** . In questa pagina, una tabella elenca i componenti che si trovano in uno stato di integrità critico.

## <a name="health-diagnostics"></a>Diagnostica integrità

La pagina **diagnostica dell'integrità** consente di visualizzare il modello di integrità di una macchina virtuale. Questa pagina elenca tutti i componenti della macchina virtuale, i criteri di integrità associati, le modifiche di stato e altri problemi significativi identificati dai componenti monitorati correlati alla VM.

![Esempio di pagina Diagnostica integrità per una macchina virtuale](./media/vminsights-health/health-diagnostics-page-01.png)

Avviare la diagnostica dell'integrità usando i metodi seguenti:

* Per stato di integrità rollup per tutte le macchine virtuali dal punto di vista della macchina virtuale aggregato in monitoraggio di Azure:

    1. Nella pagina **integrità** selezionare l'icona relativa allo stato di integrità **critico**, **avviso**, **integro**o **sconosciuto** nella sezione **integrità VM guest**.
    2. Passare alla pagina in cui sono elencate tutte le macchine virtuali corrispondenti alla categoria filtrata.
    3. Selezionare il valore nella colonna **stato di integrità** per aprire la diagnostica dell'integrità nell'ambito della macchina virtuale.

* Dal sistema operativo dal punto di vista della VM aggregato in monitoraggio di Azure. In **Distribuzione delle macchine virtuali** selezionare uno dei valori della colonna per aprire la pagina **Macchine virtuali** e visualizzare un elenco nella tabella corrispondente alla categoria filtrata. Selezionando il valore nella colonna **stato integrità** si apre diagnostica di integrità per la macchina virtuale selezionata.
 
* Dalla macchina virtuale guest nella scheda Monitoraggio di Azure per le macchine virtuali **Health** selezionare **Visualizza diagnostica dell'integrità**.

Diagnostica integrità organizza le informazioni di integrità in due categorie: disponibilità e prestazioni.
 
Tutti i criteri di integrità definiti per un componente, ad esempio disco logico, CPU e così via, possono essere visualizzati senza filtrare le due categorie. Queste visualizzazioni possono trovarsi in una vista di tutti i criteri oppure filtrando i risultati in base a ogni categoria quando si seleziona **disponibilità** o **prestazioni**.

Inoltre, la categoria criteri può essere visualizzata accanto alla colonna **criteri di integrità** . Se i criteri non corrispondono alla categoria selezionata, nella colonna **criteri di integrità** verrà visualizzato un messaggio che informa che **non sono disponibili criteri di integrità per la categoria selezionata** .

Lo stato di un criterio di integrità è definito da uno dei quattro tipi: **critico**, **avviso**, **integro**e **sconosciuto**. Le prime tre sono configurabili, vale a dire che è possibile modificare i valori di soglia dei monitoraggi direttamente nel riquadro configurazione **criteri di integrità** . È anche possibile usare l' [operazione di monitoraggio aggiornamento](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/monitors/update)API REST di monitoraggio di Azure. **Unknown** non è configurabile ed è riservato a scenari specifici.

La pagina **diagnostica dell'integrità** include tre sezioni principali:

* Modello componente
* Criteri di integrità
* Modifiche di stato

![Sezioni della pagina Diagnostica integrità](./media/vminsights-health/health-diagnostics-page-02.png)

### <a name="component-model"></a>Modello componente

La colonna più a sinistra nella pagina di **diagnostica dell'integrità** è il **modello di componente**. Tutti i componenti associati alla macchina virtuale vengono visualizzati in questa colonna insieme allo stato di integrità corrente.

Nell'esempio seguente i componenti individuati sono **disco**, **disco logico**, **processore**, **memoria**e **sistema operativo**. In questa colonna vengono individuate e visualizzate più istanze di questi componenti.

Ad esempio, la figura seguente mostra che la macchina virtuale dispone di due istanze di dischi logici, **C:** e **D:** , che sono in uno stato integro:

![Esempio di modello componente presentato in Diagnostica integrità](./media/vminsights-health/health-diagnostics-page-component.png)

### <a name="health-criteria"></a>Criteri di integrità

La colonna centrale della pagina di diagnostica dell'integrità corrisponde **ai criteri di integrità**. Il modello di integrità definito per la macchina virtuale è visualizzato in un albero gerarchico. Il modello di integrità per una macchina virtuale è costituito da unità e criteri di integrità aggregati.

![Esempio di criterio di integrità presentato in Diagnostica integrità](./media/vminsights-health/health-diagnostics-page-healthcriteria.png)

Un criterio di integrità misura l'integrità di un'istanza monitorata, che può essere un valore soglia, lo stato di un'entità e così via. Un criterio di integrità può essere costituito da due o tre soglie di stato di integrità configurabili, come descritto in precedenza. In qualsiasi momento, il criterio di integrità può trovarsi solo in uno dei relativi stati potenziali.

Il modello di integrità definisce i criteri che determinano l'integrità della destinazione complessiva e dei componenti della destinazione. La gerarchia dei criteri viene visualizzata nella sezione **criteri di integrità** della pagina **diagnostica integrità** .

I criteri di rollup dello stato fanno parte della configurazione dei criteri di integrità aggregati (il valore predefinito è impostato su **Worst-of**). È possibile trovare un set predefinito di criteri di integrità in esecuzione come parte di questa funzionalità nella sezione [monitoraggio dei dettagli della configurazione](#monitoring-configuration-details) di questo articolo.

È anche possibile usare l' [elenco di istanze di monitoraggio](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/monitorinstances/listbyresource) dell'API REST di monitoraggio di Azure per risorsa per ottenere un elenco di tutti i criteri di integrità. Questo criterio include i dettagli di configurazione in esecuzione sulla risorsa VM di Azure.

Per il tipo di criteri di integrità **unità** è possibile modificare la configurazione selezionando il collegamento con i puntini di sospensione sul lato destro. Selezionare **Mostra dettagli** per aprire il riquadro di configurazione.

![Configurazione di un esempio di criteri di integrità](./media/vminsights-health/health-diagnostics-vm-example-02.png)

Nel riquadro Configurazione per i criteri di integrità selezionati, se si usa l'esempio **media di secondi per scrittura su disco**, la soglia può essere configurata con un valore numerico diverso. Si tratta di un monitoraggio a due Stati, ovvero può essere modificato solo da **integro** ad **avviso**.

Altri criteri di integrità talvolta utilizzano tre stati, in cui è possibile configurare il valore per le soglie di avviso e di stato di integrità critiche. È anche possibile modificare una soglia usando la [configurazione di monitoraggio](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/monitors/update)API REST di monitoraggio di Azure.

>[!NOTE]
>Applicare le modifiche di configurazione ai criteri di integrità a un'istanza le applica a tutte le istanze monitorate. Se ad esempio si seleziona **disco-1 D:** e quindi si modifica la soglia **media di secondi per scrittura su disco** , la modifica viene applicata a tutte le istanze individuate e monitorate nella macchina virtuale.


![Configurazione di criteri di integrità per un esempio di monitoraggio unità](./media/vminsights-health/health-diagnostics-criteria-config-01.png)

Per ulteriori informazioni sui criteri di integrità, sono stati inclusi articoli della Knowledge base che consentono di identificare i problemi, le cause e le risoluzioni. Per visualizzare l'articolo della Knowledge Base correlato, selezionare **Visualizza informazioni** nella pagina.

Per esaminare tutti gli articoli della Knowledge base inclusi in Monitoraggio di Azure per le macchine virtuali Health, vedere la documentazione della Knowledge base sull' [integrità di monitoraggio di Azure](https://docs.microsoft.com/azure/monitoring/infrastructure-health/).

### <a name="state-changes"></a>Modifiche stato

La colonna all'estrema destra della pagina di **diagnostica dell'integrità** è **stata modificata**. In questa colonna sono elencate tutte le modifiche di stato associate ai criteri di integrità selezionati nella sezione **criteri di integrità** o la modifica dello stato della VM se è stata selezionata una macchina virtuale dalla colonna **modello di componente** o criteri di **integrità** della tabella.

![Esempio di modifiche di stato presentato in Diagnostica integrità](./media/vminsights-health/health-diagnostics-page-statechanges.png)

Nella sezione seguente vengono illustrati lo stato dei criteri di integrità e l'ora associata. Queste informazioni mostrano lo stato più recente nella parte superiore della colonna.

### <a name="association-of-component-model-health-criteria-and-state-changes-columns"></a>Associazione delle colonne del modello di componente, dei criteri di integrità e delle modifiche di stato

Le tre colonne sono correlate tra loro. Quando si seleziona un'istanza nella colonna **modello di componente** , la colonna **criteri di integrità** viene filtrata in modo tale da visualizzare il componente. La colonna modifiche di **stato** viene aggiornata in base ai criteri di integrità selezionati.

![Esempio di selezione dell'istanza monitorata e dei risultati](./media/vminsights-health/health-diagnostics-vm-example-01.png)

Se ad esempio si seleziona *disco-1 d:* nell'elenco in modello di **componente**, **criteri di integrità** filtra su *disco-1D:* e **modifiche stato** Mostra la modifica dello stato in base alla disponibilità del *disco-1 d:* .

Per visualizzare uno stato di integrità aggiornato, è possibile aggiornare la pagina di diagnostica dell'integrità selezionando il collegamento **Aggiorna** . Se lo stato di integrità del criterio di integrità viene aggiornato in base all'intervallo di polling predefinito, questa attività consente di evitare l'attesa e riflette lo stato di integrità più recente. Lo **stato dei criteri di integrità** è un filtro che consente di definire l'ambito dei risultati in base allo stato di integrità selezionato: integro, avviso, critico, sconosciuto e tutti. L'ora dell' **Ultimo aggiornamento** nell'angolo superiore destro rappresenta l'ora dell'ultimo aggiornamento della pagina di diagnostica dell'integrità.

## <a name="alerts"></a>Alerts

Monitoraggio di Azure per le macchine virtuali integrità si integra con gli [avvisi di Azure](../../azure-monitor/platform/alerts-overview.md). Genera un avviso quando i criteri predefiniti, quando vengono rilevati, cambiano da uno stato integro a uno stato non integro. Gli avvisi sono suddivisi in categorie per gravità, da gravità 0 a SEV 4, con gravità 0 come livello più alto.

Gli avvisi non sono associati a un gruppo di azioni per notificare l'attivazione dell'avviso. Un utente con ruolo proprietario nell'ambito della sottoscrizione deve configurare le notifiche attenendosi alla procedura descritta nella sezione [configurare gli avvisi](#configure-alerts) .

Il numero totale di avvisi di integrità della macchina virtuale classificati in base alla gravità è disponibile nel dashboard di **integrità** nella sezione **avvisi** . Quando si seleziona il numero totale di avvisi o il numero corrispondente a un livello di gravità, si apre la pagina **Avvisi** in cui sono elencati tutti gli avvisi corrispondenti alla selezione.

Se, ad esempio, si seleziona la riga corrispondente a **SEV Level 1**, verrà visualizzata la vista seguente:

![Esempio di tutti gli avvisi con livello di gravità 1](./media/vminsights-health/vminsights-sev1-alerts-01.png)

L'ambito della pagina **tutti gli avvisi** non consente di visualizzare solo gli avvisi che corrispondono alla selezione. Viene anche filtrato in base al **tipo di risorsa** per visualizzare solo gli avvisi di integrità generati da una risorsa VM. Questo formato viene riflesso nell'elenco degli avvisi, sotto la **risorsa di destinazione**della colonna, dove Mostra la VM di Azure l'avviso generato quando è stata soddisfatta una condizione di tipo non integro.

Gli avvisi di altri tipi di risorse o servizi non devono essere inclusi in questa vista. Questi avvisi includono gli avvisi del log, basati sulle query di log o sugli avvisi delle metriche che normalmente verrebbero visualizzati dalla pagina monitoraggio predefinito di [tutti gli avvisi](../../azure-monitor/platform/alerts-overview.md#all-alerts-page) di Azure.

È possibile filtrare questa visualizzazione selezionando i valori nei menu a discesa nella parte superiore della pagina.

|Colonna |DESCRIZIONE |
|-------|------------|
|sottoscrizione |Selezionare una sottoscrizione di Azure. Sono inclusi nella visualizzazione solo gli avvisi della sottoscrizione selezionata. |
|gruppo di risorse |Selezionare un singolo gruppo di risorse. Sono inclusi nella visualizzazione solo gli avvisi con destinazioni nel gruppo di risorse selezionato. |
|Tipo di risorsa |Selezionare uno o più tipi di risorsa. Per impostazione predefinita, in questa visualizzazione sono selezionati e inclusi solo gli avvisi della **Macchina virtuale** di destinazione. Questa colonna risulta disponibile solo dopo che è stato specificato un gruppo di risorse. |
|Risorsa |Selezionare una risorsa. Nella visualizzazione vengono inclusi solo gli avvisi con tale risorsa definita come destinazione. Questa colonna è disponibile solo dopo che è stato specificato un tipo di risorsa. |
|Gravità |Selezionare una gravità degli avvisi oppure **Tutti** per includere gli avvisi di tutti i livelli di gravità. |
|Condizione del monitoraggio |Selezionare una condizione di monitoraggio per filtrare gli avvisi se sono stati generati o risolti dal sistema se la condizione non è più attiva. In alternativa, selezionare **tutti** per includere gli avvisi di tutte le condizioni. |
|Stato dell'avviso |Selezionare uno stato di avviso, **nuovo**, **riconoscimento**, **chiuso**o **tutti** per includere gli avvisi di tutti gli Stati. |
|Servizio di monitoraggio |Selezionare un servizio oppure **Tutti** per includere tutti i servizi. Per questa funzionalità sono supportati solo gli avvisi di VM Insights.|
|Intervallo di tempo| Nella visualizzazione vengono inclusi solo gli avvisi attivati nell'intervallo di tempo selezionato. I valori supportati sono l'ultima ora, le ultime 24 ore, gli ultimi 7 giorni e gli ultimi 30 giorni. |

Quando si seleziona un avviso, viene visualizzata la pagina dei **Dettagli dell'avviso** . Questa pagina fornisce i dettagli dell'avviso e consente di modificarne lo stato.

Per altre informazioni sulla gestione degli avvisi, vedere [Creare, visualizzare e gestire gli avvisi tramite Monitoraggio di Azure](../../azure-monitor/platform/alerts-metric.md).

>[!NOTE]
>La creazione di nuovi avvisi in base ai criteri di integrità o alla modifica delle regole di avviso di integrità esistenti in monitoraggio di Azure dal portale non è attualmente supportata.


![Riquadro Dettagli avvisi per un avviso selezionato](./media/vminsights-health/alert-details-pane-01.png)

È possibile modificare uno stato di avviso per uno o più avvisi selezionandoli, quindi selezionare **Cambia stato** dalla pagina tutti gli **avvisi** nell'angolo superiore sinistro. Selezionare uno degli Stati nel riquadro **modifica stato avviso** , aggiungere una descrizione della modifica nel campo **Commento** , quindi selezionare **OK** per eseguire il commit delle modifiche. Quando le informazioni vengono verificate e le modifiche vengono applicate, tenere traccia dello stato di avanzamento in **notifiche** nel menu.

### <a name="configure-alerts"></a>Configurare gli avvisi
Non è possibile gestire determinate attività di gestione degli avvisi dal portale di Azure. Queste attività devono essere eseguite tramite l' [API REST di monitoraggio di Azure](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/components). In particolare:

- Abilitazione o disabilitazione di un avviso per i criteri di integrità
- Impostazione delle notifiche per gli avvisi relativi ai criteri di integrità

Ogni esempio usa [ARMClient](https://github.com/projectkudu/armclient) nel computer Windows. Se non si ha familiarità con questo metodo, vedere [uso di ARMClient](../platform/rest-api-walkthrough.md#use-armclient).

#### <a name="enable-or-disable-an-alert-rule"></a>Abilitare o disabilitare una regola di avviso

Per abilitare o disabilitare un avviso per criteri di integrità specifici, è necessario modificare la proprietà **alertGeneration** con un valore **disabilitato** o **abilitato**.

Per identificare *monitorId* per criteri di integrità specifici, nell'esempio seguente viene illustrato come eseguire una query per il valore per i criteri **logico\media letture disco secondi per trasferimento**:

1. In una finestra del terminale digitare **armclient.exe login**. In questo modo viene richiesto di accedere ad Azure.

2. Immettere il comando seguente per recuperare tutti i criteri di integrità attivi in una macchina virtuale specifica e identificare il valore per la proprietà *monitorId* :

    ```
    armclient GET "subscriptions/subscriptionId/resourceGroups/resourcegroupName/providers/Microsoft.Compute/virtualMachines/vmName/providers/Microsoft.WorkloadMonitor/monitors?api-version=2018-08-31-preview"
    ```

    Nell'esempio seguente viene illustrato l'output del comando *ARMCLIENT Get* . Prendere nota del valore di *MonitorId*. Questo valore è obbligatorio per il passaggio successivo, in cui è necessario specificare l'ID dei criteri di integrità e modificarne la proprietà per creare un avviso.

    ```
    "id": "/subscriptions/a7f23fdb-e626-4f95-89aa-3a360a90861e/resourcegroups/Lab/providers/Microsoft.Compute/virtualMachines/SVR01/providers/Microsoft.WorkloadMonitor/monitors/ComponentTypeId='LogicalDisk',MonitorId='Microsoft_LogicalDisk_AvgDiskSecPerRead'",
      "name": "ComponentTypeId='LogicalDisk',MonitorId='Microsoft_LogicalDisk_AvgDiskSecPerRead'",
      "type": "Microsoft.WorkloadMonitor/virtualMachines/monitors"
    },
    {
      "properties": {
        "description": "Monitor the performance counter LogicalDisk\\Avg Disk Sec Per Transfer",
        "monitorId": "Microsoft_LogicalDisk_AvgDiskSecPerTransfer",
        "monitorName": "Microsoft.LogicalDisk.AvgDiskSecPerTransfer",
        "monitorDisplayName": "Average Logical Disk Seconds Per Transfer",
        "parentMonitorName": null,
        "parentMonitorDisplayName": null,
        "monitorType": "Unit",
        "monitorCategory": "PerformanceHealth",
        "componentTypeId": "LogicalDisk",
        "componentTypeName": "LogicalDisk",
        "componentTypeDisplayName": "Logical Disk",
        "monitorState": "Enabled",
        "criteria": [
          {
            "healthState": "Warning",
            "comparisonOperator": "GreaterThan",
            "threshold": 0.1
          }
        ],
        "alertGeneration": "Enabled",
        "frequency": 1,
        "lookbackDuration": 17,
        "documentationURL": "https://aka.ms/Ahcs1r",
        "configurable": true,
        "signalType": "Metrics",
        "signalName": "VMHealth_Avg. Logical Disk sec/Transfer"
      },
      "etag": null,
    ```

3. Immettere il comando seguente per modificare la proprietà *alertGeneration* :

    ```
    armclient patch subscriptions/subscriptionId/resourceGroups/resourcegroupName/providers/Microsoft.Compute/virtualMachines/vmName/providers/Microsoft.WorkloadMonitor/monitors/Microsoft_LogicalDisk_AvgDiskSecPerTransfer?api-version=2018-08-31-preview "{'properties':{'alertGeneration':'Disabled'}}"
    ```   

4. Immettere il comando GET usato nel passaggio 2 per verificare che il valore della proprietà sia impostato su **Disabled (disabilitato**).

#### <a name="associate-an-action-group-with-health-criteria"></a>Associare un gruppo di azioni ai criteri di integrità

Monitoraggio di Azure per le macchine virtuali Health supporta SMS e notifiche tramite posta elettronica quando vengono generati avvisi da criteri di integrità non integri. Per configurare le notifiche, annotare il nome del gruppo di azioni configurato per inviare notifiche SMS o di posta elettronica.

>[!NOTE]
>Questa azione deve essere eseguita su ogni macchina virtuale monitorata per la quale si desidera ricevere una notifica. Non si applica a tutte le macchine virtuali in un gruppo di risorse.

1. In una finestra del terminale immettere *armclient. exe login*. In questo modo viene richiesto di accedere ad Azure.

2. Immettere il comando seguente per associare un gruppo di azioni alle regole di avviso:
 
    ```
    $payload = "{'properties':{'ActionGroupResourceIds':['/subscriptions/subscriptionId/resourceGroups/resourcegroupName/providers/microsoft.insights/actionGroups/actiongroupName']}}"
    armclient PUT https://management.azure.com/subscriptions/subscriptionId/resourceGroups/resourcegroupName/providers/Microsoft.Compute/virtualMachines/vmName/providers/Microsoft.WorkloadMonitor/notificationSettings/default?api-version=2018-08-31-preview $payload
    ```

3. Per verificare che il valore della proprietà **actionGroupResourceIds** sia stato aggiornato correttamente, immettere il comando seguente:

    ```
    armclient GET "subscriptions/subscriptionName/resourceGroups/resourcegroupName/providers/Microsoft.Compute/virtualMachines/vmName/providers/Microsoft.WorkloadMonitor/notificationSettings?api-version=2018-08-31-preview"
    ```

    L'output dovrebbe essere simile ai criteri seguenti:
    
    ```
    {
      "value": [
        {
          "properties": {
            "actionGroupResourceIds": [
              "/subscriptions/a7f23fdb-e626-4f95-89aa-3a360a90861e/resourceGroups/Lab/providers/microsoft.insights/actionGroups/Lab-IT%20Ops%20Notify"
            ]
          },
          "etag": null,
          "id": "/subscriptions/a7f23fdb-e626-4f95-89aa-3a360a90861e/resourcegroups/Lab/providers/Microsoft.Compute/virtualMachines/SVR01/providers/Microsoft.WorkloadMonitor/notificationSettings/default",
          "name": "notificationSettings/default",
          "type": "Microsoft.WorkloadMonitor/virtualMachines/notificationSettings"
        }
      ],
      "nextLink": null
    }
    ```

## <a name="next-steps"></a>Passaggi successivi

- Per identificare le limitazioni e le prestazioni complessive della macchina virtuale, vedere [visualizzare le prestazioni delle macchine virtuali di Azure](vminsights-performance.md).

- Per informazioni sulle dipendenze dell'applicazione individuate, vedere [visualizzare monitoraggio di Azure per le macchine virtuali mappa](vminsights-maps.md).
