---
title: Risolvere i problemi con l'agente di Log Analytics per WindowsTroubleshoot issues with Log Analytics agent for Windows
description: Descrivere i sintomi, le cause e la risoluzione per i problemi più comuni con l'agente Log Analytics per Windows in Monitoraggio di Azure.Describe the symptoms, causes, and resolution for the most common issues with the Log Analytics agent for Windows in Azure Monitor.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/21/2019
ms.openlocfilehash: 4112555347ce1d718375fbab3f166c6f2f5deeaa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80333516"
---
# <a name="how-to-troubleshoot-issues-with-the-log-analytics-agent-for-windows"></a>Come risolvere i problemi relativi all'agente di Log Analytics per Windows 

Questo articolo fornisce assistenza per la risoluzione degli errori che potrebbero verificarsi con l'agente Log Analytics per Windows in Monitoraggio di Azure e suggerisce possibili soluzioni per risolverli.

Se nessuno dei passaggi descritti risulta adatto alle proprie esigenze, sono disponibili anche i canali di supporto seguenti:

* I clienti che usufruiscono dei vantaggi del supporto tecnico Premier possono aprire una richiesta di supporto con [Premier](https://premier.microsoft.com/).
* I clienti con un contratto di supporto tecnico di Azure possono aprire una richiesta di supporto nel [portale di Azure](https://manage.windowsazure.com/?getsupport=true).
* Visita la pagina Feedback di Log [https://aka.ms/opinsightsfeedback](https://aka.ms/opinsightsfeedback) Analytics per esaminare le idee e i bug inviati o presentarne uno nuovo. 

## <a name="important-troubleshooting-sources"></a>Importanti fonti per la risoluzione dei problemi

 Per facilitare la risoluzione dei problemi relativi all'agente di Log Analytics per Windows, l'agente registra gli eventi nel registro eventi di Windows, in particolare in *Applicazioni e servizi , Operations Manager*.  

## <a name="connectivity-issues"></a>Problemi di connettività

Se l'agente comunica tramite un server proxy o un firewall, potrebbero esserci restrizioni che impediscono la comunicazione dal computer di origine e dal servizio Monitor di Azure.If the agent is communicating through a proxy server or firewall, there may be restrictions in place preventing communication from the source computer and the Azure Monitor service. Nel caso in cui la comunicazione sia bloccata, a causa di una configurazione errata, la registrazione con un'area di lavoro potrebbe non riuscire durante il tentativo di installare l'agente o configurare il post-installazione dell'agente per il report in un'area di lavoro aggiuntiva. La comunicazione dell'agente potrebbe non riuscire dopo la corretta registrazione. In questa sezione vengono descritti i metodi per risolvere questo tipo di problema con l'agente di Windows.

Verificare che il firewall o il proxy sia configurato per consentire le porte e gli URL descritti nella tabella seguente. Verificare inoltre che l'ispezione HTTP non sia abilitata per il traffico Web, in quanto può impedire un canale TLS sicuro tra l'agente e Monitoraggio di Azure.Also confirm HTTP inspection is not enabled for web traffic, as it can prevent a secure TLS channel between the agent and Azure Monitor.  

|Risorsa agente|Porte |Direction |Ignorare l'analisi HTTPS|
|------|---------|--------|--------|   
|*.ods.opinsights.azure.com |Porta 443 |In uscita|Sì |  
|*.oms.opinsights.azure.com |Porta 443 |In uscita|Sì |  
|*.blob.core.windows.net |Porta 443 |In uscita|Sì |  

Per informazioni sul firewall necessarie per Azure per enti pubblici, vedere [Gestione di Azure per enti](../../azure-government/documentation-government-services-monitoringandmanagement.md#azure-monitor-logs)pubblici. Se si prevede di usare Azure Automation Hybrid Runbook Worker per connettersi e registrarsi con il servizio di automazione per usare runbook o soluzioni di gestione nell'ambiente, deve avere accesso al numero di porta e agli URL descritti in [Configurare la rete per Hybrid Runbook Worker](../../automation/automation-hybrid-runbook-worker.md#network-planning). 

Esistono diversi modi per verificare se l'agente comunica correttamente con Monitoraggio di Azure.There are several ways you can verify if the agent is successfully communicating with Azure Monitor.

- Abilitare la [valutazione dell'integrità dell'agente](../insights/solution-agenthealth.md) di Azure Log Analytics nell'area di lavoro. Dal dashboard Integrità agente visualizzare la colonna Conteggio degli agenti che non **rispondono** per verificare rapidamente se l'agente è elencato.  

- Eseguire la query seguente per verificare che l'agente stia inviando un heartbeat all'area di lavoro a cui è configurato per la creazione di report. Sostituire `<ComputerName>` con il nome effettivo della macchina.

    ```
    Heartbeat 
    | where Computer like "<ComputerName>"
    | summarize arg_max(TimeGenerated, * ) by Computer 
    ```

    Se il computer comunica correttamente con il servizio, la query deve restituire un risultato. Se la query non ha restituito un risultato, verificare innanzitutto che l'agente sia configurato per la segnalazione nell'area di lavoro corretta. Se è configurato correttamente, procedere al passaggio 3 e cercare nel registro eventi di Windows per identificare se l'agente sta registrando il problema che potrebbe impedirgli di comunicare con Monitoraggio di Azure.If it is configured correctly, proceed to step 3 and search the Windows Event Log to identify if the agent is logging what issue might preventing it from communicating with Azure Monitor.

- Un altro metodo per identificare un problema di connettività consiste nell'eseguire lo strumento **TestCloudConnectivity.Another** method to identify a connectivity issue is by running the TestCloudConnectivity tool. Lo strumento viene installato per impostazione predefinita con l'agente nella cartella *%SystemRoot%*. Da un prompt dei comandi con privilegi elevati, passare alla cartella ed eseguire lo strumento. Lo strumento restituisce i risultati ed evidenzia dove il test non è riuscito (ad esempio, se è correlato a una determinata porta/URL che è stato bloccato). 

    ![Risultati dell'esecuzione dello strumento TestCloudConnection](./media/agent-windows-troubleshoot/output-testcloudconnection-tool-01.png)

- Filtrare il registro eventi di *Operations Manager* in base alle **origini eventi** - *Moduli del servizio*di integrità , *HealthService*e *Connettore servizio* e filtrare in base *a Event* **Level** Warning ed *Error* per verificare se sono presenti eventi scritti dalla tabella seguente. In caso affermativo, esaminare i passaggi di risoluzione inclusi per ogni possibile evento.

    |ID evento |Source (Sorgente) |Descrizione |Risoluzione |
    |---------|-------|------------|-----------|
    |2133 & 2129 |Servizio integrità |Connessione al servizio dall'agente non riuscita |Questo errore può verificarsi quando l'agente non è in grado di comunicare direttamente o tramite un server firewall/proxy al servizio Monitor di Azure.This error can occur when the agent cannot communicate directly or through a firewall/proxy server to the Azure Monitor service. Verificare le impostazioni proxy dell'agente o che il firewall/proxy di rete consenta il traffico TCP dal computer al servizio.|
    |2138 |Moduli del servizio integrità |Il proxy richiede l'autenticazione |Configurare le impostazioni proxy dell'agente e specificare il nome utente/password necessario per l'autenticazione con il server proxy. |
    |2129 |Moduli del servizio integrità |Connessione non riuscita/Negoziazione TLS non riuscita |Controllare le impostazioni TCP/IP della scheda di rete e le impostazioni proxy dell'agente.|
    |2127 |Moduli del servizio integrità |Errore durante l'invio dei dati ricevuto codice di errore |Se si verifica solo periodicamente durante il giorno, potrebbe essere solo un'anomalia casuale che può essere ignorata. Monitorare per capire quanto spesso accade. Se accade spesso durante il giorno, controllare prima la configurazione di rete e le impostazioni proxy. Se la descrizione include il codice di errore HTTP 404 ed è la prima volta che l'agente tenta di inviare dati al servizio, includerà un errore 500 con un codice di errore 404 interno. 404 significa non trovato, il che indica che è ancora in fase di provisioning l'area di archiviazione per la nuova area di lavoro. Al successivo tentativo, i dati verranno scritti correttamente nell'area di lavoro come previsto. Un errore HTTP 403 potrebbe indicare un problema di autorizzazione o credenziali. Sono disponibili ulteriori informazioni con l'errore 403 per risolvere il problema.|
    |4000 |Connettore di servizio |Risoluzione dei nomi DNS non riuscita |La macchina non è in grado di risolvere l'indirizzo Internet utilizzato durante l'invio di dati al servizio. Potrebbe trattarsi di impostazioni del resolver DNS nel computer, impostazioni proxy non corrette o forse di un problema DNS temporaneo con il provider. Se si verifica periodicamente, potrebbe essere causato da un problema temporaneo relativo alla rete.|
    |4001 |Connettore di servizio |Connessione al servizio non riuscita. |Questo errore può verificarsi quando l'agente non è in grado di comunicare direttamente o tramite un server firewall/proxy al servizio Monitor di Azure.This error can occur when the agent cannot communicate directly or through a firewall/proxy server to the Azure Monitor service. Verificare le impostazioni proxy dell'agente o che il firewall/proxy di rete consenta il traffico TCP dal computer al servizio.|
    |4002 |Connettore di servizio |Il servizio ha restituito il codice di stato HTTP 403 in risposta a una query. Rivolgersi all'amministratore del servizio per verificare l'integrità del servizio. L'esecuzione della query verrà tentata di nuovo in seguito. |Questo errore viene scritto durante la fase di registrazione iniziale dell'agente e verrà visualizzato un URL simile al seguente: *https://\<workspaceID>.oms.opinsights.azure.com/AgentService.svc/AgentTopologyRequest*. Un codice di errore 403 significa proibito e può essere causato da un ID o una chiave dell'area di lavoro digitati in modo errato oppure i dati e l'ora non sono corretti nel computer. Se l'ora è sfasata di +/- 15 minuti rispetto all'ora corrente, l'onboarding ha esito negativo. Per risolvere il problema, aggiornare la data e/o il fuso orario del computer Windows.|

## <a name="data-collection-issues"></a>Problemi di raccolta dei dati

Dopo l'installazione dell'agente e i report nell'area di lavoro o nelle aree di lavoro configurate, è possibile che si verifichi nozioni di configurazione, raccolta o inoltro di prestazioni, log o altri dati al servizio a seconda dell'abilitazione e della destinazione del computer. È necessario determinare se:

- Si tratta di un particolare tipo di dati o di tutti i dati non disponibili nell'area di lavoro?
- Il tipo di dati specificato da una soluzione o come parte della configurazione della raccolta dati dell'area di lavoro?
- Quanti computer sono interessati? Si tratta di uno o più computer che segnalano all'area di lavoro?
- Stava funzionando e si è fermato a un particolare momento della giornata, o non è mai stato raccolto? 
- La query di ricerca log in uso è corretta dal livello sintattico? 
- L'agente ha mai ricevuto la configurazione da Monitoraggio di Azure?

Il primo passaggio nella risoluzione dei problemi consiste nel determinare se il computer invia un evento heartbeat.

```
Heartbeat 
    | where Computer like "<ComputerName>"
    | summarize arg_max(TimeGenerated, * ) by Computer
```

Se la query restituisce risultati, è necessario determinare se un particolare tipo di dati non viene raccolto e inoltrato al servizio. Ciò potrebbe essere causato dal fatto che l'agente non riceve la configurazione aggiornata dal servizio o da un altro sintomo che impedisce all'agente di funzionare normalmente. Eseguire la procedura seguente per risolvere ulteriormente la risoluzione dei problemi.

1. Aprire un prompt dei comandi con privilegi elevati `net stop healthservice && net start healthservice`nel computer e riavviare il servizio agente digitando .
2. Aprire il registro eventi di *Operations Manager* e cercare gli **ID evento** *7023, 7024, 7025, 7028* e *1210* dall'origine **evento** *HealthService*.  Questi eventi indicano che l'agente sta ricevendo correttamente la configurazione da Monitoraggio di Azure e che stanno monitorando attivamente il computer. La descrizione dell'evento per l'ID evento 1210 specificherà anche nell'ultima riga tutte le soluzioni e insights incluse nell'ambito del monitoraggio nell'agente.  

    ![ID evento 1210 descrizione](./media/agent-windows-troubleshoot/event-id-1210-healthservice-01.png)

3. Se dopo alcuni minuti non vengono visualizzati i dati previsti nei risultati o nella visualizzazione della query, a seconda che si stiano visualizzando i dati da una soluzione o da un'analisi, dal registro eventi di *Operations Manager* cercare **Origini eventi** *HealthService* e *I moduli del servizio integrità* e filtrare in base a *Avviso* e *errore* a livello di **evento** per verificare se sono presenti eventi scritti dalla tabella seguente.

    |ID evento |Source (Sorgente) |Descrizione |Risoluzione |
    |---------|-------|------------|
    |8000 |Servizio integrità |Questo evento consente di specificare se un flusso di lavoro correlato a prestazioni, eventi o altro tipo di dati raccolti non è in grado di inoltrare al servizio per l'inserimento nell'area di lavoro. | L'ID evento 2136 dall'origine HealthService viene scritto insieme a questo evento e può indicare che l'agente non è in grado di comunicare con il servizio, probabilmente a causa di una configurazione errata delle impostazioni proxy e di autenticazione, dell'interruzione della rete o del firewall/proxy di rete non consente il traffico TCP dal computer al servizio.| 
    |10102 e 10103 |Moduli del servizio integrità |Il flusso di lavoro non è riuscito a risolvere l'origine dati. |Ciò può verificarsi se il contatore delle prestazioni o l'istanza specificata non esiste nel computer o è definita in modo non corretto nelle impostazioni dei dati dell'area di lavoro. Se si tratta di un [contatore delle prestazioni](data-sources-performance-counters.md#configuring-performance-counters)specificato dall'utente, verificare che le informazioni specificate seguano il formato corretto ed esistano nei computer di destinazione. |
    |26002 |Moduli del servizio integrità |Il flusso di lavoro non è riuscito a risolvere l'origine dati. |Ciò può verificarsi se il registro eventi di Windows specificato non esiste nel computer. Questo errore può essere tranquillamente ignorato se non è previsto che nel computer sia registrato questo registro eventi, altrimenti se si tratta di un [registro eventi](data-sources-windows-events.md#configuring-windows-event-logs)specificato dall'utente, verificare che le informazioni specificate siano corrette. |

