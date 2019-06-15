---
title: Come risolvere i problemi con l'agente di Log Analitica per Windows | Microsoft Docs
description: Descrivere i sintomi, cause e la risoluzione dei problemi più comuni con l'agente di Log Analitica per Windows in Monitoraggio di Azure.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: ''
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/12/2019
ms.author: magoedte
ms.openlocfilehash: afa4483677336e9a887908a8cccf9590eed27af3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67120110"
---
# <a name="how-to-troubleshoot-issues-with-the-log-analytics-agent-for-windows"></a>Come risolvere i problemi con l'agente di Log Analitica per Windows 

Questo articolo fornisce indicazioni sulla risoluzione di errori che potrebbero verificarsi con l'agente di Log Analitica per Windows in Monitoraggio di Azure e suggerisce possibili soluzioni per risolverli.

Se nessuno dei passaggi descritti risulta adatto alle proprie esigenze, sono disponibili anche i canali di supporto seguenti:

* I clienti che usufruiscono dei vantaggi del supporto tecnico Premier possono aprire una richiesta di supporto con [Premier](https://premier.microsoft.com/).
* I clienti con un contratto di supporto tecnico di Azure possono aprire una richiesta di supporto nel [portale di Azure](https://manage.windowsazure.com/?getsupport=true).
* Visitare la pagina del feedback di Log Analytics per esaminare i bug e i suggerimenti inviati, all'indirizzo [https://aka.ms/opinsightsfeedback](https://aka.ms/opinsightsfeedback), o segnalarne di nuovi. 

## <a name="important-troubleshooting-sources"></a>Origini di risoluzione dei problemi importanti

 Per facilitare la risoluzione dei problemi relativi all'agente di Log Analitica per Windows, l'agente registra gli eventi al registro eventi di Windows, in particolare in *registri applicazioni e Manager Services\Operations*.  

## <a name="connectivity-issues"></a>Problemi di connettività

Se l'agente comunica attraverso un server proxy o firewall, potrebbe esserci restrizioni impediscono la comunicazione dal computer di origine e il servizio di monitoraggio di Azure. Se la comunicazione viene bloccata, errore di configurazione, la registrazione con un'area di lavoro potrebbe non riuscire anche se provi a installare l'agente, configurare le post-impostazioni di agent per fare riferimento a un'area di lavoro aggiuntiva o della comunicazione degli agenti ha esito negativo dopo la registrazione ha esito positivo. In questa sezione vengono descritti i metodi per risolvere i problemi di questo tipo di problema con l'agente di Windows. 

Verificare che il firewall o proxy è configurato per consentire le seguenti porte e agli URL descritti nella tabella seguente. Anche confermare ispezione HTTP non è abilitato per il traffico web, come è possibile che venga impedita un canale sicuro TLS tra l'agente e monitoraggio di Azure.  

|Risorsa agente|Porte |Direzione |Ignorare l'analisi HTTPS|
|------|---------|--------|--------|   
|*.ods.opinsights.azure.com |Porta 443 |In uscita|Sì |  
|*.oms.opinsights.azure.com |Porta 443 |In uscita|Sì |  
|*.blob.core.windows.net |Porta 443 |In uscita|Sì |  
|*.azure-automation.net |Porta 443 |In uscita|Sì |  

Per informazioni sul firewall necessarie per Azure per enti pubblici, vedere [gestione di Azure per enti pubblici](../../azure-government/documentation-government-services-monitoringandmanagement.md#azure-monitor-logs). 

Esistono diversi modi, è possibile verificare se l'agente comunica correttamente con monitoraggio di Azure.

- Abilitare la [valutazione dell'integrità dell'agente di Azure Log Analitica](../insights/solution-agenthealth.md) nell'area di lavoro. Nel dashboard di integrità agente, visualizzare il **conteggio degli agenti che non risponde** colonna per visualizzare rapidamente se l'agente viene elencato.  

- Eseguire la query seguente per verificare che l'agente invia un heartbeat nell'area di lavoro a che è configurato per inviare report. Sostituire <ComputerName> con il nome effettivo del computer.

    ```
    Heartbeat 
    | where Computer like "<ComputerName>"
    | summarize arg_max(TimeGenerated, * ) by Computer 
    ```

    Se il computer è stato sta comunicando con il servizio, la query deve restituire un risultato. Se la query non ha restituito un risultato, verificare prima che l'agente è configurato per inviare report a area di lavoro corretta. Se è configurato correttamente, andare al passaggio 3 e cercare nel registro eventi di Windows per identificare se l'agente registra identificare il problema potrebbe essere ne impediscono la comunicazione con monitoraggio di Azure.

- Un altro metodo per identificare un problema di connettività consiste nell'eseguire la **TestCloudConnectivity** dello strumento. Lo strumento viene installato per impostazione predefinita con l'agente nella cartella *%systemroot%\Programmi\Microsoft c:\Programmi\Microsoft Monitoring Agent\Agent*. Da un prompt dei comandi con privilegi elevati, passare alla cartella ed eseguire lo strumento. Lo strumento restituisce i risultati e i dati principali in cui il test non è riuscito (ad esempio, se è stato collegato a un porta/URL specifico che è stato bloccato). 

    ![Risultati dell'esecuzione dello strumento TestCloudConnection](./media/agent-windows-troubleshoot/output-testcloudconnection-tool-01.png)

- Filtro il *Operations Manager* registro eventi dal **origini evento** - *moduli del servizio integrità*, *HealthService*, e *Service Connector* e filtrare in base al **livello evento** *avviso* e *errore* per verificare se la scrittura degli eventi dal Nella tabella seguente. In tal caso, esaminare i passaggi di risoluzione inclusi per ogni evento possibili.

    |ID evento |`Source` |Descrizione |Risoluzione |
    |---------|-------|------------|-----------|
    |2133 & 2129 |Servizio integrità |Impossibile stabilire la connessione al servizio dall'agente |Questo errore può verificarsi quando l'agente non può comunicare direttamente o tramite un server proxy/firewall con il servizio di monitoraggio di Azure. Verificare le impostazioni di proxy dell'agente o che il firewall/proxy di rete consenta il traffico TCP dal computer al servizio.|
    |2138 |Moduli del servizio integrità |Proxy richiede l'autenticazione |Configurare le impostazioni del proxy dell'agente e specificare il nome utente e la password necessaria per l'autenticazione con il server proxy. |
    |2129 |Moduli del servizio integrità |Connessione non riuscito/Failed la negoziazione SSL |Controllare le impostazioni TCP/IP della scheda di rete e le impostazioni di proxy dell'agente.|
    |2127 |Moduli del servizio integrità |Errore durante l'invio di dati ricevuto il codice di errore |Se il problema interessa solo periodicamente durante il giorno, è possibile semplicemente un'anomalia casuale che può essere ignorata. Monitoraggio per capire con quale frequenza si verifica. Se si verifica spesso nel corso della giornata, verificare innanzitutto la configurazione di rete e le impostazioni del proxy. Se la descrizione include il codice di errore HTTP 404 ed è la prima volta che l'agente prova a inviare dati al servizio, includerà un errore di tipo 500 con codice 404 errore interno. 404, ovvero non viene trovato, che indica che l'area di archiviazione per la nuova area di lavoro è comunque sottoposto a provisioning. Al successivo tentativo verranno correttamente di scrittura dei dati all'area di lavoro come previsto. Errore HTTP 403 potrebbe indicare un problema di credenziali o autorizzazioni. C'è altro incluso con l'errore 403 per consentire di risolvere il problema.|
    |4000 |Connettore del servizio |Risoluzione dei nomi DNS non è riuscita |Il computer non è stato possibile risolvere l'indirizzo Internet usata quando si inviano dati al servizio. Potrebbe trattarsi di impostazioni del resolver DNS nel computer, le impostazioni del proxy non corretta o forse un problema temporaneo di DNS con il provider. Se si verifica periodicamente, potrebbe essere causato da un problema temporaneo relativo alla rete.|
    |4001 |Connettore del servizio |Impossibile connettersi al servizio. |Questo errore può verificarsi quando l'agente non può comunicare direttamente o tramite un server proxy/firewall con il servizio di monitoraggio di Azure. Verificare le impostazioni di proxy dell'agente o che il firewall/proxy di rete consenta il traffico TCP dal computer al servizio.|
    |4002 |Connettore del servizio |Il servizio ha restituito il codice di stato HTTP 403 in risposta a una query. Contattare l'amministratore del servizio per l'integrità del servizio. La query verrà ritentata in un secondo momento. |Questo errore viene scritto durante la fase di registrazione iniziale dell'agente e verrà visualizzato un URL simile al seguente: *https://<workspaceID>.oms.opinsights.azure.com/AgentService.svc/AgentTopologyRequest*. Un errore 403 indica accesso negato di codice e può essere causato da un ID area di lavoro digitata incorrettamente o una chiave o la data e l'ora non è corretto nel computer. Se l'ora è sfasata di +/- 15 minuti rispetto all'ora corrente, l'onboarding ha esito negativo. Per risolvere il problema, aggiornare la data e/o il fuso orario del computer Windows.|

## <a name="data-collection-issues"></a>Problemi di raccolta dati

Dopo aver installato l'agente e i report relativa area di lavoro configurata o aree di lavoro, potrebbe smettere di ricevere configurazione, la raccolta o l'inoltro prestazioni, log o altri dati per il servizio a seconda di ciò che è abilitato e il computer di destinazione. È necessario determinare se:

- Si tratta di un particolare tipo di dati o tutti i dati che non sono disponibili nell'area di lavoro?
- Tipo di dati specificato da una soluzione o specificata come parte della configurazione di raccolta dati dell'area di lavoro?
- Il numero di computer interessato? Si tratta di un singolo o più computer che fanno riferimento all'area di lavoro?
- È stata quest ' ultimo funziona e si è arrestato a una determinata ora del giorno o è mai stato raccolto? 
- La query di ricerca di log che si usa sintassi sia corretta? 
- Dispone l'agente mai ha ricevuto la configurazione da monitoraggio di Azure?

Il primo passaggio nella risoluzione dei problemi è determinare se il computer sta inviando un evento heartbeat.

```
Heartbeat 
    | where Computer like "<ComputerName>"
    | summarize arg_max(TimeGenerated, * ) by Computer
```

Se la query restituisce risultati, è necessario determinare se un particolare tipo di dati non è raccolti e inoltrato al servizio. Ciò potrebbe essere causato dall'agente di ricezione non configurazione aggiornate dal servizio o un altro sintomo impedisce all'agente di funzionare. Eseguire i passaggi seguenti per ulteriori informazioni sulla risoluzione.

1. Aprire un prompt dei comandi con privilegi elevati nel computer e riavviare il servizio agent digitando `net stop healthservice && net start healthservice`.
2. Aprire il *Operations Manager* registro eventi e cercare **ID evento** *7023, 7024, 7025, 7028* e *1210* da **evento origine** *HealthService*.  Questi eventi indicano che l'agente sta ricevendo correttamente la configurazione da monitoraggio di Azure e sottoposte a monitoraggio attivamente il computer. La descrizione dell'evento per eventi 1210 di ID specificherà anche nell'ultima riga tutte le soluzioni e informazioni dettagliate che sono inclusi nell'ambito del monitoraggio dell'agente.  

    ![Descrizione dell'evento 1210 ID](./media/agent-windows-troubleshoot/event-id-1210-healthservice-01.png)

3. Se dopo alcuni minuti non è possibile visualizzare i dati previsti nei risultati della query o una visualizzazione, a seconda se si sta visualizzando i dati da una soluzione o l'analisi e dal *Operations Manager* registro eventi, cercare **evento origini** *HealthService* e *moduli del servizio integrità* e filtrare in base al **livello evento** *avviso* e *Errore* per verificare se la scrittura degli eventi dalla tabella seguente.

    |ID evento |`Source` |Descrizione |Risoluzione |
    |---------|-------|------------|
    |8000 |HealthService |Questo evento specificherà se un flusso di lavoro correlati alle prestazioni, eventi o un altro tipo di dati raccolti è in grado di inoltrare al servizio per l'inserimento nell'area di lavoro. | ID evento 2136 dall'origine HealthService è scritto con questo evento e può indicare l'agente non riesce a comunicare con il servizio, probabilmente a causa di errori di configurazione di impostazioni proxy e l'autenticazione, un'interruzione della rete o il firewall di rete / proxy non consente il traffico TCP dal computer al servizio.| 
    |10102 e 10103 |Moduli del servizio integrità |Flusso di lavoro non è riuscito a risolvere zdroj dat. |Ciò può verificarsi se il contatore delle prestazioni specificato o l'istanza non esiste nel computer o è definito in modo errato nelle impostazioni dell'area di lavoro dei dati. Se questo è specificato dall'utente [contatore delle prestazioni](data-sources-performance-counters.md#configuring-performance-counters), verificare le informazioni specificate di seguito sono illustrata nel formato corretto e che esista nel computer di destinazione. |
    |26002 |Moduli del servizio integrità |Flusso di lavoro non è riuscito a risolvere zdroj dat. |Ciò può verificarsi se il registro eventi di Windows specificato non esiste nel computer. Questo errore può essere ignorato se il computer non deve disporre questo registro eventi registrato, in caso contrario, se questo è specificato dall'utente [registro eventi](data-sources-windows-events.md#configuring-windows-event-logs), verificare le informazioni specificate siano corrette. |

