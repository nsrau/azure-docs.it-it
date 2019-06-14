---
title: Tracce di OpenCensus Go con Azure Application Insights | Microsoft Docs
description: Fornisce istruzioni su come integrare tracce di OpenCensus Go con il servizio di inoltro locale e Application Insights
services: application-insights
keywords: ''
author: mrbullwinkle
ms.author: mbullwin
ms.date: 09/15/2018
ms.service: application-insights
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: cdf01fbbcc8ef1f90b2e0f8973f59c46c5bf70f8
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60577858"
---
# <a name="collect-distributed-traces-from-go-preview"></a>Raccogliere tracce distribuite da Go (anteprima)

Application Insights supporta ora tracce distribuite di applicazioni Go tramite l'integrazione con [OpenCensus](https://opencensus.io) e il nuovo [servizio di inoltro locale](./opencensus-local-forwarder.md). Questo articolo descrive in modo dettagliato il processo di configurazione di OpenCensus per Go e il recupero di dati di traccia in Application Insights.

## <a name="prerequisites"></a>Prerequisiti

- È necessaria una sottoscrizione di Azure.
- È necessario che sia installato Go, di cui in questo articolo viene usata la versione 1.11. [Download di Go](https://golang.org/dl/).
- Seguire le istruzioni per installare il [servizio d'inoltro locale come servizio Windows](./opencensus-local-forwarder.md).

Se non si ha una sottoscrizione di Azure, creare un account [gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="sign-in-to-the-azure-portal"></a>Accedere al portale di Azure

Accedere al [portale di Azure](https://portal.azure.com/).

## <a name="create-application-insights-resource"></a>Creare una risorsa di Application Insights

Prima di tutto, è necessario creare una risorsa di Application Insights, che genererà una chiave di strumentazione (iKey). La chiave di strumentazione verrà quindi usata per configurare il servizio d'inoltro locale per inviare tracce distribuite dall'applicazione OpenCensus instrumentata ad Application Insights.   

1. Selezionare **Crea una risorsa** > **Strumenti di sviluppo** > **Application Insights**.

   ![Aggiunta di una risorsa di Application Insights](./media/opencensus-Go/0001-create-resource.png)

   Verrà visualizzata una casella di configurazione. Usare la tabella seguente per completare i campi di input.

    | Impostazioni        | Value           | DESCRIZIONE  |
   | ------------- |:-------------|:-----|
   | **Nome**      | Valore globalmente univoco | Nome che identifica l'app da monitorare |
   | **Tipo di applicazione** | Generale | Tipo di app da monitorare |
   | **Gruppo di risorse**     | myResourceGroup      | Nome del nuovo gruppo di risorse per l'hosting dei dati di Application Insights |
   | **Posizione** | Stati Uniti orientali | Scegliere una località nelle vicinanze o vicina a quella in cui è ospitata l'app |

2. Fare clic su **Create**(Crea).

## <a name="configure-local-forwarder"></a>Configurare il servizio d'inoltro locale

1. Selezionare **Panoramica** > **Informazioni di base** > copiare la **chiave di strumentazione** dell'applicazione.

   ![Screenshot della chiave di strumentazione](./media/opencensus-Go/0003-instrumentation-key.png)

2. Modificare il file `LocalForwarder.config` e aggiungere la chiave di strumentazione. Se sono state seguite le istruzioni fornite nella sezione dei [prerequisiti](./opencensus-local-forwarder.md), il file si trova in `C:\LF-WindowsServiceHost`

    ```xml
      <OpenCensusToApplicationInsights>
        <!--
          Instrumentation key to track telemetry to.
          -->
        <InstrumentationKey>{enter-instrumentation-key}</InstrumentationKey>
      </OpenCensusToApplicationInsights>
    
      <!-- Describes aspects of processing Application Insights telemetry-->
      <ApplicationInsights>
        <LiveMetricsStreamInstrumentationKey>{enter-instrumentation-key}</LiveMetricsStreamInstrumentationKey>
      </ApplicationInsights>
    </LocalForwarderConfiguration>
    ```

3. Riavviare il **servizio d'inoltro locale** dell'applicazione.

## <a name="opencensus-go-packages"></a>Pacchetti OpenCensus Go

1. Installare i pacchetti OpenCensus per Go dalla riga di comando:

    ```go
    go get -u go.opencensus.io
    go get -u contrib.go.opencensus.io/exporter/ocagent
    ```

2. Aggiungere il codice seguente in un file con estensione GO e quindi compilarlo ed eseguirlo. Questo esempio è stato tratto dalle linee guida ufficiali di OpenCensus, a cui è stato aggiunto il codice che semplifica l'integrazione con il servizio d'inoltro locale.

     ```go
        // Copyright 2018, OpenCensus Authors
        //
        // Licensed under the Apache License, Version 2.0 (the "License");
        // you may not use this file except in compliance with the License.
        // You may obtain a copy of the License at
        //
        //     https://www.apache.org/licenses/LICENSE-2.0
        //
        // Unless required by applicable law or agreed to in writing, software
        // distributed under the License is distributed on an "AS IS" BASIS,
        // WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
        // See the License for the specific language governing permissions and
        // limitations under the License.
        package main
        
        import (
        
            "bytes"
            "fmt"
            "log"
            "net/http"
            os "os"
            
            ocagent "contrib.go.opencensus.io/exporter/ocagent"
            "go.opencensus.io/plugin/ochttp"
            "go.opencensus.io/plugin/ochttp/propagation/tracecontext"
            "go.opencensus.io/trace"
        
        )
        
        func main() {
            // Register stats and trace exporters to export the collected data.
            serviceName := os.Getenv("SERVICE_NAME")
            if len(serviceName) == 0 {
                serviceName = "go-app"
            }
            fmt.Printf(serviceName)
            agentEndpoint := os.Getenv("OCAGENT_TRACE_EXPORTER_ENDPOINT")

            if len(agentEndpoint) == 0 {
                agentEndpoint = fmt.Sprintf("%s:%d", ocagent.DefaultAgentHost, ocagent.DefaultAgentPort)
            }
        
            fmt.Printf(agentEndpoint)
            exporter, err := ocagent.NewExporter(ocagent.WithInsecure(), ocagent.WithServiceName(serviceName), ocagent.WithAddress(agentEndpoint))
        
            if err != nil {
                log.Printf("Failed to create the agent exporter: %v", err)
            }
        
            trace.RegisterExporter(exporter)
        
            trace.ApplyConfig(trace.Config{DefaultSampler: trace.AlwaysSample()})
        
            client := &http.Client{Transport: &ochttp.Transport{Propagation: &tracecontext.HTTPFormat{}}}
        
            http.HandleFunc("/", func(w http.ResponseWriter, req *http.Request) {
                fmt.Fprintf(w, "hello world")
        
                var jsonStr = []byte(`[ { "url": "http://blank.org", "arguments": [] } ]`)
                r, _ := http.NewRequest("POST", "http://blank.org", bytes.NewBuffer(jsonStr))
                r.Header.Set("Content-Type", "application/json")
        
                // Propagate the trace header info in the outgoing requests.
                r = r.WithContext(req.Context())
                resp, err := client.Do(r)
                if err != nil {
                    log.Println(err)
                } else {
                    // TODO: handle response
                    resp.Body.Close()
                }
            })
        
            http.HandleFunc("/call_blank", func(w http.ResponseWriter, req *http.Request) {
                fmt.Fprintf(w, "hello world")
        
                r, _ := http.NewRequest("GET", "http://blank.org", nil)

                // Propagate the trace header info in the outgoing requests.
                r = r.WithContext(req.Context())
                resp, err := client.Do(r)
        
                if err != nil {
                    log.Println(err)
                } else {
                    // TODO: handle response
                    resp.Body.Close()
                }        
            })
        
            log.Fatal(http.ListenAndServe(":50030", &ochttp.Handler{Propagation: &tracecontext.HTTPFormat{}}))
        
        }
     ```

3. Quando l'app Go semplice è in esecuzione, passare a `http://localhost:50030`. Ogni aggiornamento del browser genererà il testo "hello world" accompagnato dai dati di intervallo corrispondenti che vengono selezionati dal servizio di inoltro locale.

4. Per verificare che l'**agente di inoltro locale** recuperi le tracce, controllare il file `LocalForwarder.config`. Se è stata seguita la procedura descritta nella sezione dei [prerequisiti](https://docs.microsoft.com/azure/application-insights/local-forwarder), si troverà in `C:\LF-WindowsServiceHost`.

    Nell'immagine seguente del file di log è possibile osservare che prima dell'esecuzione del secondo script in cui è stata aggiunta un'utilità di esportazione, `OpenCensus input BatchesReceived` era impostato su 0. Dopo aver iniziato a eseguire lo script aggiornato, `BatchesReceived` è stato incrementato in base al numero di valori immessi:
    
    ![Modulo per una nuova risorsa di Application Insights](./media/opencensus-go/0004-batches-received.png)

## <a name="start-monitoring-in-the-azure-portal"></a>Avviare il monitoraggio nel portale di Azure

1. È ora possibile riaprire la pagina **Panoramica** di Application Insights nel portale di Azure per visualizzare i dettagli relativi all'applicazione attualmente in esecuzione. Selezionare **Flusso metriche attive**.

   ![Screenshot del riquadro Panoramica con Flusso metriche attive selezionato nella casella rossa](./media/opencensus-go/0005-overview-live-metrics-stream.png)

2. Se si esegue la seconda app Go e si aggiorna il browser per `http://localhost:50030`, i dati di traccia verranno visualizzati in tempo reale appena arrivano in Application Insights dal servizio di inoltro locale.

   ![Screenshot del flusso di metriche attive con dati sulle prestazioni visualizzati](./media/opencensus-go/0006-stream.png)

3. Tornare alla pagina **Panoramica** e selezionare **Mappa delle applicazioni** per ottenere un layout visivo delle relazioni di dipendenza e dei tempi di chiamata tra i componenti dell'applicazione.

    ![Screenshot della mappa delle applicazioni di base](./media/opencensus-go/0007-application-map.png)

    Poiché è stata tracciata solo una chiamata a un metodo, la mappa delle applicazioni non è così interessante. Tuttavia, la mappa delle applicazioni può essere ridimensionata in modo da visualizzare applicazioni molto più distribuite:

   ![Mappa delle applicazioni](media/opencensus-go/application-map.png)

4. Selezionare **Esamina prestazioni** per eseguire un'analisi dettagliata delle prestazioni e determinare la causa principale del rallentamento delle prestazioni.

    ![Screenshot del riquadro delle prestazioni](./media/opencensus-go/0008-performance.png)

5. Selezionando **Esempi** e quindi facendo clic su uno degli esempi visualizzati nel riquadro a destra, viene avviata l'esperienza dettagliata delle transazioni end-to-end. Mentre l'app di esempio mostrerà un solo evento, un'applicazione più complessa permetterebbe di esplorare le transazioni end-to-end fino al livello dello stack di chiamate di ogni singolo evento.

     ![Screenshot dell'interfaccia delle transazioni end-to-end](./media/opencensus-go/0009-end-to-end-transaction.png)

## <a name="opencensus-trace-for-go"></a>Traccia OpenCensus per Go

Sono state presentate solo le informazioni di base sull'integrazione di OpenCensus per Go con l'agente di inoltro locale e Application Insights. Le [linee guida ufficiali sull'uso di OpenCensus Go](https://godoc.org/go.opencensus.io) includono argomenti più avanzati.

## <a name="next-steps"></a>Passaggi successivi

* [Mappa delle applicazioni](./../../azure-monitor/app/app-map.md)
* [Monitoraggio delle prestazioni end-to-end](./../../azure-monitor/learn/tutorial-performance.md)
