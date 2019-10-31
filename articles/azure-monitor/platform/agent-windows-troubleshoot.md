---
title: Risolvere i problemi relativi a Log Analytics Agent per Windows
description: Descrivere i sintomi, le cause e la risoluzione dei problemi più comuni con l'agente di Log Analytics per Windows in monitoraggio di Azure.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: MGoedtel
ms.author: magoedte
ms.date: 06/12/2019
ms.openlocfilehash: 7bf0c8429eaecd6cba83872cbea5876cc0c31221
ms.sourcegitcommit: fa5ce8924930f56bcac17f6c2a359c1a5b9660c9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/31/2019
ms.locfileid: "73199018"
---
# <a name="how-to-troubleshoot-issues-with-the-log-analytics-agent-for-windows"></a>Come risolvere i problemi relativi all'agente Log Analytics per Windows 

Questo articolo fornisce informazioni sulla risoluzione degli errori che possono verificarsi con l'agente di Log Analytics per Windows in monitoraggio di Azure e suggerisce le possibili soluzioni per risolverle.

Se nessuno dei passaggi descritti risulta adatto alle proprie esigenze, sono disponibili anche i canali di supporto seguenti:

* I clienti che usufruiscono dei vantaggi del supporto tecnico Premier possono aprire una richiesta di supporto con [Premier](https://premier.microsoft.com/).
* I clienti con un contratto di supporto tecnico di Azure possono aprire una richiesta di supporto nel [portale di Azure](https://manage.windowsazure.com/?getsupport=true).
* Visitare la pagina del feedback di Log Analytics per esaminare i bug e i suggerimenti inviati, all'indirizzo [https://aka.ms/opinsightsfeedback](https://aka.ms/opinsightsfeedback), o segnalarne di nuovi. 

## <a name="important-troubleshooting-sources"></a>Principali origini per la risoluzione dei problemi

 Per semplificare la risoluzione dei problemi relativi a Log Analytics Agent per Windows, l'agente registra gli eventi nel registro eventi di Windows, in particolare in *Application e Services\Operations Manager*.  

## <a name="connectivity-issues"></a>Problemi di connettività

Se l'agente comunica tramite un server proxy o un firewall, è possibile che si verifichino restrizioni che impediscono la comunicazione tra il computer di origine e il servizio monitoraggio di Azure. Se la comunicazione è bloccata, a causa di una configurazione errata, la registrazione con un'area di lavoro potrebbe non riuscire durante il tentativo di installare l'agente o configurare l'agente dopo l'installazione per creare un report in un'area di lavoro aggiuntiva. La comunicazione dell'agente potrebbe non riuscire dopo la registrazione. In questa sezione vengono descritti i metodi per risolvere questo tipo di problema con l'agente di Windows.

Verificare che il firewall o il proxy sia configurato per consentire le porte e gli URL seguenti descritti nella tabella seguente. Verificare inoltre che l'ispezione HTTP non sia abilitata per il traffico Web, in quanto può impedire un canale TLS sicuro tra l'agente e il monitoraggio di Azure.  

|Risorsa agente|Porte |Direzione |Ignorare l'analisi HTTPS|
|------|---------|--------|--------|   
|*.ods.opinsights.azure.com |Porta 443 |In uscita|SÌ |  
|*.oms.opinsights.azure.com |Porta 443 |In uscita|SÌ |  
|*.blob.core.windows.net |Porta 443 |In uscita|SÌ |  
|*.azure-automation.net |Porta 443 |In uscita|SÌ |  

Per informazioni sul firewall richieste per Azure per enti pubblici, vedere [gestione di Azure per enti pubblici](../../azure-government/documentation-government-services-monitoringandmanagement.md#azure-monitor-logs). 

Esistono diversi modi per verificare se l'agente è in grado di comunicare correttamente con monitoraggio di Azure.

- Abilitare la [valutazione integrità agente di Azure log Analytics](../insights/solution-agenthealth.md) nell'area di lavoro. Dal dashboard Integrità agente visualizzare il **numero di agenti che non rispondono** per verificare rapidamente se l'agente è elencato.  

- Eseguire la query seguente per confermare che l'agente sta inviando un heartbeat all'area di lavoro a cui è configurata la segnalazione. Sostituire `<ComputerName>` con il nome effettivo del computer.

    ```
    Heartbeat 
    | where Computer like "<ComputerName>"
    | summarize arg_max(TimeGenerated, * ) by Computer 
    ```

    Se il computer comunica correttamente con il servizio, la query deve restituire un risultato. Se la query non ha restituito un risultato, verificare prima di tutto che l'agente sia configurato per la segnalazione all'area di lavoro corretta. Se la configurazione è corretta, procedere al passaggio 3 e cercare nel registro eventi di Windows se l'agente sta registrando il problema che potrebbe impedire la comunicazione con monitoraggio di Azure.

- Un altro metodo per identificare un problema di connettività consiste nell'eseguire lo strumento **TestCloudConnectivity** . Per impostazione predefinita, lo strumento viene installato con l'agente nella cartella *%systemroot%\Programmi\Microsoft Files\Microsoft Monitoring Agent\Agent*. Da un prompt dei comandi con privilegi elevati, passare alla cartella ed eseguire lo strumento. Lo strumento restituisce i risultati e le evidenziazioni in cui il test ha avuto esito negativo, ad esempio se era correlato a una porta/URL particolare bloccato. 

    ![Risultati dell'esecuzione dello strumento TestCloudConnection](./media/agent-windows-troubleshoot/output-testcloudconnection-tool-01.png)

- Filtrare il registro eventi *Operations Manager* per **origini evento** - *servizio integrità Modules*, *HealthService*e *Service Connector* e filtrare per *avviso* a **livello di evento** ed *errore* in verificare se sono stati scritti eventi dalla tabella seguente. In caso affermativo, rivedere i passaggi di risoluzione inclusi per ogni possibile evento.

    |ID evento |Source (Sorgente) |Description |Risoluzione |
    |---------|-------|------------|-----------|
    |2133 & 2129 |Servizio integrità |Connessione al servizio dall'agente non riuscita |Questo errore può verificarsi quando l'agente non è in grado di comunicare direttamente o tramite un firewall o un server proxy per il servizio monitoraggio di Azure. Verificare le impostazioni del proxy di Agent o che il firewall/proxy di rete consenta il traffico TCP dal computer al servizio.|
    |2138 |Moduli Servizio integrità |Il proxy richiede l'autenticazione |Configurare le impostazioni proxy di Agent e specificare il nome utente e la password necessari per l'autenticazione con il server proxy. |
    |2129 |Moduli Servizio integrità |Connessione non riuscita/negoziazione SSL non riuscita |Controllare le impostazioni TCP/IP della scheda di rete e le impostazioni del proxy di Agent.|
    |2127 |Moduli Servizio integrità |Errore durante l'invio di dati. codice di errore ricevuto |Se si verifica solo periodicamente durante il giorno, potrebbe essere semplicemente un'anomalia casuale che può essere ignorata. Monitorare per comprendere la frequenza con cui si verifica. Se si verifica spesso nel corso della giornata, verificare prima di tutto la configurazione di rete e le impostazioni del proxy. Se la descrizione include il codice di errore HTTP 404 ed è la prima volta che l'agente tenta di inviare dati al servizio, verrà incluso un errore 500 con un codice di errore 404 interno. 404 indica che non è stato trovato, che indica che è ancora in corso il provisioning dell'area di archiviazione per la nuova area di lavoro. Al successivo tentativo, i dati vengono scritti correttamente nell'area di lavoro come previsto. Un errore HTTP 403 potrebbe indicare un problema di autorizzazione o di credenziali. Per risolvere il problema, sono disponibili altre informazioni con l'errore 403.|
    |4000 |Connettore di servizi |Risoluzione del nome DNS non riuscita |Il computer non è riuscito a risolvere l'indirizzo Internet usato durante l'invio di dati al servizio. Potrebbe trattarsi di impostazioni del resolver DNS nel computer, impostazioni proxy non corrette o problemi DNS temporanei con il provider. Se il problema si verifica periodicamente, potrebbe essere causato da un problema temporaneo relativo alla rete.|
    |4001 |Connettore di servizi |Connessione al servizio non riuscita. |Questo errore può verificarsi quando l'agente non è in grado di comunicare direttamente o tramite un firewall o un server proxy per il servizio monitoraggio di Azure. Verificare le impostazioni del proxy di Agent o che il firewall/proxy di rete consenta il traffico TCP dal computer al servizio.|
    |4002 |Connettore di servizi |Il servizio ha restituito il codice di stato HTTP 403 in risposta a una query. Rivolgersi all'amministratore del servizio per l'integrità del servizio. La query verrà ritentata in un secondo momento. |Questo errore viene scritto durante la fase di registrazione iniziale dell'agente. verrà visualizzato un URL simile al seguente: *https://\<workspaceID >. OMS. opinsights. Azure. com/agentservice. svc/AgentTopologyRequest*. Il codice di errore 403 indica non consentito e può essere causato da un ID o una chiave dell'area di lavoro tipizzata in modo errato oppure che i dati e l'ora non sono corretti nel computer. Se l'ora è sfasata di +/- 15 minuti rispetto all'ora corrente, l'onboarding ha esito negativo. Per risolvere il problema, aggiornare la data e/o il fuso orario del computer Windows.|

## <a name="data-collection-issues"></a>Problemi di raccolta dati

Dopo l'installazione dell'agente e l'esecuzione di report per l'area di lavoro o le aree di lavoro configurate, è possibile che la ricezione della configurazione, della raccolta o dell'invio delle prestazioni, dei log o di altri dati al servizio venga interrotta a seconda di ciò che è abilitato e come destinazione del computer. È necessario determinare se:

- Si tratta di un tipo di dati specifico o di tutti i dati non disponibili nell'area di lavoro?
- Tipo di dati specificato da una soluzione o specificato come parte della configurazione della raccolta dei dati dell'area di lavoro?
- Quanti computer sono interessati? Si tratta di uno o più computer che inviano report all'area di lavoro?
- Funzionava e si è arrestato a una determinata ora del giorno oppure non è mai stato raccolto? 
- La query di ricerca nei log utilizzata è sintatticamente corretta? 
- L'agente ha ricevuto la configurazione da monitoraggio di Azure?

Il primo passaggio nella risoluzione dei problemi consiste nel determinare se il computer sta inviando un evento Heartbeat.

```
Heartbeat 
    | where Computer like "<ComputerName>"
    | summarize arg_max(TimeGenerated, * ) by Computer
```

Se la query restituisce risultati, è necessario determinare se un particolare tipo di dati non viene raccolto e inviato al servizio. Questo problema potrebbe essere causato dall'agente che non riceve la configurazione aggiornata dal servizio o da un altro sintomo che impedisce il funzionamento normale dell'agente. Per ulteriori risoluzione dei problemi, attenersi alla procedura riportata di seguito.

1. Aprire un prompt dei comandi con privilegi elevati nel computer e riavviare il servizio Agent digitando `net stop healthservice && net start healthservice`.
2. Aprire il registro eventi *Operations Manager* e cercare gli **ID evento** *7023, 7024, 7025, 7028* e *1210* dall' **origine evento** *HealthService*.  Questi eventi indicano che l'agente riceve correttamente la configurazione da monitoraggio di Azure e che sta monitorando attivamente il computer. La descrizione dell'evento dell'ID evento 1210 specifica anche sull'ultima riga tutte le soluzioni e informazioni incluse nell'ambito del monitoraggio sull'agente.  

    ![Descrizione ID evento 1210](./media/agent-windows-troubleshoot/event-id-1210-healthservice-01.png)

3. Se dopo alcuni minuti i dati previsti non vengono visualizzati nei risultati della query o nella visualizzazione, a seconda che si stiano visualizzando i dati da una soluzione o da informazioni dettagliate, dal registro eventi *Operations Manager* cercare **origini evento** *HealthService* e *servizio integrità moduli* e filtrano in base al **livello di evento** *warning* ed *Error* per verificare se sono stati scritti eventi dalla tabella seguente.

    |ID evento |Source (Sorgente) |Description |Risoluzione |
    |---------|-------|------------|
    |8000 |HealthService |Questo evento specifica se un flusso di lavoro relativo a prestazioni, eventi o altri tipi di dati raccolti non è in grado di eseguire l'invio al servizio per l'inserimento nell'area di lavoro. | L'ID evento 2136 dall'origine HealthService viene scritto insieme a questo evento e può indicare che l'agente non è in grado di comunicare con il servizio, probabilmente a causa di una configurazione errata del proxy e delle impostazioni di autenticazione, dell'interruzione della rete o del firewall di rete/ il proxy non consente il traffico TCP dal computer al servizio.| 
    |10102 e 10103 |Moduli Servizio integrità |Impossibile risolvere l'origine dati. |Questo problema può verificarsi se l'istanza o il contatore delle prestazioni specificato non esiste nel computer o non è definito correttamente nelle impostazioni dei dati dell'area di lavoro. Se si tratta di un [contatore delle prestazioni](data-sources-performance-counters.md#configuring-performance-counters)specificato dall'utente, verificare che le informazioni specificate siano successive al formato corretto e che esista nei computer di destinazione. |
    |26002 |Moduli Servizio integrità |Impossibile risolvere l'origine dati. |Questo problema può verificarsi se il registro eventi di Windows specificato non esiste nel computer. Questo errore può essere ignorato se nel computer non è previsto che il registro eventi sia registrato. in caso contrario, se si tratta di un [registro eventi](data-sources-windows-events.md#configuring-windows-event-logs)specificato dall'utente, verificare che le informazioni specificate siano corrette. |

