---
title: Log di diagnostica per il servizio Azure SignalR
description: Informazioni su come configurare i log di diagnostica per il servizio Azure SignalR e su come usarlo per risolvere i problemi di autonomia.
author: wanl
ms.service: signalr
ms.topic: conceptual
ms.date: 12/17/2019
ms.author: wanl
ms.openlocfilehash: c5def306e5aa874432ebb5f47f3fdd8b5234ffd7
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75479553"
---
# <a name="diagnostic-logs-for-azure-signalr-service"></a>Log di diagnostica per il servizio Azure SignalR

Questa esercitazione illustra i log di diagnostica per il servizio Azure SignalR e come configurare i log di diagnostica e come risolvere i problemi con i log di diagnostica.

## <a name="prerequisites"></a>Prerequisiti
Per abilitare i log di diagnostica, è necessario disporre di un punto in cui archiviare i dati di log. Questa esercitazione Usa archiviazione di Azure e Log Analytics.

* [Archiviazione di Azure](../azure-monitor/platform/resource-logs-collect-storage.md) : conserva i log di diagnostica per il controllo dei criteri, l'analisi statica o il backup.
* [Log Analytics](../azure-monitor/platform/resource-logs-collect-workspace.md) : strumento di ricerca e analisi dei log flessibile che consente di analizzare i log non elaborati generati da una risorsa di Azure.

## <a name="set-up-diagnostic-logs-for-an-azure-signalr-service"></a>Configurare i log di diagnostica per un servizio Azure SignalR

È possibile visualizzare i log di diagnostica per il servizio Azure SignalR. Questi log forniscono una visualizzazione più dettagliata della connettività all'istanza del servizio Azure SignalR. I log di diagnostica forniscono informazioni dettagliate su ogni connessione. Ad esempio, le informazioni di base (ID utente, ID connessione e tipo di trasporto e così via) e le informazioni sull'evento (connessione, disconnessione e evento di interruzione e così via) della connessione. I log di diagnostica possono essere usati per l'identificazione dei problemi, il monitoraggio e l'analisi della connessione.

### <a name="enable-diagnostic-logs"></a>Abilitare i log di diagnostica

I log di diagnostica sono disabilitati per impostazione predefinita. Per abilitare i log di diagnostica, eseguire i passaggi seguenti:

1.  Nel [portale di Azure](https://portal.azure.com), in **monitoraggio**, fare clic su **impostazioni di diagnostica**.

    ![Spostamento riquadro in impostazioni di diagnostica](./media/signalr-tutorial-diagnostic-logs/diagnostic-settings-menu-item.png)

1.  Quindi fare clic su **Aggiungi impostazione di diagnostica**.

    ![Aggiungere i log di diagnostica](./media/signalr-tutorial-diagnostic-logs/add-diagnostic-setting.png)

1.  Impostare la destinazione dell'archivio desiderata. Attualmente è supportata **l'archiviazione in un account di archiviazione e l'** **invio a log Analytics**.

1. Selezionare i log che si desidera archiviare.

    ![Riquadro Impostazioni di diagnostica](./media/signalr-tutorial-diagnostic-logs/diagnostics-settings-pane.png)


1.  Salvare le nuove impostazioni di diagnostica.

Le nuove impostazioni diventano effettive in circa 10 minuti. Trascorso questo tempo, i log vengono visualizzati nella destinazione di archiviazione configurata, nel riquadro **Log di diagnostica**.

Per altre informazioni sulla configurazione della diagnostica, vedere la [panoramica dei log di diagnostica di Azure](../azure-monitor/platform/resource-logs-overview.md).

### <a name="diagnostic-logs-categories"></a>Categorie dei log di diagnostica

Il servizio Azure SignalR acquisisce i log di diagnostica in una categoria:

* **Tutti i log**: tenere traccia delle connessioni che si connettono al servizio Azure SignalR. I log forniscono informazioni sulla connessione, la disconnessione, l'autenticazione e la limitazione. Per ulteriori informazioni, vedere la sezione successiva.

### <a name="archive-to-a-storage-account"></a>Archivia in un account di archiviazione

I log vengono archiviati nell'account di archiviazione configurato nel riquadro **log di diagnostica** . Un contenitore denominato `insights-logs-alllogs` viene creato automaticamente per archiviare i log di diagnostica. All'interno del contenitore i log vengono archiviati nel file `resourceId=/SUBSCRIPTIONS/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/RESOURCEGROUPS/XXXX/PROVIDERS/MICROSOFT.SIGNALRSERVICE/SIGNALR/XXX/y=YYYY/m=MM/d=DD/h=HH/m=00/PT1H.json`. Fondamentalmente, il percorso è combinato per `resource ID` e `Date Time`. I file di log vengono suddivisi per `hour`. I minuti sono pertanto sempre `m=00`.

Tutti i log vengono archiviati in formato JavaScript Object Notation (JSON). Ogni voce presenta campi stringa che usano il formato descritto nelle sezioni seguenti.

Le stringhe JSON di log di archiviazione includono elementi elencati nelle tabelle seguenti:

**Formato**

Nome | Description
------- | -------
time | Ora evento log
level | Livello evento log
resourceId | ID risorsa del servizio Azure SignalR
posizione | Località del servizio Azure SignalR
category | Categoria dell'evento log
operationName | Nome dell'operazione dell'evento
callerIpAddress | Indirizzo IP del server/client
properties | Proprietà dettagliate correlate a questo evento del log. Per ulteriori dettagli, vedere la tabella delle proprietà seguente.

**Tabella delle proprietà**

Nome | Description
------- | -------
type | Tipo di evento del log. Attualmente, vengono fornite informazioni sulla connettività al servizio Azure SignalR. È disponibile solo `ConnectivityLogs` tipo
collection | Raccolta dell'evento log. I valori consentiti sono: `Connection`, `Authorization` e `Throttling`
connectionId | Identità della connessione
transportType | Tipo di trasporto della connessione. I valori consentiti sono: `Websockets` \| `ServerSentEvents` \| `LongPolling`
connectionType | Tipo di connessione. I valori consentiti sono: `Server` \| `Client`. `Server`: connessione dal lato server; `Client`: connessione dal lato client
userId | Identità dell'utente
message | Messaggio dettagliato dell'evento del log

Il codice seguente è un esempio di stringa JSON di log di archiviazione:

```json
{
    "properties": {
        "message": "Entered Serverless mode.",
        "type": "ConnectivityLogs",
        "collection": "Connection",
        "connectionId": "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx",
        "userId": "User",
        "transportType": "WebSockets",
        "connectionType": "Client"
    },
    "operationName": "ServerlessModeEntered",
    "category": "AllLogs",
    "level": "Informational",
    "callerIpAddress": "xxx.xxx.xxx.xxx",
    "time": "2019-01-01T00:00:00Z",
    "resourceId": "/SUBSCRIPTIONS/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/RESOURCEGROUPS/XXXX/PROVIDERS/MICROSOFT.SIGNALRSERVICE/SIGNALR/XXX",
    "location": "xxxx"
}
```

### <a name="archive-logs-schema-for-log-analytics"></a>Schema dei log di archiviazione per Log Analytics

Per visualizzare i log di diagnostica, attenersi alla procedura seguente:

1. Fare clic su `Logs` nel Log Analytics di destinazione.

    ![Voce di menu Log Analytics](./media/signalr-tutorial-diagnostic-logs/log-analytics-menu-item.png)

2. Immettere `SignalRServiceDiagnosticLogs` e selezionare intervallo di tempo per eseguire query sui log di diagnostica. Per le query avanzate, vedere [Introduzione a log Analytics in monitoraggio di Azure](../azure-monitor/log-query/get-started-portal.md)

    ![Log di query in Log Analytics](./media/signalr-tutorial-diagnostic-logs/query-log-in-log-analytics.png)

Le colonne del log di archiviazione includono gli elementi elencati nella tabella seguente:

Nome | Description
------- | ------- 
TimeGenerated | Ora evento log
Raccolta | Raccolta dell'evento log. I valori consentiti sono: `Connection`, `Authorization` e `Throttling`
OperationName | Nome dell'operazione dell'evento
Percorso | Località del servizio Azure SignalR
Livello | Livello evento log
CallerIpAddress | Indirizzo IP del server/client
Messaggio | Messaggio dettagliato dell'evento del log
UserId | Identità dell'utente
ConnectionId | Identità della connessione
ConnectionType | Tipo di connessione. I valori consentiti sono: `Server` \| `Client`. `Server`: connessione dal lato server; `Client`: connessione dal lato client
TransportType | Tipo di trasporto della connessione. I valori consentiti sono: `Websockets` \| `ServerSentEvents` \| `LongPolling`

### <a name="troubleshooting-with-diagnostic-logs"></a>Risoluzione dei problemi con i log di diagnostica

Per risolvere i problemi relativi al servizio Azure SignalR, è possibile abilitare i log lato server/client per l'acquisizione degli errori. Attualmente, il servizio Azure SignalR espone i log di diagnostica, è anche possibile abilitare i log per il lato servizio.

Quando si verifica una situazione di crescita o eliminazione imprevista della connessione, è possibile utilizzare i log di diagnostica per la risoluzione dei problemi.

I problemi tipici sono spesso relativi a modifiche impreviste per le connessioni, le connessioni raggiungono i limiti di connessione e l'errore di autorizzazione. Vedere le sezioni successive sulla risoluzione dei problemi.

#### <a name="unexpected-connection-number-changes"></a>Modifiche impreviste al numero di connessione

##### <a name="unexpected-connection-dropping"></a>Eliminazione di connessioni imprevista

Se si verificano perdite di connessioni impreviste, abilitare prima di tutto i log nei lati servizio, server e client.

Se una connessione si disconnette, i log di diagnostica registreranno questo evento di disconnessione. verrà visualizzato `ConnectionAborted` o `ConnectionEnded` in `operationName`.

La differenza tra `ConnectionAborted` e `ConnectionEnded` è che `ConnectionEnded` è una disconnessione prevista attivata dal lato client o server. Mentre il `ConnectionAborted` è in genere un evento imprevisto di eliminazione della connessione e il motivo dell'interruzione verrà fornito in `message`.

I motivi di interruzione sono elencati nella tabella seguente:

Motivo | Description
------- | ------- 
Il numero di connessioni raggiunge il limite | Il numero di connessioni raggiunge il limite del livello di prezzo corrente. Prendere in considerazione l'unità di servizio di scalabilità verticale
Il server applicazioni ha chiuso la connessione | Il server applicazioni attiva l'aborto. Può essere considerato come un aborto previsto
Timeout ping connessione | In genere è causata da un problema di rete. Provare a controllare la disponibilità del server app da Internet
Ricaricamento del servizio, riconnessione | Il servizio Azure SignalR viene ricaricato. Azure SignalR supporta la riconnessione automatica. è possibile attendere il completamento della riconnessione o riconnessione manuale al servizio Azure SignalR
Errore temporaneo del server interno | Si verifica un errore temporaneo nel servizio Azure SignalR, che deve essere recuperato automaticamente
Connessione al server eliminata | La connessione al server si interrompe con un errore sconosciuto. per prima cosa, provare a eseguire la risoluzione dei problemi con il log del lato servizio/server/client Provare a escludere problemi di base, ad esempio problema di rete, problema sul lato server app e così via. Se il problema non viene risolto, contattaci per ulteriori informazioni. Per ulteriori informazioni, vedere la sezione [ottenere la guida](#get-help) . 

##### <a name="unexpected-connection-growing"></a>Aumento imprevisto della connessione

Per risolvere i problemi relativi a un aumento imprevisto della connessione, è prima di tutto necessario filtrare le connessioni aggiuntive. È possibile aggiungere un ID utente di test univoco alla connessione client di test. Quindi, verificarlo con i log di diagnostica, se vengono visualizzate più connessioni client con lo stesso ID utente test o IP, è probabile che il lato client crei e stabilisca un numero maggiore di connessioni rispetto a quello previsto. Verificare il lato client.

#### <a name="authorization-failure"></a>Errore di autorizzazione

Se viene restituito 401 non autorizzato per le richieste client, controllare i log di diagnostica. Se si verifica `Failed to validate audience. Expected Audiences: <valid audience>. Actual Audiences: <actual audience>`, significa che tutti i gruppi di destinatari nel token di accesso non sono validi. Provare a usare i destinatari validi suggeriti nel log.


#### <a name="throttling"></a>Limitazione

Se non è possibile stabilire connessioni client SignalR al servizio Azure SignalR, controllare i log di diagnostica. Se si verificano `Connection count reaches limit` nel log di diagnostica, si stabiliscono troppe connessioni al servizio SignalR, che raggiungono il limite del numero di connessioni. Prendere in considerazione la scalabilità verticale del servizio SignalR. Se si verifica `Message count reaches limit` nel log di diagnostica, significa che si usa il livello gratuito e si usa la quota di messaggi. Se si desidera inviare più messaggi, provare a modificare il servizio SignalR nel livello standard per inviare messaggi aggiuntivi. Per altre informazioni, vedere [prezzi del servizio Azure SignalR](https://azure.microsoft.com/pricing/details/signalr-service/).

### <a name="get-help"></a>Ottenere supporto

Si consiglia di eseguire prima la risoluzione dei problemi. La maggior parte dei problemi è causata da problemi di rete o del server dell'app. Seguire la [Guida alla risoluzione dei problemi con il log di diagnostica](#troubleshooting-with-diagnostic-logs) e la guida alla risoluzione dei problemi di [base](https://github.com/Azure/azure-signalr/blob/dev/docs/tsg.md) per individuare la causa principale.
Se il problema persiste, è consigliabile aprire un problema in GitHub o creare un ticket nel portale di Azure.
Specificare:
1. Intervallo di tempo di circa 30 minuti quando si verifica il problema
2. ID risorsa del servizio Azure SignalR
3. Dettagli del problema, il più possibile specifico: ad esempio, AppServer non invia messaggi, le perdite di connessione client e così via
4. Log raccolti dal lato server/client e altro materiale che può essere utile
5. Opzionale Codice di ripetizione

> Nota: se si apre un problema in GitHub, tenere private le informazioni riservate (ad esempio, ID risorsa, log server/client), inviare solo ai membri nell'organizzazione Microsoft privata.  
