---
title: Raggruppare i computer usando le dipendenze dei computer con Azure Migrate | Microsoft Docs
description: Descrive come creare una valutazione usando le dipendenze dei computer con il servizio Azure Migrate.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 10/01/2019
ms.author: hamusa
ms.openlocfilehash: 1cd1ff83fd706e3474426f6cc2ac99d39e74dc22
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2019
ms.locfileid: "72177940"
---
# <a name="set-up-dependency-visualization-for-assessment"></a>Configurare la visualizzazione delle dipendenze per la valutazione

Questo articolo descrive come configurare il mapping delle dipendenze in Azure Migrate: server assessment.

Il mapping delle dipendenze consente di visualizzare le dipendenze tra i computer di cui si desidera eseguire la valutazione e la migrazione.

- In Azure Migrate: la valutazione del server raccoglie i computer per la valutazione. In genere i computer di cui si desidera eseguire la migrazione insieme.
- In genere si usa il mapping delle dipendenze quando si desidera valutare i gruppi con livelli di confidenza più elevati.
- Il mapping delle dipendenze consente di controllare in modo incrociato le dipendenze dei computer, prima di eseguire una valutazione e una migrazione.
- Il mapping e la visualizzazione delle dipendenze consentono di pianificare in modo efficace la migrazione ad Azure. Consente di garantire che non venga lasciato nulla, evitando così interruzioni di sorpresa durante la migrazione.
- Utilizzando il mapping è possibile individuare i sistemi interdipendenti di cui è necessario eseguire la migrazione insieme. È anche possibile determinare se un sistema in esecuzione sta ancora servendo gli utenti o se è un candidato per la rimozione delle autorizzazioni anziché per la migrazione.

[Altre](concepts-dependency-visualization.md#how-does-it-work) informazioni sulla visualizzazione delle dipendenze.

## <a name="before-you-start"></a>Prima di iniziare

- Assicurarsi di aver [creato](how-to-add-tool-first-time.md) un progetto Azure migrate.
- Se è già stato creato un progetto, verificare di aver [aggiunto](how-to-assess.md) lo strumento Azure migrate: server assessment.
- Assicurarsi di aver individuato i computer in Azure Migrate; a tale scopo, è possibile configurare un appliance Azure Migrate per [VMware](how-to-set-up-appliance-vmware.md) o [Hyper-V](how-to-set-up-appliance-hyper-v.md). L'appliance individua i computer locali e invia i metadati e i dati sulle prestazioni a Azure Migrate: server assessment. [Altre informazioni](migrate-appliance.md).


**Funzionalità** | **Nota**
--- | ---
Disponibilità | La visualizzazione delle dipendenze non è disponibile in Azure per enti pubblici.
Elenco dei servizi | La visualizzazione delle dipendenze USA Mapping dei servizi soluzione in monitoraggio di Azure. [Mapping dei servizi](../azure-monitor/insights/service-map.md) individua automaticamente e visualizza le connessioni tra i server.
Agents | Per usare la visualizzazione delle dipendenze, installare gli agenti seguenti nei computer di cui si vuole eseguire il mapping:<br/> - agente [agente log Analytics](../azure-monitor/platform/log-analytics-agent.md) (in precedenza definito Microsoft Monitoring Agent (MMA).<br/> - [mapping dei servizi Dependency Agent](../azure-monitor/insights/vminsights-enable-overview.md#the-microsoft-dependency-agent).<br/><br/> Per automatizzare l'installazione dell'agente, è possibile usare uno strumento di distribuzione come System Center Configuration Manager, che dispone di una soluzione di distribuzione dell'agente per Azure Migrate.
Dependency Agent | Esaminare il [supporto di Dependency Agent](../azure-monitor/insights/vminsights-enable-overview.md#the-microsoft-dependency-agent) per Windows e Linux.<br/><br/> [Altre](../azure-monitor/insights/vminsights-enable-hybrid-cloud.md#installation-script-examples) informazioni sull'uso degli script per installare Dependency Agent.
Agente di Log Analytics (MMA) | [Altre](../azure-monitor/platform/log-analytics-agent.md#install-and-configure-agent) informazioni sui metodi di installazione di MMA.<br/><br/> Per i computer monitorati da System Center Operations Manager 2012 R2 o versione successiva, non è necessario installare l'agente MMA. Mapping dei servizi si integra con Operations Manager. È possibile abilitare l'integrazione usando il materiale sussidiario disponibile [qui](https://docs.microsoft.com/azure/azure-monitor/insights/service-map-scom#prerequisites). Si noti, tuttavia, che l'agente di dipendenza dovrà essere installato in tali computer.<br/><br/> [Esaminare](../azure-monitor/platform/log-analytics-agent.md#supported-linux-operating-systems) i sistemi operativi Linux supportati dall'agente log Analytics.
Gruppi di valutazione | I gruppi per i quali si vuole visualizzare le dipendenze non devono includere più di 10 computer. Se sono presenti più di 10 computer, suddividerli in gruppi più piccoli per visualizzare le dipendenze.

## <a name="associate-a-log-analytics-workspace"></a>Associare un'area di lavoro Log Analytics

Per usare la visualizzazione delle dipendenze, è necessario associare un' [area di lavoro log Analytics](../azure-monitor/platform/manage-access.md) a un progetto Azure migrate.

- È possibile aggiungere un'area di lavoro solo nella sottoscrizione del progetto Azure Migrate.
- È possibile alleghi un'area di lavoro esistente o crearne una nuova.
- L'area di lavoro viene collegata la prima volta che si configura la visualizzazione delle dipendenze per un computer.
- È possibile aggiungere un'area di lavoro solo dopo l'individuazione di computer nel progetto Azure Migrate. A tale scopo, è possibile configurare un appliance Azure Migrate per [VMware](how-to-set-up-appliance-vmware.md) o [Hyper-V](how-to-set-up-appliance-hyper-v.md). L'appliance individua i computer locali e invia i metadati e i dati sulle prestazioni a Azure Migrate: server assessment. [Altre informazioni](migrate-appliance.md).

Alleghi un'area di lavoro come segue:

1. In **Azure migrate: server Assessment**, fare clic su **Panoramica**. Se lo strumento Server Assessment non è ancora stato aggiunto, [eseguire prima questa operazione](how-to-assess.md).
2. In **Panoramica**fare clic sulla freccia in giù per espandere **Essentials**.
3. Nell' **area di lavoro di OMS**fare clic su **richiede la configurazione**.
4. In **Configura area di lavoro**, specificare se si desidera creare una nuova area di lavoro oppure utilizzarne una esistente:

    ![Aggiungi area di lavoro](./media/how-to-create-group-machine-dependencies/workspace.png)

    - Dopo aver specificato un nome per una nuova area di lavoro, è possibile scegliere l' [area](https://azure.microsoft.com/global-infrastructure/regions/) in cui verrà creata l'area di lavoro.
    - Quando si collega un'area di lavoro esistente, è possibile sceglierla tra tutte le aree di lavoro disponibili nella stessa sottoscrizione del progetto di migrazione.
    - Per poter essere collegato, è necessario l'accesso in lettura all'area di lavoro.
    - Non è possibile modificare l'area di lavoro associata a un progetto dopo che è stata collegata.

## <a name="download-and-install-the-vm-agents"></a>Scaricare e installare gli agenti di macchine virtuali

Scaricare e installare gli agenti in ogni computer locale che si vuole visualizzare con il mapping delle dipendenze.

1. In **Azure migrate: server Assessment**fare clic su **server individuati**.
2. Per ogni computer per cui si vuole usare la visualizzazione delle dipendenze, fare clic su **richiede l'installazione dell'agente**.
3. Nella pagina **dipendenze** di un computer > **scaricare e installare MMA**, scaricare l'agente appropriato e installarlo come descritto di seguito.
4. In **scaricare e installare Dependency Agent**scaricare l'agente appropriato e installarlo come descritto di seguito.
5. In **Configura agente MMA**copiare l'ID e la chiave dell'area di lavoro. Sono necessari quando si installa l'agente MMA.

### <a name="install-the-mma"></a>Installare MMA

#### <a name="install-the-agent-on-a-windows-machine"></a>Installare l'agente in un computer Windows

Per installare l'agente in un computer Windows:

1. Fare doppio clic sull'agente scaricato.
2. Nella pagina di **benvenuto** fare clic su **Avanti**. Nella pagina **Condizioni di licenza** fare clic su **Accetto** per accettare la licenza.
3. In **Cartella di destinazione** mantenere o modificare la cartella di installazione predefinita e quindi fare clic su **Avanti**.
4. In **Opzioni di installazione dell'agente** selezionare **Azure Log Analytics** > **Avanti**.
5. Fare clic su **Aggiungi** per aggiungere una nuova area di lavoro Log Analytics. Incollare l'ID e la chiave dell'area di lavoro copiati dal portale. Fare clic su **Next** (Avanti).

È possibile installare l'agente dalla riga di comando o usando un metodo automatizzato, ad esempio System Center Configuration Manager o [Intigua](https://go.microsoft.com/fwlink/?linkid=2104196). [Altre informazioni](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent#install-and-configure-agent) sull'uso di questi metodi per installare l'agente MMA. L'agente MMA può essere installato anche usando questo [script](https://go.microsoft.com/fwlink/?linkid=2104394).

[Altre](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent#supported-windows-operating-systems) informazioni sui sistemi operativi Windows supportati da MMA.

#### <a name="install-the-agent-on-a-linux-machine"></a>Installare l'agente in un computer Linux

Per installare l'agente in un computer Linux:

1. Trasferire il bundle appropriato (x86 o x64) nel computer Linux mediante scp/sftp.
2. Installare il bundle usando l'argomento --install.

    ```sudo sh ./omsagent-<version>.universal.x64.sh --install -w <workspace id> -s <workspace key>```

[Altre informazioni](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent#supported-linux-operating-systems) sull'elenco dei sistemi operativi Linux supportati da MMA. 

### <a name="install-the-dependency-agent"></a>Installare Dependency Agent
1. Per installare Dependency Agent in un computer Windows, fare doppio clic sul file di installazione e seguire la procedura guidata.
2. Per installare Dependency Agent in un computer Linux, procedere all'installazione come utente ROOT usando il comando seguente:

    ```sh InstallDependencyAgent-Linux64.bin```

[Altre informazioni](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-enable-hybrid-cloud#installation-script-examples) sul modo in cui è possibile usare gli script per installare l'agente di dipendenza.

[Altre](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-enable-overview#supported-operating-systems) informazioni sui sistemi operativi supportati da Dependency Agent.


## <a name="create-a-group-using-dependency-visualization"></a>Creare un gruppo usando la visualizzazione delle dipendenze

1. In **Azure migrate: server Assessment**fare clic su **server individuati**.
2. Nella colonna **dipendenze** fare clic su **Visualizza dipendenze** per ogni computer che si desidera esaminare.
3. Nella mappa delle dipendenze è possibile vedere quanto segue:
    - Connessioni TCP in ingresso (client) e in uscita (Server), da e verso il computer.
    - I computer dipendenti in cui non sono installati gli agenti di dipendenza sono raggruppati in base ai numeri di porta.
    - I computer dipendenti con agenti di dipendenza installati vengono visualizzati come caselle separate.
    - Processi in esecuzione all'interno del computer. Espandere ogni casella del computer per visualizzare i processi.
    - Proprietà del computer (incluso FQDN, sistema operativo, indirizzo MAC). Fare clic su ogni casella del computer per visualizzare i dettagli.

4. È possibile esaminare le dipendenze per intervalli di tempo diversi facendo clic sulla durata nell'etichetta dell'intervallo di tempo. Per impostazione predefinita, l'intervallo è un'ora. È possibile modificare l'intervallo di tempo oppure specificare le date di inizio e fine e la durata.

    > [!NOTE]
    > L'intervallo di tempo può essere fino a un'ora. Se è necessario un intervallo più lungo, usare monitoraggio di Azure per eseguire query sui dati dipendenti per un periodo di tempo più lungo.

5. Dopo aver identificato i computer dipendenti che si desidera raggruppare, premere CTRL + clic per selezionare più computer nella mappa e fare clic su **raggruppa computer**.
6. Specificare un nome di gruppo.
7. Verificare che i computer dipendenti siano stati individuati da Azure Migrate.

    - Se un computer dipendente non viene individuato da Azure Migrate: server Assessment, non è possibile aggiungerlo al gruppo.
    - Per aggiungere un computer, eseguire di nuovo l'individuazione e verificare che il computer sia stato individuato.

8. Se si vuole creare una valutazione per questo gruppo, selezionare la casella di controllo per creare una nuova valutazione per il gruppo.
8. Fare clic su **OK** per salvare il gruppo.

Dopo aver creato il gruppo, è consigliabile installare gli agenti in tutti i computer del gruppo e quindi visualizzare le dipendenze per l'intero gruppo.

## <a name="query-dependency-data-in-azure-monitor"></a>Eseguire query sui dati delle dipendenze in monitoraggio di Azure

È possibile eseguire query sui dati delle dipendenze acquisiti da Mapping dei servizi nell'area di lavoro Log Analytics associata al progetto Azure Migrate. Log Analytics viene usato per scrivere ed eseguire query di log di monitoraggio di Azure.

- [Informazioni su come](../azure-monitor/insights/service-map.md#log-analytics-records) cercare i dati Mapping dei servizi in log Analytics.
- [Ottenere una panoramica sulla](../azure-monitor/log-query/get-started-queries.md) scrittura di query di log in [log Analytics](../azure-monitor/log-query/get-started-portal.md).

Eseguire una query per i dati di dipendenza come segue:

1. Dopo aver installato gli agenti, accedere al portale e fare clic su **Panoramica**.
2. In **Azure migrate: server Assessment**, fare clic su **Panoramica**. Per espandere **Essentials**, fare clic sulla freccia verso il basso.
3. Nell' **area di lavoro di OMS**fare clic sul nome dell'area di lavoro.
3. Nella pagina Log Analytics area di lavoro > **generale**, fare clic su **log**.
4. Scrivere la query e fare clic su **Esegui**.

### <a name="sample-queries"></a>Query di esempio

Sono disponibili un numero di query di esempio che è possibile usare per estrarre i dati delle dipendenze.

- È possibile modificare le query per estrarre i punti dati preferiti.
- [Esaminare](https://docs.microsoft.com/azure/azure-monitor/insights/service-map#log-analytics-records) un elenco completo dei record dei dati delle dipendenze.
- [Esaminare](https://docs.microsoft.com/azure/azure-monitor/insights/service-map#sample-log-searches) le query di esempio aggiuntive.

#### <a name="sample-review-inbound-connections"></a>Esempio: esaminare le connessioni in ingresso

Verificare le connessioni in ingresso per un set di macchine virtuali.

- I record della tabella per le metriche di connessione (VMConnection) non rappresentano le singole connessioni di rete fisica.
- Più connessioni di rete fisiche vengono raggruppate in una connessione logica.
- [Altre](https://docs.microsoft.com/azure/azure-monitor/insights/service-map#connections) informazioni sul modo in cui i dati della connessione di rete fisica sono aggregati in VMConnection.

```
// the machines of interest
let ips=materialize(ServiceMapComputer_CL
| summarize ips=makeset(todynamic(Ipv4Addresses_s)) by MonitoredMachine=ResourceName_s
| mvexpand ips to typeof(string));
let StartDateTime = datetime(2019-03-25T00:00:00Z);
let EndDateTime = datetime(2019-03-30T01:00:00Z);
VMConnection
| where Direction == 'inbound'
| where TimeGenerated > StartDateTime and TimeGenerated  < EndDateTime
| join kind=inner (ips) on $left.DestinationIp == $right.ips
| summarize sum(LinksEstablished) by Computer, Direction, SourceIp, DestinationIp, DestinationPort
```

#### <a name="sample-summarize-sent-and-received-data"></a>Esempio: riepilogare i dati inviati e ricevuti

In questo esempio viene riepilogato il volume di dati inviati e ricevuti per le connessioni in ingresso tra un set di computer.

```
// the machines of interest
let ips=materialize(ServiceMapComputer_CL
| summarize ips=makeset(todynamic(Ipv4Addresses_s)) by MonitoredMachine=ResourceName_s
| mvexpand ips to typeof(string));
let StartDateTime = datetime(2019-03-25T00:00:00Z);
let EndDateTime = datetime(2019-03-30T01:00:00Z);
VMConnection
| where Direction == 'inbound'
| where TimeGenerated > StartDateTime and TimeGenerated  < EndDateTime
| join kind=inner (ips) on $left.DestinationIp == $right.ips
| summarize sum(BytesSent), sum(BytesReceived) by Computer, Direction, SourceIp, DestinationIp, DestinationPort
```

## <a name="next-steps"></a>Passaggi successivi

[Creare una valutazione](how-to-create-assessment.md) per un gruppo.
