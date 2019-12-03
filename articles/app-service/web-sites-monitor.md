---
title: Monitorare le app
description: Informazioni su come monitorare le app di Servizio app di Azure tramite il portale di Azure. Comprendere le quote e le metriche segnalate.
author: btardif
ms.assetid: d273da4e-07de-48e0-b99d-4020d84a425e
ms.topic: article
ms.date: 01/11/2019
ms.author: byvinyal
ms.custom: seodec18
ms.openlocfilehash: 4c0d3822b5000611d1b5229924cb44d055795468
ms.sourcegitcommit: 48b7a50fc2d19c7382916cb2f591507b1c784ee5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/02/2019
ms.locfileid: "74688268"
---
# <a name="monitor-apps-in-azure-app-service"></a>Monitorare le app in Servizio app di Azure
[Servizio app di Azure](https://go.microsoft.com/fwlink/?LinkId=529714) offre funzionalità di monitoraggio predefinite per app Web, sistemi back-end mobili e app per le API nel [portale di Azure](https://portal.azure.com).

Nella portale di Azure è possibile esaminare le *quote* e le *metriche* per un piano di servizio app e app e configurare gli *avvisi* e la *scalabilità automatica* basati su metriche.

## <a name="understand-quotas"></a>Informazioni sulle quote

Le app ospitate nel servizio app sono soggette a determinati limiti in relazione alle risorse che possono usare. Tali limiti sono definiti nel piano di servizio app associato all'app.

[!INCLUDE [app-service-dev-test-note](../../includes/app-service-dev-test-note.md)]

Se l'app è ospitata in un piano *Gratuito* o *Condiviso*, i limiti relativi alle risorse che l'app può usare sono definiti dalle quote.

Se l'app è ospitata in un piano *Basic*, *Standard* o *Premium*, i limiti relativi alle risorse che può usare vengono stabiliti in base alle *dimensioni* (Small, Medium, Large) e al *numero di istanze* (1, 2, 3 e così via) del piano di servizio app.

Le quote per le app ospitate nel piano Gratuito o Condiviso sono:

| Quota | Description |
| --- | --- |
| **Tempo CPU (breve)** | Quantità di CPU consentita per l'app in un intervallo di 5 minuti. Questa quota viene reimpostata automaticamente ogni cinque minuti. |
| **Tempo CPU (giorno)** | Quantità totale di CPU consentita per l'app in un giorno. Questa quota viene reimpostata automaticamente ogni 24 ore a mezzanotte (ora UTC). |
| **Memoria** | Quantità totale di memoria consentita per l'app. |
| **Larghezza di banda** | Quantità totale di larghezza di banda in uscita consentita per l'app in un giorno. Questa quota viene reimpostata automaticamente ogni 24 ore a mezzanotte (ora UTC). |
| **File system** | Quantità totale di spazio di archiviazione consentito. |

L'unica quota applicabile alle app ospitate in *Basic*, *standard*e *Premium* è file System.

Per altre informazioni su quote, funzionalità e limiti specifici per i vari SKU del servizio app, vedere i [limiti del servizio per la sottoscrizione di Azure](../azure-subscription-service-limits.md#app-service-limits).

### <a name="quota-enforcement"></a>Applicazione delle quote

Se l'uso di un'app supera la quota *Tempo CPU (breve)* , *Tempo CPU (giorno)* o *Larghezza di banda*, l'app viene arrestata fino al momento in cui viene reimpostata la quota. Durante questo intervallo di tempo, per tutte le richieste in ingresso viene restituito un errore HTTP 403.

![Messaggio di errore 403][http403]

Se viene superata la quota Memoria dell'app, quest'ultima viene riavviata.

Se viene superata la quota Filesystem, tutte le operazioni di scrittura hanno esito negativo. Gli eventuali errori delle operazioni di scrittura includono qualsiasi operazione di scrittura nei log.

È possibile aumentare o rimuovere le quote dall'app aggiornando il piano di servizio app.

## <a name="understand-metrics"></a>Informazioni sulle metriche

> [!NOTE]
> L' **utilizzo del file System** è una nuova metrica distribuita a livello globale, non è previsto alcun dato, a meno che non sia stata inserita nell'elenco elementi consentiti per l'anteprima privata.
> 

Le metriche forniscono informazioni sull'app o sul comportamento del piano di servizio app.

Le metriche disponibili per un'app sono:

| Metrica | Description |
| --- | --- |
| **Tempo medio di risposta** | Tempo medio impiegato dall'app per gestire le richieste, in secondi. |
| **Working set della memoria medio** | Quantità media di memoria usata dall'app, espressa in megabyte (MiB). |
| **Connessioni** | Numero di socket associati esistenti nella sandbox (w3wp.exe e i processi figlio).  Un socket associato viene creato chiamando le API bind()/connect() e permane finché non viene chiuso con CloseHandle()/closesocket(). |
| **Tempo CPU** | Quantità di CPU utilizzata dall'app, espressa in secondi. Per altre informazioni su questa metrica, vedere [Tempo CPU e percentuale CPU](#cpu-time-vs-cpu-percentage). |
| **Assembly attuali** | Numero corrente di assembly caricati in tutti i domini dell'applicazione di questa applicazione. |
| **Dati in entrata** | Larghezza di banda in entrata utilizzata dall'app, espressa in MiB. |
| **Dati in uscita** | Larghezza di banda in uscita utilizzata dall'app, espressa in MiB. |
| **Utilizzo del file System** | Percentuale di quota del file System utilizzata dall'app. |
| **Garbage Collection di generazione 0** | Numero di operazioni di Garbage Collection sugli oggetti di generazione 0 dall'avvio del processo dell'app. Le operazioni di GC di generazione superiore includono tutte quelle di generazione inferiore.|
| **Garbage Collection di generazione 1** | Numero di operazioni di Garbage Collection sugli oggetti di generazione 1 dall'avvio del processo dell'app. Le operazioni di GC di generazione superiore includono tutte quelle di generazione inferiore.|
| **Garbage Collection di generazione 2** | Numero di operazioni di Garbage Collection sugli oggetti di generazione 2 dall'avvio del processo dell'app.|
| **Numero di handle** | Numero totale di handle attualmente aperti dal processo dell'app.|
| **Http 2xx** | Numero di richieste che hanno restituito un codice di stato HTTP ≥ 200 e < 300. |
| **Http 3xx** | Numero di richieste che hanno restituito un codice di stato HTTP ≥ 300 e < 400. |
| **Http 401** | Numero di richieste che hanno restituito un codice di stato HTTP 401. |
| **Http 403** | Numero di richieste che hanno restituito un codice di stato HTTP 403. |
| **Http 404** | Numero di richieste che hanno restituito un codice di stato HTTP 404. |
| **Http 406** | Numero di richieste che hanno restituito un codice di stato HTTP 406. |
| **Http 4xx** | Numero di richieste che hanno restituito un codice di stato HTTP ≥ 400 e < 500. |
| **Errori server HTTP** | Numero di richieste che hanno restituito un codice di stato HTTP ≥ 500 e < 600. |
| **I/O - Altri byte al secondo** | Frequenza con cui il processo dell'app rilascia byte alle operazioni di I/O che non coinvolgono i dati, ad esempio le operazioni di controllo.|
| **I/O - Altre operazioni al secondo** | Velocità con cui il processo dell'app emette operazioni di I/O che non sono operazioni di lettura o scrittura.|
| **I/O - Byte in lettura al secondo** | Frequenza con cui il processo dell'app legge i byte dalle operazioni di I/O.|
| **I/O - Operazioni di lettura al secondo** | Frequenza con cui il processo dell'app rilascia le operazioni di I/O di lettura.|
| **I/O - Byte in scrittura al secondo** | Frequenza con cui il processo dell'app scrive i byte nelle operazioni di I/O.|
| **I/O - Operazioni di scrittura al secondo** | Frequenza con cui il processo dell'app rilascia le operazioni di I/O di scrittura.|
| **Working set della memoria** | Quantità di memoria corrente usata dall'app, espressa in MiB. |
| **Byte privati** | I byte privati sono la dimensione corrente, in byte, della memoria allocata dal processo dell'app, che non può essere condivisa con altri processi.|
| **Richieste** | Numero totale di richieste, indipendentemente dal codice di stato HTTP restituito. |
| **Richieste nella coda dell'applicazione** | Numero di richieste nella coda di richieste dell'applicazione.|
| **Conteggio thread** | Numero di thread attualmente attivi nel processo dell'app.|
| **Totale di domini app** | Numero corrente di domini dell'applicazione caricati in questa applicazione.|
| **Totale di domini app scaricati** | Numero totale di domini dell'applicazione scaricati dall'avvio dell'applicazione.|


Le metriche disponibili per un piano di servizio app sono:

> [!NOTE]
> Le metriche del piano di servizio app sono disponibili solo per i piani dei livelli *Basic*, *Standard* e *Premium*.
> 

| Metrica | Description |
| --- | --- |
| **Percentuale di CPU** | CPU media usata tra tutte le istanze del piano. |
| **Percentuale memoria** | Memoria media usata tra tutte le istanze del piano. |
| **Dati in entrata** | Larghezza di banda in ingresso media usata tra tutte le istanze del piano. |
| **Dati in uscita** | Larghezza di banda in uscita media usata tra tutte le istanze del piano. |
| **Lunghezza coda disco** | Numero medio di richieste di lettura e scrittura accodate nella risorsa di archiviazione. Una lunghezza coda disco elevata può indicare il rallentamento di un'app a causa di un eccessivo numero di operazioni di I/O su disco. |
| **Lunghezza coda HTTP** | Numero medio di richieste HTTP che hanno dovuto attendere in coda prima di essere completate. Una lunghezza coda HTTP elevata o in aumento indica che il piano si trova in condizioni di carico eccessivo. |

### <a name="cpu-time-vs-cpu-percentage"></a>Tempo CPU e percentuale CPU
<!-- To do: Fix Anchor (#CPU-time-vs.-CPU-percentage) -->

Le metriche che riflettono l'utilizzo della CPU sono due:

**Tempo CPU**: utile per le app ospitate in piani gratuiti o condivisi, perché una delle rispettive quote è definita in minuti CPU usati dall'app.

**Percentuale CPU**: utile per le app ospitate nei piani Basic, standard e Premium, perché possono essere scalate orizzontalmente. La percentuale di CPU è una indicazione corretta dell'utilizzo complessivo in tutte le istanze.

## <a name="metrics-granularity-and-retention-policy"></a>Granularità delle metriche e criteri di conservazione
Le metriche per un'app e un piano di servizio app vengono registrate e aggregate dal servizio con le granularità e i criteri di conservazione seguenti:

* Le metriche di granularità basate su **Minuto** vengono mantenute per 30 ore.
* Le metriche di granularità basate su **Ora** vengono mantenute per 30 giorni.
* Le metriche di granularità basate su **Giorno** vengono mantenute per 30 giorni.

## <a name="monitoring-quotas-and-metrics-in-the-azure-portal"></a>Monitoraggio di quote e metriche nel portale di Azure
Per esaminare lo stato delle varie quote e metriche che interessano un'app, accedere al [portale di Azure](https://portal.azure.com).

![Grafico delle quote nel portale di Azure][quotas]

Per trovare le quote, selezionare **Impostazioni** > **Quote**. Nel grafico è possibile esaminare gli elementi seguenti: 
1. Nome della quota
1. Intervallo di reimpostazione
1. Limite corrente
1. Valore corrente

![Grafico delle metriche nel portale di Azure][metrics] È possibile accedere alle metriche direttamente dalla pagina delle **risorse**. Per personalizzare il grafico: 
1. Selezionare il grafico.
1. Selezionare **Modifica grafico**.
1. Modificare il valore di **Intervallo di tempo**.
1. Modificare **Tipo di grafico**.
1. Modificare le metriche da visualizzare.  

Per altre informazioni sulle metriche, vedere [Monitorare le metriche del servizio](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md).

## <a name="alerts-and-autoscale"></a>Avvisi e scalabilità automatica
Le metriche per un'app o un piano di servizio app possono essere collegate ad avvisi specifici. Per altre informazioni, vedere [Ricevere notifiche di avviso](../monitoring-and-diagnostics/insights-alerts-portal.md).

Le app del servizio app ospitate nei piani di servizio app Basic, Standard e Premium supportano la scalabilità automatica. Con questa funzionalità è possibile configurare le regole che consentono di monitorare le metriche del piano di servizio app. Le regole possono aumentare o diminuire il numero di istanze per fornire risorse aggiuntive in base alla esigenze. L'uso delle regole consente anche di ridurre i costi quando l'app ha un provisioning eccessivo.

Per altre informazioni sulla scalabilità automatica, vedere [Procedura dettagliata sulla scalabilità automatica](../monitoring-and-diagnostics/insights-how-to-scale.md) e [Procedure consigliate per la scalabilità automatica in Monitoraggio di Azure](../azure-monitor/platform/autoscale-best-practices.md).

[fzilla]:https://go.microsoft.com/fwlink/?LinkId=247914
[vmsizes]:https://go.microsoft.com/fwlink/?LinkID=309169

<!-- Images. -->
[http403]: ./media/web-sites-monitor/http403.png
[quotas]: ./media/web-sites-monitor/quotas.png
[metrics]: ./media/web-sites-monitor/metrics.png