---
ms.openlocfilehash: ed7be82146f38cc7ae57fd863bb0c1b8e6910fd2
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/20/2020
ms.locfileid: "88691173"
---
Per eseguire il codice di esempio, seguire questa procedura:

1. In Visual Studio Code passare a *src/cloud-to-device-console-app/operations.json*.
1. Nel nodo **GraphTopologySet** assicurarsi che sia impostato il valore seguente:

    `"topologyUrl" : "https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/motion-detection/topology.json"`
1. Nei nodi **GraphInstanceSet** e **GraphTopologyDelete** assicurarsi che il valore di `topologyName` corrisponda al valore della proprietà `name` nella topologia del grafo:

    `"topologyName" : "MotionDetection"`
    
1. Avviare una sessione di debug premendo F5. Nella finestra **TERMINALE** verranno visualizzati alcuni messaggi.
1. Il file *operations.json* verrà avviato con le chiamate a `GraphTopologyList` e `GraphInstanceList`. Se dopo aver completato gli argomenti di avvio rapido precedenti sono state pulite le risorse, questa procedura restituirà elenchi vuoti e quindi verrà sospesa. Per continuare, premere INVIO.

    ```
    --------------------------------------------------------------------------
    Executing operation GraphTopologyList
    -----------------------  Request: GraphTopologyList  --------------------------------------------------
    {
        "@apiVersion": "1.0"
    }
    ---------------  Response: GraphTopologyList - Status: 200  ---------------
    {
        "value": []
    }
    --------------------------------------------------------------------------
    Executing operation WaitForInput
    Press Enter to continue
    ```
    
    La finestra **TERMINALE** mostra il set successivo di chiamate ai metodi diretti:
     * Una chiamata a `GraphTopologySet` che usa `topologyUrl` precedente
     * Una chiamata a `GraphInstanceSet` che usa il corpo seguente:
         
    ```
    {
      "@apiVersion": "1.0",
      "name": "Sample-Graph",
      "properties": {
        "topologyName": "MotionDetection",
        "description": "Sample graph description",
        "parameters": [
          {
            "name": "rtspUrl",
            "value": "rtsp://rtspsim:554/media/camera-300s.mkv"
          },
          {
            "name": "rtspUserName",
            "value": "testuser"
          },
          {
            "name": "rtspPassword",
            "value": "testpassword"
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
    * Una chiamata finale a `GraphTopologyList` mostra che l'elenco è vuoto.
