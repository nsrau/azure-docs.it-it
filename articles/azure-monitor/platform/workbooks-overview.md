---
title: Creare report interattivi con cartelle di lavoro di Monitoraggio di Azure | Microsoft Docs
description: Semplificare la creazione di report complessi con cartelle di lavoro con parametri predefinite e personalizzate
author: mrbullwinkle
manager: carmonm
services: azure-monitor
ms.service: azure-monitor
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: mbullwin
ms.openlocfilehash: 2cbc128e59472e36e2b5685efdf4cd02144edf15
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73165927"
---
# <a name="azure-monitor-workbooks"></a>Cartelle di lavoro di monitoraggio di Azure

Le cartelle di lavoro di forniscono un'area di disegno flessibile per l'analisi dei dati e la creazione di report visivi avanzati all'interno del portale di Azure. Consentono di accedere a più origini dati da Azure e combinarle in esperienze interattive unificate. 

## <a name="data-sources"></a>Origini dati

Le cartelle di lavoro possono eseguire query sui dati da più origini in Azure. Gli autori delle cartelle di lavoro possono trasformare questi dati per fornire informazioni dettagliate sulla disponibilità, le prestazioni, l'utilizzo e l'integrità complessiva dei componenti sottostanti. Ad esempio, l'analisi dei log delle prestazioni dalle macchine virtuali per identificare le istanze di CPU elevata o di memoria insufficiente e visualizzare i risultati come griglia in un report interattivo.
  
Ma la vera potenza delle cartelle di lavoro è la possibilità di combinare i dati provenienti da origini diverse all'interno di un singolo report. Ciò consente la creazione di visualizzazioni di risorse Composite o join tra le risorse che consentono dati e informazioni dettagliate più completi che altrimenti sarebbero impossibili.

Le cartelle di lavoro sono attualmente compatibili con le origini dati seguenti:

* [Log](workbooks-data-sources.md#logs)
* [Metriche](workbooks-data-sources.md#metrics)
* [Grafico delle risorse di Azure](workbooks-data-sources.md#azure-resource-graph)
* [Avvisi (anteprima)](workbooks-data-sources.md#alerts-preview)
* [Stato del carico di lavoro (anteprima)](workbooks-data-sources.md#workload-health-preview)
* [Integrità risorse di Azure (anteprima)](workbooks-data-sources.md#azure-resource-health)
* [Esplora dati di Azure (anteprima)](workbooks-data-sources.md#azure-data-explorer-preview)

## <a name="visualizations"></a>Visualizzazioni

Le cartelle di lavoro di offrono un set completo di funzionalità per la visualizzazione dei dati. Per esempi dettagliati di ogni tipo di visualizzazione, è possibile consultare i collegamenti di esempio seguenti:

* [Text](workbooks-visualizations.md#text)
* [Grafici](workbooks-visualizations.md#charts)
* [Griglie](workbooks-visualizations.md#grids)
* [Piastrelle](workbooks-visualizations.md#tiles)
* [Alberi](workbooks-visualizations.md#trees)
* [Grafici](workbooks-visualizations.md#graphs)

![Visualizzazioni di cartelle di lavoro di esempio](./media/workbooks-overview/visualizations.png)

## <a name="getting-started"></a>Inizia ora

Per esplorare l'esperienza delle cartelle di lavoro, passare prima al servizio monitoraggio di Azure. Questa operazione può essere eseguita digitando **monitoraggio** nella casella cerca della portale di Azure.

Selezionare quindi **cartelle di lavoro (anteprima)** .

![Screenshot del pulsante di anteprima delle cartelle di lavoro evidenziato in una casella rossa](./media/workbooks-overview/workbooks-preview.png)

### <a name="gallery"></a>Raccolta

In questo modo si passa alla raccolta cartelle di lavoro:

![Screenshot della visualizzazione della raccolta di cartelle di lavoro di monitoraggio di Azure](./media/workbooks-overview/gallery.png)

### <a name="workbooks-versus-workbook-templates"></a>Cartelle di lavoro e modelli di cartella di lavoro

È possibile visualizzare una _cartella di lavoro_ in verde e un numero di _modelli di cartella di lavoro_ in viola. I modelli vengono usati come report curati progettati per un riutilizzo flessibile da parte di più utenti e team. L'apertura di un modello consente di creare una cartella di lavoro temporanea popolata con il contenuto del modello. 

È possibile modificare i parametri della cartella di lavoro basata su modelli ed eseguire l'analisi senza temere che si verifichi un'esperienza di Reporting futura per i colleghi. Se si apre un modello, si apportano alcune modifiche e quindi si seleziona l'icona Salva, il modello verrà salvato come cartella di lavoro che verrebbe visualizzata in verde lasciando invariato il modello originale. 

Dietro le quinte, i modelli differiscono anche dalle cartelle di lavoro salvate. Il salvataggio di una cartella di lavoro crea una risorsa Azure Resource Manager associata, mentre la cartella di lavoro temporanea creata quando si apre solo un modello non dispone di una risorsa univoca associata. Per altre informazioni sulla gestione del controllo di accesso nelle cartelle di lavoro, vedere l'articolo relativo al [controllo di accesso delle cartelle di lavoro](workbooks-access-control.md).

### <a name="exploring-a-workbook-template"></a>Esplorazione di un modello di cartella di lavoro

Selezionare **analisi degli errori dell'applicazione** per visualizzare uno dei modelli di cartella di lavoro delle applicazioni predefinite.

![Screenshot del modello di analisi degli errori dell'applicazione](./media/workbooks-overview/failure-analysis.png)

Come indicato in precedenza, l'apertura del modello consente di creare una cartella di lavoro temporanea per consentire l'interazione con. Per impostazione predefinita, la cartella di lavoro viene aperta in modalità di lettura che visualizza solo le informazioni per l'esperienza di analisi desiderata creata dall'autore del modello originale.

Nel caso di questa particolare cartella di lavoro, l'esperienza è interattiva. È possibile modificare la sottoscrizione, le app di destinazione e l'intervallo di tempo dei dati che si desidera visualizzare. Una volta effettuate le selezioni, la griglia delle richieste HTTP è interattiva, in modo che la selezione di una singola riga modifichi i dati di cui viene eseguito il rendering nei due grafici nella parte inferiore del report.

### <a name="editing-mode"></a>Modalità di modifica

Per comprendere il modo in cui il modello di cartella di lavoro viene combinato, è necessario passare alla modalità di modifica selezionando **modifica**. 

![Screenshot del modello di analisi degli errori dell'applicazione](./media/workbooks-overview/edit.png)

Dopo aver impostato la modalità di modifica, si noterà che un numero di caselle di **modifica** viene visualizzato a destra corrispondente a ogni singolo aspetto della cartella di lavoro.

![Screenshot del pulsante modifica](./media/workbooks-overview/edit-mode.png)

Se si seleziona il pulsante modifica immediatamente sotto la griglia dei dati della richiesta, è possibile osservare che questa parte della cartella di lavoro è costituita da una query kusto sui dati di una risorsa Application Insights.

![Screenshot della query kusto sottostante](./media/workbooks-overview/kusto.png)

Facendo clic sugli altri pulsanti di **modifica** a destra si riveleranno alcuni componenti di base che compongono cartelle di lavoro quali [caselle di testo](workbooks-visualizations.md#text)basate su Markdown, elementi dell'interfaccia utente per la [selezione dei parametri](workbooks-parameters.md) e altri [tipi di grafico/visualizzazione](workbooks-visualizations.md). 

Esplorare i modelli predefiniti in modalità di modifica e quindi modificarli in base alle proprie esigenze e salvare la cartella di lavoro personalizzata è un modo eccellente per iniziare a conoscere le funzionalità possibili con le cartelle di lavoro di monitoraggio di Azure.

## <a name="pinning-visualizations"></a>Aggiunta di visualizzazioni

I passaggi relativi a testo, query e metriche in una cartella di lavoro possono essere aggiunti usando il pulsante Aggiungi su tali elementi quando la cartella di lavoro è in modalità PIN oppure se l'autore della cartella di lavoro ha abilitato le impostazioni per tale elemento per rendere visibile l'icona del PIN. 

Per accedere alla modalità PIN, fare clic su **modifica** per passare alla modalità di modifica e selezionare l'icona del pin blu nella barra superiore. Una singola icona PIN verrà visualizzata sopra ogni casella di *modifica* della parte della cartella di lavoro corrispondente sul lato destro dello schermo.

![Aggiungi esperienza](./media/workbooks-overview/pin-experience.png)

> [!NOTE]
> Lo stato della cartella di lavoro viene salvato al momento del PIN e le cartelle di lavoro aggiunte in un dashboard non vengono aggiornate se la cartella di lavoro sottostante viene modificata. Per aggiornare una parte della cartella di lavoro bloccata, è necessario eliminare e aggiungere nuovamente tale parte.

## <a name="dashboard-time-ranges"></a>Intervalli di tempo del dashboard

Le parti della query della cartella di lavoro bloccata rispetteranno l'intervallo di tempo del dashboard se l'elemento bloccato è configurato per l'utilizzo di un parametro di *intervallo di tempo* . Il valore dell'intervallo di tempo del dashboard verrà usato come valore del parametro dell'intervallo di tempo e qualsiasi modifica dell'intervallo di tempo del dashboard provocherà l'aggiornamento dell'elemento bloccato. Se una parte bloccata usa l'intervallo di tempo del dashboard, verrà visualizzato il sottotitolo dell'aggiornamento della parte bloccata per visualizzare l'intervallo di tempo del dashboard ogni volta che viene modificato l'intervallo di tempo. 

Inoltre, le parti della cartella di lavoro bloccate che usano un parametro di intervallo di tempo vengono aggiornate automaticamente a una velocità determinata dall'intervallo di tempo del dashboard. L'ultima esecuzione della query verrà visualizzata nel sottotitolo della parte bloccata.

Se un passaggio aggiunto ha un intervallo di tempo impostato in modo esplicito (non usa un parametro di intervallo di tempo), l'intervallo di tempo verrà sempre usato per il dashboard, indipendentemente dalle impostazioni del dashboard. Il sottotitolo della parte bloccata non visualizzerà l'intervallo di tempo del dashboard e la query non viene aggiornata automaticamente nel dashboard. Il sottotitolo mostrerà l'ultima esecuzione della query.

> [!NOTE]
> Le query che utilizzano l'origine dati di *merge* non sono attualmente supportate durante l'aggiunta ai dashboard.

## <a name="sharing-workbook-templates"></a>Condivisione di modelli di cartella di lavoro

Una volta avviata la creazione di modelli di cartella di lavoro personalizzati, può essere utile condividerla con la community più ampia. Per altre informazioni e per esplorare altri modelli che non fanno parte della visualizzazione raccolta predefinita di monitoraggio di Azure, visitare il [repository GitHub](https://github.com/Microsoft/Application-Insights-Workbooks/blob/master/README.md). Per esplorare le cartelle di lavoro esistenti, visitare la [libreria delle cartelle di lavoro](https://github.com/microsoft/Application-Insights-Workbooks/tree/master/Workbooks) in GitHub.

## <a name="next-step"></a>Passaggio successivo

* Per [iniziare ad](workbooks-visualizations.md) apprendere altre informazioni sulle cartelle di lavoro, sono disponibili molte opzioni di visualizzazione avanzate.
* [Controllare](workbooks-access-control.md) e condividere l'accesso alle risorse della cartella di lavoro.
