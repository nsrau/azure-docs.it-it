---
title: Usare un Notebook di Jupyter per analizzare i dati in Esplora dati di Azure
description: In questo argomento illustra come analizzare i dati in Esplora dati di Azure usando un Notebook di Jupyter e l'estensione Kqlmagic.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 12/19/2018
ms.openlocfilehash: 896a5d13279c15f0035f214da3d5a7d7e6f1861f
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2019
ms.locfileid: "59045066"
---
# <a name="use-a-jupyter-notebook-and-kqlmagic-extension-to-analyze-data-in-azure-data-explorer"></a>Usare un Notebook di Jupyter e Kqlmagic l'estensione per analizzare i dati in Esplora dati di Azure

Jupyter Notebook è un'applicazione Web open source che consente di creare e condividere documenti contenenti codice attivo, equazioni, visualizzazioni e testo narrativo. L'uso include la pulizia e la trasformazione dei dati, simulazioni numeriche, modellazioni statistiche, visualizzazioni di dati e apprendimento automatico.
[Jupyter Notebook](https://jupyter.org/) supporta funzioni magic che estendono le funzionalità del kernel attraverso il supporto di comandi aggiuntivi. KQL magic è un comando che estende le funzionalità del kernel Python in Jupyter Notebook consentendo all'utente di eseguire le query in linguaggio Kusto in modo nativo. È possibile combinare facilmente il linguaggio di query Kusto e Python per eseguire query e visualizzare i dati tramite la libreria avanzata Plot.ly integrata con i comandi `render`. Le origini dati per l'esecuzione di query sono supportate. Queste origini dati includono Esplora dati di Azure, un servizio di esplorazione dei dati rapido e a scalabilità elevata per i log e dati di telemetria, nonché i log di monitoraggio di Azure e Application Insights. KQL magic funziona anche con Azure Notebooks, Jupyter Lab e l'estensione Jupyter di Visual Studio Code.

## <a name="prerequisites"></a>Prerequisiti

- Un account di posta elettronica dell'organizzazione che sia membro di Azure Active Directory (AAD).
- Jupyter Notebook installato nel computer locale oppure usare Azure Notebooks e clonare il [Notebook di Azure](https://kustomagicsamples-manojraheja.notebooks.azure.com/j/notebooks/Getting%20Started%20with%20kqlmagic%20on%20Azure%20Data%20Explorer.ipynb) di esempio

## <a name="install-kql-magic-library"></a>Installare la libreria KQL magic

1. Installare KQL magic:

    ```python
    !pip install Kqlmagic --no-cache-dir  --upgrade
    ```
    > [!NOTE]
    > Quando si usa Azure Notebooks, questo passaggio non è obbligatorio.

1. Caricare KQL magic:

    ```python
    reload_ext Kqlmagic
    ```

## <a name="connect-to-the-azure-data-explorer-help-cluster"></a>Connettersi al cluster della guida di Esplora dati di Azure

Usare il comando seguente per connettersi al database *Esempi* ospitato nel cluster della *guida*. Per gli utenti non Microsoft AAD, sostituire il nome del tenant `Microsoft.com` con il Tenant AAD.

```python
%kql AzureDataExplorer://tenant="Microsoft.com";code;cluster='help';database='Samples'
```

## <a name="query-and-visualize"></a>Eseguire una query e visualizzare

Eseguire la query dei dati usando l'[operatore di rendering](/azure/kusto/query/renderoperator) e visualizzare i dati tramite la libreria ploy.ly. La query e la visualizzazione forniscono un'esperienza integrata che fa uso di KQL (linguaggio di query Kusto) nativo. Kqlmagic supporta la maggior parte dei grafici tranne `timepivot`, `pivotchart` e `ladderchart`. Il rendering è supportato con tutti gli attributi tranne `kind`, `ysplit` e `accumulate`. 

### <a name="query-and-render-piechart"></a>Eseguire una query e il rendering del grafico a torta

```python
%%kql
StormEvents
| summarize statecount=count() by State
| sort by statecount 
| limit 10
| render piechart title="My Pie Chart by State"
```

### <a name="query-and-render-timechart"></a>Eseguire una query e il rendering del diagramma temporale

```python
%%kql
StormEvents
| summarize count() by bin(StartTime,7d)
| render timechart
```

> [!NOTE]
> I grafici sono interattivi. Selezionare un intervallo di tempo per ingrandire un momento specifico.

### <a name="customize-the-chart-colors"></a>Personalizzare i colori del grafico

Se la tavolozza predefinita dei colori non è di proprio gradimento, personalizzare i grafici usando le opzioni di tavolozza. Le tavolozze disponibili sono reperibili qui: [Choose colors palette for your KQL magic query chart result](https://mybinder.org/v2/gh/Microsoft/jupyter-Kqlmagic/master?filepath=notebooks%2FColorYourCharts.ipynb) (Scegliere la tavolozza dei colori per il risultato del grafico della query di KQL magic)

1. Per un elenco delle tavolozze:

    ```python
    %kql --palettes -popup_window
    ```

1. Selezionare la tavolozza dei colori `cool` ed eseguire nuovamente il rendering della query:

    ```python
    %%kql -palette_name "cool"
    StormEvents
    | summarize statecount=count() by State
    | sort by statecount
    | limit 10
    | render piechart title="My Pie Chart by State"
    ```

## <a name="parameterize-a-query-with-python"></a>Stabilire i parametri di una query con Python

KQL magic consente un interscambio semplice tra il linguaggio di query Kusto e Python. Per altre informazioni: [Parametrize your KQL magic query with Python](https://mybinder.org/v2/gh/Microsoft/jupyter-Kqlmagic/master?filepath=notebooks%2FParametrizeYourQuery.ipynb) (Stabilire i parametri della query di KQL magic con Python)

### <a name="use-a-python-variable-in-your-kql-query"></a>Usare una variabile di Python nella query KQL

È possibile usare il valore di una variabile di Python nella query per filtrare i dati:

```python
statefilter = ["TEXAS", "KANSAS"]
```

```python
%%kql
let _state = statefilter;
StormEvents 
| where State in (_state) 
| summarize statecount=count() by bin(StartTime,1d), State
| render timechart title = "Trend"
```

### <a name="convert-query-results-to-pandas-dataframe"></a>Convertire i risultati della query in dataframe Pandas

È possibile accedere ai risultati di una query KQL in dataframe Pandas. Accedere ai risultati dell'ultima query eseguita tramite la variabile `_kql_raw_result_` e convertire facilmente i risultati in dataframe Pandas come indicato di seguito:

```python
df = _kql_raw_result_.to_dataframe()
df.head(10)
```

### <a name="example"></a>Esempio

In molti scenari di analitica è possibile creare notebook riutilizzabili contenenti molte query e distribuire i risultati da una query a quelle successive. L'esempio seguente usa la variabile di Python `statefilter` per filtrare i dati.

1. Eseguire una query per visualizzare i primi 10 stati con `DamageProperty` massima:

    ```python
    %%kql
    StormEvents
    | summarize max(DamageProperty) by State
    | order by max_DamageProperty desc
    | limit 10
    ```

1. Eseguire una query per estrarre lo stato principale e impostarlo in una variabile di Python:

    ```python
    df = _kql_raw_result_.to_dataframe()
    statefilter =df.loc[0].State
    statefilter
    ```

1. Eseguire una query usando l'istruzione `let` e la variabile di Python:

    ```python
    %%kql
    let _state = statefilter;
    StormEvents 
    | where State in (_state)
    | summarize statecount=count() by bin(StartTime,1d), State
    | render timechart title = "Trend"
    ```

1. Eseguire il comando Guida:

    ```python
    %kql --help "help"
    ```

## <a name="next-steps"></a>Passaggi successivi

Eseguire il comando Guida per esplorare i notebook di esempio seguenti contenenti tutte le funzionalità supportate:
- [Get started with KQL magic for Azure Data Explorer](https://mybinder.org/v2/gh/Microsoft/jupyter-Kqlmagic/master?filepath=notebooks%2FQuickStart.ipynb) (Introduzione a KQL magic per Esplora dati di Azure) 
- [Get started with KQL magic for Application Insights](https://mybinder.org/v2/gh/Microsoft/jupyter-Kqlmagic/master?filepath=notebooks%2FQuickStartAI.ipynb) (Introduzione a KQL magic per Application Insights) 
- [Introduzione a magic KQL per i log di monitoraggio di Azure](https://mybinder.org/v2/gh/Microsoft/jupyter-Kqlmagic/master?filepath=notebooks%2FQuickStartLA.ipynb) 
- [Parametrize your KQL magic query with Python](https://mybinder.org/v2/gh/Microsoft/jupyter-Kqlmagic/master?filepath=notebooks%2FParametrizeYourQuery.ipynb) (Stabilire i parametri della query di KQL magic con Python) 
- [Choose colors palette for your KQL magic query chart result](https://mybinder.org/v2/gh/Microsoft/jupyter-Kqlmagic/master?filepath=notebooks%2FColorYourCharts.ipynb) (Scegliere la tavolozza dei colori per il risultato del grafico della query di KQL magic)
