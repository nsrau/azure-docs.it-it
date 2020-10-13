---
title: Visualizzazioni albero delle cartelle di lavoro di monitoraggio di Azure
description: Informazioni su tutte le visualizzazioni dell'albero delle cartelle di lavoro di monitoraggio di Azure.
services: azure-monitor
author: lgayhardt
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 09/04/2020
ms.author: lagayhar
ms.openlocfilehash: a815f4dcf7c76d4c11b4f2e227802b15f9c92a54
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89664250"
---
# <a name="tree-visualizations"></a>Visualizzazioni ad albero

Le cartelle di lavoro supportano le visualizzazioni gerarchiche tramite griglie di albero. Gli alberi consentono di espandere alcune righe nel livello successivo per un'esperienza di drill-down.

Nell'esempio seguente vengono illustrate le metriche di integrità del contenitore (dimensioni working set) visualizzate come griglia ad albero. I nodi di livello superiore sono i nodi del servizio Azure Kubernetes (AKS), il livello successivo sono pod e il livello finale sono i contenitori. Si noti che è comunque possibile formattare le colonne come in una griglia (mappa termica, icons, link). In questo caso, l'origine dati sottostante è un'area di lavoro Log Analytics con log AKS.

[![Screenshot della visualizzazione Riepilogo dei riquadri](./media/workbooks-tree-visualizations/trees.png)](./media/workbooks-tree-visualizations/trees.png#lightbox)

## <a name="adding-a-tree-grid"></a>Aggiunta di una griglia ad albero
1. Impostare la cartella di lavoro in modalità di modifica facendo clic sulla voce _Modifica_ sulla barra degli strumenti.
2. Selezionare **Aggiungi** e quindi *Aggiungi query* per aggiungere un controllo query di log alla cartella di lavoro.
3. Selezionare il tipo di query come **log**, il tipo di risorsa (ad esempio, Application Insights) e le risorse di destinazione.
4. Utilizzare l'editor di query per immettere il KQL per l'analisi
    ```kusto
    requests
    | summarize Requests = count() by ParentId = appName, Id = name
    | extend Kind = 'Request', Name = strcat('🌐 ', Id)
    | union (requests
    | summarize Requests = count() by Id = appName
    | extend Kind = 'Request', ParentId = '', Name = strcat('📱 ', Id))
    | project Name, Kind, Requests, Id, ParentId
    | order by Requests desc
    ```
5. Impostare la visualizzazione su **griglia**
6. Selezionare il pulsante **Impostazioni colonna** per aprire il riquadro Impostazioni
7. Nella sezione **Impostazioni albero/raggruppamento** in basso, impostare:
    * Tipo di albero: `Parent/Child`
    * Campo ID: `Id`
    * Campo ID padre: `ParentId`
    * Mostra l'espansore su: `Name`
    * Selezionare *Espandi il livello superiore della casella di controllo albero* .
8. Nella parte superiore della sezione _colonne_ impostare:
    * Renderer _ID_ -colonna:`Hidden`
    * Renderer della colonna _ID padre_ :`Hidden`
    * _Richieste_ -renderer colonna: `Bar` , colore: `Blue` , valore minimo: `0`
9. Selezionare il pulsante **Salva e Chiudi** nella parte inferiore del riquadro.

[![Screenshot della visualizzazione Riepilogo dei riquadri con la query e le impostazioni precedenti.](./media/workbooks-tree-visualizations/tree-settings.png)](./media/workbooks-tree-visualizations/tree-settings.png#lightbox)

## <a name="tree-settings"></a>Impostazioni albero

| Impostazione | Spiegazione |
|:------------- |:-------------|
| `Id Field` | ID univoco di ogni riga della griglia. |
| `Parent Id Field` | ID dell'elemento padre della riga corrente. |
| `Show the expander on` | Colonna in cui visualizzare l'espansore della struttura ad albero. È normale che le griglie dell'albero nascondano il proprio ID e il campo ID padre perché non sono leggibili. Il Expander viene invece visualizzato in un campo con un valore più leggibile, ad esempio il nome dell'entità. |
| `Expand the top level of the tree` | Se questa opzione è selezionata, la griglia dell'albero verrà espansa al livello superiore. Utile se si desidera visualizzare altre informazioni per impostazione predefinita. |

## <a name="grouping-in-a-grid"></a>Raggruppamento in una griglia

Il raggruppamento consente di creare visualizzazioni gerarchiche simili a quelle riportate sopra con query significativamente più semplici. L'aggregazione viene persa nei nodi interni dell'albero, ma ciò sarà accettabile per alcuni scenari. Utilizzare *Group by* per compilare visualizzazioni ad albero quando il set di risultati sottostante non può essere trasformato in un formato libero appropriato, ad esempio: avviso, stato e dati di metrica.

## <a name="adding-a-tree-using-grouping"></a>Aggiunta di un albero utilizzando il raggruppamento

1. Impostare la cartella di lavoro in modalità di modifica facendo clic sulla voce _Modifica_ sulla barra degli strumenti.
2. Selezionare **Aggiungi** e quindi *Aggiungi query* per aggiungere un controllo query di log alla cartella di lavoro.
3. Selezionare il tipo di query come **log**, il tipo di risorsa (ad esempio, Application Insights) e le risorse di destinazione.
4. Utilizzare l'editor di query per immettere il KQL per l'analisi
    ```kusto
    requests
    | summarize Requests = count() by App = appName, RequestName = name
    | order by Requests desc
    ```
1. Impostare la visualizzazione su *griglia*.
2. Selezionare il **pulsante Impostazioni colonne** per aprire il riquadro Impostazioni.
3. Nella sezione *Impostazioni albero/raggruppamento* in basso, impostare:
    * Tipo di albero: `Group By`
    * Raggruppa per: `App`
    * Quindi per: `None`
    * Selezionare *Espandi il livello superiore della casella di controllo albero* .
4. Nella parte superiore della sezione *colonne* impostare:
    * Renderer di colonna *app* :`Hidden`
    * *Richieste* -renderer colonna: `Bar` , colore: `Blue` , valore minimo: `0`
5. Selezionare il pulsante **Salva e Chiudi** nella parte inferiore del riquadro.

[![Screenshot che illustra la creazione di una visualizzazione albero nelle cartelle di lavoro](./media/workbooks-tree-visualizations/tree-group-create.png)](./media/workbooks-tree-visualizations/tree-group-create.png#lightbox)

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su come creare un [grafico nelle cartelle di lavoro](workbooks-graph-visualizations.md)di.
* Informazioni su come creare un [riquadro nelle cartelle di lavoro](workbooks-tile-visualizations.md)di.
