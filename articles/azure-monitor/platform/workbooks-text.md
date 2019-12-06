---
title: Parametri di testo per le cartelle di lavoro di monitoraggio di Azure
description: Semplifica la creazione di report complessi con cartelle di lavoro predefinite e personalizzate con parametri. Altre informazioni sui parametri di testo della cartella di lavoro.
services: azure-monitor
author: mrbullwinkle
manager: carmonm
ms.service: azure-monitor
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: mbullwin
ms.openlocfilehash: 3b7affb9c7d7be790d4279c1db8174a5dd3b2c2a
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/05/2019
ms.locfileid: "74872775"
---
# <a name="workbook-text-parameters"></a>Parametri di testo cartella di lavoro

I parametri TextBox forniscono un modo semplice per raccogliere input di testo dagli utenti della cartella di lavoro. Vengono usati quando non è pratico usare un elenco a discesa per raccogliere l'input (ad esempio, una soglia arbitraria o filtri generici). Le cartelle di lavoro consentono agli autori di ottenere il valore predefinito della casella di testo da una query. Questo consente scenari interessanti, ad esempio l'impostazione della soglia predefinita basata sull'P95 della metrica.

Un uso comune delle caselle di testo è come variabili interne usate da altri controlli della cartella di lavoro. Questa operazione viene eseguita sfruttando una query per i valori predefiniti e rendendo invisibile il controllo di input in modalità lettura. È ad esempio possibile che un utente desideri che una soglia provenga da una formula (non da un utente) e quindi usi la soglia nelle query successive.

## <a name="creating-a-text-parameter"></a>Creazione di un parametro di testo
1. Iniziare con una cartella di lavoro vuota in modalità di modifica.
2. Scegliere _Aggiungi parametri_ dai collegamenti all'interno della cartella di lavoro.
3. Fare clic sul pulsante _Aggiungi parametro_ blu.
4. Nel riquadro nuovo parametro visualizzato immettere:
    1. Nome parametro: `SlowRequestThreshold`
    2. Tipo di parametro: `Text`
    3. Obbligatorio: `checked`
    4. Ottieni valore predefinito dalla query: `unchecked`
5. Scegliere ' Salva ' dalla barra degli strumenti per creare il parametro.

    ![Immagine che mostra la creazione di un parametro di testo](./media/workbooks-text/text-create.png)

Questo è il modo in cui la cartella di lavoro sarà simile in modalità lettura.

![Immagine che mostra un parametro di testo in modalità lettura](./media/workbooks-text/text-readmode.png)

## <a name="referencing-a-text-parameter"></a>Riferimento a un parametro di testo
1. Aggiungere un controllo query alla cartella di lavoro selezionando il collegamento `Add query` blu e selezionando una risorsa Application Insights.
2. Nella casella KQL aggiungere il frammento di codice seguente:
    ```kusto
    requests
    | summarize AllRequests = count(), SlowRequests = countif(duration >= {SlowRequestThreshold}) by name
    | extend SlowRequestPercent = 100.0 * SlowRequests / AllRequests
    | order by SlowRequests desc
    ```
3. Utilizzando il parametro di testo con un valore di 500 associato al controllo di query, è possibile eseguire la query in modo efficace:
    ```kusto
    requests
    | summarize AllRequests = count(), SlowRequests = countif(duration >= 500) by name
    | extend SlowRequestPercent = 100.0 * SlowRequests / AllRequests
    | order by SlowRequests desc
    ```
4. Eseguire la query per visualizzare i risultati

    ![Immagine che mostra un parametro di testo a cui si fa riferimento in KQL](./media/workbooks-text/text-reference.png)


## <a name="setting-default-values"></a>Impostazione dei valori predefiniti
1. Iniziare con una cartella di lavoro vuota in modalità di modifica.
2. Scegliere _Aggiungi parametri_ dai collegamenti all'interno della cartella di lavoro.
3. Fare clic sul pulsante _Aggiungi parametro_ blu.
4. Nel riquadro nuovo parametro visualizzato immettere:
    1. Nome parametro: `SlowRequestThreshold`
    2. Tipo di parametro: `Text`
    3. Obbligatorio: `checked`
    4. Ottieni valore predefinito dalla query: `checked`
5. Nella casella KQL aggiungere il frammento di codice seguente:
    ```kusto
    requests
    | summarize round(percentile(duration, 95), 2)
    ```
    Questa query imposta il valore predefinito della casella di testo sulla durata del 95 ° percentile per tutte le richieste nell'app.
6. Eseguire la query per visualizzare il risultato
7. Scegliere ' Salva ' dalla barra degli strumenti per creare il parametro.

    ![Immagine che mostra un parametro di testo con valore predefinito da KQL](./media/workbooks-text/text-default-value.png)

> [!NOTE]
> Sebbene in questo esempio venga eseguita una query Application Insights dati, è possibile usare l'approccio per qualsiasi origine dati basata su log, Log Analytics, Azure Resource Graph e così via.

## <a name="next-steps"></a>Passaggi successivi

* Per [iniziare ad](workbooks-visualizations.md) apprendere altre informazioni sulle cartelle di lavoro, sono disponibili molte opzioni di visualizzazione avanzate.
* [Controllare](workbooks-access-control.md) e condividere l'accesso alle risorse della cartella di lavoro.
