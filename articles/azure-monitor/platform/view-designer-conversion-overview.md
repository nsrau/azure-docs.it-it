---
title: Guida alla transizione di Progettazione visualizzazione di Monitoraggio di Azure Monitor per la transizione alle cartelle di lavoroAzure Monitor view designer to workbooks transition guide
description: ''
author: austonli
ms.author: aul
ms.subservice: ''
ms.topic: conceptual
ms.date: 02/07/2020
ms.openlocfilehash: 234da921b4f0d1243ca8cfdb12ba2d851db2b43f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77658694"
---
# <a name="azure-monitor-view-designer-to-workbooks-transition-guide"></a>Guida alla transizione di Progettazione visualizzazione di Monitoraggio di Azure Monitor per la transizione alle cartelle di lavoroAzure Monitor view designer to workbooks transition guide
[Progettazione viste](view-designer.md) è una funzionalità di Monitoraggio di Azure che consente di creare visualizzazioni personalizzate che consentono di visualizzare i dati nell'area di lavoro di Log Analytics, con grafici, elenchi e sequenze temporali. Essi vengono gradualmente eliminati e sostituiti con cartelle di lavoro che forniscono funzionalità aggiuntive. In questo articolo viene fornita una panoramica del processo di conversione delle visualizzazioni esistenti in cartelle di lavoro.

## <a name="workbooks-overview"></a>Panoramica delle cartelle di lavoro
[Le cartelle di lavoro combinano](../insights/vminsights-workbooks.md) testo, [query di log,](../log-query/query-language.md)metriche e parametri in report interattivi avanzati. Anche i membri del team con lo stesso accesso alle risorse di Azure possono modificare le cartelle di lavoro.

Le cartelle di lavoro sono utili per scenari quali:Workbooks are helpful for scenarios such as:

-   Esplorazione dell'utilizzo della macchina virtuale quando non si conoscono in anticipo le metriche di interesse: utilizzo della CPU, spazio su disco, memoria, dipendenze di rete e così via. A differenza di altri strumenti di analisi dell'utilizzo, le cartelle di lavoro consentono di combinare più tipi di visualizzazioni e analisi, rendendole ideali per questo tipo di esplorazione in formato libero.
-   Spiegare al team le prestazioni di una macchina virtuale di cui è stato eseguito il provisioning di recente, mostrando le metriche per i contatori chiave e altri eventi di log.
-   Condivisione dei risultati di un esperimento di ridimensionamento della macchina virtuale con altri membri del team. È possibile spiegare gli obiettivi per l'esperimento con il testo, quindi mostrare ogni metrica di utilizzo e le query di analisi utilizzate per valutare l'esperimento, insieme a callout chiari per verificare se ogni metrica era superiore o inferiore alla destinazione.
-   Segnalazione dell'impatto di un'interruzione sull'utilizzo della macchina virtuale, combinazione di dati, spiegazione testuale e discussione dei passaggi successivi per evitare interruzioni in futuro.


## <a name="why-convert-view-designer-dashboards-to-workbooks"></a>Perché convertire i dashboard di progettazione della vista in cartelle di lavoro?

Progettazione viste offre la possibilità di generare visualizzazioni e visualizzazioni diverse basate su query. Molte personalizzazioni di alto livello rimangono però limitate, ad esempio la formattazione delle griglie e i layout dei riquadri o la selezione di elementi grafici alternativi per rappresentare i dati. Progettazione viste è limitato a un totale di nove riquadri distinti per rappresentare i dati.

Cartelle di lavoro è una piattaforma che permette di sfruttare appieno il potenziale dei dati disponibili. le cartelle di lavoro non solo mantengono tutte le funzionalità, ma supportano anche funzionalità aggiuntive tramite testo, metriche, parametri e molto altro ancora. Ad esempio, le cartelle di lavoro consentono agli utenti di consolidare griglie dense e aggiungere barre di ricerca per filtrare e analizzare facilmente i dati. 

### <a name="advantages-of-using-workbooks-over-view-designer"></a>Vantaggi dell'utilizzo di cartelle di lavoro su Progettazione viste

* Supporta sia i log che le metriche.
* Consente visualizzazioni personali per il controllo di accesso individuale e le visualizzazioni delle cartelle di lavoro condivise.
* Opzioni di layout personalizzate con schede, ridimensionamento e controlli di ridimensionamento.
* Supporto per l'esecuzione di query in più aree di lavoro di Log Analytics, applicazioni di Application Insights e sottoscrizioni.
* Abilita i parametri personalizzati che aggiornano dinamicamente i grafici e le visualizzazioni associati.
* Supporto della raccolta di modelli da GitHub pubblico.

Sebbene questa guida offra semplici passaggi per ricreare direttamente molte delle visualizzazioni di progettazione delle visualizzazioni di uso comune, le cartelle di lavoro consentono agli utenti di avere la libertà di creare e progettare le proprie visualizzazioni e metriche personalizzate. La schermata seguente è tratta dal [modello di utilizzo Area di lavoro](https://go.microsoft.com/fwlink/?linkid=874159&resourceId=Azure%20Monitor&featureName=Workbooks&itemId=community-Workbooks%2FAzure%20Monitor%20-%20Workspaces%2FWorkspace%20Usage&workbookTemplateName=Workspace%20Usage&func=NavigateToPortalFeature&type=workbook) e mostra un esempio di quali cartelle di lavoro sono in grado di creare:The following screenshot is from the Workspace usage template and shows an example of what workbooks are capable of creating:


![Esempio di applicazione di cartelle di lavoro](media/view-designer-conversion-overview/workbook-template-example.jpg)


## <a name="how-to-start-using-workbooks"></a>Come iniziare a usare le cartelle di lavoro
Le cartelle di lavoro aperte dalle cartelle di lavoro sono abilitate nelle aree di lavoro di Log Analytics come elemento nella barra di spostamento laterale, direttamente sotto il percorso di progettazione della visualizzazione.

![Navigazione nelle cartelle di lavoro](media/view-designer-conversion-overview/workbooks-nav.png)

Una volta selezionata, verrà visualizzata una raccolta che elenca tutte le cartelle di lavoro e i modelli salvati per l'area di lavoro.

![Raccolta cartelle di lavoro](media/view-designer-conversion-overview/workbooks-gallery.png)

Per avviare una nuova cartella di lavoro, è possibile selezionare il modello **vuoto** in **Avvio rapido**o l'icona **Nuovo** nella barra di spostamento superiore. Per visualizzare i modelli o tornare alle cartelle di lavoro salvate, selezionare l'elemento dalla raccolta o cercare il nome nella barra di ricerca.

Per salvare una cartella di lavoro, è necessario salvare il report con un titolo, una sottoscrizione, un gruppo di risorse e un percorso specifici.
La cartella di lavoro verrà riempita automaticamente in base alle stesse impostazioni dell'area di lavoro di LOs Angeles, con la stessa sottoscrizione, gruppo di risorse, tuttavia, gli utenti possono modificare queste impostazioni del report. Le cartelle di lavoro vengono salvate per impostazione predefinita in *Report*personali , accessibili solo al singolo utente. Possono anche essere salvati direttamente nei rapporti condivisi o condivisi in un secondo momento.

![Salvataggio delle cartelle di lavoro](media/view-designer-conversion-overview/workbooks-save.png)

## <a name="next-steps"></a>Passaggi successivi

- [Opzioni di conversione](view-designer-conversion-options.md)
