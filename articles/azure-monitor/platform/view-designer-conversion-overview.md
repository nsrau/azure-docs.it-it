---
title: Guida alla transizione da Progettazione viste di monitoraggio di Azure a cartelle di lavoro
description: ''
author: austonli
ms.author: aul
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
ms.date: 02/07/2020
ms.openlocfilehash: 5c07d14f4a6d29b65a1ffe31fc6eaad18e8fbe26
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/12/2020
ms.locfileid: "77170903"
---
# <a name="azure-monitor-view-designer-to-workbooks-transition-guide"></a>Guida alla transizione da Progettazione viste di monitoraggio di Azure a cartelle di lavoro
[Progettazione](view-designer.md) viste è una funzionalità di monitoraggio di Azure che consente di creare visualizzazioni personalizzate che consentono di visualizzare i dati nell'area di lavoro log Analytics, con grafici, elenchi e sequenze temporali. Vengono eliminati e sostituiti con cartelle di lavoro che forniscono funzionalità aggiuntive. Questo articolo fornisce una panoramica del processo di conversione delle visualizzazioni esistenti in cartelle di lavoro di.

## <a name="workbooks-overview"></a>Cenni preliminari sulle cartelle di lavoro
Le [cartelle di lavoro](../insights/vminsights-workbooks.md) combinano testo, query di [log](../log-query/query-language.md), metriche e parametri in report interattivi avanzati. I membri del team con lo stesso accesso alle risorse di Azure possono anche modificare le cartelle di lavoro.

Le cartelle di lavoro sono utili per scenari come:

-   Esplorazione dell'utilizzo della macchina virtuale quando non si conoscono in anticipo le metriche di interesse: utilizzo della CPU, spazio su disco, memoria, dipendenze di rete e così via. A differenza di altri strumenti di analisi dell'utilizzo, le cartelle di lavoro consentono di combinare più tipi di visualizzazioni e analisi, rendendoli eccezionali per questo tipo di esplorazione in formato libero.
-   Come spiegare al team l'esecuzione di una macchina virtuale di cui è stato effettuato il provisioning di recente, mostrando le metriche per i contatori chiave e altri eventi del registro.
-   Condivisione dei risultati di un esperimento di ridimensionamento della macchina virtuale con altri membri del team. È possibile illustrare gli obiettivi dell'esperimento con il testo, quindi mostrare ogni metrica di utilizzo e le query di analisi usate per valutare l'esperimento, oltre a deselezionare le chiamate per determinare se ogni metrica è sopra o sotto la destinazione.
-   Segnalazione dell'effetto di un'interruzione sull'utilizzo della VM, combinazione di dati, spiegazione del testo e descrizione dei passaggi successivi per evitare interruzioni in futuro.


## <a name="why-convert-view-designer-dashboards-to-workbooks"></a>Perché convertire i dashboard di Progettazione viste in cartelle di lavoro?

Progettazione viste offre la possibilità di generare visualizzazioni e visualizzazioni basate su query diverse. Molte personalizzazioni di alto livello rimangono però limitate, ad esempio la formattazione delle griglie e i layout dei riquadri o la selezione di elementi grafici alternativi per rappresentare i dati. Progettazione viste è limitato a un totale di nove riquadri distinti per rappresentare i dati.

Cartelle di lavoro è una piattaforma che permette di sfruttare appieno il potenziale dei dati disponibili. le cartelle di lavoro non solo mantengono tutte le funzionalità, ma supportano anche funzionalità aggiuntive tramite testo, metriche, parametri e molto altro ancora. Ad esempio, le cartelle di lavoro consentono agli utenti di consolidare le griglie dense e aggiungere barre di ricerca per filtrare e analizzare facilmente i dati. 

### <a name="advantages-of-using-workbooks-over-view-designer"></a>Vantaggi dell'utilizzo delle cartelle di lavoro in Progettazione viste

* Supporta sia i log che la metrica.
* Consente le visualizzazioni personali per il controllo di accesso e le visualizzazioni di cartelle di lavoro condivise.
* Opzioni di layout personalizzate con schede, ridimensionamento e controlli di ridimensionamento.
* Supporto per l'esecuzione di query su più aree di lavoro Log Analytics, Application Insights applicazioni e sottoscrizioni.
* Abilita parametri personalizzati che aggiornano dinamicamente grafici e visualizzazioni associati.
* Supporto della raccolta di modelli da GitHub pubblico.

Questa guida offre semplici passaggi per ricreare direttamente diverse visualizzazioni di Progettazione viste di uso comune, mentre le cartelle di lavoro consentono agli utenti di avere la libertà di creare e progettare le proprie visualizzazioni e metriche personalizzate. Lo screenshot seguente viene dal [modello di utilizzo dell'area di lavoro](https://go.microsoft.com/fwlink/?linkid=874159&resourceId=Azure%20Monitor&featureName=Workbooks&itemId=community-Workbooks%2FAzure%20Monitor%20-%20Workspaces%2FWorkspace%20Usage&workbookTemplateName=Workspace%20Usage&func=NavigateToPortalFeature&type=workbook) e Mostra un esempio delle cartelle di lavoro che è in grado di creare:


![Esempio di applicazione di cartelle di lavoro](media/view-designer-conversion-overview/workbook-template-example.jpg)


## <a name="how-to-start-using-workbooks"></a>Come iniziare a usare le cartelle di lavoro
Le cartelle di lavoro aperte dalle cartelle di lavoro sono abilitate nelle aree di lavoro Log Analytics come un elemento nella barra di spostamento laterale, direttamente sotto la posizione della finestra di progettazione della vista.

![Navigazione delle cartelle di lavoro](media/view-designer-conversion-overview/workbooks-nav.png)

Una volta selezionata, viene visualizzata una raccolta che elenca tutte le cartelle di lavoro e i modelli salvati per l'area di lavoro.

![Raccolta cartelle di lavoro](media/view-designer-conversion-overview/workbooks-gallery.png)

Per avviare una nuova cartella di lavoro, è possibile selezionare il modello **vuoto** sotto **avvio rapido**o la **nuova** icona nella barra di spostamento superiore. Per visualizzare i modelli o tornare alle cartelle di lavoro salvate, selezionare l'elemento dalla raccolta o cercare il nome nella barra di ricerca.

Per salvare una cartella di lavoro, è necessario salvare il report con un titolo, una sottoscrizione, un gruppo di risorse e una località specifici.
La cartella di lavoro consente di compilare automaticamente le stesse impostazioni dell'area di lavoro di LA, con la stessa sottoscrizione, gruppo di risorse. Tuttavia, gli utenti possono modificare le impostazioni del report. Per impostazione predefinita, le cartelle di lavoro vengono salvate in *report personali*, accessibili solo al singolo utente. Possono anche essere salvati direttamente nei report condivisi o condivisi in un secondo momento.

![Salva cartelle di lavoro](media/view-designer-conversion-overview/workbooks-save.png)

## <a name="next-steps"></a>Passaggi successivi

- [Opzioni di conversione](view-designer-conversion-options.md)
