---
title: Panoramica della diagnostica di Funzioni di Azure
description: Informazioni su come risolvere i problemi dell'app per le funzioni con la diagnostica di Funzioni di Azure.
author: yunjchoi
ms.topic: article
ms.date: 11/01/2019
ms.author: yunjchoi
ms.custom: na
ms.openlocfilehash: fb329273d442c023233fee2e7e01aabe5f5bff8c
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83122367"
---
# <a name="azure-functions-diagnostics-overview"></a>Panoramica della diagnostica di Funzioni di Azure

Quando si esegue un'app per le funzioni, è utile essere pronti ad affrontare gli eventuali problemi che possono verificarsi, dagli errori di tipo 4xx a quelli dei trigger. La diagnostica di Funzioni di Azure offre un'esperienza interattiva e intelligente che consente di risolvere i problemi delle app per le funzioni senza configurazioni o costi aggiuntivi. Quando si verificano problemi con l'app per le funzioni, la diagnostica di Funzioni di Azure indica gli errori. Vengono fornite le informazioni appropriate per individuare e risolvere i problemi in modo più semplice e rapido. Questo articolo illustra le nozioni di base per l'uso della diagnostica di Funzioni di Azure per diagnosticare e risolvere più rapidamente i problemi comuni delle app per le funzioni.

## <a name="start-azure-functions-diagnostics"></a>Avviare la diagnostica di Funzioni di Azure

Per avviare la diagnostica di Funzioni di Azure:

1. Passare all'app per le funzioni nel [portale di Azure](https://portal.azure.com).
1. Selezionare **Diagnostica e risoluzione dei problemi** per aprire la diagnostica di Funzioni di Azure.
1. Scegliere una categoria che descriva bene il problema dell'app per le funzioni usando le parole chiave nel riquadro della home page. È anche possibile digitare una parola chiave che descriva il problema nella barra di ricerca. È ad esempio possibile digitare `execution` per visualizzare un elenco di report di diagnostica correlati all'esecuzione dell'app per le funzioni e aprirli direttamente dalla home page.

   :::image type="content" source="./media/functions-diagnostics/functions-app-search-azure-functions-diagnostics.png" alt-text="Ricerca di informazioni di diagnostica di Funzioni di Azure." border="true":::

## <a name="use-the-interactive-interface"></a>Usare l'interfaccia interattiva

Dopo aver selezionato una categoria della home page pertinente per il problema dell'app per le funzioni, l'interfaccia interattiva di diagnostica di Funzioni di Azure, detta Genie, può guidare l'utente nella diagnosi e nella risoluzione del problema dell'app. È possibile usare i collegamenti nei riquadri forniti da Genie per visualizzare il report di diagnostica completo della categoria di problemi a cui si è interessati. I collegamenti nei riquadri consentono di accedere direttamente alle metriche di diagnostica.

:::image type="content" source="./media/functions-diagnostics/genie.png" alt-text="Genie è un'interfaccia di diagnostica di Funzioni di Azure." border="false":::

Dopo aver selezionato un riquadro, è possibile visualizzare un elenco di argomenti correlati al problema descritto nel riquadro. Questi argomenti forniscono frammenti del report completo contenenti informazioni rilevanti. Selezionare uno di questi argomenti per esaminare ulteriormente i problemi. È anche possibile selezionare **Visualizza report completo** per esplorare tutti gli argomenti in un'unica pagina.

:::image type="content" source="./media/functions-diagnostics/preview-of-diagnostic-report.png" alt-text="Anteprima del report di diagnostica" border="false":::

## <a name="view-a-diagnostic-report"></a>Visualizzare un report di diagnostica

Dopo aver scelto un argomento, è possibile visualizzare un report di diagnostica specifico per l'app per le funzioni. I report di diagnostica usano le icone di stato per indicare se sono presenti problemi specifici dell'app. Vengono visualizzati una descrizione dettagliata del problema, le azioni consigliate, le metriche correlate e i documenti utili. I report di diagnostica personalizzati vengono generati da una serie di controlli eseguiti sull'app per le funzioni. I report di diagnostica possono essere uno strumento utile per individuare i problemi nell'app per le funzioni e guidare l'utente alla risoluzione del problema.

## <a name="find-the-problem-code"></a>Individuare il codice problematico

Per le funzioni basate su script, è possibile usare **Function Execution and Errors** (Esecuzione ed errori funzioni) in **Function App Down or Reporting Errors** (Mancata disponibilità o errori app per le funzioni) per visualizzare le righe di codice che causano eccezioni o errori. È possibile usare questo strumento per ottenere la causa radice e correggere i problemi di una riga di codice specifica. Questa opzione non è disponibile per le funzioni Java e C# precompilate.

:::image type="content" source="./media/functions-diagnostics/diagnostic-report-on-function-execution-errors.png" alt-text="Report di diagnostica sugli errori di esecuzione della funzione" border="false":::

:::image type="content" source="./media/functions-diagnostics/function-exception.png" alt-text="Visualizzazione dei dettagli dell'eccezione." border="false":::

## <a name="next-steps"></a>Passaggi successivi

È possibile porre domande o inviare feedback sulla diagnostica di Funzioni di Azure tramite [UserVoice](https://feedback.azure.com/forums/355860-azure-functions). Includere `[Diag]` nel titolo del feedback.

> [!div class="nextstepaction"]
> [Monitorare le app per le funzioni](functions-monitoring.md)
