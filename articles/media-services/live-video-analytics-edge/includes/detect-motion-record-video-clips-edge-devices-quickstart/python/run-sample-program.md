---
ms.openlocfilehash: f5e180cb85e65cf832ffe0a3746e25790644e1ba
ms.sourcegitcommit: 23aa0cf152b8f04a294c3fca56f7ae3ba562d272
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/07/2020
ms.locfileid: "91829231"
---
1. In Visual Studio Code aprire la scheda **Estensioni** (oppure premere CTRL+MAIUSC+X) e cercare Hub IoT di Azure.
1. Fare clic con il pulsante destro del mouse e scegliere **Impostazioni estensione**.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="../../../media/run-program/extensions-tab.png" alt-text="Impostazioni estensione&quot;:::
1. Cercare e abilitare &quot;Show Verbose Message" (Visualizza messaggio dettagliato).

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="../../../media/run-program/show-verbose-message.png" alt-text="Impostazioni estensione&quot;:::
1. Cercare e abilitare &quot;Show Verbose Message"
        }
      ]
    }
  }
  ```
    
  * Una chiamata a `GraphInstanceActivate` che avvia l'istanza del grafo e il flusso del video.
  * Una seconda chiamata a `GraphInstanceList` che mostra che l'istanza del grafo è in esecuzione.
1. L'output della finestra **TERMINALE** viene sospeso in corrispondenza di `Press Enter to continue`. Non premere ancora INVIO. Scorrere in alto per visualizzare i payload della risposta JSON per i metodi diretti richiamati.
1. Passare alla finestra **OUTPUT** in Visual Studio Code. Vengono visualizzati i messaggi inviati dal modulo Analisi video live in IoT Edge all'hub IoT. La sezione seguente di questo argomento di avvio rapido descrive questi messaggi.
1. Il grafo multimediale continua a essere eseguito e a stampare i risultati. Il simulatore RTSP continua a eseguire il video di origine in un ciclo. Quindi, per arrestare il grafo multimediale, tornare nella finestra **TERMINALE** e premere INVIO. 

    La serie successiva di chiamate pulisce le risorse:

    * Una chiamata a `GraphInstanceDeactivate` disattiva l'istanza del grafo.
    * Una chiamata a `GraphInstanceDelete` elimina l'istanza.
    * Una chiamata a `GraphTopologyDelete` elimina la topologia.
    * Una chiamata finale a `GraphTopologyList` mostra che l'elenco è ora vuoto.
