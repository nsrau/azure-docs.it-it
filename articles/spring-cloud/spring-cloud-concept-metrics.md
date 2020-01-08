---
title: Informazioni sulle metriche in Azure Spring Cloud
description: Informazioni su come esaminare le metriche nel cloud Spring di Azure
author: jpconnock
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 12/06/2019
ms.author: jeconnoc
ms.openlocfilehash: 682cc8383db5704b28b9f4d6b8c22c4bbfc3f1ab
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/28/2019
ms.locfileid: "75528199"
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

Selezionare quindi tipo di aggregazione per ogni metrica:

![Aggregazione metrica](media/metrics/metrics-5.png)

Il tipo di aggregazione indica come aggregare l'ora. C'è un punto di metrica ogni minuto.
* Total: somma tutte le metriche come output di destinazione.
* Media: usare il valore medio nel periodo come output di destinazione.
* Max/min: usare il valore max/min nel periodo come output di destinazione.

È anche possibile modificare l'intervallo di tempo da visualizzare.  L'intervallo di tempo può essere selezionato dagli ultimi 30 minuti agli ultimi 30 giorni o da un intervallo di tempo personalizzato.

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

## <a name="user-portal-metrics-options"></a>Opzioni metriche del portale per gli utenti

La tabella seguente illustra le metriche e i dettagli disponibili.
>[!div class="mx-tdBreakAll"]
>| Nome | Nome visualizzato | Nome metrica attuatore molla | Unità | Dettagli |
>|----|----|----|----|------------|
>| SystemCpuUsagePercentage | Percentuale di utilizzo CPU di sistema | System. CPU. Usage | Percentuale | Utilizzo di CPU recente per l'intero sistema. Questo valore è un valore Double nell'intervallo [0.0, 1.0]. Il valore 0,0 indica che tutte le CPU erano inattive durante il periodo di tempo recente osservato, mentre un valore pari a 1,0 indica che tutte le CPU stavano eseguendo attivamente il 100% del tempo durante l'osservazione del periodo recente. Tutti i valori compresi tra 0,0 e 1,0 sono possibili a seconda delle attività in corso nel sistema. Se l'utilizzo della CPU recente del sistema non è disponibile, il metodo restituisce un valore negativo. |
>| AppCpuUsagePercentage | Percentuale di utilizzo della CPU dell'app | Percentuale di utilizzo della CPU dell'app | Percentuale | Utilizzo di CPU recente per il processo Java Virtual Machine. Questo valore è un valore Double nell'intervallo [0.0, 1.0]. Il valore 0,0 indica che nessuna CPU stava eseguendo thread dal processo JVM durante il periodo di tempo recente osservato, mentre il valore 1,0 indica che tutte le CPU stavano eseguendo attivamente i thread dalla JVM 100% del tempo durante il periodo recente osservato. I thread del JVM includono i thread dell'applicazione, nonché i thread interni JVM. Tutti i valori compresi tra 0,0 e 1,0 sono possibili a seconda delle attività in corso nel processo JVM e dell'intero sistema. Se il Java Virtual Machine utilizzo di CPU recente non è disponibile, il metodo restituisce un valore negativo. |
>| AppMemoryCommitted | Memoria app assegnata | JVM. memory. commit | Byte | Rappresenta la quantità di memoria (in byte) che deve essere disponibile per l'utilizzo da parte della macchina virtuale Java. La quantità di memoria vincolata può variare nel tempo (aumento o riduzione). La macchina virtuale Java può rilasciare memoria al sistema e il commit potrebbe essere inferiore a init. il commit sarà sempre maggiore o uguale a quello usato. |
>| AppMemoryUsed | Memoria app utilizzata | JVM. memory. used | Byte | Rappresenta la quantità di memoria attualmente utilizzata in byte. |
>| AppMemoryMax | Memoria app Max | JVM. memory. max | Byte | Rappresenta la quantità massima di memoria (in byte) che può essere utilizzata per la gestione della memoria. Il valore può essere indefinito. La quantità massima di memoria può variare nel tempo, se definita. La quantità di memoria utilizzata e di cui è stato eseguito il commit sarà sempre minore o uguale a max se è definito max. Un'allocazione di memoria potrebbe non riuscire se tenta di aumentare la memoria utilizzata, in modo da usare > eseguito il commit anche se utilizzata < = max sarebbe ancora true (ad esempio, quando il sistema è insufficiente nella memoria virtuale). |
>| MaxOldGenMemoryPoolBytes | Dimensioni massime dei dati di generazione obsoleti disponibili | JVM. GC. max. Data. size | Byte | Utilizzo massimo della memoria del pool di memoria di generazione precedente dall'avvio della macchina virtuale Java. |
>| OldGenMemoryPoolBytes | Dimensioni dati di generazione precedenti | JVM. GC. Live. Data. size | Byte | Dimensioni del pool di memoria di generazione precedente dopo un catalogo globale completo. |
>| OldGenPromotedBytes | Innalzamento di livello alle dimensioni dei dati di generazione precedente | JVM. GC. memory. promossi | Byte | Numero di aumenti positivi nelle dimensioni del pool di memoria precedente prima della GC dopo GC. |
>| YoungGenPromotedBytes | Innalzamento di livello a dimensioni dati di generazione giovani | JVM. GC. memory. allocato | Byte | Incrementato per un aumento delle dimensioni del pool di memoria di generazione giovani dopo un GC a prima del successivo. |
>| GCPauseTotalCount | Conteggio delle pause GC | JVM. GC. pause (Total-count) | Conteggio | Numero totale di GC dopo l'avvio di questo Giuseppe, incluso GC recente e vecchio. |
>| GCPauseTotalTime | Tempo totale pausa GC | JVM. GC. pause (tempo totale) | Millisecondi | Tempo totale GC utilizzato dopo l'avvio di questo Giuseppe, inclusa la GC precedente e quella precedente. |
>| TomcatSentBytes | Totale byte inviati Tomcat | Tomcat. Global. Sent | Byte | Quantità di dati inviati dal server Web Tomcat, in byte |
>| TomcatReceivedBytes | Totale byte ricevuti Tomcat | Tomcat. Global. received | Byte | Quantità di dati ricevuti dal server Web Tomcat, in byte |
>| TomcatRequestTotalTime | Tempo totale richiesta Tomcat | Tomcat. Global. Request (tempo totale) | Millisecondi | Tempo totale del server Web Tomcat per l'elaborazione delle richieste. |
>| TomcatRequestTotalCount | Conteggio totale richieste Tomcat | Tomcat. Global. Request (Totale-conteggio) | Conteggio | Numero totale di richieste elaborate dal server Web Tomcat |
>| TomcatRequestMaxTime | Tempo massimo richiesta Tomcat | Tomcat. Global. Request. max | Millisecondi | Tempo massimo di elaborazione di una richiesta da server Web Tomcat |
>| TomcatErrorCount | Errore globale Tomcat | Tomcat. Global. Error | Conteggio | Numero di errori che si verificano nelle richieste elaborate |
>| TomcatSessionActiveMaxCount | Numero massimo attivo sessione Tomcat | Tomcat. Sessions. Active. max | Conteggio | Numero massimo di sessioni attive allo stesso tempo |
>| TomcatSessionAliveMaxTime | Tempo massimo attivo della sessione Tomcat | Tomcat. Sessions. Alive. max | Millisecondi | Tempo più lungo (in secondi) durante il quale una sessione scaduta è stata attiva |
>| TomcatSessionCreatedCount | Conteggio creazione sessione Tomcat | Tomcat. Sessions. created | Conteggio | Numero di sessioni create |
>| TomcatSessionExpiredCount | Conteggio scaduto sessione Tomcat | Tomcat. Sessions. scaduto | Conteggio | Numero di sessioni scadute |
>| TomcatSessionRejectedCount | Conteggio rifiutato sessione Tomcat | Tomcat. Sessions. rifiutato | Conteggio | Numero di sessioni che non sono state create perché è stato raggiunto il numero massimo di sessioni attive. |

## <a name="see-also"></a>Vedi anche
* [Introduzione a Esplora metriche di Azure](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-getting-started)

* [Analizzare i log e le metriche con le impostazioni di diagnostica](https://docs.microsoft.com/azure/spring-cloud/diagnostic-services)

## <a name="next-steps"></a>Passaggi successivi
* [Esercitazione: monitorare le risorse cloud di Spring usando gli avvisi e i gruppi di azione](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-tutorial-alerts-action-groups)

* [Quote e piani di servizio per Azure Spring cloud](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-quotas)

