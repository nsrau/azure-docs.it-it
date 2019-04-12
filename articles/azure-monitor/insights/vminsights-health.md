---
title: Monitorare l'integrità della macchina virtuale con Monitoraggio di Azure per le macchine virtuali (anteprima) | Microsoft Docs
description: Questo articolo descrive come comprendere l'integrità della macchina virtuale e del sistema operativo sottostante con Monitoraggio di Azure per le macchine virtuali.
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
ms.date: 04/10/2019
ms.author: magoedte
ms.openlocfilehash: b1118a3add665de403e4e0f8fd8883ce0094d9dd
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/11/2019
ms.locfileid: "59490042"
---
# <a name="understand-the-health-of-your-azure-virtual-machines"></a>Comprendere l'integrità delle macchine virtuali di Azure

Azure include più servizi che singolarmente eseguono un'attività o un ruolo specifico nell'area di monitoraggio, ma non hanno fornito un punto di vista approfondita dell'integrità del sistema operativo ospitato su macchine virtuali di Azure. Anche se è possibile monitorare per diverse condizioni tramite Monitoraggio di Azure, non è stato progettato per modellare e rappresentano l'integrità complessiva della macchina virtuale o per l'integrità dei componenti di base. Con la funzionalità dell'integrità di Monitoraggio di Azure per macchine virtuali, è possibile monitorare in modo proattivo la disponibilità e le prestazioni dei sistemi operativi guest Windows o Linux con un modello che rappresenta i componenti principali e le relative relazioni, criteri che specificano come misurare l'integrità di tali componenti e avvisi che segnalano quando viene rilevata una condizione di non integrità.  

Lo stato di integrità globale della macchina virtuale di Azure e del sistema operativo sottostante può essere visualizzato da due prospettive con Monitoraggio di Azure per le macchine virtuali, direttamente dalla macchina virtuale o per tutte le macchine virtuali in un gruppo di risorse da Monitoraggio di Azure.

Questo articolo illustra come valutare, analizzare e risolvere rapidamente i problemi di integrità rilevati.

Per informazioni sulla configurazione di Monitoraggio di Azure per le macchine virtuali, vedere [Enable Azure Monitor for VMs](vminsights-onboard.md) (Abilitare Monitoraggio di Azure per le macchine virtuali).

## <a name="monitoring-configuration-details"></a>Dettagli di configurazione di monitoraggio

Questa sezione descrive i criteri di integrità predefiniti per monitorare le macchine virtuali Linux e Windows Azure. Tutti i criteri di integrità sono preconfigurati per l'invio di un avviso quando viene rilevata una condizione di non integrità. 

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

Accedere al [portale di Azure](https://portal.azure.com). 

## <a name="introduction-to-health-experience"></a>Introduzione all'esperienza dell'integrità

Prima di approfondire l'uso della funzionalità dell'integrità per una singola macchina virtuale o per un gruppo di macchine virtuali, è importante fornire una breve introduzione su come vengono presentate le informazioni e su ciò che rappresentano le visualizzazioni.  

## <a name="view-health-directly-from-a-virtual-machine"></a>Visualizzare l'integrità direttamente da una macchina virtuale 

Per visualizzare l'integrità di una macchina virtuale di Azure, selezionare **Informazioni dettagliate (anteprima)** nel riquadro a sinistra della macchina virtuale. Nella pagina Informazioni dettagliate macchina virtuale, la scheda **Integrità** è aperta per impostazione predefinita e mostra la visualizzazione dell'integrità della macchina virtuale.  

![Panoramica dell'integrità di Monitoraggio di Azure per macchine virtuali per una macchina virutale di Azure selezionata](./media/vminsights-health/vminsights-directvm-health.png)

Nella sezione **Integrità della macchina virtuale guest** della scheda **Integrità**, la tabella mostra lo stato di integrità corrente della macchina virtuale e il numero totale di avvisi sull'integrità della macchina virtuale generati da un componente non integro. Per altre informazioni, vedere [avvisi](#alerts) per altri dettagli sull'avviso esperienza.  

La tabella seguente descrive gli stati di integrità definiti per una macchina virtuale: 

|Icona |Stato di integrità |Significato |
|-----|-------------|------------|
| |Healthy |Lo stato è Integro se è conforme alle condizioni di integrità definite, e questo indica che per la macchina virtuale non sono stati rilevati problemi e la macchina funziona in modo corretto. Con un monitoraggio rollup padre, esegue il rollup dell'integrità che riflette lo stato peggiore o migliore dell'elemento figlio.|
| |Critico |Lo stato di integrità è critico se non è conforme alla condizione di integrità definita, la quale indica che sono stati rilevati uno o più problemi critici, che devono essere risolti per poter ripristinare il normale funzionamento. Con un monitoraggio rollup padre, esegue il rollup dell'integrità che riflette lo stato peggiore o migliore dell'elemento figlio.|
| |Avviso |Lo stato di integrità è definito come Avviso se è compreso tra due valori soglia della condizione di integrità definita, dove uno indica uno stato *Avviso* e l'altro indica uno stato *Critico* (è possibile configurare tre soglie per lo stato di integrità), oppure quando viene rilevato un problema non critico che può causare problemi critici se non risolto. Con un rollup padre monitor, se uno o più elementi figlio è in uno stato di avviso, quindi padre rifletterà *avviso* dello stato. Se è presente un elemento figlio in stato *Critico* e un altro in stato *Avviso*, il rollup padre mostra uno stato di integrità *Critico*.|
| |Sconosciuto |Lo stato di integrità è *Sconosciuto* quando non può essere calcolato per vari motivi, ad esempio non è possibile raccogliere i dati, il servizio non è inizializzato e così via. Questo stato di integrità non è configurabile.| 

Selezionando **Visualizza diagnostica integrità** si apre una pagina che mostra tutti i componenti della macchina virtuale, i criteri di integrità associati, le modifiche dello stato e altri problemi importanti rilevati dal monitoraggio dei componenti correlati alla macchina virtuale. Per altre informazioni, vedere [Diagnostica integrità](#health-diagnostics). 

Nella sezione **Integrità componenti** la tabella mostra uno stato di rollup dell'integrità delle categorie di prestazioni primarie monitorate dai criteri di integrità per tali aree, in particolare **CPU**,  **Memoria**, **Disco** e **Rete**.  Selezionando uno dei componenti si apre una pagina che elenca tutti gli aspetti di monitoraggio di ciascun criterio di integrità del componente e il rispettivo stato di integrità di ciascuno.  

Quando si accede integrità da una VM di Azure che eseguono il sistema operativo Windows, lo stato di integrità dei primi cinque funzionalità di base Windows vengono visualizzati nella sezione **integrità dei servizi di base**.  Selezionando uno dei servizi si apre una pagina in cui sono elencati i criteri di integrità che monitorano il componente e il relativo stato di integrità.  Fare clic sul nome dei criteri di integrità per aprire il riquadro delle proprietà da cui è possibile esaminare i dettagli di configurazione, incluso il fatto che sia stato definito un avviso di Monitoraggio di Azure per i criteri di integrità. Per altre informazioni, vedere [Diagnostica dell'integrità e uso dei criteri di integrità](#health-diagnostics).  

## <a name="aggregate-virtual-machine-perspective"></a>Prospettiva delle macchine virtuali aggregate

Per visualizzare la raccolta di integrità per tutte le macchine virtuali in un gruppo di risorse, dall'elenco spostamenti del portale selezionare **Monitoraggio di Azure**, quindi **Macchine virtuali (anteprima)**.  

![Visualizzazione del monitoraggio di Informazioni dettagliate macchina virtuale da Monitoraggio di Azure](./media/vminsights-health/vminsights-aggregate-health.png)

Dagli elenchi a discesa **Sottoscrizione** e **Gruppo di risorse** selezionare il gruppo di risorse appropriato, in cui sono incluse le macchine virtuali correlate al gruppo, per visualizzarne lo stato di integrità.  La selezione è valida solo per la funzionalità Integrità e non si applica alla funzionalità Prestazioni o Mappa.

Dalla scheda **Integrità** si apprende quanto segue:

* Quante macchine virtuali si trovano in uno stato critico o non integro e quante sono integre o non inviano dati (ossia si trovano in uno stato sconosciuto)?
* Quali e quante macchine virtuali per sistema operativo segnalano uno stato non integro?
* Quante macchine virtuali sono nello stato non integro a causa di un problema relativo a processore, disco, memoria o scheda di rete, classificate in base allo stato di integrità?  
* Quante macchine virtuali sono nello stato non integro a causa di un problema relativo a un servizio di base del sistema operativo, classificate in base allo stato di integrità?

Qui è possibile identificare rapidamente i principali problemi critici rilevati dai criteri di integrità in modo proattivo monitorando la macchina virtuale ed esaminare i dettagli degli avvisi di integrità della macchina virtuale e l'articolo associato della Knowledge Base che offre assistenza nella diagnosi e nella correzione del problema.  Selezionare uno dei valori di gravità per aprire la pagina [Tutti gli avvisi](../../azure-monitor/platform/alerts-overview.md#all-alerts-page) filtrata in base al livello di gravità specificato.

L'elenco **Distribuzione delle macchine virtuali - Sistema operativo** mostra le macchine virtuali elencate in base alla versione di Windows o alla distribuzione di Linux, insieme alla relativa versione. In ogni categoria di sistema operativo, le macchine virtuali sono suddivise ulteriormente in base alla relativa integrità. 

![Prospettiva della distribuzione delle macchine virtuali in Informazioni dettagliate macchina virtuale](./media/vminsights-health/vminsights-vmdistribution-by-os.png)

È possibile fare clic su qualsiasi elemento di colonna, ad esempio **Numero di macchine virtuali**, **Critico**, **Avviso**, **Integro** o **Sconosciuto** per eseguire il drill-down nella pagina **Macchine virtuali** e visualizzare un elenco dei risultati filtrati corrispondenti alla colonna selezionata. Ad esempio, per esaminare tutte le macchine virtuali che eseguono **Red Hat Enterprise Linux versione 7.5**, fare clic sul valore **Numero di macchine virtuali** per tale sistema operativo e aprire così la paina seguente, con l'elenco delle macchine virtuali corrispondenti al filtro e il relativo stato di integrità noto.  

![Esempio di rollup delle macchine virtuali Red Hat Linux](./media/vminsights-health/vminsights-rollup-vm-rehl-01.png)
 
Se si seleziona il nome di una macchina virtuale nella colonna **Nome macchina virtuale** nella pagina **Macchine virtuali**, si viene reindirizzati alla pagina dell'istanza della macchina virtuale con ulteriori dettagli degli avvisi e dei problemi dei criteri di integrità individuati che interessano la macchina virtuale selezionata.  Da qui è possibile filtrare i dettagli dello stato di integrità facendo clic sull'icona **Stato di integrità** nell'angolo superiore sinistro della pagina per vedere i componenti non integri o visualizzare gli avvisi di integrità della macchina virtuale generati da un componente non integro classificati in base alla gravità dell'avviso.    

Facendo clic sul nome di una macchina virtuale dalla vista elenco delle macchine virtuali si apre la pagina **Integrità** per la macchina virtuale selezionata, come quando si seleziona **Informazioni dettagliate (anteprima)** direttamente dalla macchina virtuale.

![Informazioni dettagliate macchina virtuale di una macchina virtuale di Azure selezionata](./media/vminsights-health/vminsights-directvm-health.png)

Di seguito viene presentato uno **Stato integrità** rollup per la macchina virtuale e gli **Avvisi**, classificati in base alla gravità, che sono stati generati quando lo stato di integrità è passato da integro a non integro per un criterio di integrità.  Selezionando **Macchine virtuali in condizioni critiche** si apre una pagina con un elenco di una o più macchine virtuali in uno stato di integrità critico.  Facendo clic sullo stato di integrità per una delle macchine virtuali nell'elenco verrà mostrata la visualizzazione **Diagnostica integrità** della macchina virtuale.  Qui è possibile individuare i criteri di integrità che riflettono un problema relativo allo stato di integrità. Quando si apre la pagina **Diagnostica integrità** vengono mostrati tutti i componenti della macchina virtuale e i relativi criteri di integrità associati con lo stato di integrità corrente. Per altre informazioni, vedere [integrità diagnostica](#health-diagnostics).  

Selezionando **Visualizza tutti i criteri di integrità** si apre una pagina con un elenco di tutti i criteri di integrità disponibili con questa funzionalità.  Le informazioni possono essere ulteriormente filtrate in base alle opzioni seguenti:

* **Tipo**: sono disponibili tre tipi di criteri di integrità per valutare le condizioni ed eseguire il rollup dello stato di integrità globale della macchina virtuale monitorata.  
    a. **Unità**: misura alcuni aspetti della macchina virtuale. Questo tipo di criterio di integrità potrebbe controllare un contatore delle prestazioni per determinare le prestazioni del componente, eseguire uno script per effettuare una transazione sintetica o cercare un evento che indichi un errore.  Per impostazione predefinita il filtro è impostato su unità.  
    b. **Dipendenza**: fornisce il rollup dell'integrità tra diverse entità. Questo criterio di integrità consente la dipendenza dell'integrità di un'entità dall'integrità di un altro tipo di entità su cui si basa per l'esito positivo dell'operazione.  
    c. **Aggregato**: fornisce uno stato di integrità combinato di criteri di integrità simili. I criteri di integrità unità e dipendenza sono in genere configurati in un criterio di integrità aggregato. Oltre a fornire una migliore organizzazione generale dei diversi criteri di integrità destinati a un'entità, il criterio di integrità aggregato fornisce uno stato di integrità univoco per categorie distinte di entità.

* **Categoria**: tipo di criterio di integrità usato per raggruppare i criteri di tipo simile al fine di creare rapporti.  Possono essere **Disponibilità** o **Prestazioni**.

È possibile scorrere ulteriormente verso il basso per visualizzare le istanze non integre facendo clic su un valore nella colonna **Componente non integro**.  Nella pagina una tabella elenca i componenti il cui stato di integrità è critico.    

## <a name="health-diagnostics"></a>Diagnostica integrità

La pagina **Diagnostica integrità** consente di visualizzare il modello di integrità della macchina virtuale, di elencare tutti i componenti della macchina virtuale, i criteri di integrità associati, le modifiche stato e altri problemi significativi rilevati da componenti monitorati correlati alla macchina virtuale.

![Esempio di pagina Diagnostica integrità per una macchina virtuale](./media/vminsights-health/health-diagnostics-page-01.png)

È possibile avviare Diagnostica integrità nei modi seguenti.

* Dallo stato di integrità di rollup per tutte le macchine virtuali dalla prospettiva delle macchine virtuali aggregate in Monitoraggio di Azure.  Nella pagina **Integrità** fare clic sull'icona dello stato di integrità **Critico**, **Avviso**, **Integro** o **Sconosciuto** nella sezione **Integrità della macchina virtuale guest** e andare alla pagina che elenca tutte le macchine virtuali corrispondenti alla categoria filtrata.  Facendo clic sul valore nella colonna **Stato di integrità** si apre la pagina Diagnostica integrità nell'ambito della macchina virtuale specifica.      

* Dal sistema operativo dalla prospettiva delle macchine virtuali aggregate in Monitoraggio di Azure. In **Distribuzione delle macchine virtuali** selezionare uno dei valori della colonna per aprire la pagina **Macchine virtuali** e visualizzare un elenco nella tabella corrispondente alla categoria filtrata.  Facendo clic sul valore nella colonna **Stato di integrità** si apre la pagina Diagnostica integrità per la macchina virtuale selezionata.    
 
* Dalla macchina virtuale guest nella scheda **Integrità** di Monitoraggio di Azure per le macchine virtuali selezionando **Visualizza diagnostica integrità** 

Diagnostica integrità organizza le informazioni sull'integrità nelle categorie seguenti: 

* Disponibilità
* Prestazioni
 
Tutti i criteri di integrità definiti per un componente specifico, ad esempio disco logico, CPU e così via possono essere visualizzati senza il filtro in due categorie, ovvero una visualizzazione completa di tutti i criteri, o filtrare i risultati per una categoria quando si seleziona **disponibilità**  oppure **prestazioni** opzioni nella pagina. Inoltre, la categoria dei criteri può essere visualizzata accanto a esso nel **criteri di integrità** colonna. Se i criteri non corrisponde alla categoria selezionata, il messaggio indicherà **alcun criterio di integrità disponibili per la categoria selezionata** nel **criteri di integrità** colonna.  

Lo stato per i criteri di integrità viene definito mediante uno dei quattro stati: *Critico*, *Avviso*, *Integro* e *Sconosciuto*. I primi tre sono configurabili, vale a dire è possibile modificare i valori di soglia per i monitor direttamente nel riquadro di configurazione di criteri di integrità o usando l'API REST di monitoraggio di Azure [monitorare l'operazione di aggiornamento](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/monitors/update). Lo stato *Sconosciuto* non è configurabile ed è riservato per scenari specifici.  

La pagina Diagnostica integrità ha tre sezioni principali:

* Modello componente 
* Criteri di integrità
* Modifiche di stato 

![Sezioni della pagina Diagnostica integrità](./media/vminsights-health/health-diagnostics-page-02.png)

### <a name="component-model"></a>Modello componente

La colonna più a sinistra nella pagina Diagnostica integrità rappresenta il modello componente. Tutti i componenti, associati alla macchina virtuale, sono visualizzati in questa colonna assieme al loro stato di integrità corrente. 

Nell'esempio seguente i componenti individuati sono disco, disco logico, processore, memoria e sistema operativo. In questa colonna vengono individuate e visualizzate più istanze di questi componenti. Ad esempio, l'immagine seguente mostra che la macchina virtuale dispone di due istanze di dischi logici: C: e D:, che sono in uno stato integro.  

![Esempio di modello componente presentato in Diagnostica integrità](./media/vminsights-health/health-diagnostics-page-component.png)

### <a name="health-criteria"></a>Criteri di integrità

La colonna centrale nella pagina Diagnostica integrità è **Criteri di integrità**. Il modello di integrità definito per la macchina virtuale è visualizzato in un albero gerarchico. Il modello di integrità per una macchina virtuale è costituito da unità e criteri di integrità aggregati.  

![Esempio di criterio di integrità presentato in Diagnostica integrità](./media/vminsights-health/health-diagnostics-page-healthcriteria.png)

Un criterio di integrità misura l'integrità dell'istanza monitorata rispetto ad alcuni criteri, che possono essere un valore di soglia specificato, lo stato di un'entità e così via. Un criterio di integrità ha due o tre stati di integrità configurabili come descritto nella sezione precedente. In qualsiasi momento, il criterio di integrità può trovarsi in uno solo dei relativi stati possibili. 

L'integrità globale di una destinazione è determinata dall'integrità di ogni relativo criterio di integrità definito nel modello di integrità. È una combinazione di criteri di integrità destinati direttamente all'obiettivo di criteri di integrità destinati a componenti rollup alla destinazione mediante un criterio di integrità aggregato. Questa gerarchia è illustrata nella sezione **Criteri di integrità** della pagina Diagnostica integrità. I criteri relativi al rollup dell'integrità fanno parte della configurazione dei criteri di integrità di aggregazione (l'impostazione predefinita è *Worst-of*). È possibile trovare un elenco di set predefinito di criteri di integrità in esecuzione come parte di questa funzionalità nella sezione [dettagli di configurazione di monitoraggio](#monitoring-configuration-details), ed è possibile usare l'API REST di monitoraggio di Azure [monitorare istanze - elenco in base alla risorsa operazione](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/monitorinstances/listbyresource) per ottenere un elenco di tutti i criteri di integrità e la configurazione dettagliata in esecuzione sulla risorsa della macchina virtuale di Azure.  

È possibile modificare la configurazione dei criteri di integrità di tipo **Unità** facendo clic sul collegamento con i puntini di sospensione a destra e selezionando **Mostra dettagli** per aprire il riquadro di configurazione. 

![Configurazione di un esempio di criteri di integrità](./media/vminsights-health/health-diagnostics-vm-example-02.png)

Nel riquadro di configurazione per i criteri di integrità selezionato, usando l'esempio **Media scritture disco/secondi**, la soglia può essere configurata con un valore numerico diverso. È un monitoraggio a due stati, il che significa che passa solo da integro ad avviso. Un altro criterio di integrità può avere tre stati e consentire la configurazione di un valore per la soglia tra stato di integrità Avviso e stato Critico. È inoltre possibile modificare la soglia usando l'API REST di monitoraggio di Azure [monitorare l'operazione di aggiornamento](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/monitors/update).

>[!NOTE]
>La modifica di configurazione dei criteri di integrità a un'istanza viene applicata a tutte le istanze monitorate.  Se ad esempio si seleziona **Disk -1 D:** e si modifica la soglia **Media scritture disco/secondi**, la modifica non si applica solo a tale istanza, ma a tutte le altre istanze di disco individuate e monitorate nella macchina virtuale.
>

![Configurazione di criteri di integrità per un esempio di monitoraggio unità](./media/vminsights-health/health-diagnostics-criteria-config-01.png)

Per altre informazioni sull'indicatore di integrità, sono inclusi articoli della Knowledge Base sull'identificazione di problemi, cause e risoluzioni. Fare clic sul collegamento **Visualizza informazioni** nella pagina per aprire una nuova scheda del browser che mostra lo specifico articolo della Knowledge Base. In qualsiasi momento, è possibile esaminare tutti gli articoli della Knowledge Base sui criteri di integrità inclusi con la funzionalità dell'integrità di Monitoraggio di Azure per le macchine virtuali [qui](https://docs.microsoft.com/azure/monitoring/infrastructure-health/).
  
### <a name="state-changes"></a>Modifiche stato

La colonna più a destra nella pagina Diagnostica integrità è **Modifiche stato**. Elenca tutte le modifiche di state associate ai criteri di integrità selezionati nella sezione **Criteri di integrità** o la modifica di stato della macchina virtuale se ne è stata selezionata una nella colonna **Modello componente** o **Criteri di integrità** della tabella. 

![Esempio di modifiche di stato presentato in Diagnostica integrità](./media/vminsights-health/health-diagnostics-page-statechanges.png)

Questa sezione è costituita dallo stato dei criteri di integrità e dall'ora associata ordinati con lo stato più recente in alto.   

### <a name="association-of-component-model-health-criteria-and-state-change-columns"></a>Associazione del modello del componente Criteri di integrità e dello stato modificare colonne 

Le tre colonne sono correlate tra loro. Quando un utente seleziona un'istanza individuata nella sezione **Modello componente**, la sezione **Criteri di integrità** viene filtrata in base alla visualizzazione di tale componente e, di conseguenza, la sezione **Modifica stato** viene aggiornata in base ai criteri di integrità selezionati. 

![Esempio di selezione dell'istanza monitorata e dei risultati](./media/vminsights-health/health-diagnostics-vm-example-01.png)

Nell'esempio precedente, quando si seleziona **Disk - 1 D:**, l'albero Criteri di integrità viene filtrato in base a **Disk - 1 D:**. La colonna **State Change** (Modifica stato) indica il cambiamento dello stato in base alla disponibilità di **Disk - 1 D:**. 

Per visualizzare lo stato di integrità aggiornato, è possibile aggiornare la pagina Diagnostica integrità facendo clic sul collegamento **Aggiorna**.  Se lo stato di integrità del criterio di integrità viene aggiornato in base all'intervallo di polling predefinito, questa attività consente di evitare l'attesa e riflette lo stato di integrità più recente.  Il filtro **Stato criteri di integrità** consente di esaminare i risultati in base allo stato di integrità selezionato: *Integro*, *Avviso*, *Critico*, *Sconosciuti* e *tutti*.  L'ora riportata in **Ultimo aggiornamento** nell'angolo in alto a destra indica l'ora dell'ultimo aggiornamento della pagina Diagnostica integrità.  

## <a name="alerts"></a>Avvisi

La funzionalità dell'integrità di Monitoraggio di Azure per le macchine virtuali è integrata ad [Avvisi di Azure](../../azure-monitor/platform/alerts-overview.md) e genera un avviso quando i criteri di integrità predefiniti passano da uno stato integro a uno non integro quando viene rilevata la condizione. Gli avvisi sono classificati in base alla gravità, da 0 a 4 dove la gravità 0 rappresenta il livello più alto.  

Il numero totale di avvisi di integrità delle macchine virtuali classificati in base alla gravità è disponibile nel dashboard **Integrità** nella sezione **Avvisi**. Quando si seleziona il numero totale di avvisi o il numero corrispondente a un livello di gravità, si apre la pagina **Avvisi** in cui sono elencati tutti gli avvisi corrispondenti alla selezione.  Ad esempio, se si seleziona la riga corrispondente al **livello di gravità 1**, viene visualizzato quanto segue:

![Esempio di tutti gli avvisi con livello di gravità 1](./media/vminsights-health/vminsights-sev1-alerts-01.png)

Nella pagina **Avvisi** non vengono solo visualizzati gli avvisi corrispondenti alla sezione, ma viene anche applicato il filtro **Tipo di risorsa**, che consente di visualizzare solo gli avvisi di integrità generati dalla risorsa macchina virtuale.  Questa modifica è riflessa nell'elenco degli avvisi, nella colonna **risorsa di destinazione**, in cui illustra la macchina virtuale di Azure è stato generato l'avviso per quando tipo non integro condizione dei criteri di integrità specifico è stata soddisfatta.  

Gli avvisi provenienti da altri tipi di risorse o servizi non devono essere inclusi in questa visualizzazione, ad esempio gli avvisi di log basati su query di log o avvisi sulle metriche che verrebbero normalmente visualizzati da quello predefinito di monitoraggio di Azure [tutti gli avvisi](../../azure-monitor/platform/alerts-overview.md#all-alerts-page) pagina. 

È possibile filtrare questa visualizzazione selezionando i valori nei menu a discesa nella parte superiore della pagina.

|Colonna |DESCRIZIONE | 
|-------|------------| 
|Sottoscrizione |Selezionare una sottoscrizione di Azure. Sono inclusi nella visualizzazione solo gli avvisi della sottoscrizione selezionata. | 
|Gruppo di risorse |Selezionare un singolo gruppo di risorse. Sono inclusi nella visualizzazione solo gli avvisi con destinazioni nel gruppo di risorse selezionato. | 
|Tipo di risorsa |Selezionare uno o più tipi di risorsa. Per impostazione predefinita, in questa visualizzazione sono selezionati e inclusi solo gli avvisi della **Macchina virtuale** di destinazione. Questa colonna risulta disponibile solo dopo che è stato specificato un gruppo di risorse. | 
|Risorsa |Selezionare una risorsa. Nella visualizzazione vengono inclusi solo gli avvisi con tale risorsa definita come destinazione. Questa colonna risulta disponibile solo dopo che è stato specificato un tipo di risorsa. | 
|Gravità |Selezionare un livello di gravità degli avvisi oppure *Tutti* per includere gli avvisi di tutti i livelli di gravità. | 
|Condizione del monitoraggio |Selezionare una condizione di monitoraggio per filtrare gli avvisi se sono stati *Attivati* dal sistema oppure *Risolti* dal sistema se la condizione non è più attiva. In alternativa selezionare *Tutti* per includere gli avvisi di tutte le condizioni. | 
|Stato dell'avviso |Selezionare uno stato dell'avviso, *Nuovo*, *Conferma*, *Chiuso*, oppure selezionare *Tutti* per includere gli avvisi di tutti gli stati. | 
|Servizio di monitoraggio |Selezionare un servizio oppure *Tutti* per includere tutti i servizi. Per questa funzionalità sono supportati solo gli avvisi generati da *Informazioni dettagliate macchina virtuale*.| 
|Intervallo di tempo| Nella visualizzazione vengono inclusi solo gli avvisi attivati nell'intervallo di tempo selezionato. I valori supportati sono l'ultima ora, le ultime 24 ore, gli ultimi 7 giorni e gli ultimi 30 giorni. | 

La pagina **Dettagli avviso** viene visualizzata quando si seleziona un avviso, fornendo i dettagli dell'avviso e consentendo di modificarne lo stato. Per altre informazioni sulla gestione degli avvisi, vedere [Creare, visualizzare e gestire gli avvisi tramite Monitoraggio di Azure](../../azure-monitor/platform/alerts-metric.md).  

>[!NOTE]
>Attualmente non è supportata la creazione di nuovi avvisi in base ai criteri di integrità o la modifica di regole di avvisi di integrità esistenti in Monitoraggio di Azure direttamente dal portale.  
>

![Riquadro Dettagli avvisi per un avviso selezionato](./media/vminsights-health/alert-details-pane-01.png)

Lo stato dell'avviso può anche essere modificato per uno o più avvisi selezionandolo e selezionando **Modifica stato** dalla pagina **Tutti gli avvisi** nell'angolo in alto a sinistra. Nel riquadro **Modifica stato avviso** selezionare uno degli stati, aggiungere una descrizione della modifica nel campo **Commento**, quindi fare clic su **OK** per confermare le modifiche. Durante la verifica delle informazioni e l'applicazione delle modifiche, è possibile tenere traccia dell'avanzamento scegliendo **Notifiche** dal menu.  

### <a name="configure-alerts"></a>Configurare gli avvisi
Alcuni avvisi Gestione attività non possono essere gestite dal portale di Azure e devono essere eseguite usando il [API REST di monitoraggio di Azure](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/components). In particolare:

- Abilitazione o disabilitazione di un avviso per i criteri di integrità 
- Configurare le notifiche per gli avvisi di integrità criteri 

Usa l'approccio usato in ogni esempio [ARMClient](https://github.com/projectkudu/armclient) nel computer Windows. Se non ha familiarità con questo metodo, vedere [ARMClient usando](../platform/rest-api-walkthrough.md#use-armclient).  

#### <a name="enable-or-disable-alert-rule"></a>Abilitare o disabilitare la regola di avviso

Per abilitare o disabilitare un avviso per un criterio di integrità specifico, la proprietà di criteri di integrità *alertGeneration* deve essere modificato con un valore di entrambi **disabilitato** o **abilitato**. Per identificare il *monitorId* di un criterio di integrità specifico, nell'esempio seguente mostrerà come eseguire una query per tale valore per i criteri **Logico\media trasferimenti disco/secondi**.

1. In una finestra del terminale digitare **armclient.exe login**. In questo modo viene richiesto di accedere ad Azure.

2. Digitare il comando seguente per recuperare tutti i criteri di integrità attivi in una macchina virtuale specifica e identificare il valore per *monitorId* proprietà. 

    ```
    armclient GET "subscriptions/subscriptionId/resourceGroups/resourcegroupName/providers/Microsoft.Compute/virtualMachines/vmName/providers/Microsoft.WorkloadMonitor/monitors?api-version=2018-08-31-preview”
    ```

    Nell'esempio seguente mostra l'output del comando. Prendere nota del valore del *MonitorId*. Questo valore è obbligatorio per il passaggio successivo in cui è necessario specificare l'ID dei criteri di integrità e modificare le proprietà per creare un avviso.

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

3. Digitare il comando seguente per modificare la *alertGeneration* proprietà.

    ```
    armclient patch subscriptions/subscriptionId/resourceGroups/resourcegroupName/providers/Microsoft.Compute/virtualMachines/vmName/providers/Microsoft.WorkloadMonitor/monitors/Microsoft_LogicalDisk_AvgDiskSecPerTransfer?api-version=2018-08-31-preview "{'properties':{'alertGeneration':'Disabled'}}"
    ```   

4. Digitare il comando GET usato nel passaggio 2 per verificare il valore della proprietà è impostato su **disabilitato**.  

#### <a name="associate-action-group-with-health-criteria"></a>Associare i criteri di integrità gruppo di azioni

Monitoraggio di integrità delle VM di Azure supporta le notifiche di posta elettronica e SMS quando vengono generati avvisi quando i criteri di integrità diventa non integro. Per configurare le notifiche, è necessario annotare il nome del gruppo di azioni che è configurato per l'invio di notifiche di posta elettronica o SMS. 

>[!NOTE]
>Questa azione deve essere eseguita su ogni macchina virtuale monitorati che si desidera ricevere una notifica per.

1. In una finestra del terminale digitare **armclient.exe login**. In questo modo viene richiesto di accedere ad Azure.

2. Digitare il comando seguente per associare un gruppo di azioni delle regole di avviso.
 
    ```
    $payload = "{'properties':{'ActionGroupResourceIds':['/subscriptions/subscriptionId/resourceGroups/resourcegroupName/providers/microsoft.insights/actionGroups/actiongroupName']}}" 
    armclient PUT https://management.azure.com/subscriptions/subscriptionId/resourceGroups/resourcegroupName/providers/Microsoft.Compute/virtualMachines/vmName/providers/Microsoft.WorkloadMonitor/notificationSettings/default?api-version=2018-08-31-preview $payload
    ```

3. Per verificare il valore della proprietà **actionGroupResourceIds** è stato aggiornato, digitare il comando seguente.

    ```
    armclient GET "subscriptions/subscriptionName/resourceGroups/resourcegroupName/providers/Microsoft.Compute/virtualMachines/vmName/providers/Microsoft.WorkloadMonitor/notificationSettings?api-version=2018-08-31-preview"
    ```

    L'output sarà simile al seguente:
    
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

Per individuare i colli di bottiglia e l'utilizzo generale con le prestazioni delle macchine virtuali, vedere [Visualizzare le prestazioni delle macchine virtuali di Azure](vminsights-performance.md); per visualizzare le dipendenze dell'applicazione individuate, vedere [Visualizzare la mappa di Monitoraggio di Azure per le macchine virtuali](vminsights-maps.md). 
