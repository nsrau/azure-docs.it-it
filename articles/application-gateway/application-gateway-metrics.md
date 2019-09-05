---
title: Metriche di monitoraggio di Azure per il gateway applicazione
description: Informazioni su come usare le metriche per monitorare le prestazioni del gateway applicazione
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 8/29/2019
ms.author: absha
ms.openlocfilehash: f0937ee53e66cb1bf0c5d6b55a8dde045570e924
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70309771"
---
# <a name="metrics-for-application-gateway"></a>Metriche per il gateway applicazione

Il gateway applicazione pubblica i punti dati, detti metrica, in [monitoraggio di Azure](https://docs.microsoft.com/azure/azure-monitor/overview) per le prestazioni del gateway applicazione e delle istanze back-end. Queste metriche sono valori numerici in un set ordinato di dati di serie temporali che descrivono un aspetto del gateway applicazione in un determinato momento. Se sono presenti richieste che passano attraverso il gateway applicazione, misura e invia le metriche in intervalli di 60 secondi. Se non sono presenti richieste che passano attraverso il gateway applicazione o nessun dato per una metrica, la metrica non viene segnalata. Per altre informazioni, vedere le [metriche di Monitoraggio di Azure](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-metrics).

## <a name="metrics-supported-by-application-gateway-v2-sku"></a>Metriche supportate dallo SKU del gateway applicazione V2

### <a name="timing-metrics"></a>Metriche temporali

Sono disponibili le metriche seguenti relative alla tempistica della richiesta e della risposta. Analizzando queste metriche, è possibile determinare se il rallentamento dell'applicazione a causa della WAN, del gateway applicazione, della rete tra il gateway applicazione e il back-end o le prestazioni dell'applicazione.

- **RTT client**

  Tempo medio round trip tra i client e il gateway applicazione. Questa metrica indica il tempo necessario per stabilire le connessioni e restituire i riconoscimenti.

- **Tempo totale del gateway applicazione**

  Tempo medio necessario per l'elaborazione di una richiesta e la relativa risposta da inviare. Viene calcolato come media dell'intervallo dal momento in cui il gateway applicazione riceve il primo byte di una richiesta HTTP al momento in cui termina l'operazione di invio della risposta. È importante tenere presente che questo in genere include il tempo di elaborazione del gateway applicazione, il tempo in cui i pacchetti di richiesta e risposta vengono spostati in rete e il tempo impiegato dal server back-end per rispondere.

- **Tempo di connessione back-end**

  Tempo impiegato per stabilire una connessione con un server back-end. 

- **Tempo di risposta primo byte back-end**

  Intervallo di tempo tra l'inizio del tentativo di stabilire una connessione al server back-end e la ricezione del primo byte dell'intestazione della risposta, il tempo di elaborazione approssimativo del server back-end

- **Tempo di risposta ultimo byte back-end**

  Intervallo di tempo tra l'inizio del tentativo di stabilire una connessione al server back-end e la ricezione dell'ultimo byte del corpo della risposta

### <a name="application-gateway-metrics"></a>Metriche del gateway applicazione

Per il gateway applicazione sono disponibili le metriche seguenti:

- **Byte ricevuti**

   Numero di byte ricevuti dal gateway applicazione dai client

- **Byte inviati**

   Numero di byte inviati dal gateway applicazione ai client

- **Protocollo TLS client**

   Numero di richieste TLS e non TLS avviate dal client che hanno stabilito la connessione al gateway applicazione. Per visualizzare la distribuzione del protocollo TLS, filtrare in base al protocollo TLS della dimensione.

- **Unità di capacità correnti**

   Numero di unità di capacità utilizzate. Le unità di capacità misurano i costi in base al consumo addebitati in aggiunta al costo fisso. Esistono tre fattori determinanti per la capacità: unità di calcolo, connessioni permanenti e velocità effettiva. Ogni unità di capacità è costituita al massimo da: 1 unità di calcolo o 2500 connessioni permanenti o velocità effettiva di 2,22 Mbps.

- **Unità di calcolo correnti**

   Conteggio della capacità del processore utilizzata. I fattori che influiscono sull'unità di calcolo sono connessioni TLS/sec, calcoli di riscrittura URL e elaborazione di regole WAF. 

- **Connessioni correnti**

   Numero di connessioni correnti stabilite con il gateway applicazione

- **Richieste non riuscite**

   Numero di richieste non riuscite gestite dal gateway applicazione. Il numero di richieste può essere ulteriormente filtrato per visualizzare il conteggio per ogni combinazione di impostazioni http del pool back-end specifico.


- **Stato della risposta**

   Stato della risposta HTTP restituito dal gateway applicazione. La distribuzione del codice di stato della risposta può essere ulteriormente classificata per visualizzare le risposte nelle categorie 2xx, 3xx, 4xx e 5xx.

- **Velocità effettiva**

   Numero di byte al secondo distribuiti dal gateway applicazione

- **Richieste totali**

   Numero di richieste riuscite gestite dal gateway applicazione. Il numero di richieste può essere ulteriormente filtrato per visualizzare il conteggio per ogni combinazione di impostazioni http del pool back-end specifico.

- **Regole corrispondenti al firewall applicazione Web**

- **Regole attive del Web Application Firewall**

### <a name="backend-metrics"></a>Metriche back-end

Per il gateway applicazione sono disponibili le metriche seguenti:

- **Stato della risposta back-end**

  Numero di codici di stato della risposta HTTP restituiti dai backend. Non sono inclusi i codici di risposta generati dal gateway applicazione. La distribuzione del codice di stato della risposta può essere ulteriormente classificata per visualizzare le risposte nelle categorie 2xx, 3xx, 4xx e 5xx.

- **Numero host integro**

  Il numero di backend che sono determinati integri dal probe di integrità. È possibile filtrare in base al pool back-end per visualizzare gli host integri o non integri in un pool back-end specifico.

- **Numero host non integro**

  Il numero di backend determinati da un probe di integrità non integro. È possibile filtrare in base al pool back-end per visualizzare gli host non integri in un pool back-end specifico.

## <a name="metrics-supported-by-application-gateway-v1-sku"></a>Metriche supportate dallo SKU del gateway applicazione V1

### <a name="application-gateway-metrics"></a>Metriche del gateway applicazione

Per il gateway applicazione sono disponibili le metriche seguenti:

- **Connessioni correnti**

  Numero di connessioni correnti stabilite con il gateway applicazione

- **Richieste non riuscite**

  Numero di richieste non riuscite gestite dal gateway applicazione. Il numero di richieste può essere ulteriormente filtrato per visualizzare il conteggio per ogni combinazione di impostazioni http del pool back-end specifico.

- **Stato della risposta**

  Stato della risposta HTTP restituito dal gateway applicazione. La distribuzione del codice di stato della risposta può essere ulteriormente classificata per visualizzare le risposte nelle categorie 2xx, 3xx, 4xx e 5xx.

- **Velocità effettiva**

  Numero di byte al secondo distribuiti dal gateway applicazione

- **Richieste totali**

  Numero di richieste riuscite gestite dal gateway applicazione. Il numero di richieste può essere ulteriormente filtrato per visualizzare il conteggio per ogni combinazione di impostazioni http del pool back-end specifico.

- **Regole corrispondenti al firewall applicazione Web**

- **Regole attive del Web Application Firewall**

### <a name="backend-metrics"></a>Metriche back-end

Per il gateway applicazione sono disponibili le metriche seguenti:

- **Numero host integro**

  Il numero di backend che sono determinati integri dal probe di integrità. È possibile filtrare in base al pool back-end per visualizzare gli host integri o non integri in un pool back-end specifico.

- **Numero host non integro**

  Il numero di backend determinati da un probe di integrità non integro. È possibile filtrare in base al pool back-end per visualizzare gli host non integri in un pool back-end specifico.

## <a name="metrics-visualization"></a>Visualizzazione metrica

Passare a un gateway applicazione, in **monitoraggio** selezionare **metriche**. Per visualizzare i valori disponibili, selezionare l'elenco a discesa **METRICA**.

Nella figura seguente è illustrato un esempio con tre metriche visualizzate per gli ultimi 30 minuti:

[![](media/application-gateway-diagnostics/figure5.png "Visualizzazione delle metriche")](media/application-gateway-diagnostics/figure5-lb.png#lightbox)

Per un elenco delle metriche correnti, vedere [Metriche supportate con il monitoraggio di Azure](../azure-monitor/platform/metrics-supported.md).

### <a name="alert-rules-on-metrics"></a>Regole di avviso per le metriche

È possibile avviare le regole di avviso in base alle metriche per una risorsa. Ad esempio, un avviso può chiamare un webhook o inviare un messaggio di posta elettronica a un amministratore se la velocità effettiva del gateway applicazione è al di sopra, al di sotto o corrisponde alla soglia per un periodo di tempo specificato.

L'esempio seguente illustra la creazione di una regola di avviso per l'invio di un messaggio di posta elettronica a un amministratore al superamento della soglia della velocità effettiva:

1. Selezionare **Aggiungi avviso metrica** per aprire la pagina **Aggiungi regola** . È anche possibile accedere a questa pagina dalla pagina metrica.

   ![Pulsante "Aggiungi avviso per la metrica"][6]

2. Nella pagina **Aggiungi regola** , compilare le sezioni nome, condizione e notifica, quindi selezionare **OK**.

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
