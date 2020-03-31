---
title: Panoramica di Cartelle di lavoro di Monitoraggio di Azure
description: Semplificare la creazione di report complessi con cartelle di lavoro con parametri predefinite e personalizzate
author: mrbullwinkle
manager: carmonm
services: azure-monitor
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: mbullwin
ms.openlocfilehash: 29e675f3ae35df9211f58d45ad8450566d67a588
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77658235"
---
# <a name="azure-monitor-workbooks"></a>Cartelle di lavoro di Monitoraggio di Azure

Le cartelle di lavoro forniscono un'area di disegno flessibile per l'analisi dei dati e la creazione di report visivi avanzati all'interno del portale di Azure.Workbooks provide a flexible canvas for data analysis and the creation of rich visual reports within the Azure portal. Consentono di accedere a più origini dati da Azure e di combinarle in esperienze interattive unificate. 

## <a name="data-sources"></a>Origini dati

Le cartelle di lavoro possono eseguire query sui dati da più origini all'interno di Azure.Workbooks can query data from multiple sources within Azure. Gli autori delle cartelle di lavoro possono trasformare questi dati per fornire informazioni dettagliate sulla disponibilità, le prestazioni, l'utilizzo e l'integrità generale dei componenti sottostanti. Ad esempio, l'analisi dei log delle prestazioni dalle macchine virtuali per identificare istanze di CPU o memoria insufficiente elevata e la visualizzazione dei risultati come griglia in un report interattivo.
  
Ma il vero potere delle cartelle di lavoro è la possibilità di combinare dati provenienti da fonti diverse all'interno di un singolo report. Ciò consente la creazione di visualizzazioni o join di risorse composite tra risorse che consentono dati e informazioni dettagliate più dettagliate che altrimenti sarebbero impossibili.

Le cartelle di lavoro sono attualmente compatibili con le origini dati seguenti:Workbooks are currently compatible with the following data sources:

* [Registri](workbooks-data-sources.md#logs)
* [Metriche](workbooks-data-sources.md#metrics)
* [Grafico delle risorse di AzureAzure Resource Graph](workbooks-data-sources.md#azure-resource-graph)
* [Avvisi (anteprima)](workbooks-data-sources.md#alerts-preview)
* [Integrità del carico di lavoro (anteprima)Workload Health (Preview)](workbooks-data-sources.md#workload-health-preview)
* [Integrità risorse di Azure (anteprima)Azure Resource Health (Preview)](workbooks-data-sources.md#azure-resource-health)
* [Esplora dati di Azure (anteprima)Azure Data Explorer (Preview)](workbooks-data-sources.md#azure-data-explorer-preview)

## <a name="visualizations"></a>Visualizzazioni

Le cartelle di lavoro offrono un set completo di funzionalità per la visualizzazione dei dati. Per esempi dettagliati di ogni tipo di visualizzazione è possibile consultare i collegamenti di esempio riportati di seguito:

* [Testo](workbooks-visualizations.md#text)
* [Grafici](workbooks-visualizations.md#charts)
* [Griglie](workbooks-visualizations.md#grids)
* [Riquadri](workbooks-visualizations.md#tiles)
* [Trees](workbooks-visualizations.md#trees)
* [Grafici](workbooks-visualizations.md#graphs)

![Visualizzazioni di cartelle di lavoro di esempio](./media/workbooks-overview/visualizations.png)

## <a name="getting-started"></a>Introduzione

Per esplorare l'esperienza delle cartelle di lavoro, passare innanzitutto al servizio Monitoraggio di Azure.To explore the workbooks experience, first navigate to the Azure Monitor service. Questa operazione può essere eseguita digitando Monitor nella casella di ricerca nel portale di Azure.This can be done by typing **Monitor** into the search box in the Azure portal.

Selezionare **quindi Cartelle di lavoro (anteprima)**.

![Screenshot del pulsante di anteprima Cartelle di lavoro evidenziato in una casella rossa](./media/workbooks-overview/workbooks-preview.png)

### <a name="gallery"></a>Gallery

Verrà visualizzata la raccolta di cartelle di lavoro:

![Screenshot della visualizzazione della raccolta di cartelle di lavoro di Monitoraggio di Azure](./media/workbooks-overview/gallery.png)

### <a name="workbooks-versus-workbook-templates"></a>Modelli di cartelle di lavoro e cartelle di lavoro

È possibile visualizzare una _cartella di lavoro_ in verde e numerosi modelli di cartella di _lavoro_ in viola. I modelli fungono da report selezionati progettati per un riutilizzo flessibile da parte di più utenti e team. L'apertura di un modello crea una cartella di lavoro temporanea popolata con il contenuto del modello. 

È possibile regolare i parametri della cartella di lavoro basata su modelli ed eseguire l'analisi senza temere di interrompere l'esperienza di creazione di report futura per i colleghi. Se si apre un modello, apportare alcune modifiche e quindi selezionare l'icona di salvataggio si salverà il modello come una cartella di lavoro che verrebbe quindi visualizzata in verde lasciando intatto il modello originale. 

Sotto il cofano, i modelli differiscono anche dalle cartelle di lavoro salvate. Il salvataggio di una cartella di lavoro crea una risorsa di Azure Resource Manager associata, mentre la cartella di lavoro temporanea creata quando si apre un modello non è associata alcuna risorsa univoca. Per ulteriori informazioni sulla gestione del controllo di accesso nelle cartelle di lavoro, consultare l'articolo sul controllo di [accesso delle cartelle di lavoro.](workbooks-access-control.md)

### <a name="exploring-a-workbook-template"></a>Esplorazione di un modello di cartella di lavoro

Selezionare **Analisi errori applicazione** per visualizzare uno dei modelli di cartella di lavoro dell'applicazione predefiniti.

![Schermata del modello di analisi degli errori dell'applicazione](./media/workbooks-overview/failure-analysis.png)

Come indicato in precedenza, l'apertura del modello crea una cartella di lavoro temporanea con cui interagire. Per impostazione predefinita, la cartella di lavoro viene aperta in modalità di lettura che visualizza solo le informazioni per l'esperienza di analisi desiderata creata dall'autore del modello originale.

Nel caso di questa particolare cartella di lavoro, l'esperienza è interattiva. È possibile regolare l'abbonamento, le app di destinazione e l'intervallo di tempo dei dati che si desidera visualizzare. Una volta effettuate tali selezioni, la griglia delle richieste HTTP è interattiva, per cui la selezione di una singola riga modificherà i dati di cui viene eseguito il rendering nei due grafici nella parte inferiore del report.

### <a name="editing-mode"></a>Modalità di modifica

Per capire come questo modello di cartella di lavoro viene messo insieme è necessario passare alla modalità di modifica selezionando **Modifica**. 

![Schermata del modello di analisi degli errori dell'applicazione](./media/workbooks-overview/edit.png)

Una volta che si è passati alla modalità di modifica si noterà un certo numero di **caselle** di modifica appaiono a destra corrispondente con ogni singolo aspetto della cartella di lavoro.

![Screenshot del pulsante Modifica](./media/workbooks-overview/edit-mode.png)

Se si seleziona il pulsante di modifica immediatamente sotto la griglia dei dati della richiesta, è possibile vedere che questa parte della cartella di lavoro è costituita da una query Kusto sui dati di una risorsa di Application Insights.

![Schermata della query Kusto sottostante](./media/workbooks-overview/kusto.png)

Facendo clic sugli altri pulsanti **Modifica** a destra verrà visualizzato un numero di componenti di base che costituiscono cartelle di lavoro come [caselle](workbooks-visualizations.md#text)di testo basate su markdown, elementi dell'interfaccia utente di selezione dei [parametri](workbooks-parameters.md) e altri tipi [di grafico/visualizzazione.](workbooks-visualizations.md) 

Esplorare i modelli predefiniti in modalità di modifica e modificarli in base alle proprie esigenze e salvare la propria cartella di lavoro personalizzata è un ottimo modo per iniziare a scoprire cosa è possibile fare con le cartelle di lavoro di Monitoraggio di Azure.Exploring the pre-built templates in edit-mode and then modifying them to your needs and save your own custom workbook is an excellent way to learn to learn about what is possible with Azure Monitor workbooks.

## <a name="pinning-visualizations"></a>Aggiunta di visualizzazioni

I passaggi di testo, query e metriche in una cartella di lavoro possono essere aggiunti usando il pulsante di aggiunta a tali elementi mentre la cartella di lavoro è in modalità pin o se l'autore della cartella di lavoro ha abilitato le impostazioni per tale elemento per rendere visibile l'icona a forma di puntina. 

Per accedere alla modalità pin, fare clic su **Modifica** per attivare la modalità di modifica e selezionare l'icona a forma di puntina blu nella barra superiore. Una singola icona a forma di puntina verrà quindi visualizzata sopra la casella *Modifica* di ogni parte della cartella di lavoro corrispondente sul lato destro dello schermo.

![Esperienza Pin](./media/workbooks-overview/pin-experience.png)

> [!NOTE]
> Lo stato della cartella di lavoro viene salvato al momento del pin e le cartelle di lavoro aggiunte in un dashboard non verranno aggiornate se la cartella di lavoro sottostante viene modificata. Per aggiornare una parte della cartella di lavoro bloccata, è necessario eliminare e aggiungere nuovamente tale parte.

## <a name="dashboard-time-ranges"></a>Intervalli di tempo del dashboard

Le parti di query della cartella di lavoro aggiunte rispetteranno l'intervallo di tempo del dashboard se l'elemento aggiunto è configurato per l'utilizzo di un parametro *Intervallo* di tempo. Il valore dell'intervallo di tempo del dashboard verrà utilizzato come valore del parametro dell'intervallo di tempo e qualsiasi modifica dell'intervallo di tempo del dashboard causerà l'aggiornamento dell'elemento aggiunto. Se una parte aggiunta utilizza l'intervallo di tempo del dashboard, verrà visualizzato il sottotitolo dell'aggiornamento della parte aggiunta per mostrare l'intervallo di tempo del dashboard ogni volta che l'intervallo di tempo cambia. 

Inoltre, le parti della cartella di lavoro aggiunte che utilizzano un parametro dell'intervallo di tempo verranno aggiornate automaticamente a una frequenza determinata dall'intervallo di tempo del dashboard. L'ultima volta che la query è stata eseguita verrà visualizzata nel sottotitolo della parte bloccata.

Se un passaggio bloccato ha un intervallo di tempo impostato in modo esplicito (non utilizza un parametro di intervallo di tempo), tale intervallo di tempo verrà sempre utilizzato per il dashboard, indipendentemente dalle impostazioni del dashboard. Il sottotitolo della parte aggiunta non mostrerà l'intervallo di tempo del dashboard e la query non verrà aggiornata automaticamente nel dashboard. Il sottotitolo mostrerà l'ultima volta che la query è stata eseguita.

> [!NOTE]
> Le query che utilizzano l'origine dati *di unione* non sono attualmente supportate durante l'aggiunta ai dashboard.

## <a name="sharing-workbook-templates"></a>Condivisione di modelli di cartella di lavoro

Una volta che si inizia a creare i propri modelli di cartella di lavoro, è possibile condividerlo con la comunità più ampia. Per altre informazioni ed esplorare altri modelli che non fanno parte della visualizzazione predefinita della raccolta di Monitoraggio di Azure, visitare il [repository GitHub.](https://github.com/Microsoft/Application-Insights-Workbooks/blob/master/README.md) Per esplorare le cartelle di lavoro esistenti, visitare la libreria di cartelle di lavoro in GitHub.To browse existing workbooks, visit the [Workbook library](https://github.com/microsoft/Application-Insights-Workbooks/tree/master/Workbooks) on GitHub.

## <a name="next-step"></a>Passaggio successivo

* [Iniziare a](workbooks-visualizations.md) conoscere le cartelle di lavoro su molte opzioni di visualizzazione avanzate.
* [Controllare](workbooks-access-control.md) e condividere l'accesso alle risorse della cartella di lavoro.
