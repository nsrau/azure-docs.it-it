---
title: Informazioni sulle metriche in Azure Spring Cloud
description: Informazioni su come esaminare le metriche nel cloud Spring di Azure
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 12/06/2019
ms.author: brendm
ms.openlocfilehash: 3f88857ea66f65ff90705bc1d1e3fb745cc33709
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/08/2020
ms.locfileid: "82982752"
---
# <a name="understand-metrics-for-azure-spring-cloud"></a>Informazioni sulle metriche per il cloud Spring di Azure

Esplora metriche di Azure è un componente del portale di Microsoft Azure che consente di tracciare grafici, correlare visivamente le tendenze e analizzare picchi e flessioni nelle metriche. Usare Esplora metriche per esaminare l'integrità e l'utilizzo delle risorse. 

Nel cloud Spring di Azure sono disponibili due punti di visualizzazione per le metriche.
* Grafici in ogni pagina di panoramica dell'applicazione
* Pagina metrica comune

 ![Grafici di metriche](media/metrics/metrics-1.png)

I grafici nella **Panoramica** dell'applicazione forniscono controlli di stato rapidi per ogni applicazione. La pagina **metrica** comune contiene tutte le metriche disponibili per riferimento. È possibile creare grafici personalizzati nella pagina metrica comune e aggiungerli nel dashboard.

## <a name="application-overview-page"></a>Pagina Panoramica applicazione
Selezionare un'app in **gestione app** per trovare i grafici nella pagina panoramica.  

 ![Gestione delle metriche delle applicazioni](media/metrics/metrics-2.png)

Ogni pagina **Panoramica sull'applicazione** presenta un grafico di metriche che consente di eseguire una rapida verifica dello stato dell'applicazione.  

 ![Cenni preliminari sulle metriche delle applicazioni](media/metrics/metrics-3.png)

Azure Spring cloud fornisce questi cinque grafici con metriche che vengono aggiornate ogni minuto:

* **Errori del server http**: numero di errori per le richieste HTTP all'app
* **Dati in**: byte ricevuti dall'app
* **Dati in uscita**: byte inviati dall'app
* **Richieste**: richieste ricevute dall'app
* **Tempo medio di risposta**: tempo medio di risposta dall'app

Per il grafico, è possibile selezionare un intervallo di tempo compreso tra un'ora e sette giorni.

## <a name="common-metrics-page"></a>Pagina metrica comune

Le **metriche** nel riquadro di spostamento a sinistra sono collegate alla pagina metrica comune.

Selezionare prima di tutto le metriche da visualizzare:

![Selezionare la visualizzazione delle metriche](media/metrics/metrics-4.png)

I dettagli di tutte le metriche sono disponibili nella [sezione](#user-metrics-options) seguente.

Selezionare quindi tipo di aggregazione per ogni metrica:

![Aggregazione metrica](media/metrics/metrics-5.png)

Il tipo di aggregazione indica come aggregare i punti metrica nel grafico in base all'ora. Esiste un punto di metrica non elaborato ogni minuto e il tipo di pre-aggregazione entro pochi minuti è predefinito dal tipo di metrica.
* Sum: somma tutti i valori come output di destinazione.
* Media: usare il valore medio nel periodo come output di destinazione.
* Max/min: usare il valore max/min nel periodo come output di destinazione.

È anche possibile modificare l'intervallo di tempo da visualizzare. L'intervallo di tempo può essere selezionato dagli ultimi 30 minuti agli ultimi 30 giorni o da un intervallo di tempo personalizzato.

![Modifica della metrica](media/metrics/metrics-6.png)

La visualizzazione predefinita include tutte le metriche dell'applicazione di un servizio cloud di Azure Spring. Le metriche di un'app o di un'istanza possono essere filtrate nella visualizzazione.  Fare clic su **Aggiungi filtro**, impostare la proprietà su **app**e selezionare l'applicazione di destinazione che si desidera monitorare nella casella di testo **valori** . 

È possibile utilizzare due tipi di filtri (proprietà):
* App: filtrare in base al nome dell'app
* Istanza: filtrare in base all'istanza dell'app

![Filtri metrica](media/metrics/metrics-7.png)

È anche possibile usare l'opzione **applica suddivisione** , che consente di creare più righe per un'app:

![Suddivisione delle metriche](media/metrics/metrics-8.png)

>[!TIP]
> È possibile creare grafici personalizzati nella pagina di metriche e aggiungerli al **dashboard**. Per iniziare, assegnare un nome al grafico.  Selezionare quindi **Aggiungi al dashboard** nell'angolo in alto a destra. È ora possibile controllare l'applicazione nel **dashboard** del portale.

## <a name="user-metrics-options"></a>Opzioni metriche utente

Le tabelle seguenti illustrano le metriche e i dettagli disponibili.

### <a name="error"></a>Errore
>[!div class="mx-tdCol2BreakAll"]
>| Nome | Nome metrica attuatore molla | Unità | Dettagli |
>|----|----|----|------------|
>| TomcatErrorCount<br><br>Errore globale di Tomcat (deprecato) | Tomcat. Global. Error | Conteggio | Numero di errori che si verificano nelle richieste elaborate |
>| Tomcat. Global. Error | Tomcat. Global. Error | Conteggio | Numero di errori che si verificano nelle richieste elaborate |

### <a name="performance"></a>Prestazioni
>[!div class="mx-tdCol2BreakAll"]
>| Nome | Nome metrica attuatore molla | Unità | Dettagli |
>|----|----|----|------------|
>| SystemCpuUsagePercentage<br><br>Percentuale di utilizzo della CPU di sistema (deprecata) | System. CPU. Usage | Percentuale | Utilizzo di CPU recente per l'intero sistema. Questo valore è un valore Double nell'intervallo [0.0, 1.0]. Il valore 0,0 indica che tutte le CPU erano inattive durante il periodo di tempo recente osservato, mentre un valore pari a 1,0 indica che tutte le CPU stavano eseguendo attivamente il 100% del tempo durante l'osservazione del periodo recente.|
>| System. CPU. Usage | System. CPU. Usage | Percentuale | Utilizzo di CPU recente per l'intero sistema. Questo valore è un valore Double nell'intervallo [0.0, 1.0]. Il valore 0,0 indica che tutte le CPU erano inattive durante il periodo di tempo recente osservato, mentre un valore pari a 1,0 indica che tutte le CPU stavano eseguendo attivamente il 100% del tempo durante l'osservazione del periodo recente.|
>| AppCpuUsagePercentage<br><br>Percentuale di utilizzo della CPU dell'app (deprecata) | Percentuale di utilizzo della CPU dell'app | Percentuale | Utilizzo di CPU recente per il processo Java Virtual Machine. Questo valore è un valore Double nell'intervallo [0.0, 1.0]. Il valore 0,0 indica che nessuna CPU stava eseguendo thread dal processo JVM durante il periodo di tempo recente osservato, mentre il valore 1,0 indica che tutte le CPU stavano eseguendo attivamente i thread dalla JVM 100% del tempo durante il periodo recente osservato. I thread del JVM includono i thread dell'applicazione, nonché i thread interni JVM.|
>| Process. CPU. Usage | Percentuale di utilizzo della CPU dell'app | Percentuale | Utilizzo di CPU recente per il processo Java Virtual Machine. Questo valore è un valore Double nell'intervallo [0.0, 1.0]. Il valore 0,0 indica che nessuna CPU stava eseguendo thread dal processo JVM durante il periodo di tempo recente osservato, mentre il valore 1,0 indica che tutte le CPU stavano eseguendo attivamente i thread dalla JVM 100% del tempo durante il periodo recente osservato. I thread del JVM includono i thread dell'applicazione, nonché i thread interni JVM.|
>| AppMemoryCommitted<br><br>Memoria app assegnata (deprecata) | JVM. memory. commit | Byte | Rappresenta la quantità di memoria che deve essere disponibile per l'utilizzo da parte della JVM. JVM può rilasciare memoria al sistema e il commit potrebbe essere inferiore a init. il commit sarà sempre maggiore o uguale a quello usato. |
>| JVM. memory. commit | JVM. memory. commit | Byte | Rappresenta la quantità di memoria che deve essere disponibile per l'utilizzo da parte della JVM. JVM può rilasciare memoria al sistema e il commit potrebbe essere inferiore a init. il commit sarà sempre maggiore o uguale a quello usato. |
>| AppMemoryUsed <br><br>Memoria app utilizzata (deprecata) | JVM. memory. used | Byte | Rappresenta la quantità di memoria attualmente utilizzata in byte. |
>| JVM. memory. used | JVM. memory. used | Byte | Rappresenta la quantità di memoria attualmente utilizzata in byte. |
>| AppMemoryMax<br><br>Memoria app massima (deprecata) | JVM. memory. max | Byte | Rappresenta la quantità massima di memoria che può essere utilizzata per la gestione della memoria. La quantità di memoria utilizzata e di cui è stato eseguito il commit sarà sempre minore o uguale a max se è definito max. Un'allocazione di memoria potrebbe non riuscire se tenta di aumentare la memoria utilizzata, in modo da usare > eseguito il commit anche se utilizzata <= Max sarebbe ancora true (ad esempio, quando il sistema è insufficiente nella memoria virtuale). |
>| JVM. memory. max | JVM. memory. max | Byte | Rappresenta la quantità massima di memoria che può essere utilizzata per la gestione della memoria. La quantità di memoria utilizzata e di cui è stato eseguito il commit sarà sempre minore o uguale a max se è definito max. Un'allocazione di memoria potrebbe non riuscire se tenta di aumentare la memoria utilizzata, in modo da usare > eseguito il commit anche se utilizzata <= Max sarebbe ancora true (ad esempio, quando il sistema è insufficiente nella memoria virtuale). |
>| MaxOldGenMemoryPoolBytes<br><br>Dimensioni massime dei dati di generazione precedenti disponibili (deprecato) | JVM. GC. max. Data. size | Byte | Utilizzo massimo della memoria del pool di memoria di generazione precedente dall'avvio della macchina virtuale Java. |
>| JVM. GC. max. Data. size | JVM. GC. max. Data. size | Byte | Utilizzo massimo della memoria del pool di memoria di generazione precedente dall'avvio della macchina virtuale Java. |
>| OldGenMemoryPoolBytes<br><br>Dimensioni dati di generazione precedenti (deprecato) | JVM. GC. Live. Data. size | Byte | Dimensioni del pool di memoria di generazione precedente dopo un catalogo globale completo. |
>| JVM. GC. Live. Data. size | JVM. GC. Live. Data. size | Byte | Dimensioni del pool di memoria di generazione precedente dopo un catalogo globale completo. |
>| OldGenPromotedBytes<br><br>Innalzamento di livello alle dimensioni dei dati di generazione precedente (deprecato) | JVM. GC. memory. promossi | Byte | Numero di aumenti positivi nelle dimensioni del pool di memoria precedente prima della GC dopo GC. |
>| JVM. GC. memory. promossi | JVM. GC. memory. promossi | Byte | Numero di aumenti positivi nelle dimensioni del pool di memoria precedente prima della GC dopo GC. |
>| YoungGenPromotedBytes<br><br>Innalzamento di livello a dimensioni dati di generazione giovani (deprecato) | JVM. GC. memory. allocato | Byte | Incrementato per un aumento delle dimensioni del pool di memoria di generazione giovani dopo un GC a prima del successivo. |
>| JVM. GC. memory. allocato | JVM. GC. memory. allocato | Byte | Incrementato per un aumento delle dimensioni del pool di memoria di generazione giovani dopo un GC a prima del successivo. |
>| GCPauseTotalCount<br><br>Conteggio delle pause GC (deprecato) | JVM. GC. pause (Total-count) | Conteggio | Numero totale di GC dopo l'avvio di questo Giuseppe, incluso GC recente e vecchio. |
>| JVM. GC. pause. Total. Count | JVM. GC. pause (Total-count) | Conteggio | Numero totale di GC dopo l'avvio di questo Giuseppe, incluso GC recente e vecchio. |
>| GCPauseTotalTime<br><br>Tempo totale di sospensione GC (obsoleto) | JVM. GC. pause (tempo totale) | Millisecondi | Tempo totale GC utilizzato dopo l'avvio di questo Giuseppe, inclusa la GC precedente e quella precedente. |
>| JVM. GC. pause. Total. Time | JVM. GC. pause (tempo totale) | Millisecondi | Tempo totale GC utilizzato dopo l'avvio di questo Giuseppe, inclusa la GC precedente e quella precedente. |
>| Tomcat. Threads. config. max | Tomcat. Threads. config. max | Conteggio | Numero massimo thread di configurazione Tomcat |
>| Tomcat. Threads. Current | Tomcat. Threads. Current | Conteggio | Conteggio thread correnti Tomcat |
>| Tomcat. Global. Request. AVG. Time | Tomcat. Global. Request. AVG. Time | Millisecondi | Tempo medio richiesta Tomcat |


### <a name="request"></a>Richiesta
>[!div class="mx-tdCol2BreakAll"]
>| Nome | Nome metrica attuatore molla | Unità | Dettagli |
>|----|----|----|------------|
>| TomcatSentBytes<br><br>Totale byte inviati Tomcat (deprecati) | Tomcat. Global. Sent | Byte | Quantità di dati inviati dal server Web Tomcat |
>| Tomcat. Global. Sent | Tomcat. Global. Sent | Byte | Quantità di dati inviati dal server Web Tomcat |
>| TomcatReceivedBytes<br><br>Totale byte ricevuti Tomcat (deprecati) | Tomcat. Global. received | Byte | Quantità di dati ricevuti dal server Web Tomcat |
>| Tomcat. Global. received | Tomcat. Global. received | Byte | Quantità di dati ricevuti dal server Web Tomcat |
>| TomcatRequestTotalTime<br><br>Tempo totale richiesta Tomcat (obsoleto) | Tomcat. Global. Request (tempo totale) | Millisecondi | Tempo totale del server Web Tomcat per l'elaborazione delle richieste |
>| TomcatRequestTotalCount<br><br>Conteggio totale richieste Tomcat (deprecato) | Tomcat. Global. Request (Totale-conteggio) | Conteggio | Numero totale di richieste elaborate dal server Web Tomcat |
>| Tomcat. Global. Request. Total. Count | Tomcat. Global. Request (Totale-conteggio) | Conteggio | Numero totale di richieste elaborate dal server Web Tomcat |
>| TomcatRequestMaxTime<br><br>Tempo massimo richiesta Tomcat (obsoleto) | Tomcat. Global. Request. max | Millisecondi | Tempo massimo di elaborazione di una richiesta da server Web Tomcat |
>| Tomcat. Global. Request. max | Tomcat. Global. Request. max | Millisecondi | Tempo massimo di elaborazione di una richiesta da server Web Tomcat |

### <a name="session"></a>sessione
>[!div class="mx-tdCol2BreakAll"]
>| Nome | Nome metrica attuatore molla | Unità | Dettagli |
>|----|----|----|------------|
>| TomcatSessionActiveMaxCount<br><br>Numero massimo attivo sessione Tomcat (deprecato) | Tomcat. Sessions. Active. max | Conteggio | Numero massimo di sessioni attive allo stesso tempo |
>| Tomcat. Sessions. Active. max | Tomcat. Sessions. Active. max | Conteggio | Numero massimo di sessioni attive allo stesso tempo |
>| TomcatSessionAliveMaxTime<br><br>Tempo massimo attivo della sessione Tomcat (obsoleto) | Tomcat. Sessions. Alive. max | Millisecondi | Tempo più lungo (in secondi) durante il quale una sessione scaduta è stata attiva |
>| Tomcat. Sessions. Alive. max | Tomcat. Sessions. Alive. max | Millisecondi | Tempo più lungo (in secondi) durante il quale una sessione scaduta è stata attiva |
>| TomcatSessionCreatedCount<br><br>Conteggio creato sessione Tomcat (deprecato) | Tomcat. Sessions. created | Conteggio | Numero di sessioni create |
>| Tomcat. Sessions. created | Tomcat. Sessions. created | Conteggio | Numero di sessioni create |
>| TomcatSessionExpiredCount<br><br>Conteggio scaduto sessione Tomcat (deprecato) | Tomcat. Sessions. scaduto | Conteggio | Numero di sessioni scadute |
>| Tomcat. Sessions. scaduto | Tomcat. Sessions. scaduto | Conteggio | Numero di sessioni scadute |
>| TomcatSessionRejectedCount<br><br>Conteggio rifiutato sessione Tomcat (deprecato) | Tomcat. Sessions. rifiutato | Conteggio | Numero di sessioni che non sono state create perché è stato raggiunto il numero massimo di sessioni attive. |
>| Tomcat. Sessions. rifiutato | Tomcat. Sessions. rifiutato | Conteggio | Numero di sessioni che non sono state create perché è stato raggiunto il numero massimo di sessioni attive. |
>| Tomcat. Sessions. Active. Current | Tomcat. Sessions. Active. Current | Conteggio | Conteggio attivo sessione Tomcat |

## <a name="see-also"></a>Vedere anche
* [Introduzione a Esplora metriche di Azure](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-getting-started)

* [Analizzare i log e le metriche con le impostazioni di diagnostica](https://docs.microsoft.com/azure/spring-cloud/diagnostic-services)

## <a name="next-steps"></a>Passaggi successivi
* [Esercitazione: monitorare le risorse cloud di Spring usando gli avvisi e i gruppi di azione](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-tutorial-alerts-action-groups)

* [Quote e piani di servizio per Azure Spring cloud](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-quotas)

