---
title: Informazioni sulle metriche in Azure Spring Cloud
description: Scopri come esaminare le metriche in Azure Spring Cloud
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 12/06/2019
ms.author: brendm
ms.openlocfilehash: bb23afff2b4b449897d8e420934d038938d20205
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79256757"
---
# <a name="understand-metrics-for-azure-spring-cloud"></a>Informazioni sulle metriche per Azure Spring CloudUnderstand metrics for Azure Spring Cloud

Esplora metriche di Azure è un componente del portale di Microsoft Azure che consente di tracciare grafici, correlare visivamente le tendenze e analizzare picchi e cali nelle metriche. Usare Esplora metriche per esaminare l'integrità e l'utilizzo delle risorse. 

In Azure Spring Cloud sono disponibili due punti di visualizzazione per le metriche.
* Grafici in ogni pagina di panoramica dell'applicazione
* Pagina Metriche comuni

 ![Grafici delle metriche](media/metrics/metrics-1.png)

I grafici nella **panoramica** dell'applicazione forniscono controlli rapidi dello stato per ogni applicazione. La pagina **Metriche** comune contiene tutte le metriche disponibili come riferimento. Puoi creare i tuoi grafici nella pagina delle metriche comuni e aggiungerli in Dashboard.

## <a name="application-overview-page"></a>Pagina di panoramica dell'applicazione
Selezionare un'app in **Gestione app** per trovare i grafici nella pagina di panoramica.  

 ![Gestione delle metriche delle applicazioni](media/metrics/metrics-2.png)

Ogni pagina **Panoramica sull'applicazione** presenta un grafico di metriche che consente di eseguire una rapida verifica dello stato dell'applicazione.  

 ![Panoramica delle metriche dell'applicazioneApplication Metrics Overview](media/metrics/metrics-3.png)

Azure Spring Cloud offre questi cinque grafici con metriche che vengono aggiornate ogni minuto:Azure Spring Cloud provides these five charts with metrics that are updated every minute:

* **Errori del server HTTP:** conteggio degli errori per le richieste HTTP all'app
* **Dati in:** byte ricevuti dall'app
* **Uscita dati**: Byte inviati dall'app
* **Richieste:** richieste ricevute dall'app
* **Tempo di risposta medio:** tempo di risposta medio dall'app

Per il grafico, è possibile selezionare un intervallo di tempo da un'ora a sette giorni.

## <a name="common-metrics-page"></a>Pagina Metriche comuni

Le **metriche** nel riquadro di spostamento a sinistra sono collegate alla pagina Metriche comuni.

Selezionare innanzitutto le metriche da visualizzare:First, select metrics to view:

![Seleziona vista metrica](media/metrics/metrics-4.png)

I dettagli di tutte le metriche sono disponibili nella [sezione](#user-metrics-options) seguente.

Selezionare quindi il tipo di aggregazione per ogni metrica:Next, select aggregation type for each metric:

![Aggregazione metrica](media/metrics/metrics-5.png)

Il tipo di aggregazione indica come aggregare i punti metrici nel grafico in base al tempo. C'è un punto metrico non elaborato ogni minuto e il tipo di pre-aggregazione entro pochi minuti è predefinito per tipo di metrica.
* Somma: somma tutti i valori come output di destinazione.
* Media: utilizzare il valore Medio nel periodo come output di destinazione.
* Max/Min: usare il valore Max/Min nel periodo come output di destinazione.

L'intervallo di tempo da visualizzare può anche essere modificato. L'intervallo di tempo può essere scelto dagli ultimi 30 minuti agli ultimi 30 giorni o da un intervallo di tempo personalizzato.

![Modifica metrica](media/metrics/metrics-6.png)

La visualizzazione predefinita include tutte le metriche dell'applicazione di un servizio Cloud di Azure Spring insieme. Le metriche di un'app o di un'istanza possono essere filtrate nella visualizzazione.  Fare clic su **Aggiungi filtro**, impostare la proprietà **su App**e selezionare l'applicazione di destinazione che si desidera monitorare nella casella di testo **Valori.** 

È possibile utilizzare due tipi di filtri (proprietà):
* App: filtra in base al nome dell'app
* Istanza: filtro in base all'istanza dell'appInstance: filter by app instance

![Filtri di metrica](media/metrics/metrics-7.png)

Puoi anche usare l'opzione **Applica divisione,** che disegnerà più linee per un'app:

![Divisione metrica](media/metrics/metrics-8.png)

>[!TIP]
> È possibile creare grafici personalizzati nella pagina di metriche e aggiungerli al **dashboard**. Per iniziare, assegnare un nome al grafico.  Selezionare quindi **Aggiungi al dashboard** nell'angolo in alto a destra. È ora possibile controllare l'applicazione nel **dashboard** del portale.

## <a name="user-metrics-options"></a>Opzioni per le metriche utente

Le tabelle seguenti mostrano le metriche e i dettagli disponibili.

### <a name="error"></a>Errore
>[!div class="mx-tdCol2BreakAll"]
>| Nome | Nome metrico attuatore a molla | Unità | Dettagli |
>|----|----|----|------------|
>| Errore globale Tomcat | tomcat.global.error | Conteggio | Numero di errori delle richieste elaborate |

### <a name="performance"></a>Prestazioni
>[!div class="mx-tdCol2BreakAll"]
>| Nome | Nome metrico attuatore a molla | Unità | Dettagli |
>|----|----|----|------------|
>|Percentuale di utilizzo della CPU di sistema | system.cpu.usage | Percentuale | Utilizzo recente della CPU per l'intero sistema. Questo valore è un valore double nell'intervallo [0.0,1.0]. Un valore pari a 0,0 indica che tutte le CPU erano inattive durante il recente periodo di tempo osservato, mentre un valore pari a 1,0 indica che tutte le CPU erano attive nel 100% del tempo durante il recente periodo osservato.|
>| Percentuale di utilizzo della CPU dell'app | Percentuale di utilizzo della CPU dell'app | Percentuale | Utilizzo recente della CPU per il processo di Java Virtual Machine. Questo valore è un valore double nell'intervallo [0.0,1.0]. Un valore pari a 0,0 indica che nessuna delle CPU era in esecuzione thread dal processo JVM durante il recente periodo di tempo osservato, mentre un valore pari a 1,0 indica che tutte le CPU stavano eseguendo attivamente i thread dalla JVM 100% del tempo durante il periodo recente osservato. I thread della JVM includono i thread dell'applicazione e i thread interni JVM.|
>| Memoria app assegnata | jvm.memory.committed | Byte | Rappresenta la quantità di memoria che è garantita per essere disponibile per l'utilizzo da parte della JVM. La JVM può rilasciare memoria al sistema e il commit potrebbe essere inferiore a init. sempre impegnato sarà maggiore o uguale a usato. |
>| Memoria dell'app utilizzata | jvm.memory.used | Byte | Rappresenta la quantità di memoria attualmente utilizzata in byte. |
>| Memoria app max | jvm.memory.max | Byte | Rappresenta la quantità massima di memoria che può essere utilizzata per la gestione della memoria. La quantità di memoria utilizzata e di cui è stato eseguito il commit sarà sempre minore o uguale a max se max è definito. Un'allocazione di memoria potrebbe non riuscire se tenta di aumentare la memoria utilizzata in modo tale che utilizzava > commit anche se utilizzato <s max sarebbe ancora true (ad esempio, quando il sistema è insufficiente nella memoria virtuale). |
>| Dimensioni massime dei dati di vecchia generazione disponibiliMax Available Old Generation Data Size | jvm.gc.max.data.size | Byte | Utilizzo massimo della memoria del pool di memoria di generazione precedente dall'avvio della macchina virtuale Java. |
>| Dimensioni dei dati di generazione precedente | jvm.gc.live.data.size | Byte | Dimensioni del pool di memoria di vecchia generazione dopo un GC completo. |
>| Promuovi a dimensioni dei dati di vecchia generazione | jvm.gc.memory.promoted | Byte | Numero di aumenti positivi delle dimensioni del pool di memoria di vecchia generazione prima di GC a dopo GC. |
>| Promuovere a giovani dimensioni dei dati | jvm.gc.memory.allocated | Byte | Incrementato per un aumento delle dimensioni del pool di memoria di generazione giovane dopo un GC a prima del successivo. |
>| Conteggio pause GC | jvm.gc.pause (conteggio totale) | Conteggio | Il numero totale di GC dopo l'inizio di questa JMV, tra cui Young e Old GC. |
>| Tempo totale pausa GC | jvm.gc.pause (tempo totale) | Millisecondi | Tempo totale GC consumato dopo l'inizio di questo JMV, tra cui Young e Old GC. |

### <a name="request"></a>Richiesta
>[!div class="mx-tdCol2BreakAll"]
>| Nome | Nome metrico attuatore a molla | Unità | Dettagli |
>|----|----|----|------------|
>| Totale byte inviati Tomcat | tomcat.global.sent | Byte | Quantità di dati inviati dal server Web Tomcat |
>| Byte totali ricevuti Tomcat | tomcat.global.received | Byte | Quantità di dati ricevuti dal server Web Tomcat |
>| Tomcat Richiesta Tempo totale | tomcat.global.request (tempo totale) | Millisecondi | Tempo totale del server Web Tomcat per l'elaborazione delle richieste |
>| Conteggio totale richieste Tomcat | tomcat.global.request (conteggio totale) | Conteggio | Numero totale di richieste elaborate dal server Web Tomcat |
>| Ora massima richiesta Tomcat | tomcat.global.request.max | Millisecondi | Tempo massimo di elaborazione di una richiesta da parte del server Web Tomcat |

### <a name="session"></a>sessione
>[!div class="mx-tdCol2BreakAll"]
>| Nome | Nome metrico attuatore a molla | Unità | Dettagli |
>|----|----|----|------------|
>| Conteggio attivo massimo sessione Tomcat | tomcat.sessions.active.max | Conteggio | Numero massimo di sessioni attive contemporaneamente |
>| Tempo di vita massimo sessione Tomcat | tomcat.sessions.alive.max | Millisecondi | Tempo più lungo (in secondi) in cui era stata riattiva una sessione scaduta |
>| Conteggio sessioni Tomcat create | tomcat.sessions .created | Conteggio | Numero di sessioni create |
>| Conteggio sessione tomcat scaduto | tomcat.sessions.expired | Conteggio | Numero di sessioni scadute |
>| Conteggio sessioni Tomcat rifiutate | tomcat.sessions.rejected | Conteggio | Numero di sessioni che non sono state create perché è stato raggiunto il numero massimo di sessioni attive. |

## <a name="see-also"></a>Vedere anche
* [Introduzione a Esplora metriche di Azure](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-getting-started)

* [Analizzare i log e le metriche con le impostazioni di diagnosticaAnalyze logs and metrics with diagnostics settings](https://docs.microsoft.com/azure/spring-cloud/diagnostic-services)

## <a name="next-steps"></a>Passaggi successivi
* [Esercitazione: Monitorare le risorse di Spring Cloud usando avvisi e gruppi di azioniTutorial: Monitor Spring Cloud resources using alerts and action groups](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-tutorial-alerts-action-groups)

* [Quote e piani di servizio per Azure Spring Cloud](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-quotas)

