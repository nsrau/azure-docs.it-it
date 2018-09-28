---
title: Monitorare l'integrità della macchina virtuale con Monitoraggio di Azure per le macchine virtuali | Microsoft Docs
description: Questo articolo descrive come comprendere l'integrità della macchina virtuale e del sistema operativo sottostante con Monitoraggio di Azure per le macchine virtuali.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/18/2018
ms.author: magoedte
ms.openlocfilehash: c8a8598640e31f59476b5b3351fdb2eab7b66a6c
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/24/2018
ms.locfileid: "46952920"
---
# <a name="understand-the-health-of-your-azure-virtual-machines-with-azure-monitor-for-vms"></a>Comprendere l'integrità delle macchine virtuali di Azure con Monitoraggio di Azure per le macchine virtuali
Azure include più servizi che singolarmente eseguono un'attività o un ruolo specifico nello spazio di monitoraggio, ma la possibilità di avere una prospettiva approfondita sull'integrità del sistema operativo ospitato nelle macchine virtuali di Azure non era disponibile.  Anche se è possibile monitorare diverse condizioni utilizzando Log Analytics o Monitoraggio di Azure, queste applicazioni non sono progettate per modellare e rappresentare l'integrità dei componenti di base o quella globale della macchina virtuale.  Con la funzionalità dell'integrità di Monitoraggio di Azure per macchine virtuali, è possibile monitorare in modo proattivo la disponibilità e le prestazioni dei sistemi operativi guest Windows o Linux con un modello che rappresenta i componenti principali e le relative relazioni, criteri che specificano come misurare l'integrità di tali componenti e avvisi che segnalano quando viene rilevata una condizione di non integrità.  

Lo stato di integrità globale della macchina virtuale di Azure e del sistema operativo sottostante può essere visualizzato da due prospettive con Monitoraggio di Azure per le macchine virtuali, direttamente dalla macchina virtuale o per tutte le macchine virtuali in un gruppo di risorse da Monitoraggio di Azure.

Questo articolo illustra come valutare, analizzare e risolvere rapidamente i problemi di integrità rilevati.

Per informazioni sulla configurazione di Monitoraggio di Azure per le macchine virtuali, vedere [Abilitare Monitoraggio di Azure per le macchine virtuali](monitoring-vminsights-onboard.md).

## <a name="monitoring-configuration-details"></a>Dettagli di configurazione di monitoraggio
Questa sezione descrive i criteri di integrità predefiniti per monitorare le macchine virtuali Linux e Windows Azure.

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
- Percentuale tempo di inattività del disco fisico
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
- Percentuale tempo DPC processore
- Percentuale tempo processore
- Percentuale tempo processore totale
- Percentuale tempo DPC totale
- Megabyte disponibili per la memoria del sistema operativo

## <a name="sign-in-to-the-azure-portal"></a>Accedere al portale di Azure
Accedere al [portale di Azure](https://portal.azure.com). 

## <a name="introduction-to-health-experience"></a>Introduzione all'esperienza dell'integrità
Prima di approfondire l'uso della funzionalità dell'integrità per una singola macchina virtuale o per un gruppo di macchine virtuali, è importante fornire una breve introduzione su come vengono presentate le informazioni e su ciò che rappresentano le visualizzazioni.  

## <a name="view-health-directly-from-a-virtual-machine"></a>Visualizzare l'integrità direttamente da una macchina virtuale 
Per visualizzare l'integrità di una macchina virtuale di Azure, selezionare **Informazioni dettagliate (anteprima)** nel riquadro a sinistra della macchina virtuale. Nella pagina Informazioni dettagliate macchina virtuale, la scheda **Integrità** è aperta per impostazione predefinita e mostra la visualizzazione dell'integrità della macchina virtuale.  

![Panoramica dell'integrità di Monitoraggio di Azure per macchine virtuali per una macchina virutale di Azure selezionata](./media/monitoring-vminsights-health/vminsights-directvm-health.png)

Nella sezione **Integrità della macchina virtuale guest** della scheda **Integrità**, la tabella mostra lo stato di integrità corrente della macchina virtuale e il numero totale di avvisi sull'integrità della macchina virtuale generati da un componente non integro. Per ulteriori dettagli, fare riferimento a [Avvisi e gestione degli avvisi](#alerting-and-alert-management).  

Gli stati di integrità definiti per una macchina virtuale sono: 

* **Integro**: nessun problema rilevato per la macchina virtuale che funziona come richiesto.  
* **Critico**: sono stati rilevati uno o più problemi critici che devono essere risolti per ripristinare il normale funzionamento come previsto. 
* **Avviso**: sono stati rilevati uno o più problemi che devono essere risolti per evitare che la condizione di integrità diventi critica.  
* **Sconosciuto**: se non è stato possibile stabilire una connessione con la macchina virtuale, viene impostato uno stato sconosciuto.  

Selezionando **Visualizza diagnostica integrità** si apre una pagina che mostra tutti i componenti della macchina virtuale, i criteri di integrità associati, le modifiche dello stato e altri problemi importanti rilevati dal monitoraggio dei componenti correlati alla macchina virtuale. Per ulteriori dettagli, fare riferimento a [Diagnostica integrità](#health-diagnostics). 

Nella sezione **Integrità componenti** la tabella mostra uno stato di rollup dell'integrità delle categorie di prestazioni primarie monitorate dai criteri di integrità per tali aree, in particolare **CPU**,  **Memoria**, **Disco** e **Rete**.  Selezionando uno dei componenti si apre una pagina che elenca tutti gli aspetti di monitoraggio di ciascun criterio di integrità del componente e il rispettivo stato di integrità di ciascuno.  

Quando si accede a Integrità da una macchina virtuale di Azure che esegue il sistema operativo Windows, lo stato di integrità dei cinque servizi di base principali di Windows è mostrato nella sezione **Integrità dei servizi di base**.  Selezionando uno dei servizi si apre una pagina in cui sono elencati i criteri di integrità che monitorano il componente e il relativo stato di integrità.  Fare clic sul nome dei criteri di integrità per aprire il riquadro delle proprietà da cui è possibile esaminare i dettagli di configurazione, incluso il fatto che sia stato definito un avviso di Monitoraggio di Azure per i criteri di integrità. Per ulteriori informazioni, vedere [Diagnostica dell'integrità e uso dei criteri di integrità](#health-diagnostics).  

## <a name="aggregate-virtual-machine-perspective"></a>Prospettiva delle macchine virtuali aggregate
Per visualizzare la raccolta di integrità per tutte le macchine virtuali in un gruppo di risorse, dall'elenco spostamenti del portale selezionare **Monitoraggio di Azure**, quindi **Macchine virtuali (anteprima)**.  

![Visualizzazione del monitoraggio di Informazioni dettagliate macchina virtuale da Monitoraggio di Azure](./media/monitoring-vminsights-health/vminsights-aggregate-health.png)

Dagli elenchi a discesa **Sottoscrizione** e **Gruppo di risorse** selezionare l'opzione appropriata che include le macchine virtuali di destinazione caricate per visualizzarne lo stato di integrità. 

Dalla scheda **Integrità** si apprende quanto segue:

* Quante macchine virtuali si trovano in uno stato critico o non integro e quante sono integre o non inviano dati (ossia si trovano in uno stato sconosciuto)?
* Quali e quante macchine virtuali per sistema operativo segnalano uno stato non integro?
* Quante macchine virtuali sono nello stato non integro a causa di un problema relativo a processore, disco, memoria o scheda di rete, classificate in base allo stato di integrità?  
* Quante macchine virtuali sono nello stato non integro a causa di un problema relativo a un servizio di base del sistema operativo, classificate in base allo stato di integrità?

Qui è possibile identificare rapidamente i principali problemi critici rilevati dai criteri di integrità in modo proattivo monitorando la macchina virtuale ed esaminare i dettagli degli avvisi di integrità della macchina virtuale e l'articolo associato della Knowledge Base che offre assistenza nella diagnosi e nella correzione del problema.  Selezionare uno dei valori di gravità per aprire la pagina [Tutti gli avvisi](../monitoring-and-diagnostics/monitoring-overview-unified-alerts.md#all-alerts-page) filtrata in base al livello di gravità specificato.

L'elenco **Distribuzione delle macchine virtuali - Sistema operativo** mostra le macchine virtuali elencate in base alla versione di Windows o alla distribuzione di Linux, insieme alla relativa versione. In ogni categoria di sistema operativo, le macchine virtuali sono suddivise ulteriormente in base alla relativa integrità. 

![Prospettiva della distribuzione delle macchine virtuali in Informazioni dettagliate macchina virtuale](./media/monitoring-vminsights-health/vminsights-vmdistribution-by-os.png)

Gli stati di integrità definiti per una macchina virtuale sono: 

* **Integro**: nessun problema rilevato per la macchina virtuale che funziona come richiesto.  
* **Critico**: sono stati rilevati uno o più problemi critici che devono essere risolti per ripristinare il normale funzionamento come previsto. 
* **Avviso**: sono stati rilevati uno o più problemi che devono essere risolti per evitare che la condizione di integrità diventi critica.  
* **Sconosciuto**: se non è stato possibile stabilire una connessione con la macchina virtuale, viene impostato uno stato sconosciuto.  

È possibile fare clic su qualsiasi elemento di colonna, ad esempio **Numero di macchine virtuali**, **Critico**, **Avviso**, **Integro** o **Sconosciuto** per eseguire il drill-down nella pagina **Macchine virtuali** e visualizzare un elenco dei risultati filtrati corrispondenti alla colonna selezionata. Ad esempio, per esaminare tutte le macchine virtuali che eseguono **Red Hat Enterprise Linux versione 7.5**, fare clic sul valore **Numero di macchine virtuali** per tale sistema operativo e aprire così la paina seguente, con l'elenco delle macchine virtuali corrispondenti al filtro e il relativo stato di integrità noto.  

![Esempio di rollup delle macchine virtuali Red Hat Linux](./media/monitoring-vminsights-health/vminsights-rollup-vm-rehl-01.png)
 
Se si seleziona il nome di una macchina virtuale nella colonna **Nome macchina virtuale** nella pagina **Macchine virtuali**, si viene reindirizzati alla pagina dell'istanza della macchina virtuale con ulteriori dettagli degli avvisi e dei problemi dei criteri di integrità individuati che interessano la macchina virtuale selezionata.  Da qui è possibile filtrare i dettagli dello stato di integrità facendo clic sull'icona **Stato di integrità** nell'angolo superiore sinistro della pagina per vedere i componenti non integri o visualizzare gli avvisi di integrità della macchina virtuale generati da un componente non integro classificati in base alla gravità dell'avviso.    

Facendo clic sul nome di una macchina virtuale dalla vista elenco delle macchine virtuali si apre la pagina **Integrità** per la macchina virtuale selezionata, come quando si seleziona **Informazioni dettagliate (anteprima)** direttamente dalla macchina virtuale.

![Informazioni dettagliate macchina virtuale di una macchina virtuale di Azure selezionata](./media/monitoring-vminsights-health/vminsights-directvm-health.png)

Di seguito viene presentato uno **Stato integrità** rollup per la macchina virtuale e gli **Avvisi**, classificati in base alla gravità, che sono stati generati quando lo stato di integrità è passato da integro a non integro per un criterio di integrità.  Selezionando **Macchine virtuali in condizioni critiche** si apre una pagina con un elenco di una o più macchine virtuali in uno stato di integrità critico.  Facendo clic sullo stato di integrità per una delle macchine virtuali nell'elenco verrà mostrata la visualizzazione **Diagnostica integrità** della macchina virtuale.  Qui è possibile individuare i criteri di integrità che riflettono un problema relativo allo stato di integrità. Quando si apre la pagina **Diagnostica integrità** vengono mostrati tutti i componenti della macchina virtuale e i relativi criteri di integrità associati con lo stato di integrità corrente.  Per ulteriori dettagli, fare riferimento a [Diagnostica integrità](#health-diagnostics).  

Selezionando **Visualizza tutti i criteri di integrità** si apre una pagina con un elenco di tutti i criteri di integrità disponibili con questa funzionalità.  Le informazioni possono essere ulteriormente filtrate in base alle opzioni seguenti:

* **Tipo**: sono disponibili tre tipi di criteri di integrità per valutare le condizioni ed eseguire il rollup dello stato di integrità globale della macchina virtuale monitorata.  
    a. **Unità**: misura alcuni aspetti della macchina virtuale. Questo tipo di criterio di integrità potrebbe controllare un contatore delle prestazioni per determinare le prestazioni del componente, eseguire uno script per effettuare una transazione sintetica o cercare un evento che indichi un errore.  Per impostazione predefinita il filtro è impostato su unità.  
    b. **Dipendenza**: fornisce il rollup dell'integrità tra diverse entità. Questo criterio di integrità consente la dipendenza dell'integrità di un'entità dall'integrità di un altro tipo di entità su cui si basa per l'esito positivo dell'operazione.  
    c. **Aggregato**: fornisce uno stato di integrità combinato di criteri di integrità simili. I criteri di integrità unità e dipendenza sono in genere configurati in un criterio di integrità aggregato. Oltre a fornire una migliore organizzazione generale dei diversi criteri di integrità destinati a un'entità, il criterio di integrità aggregato fornisce uno stato di integrità univoco per categorie distinte di entità.

* **Categoria**: tipo di criterio di integrità usato per raggruppare i criteri di tipo simile al fine di creare rapporti.  Possono essere **Disponibilità** o **Prestazioni**.

È possibile scorrere ulteriormente verso il basso per visualizzare le istanze non integre facendo clic su un valore nella colonna **Componente non integro**.  Nella pagina una tabella elenca i componenti il cui stato di integrità è critico.    

## <a name="health-diagnostics"></a>Diagnostica integrità
La pagina **Diagnostica integrità** consente di visualizzare tutti i componenti della macchina virtuale, i criteri di integrità associati, le modifiche dello stato e altri problemi importanti rilevati dal monitoraggio degli oggetti correlati alla macchina virtuale. 

![Esempio di pagina Diagnostica integrità per una macchina virtuale](./media/monitoring-vminsights-health/health-diagnostics-page-01.png)

È possibile avviare Diagnostica integrità nei modi seguenti.

* Dallo stato di integrità di rollup per tutte le macchine virtuali dalla prospettiva delle macchine virtuali aggregate in Monitoraggio di Azure.  Nella pagina **Integrità** fare clic sull'icona dello stato di integrità **Critico**, **Avviso**, **Integro** o **Sconosciuto** nella sezione **Integrità della macchina virtuale guest** e andare alla pagina che elenca tutte le macchine virtuali corrispondenti alla categoria filtrata.  Facendo clic sul valore nella colonna **Stato di integrità** si apre la pagina Diagnostica integrità nell'ambito della macchina virtuale specifica.      

* Dal sistema operativo dalla prospettiva delle macchine virtuali aggregate in Monitoraggio di Azure. In **Distribuzione delle macchine virtuali** selezionare uno dei valori della colonna per aprire la pagina **Macchine virtuali** e visualizzare un elenco nella tabella corrispondente alla categoria filtrata.  Facendo clic sul valore nella colonna **Stato integrità** si apre la pagina Diagnostica integrità per la macchina virtuale selezionata.    
 
* Dalla macchina virtuale guest nella scheda **Integrità** di Monitoraggio di Azure per le macchine virtuali selezionando **Visualizza diagnostica integrità** 

Diagnostica integrità organizza le informazioni sull'integrità nelle categorie seguenti: 

* Disponibilità
* Prestazioni
 
Tutti i criteri di integrità definiti per una destinazione selezionata vengono visualizzate nella categoria appropriata. 

Lo stato di integrità per i criteri di integrità viene definito mediante uno dei tre stati: *Critico*, *Avviso* e *Integro*. Lo stato *Sconosciuto* non è associato allo stato di integrità, ma rappresenta lo stato di monitoraggio noto dalla funzionalità.  

La tabella seguente fornisce dettagli sugli stati di integrità rappresentati in Diagnostica integrità.

|Icona |Stato di integrità |Significato |
|-----|-------------|------------|
| |Healthy |Lo stato è integro se soddisfa le condizioni di integrità definite. Nel caso di un monitoraggio rollup padre, l'integrità esegue il rollup e riflette lo stato migliore o quello peggiore dell'elemento figlio.|
| |Critico |Lo stato di integrità è critico se non soddisfa la condizione di integrità definita. Nel caso di un monitoraggio rollup padre, l'integrità esegue il rollup e riflette lo stato migliore o quello peggiore dell'elemento figlio.|
| |Avviso |Lo stato di integrità è avviso se è compreso tra due valori di soglia per la condizione di integrità definita, di cui uno indica uno stato *Avviso* e l'altro uno stato *Critico*. In caso di monitoraggio rollup padre, se uno o più elementi figlio sono in stato *Avviso*, il padre rifletterà tale stato. Se è presente un elemento figlio in stato *Critico* e un altro in stato *Avviso*, il rollup padre mostra uno stato di integrità *Critico*.|
| |Sconosciuto |Lo stato di integrità è *Sconosciuto* quando non può essere calcolato per vari motivi, ad esempio non è possibile raccogliere i dati, il servizio non è inizializzato e così via.| 

La pagina Diagnostica integrità ha tre sezioni principali:

* Modello componente 
* Criteri di integrità
* Modifiche di stato 

![Sezioni della pagina Diagnostica integrità](./media/monitoring-vminsights-health/health-diagnostics-page-02.png)

### <a name="component-model"></a>Modello componente
La colonna più a sinistra in una pagina Diagnostica integrità è il modello componente. Tutti i componenti e le istanze individuate, associate alla macchina virtuale, sono visualizzati in questa colonna. 

Nell'esempio seguente i componenti individuati sono disco, disco logico, processore, memoria e sistema operativo. In questa colonna sono individuate e visualizzate più istanze di questi componenti, con due istanze di disco logico **/**, **/boot** e **/mnt/resource**, un'istanza di scheda di rete **eth0**, due istanze di disco **sda** e **sdb**, due istanze di processore **0 e 1** e un'istanza di sistema operativo **Red Hat Enterprise Linux Server versione 7.4 (Maipo)**. 

![Esempio di modello componente presentato in Diagnostica integrità](./media/monitoring-vminsights-health/health-diagnostics-page-component.png)

### <a name="health-criteria"></a>Criteri di integrità
La colonna centrale nella pagina Diagnostica integrità è **Criteri di integrità**. Il modello di integrità definito per la macchina virtuale è visualizzato in un albero gerarchico. Il modello di integrità per una macchina virtuale è costituito da unità, dipendenza e criteri di integrità aggregati.  

![Esempio di criterio di integrità presentato in Diagnostica integrità](./media/monitoring-vminsights-health/health-diagnostics-page-healthcriteria.png)

Un criterio di integrità misura l'integrità dell'istanza monitorata rispetto ad alcuni criteri, che possono essere un valore di soglia specificato o uno stato di un'entità e così via. Un criterio di integrità ha due o tre stati di integrità come descritto nella sezione precedente. In qualsiasi momento, il criterio di integrità può trovarsi in uno solo dei relativi stati possibili. 

L'integrità globale di una destinazione è determinata dall'integrità di ciascuno dei relativi criteri di integrità definiti nel modello di integrità. Sarà una combinazione di criteri di integrità mirati direttamente alla destinazione, criteri di integrità mirati ai componenti con rollup alla destinazione mediante un criterio di integrità di dipendenza. Questa gerarchia è illustrata nella sezione **Criteri di integrità** della pagina Diagnostica integrità. I criteri relativi al rollup dell'integrità fanno parte della configurazione dei criteri di integrità aggregati e di dipendenza. Un elenco del set predefinito dei criteri di integrità in esecuzione come parte di questa funzionalità è disponibile nella sezione [Dettagli di configurazione di monitoraggio](#monitoring-configuration-details).  

Nell'esempio seguente il criterio di integrità aggregato **Rollup servizi Windows Core** per una macchina virtuale basata su Windows, valuta l'integrità dei servizi Windows più critici in base a criteri di integrità dei singoli servizi. Lo stato di ogni servizio, ad esempio DNS, DHCP e così via, viene valutato e viene eseguito il rollup dell'integrità in base al criterio di integrità di rollup corrispondente (come illustrato di seguito).  

![Esempio di rollup dell'integrità](./media/monitoring-vminsights-health/health-diagnostics-windows-svc-rollup.png)

L'integrità di **Rollup servizi Windows Core** si inserisce nell'integrità di **Disponibilità sistema operativo**, che infine entra nella **Disponibilità** della macchina virtuale. 

È possibile modificare la configurazione dei criteri di integrità di tipo **Unità** facendo clic sul collegamento con i puntini di sospensione a destra e selezionando **Mostra dettagli** per aprire il riquadro di configurazione. 

![Configurazione di un esempio di criteri di integrità](./media/monitoring-vminsights-health/health-diagnostics-linuxvm-example-03.png)

Nel riquadro di configurazione per i criteri di integrità selezionati, in questo esempio è possibile configurare **% spazio libero su disco logico** con un valore numerico diverso per la soglia, in quanto è un monitor a due stati, ossia integro o critico.  Un altro criterio di integrità può avere tre stati e consentire la configurazione di un valore per la soglia dello stato di integrità avviso e critico.  

>[!NOTE]
>La modifica di configurazione dei criteri di integrità a un'istanza viene applicata a tutte le istanze monitorate.  Ad esempio, se si seleziona **/mnt/resource** e si modifica la soglia **% spazio libero su disco logico**, la modifica non si applica solo a tale istanza, ma a tutte le altre istanze di disco logico individuate e monitorate nella macchina virtuale.
>

![Configurazione di criteri di integrità per un esempio di monitoraggio unità](./media/monitoring-vminsights-health/health-diagnostics-linuxvm-example-04.png)


### <a name="state-changes"></a>Modifiche stato
La colonna più a destra nella pagina Diagnostica integrità è **Modifiche stato**. Elenca tutte le modifiche di state associate ai criteri di integrità selezionati nella sezione **Criteri di integrità** o la modifica di stato della macchina virtuale se ne è stata selezionata una nella colonna **Modello componente** o **Criteri di integrità** della tabella. 

![Esempio di modifiche di stato presentato in Diagnostica integrità](./media/monitoring-vminsights-health/health-diagnostics-page-statechanges.png)

Questa sezione è costituita dallo stato dei criteri di integrità e dall'ora associata ordinati con lo stato più recente in alto.   

### <a name="association-of-component-model-health-criteria-and-state-change-columns"></a>Associazione delle colonne Modello componente, Criteri di integrità e Modifica stato 
Le tre colonne sono correlate tra loro. Quando un utente seleziona un'istanza individuata in Modello componente, la sezione **Criteri di integrità** viene filtrata in base alla visualizzazione di tale componente e, di conseguenza, la colonna **Modifica stato** viene aggiornato in base ai criteri di integrità selezionati. 

![Esempio di selezione dell'istanza monitorata e dei risultati](./media/monitoring-vminsights-health/health-diagnostics-linuxvm-example-02.png)

Nell'esempio precedente, quando si seleziona uno **/mnt (disco logico)**, l'albero Criteri di integrità viene filtrato in base a **/mnt (disco logico)**. Le schede **Disponibilità** e **Prestazioni** sono filtrate di conseguenza. La colonna **Modifica stato** indica la modifica dello stato in base alla disponibilità di **/mnt (disco logico)**. 

Per visualizzare lo stato di integrità aggiornato, è possibile aggiornare la pagina Diagnostica integrità facendo clic sul collegamento **Aggiorna**.  Se lo stato di integrità del criterio di integrità viene aggiornato in base all'intervallo di polling predefinito, questa attività consente di evitare l'attesa e riflette lo stato di integrità più recente.  Il filtro **Stato criteri di integrità** consente di esaminare i risultati in base allo stato di integrità selezionato: Integro, Avviso, Critico, Sconosciuti e tutti.  L'ora dell'**Ultimo aggiornamento** nell'angolo in alto a destra rappresenta l'ultima volta in cui la pagina Diagnostica integrità è stata aggiornata.  

## <a name="alerting-and-alert-management"></a>Avvisi e gestione degli avvisi 
La funzionalità dell'integrità di Monitoraggio di Azure per le macchine virtuali è integrata ad [Avvisi di Azure](../monitoring-and-diagnostics/monitoring-overview-unified-alerts.md) e genera un avviso quando i criteri di integrità predefiniti passano da uno stato integro a uno non integro quando viene rilevata la condizione. Gli avvisi sono classificati in base alla gravità, da 0 a 4 dove la gravità 0 rappresenta il livello più alto.  

Il numero totale di avvisi di integrità delle macchine virtuali classificati in base alla gravità è disponibile nel dashboard **Integrità** nella sezione **Avvisi**. Quando si seleziona il numero totale di avvisi o il numero corrispondente a un livello di gravità, si apre la pagina **Avvisi** in cui sono elencati tutti gli avvisi corrispondenti alla selezione.  Ad esempio, se si seleziona la riga corrispondente al **livello di gravità 1**, viene visualizzato quanto segue:

![Esempio di tutti gli avvisi con livello di gravità 1](./media/monitoring-vminsights-health/vminsights-sev1-alerts-01.png)

È possibile filtrare questa visualizzazione selezionando i valori nei menu a discesa nella parte superiore della pagina.

|Colonna |DESCRIZIONE | 
|-------|------------| 
|Sottoscrizione |Selezionare una sottoscrizione di Azure. Sono inclusi nella visualizzazione solo gli avvisi della sottoscrizione selezionata. | 
|Gruppo di risorse |Selezionare un singolo gruppo di risorse. Sono inclusi nella visualizzazione solo gli avvisi con destinazioni nel gruppo di risorse selezionato. | 
|Tipo di risorsa |Selezionare uno o più tipi di risorsa. Sono inclusi nella visualizzazione solo gli avvisi con destinazioni del tipo selezionato. Questa colonna risulta disponibile solo dopo che è stato specificato un gruppo di risorse. | 
|Risorsa |Selezionare una risorsa. Nella visualizzazione vengono inclusi solo gli avvisi con tale risorsa definita come destinazione. Questa colonna risulta disponibile solo dopo che è stato specificato un tipo di risorsa. | 
|Gravità |Selezionare un livello di gravità degli avvisi oppure *Tutti* per includere gli avvisi di tutti i livelli di gravità. | 
|Condizione del monitoraggio |Selezionare una condizione di monitoraggio per filtrare gli avvisi se sono stati *Attivati* dal sistema oppure *Risolti* dal sistema se la condizione non è più attiva. In alternativa selezionare *Tutti* per includere gli avvisi di tutte le condizioni. | 
|Stato dell'avviso |Selezionare uno stato dell'avviso, *Nuovo*, *Conferma*, *Chiuso*, oppure selezionare *Tutti* per includere gli avvisi di tutti gli stati. | 
|Servizio di monitoraggio |Selezionare un servizio oppure *Tutti* per includere tutti i servizi. Per questa funzionalità sono supportati solo gli avvisi da Informazioni dettagliate sull'infrastruttura. | 
|Intervallo di tempo| Nella visualizzazione vengono inclusi solo gli avvisi attivati nell'intervallo di tempo selezionato. I valori supportati sono l'ultima ora, le ultime 24 ore, gli ultimi 7 giorni e gli ultimi 30 giorni. | 

La pagina **Dettagli avviso** viene visualizzata quando si seleziona un avviso, fornendo i dettagli dell'avviso e consentendo di modificarne lo stato. Altre informazioni su come utilizzare le regole di avviso e gestire gli avvisi sono disponibili in [Creare, visualizzare e gestire gli avvisi tramite Monitoraggio di Azure](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md).

![Riquadro Dettagli avvisi per un avviso selezionato](./media/monitoring-vminsights-health/alert-details-pane-01.png)

Lo stato dell'avviso può anche essere modificato per uno o più avvisi selezionandolo e selezionando **Modifica stato** dalla pagina **Tutti gli avvisi** nell'angolo in alto a sinistra. Nel riquadro **Modifica stato avviso** selezionare uno degli stati, aggiungere una descrizione della modifica nel campo **Commento**, quindi fare clic su **OK** per confermare le modifiche. Durante la verifica delle informazioni e l'applicazione delle modifiche, è possibile tenere traccia dell'avanzamento scegliendo **Notifiche** dal menu.  

## <a name="next-steps"></a>Passaggi successivi
Per individuare i colli di bottiglia e l'utilizzo generale con le prestazioni delle macchine virtuali, vedere [Visualizzare le prestazioni delle macchine virtuali di Azure](monitoring-vminsights-performance.md); per visualizzare le dipendenze dell'applicazione individuate, vedere [Visualizzare la mappa di Monitoraggio di Azure per le macchine virtuali](monitoring-vminsights-maps.md). 