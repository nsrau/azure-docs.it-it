---
title: Cenni preliminari sulla diagnostica di Funzioni di AzureAzure Functions diagnostics Overview
description: Informazioni su come risolvere i problemi relativi all'app per le funzioni con la diagnostica di Funzioni di Azure.Learn how you can troubleshoot issues with your function app with Azure Functions diagnostics.
author: yunjchoi
ms.topic: article
ms.date: 11/01/2019
ms.author: yunjchoi
ms.custom: na
ms.openlocfilehash: 41acc5703f6b6e5cdeedf2afd0dc61b23bc579c6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75834041"
---
# <a name="azure-functions-diagnostics-overview"></a>Panoramica della diagnostica di Funzioni di AzureAzure Functions diagnostics overview

Quando si esegue un'app per le funzioni, si vuole essere preparati per eventuali problemi che possono verificarsi, da errori 4xx per attivare gli errori. La diagnostica di Funzioni di Azure è un'esperienza intelligente e interattiva che consente di risolvere i problemi dell'app per le funzioni senza costi aggiuntivi o di configurazione. Quando si verificano problemi con l'app per le funzioni, la diagnostica di Funzioni di Azure indica cosa è sbagliato per visualizzare le informazioni corrette per risolvere e risolvere il problema in modo più semplice e rapido. Questo articolo illustra le nozioni di base su come usare la diagnostica di Funzioni di Azure per diagnosticare e risolvere più rapidamente i problemi comuni delle app per le funzioni.

## <a name="start-azure-functions-diagnostics"></a>Avviare la diagnostica di Funzioni di AzureStart Azure Functions diagnostics

Per accedere alla diagnostica di Funzioni di Azure:To access Azure Functions diagnostics:

1. Passare all'app per le funzioni nel portale di [Azure.](https://portal.azure.com)
2. Selezionare la scheda **Funzionalità piattaforma.**
3. Selezionare **Diagnostica e risolvi problemi** in Gestione risorse , che apre la diagnostica di Funzioni di Azure.Select Diagnose and solve problems under Resource **Management**, which opens Azure Functions diagnostics.
4. Scegli una categoria che meglio descriva il problema della tua app per le funzioni usando le parole chiave nel riquadro della home page. Puoi anche digitare una parola chiave che descriva meglio il tuo problema nella barra di ricerca. Ad esempio, è `execution` possibile digitare per visualizzare un elenco di report diagnostici correlati all'esecuzione dell'app per le funzioni e aprirli direttamente dalla home page.

![Home page](./media/functions-diagnostics/homepage.png)

## <a name="use-the-interactive-interface"></a>Utilizzare l'interfaccia interattiva

Dopo aver selezionato una categoria di home page che meglio si allinea al problema dell'app per le funzioni, l'interfaccia interattiva della diagnostica di Funzioni di Azure, Genie, può guidare l'utente attraverso la diagnosi e la risoluzione del problema dell'app. È possibile utilizzare i tasti di scelta rapida dei riquadri forniti da Genie per visualizzare il report diagnostico completo della categoria di problemi a cui si è interessati. I collegamenti ai riquadri offrono un modo diretto per accedere alle metriche di diagnostica.

![Genie](./media/functions-diagnostics/genie.png)

Dopo aver selezionato un riquadro, è possibile visualizzare un elenco di argomenti correlati al problema descritto nel riquadro. Questi argomenti forniscono frammenti di informazioni importanti dal report completo. È possibile selezionare uno qualsiasi di questi argomenti per analizzare ulteriormente i problemi. Inoltre, è possibile selezionare **Visualizza report completo** per esplorare tutti gli argomenti in una singola pagina.

![Anteprima del rapporto di diagnostica](./media/functions-diagnostics/preview-of-diagnostic-report.png)

## <a name="view-a-diagnostic-report"></a>Visualizzare un rapporto di diagnostica

Dopo aver scelto un argomento, è possibile visualizzare un report di diagnostica specifico per l'app per le funzioni. I rapporti di diagnostica usano le icone di stato per indicare se ci sono problemi specifici con la tua app. Viene visualizzata una descrizione dettagliata del problema, le azioni consigliate, le metriche correlate e i documenti utili. I report di diagnostica personalizzati vengono generati da una serie di controlli eseguiti nell'app per le funzioni. I rapporti di diagnostica possono essere uno strumento utile per individuare i problemi nell'app per le funzioni e guidare l'utente alla risoluzione del problema.

## <a name="find-the-problem-code"></a>Trovare il codice del problema

Per le funzioni basate su script, è possibile usare **L'esecuzione e gli errori** delle funzioni in **Function App Down o la segnalazione di errori** nella riga di codice che causano eccezioni o errori. Questa funzionalità può essere uno strumento utile per ottenere la causa principale e risolvere i problemi da una riga di codice specifica. Questa opzione non è disponibile per le funzioni precompilate di C e Java.

![Rapporto di diagnostica sugli errori di esecuzione delle funzioni](./media/functions-diagnostics/diagnostic-report-on-function-execution-errors.png)

![Eccezione di funzione](./media/functions-diagnostics/function-exception.png)

## <a name="next-steps"></a>Passaggi successivi

È possibile porre domande o fornire commenti e suggerimenti sulla diagnostica di Funzioni di Azure in [UserVoice](https://feedback.azure.com/forums/355860-azure-functions). Si `[Diag]` prega di includere nel titolo del tuo feedback.

> [!div class="nextstepaction"]
> [Monitorare le app per le funzioniMonitor your function apps](functions-monitoring.md)
