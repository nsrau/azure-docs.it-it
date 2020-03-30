---
title: Parametri di testo delle cartelle di lavoro di Azure MonitorAzure Monitor workbooks text parameters
description: Semplifica la creazione di report complessi con cartelle di lavoro con parametri predefinite e personalizzate. Ulteriori informazioni sui parametri di testo della cartella di lavoro.
services: azure-monitor
author: mrbullwinkle
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: mbullwin
ms.openlocfilehash: c5fb585d0eb6aeb7866c2ab04b324ee31fe903ca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77658050"
---
# <a name="workbook-text-parameters"></a>Parametri di testo della cartella di lavoro

I parametri della casella di testo forniscono un modo semplice per raccogliere input di testo dagli utenti della cartella di lavoro. Vengono utilizzati quando non è pratico utilizzare un elenco a discesa per raccogliere l'input (ad esempio, una soglia arbitraria o filtri generici). Le cartelle di lavoro consentono agli autori di ottenere il valore predefinito della casella di testo da una query. Ciò consente scenari interessanti come l'impostazione della soglia predefinita in base al p95 della metrica.

Un utilizzo comune delle caselle di testo è come variabili interne utilizzate da altri controlli della cartella di lavoro. Questa operazione viene eseguita sfruttando una query per i valori predefiniti e rendendo invisibile il controllo di input in modalità di lettura. Ad esempio, un utente potrebbe desiderare che una soglia provenga da una formula (non da un utente) e quindi utilizzare la soglia nelle query successive.

## <a name="creating-a-text-parameter"></a>Creazione di un parametro di testo
1. Iniziare con una cartella di lavoro vuota in modalità di modifica.
2. Scegliere _Aggiungi parametri_ dai collegamenti all'interno della cartella di lavoro.
3. Fare clic sul pulsante blu _Aggiungi parametro._
4. Nel nuovo riquadro dei parametri visualizzato immettere:
    1. Nome parametro:`SlowRequestThreshold`
    2. Tipo di parametro:`Text`
    3. Obbligatorio:`checked`
    4. Ottenere il valore predefinito dalla query:Get default value from query:`unchecked`
5. Scegliere 'Salva' dalla barra degli strumenti per creare il parametro.

    ![Immagine che mostra la creazione di un parametro di testo](./media/workbooks-text/text-create.png)

Questo è l'aspetto della cartella di lavoro in modalità di lettura.

![Immagine che mostra un parametro di testo in modalità di lettura](./media/workbooks-text/text-readmode.png)

## <a name="referencing-a-text-parameter"></a>Riferimento a un parametro di testo
1. Aggiungere un controllo query alla cartella `Add query` di lavoro selezionando il collegamento blu e selezionare una risorsa di Application Insights.Add a query control to the workbook by selecting the blue link and select an Application Insights resource.
2. Nella casella KQL aggiungere il frammento di codice seguente:In the KQL box, add this snippet:
    ```kusto
    requests
    | summarize AllRequests = count(), SlowRequests = countif(duration >= {SlowRequestThreshold}) by name
    | extend SlowRequestPercent = 100.0 * SlowRequests / AllRequests
    | order by SlowRequests desc
    ```
3. Utilizzando il parametro text con un valore di 500 associato al controllo query è effettivamente in esecuzione la query riportata di seguito:
    ```kusto
    requests
    | summarize AllRequests = count(), SlowRequests = countif(duration >= 500) by name
    | extend SlowRequestPercent = 100.0 * SlowRequests / AllRequests
    | order by SlowRequests desc
    ```
4. Eseguire una query per visualizzare i risultati

    ![Immagine che mostra un parametro di testo a cui si fa riferimento in KQL](./media/workbooks-text/text-reference.png)


## <a name="setting-default-values"></a>Impostazione dei valori predefiniti
1. Iniziare con una cartella di lavoro vuota in modalità di modifica.
2. Scegliere _Aggiungi parametri_ dai collegamenti all'interno della cartella di lavoro.
3. Fare clic sul pulsante blu _Aggiungi parametro._
4. Nel nuovo riquadro dei parametri visualizzato immettere:
    1. Nome parametro:`SlowRequestThreshold`
    2. Tipo di parametro:`Text`
    3. Obbligatorio:`checked`
    4. Ottenere il valore predefinito dalla query:Get default value from query:`checked`
5. Nella casella KQL aggiungere il frammento di codice seguente:In the KQL box, add this snippet:
    ```kusto
    requests
    | summarize round(percentile(duration, 95), 2)
    ```
    Questa query imposta il valore predefinito della casella di testo sulla durata del 95esimo percentile per tutte le richieste nell'app.
6. Eseguire una query per visualizzare il risultato
7. Scegliere 'Salva' dalla barra degli strumenti per creare il parametro.

    ![Immagine che mostra un parametro di testo con valore predefinito da KQL](./media/workbooks-text/text-default-value.png)

> [!NOTE]
> Mentre questo esempio esegue query sui dati di Application Insights, l'approccio può essere usato per qualsiasi origine dati basata su log: Log Analytics, Azure Resource Graph e così via.

## <a name="next-steps"></a>Passaggi successivi

* [Iniziare a](workbooks-visualizations.md) conoscere le cartelle di lavoro su molte opzioni di visualizzazione avanzate.
* [Controllare](workbooks-access-control.md) e condividere l'accesso alle risorse della cartella di lavoro.
