---
title: Metriche di monitoraggio di Azure per il gateway applicazione
description: Informazioni su come usare le metriche per monitorare le prestazioni del gateway applicazione
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 06/06/2020
ms.author: absha
ms.openlocfilehash: c072e7c1339a2217a3c167be3237029bd71429c2
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/05/2020
ms.locfileid: "93397740"
---
# <a name="metrics-for-application-gateway"></a>Metriche per il gateway applicazione

Il gateway applicazione pubblica i punti dati, detti metrica, in [monitoraggio di Azure](../azure-monitor/overview.md) per le prestazioni del gateway applicazione e delle istanze back-end. Queste metriche sono valori numerici in un set ordinato di dati di serie temporali che descrivono un aspetto del gateway applicazione in un determinato momento. Se sono presenti richieste che passano attraverso il gateway applicazione, misura e invia le metriche in intervalli di 60 secondi. Se non sono presenti richieste che passano attraverso il gateway applicazione o nessun dato per una metrica, la metrica non viene segnalata. Per altre informazioni, vedere [metriche di monitoraggio di Azure](../azure-monitor/platform/data-platform-metrics.md).

## <a name="metrics-supported-by-application-gateway-v2-sku"></a>Metriche supportate dallo SKU del gateway applicazione V2

### <a name="timing-metrics"></a>Metriche temporali

Il gateway applicazione offre diverse metriche temporali predefinite relative alla richiesta e alla risposta, che sono tutte misurate in millisecondi. 

![Diagramma delle metriche temporali per il gateway applicazione.](./media/application-gateway-metrics/application-gateway-metrics.png)

> [!NOTE]
>
> Se sono presenti più listener nel gateway applicazione, filtrare sempre in base alla dimensione del *listener* confrontando le metriche di latenza diverse per ottenere un'inferenza significativa.

- **Tempo di connessione back-end**

  Tempo impiegato per stabilire una connessione con l'applicazione back-end. 

  Ciò include la latenza di rete e il tempo impiegato dallo stack TCP del server back-end per stabilire nuove connessioni. Nel caso di TLS, include anche il tempo trascorso nell'handshake. 

- **Tempo di risposta primo byte back-end**

  Intervallo di tempo tra l'inizio del tentativo di stabilire una connessione al server back-end e la ricezione del primo byte dell'intestazione della risposta. 

  In questo modo si approssimano la somma del *tempo di connessione back-end* , il tempo impiegato dalla richiesta per raggiungere il back-end dal gateway applicazione, il tempo impiegato dall'applicazione back-end per rispondere (il tempo impiegato dal server per generare il contenuto, potenzialmente recuperare le query di database) e il tempo impiegato dal primo byte della risposta per raggiungere il gateway applicazione dal back-end.

- **Tempo di risposta ultimo byte back-end**

  Intervallo di tempo tra l'inizio del tentativo di stabilire una connessione al server back-end e la ricezione dell'ultimo byte del corpo della risposta. 

  Si tratta all'incirca della somma tra il *tempo di risposta del primo byte del back-end* e il tempo di trasferimento dei dati. Questo numero può variare enormemente in base alle dimensioni degli oggetti richiesti e alla latenza di rete del server.

- **Tempo totale del gateway applicazione**

  Tempo medio necessario per la ricezione, l'elaborazione e la risposta della richiesta da inviare. 

  Si tratta dell'intervallo dal momento in cui il gateway applicazione riceve il primo byte della richiesta HTTP al momento in cui l'ultimo byte di risposta è stato inviato al client. Questo include il tempo di elaborazione impiegato dal gateway applicazione, il *tempo di risposta ultimo byte back-end* , il tempo impiegato dal gateway applicazione per inviare tutte le risposte e il RTT del *client*.

- **Client RTT (Scrittura in tempo reale client)**

  Tempo medio round trip tra i client e il gateway applicazione.



Queste metriche possono essere usate per determinare se il rallentamento osservato è dovuto alla rete client, alle prestazioni del gateway applicazione, alla rete di back-end e al server back-end, alla saturazione dello stack TCP, alle prestazioni dell'applicazione back-end o alle dimensioni del file di grandi dimensioni.

Se, ad esempio, si verifica un picco nella tendenza del *tempo di risposta del primo byte di back* -end, ma la tendenza del tempo di *connessione back* -end è stabile, è possibile dedurre che il gateway applicazione alla latenza del back-end e il tempo necessario per stabilire la connessione siano stabili e il picco è causato da un aumento del tempo di risposta dell'applicazione back-end. D'altra parte, se il picco nel *tempo di risposta del primo byte di back-end* è associato a un picco corrispondente nel tempo di *connessione back-end* , è possibile dedurre che la rete tra il gateway applicazione e il server back-end o lo stack TCP del server back-end è satura. 

Se si nota un picco nel *tempo di risposta dell'ultimo byte del back-end* , ma il *tempo di risposta del primo byte back-end* è stabile, è possibile dedurre che il picco è dovuto a un file più grande richiesto.

Analogamente, se il *tempo totale del gateway applicazione* ha un picco ma il *tempo di risposta ultimo byte del back-end* è stabile, può essere un segno di collo di bottiglia delle prestazioni nel gateway applicazione o un collo di bottiglia nella rete tra il client e il gateway applicazione. Inoltre, se il valore *RTT del client* dispone anche di un picco corrispondente, significa che il calo è dovuto alla rete tra il gateway applicazione e il client.

### <a name="application-gateway-metrics"></a>Metriche del gateway applicazione

Per il gateway applicazione sono disponibili le metriche seguenti:

- **Byte ricevuti**

   Numero di byte ricevuti dal gateway applicazione dai client

- **Byte inviati**

   Numero di byte inviati dal gateway applicazione ai client

- **Protocollo TLS client**

   Numero di richieste TLS e non TLS avviate dal client che hanno stabilito la connessione al gateway applicazione. Per visualizzare la distribuzione del protocollo TLS, filtrare in base al protocollo TLS della dimensione.

- **Unità di capacità correnti**

   Il numero di unità di capacità utilizzate per bilanciare il carico del traffico. Esistono tre fattori determinanti per la capacità: unità di calcolo, connessioni permanenti e velocità effettiva. Ogni unità di capacità è composta al massimo da: 1 unità di calcolo o 2500 connessioni persistenti o 2,22 Mbps di velocità effettiva.

- **Unità di calcolo correnti**

   Conteggio della capacità del processore utilizzata. I fattori che influiscono sull'unità di calcolo sono il numero di connessioni TLS/sec, i calcoli di URL Rewrite e l'elaborazione di regole di WAF. 

- **connessioni correnti**

   Il numero totale di connessioni simultanee attive dai client al gateway applicazione
   
- **Unità di capacità stimate per la fatturazione**

  Con lo SKU v2, il modello di determinazione dei prezzi è determinato dal consumo. Le unità di capacità misurano i costi in base al consumo addebitati in aggiunta al costo fisso. *Unità di capacità fatturate stimate* indica il numero di unità di capacità in base alle quali viene stimata la fatturazione. Questo calcolo viene eseguito considerando il valore maggiore tra *unità di capacità correnti* (le unità di capacità necessarie per il bilanciamento del carico del traffico) e *unità di capacità fatturabili fisse* (le unità di capacità minime di cui viene mantenuto il provisioning).

- **Richieste non riuscite**

  Numero di richieste gestite dal gateway applicazione con i codici di errore del server 5xx. Sono inclusi i codici 5xx generati dal gateway applicazione, nonché i codici 5xx generati dal back-end. Il numero di richieste può essere ulteriormente filtrato per visualizzare il conteggio per ogni combinazione di impostazioni http del pool back-end specifico.
   
- **Unità di capacità fatturabili fisse**

  Il numero minimo di unità di capacità di cui viene mantenuto il provisioning in base all'impostazione di *Numero minimo unità di scala* (un'istanza si traduce in 10 unità di capacità) nella configurazione del gateway applicazione.
   
 - **Nuove connessioni al secondo**

   Numero medio di nuove connessioni TCP al secondo stabilite dai client al gateway applicazione e dal gateway applicazione ai membri back-end.


- **Stato della risposta**

   Stato della risposta HTTP restituito dal gateway applicazione. La distribuzione del codice di stato della risposta può essere ulteriormente classificata per visualizzare le risposte nelle categorie 2xx, 3xx, 4xx e 5xx.

- **Velocità effettiva**

   Numero di byte al secondo distribuiti dal gateway applicazione

- **Richieste totali**

   Numero di richieste riuscite gestite dal gateway applicazione. Il numero di richieste può essere ulteriormente filtrato per visualizzare il conteggio per ogni combinazione di impostazioni http del pool back-end specifico.

### <a name="backend-metrics"></a>Metriche back-end

Per il gateway applicazione sono disponibili le metriche seguenti:

- **Stato della risposta back-end**

  Numero di codici di stato della risposta HTTP restituiti dai backend. Non sono inclusi i codici di risposta generati dal gateway applicazione. La distribuzione del codice di stato della risposta può essere ulteriormente classificata per visualizzare le risposte nelle categorie 2xx, 3xx, 4xx e 5xx.

- **Numero host integro**

  Il numero di backend che sono determinati integri dal probe di integrità. È possibile filtrare in base al pool back-end per visualizzare il numero di host integri in un pool back-end specifico.

- **Numero host non integro**

  Il numero di backend determinati da un probe di integrità non integro. È possibile filtrare in base al pool back-end per visualizzare il numero di host non integri in un pool back-end specifico.
  
- **Requests per minute per Healthy Host (Richieste al minuto per host integro)**

  Numero medio di richieste ricevute da ogni membro integro in un pool back-end in un minuto. È necessario specificare il pool back-end usando la dimensione *end di integri* .  
  

## <a name="metrics-supported-by-application-gateway-v1-sku"></a>Metriche supportate dallo SKU del gateway applicazione V1

### <a name="application-gateway-metrics"></a>Metriche del gateway applicazione

Per il gateway applicazione sono disponibili le metriche seguenti:

- **Utilizzo CPU**

  Visualizza l'utilizzo delle CPU allocate al gateway applicazione.  In condizioni normali, l'utilizzo della CPU non deve superare regolarmente il 90%, in quanto ciò potrebbe causare una latenza nei siti Web ospitati dietro il gateway applicazione e compromettere l'esperienza del client. È possibile controllare indirettamente o migliorare l'utilizzo della CPU modificando la configurazione del gateway applicazione tramite un aumento del numero di istanze o il passaggio a dimensioni di SKU maggiori oppure eseguendo entrambe queste operazioni.

- **Connessioni correnti**

  Numero di connessioni correnti stabilite con il gateway applicazione

- **Richieste non riuscite**

  Numero di richieste gestite dal gateway applicazione con i codici di errore del server 5xx. Sono inclusi i codici 5xx generati dal gateway applicazione, nonché i codici 5xx generati dal back-end. Il numero di richieste può essere ulteriormente filtrato per visualizzare il conteggio per ogni combinazione di impostazioni http del pool back-end specifico.

- **Stato della risposta**

  Stato della risposta HTTP restituito dal gateway applicazione. La distribuzione del codice di stato della risposta può essere ulteriormente classificata per visualizzare le risposte nelle categorie 2xx, 3xx, 4xx e 5xx.

- **Velocità effettiva**

  Numero di byte al secondo distribuiti dal gateway applicazione

- **Richieste totali**

  Numero di richieste riuscite gestite dal gateway applicazione. Il numero di richieste può essere ulteriormente filtrato per visualizzare il conteggio per ogni combinazione di impostazioni http del pool back-end specifico.

- **Web Application Firewall Blocked Requests Count (Web application firewall - Conteggio richieste bloccate)**
- **Distribuzione richieste bloccate del Web Application Firewall**
- **Web Application Firewall Total Rule Distribution (Web application firewall - Distribuzione regole totali)**

### <a name="backend-metrics"></a>Metriche back-end

Per il gateway applicazione sono disponibili le metriche seguenti:

- **Numero host integro**

  Il numero di backend che sono determinati integri dal probe di integrità. È possibile filtrare in base al pool back-end per visualizzare il numero di host integri in un pool back-end specifico.

- **Numero host non integro**

  Il numero di backend determinati da un probe di integrità non integro. È possibile filtrare in base al pool back-end per visualizzare il numero di host non integri in un pool back-end specifico.

## <a name="metrics-visualization"></a>Visualizzazione metrica

Passare a un gateway applicazione, in **monitoraggio** selezionare **metriche**. Per visualizzare i valori disponibili, selezionare l'elenco a discesa **METRICA**.

Nella figura seguente è illustrato un esempio con tre metriche visualizzate per gli ultimi 30 minuti:

:::image type="content" source="media/application-gateway-diagnostics/figure5.png" alt-text="Visualizzazione metrica." lightbox="media/application-gateway-diagnostics/figure5-lb.png":::

Per un elenco delle metriche correnti, vedere [Metriche supportate con il monitoraggio di Azure](../azure-monitor/platform/metrics-supported.md).

### <a name="alert-rules-on-metrics"></a>Regole di avviso per le metriche

È possibile avviare le regole di avviso in base alle metriche per una risorsa. Ad esempio, un avviso può chiamare un webhook o inviare un messaggio di posta elettronica a un amministratore se la velocità effettiva del gateway applicazione è al di sopra, al di sotto o corrisponde alla soglia per un periodo di tempo specificato.

L'esempio seguente illustra la creazione di una regola di avviso per l'invio di un messaggio di posta elettronica a un amministratore al superamento della soglia della velocità effettiva:

1. Selezionare **Aggiungi avviso metrica** per aprire la pagina **Aggiungi regola** . È anche possibile accedere a questa pagina dalla pagina metrica.

   ![Pulsante "Aggiungi avviso per la metrica"][6]

2. Nella pagina **Aggiungi regola** , compilare le sezioni nome, condizione e notifica, quindi selezionare **OK**.

   * Nel selettore **Condizione** selezionare uno dei quattro valori seguenti: **Maggiore di** , **Maggiore di o uguale a** , **Minore di** o **Minore o uguale a**.

   * Nel selettore **Periodo** selezionare un periodo compreso tra cinque minuti e sei ore.

   * Se si seleziona **Invia messaggio di posta elettronica a proprietari, collaboratori e lettori** , il messaggio di posta elettronica può essere dinamico basato sugli utenti che hanno accesso alla risorsa. In alternativa, è possibile inserire un elenco di utenti separato da virgole nella casella **Indirizzi di posta elettronica aggiuntivi dell'amministratore**.

   ![Pagina Aggiungi regola][7]

Se la soglia viene superata, l'utente riceve un messaggio di posta elettronica simile al seguente:

![Messaggio di posta elettronica per il superamento della soglia][8]

Dopo aver creato un avviso per la metrica, viene visualizzato un elenco di avvisi. L'elenco offre una panoramica di tutte le regole di avviso.

![Elenco di avvisi e regole][9]

Per altre informazioni sulle notifiche di avviso, vedere [Ricevere notifiche di avviso](../azure-monitor/platform/alerts-overview.md).

Per altre informazioni sui webhook e su come usarli con gli avvisi, vedere [Configurare un webhook in un avviso relativo alle metriche di Azure](../azure-monitor/platform/alerts-webhooks.md).

## <a name="next-steps"></a>Passaggi successivi

* Visualizzare i log contatori ed eventi usando i [log di Monitoraggio di Azure](../azure-monitor/insights/azure-networking-analytics.md).
* Post di blog [Visualize your Azure activity log with Power BI](https://powerbi.microsoft.com/blog/monitor-azure-audit-logs-with-power-bi/) (Visualizzare il log attività di Azure con Power BI).
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