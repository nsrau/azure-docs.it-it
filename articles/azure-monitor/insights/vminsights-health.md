---
title: Comprendere l'integrità delle macchine virtuali di Azure | Microsoft Docs
description: Questo articolo viene descritto come comprendere l'integrità delle macchine virtuali e sistemi operativi sottostanti con monitoraggio di Azure per le macchine virtuali.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/22/2019
ms.author: magoedte
ms.openlocfilehash: 2bf891f8cfecbb9e78e511dcee7ed1c61c170016
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/24/2019
ms.locfileid: "67340133"
---
# <a name="understand-the-health-of-your-azure-virtual-machines"></a>Comprendere l'integrità delle macchine virtuali di Azure

Azure include servizi per specifici ruoli o le attività nell'area di monitoraggio, ma non fornisce le prospettive approfondita dell'integrità dei sistemi operativi (Open Source OSs) ospitate in macchine virtuali di Azure (VM). Sebbene sia possibile usare monitoraggio di Azure per diverse condizioni, non è stata progettata per modellare e rappresentano l'integrità dei componenti di base o l'integrità complessiva delle macchine virtuali.

Tramite Monitoraggio di Azure per l'integrità di macchine virtuali, è possibile monitorare attivamente la disponibilità e prestazioni di un guest di Windows o Linux del sistema operativo. La funzionalità di integrità Usa un modello che rappresenta i componenti principali e le relative relazioni, fornisce i criteri che specifica la modalità misurare l'integrità dei componenti e invia un avviso quando rileva una condizione di tipo non integro.

Visualizzazione dello stato di integrità complessivo di una VM di Azure e il sistema operativo sottostante può essere rilevata da due prospettive: direttamente da una macchina virtuale, o tutte le macchine virtuali in un gruppo di risorse da monitoraggio di Azure.

Questo articolo illustra come valutare, analizzare e risolvere i problemi di integrità quando vengono rilevati dal monitoraggio di Azure per la funzionalità di integrità di macchine virtuali.

Per informazioni sulla configurazione di Monitoraggio di Azure per le macchine virtuali, vedere [Enable Azure Monitor for VMs](vminsights-enable-overview.md) (Abilitare Monitoraggio di Azure per le macchine virtuali).

## <a name="monitoring-configuration-details"></a>Dettagli di configurazione di monitoraggio

Questa sezione descrive i criteri di integrità predefiniti per monitorare le macchine virtuali Linux e Windows Azure. Tutti i criteri di integrità sono configurati in precedenza per inviare un avviso quando rilevano una condizione di tipo non integro.

### <a name="windows-vms"></a>Macchine virtuali di Windows

- Megabyte disponibili per la memoria
- Media scritture disco/secondi (disco logico)
- Media scritture disco/secondi (disco)
- Media lettura disco logico/secondi
- Media trasferimento disco logico/secondi
- Media lettura disco/secondi
- Media trasferimento disco/secondi
- Lunghezza corrente coda su disco (disco logico)
- Lunghezza corrente coda su disco (disco)
- Percentuale tempo di inattività del disco
- Errore o danneggiamento del file system
- Spazio disponibile su disco logico (%) insufficiente
- Spazio disponibile su disco logico (MB) insufficiente
- Percentuale tempo di inattività del disco logico
- Pagine di memoria al secondo
- Percentuale di larghezza di banda utilizzata per lettura
- Percentuale di larghezza di banda utilizzata in totale
- Percentuale di larghezza di banda utilizzata per scrittura
- Percentuale di memoria in uso di cui è stato eseguito il commit
- Percentuale tempo di inattività del disco
- Integrità del servizio di client DHCP
- Integrità del servizio di client DNS
- Integrità del servizio RPC
- Integrità del servizio di server
- Percentuale di utilizzo di CPU totale
- Integrità del servizio Registro eventi di Windows
- Integrità del servizio Windows Firewall
- Integrità del servizio Gestione remota Windows

### <a name="linux-vms"></a>Macchine virtuali di Linux

- Media disco Disk sec/Transfer
- Media disco Disk sec/Read
- Media disco Disk sec/Write
- Integrità disco
- Spazio disponibile su disco logico
- % spazio disponibile su disco logico
- % inode disponibili su disco logico
- Integrità scheda di rete
- Percentuale tempo processore totale
- Megabyte disponibili per la memoria del sistema operativo

## <a name="sign-in-to-the-azure-portal"></a>Accedere al portale di Azure

Per accedere, vedere la [portale di Azure](https://portal.azure.com).

## <a name="introduction-to-azure-monitor-for-vms-health"></a>Introduzione a monitoraggio di Azure per l'integrità di macchine virtuali

Prima di usare la funzionalità di integrità per una singola macchina virtuale o un gruppo di macchine virtuali, è importante comprendere come vengono visualizzate le informazioni e ciò che rappresentano le visualizzazioni.

### <a name="view-health-directly-from-a-vm"></a>Visualizzare l'integrità direttamente da una macchina virtuale

Per visualizzare l'integrità di una VM di Azure, selezionare **Insights (anteprima)** nel riquadro sinistro della macchina virtuale. Nella pagina informazioni dettagliate della macchina virtuale, il **integrità** scheda aperta per impostazione predefinita e viene illustrata la visualizzazione dell'integrità della macchina virtuale.

![Panoramica dell'integrità di Monitoraggio di Azure per macchine virtuali per una macchina virutale di Azure selezionata](./media/vminsights-health/vminsights-directvm-health.png)

Nel **Health** nella scheda **integrità della macchina virtuale Guest**, la tabella mostra lo stato di integrità della macchina virtuale e il numero totale di avvisi sull'integrità della macchina virtuale non generato da un componente di tipo non integro.

Per altre informazioni, vedere [Avvisi](#alerts).

La tabella seguente descrive gli stati di integrità definiti per una macchina virtuale:

|Icona |Stato di integrità |Significato |
|-----|-------------|---------------|
| |Healthy |La macchina virtuale è compreso le condizioni di integrità definiti. Questo stato indica la presenza è Nessun problema rilevato e corretto funzionamento della macchina virtuale. Con un monitoraggio rollup padre, dell'integrità esegue il rollup e riflette lo stato peggiore o migliore dell'elemento figlio.|
| |Critico |Lo stato non è all'interno della condizione di integrità definiti, che indica che uno o sono stati rilevati problemi più critici. Questi problemi da affrontare per ripristinare il normale funzionamento. Con un monitoraggio rollup padre, lo stato di integrità esegue il rollup e riflette lo stato peggiore o migliore dell'elemento figlio.|
| |Avviso |Lo stato è compreso tra due valori di soglia per la condizione di integrità definiti, in cui uno indica uno stato di avviso e l'altro indica uno stato critico (tre soglie di stato di integrità possono essere configurate) o quando un problema non critico può causare i problemi critici se non risolto. Con un monitoraggio rollup padre, se uno o più elementi figlio si trova in uno stato di avviso, l'elemento padre rifletterà lo stato di avviso. Se un elemento figlio si trova in uno stato critico e un altro elemento figlio in uno stato di avviso, il rollup padre mostrerà lo stato di integrità come critici.|
| |Sconosciuto |Impossibile calcolare lo stato per diversi motivi. La sezione seguente fornisce dettagli aggiuntivi e le possibili soluzioni. |

Uno stato di integrità sconosciuto può essere causato da problemi seguenti:

- L'agente è stato riconfigurato e non è più report all'area di lavoro specificato quando è stato abilitato il monitoraggio di Azure per le macchine virtuali. Per configurare l'agente invii report per l'area di lavoro, vedere [aggiungendo o rimuovendo un'area di lavoro](../platform/agent-manage.md#adding-or-removing-a-workspace).
- La macchina virtuale è stata eliminata.
- L'area di lavoro associato a monitoraggio di Azure per le macchine virtuali è stato eliminato. Se hai i benefici di supporto tecnico Premier, è possibile ripristinare l'area di lavoro. Passare a [Premier](https://premier.microsoft.com/) e aprire una richiesta di supporto.
- Le dipendenze di soluzione sono state eliminate. Per abilitare nuovamente le soluzioni ServiceMap e InfrastructureInsights nell'area di lavoro di Log Analitica, reinstallare queste soluzioni tramite le [modello di Azure Resource Manager](vminsights-enable-at-scale-powershell.md#install-the-servicemap-and-infrastructureinsights-solutions). In alternativa, usare l'opzione di configurazione dell'area di lavoro disponibile nella scheda iniziare.
- La macchina virtuale è stata arrestata.
- Il servizio macchine Virtuali di Azure è disponibile o è in corso la manutenzione.
- L'area di lavoro [giornaliera dei dati o il limite di conservazione](../platform/manage-cost-storage.md) è stata soddisfatta.

Selezionare **visualizzare diagnostica dell'integrità** per aprire una pagina che mostra tutti i componenti di una macchina virtuale, associato ai criteri di integrità, le modifiche dello stato e altri problemi rilevati dal monitoraggio dei componenti correlati alla macchina virtuale.

Per altre informazioni, vedere [Diagnostica integrità](#health-diagnostics).

Nel **integrità** sezione una tabella mostra il rollup dello stato dei componenti performance monitorate dai criteri di integrità. Questi componenti includono **CPU**, **memoria**, **disco**, e **rete**. Se si seleziona un componente viene aperta una pagina che elenca tutti i criteri di monitoraggio e lo stato di integrità del componente.

Quando si accede integrità da una macchina virtuale di Azure che esegue Windows, lo stato di integrità dei servizi Windows core primi cinque visualizzato nella casella **integrità dei servizi di base**. Se si seleziona uno dei servizi viene aperta una pagina che elenca i criteri di integrità di monitoraggio per il componente con lo stato di integrità.

Se si seleziona il nome dei criteri di integrità viene aperto il riquadro proprietà. In questo riquadro, è possibile esaminare i dettagli di configurazione, inclusi se i criteri di integrità dispongono di un avviso di monitoraggio di Azure corrispondente.

Per altre informazioni, vedere [diagnostica dell'integrità e uso dei criteri di integrità](#health-diagnostics).

### <a name="aggregate-vm-perspective"></a>Punto di vista di aggregazione della macchina virtuale

Per visualizzare la raccolta di integrità per tutte le macchine virtuali in un gruppo di risorse, selezionare **monitoraggio di Azure** nell'elenco di navigazione nel portale e quindi selezionare **macchine virtuali (anteprima)** .

![Visualizzazione del monitoraggio di Informazioni dettagliate macchina virtuale da Monitoraggio di Azure](./media/vminsights-health/vminsights-aggregate-health.png)

Nel **abbonamento** e **gruppo di risorse** elenchi a discesa, selezionare il gruppo di risorse appropriato che include le macchine virtuali correlate al gruppo, per visualizzare lo stato di integrità segnalati. La selezione solo si applica alla funzionalità di integrità e non si applica a **Performance** oppure **mappa** schede.

Il **integrità** scheda fornisce le informazioni seguenti:

* Quante VM sono in uno stato critico o non integro, rispetto a quanti sono integri o non invia dati (detto uno stato sconosciuto).
* Quali e quante macchine virtuali dal sistema operativo segnalano uno stato non integro.
* Numero di macchine virtuali a causa di un problema rilevato con un processore, disco, memoria o scheda di rete, classificati in base dello stato di integrità non è integro.
* Numero di macchine virtuali a causa di un problema rilevato con un servizio del sistema operativo di base, classificati in base dello stato di integrità non è integro.

Nel **integrità** scheda, è possibile identificare i problemi critici rilevati dai criteri di integrità, il monitoraggio della macchina virtuale e i dettagli degli avvisi di revisione e articoli della Knowledge Base associato. Questi articoli possono facilitare la diagnosi e risoluzione dei problemi. Selezionare uno dei valori di gravità per aprire la pagina [Tutti gli avvisi](../../azure-monitor/platform/alerts-overview.md#all-alerts-page) filtrata in base al livello di gravità specificato.

L'elenco **Distribuzione delle macchine virtuali - Sistema operativo** mostra le macchine virtuali elencate in base alla versione di Windows o alla distribuzione di Linux, insieme alla relativa versione. In ogni categoria del sistema operativo, le macchine virtuali sono suddivisi ulteriormente in base all'integrità della macchina virtuale.

![Prospettiva della distribuzione delle macchine virtuali in Informazioni dettagliate macchina virtuale](./media/vminsights-health/vminsights-vmdistribution-by-os.png)

Selezionare qualsiasi colonna che include **numero di macchine Virtuali**, **critico**, **avviso**, **integro**, o **sconosciuto**. Visualizzare l'elenco dei risultati filtrati nel **macchine virtuali** pagina che soddisfano la colonna selezionata.

Ad esempio, per esaminare tutte le macchine virtuali che eseguono Red Hat Enterprise Linux versione 7.5, selezionare la **numero di macchine Virtuali** valore per tale sistema operativo che elenca le macchine virtuali corrispondenti che il filtro e il relativo stato di integrità corrente.

![Esempio di rollup delle macchine virtuali Red Hat Linux](./media/vminsights-health/vminsights-rollup-vm-rehl-01.png)

Nel **macchine virtuali** pagina, se si seleziona il nome di una macchina virtuale nella colonna **nome macchina virtuale**, si viene indirizzati al **istanza di macchina virtuale** pagina. Questa pagina vengono fornite informazioni dettagliate degli avvisi e problemi di criteri di integrità che compromettono le prestazioni della macchina virtuale selezionata. Filtrare i dettagli dello stato di integrità selezionando **lo stato di integrità** icona nell'angolo superiore sinistro della pagina per vedere quali componenti sono integri. È anche possibile visualizzare gli avvisi di integrità della macchina virtuale generati da un componente di tipo non integro classificati in base alla gravità dell'avviso.

Dal **VM list** visualizzazione, selezionare il nome di una macchina virtuale per aprire il **integrità** pagina per la macchina virtuale, in modo analogo a come se si seleziona **Insights (anteprima)** dalla macchina virtuale direttamente.

![Informazioni dettagliate macchina virtuale di una macchina virtuale di Azure selezionata](./media/vminsights-health/vminsights-directvm-health.png)

Il **Insights (anteprima)** pagina mostra un rollup dello stato di integrità per le macchine Virtuali e gli avvisi. Questo stato di integrità è stato categorizzato in base alla gravità, che rappresenta una macchina virtuale gli avvisi di integrità generati quando lo stato modificato da integro a non integro, in base ai criteri. Selezionando **macchine virtuali in una condizione critica** apre una pagina con un elenco di uno o più macchine virtuali in uno stato di integrità critico.

Lo stato di integrità per uno dei programmi macchine virtuali selezionando il **diagnostica dell'integrità** vista della macchina virtuale. In questa visualizzazione, è possibile determinare quali criteri di integrità è indicare un problema di stato di integrità. Quando la **diagnostica dell'integrità** verrà visualizzata la pagina, vengono mostrati tutti i componenti della macchina virtuale e i relativi criteri di integrità associati con lo stato di integrità corrente.

Per altre informazioni, vedere [Diagnostica integrità](#health-diagnostics).

Selezionando **Visualizza tutti i criteri di integrità** si apre una pagina con un elenco di tutti i criteri di integrità disponibili con questa funzionalità. Le informazioni possono essere ulteriormente filtrate in base alle opzioni seguenti:

* **Tipo**. Esistono tre tipi di criteri di integrità per valutare condizioni e rollup dello stato di integrità complessivo di una macchina virtuale monitorata:
    - **Unit**. Consente di misurare alcuni aspetti di una macchina virtuale. Questo tipo di criteri di integrità potrebbe essere necessario controllare un contatore delle prestazioni per determinare le prestazioni del componente, eseguire uno script per eseguire una transazione sintetica o il controllo di un evento che indica un errore. Per impostazione predefinita, il filtro verrà impostato sull'unità.
    - **Dipendenza**. Fornisce un rollup dello stato tra le diverse entità. Questi criteri di integrità consente l'integrità di un'entità a dipende dall'integrità di un altro tipo di entità cui si basa per l'operazione ha esito positivo.
    - **Aggregazione**. Fornisce uno stato di integrità complessivo dei criteri di integrità simili. Criteri di integrità unità e dipendenze vengono in genere configurati in un criterio di integrità aggregato. Oltre a fornire una migliore organizzazione generale dei diversi criteri di integrità destinati a un'entità, il criterio di integrità aggregato fornisce uno stato di integrità univoco per categorie distinte di entità.

* **Categoria**. Tipo di criteri di integrità utilizzate per raggruppare criteri simili ai fini dei report. Queste categorie sono **disponibilità** e **prestazioni**.

Selezionare un valore inferiore per visualizzare le istanze che non sono integri, il **componente non integro** colonna. In questa pagina, una tabella sono elencati i componenti che sono in uno stato di integrità critico.

## <a name="health-diagnostics"></a>Diagnostica integrità

Il **diagnostica dell'integrità** pagina consente di visualizzare il modello di integrità di una macchina virtuale. Questa pagina elenca tutti i componenti della macchina virtuale, i criteri di integrità associata, le modifiche dello stato e altri importanti problemi identificati da componenti monitorati correlati alla macchina virtuale.

![Esempio di pagina Diagnostica integrità per una macchina virtuale](./media/vminsights-health/health-diagnostics-page-01.png)

Avviare diagnostica dell'integrità usando i metodi seguenti:

* Dallo stato di integrità di rollup per tutte le macchine virtuali dal punto di vista aggregata di macchina virtuale in Monitoraggio di Azure:

    1. Nel **integrità** pagina, selezionare l'icona **critico**, **avviso**, **integro**, o **sconosciuto** lo stato di integrità nella sezione **integrità della macchina virtuale Guest**.
    2. Passare alla pagina che elenca tutte le macchine virtuali corrispondenti a tale categoria filtrata.
    3. Selezionare il valore di **lo stato di integrità** colonna per aprire la diagnostica dell'integrità nell'ambito di tale macchina virtuale.

* Dal sistema operativo dal punto di vista aggregata di macchina virtuale in Monitoraggio di Azure. In **Distribuzione delle macchine virtuali** selezionare uno dei valori della colonna per aprire la pagina **Macchine virtuali** e visualizzare un elenco nella tabella corrispondente alla categoria filtrata. Selezione del valore sotto **lo stato di integrità** colonna verrà visualizzata la diagnostica dell'integrità per la VM selezionata.
 
* Dalla VM del monitoraggio di Azure di macchine virtuali guest **Health** scheda, selezionando **visualizzare diagnostica dell'integrità**.

Diagnostica dell'integrità consente di organizzare le informazioni di integrità in due categorie: disponibilità e prestazioni.
 
È possibile visualizzare tutti i criteri di integrità definiti per un componente, ad esempio disco logico, CPU e così via, senza il filtro in due categorie. Queste viste possono essere in una visualizzazione completa dei criteri, o tramite filtrare i risultati per una categoria quando si seleziona **disponibilità** oppure **prestazioni**.

La categoria di criteri possa essere visualizzata anche accanto al **criteri di integrità** colonna. Se i criteri non corrispondono alla categoria selezionata in un messaggio che informa **alcun criterio di integrità disponibili per la categoria selezionata** viene visualizzato nei **criteri di integrità** colonna.

Lo stato di un criterio di integrità viene definito mediante uno dei quattro tipi: **Critici**, **avviso**, **integro**, e **sconosciuto**. I primi tre sono configurabili, vale a dire che è possibile modificare i valori di soglia per i monitor direttamente nella **criteri di integrità** riquadro di configurazione. Ciò è possibile anche tramite API REST di monitoraggio di Azure [monitorare l'operazione di aggiornamento](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/monitors/update). **Sconosciuto** non è configurabile ed è riservata per scenari specifici.

Il **diagnostica dell'integrità** pagina ha tre sezioni principali:

* Modello componente
* Criteri di integrità
* Modifiche di stato

![Sezioni della pagina Diagnostica integrità](./media/vminsights-health/health-diagnostics-page-02.png)

### <a name="component-model"></a>Modello componente

La colonna all'estrema sinistra nella **diagnostica dell'integrità** pagina viene **modello componente**. Tutti i componenti, che sono associati con la macchina virtuale, vengono visualizzati in questa colonna insieme al relativo stato di integrità corrente.

Nell'esempio seguente, sono i componenti individuati **disco**, **disco logico**, **processore**, **memoria**, e  **Sistema operativo**. In questa colonna vengono individuate e visualizzate più istanze di questi componenti.

Ad esempio, la figura seguente mostra che la macchina virtuale dispone di due istanze di dischi logici, **c:** e **unità d:** , che sono in uno stato integro:

![Esempio di modello componente presentato in Diagnostica integrità](./media/vminsights-health/health-diagnostics-page-component.png)

### <a name="health-criteria"></a>Criteri di integrità

La colonna centro nella pagina di diagnostica dell'integrità **criteri di integrità**. Il modello di integrità definito per la macchina virtuale è visualizzato in un albero gerarchico. Il modello di integrità per una macchina virtuale è costituito da unità e criteri di integrità aggregati.

![Esempio di criterio di integrità presentato in Diagnostica integrità](./media/vminsights-health/health-diagnostics-page-healthcriteria.png)

Un criterio di integrità misura l'integrità di un'istanza monitorata, che può essere un valore di soglia dello stato di un'entità e così via. Un criterio di integrità dispone di due o tre soglie di stato di integrità configurabili, come descritto in precedenza. In qualsiasi momento, il criterio di integrità può trovarsi in uno solo dei relativi stati possibili.

Il modello di integrità definisce i criteri che determinano l'integrità di destinazione complessiva e componenti di destinazione. Viene illustrata la gerarchia dei criteri di **criteri di integrità** sezione il **diagnostica dell'integrità** pagina.

I criteri di rollup dell'integrità fanno parte della configurazione dei criteri di integrità aggregato (il valore predefinito è impostato su **peggiore-of**). È possibile trovare un set predefinito di criteri di integrità in esecuzione come parte di questa funzionalità, il [dettagli di configurazione di monitoraggio](#monitoring-configuration-details) sezione di questo articolo.

È anche possibile usare l'API REST di monitoraggio di Azure [elenco di istanze di monitoraggio da risorsa](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/monitorinstances/listbyresource) per ottenere un elenco di tutti i criteri di integrità. Questo criterio include i dettagli di configurazione in esecuzione sulla risorsa della macchina virtuale di Azure.

Il **Unit** il tipo di criteri di integrità può avere la configurazione modificata selezionando il collegamento di puntini di sospensione a destra. Selezionare **Mostra dettagli** per aprire il riquadro di configurazione.

![Configurazione di un esempio di criteri di integrità](./media/vminsights-health/health-diagnostics-vm-example-02.png)

Nel riquadro di configurazione per i criteri di integrità selezionato, se si usa l'esempio **Media scritture disco/secondi**, la soglia può essere configurata con un valore numerico diverso. È un monitoraggio a due stati, vale a dire può cambiare solo da **integro** al **avviso**.

Altri criteri di integrità usano talvolta tre stati, in cui è possibile configurare il valore per le soglie di stato di integrità critiche o avviso. È inoltre possibile modificare una soglia usando l'API REST di monitoraggio di Azure [configurazione del monitor](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/monitors/update).

>[!NOTE]
>Applicazione di modifiche alla configurazione di criteri di integrità a un'istanza, li applica a tutte le istanze monitorate. Ad esempio, se si seleziona **disco d-1** e quindi modificare il **Media scritture disco/secondi** soglia, la modifica viene applicata a tutte le istanze individuate e monitorate sulla macchina virtuale.


![Configurazione di criteri di integrità per un esempio di monitoraggio unità](./media/vminsights-health/health-diagnostics-criteria-config-01.png)

Se vuoi altre informazioni sui criteri di integrità, sono stati inclusi articoli della Knowledge base che consentono di identificare i problemi, le cause e risoluzioni. Selezionare **consente di visualizzare informazioni** nella pagina per vedere l'articolo della Knowledge Base correlati.

Per esaminare tutti gli articoli della Knowledge Base inclusi con monitoraggio di Azure per l'integrità di macchine virtuali, vedere [documentazione di monitoraggio di Azure dell'integrità della Knowledge Base](https://docs.microsoft.com/azure/monitoring/infrastructure-health/).

### <a name="state-changes"></a>Modifiche stato

La colonna all'estrema destra della **diagnostica dell'integrità** è pagina **cambiamenti di stato**. Questa colonna elenca tutte le modifiche di stato associate con i criteri di integrità selezionati nel **criteri di integrità** sezione o la modifica dello stato della macchina virtuale se una macchina virtuale è stata selezionata la **modello del componente** o  **Criteri di integrità** colonna della tabella.

![Esempio di modifiche di stato presentato in Diagnostica integrità](./media/vminsights-health/health-diagnostics-page-statechanges.png)

La sezione seguente illustra lo stato di criteri di integrità e l'ora associato. Queste informazioni mostrano lo stato più recente nella parte superiore della colonna.

### <a name="association-of-component-model-health-criteria-and-state-changes-columns"></a>Associazione delle colonne del modello di componente, criteri di integrità e le modifiche dello stato

Le tre colonne sono correlate tra loro. Quando si seleziona un'istanza nel **modello del componente** colonna, il **criteri di integrità** colonna viene filtrata per la visualizzazione componente. Di conseguenza, il **modifiche apportate allo stato** colonna viene aggiornata in base ai criteri di integrità selezionato.

![Esempio di selezione dell'istanza monitorata e dei risultati](./media/vminsights-health/health-diagnostics-vm-example-01.png)

Ad esempio, se si seleziona *disco - d 1:* dall'elenco in **modello componente**, **criteri di integrità** filtri ai *disco - 1, D:* e  **Le modifiche di stato** Mostra la modifica dello stato in base alla disponibilità di *Disk - 1 d*.

Per visualizzare uno stato di integrità aggiornato, è possibile aggiornare la pagina di diagnostica dell'integrità, selezionare la **Aggiorna** collegamento. Se lo stato di integrità del criterio di integrità viene aggiornato in base all'intervallo di polling predefinito, questa attività consente di evitare l'attesa e riflette lo stato di integrità più recente. Il **lo stato di integrità criteri** è un filtro che consente di definire ambito risultati basati sullo stato di integrità selezionato: Integro, avviso, critico, Unknown e tutti. Il **ultimo aggiornamento** nell'angolo superiore destro di durata rappresenta l'ultimo aggiornamento la pagina di diagnostica dell'integrità.

## <a name="alerts"></a>Avvisi

Monitoraggio di Azure per l'integrità di macchine virtuali dei serviziI [avvisi di Azure](../../azure-monitor/platform/alerts-overview.md). Genera un avviso quando modificano criteri predefiniti, se rilevato, da uno stato integro a uno stato non integro. Gli avvisi vengono classificati in base alla gravità, da gravità 0 a 4 livelli di gravità, con gravità 0 come il livello più alto.

Gli avvisi non sono associati a un gruppo di azioni per notificare quando è stato attivato l'avviso. Il proprietario della sottoscrizione è necessario configurare le notifiche seguendo la procedura descritta nel [configurare gli avvisi](#configure-alerts) sezione.

Il numero totale di classificati in base al livello di gravità degli avvisi di integrità della macchina virtuale è disponibile nel **Health** dashboard sotto il **avvisi** sezione. Quando si seleziona il numero totale di avvisi o il numero corrispondente a un livello di gravità, si apre la pagina **Avvisi** in cui sono elencati tutti gli avvisi corrispondenti alla selezione.

Ad esempio, se si seleziona la riga corrispondente **a livello di gravità 1**, si noterà la vista seguente:

![Esempio di tutti gli avvisi con livello di gravità 1](./media/vminsights-health/vminsights-sev1-alerts-01.png)

Il **tutti gli avvisi** pagina non è con ambita per mostrare solo gli avvisi che corrispondono alla selezione. Inoltre viene filtrato in base **tipo di risorsa** per mostrare solo gli avvisi di integrità generati da una risorsa di macchina virtuale. Questo formato viene riflessa nell'elenco degli avvisi, nella colonna **risorsa di destinazione**, in cui viene illustrata la macchina virtuale di Azure all'avviso generato quando è stata soddisfatta una condizione di tipo non integro.

Gli avvisi provenienti da altri tipi di risorse o servizi non devono essere inclusi in questa vista. Questi avvisi includono gli avvisi del log, che sono basati su query di log o gli avvisi delle metriche che verrebbero normalmente visualizzati da quello predefinito di monitoraggio di Azure [tutti gli avvisi](../../azure-monitor/platform/alerts-overview.md#all-alerts-page) pagina.

È possibile filtrare questa visualizzazione selezionando i valori nei menu a discesa nella parte superiore della pagina.

|Colonna |Descrizione |
|-------|------------|
|Sottoscrizione |Selezionare una sottoscrizione di Azure. Sono inclusi nella visualizzazione solo gli avvisi della sottoscrizione selezionata. |
|Gruppo di risorse |Selezionare un singolo gruppo di risorse. Sono inclusi nella visualizzazione solo gli avvisi con destinazioni nel gruppo di risorse selezionato. |
|Tipo di risorsa |Selezionare uno o più tipi di risorsa. Per impostazione predefinita, in questa visualizzazione sono selezionati e inclusi solo gli avvisi della **Macchina virtuale** di destinazione. Questa colonna risulta disponibile solo dopo che è stato specificato un gruppo di risorse. |
|Resource |Selezionare una risorsa. Nella visualizzazione vengono inclusi solo gli avvisi con tale risorsa definita come destinazione. Questa colonna è disponibile solo dopo che è stato specificato un tipo di risorsa. |
|Severity |Selezionare una gravità degli avvisi oppure **Tutti** per includere gli avvisi di tutti i livelli di gravità. |
|Condizione del monitoraggio |Se sono stati attivati o risolti dal sistema se la condizione non è più attiva, selezionare una condizione di monitoraggio per gli avvisi di filtro. In alternativa, selezionare **tutti** per includere avvisi di tutte le condizioni. |
|Stato dell'avviso |Selezionare uno stato di avviso, **New**, **riconoscimento**, **Closed**, oppure **tutti** per includere avvisi di tutti gli stati. |
|Servizio di monitoraggio |Selezionare un servizio oppure **Tutti** per includere tutti i servizi. Per questa funzionalità sono supportati solo gli avvisi da Insights della macchina virtuale.|
|Intervallo di tempo| Nella visualizzazione vengono inclusi solo gli avvisi attivati nell'intervallo di tempo selezionato. I valori supportati sono l'ultima ora, le ultime 24 ore, gli ultimi 7 giorni e gli ultimi 30 giorni. |

Quando si seleziona un avviso, il **dettaglio di avviso** viene visualizzata la pagina. Questa pagina fornisce i dettagli dell'avviso e consente di modificare il relativo stato.

Per altre informazioni sulla gestione degli avvisi, vedere [Creare, visualizzare e gestire gli avvisi tramite Monitoraggio di Azure](../../azure-monitor/platform/alerts-metric.md).

>[!NOTE]
>Creazione di nuovi avvisi in base ai criteri di integrità o modifica integrità regole di avviso esistenti in Monitoraggio di Azure dal portale non è attualmente supportata.


![Riquadro Dettagli avvisi per un avviso selezionato](./media/vminsights-health/alert-details-pane-01.png)

È possibile modificare uno stato di avviso per uno o più avvisi selezionandole e quindi selezionando **modificare lo stato** dalle **tutti gli avvisi** pagina nell'angolo superiore sinistro. Selezionare uno degli stati nella **modificare lo stato di avviso** riquadro, aggiungere una descrizione della modifica delle **commento** campo e quindi selezionare **Ok** per eseguire il commit delle modifiche. Quando le informazioni vengono verificate e vengono applicate le modifiche, tenere traccia dell'avanzamento sotto **notifiche** nel menu di scelta.

### <a name="configure-alerts"></a>Configurare gli avvisi
È possibile gestire determinate attività di gestione avvisi dal portale di Azure. Queste attività devono essere eseguite tramite il [API REST di monitoraggio di Azure](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/components). In particolare:

- Abilitazione o disabilitazione di un avviso per i criteri di integrità
- Configurazione delle notifiche per gli avvisi di integrità criteri

Ogni esempio usi [ARMClient](https://github.com/projectkudu/armclient) nel computer Windows. Se non ha familiarità con questo metodo, vedere [ARMClient usando](../platform/rest-api-walkthrough.md#use-armclient).

#### <a name="enable-or-disable-an-alert-rule"></a>Abilitare o disabilitare una regola di avviso

Per abilitare o disabilitare un avviso per i criteri di integrità specifico, la proprietà **alertGeneration** devono essere modificate con un valore di entrambi **disabilitato** oppure **abilitato**.

Per identificare il *monitorId* per i criteri di integrità specifico, nell'esempio seguente viene illustrato come eseguire una query per tale valore per i criteri **Logico\media trasferimenti disco/secondi**:

1. In una finestra del terminale digitare **armclient.exe login**. In questo modo viene richiesto di accedere ad Azure.

2. Immettere il comando seguente per recuperare tutti i criteri di integrità attivi in una macchina virtuale specifica e identificare il valore per *monitorId* proprietà:

    ```
    armclient GET "subscriptions/subscriptionId/resourceGroups/resourcegroupName/providers/Microsoft.Compute/virtualMachines/vmName/providers/Microsoft.WorkloadMonitor/monitors?api-version=2018-08-31-preview”
    ```

    L'esempio seguente mostra l'output del *armclient GET* comando. Prendere nota del valore del *MonitorId*. Questo valore è obbligatorio per il passaggio successivo, in cui è necessario specificare l'ID dei criteri di integrità e modificare le proprietà per creare un avviso.

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

3. Immettere il comando seguente per modificare la *alertGeneration* proprietà:

    ```
    armclient patch subscriptions/subscriptionId/resourceGroups/resourcegroupName/providers/Microsoft.Compute/virtualMachines/vmName/providers/Microsoft.WorkloadMonitor/monitors/Microsoft_LogicalDisk_AvgDiskSecPerTransfer?api-version=2018-08-31-preview "{'properties':{'alertGeneration':'Disabled'}}"
    ```   

4. Immettere il comando GET utilizzato nel passaggio 2 per verificare che il valore della proprietà è impostato su **disabilitato**.

#### <a name="associate-an-action-group-with-health-criteria"></a>Associare un gruppo di azioni con criteri di integrità

Monitoraggio per l'integrità di macchine virtuali di Azure supporta le notifiche di posta elettronica e SMS quando gli avvisi vengono generati da criteri di integrità integro. Per configurare le notifiche, prendere nota del nome del gruppo di azione configurata per inviare notifiche tramite posta elettronica o SMS.

>[!NOTE]
>Questa azione deve essere eseguita su ogni VM monitorati che si desidera ricevere una notifica per. Non è applicabile a tutte le macchine virtuali in un gruppo di risorse.

1. In una finestra del terminale immettere *armclient.exe login*. In questo modo viene richiesto di accedere ad Azure.

2. Immettere il comando seguente per associare un gruppo di azioni delle regole di avviso:
 
    ```
    $payload = "{'properties':{'ActionGroupResourceIds':['/subscriptions/subscriptionId/resourceGroups/resourcegroupName/providers/microsoft.insights/actionGroups/actiongroupName']}}"
    armclient PUT https://management.azure.com/subscriptions/subscriptionId/resourceGroups/resourcegroupName/providers/Microsoft.Compute/virtualMachines/vmName/providers/Microsoft.WorkloadMonitor/notificationSettings/default?api-version=2018-08-31-preview $payload
    ```

3. Per verificare che il valore della proprietà **actionGroupResourceIds** è stata aggiornata, immettere il comando seguente:

    ```
    armclient GET "subscriptions/subscriptionName/resourceGroups/resourcegroupName/providers/Microsoft.Compute/virtualMachines/vmName/providers/Microsoft.WorkloadMonitor/notificationSettings?api-version=2018-08-31-preview"
    ```

    L'output dovrebbe essere simile ai seguenti criteri:
    
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

- Per identificare limitazioni e le prestazioni generali di macchina virtuale, vedere [prestazioni delle VM di Azure Visualizza](vminsights-performance.md).
- Per altre informazioni sulle dipendenze dell'applicazione individuati, vedere [visualizzazione di monitoraggio di Azure per la mappa VMs](vminsights-maps.md).
