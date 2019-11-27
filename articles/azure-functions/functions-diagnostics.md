---
title: Panoramica della diagnostica di funzioni di Azure
description: Informazioni su come risolvere i problemi dell'app per le funzioni con la diagnostica di funzioni di Azure.
author: yunjchoi
ms.topic: article
ms.date: 11/01/2019
ms.author: yunjchoi
ms.custom: na
ms.openlocfilehash: 71edb9a32254eeee123b874305dd25b699e05cc1
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74227012"
---
# <a name="azure-functions-diagnostics-overview"></a>Panoramica della diagnostica di funzioni di Azure

Quando si esegue un'app per le funzioni, si desidera prepararsi a eventuali problemi che possono verificarsi, da errori di 4xx per l'attivazione degli errori. Diagnostica funzioni di Azure è un'esperienza intelligente e interattiva che consente di risolvere i problemi dell'app per le funzioni senza alcuna configurazione o costi aggiuntivi. Quando si verificano problemi con l'app per le funzioni, la diagnostica di funzioni di Azure rileva gli errori che consentono di ottenere le informazioni appropriate per risolvere i problemi in modo più semplice e rapido e risolvere il problema. Questo articolo illustra le nozioni di base su come usare la diagnostica di funzioni di Azure per diagnosticare e risolvere più rapidamente i problemi comuni delle app per le funzioni.

## <a name="start-azure-functions-diagnostics"></a>Avviare la diagnostica di funzioni di Azure

Per accedere alla diagnostica di funzioni di Azure:

1. Passare all'app per le funzioni nella [portale di Azure](https://portal.azure.com).
2. Selezionare la scheda **funzionalità della piattaforma** .
3. Selezionare **diagnostica e Risolvi i problemi** in **Gestione risorse**, che consente di aprire diagnostica funzioni di Azure.
4. Scegliere una categoria che descriva meglio il problema dell'app per le funzioni usando le parole chiave nel riquadro della Home page. È anche possibile digitare una parola chiave che descrive meglio il problema nella barra di ricerca. Ad esempio, è possibile digitare `execution` per visualizzare un elenco di report di diagnostica correlati all'esecuzione dell'app per le funzioni e aprirli direttamente dalla Home page.

![Home page](./media/functions-diagnostics/homepage.png)

## <a name="use-the-interactive-interface"></a>Usare l'interfaccia interattiva

Dopo aver selezionato una categoria della Home page più adatta al problema dell'app per le funzioni, l'interfaccia interattiva di diagnostica di funzioni di Azure, Genie, può guidare l'utente nella diagnosi e nella risoluzione dei problemi dell'app. È possibile usare i collegamenti ai riquadri forniti da Genie per visualizzare il report di diagnostica completo della categoria di problemi a cui si è interessati. I collegamenti ai riquadri consentono di accedere direttamente alle metriche di diagnostica.

![Genie](./media/functions-diagnostics/genie.png)

Dopo aver selezionato un riquadro, è possibile visualizzare un elenco di argomenti correlati al problema descritto nel riquadro. In questi argomenti vengono forniti frammenti di informazioni rilevanti dal report completo. È possibile selezionare uno di questi argomenti per esaminare ulteriormente i problemi. Inoltre, è possibile selezionare **Visualizza report completo** per esplorare tutti gli argomenti in una singola pagina.

![Anteprima del report di diagnostica](./media/functions-diagnostics/preview-of-diagnostic-report.png)

## <a name="view-a-diagnostic-report"></a>Visualizzare un report di diagnostica

Dopo aver scelto un argomento, è possibile visualizzare un report di diagnostica specifico per l'app per le funzioni. I report di diagnostica usano le icone di stato per indicare eventuali problemi specifici dell'app. Viene visualizzata una descrizione dettagliata del problema, delle azioni consigliate, delle metriche correlate e dei documenti utili. I report di diagnostica personalizzati vengono generati da una serie di controlli eseguiti nell'app per le funzioni. I report di diagnostica possono essere uno strumento utile per individuare i problemi nell'app per le funzioni e guidare l'utente alla risoluzione del problema.

## <a name="find-the-problem-code"></a>Trovare il codice problema 

Per le funzioni basate su script, è possibile usare **l'esecuzione di funzioni** e gli **errori in app per le funzioni o segnalare errori** per restringere la riga di codice che causa eccezioni o errori. Questa funzionalità può essere utile per ottenere la causa principale e correggere i problemi da una riga di codice specifica. Questa opzione non è disponibile per C# le funzioni precompilate e Java.

![Report di diagnostica sugli errori di esecuzione della funzione](./media/functions-diagnostics/diagnostic-report-on-function-execution-errors.png)

![Eccezione di funzione](./media/functions-diagnostics/function-exception.png)

## <a name="next-steps"></a>Passaggi successivi

È possibile porre domande o fornire commenti e suggerimenti sulla diagnostica di funzioni di Azure in [UserVoice](https://feedback.azure.com/forums/355860-azure-functions). Includere `[Diag]` nel titolo dei commenti.

> [!div class="nextstepaction"]
> [Monitorare le app per le funzioni](functions-monitoring.md)
