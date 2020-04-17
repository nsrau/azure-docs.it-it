---
title: Azure Monitor metrics for Application Gateway
description: Informazioni su come usare le metriche per monitorare le prestazioni del gateway applicazione
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 2/5/2019
ms.author: absha
ms.openlocfilehash: ebbdda39f019f374f8e5abe951d0180c0dd453f6
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81457876"
---
# <a name="metrics-for-application-gateway"></a>Metriche per il gateway applicazione

Il gateway applicazione pubblica i punti dati, denominati metriche, in [Monitoraggio di Azure](https://docs.microsoft.com/azure/azure-monitor/overview) per le prestazioni del gateway applicazione e delle istanze back-end. Queste metriche sono valori numerici in un set ordinato di dati di serie temporali che descrivono alcuni aspetti del gateway applicazione in un determinato momento. Se sono presenti richieste che passano attraverso il gateway applicazione, misura e invia le metriche in intervalli di 60 secondi. Se non sono presenti richieste che passano attraverso il gateway applicazione o non sono dati per una metrica, la metrica non viene segnalata. Per altre informazioni, vedere Metriche di Monitoraggio di Azure.For more information, see [Azure Monitor metrics.](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-metrics)

## <a name="metrics-supported-by-application-gateway-v2-sku"></a>Metriche supportate dallo SKU del gateway applicazione V2

### <a name="timing-metrics"></a>Metriche di temporizzazione

Il gateway applicazione fornisce diverse metriche di temporizzazione incorporate relative alla richiesta e alla risposta, tutte misurate in millisecondi. 

![](./media/application-gateway-metrics/application-gateway-metrics.png)

> [!NOTE]
>
> Se nel gateway applicazione sono presenti più listener, filtrare sempre in base alla dimensione *Listener* confrontando metriche di latenza diverse per ottenere un'inferenza significativa.

- **Tempo di connessione back-end**

  Tempo impiegato per stabilire una connessione con l'applicazione back-end. 

  Ciò include la latenza di rete e il tempo impiegato dallo stack TCP del server back-end per stabilire nuove connessioni. Nel caso di TLS, include anche il tempo trascorso per l'handshake. 

- **Tempo di risposta del primo byte back-end**

  Intervallo di tempo tra l'inizio della connessione al server back-end e la ricezione del primo byte dell'intestazione della risposta. 

  Ciò approssima la somma del tempo di *connessione back-end,* il tempo impiegato dalla richiesta di raggiungere il back-end dal gateway applicazione, il tempo impiegato dall'applicazione back-end per rispondere (il tempo impiegato dal server per generare contenuto, potenzialmente il recupero di query di database) e il tempo impiegato dal primo byte della risposta per raggiungere il gateway applicazione dal back-end.

- **Tempo di risposta dell'ultimo byte back-end**

  Intervallo di tempo tra l'inizio della connessione al server back-end e la ricezione dell'ultimo byte del corpo della risposta. 

  Questo approssima la somma del tempo di *risposta del primo byte di back-end* e del tempo di trasferimento dei dati (questo numero può variare notevolmente a seconda delle dimensioni degli oggetti richiesti e della latenza della rete del server).

- **Tempo totale del gateway applicazione**

  Tempo medio necessario per ricevere, elaborare una richiesta e inviare la relativa risposta. 

  Questo è l'intervallo dal momento in cui il gateway applicazione riceve il primo byte della richiesta HTTP al momento in cui l'ultimo byte di risposta è stato inviato al client. Sono inclusi il tempo di elaborazione impiegato dal gateway applicazione, il tempo di *risposta dell'ultimo byte back-end,* il tempo impiegato dal gateway applicazione per inviare tutta la risposta e l'RTT *client.*

- **Client RTT**

  Tempo di andata e ritorno medio tra i client e il gateway applicazione.



Queste metriche possono essere utilizzate per determinare se il rallentamento osservato è dovuto alla rete client, alle prestazioni del gateway applicazione, alla saturazione dello stack TCP della rete back-end e del server back-end, alle prestazioni dell'applicazione back-end o alle dimensioni di file di grandi dimensioni.

Ad esempio, se si verifica un picco nella tendenza del tempo di risposta del *primo byte back-end* ma l'andamento del tempo di *connessione back-end* è stabile, è possibile dedurre che il gateway applicazione per la latenza di back-end e il tempo impiegato per stabilire la connessione è stabile e il picco è causato da un aumento del tempo di risposta dell'applicazione back-end. D'altra parte, se il picco nel tempo di risposta del *primo byte back-end* è associato a un picco corrispondente nel tempo di *connessione back-end,* si può dedurre che la rete tra il gateway applicazione e il server back-end o lo stack TCP del server back-end è saturo. 

Se si nota un picco nel tempo di *risposta dell'ultimo byte di back-end* ma il tempo di *risposta del primo byte di back-end* è stabile, è possibile dedurre che il picco è dovuto alla richiesta di un file più grande.

Analogamente, se il *tempo totale* del gateway applicazione ha un picco ma il tempo di risposta *dell'ultimo byte back-end* è stabile, può essere un segno di collo di bottiglia delle prestazioni nel gateway applicazione o un collo di bottiglia nella rete tra client e gateway applicazione. Inoltre, se il *RTT client* ha anche un picco corrispondente, quindi indica che la degradazione è a causa della rete tra client e gateway applicazione.

### <a name="application-gateway-metrics"></a>Metriche del gateway applicazione

Per il gateway applicazione sono disponibili le metriche seguenti:

- **Byte ricevuti**

   Numero di byte ricevuti dal gateway applicazione dai client

- **Byte inviati**

   Numero di byte inviati dal gateway applicazione ai client

- **Protocollo TLS client**

   Numero di richieste TLS e non TLS avviate dal client che ha stabilito la connessione con il gateway applicazione. Per visualizzare la distribuzione del protocollo TLS, filtrare in base alla dimensione Protocollo TLS.

- **Unità di capacità correnti**

   Numero di unità di capacità utilizzate per bilanciare il carico del traffico. Esistono tre determinanti per unità di capacità: unità di calcolo, connessioni persistenti e velocità effettiva. Ogni unità di capacità è composta al massimo da: 1 unità di calcolo, o 2500 connessioni persistenti o velocità effettiva 2,22 Mbps.

- **Unità di calcolo correnti**

   Numero di capacità del processore consumate. I fattori che influiscono sull'unità di calcolo sono le connessioni TLS al secondo, i calcoli di riscrittura URL e l'elaborazione delle regole WAF. 

- **Connessioni correnti**

   Il numero totale di connessioni simultanee attive dai client al gateway applicazione
   
- **Unità stimate di capacità fatturate**

  Con lo SKU v2, il modello di determinazione dei prezzi è guidato dal consumo. Le unità di capacità misurano il costo basato sul consumo addebitato in aggiunta al costo fisso. *Unità capacità fatturate stimate* indica il numero di unità di capacità che utilizzano le quali viene stimata la fatturazione. Viene calcolato come valore maggiore tra le unità di *capacità corrente* (unità di capacità necessarie per bilanciare il carico del traffico) e le unità di capacità *fatturabili* fisse (unità di capacità minima mantenute sottoposte a provisioning).

- **Richieste non riuscite**

  Numero di richieste servite da Gateway applicazione con codici di errore del server 5xx. Sono inclusi i codici 5xx generati dal gateway applicazione e i codici 5xx generati dal back-end. Il conteggio delle richieste può essere ulteriormente filtrato per mostrare il conteggio per ogni combinazione di impostazioni del pool back-end specifico-http.
   
- **Unità di capacità fatturabili fisse**

  Il numero minimo di unità di capacità mantenute sottoposte a provisioning in base all'impostazione Unità di *scala minima* (un'istanza viene convertita in 10 unità di capacità) nella configurazione del gateway applicazione.
   
 - **Nuove connessioni al secondo**

   Numero medio di nuove connessioni TCP al secondo stabilito dai client al gateway applicazione e dai membri del gateway applicazione ai membri back-end.


- **Stato della risposta**

   Stato della risposta HTTP restituito dal gateway applicazione. La distribuzione del codice di stato della risposta può essere ulteriormente classificata per visualizzare le risposte nelle categorie 2xx, 3xx, 4xx e 5xx.

- **Velocità effettiva**

   Numero di byte al secondo distribuiti dal gateway applicazione

- **Totale richieste**

   Numero di richieste completate servite dal gateway applicazione. Il conteggio delle richieste può essere ulteriormente filtrato per mostrare il conteggio per ogni combinazione di impostazioni del pool back-end specifico-http.

### <a name="backend-metrics"></a>Metriche back-end

Per il gateway applicazione sono disponibili le metriche seguenti:

- **Stato della risposta back-end**

  Numero di codici di stato della risposta HTTP restituiti dai back-end. Non sono inclusi i codici di risposta generati dal gateway applicazione. La distribuzione del codice di stato della risposta può essere ulteriormente classificata per visualizzare le risposte nelle categorie 2xx, 3xx, 4xx e 5xx.

- **Numero di host integri**

  Numero di back-end determinati in modo integro dal probe di integrità. È possibile filtrare in base al pool back-end per visualizzare il numero di host integri in un pool back-end specifico.

- **Numero di host non integri**

  Numero di back-end determinati come non integri dal probe di integrità. È possibile filtrare in base al pool back-end per visualizzare il numero di host non integri in un pool back-end specifico.
  
- **Richieste al minuto per host integroRequests per Minute Per Healthy Host**

  Numero medio di richieste ricevute da ogni membro integro in un pool back-end in un minuto. È necessario specificare il pool back-end utilizzando la dimensione *HttpSettings BackendPool.*  
  

## <a name="metrics-supported-by-application-gateway-v1-sku"></a>Metriche supportate dallo SKU del gateway applicazione V1

### <a name="application-gateway-metrics"></a>Metriche del gateway applicazione

Per il gateway applicazione sono disponibili le metriche seguenti:

- **Cpu**

  Visualizza l'utilizzo delle CPU allocate al gateway applicazione.  In condizioni normali, l'utilizzo della CPU non deve superare regolarmente il 90%, in quanto ciò potrebbe causare latenza nei siti Web ospitati dietro il gateway applicazione e interrompere l'esperienza client. È possibile controllare o migliorare indirettamente l'utilizzo della CPU modificando la configurazione del gateway applicazione aumentando il numero di istanze o passando a una dimensione SKU maggiore o eseguendo entrambe le operazioni.

- **Connessioni correnti**

  Numero di connessioni correnti stabilite con il gateway applicazione

- **Richieste non riuscite**

  Numero di richieste servite da Gateway applicazione con codici di errore del server 5xx. Sono inclusi i codici 5xx generati dal gateway applicazione e i codici 5xx generati dal back-end. Il conteggio delle richieste può essere ulteriormente filtrato per mostrare il conteggio per ogni combinazione di impostazioni del pool back-end specifico-http.

- **Stato della risposta**

  Stato della risposta HTTP restituito dal gateway applicazione. La distribuzione del codice di stato della risposta può essere ulteriormente classificata per visualizzare le risposte nelle categorie 2xx, 3xx, 4xx e 5xx.

- **Velocità effettiva**

  Numero di byte al secondo distribuiti dal gateway applicazione

- **Totale richieste**

  Numero di richieste completate servite dal gateway applicazione. Il conteggio delle richieste può essere ulteriormente filtrato per mostrare il conteggio per ogni combinazione di impostazioni del pool back-end specifico-http.

- **Conteggio richieste bloccate del firewall dell'applicazione Web**
- **Distribuzione delle richieste bloccate del firewall dell'applicazione Web**
- **Distribuzione totale delle regole del firewall dell'applicazione Web**

### <a name="backend-metrics"></a>Metriche back-end

Per il gateway applicazione sono disponibili le metriche seguenti:

- **Numero di host integri**

  Numero di back-end determinati in modo integro dal probe di integrità. È possibile filtrare in base al pool back-end per visualizzare il numero di host integri in un pool back-end specifico.

- **Numero di host non integri**

  Numero di back-end determinati come non integri dal probe di integrità. È possibile filtrare in base al pool back-end per visualizzare il numero di host non integri in un pool back-end specifico.

## <a name="metrics-visualization"></a>Visualizzazione delle metriche

Passare a un gateway applicazione, in **Monitoraggio** selezionare **Metriche**. Per visualizzare i valori disponibili, selezionare l'elenco a discesa **METRICA**.

Nella figura seguente è illustrato un esempio con tre metriche visualizzate per gli ultimi 30 minuti:

[![](media/application-gateway-diagnostics/figure5.png "Metric view")](media/application-gateway-diagnostics/figure5-lb.png#lightbox)

Per un elenco delle metriche correnti, vedere [Metriche supportate con il monitoraggio di Azure](../azure-monitor/platform/metrics-supported.md).

### <a name="alert-rules-on-metrics"></a>Regole di avviso sulle metriche

È possibile avviare le regole di avviso in base alle metriche per una risorsa. Ad esempio, un avviso può chiamare un webhook o inviare un messaggio di posta elettronica a un amministratore se la velocità effettiva del gateway applicazione è al di sopra, al di sotto o corrisponde alla soglia per un periodo di tempo specificato.

L'esempio seguente illustra la creazione di una regola di avviso per l'invio di un messaggio di posta elettronica a un amministratore al superamento della soglia della velocità effettiva:

1. Selezionare **Aggiungi avviso metrica** per aprire la pagina **Aggiungi regola.** Puoi anche raggiungere questa pagina dalla pagina delle metriche.

   ![Pulsante "Aggiungi avviso per la metrica"][6]

2. Nella pagina **Aggiungi regola** compilare il nome, la condizione e le sezioni di notifica e selezionare **OK**.

   * Nel selettore **Condizione** selezionare uno dei quattro valori seguenti: **Maggiore di**, **Maggiore di o uguale a**, **Minore di** o **Minore o uguale a**.

   * Nel selettore **Periodo** selezionare un periodo compreso tra cinque minuti e sei ore.

   * Se si seleziona **Invia messaggio di posta elettronica a proprietari, collaboratori e lettori**, il messaggio di posta elettronica può essere dinamico basato sugli utenti che hanno accesso alla risorsa. In alternativa, è possibile inserire un elenco di utenti separato da virgole nella casella **Indirizzi di posta elettronica aggiuntivi dell'amministratore**.

   ![Pagina Aggiungi regola][7]

Se la soglia viene superata, l'utente riceve un messaggio di posta elettronica simile al seguente:

![Messaggio di posta elettronica per il superamento della soglia][8]

Dopo aver creato un avviso per la metrica, viene visualizzato un elenco di avvisi. L'elenco offre una panoramica di tutte le regole di avviso.

![Elenco di avvisi e regole][9]

Per altre informazioni sulle notifiche di avviso, vedere [Ricevere notifiche di avviso](../monitoring-and-diagnostics/insights-receive-alert-notifications.md).

Per altre informazioni sui webhook e su come usarli con gli avvisi, vedere [Configurare un webhook in un avviso relativo alle metriche di Azure](../azure-monitor/platform/alerts-webhooks.md).

## <a name="next-steps"></a>Passaggi successivi

* Visualizzare i log contatori ed eventi usando i [log di Monitoraggio di Azure](../azure-monitor/insights/azure-networking-analytics.md).
* Post di blog [Visualize your Azure activity log with Power BI](https://blogs.msdn.com/b/powerbi/archive/2015/09/30/monitor-azure-audit-logs-with-power-bi.aspx) (Visualizzare il log attività di Azure con Power BI).
* Post di blog [View and analyze Azure activity logs in Power BI and more](https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/) (Visualizzare e analizzare i log attività di Azure in Power BI e altre opzioni).

[1]: ./media/application-gateway-diagnostics/figure1.png
[2]: ./media/application-gateway-diagnostics/figure2.png
[3]: ./media/application-gateway-diagnostics/figure3.png
[4]: ./media/application-gateway-diagnostics/figure4.png
[5]: ./media/application-gateway-diagnostics/figure5.png
[6]: ./media/application-gateway-diagnostics/figure6.png
[7]: ./media/application-gateway-diagnostics/figure7.png
[8]: ./media/application-gateway-diagnostics/figure8.png
[9]: ./media/application-gateway-diagnostics/figure9.png
[10]: ./media/application-gateway-diagnostics/figure10.png
