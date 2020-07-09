---
title: Configurare l'analisi delle dipendenze basate su agente in Azure Migrate server Assessment
description: Questo articolo descrive come configurare l'analisi delle dipendenze basate su agente in Azure Migrate server assessment.
ms.topic: how-to
ms.date: 6/09/2020
ms.openlocfilehash: 1a656ec734ff098dd5835f653010c7f298c13b38
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/08/2020
ms.locfileid: "86109995"
---
# <a name="set-up-dependency-visualization"></a>Configurare la visualizzazione delle dipendenze

Questo articolo descrive come configurare l'analisi delle dipendenze senza agente in Azure Migrate: server assessment. L' [analisi delle dipendenze](concepts-dependency-visualization.md) consente di identificare e comprendere le dipendenze tra i computer di cui si vuole valutare e migrare in Azure.

## <a name="before-you-start"></a>Prima di iniziare

- Esaminare i requisiti di supporto e distribuzione per l'analisi delle dipendenze basate su agente per:
    - [VM VMware](migrate-support-matrix-vmware.md#dependency-analysis-requirements-agent-based)
    - [Server fisici](migrate-support-matrix-physical.md#agent-based-dependency-analysis-requirements)
    - [Macchine virtuali Hyper-V](migrate-support-matrix-hyper-v.md#agent-based-dependency-analysis-requirements).
- Assicurarsi di:
    - Avere un progetto Azure Migrate. In caso contrario, [crearne](how-to-add-tool-first-time.md) uno ora.
    - Verificare di aver [aggiunto](how-to-assess.md) lo strumento Azure migrate: server Assessment al progetto.
    - Configurare un' [appliance Azure migrate](migrate-appliance.md) per individuare i computer locali. L'appliance individua i computer locali e invia i metadati e i dati sulle prestazioni a Azure Migrate: server assessment. Configurare un'appliance per:
        - [VMware](how-to-set-up-appliance-vmware.md) Macchine virtuali.
        - [Hyper-V](how-to-set-up-appliance-hyper-v.md) Macchine virtuali.
        - [Server fisici](how-to-set-up-appliance-physical.md).
- Per usare la visualizzazione delle dipendenze, è necessario associare un' [area di lavoro log Analytics](../azure-monitor/platform/manage-access.md) a un progetto Azure migrate:
    - È possibile aggiungere un'area di lavoro solo dopo aver configurato il dispositivo Azure Migrate e individuando i computer nel progetto Azure Migrate.
    - Assicurarsi di disporre di un'area di lavoro nella sottoscrizione che contiene il progetto Azure Migrate.
    - L'area di lavoro deve trovarsi nelle aree Stati Uniti orientali, Asia sud-orientale o Europa occidentale. Non è possibile associare a un progetto aree di lavoro di altre regioni.
    - L'area di lavoro deve trovarsi in una regione in cui la soluzione [Mapping dei servizi è supportata](../azure-monitor/insights/vminsights-enable-overview.md#prerequisites).
    - È possibile associare un'area di lavoro Log Analytics nuova o esistente a un progetto Azure Migrate.
    - L'area di lavoro viene collegata la prima volta che si configura la visualizzazione delle dipendenze per un computer. Non è possibile modificare l'area di lavoro per un progetto Azure Migrate dopo che è stata aggiunta.
    - In Log Analytics, l'area di lavoro associata ad Azure Migrate è contrassegnata con la chiave di migrazione progetto e con il nome del progetto.

## <a name="associate-a-workspace"></a>Associare un'area di lavoro

1. Dopo aver individuato i computer per la valutazione, in **Server**  >  **Azure migrate: server Assessment**, fare clic su **Panoramica**.  
2. In **Azure migrate: server Assessment**fare clic su **Essentials**.
3. Nell' **area di lavoro di OMS**fare clic su **richiede la configurazione**.

     ![Configurare un'area di lavoro Log Analytics](./media/how-to-create-group-machine-dependencies/oms-workspace-select.png)   

4. In **Configura area di lavoro OMS**specificare se si desidera creare una nuova area di lavoro o utilizzarne una esistente.
    - È possibile selezionare un'area di lavoro esistente da tutte le aree di lavoro nella sottoscrizione migrate Project.
    - È necessario l'accesso in lettura all'area di lavoro per associarlo.
5. Se si crea una nuova area di lavoro, selezionarne il percorso.

    ![Aggiungere una nuova area di lavoro](./media/how-to-create-group-machine-dependencies/workspace.png)


## <a name="download-and-install-the-vm-agents"></a>Scaricare e installare gli agenti di macchine virtuali

Installare gli agenti in ogni computer che si desidera analizzare.

> [!NOTE]
> Per i computer monitorati da System Center Operations Manager 2012 R2 o versione successiva, non è necessario installare l'agente MMA. Mapping dei servizi si integra con Operations Manager. [Seguire](../azure-monitor/insights/service-map-scom.md#prerequisites) le linee guida per l'integrazione.

1. In **Azure migrate: server Assessment**fare clic su **server individuati**.
2. Per ogni computer che si vuole analizzare con la visualizzazione delle dipendenze, nella colonna **dipendenze** fare clic su **richiede l'installazione dell'agente**.
3. Nella pagina **dipendenze** scaricare MMA e Dependency Agent per Windows o Linux.
4. In **Configura agente MMA**copiare l'ID e la chiave dell'area di lavoro. Sono necessari quando si installa l'agente MMA.

    ![Installare gli agenti](./media/how-to-create-group-machine-dependencies/dependencies-install.png)


## <a name="install-the-mma"></a>Installare MMA

Installare MMA in ogni computer Windows o Linux che si vuole analizzare.

### <a name="install-mma-on-a-windows-machine"></a>Installare MMA in un computer Windows

Per installare l'agente in un computer Windows:

1. Fare doppio clic sull'agente scaricato.
2. Nella pagina di **benvenuto** fare clic su **Avanti**. Nella pagina **condizioni di licenza** **fare clic su Accetto per** accettare la licenza.
3. In **Cartella di destinazione** mantenere o modificare la cartella di installazione predefinita e quindi fare clic su **Avanti**.
4. In **Opzioni di installazione dell'agente** selezionare **Azure Log Analytics** > **Avanti**.
5. Fare clic su **Aggiungi** per aggiungere una nuova area di lavoro Log Analytics. Incollare l'ID e la chiave dell'area di lavoro copiati dal portale. Fare clic su **Avanti**.

È possibile installare l'agente dalla riga di comando o usando un metodo automatizzato, ad esempio Configuration Manager o [Intigua](https://www.intigua.com/intigua-for-azure-migration).
- [Altre informazioni](../azure-monitor/platform/log-analytics-agent.md#installation-and-configuration) sull'uso di questi metodi per installare l'agente MMA.
- L'agente MMA può essere installato anche usando questo [script](https://go.microsoft.com/fwlink/?linkid=2104394).
- [Altre](../azure-monitor/platform/log-analytics-agent.md#supported-windows-operating-systems) informazioni sui sistemi operativi Windows supportati da MMA.

### <a name="install-mma-on-a-linux-machine"></a>Installare MMA in un computer Linux

Per installare MMA in un computer Linux:

1. Trasferire il bundle appropriato (x86 o x64) nel computer Linux mediante scp/sftp.
2. Installare il bundle usando l'argomento --install.

    ```sudo sh ./omsagent-<version>.universal.x64.sh --install -w <workspace id> -s <workspace key>```

[Altre informazioni](../azure-monitor/platform/log-analytics-agent.md#supported-linux-operating-systems) sull'elenco dei sistemi operativi Linux supportati da MMA. 

## <a name="install-the-dependency-agent"></a>Installare Dependency Agent

1. Per installare Dependency Agent in un computer Windows, fare doppio clic sul file di installazione e seguire la procedura guidata.
2. Per installare Dependency Agent in un computer Linux, procedere all'installazione come utente ROOT usando il comando seguente:

    ```sh InstallDependencyAgent-Linux64.bin```

- [Altre informazioni](../azure-monitor/insights/vminsights-enable-hybrid-cloud.md#installation-script-examples) sul modo in cui è possibile usare gli script per installare l'agente di dipendenza.
- [Altre](../azure-monitor/insights/vminsights-enable-overview.md#supported-operating-systems) informazioni sui sistemi operativi supportati da Dependency Agent.


## <a name="create-a-group-using-dependency-visualization"></a>Creare un gruppo usando la visualizzazione delle dipendenze

A questo punto, creare un gruppo per la valutazione. 


> [!NOTE]
> I gruppi per i quali si vuole visualizzare le dipendenze non devono includere più di 10 computer. Se sono presenti più di 10 computer, suddividerli in gruppi più piccoli.

1. In **Azure migrate: server Assessment**fare clic su **server individuati**.
2. Nella colonna **dipendenze** fare clic su **Visualizza dipendenze** per ogni computer che si desidera esaminare.
3. Nella mappa delle dipendenze è possibile vedere quanto segue:
    - Connessioni TCP in ingresso (client) e in uscita (Server), da e verso il computer.
    - I computer dipendenti in cui non sono installati gli agenti di dipendenza sono raggruppati in base ai numeri di porta.
    - I computer dipendenti con agenti di dipendenza installati vengono visualizzati come caselle separate.
    - Processi in esecuzione all'interno del computer. Espandere ogni casella del computer per visualizzare i processi.
    - Proprietà del computer (incluso FQDN, sistema operativo, indirizzo MAC). Fare clic su ogni casella del computer per visualizzare i dettagli.

4. È possibile esaminare le dipendenze per intervalli di tempo diversi facendo clic sulla durata nell'etichetta dell'intervallo di tempo.
    - Per impostazione predefinita, l'intervallo è un'ora. 
    - È possibile modificare l'intervallo di tempo oppure specificare le date di inizio e fine e la durata.
    - L'intervallo di tempo può essere fino a un'ora. Se è necessario un intervallo più lungo, usare monitoraggio di Azure per eseguire query sui dati dipendenti per un periodo di tempo più lungo.

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

Ecco alcune query di esempio che è possibile usare per estrarre i dati delle dipendenze.

- È possibile modificare le query per estrarre i punti dati preferiti.
- [Esaminare](../azure-monitor/insights/service-map.md#log-analytics-records) un elenco completo dei record dei dati delle dipendenze.
- [Esaminare](../azure-monitor/insights/service-map.md#sample-log-searches) le query di esempio aggiuntive.

#### <a name="sample-review-inbound-connections"></a>Esempio: esaminare le connessioni in ingresso

Verificare le connessioni in ingresso per un set di macchine virtuali.

- I record della tabella per le metriche di connessione (VMConnection) non rappresentano le singole connessioni di rete fisica.
- Più connessioni di rete fisiche vengono raggruppate in una connessione logica.
- [Altre](../azure-monitor/insights/service-map.md#connections) informazioni sul modo in cui i dati della connessione di rete fisica sono aggregati in VMConnection.

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


