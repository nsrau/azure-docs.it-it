---
title: Ridefinire un gruppo di valutazione con il mapping delle dipendenze del gruppo in Azure Migrate | Microsoft Docs
description: Descrive come ridefinire una valutazione usando il mapping delle dipendenze del gruppo nel servizio Azure Migrate.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 12/05/2018
ms.author: raynew
ms.openlocfilehash: 3ee528cc68a2a5637e85dc1d5ef68203916138e7
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60596860"
---
# <a name="refine-a-group-using-group-dependency-mapping"></a>Ridefinire un gruppo usando il mapping delle dipendenze del gruppo

In questo articolo viene descritto come ridefinire un gruppo visualizzando le dipendenze di tutti i computer nel gruppo. In genere si usa questo metodo quando si vogliono ridefinire le appartenenze di un gruppo esistente, controllando in modo incrociato le dipendenze del gruppo, prima di eseguire una valutazione. Ridefinizione di un gruppo tramite la visualizzazione delle dipendenze è utile per pianificare in modo efficace la migrazione a Azure. È possibile individuare tutti i sistemi interdipendenti di cui è necessario eseguire la migrazione insieme. Garantisce inoltre una migrazione completa e senza interruzioni a sorpresa.


> [!NOTE]
> I gruppi per i quali si vuole visualizzare le dipendenze non devono includere più di 10 computer. Se il gruppo contiene più di 10 computer, è consigliabile suddividerlo in gruppi più piccoli per sfruttare la funzionalità di visualizzazione delle dipendenze.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prepare-for-dependency-visualization"></a>Prepararsi per la visualizzazione delle dipendenze
Azure Migrate Usa mapping dei servizi nei log di monitoraggio di Azure per abilitare la visualizzazione delle dipendenze delle macchine.

> [!NOTE]
> La funzionalità di visualizzazione delle dipendenze non è disponibile in Azure per enti pubblici.

### <a name="associate-a-log-analytics-workspace"></a>Associare un'area di lavoro Log Analytics
Per sfruttare i vantaggi della visualizzazione delle dipendenze è possibile associare un'area di lavoro Log Analytics, nuova o esistente, a un progetto di Azure Migrate. È possibile creare o collegare solo un'area di lavoro nella stessa sottoscrizione in cui viene creato il progetto di migrazione.

- Per collegare un'area di lavoro Log Analytics a un progetto, in **Panoramica** passare alla sezione **Essentials** del progetto e fare clic su **Configurazione richiesta**

    ![Associare un'area di lavoro Log Analytics](./media/concepts-dependency-visualization/associate-workspace.png)

- Durante l'associazione di un'area di lavoro si avrà la possibilità di creare una nuova area di lavoro o di collegarne una esistente:
    - Quando si crea una nuova area di lavoro è necessario specificare un nome per essa. L'area di lavoro viene quindi creata in una regione della stessa [area geografica di Azure](https://azure.microsoft.com/global-infrastructure/geographies/) come progetto di migrazione.
    - Quando si collega un'area di lavoro esistente, è possibile sceglierla tra tutte le aree di lavoro disponibili nella stessa sottoscrizione del progetto di migrazione. Sono elencate solo le aree di lavoro create in un'area geografica in cui [è supportato il mapping dei servizi](https://docs.microsoft.com/azure/azure-monitor/insights/service-map-configure#supported-azure-regions). Per poter collegare un'area di lavoro è necessario avere l'accesso in lettura all'area di lavoro.

> [!NOTE]
> Non è possibile modificare l'area di lavoro associata a un progetto di migrazione.

### <a name="download-and-install-the-vm-agents"></a>Scaricare e installare gli agenti di macchine virtuali
Per visualizzare le dipendenze di un gruppo, è necessario scaricare e installare gli agenti in ogni computer locale appartenente al gruppo. Se si hanno computer senza accesso a Internet, è necessario scaricare e installare il [Gateway Log Analytics](../azure-monitor/platform/gateway.md).

1. In **Panoramica**, fare clic su **Gestisci** > **gruppi** e andare al gruppo desiderato.
2. Nell'elenco di computer nella colonna **Dependency agent**, fare clic su **Richiede l'installazione** per visualizzare le istruzioni su come scaricare e installare gli agenti.
3. Nella pagina **Dipendenze**, scaricare e installare Microsoft Monitoring Agent (MMA) e Dependency Agent in ogni macchina virtuale del gruppo.
4. Copiare l'ID e la chiave dell'area di lavoro. Questi dati sono necessari quando si installa MMA nei computer locali.

### <a name="install-the-mma"></a>Installare MMA

#### <a name="install-the-agent-on-a-windows-machine"></a>Installare l'agente in un computer Windows

Per installare l'agente in un computer Windows:

1. Fare doppio clic sull'agente scaricato.
2. Nella pagina di **benvenuto** fare clic su **Avanti**. Nella pagina **Condizioni di licenza** fare clic su **Accetto** per accettare la licenza.
3. In **Cartella di destinazione** mantenere o modificare la cartella di installazione predefinita e quindi fare clic su **Avanti**.
4. In **Opzioni di installazione dell'agente** selezionare **Azure Log Analytics** > **Avanti**.
5. Fare clic su **Aggiungi** per aggiungere una nuova area di lavoro Log Analytics. Incollare l'ID e la chiave dell'area di lavoro copiati dal portale. Fare clic su **Avanti**.

È possibile installare l'agente dalla riga di comando o usando un metodo automatizzato, ad esempio System Center Configuration Manager. [Altre informazioni](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent#install-and-configure-agent) sull'uso di questi metodi per installare l'agente MMA.

#### <a name="install-the-agent-on-a-linux-machine"></a>Installare l'agente in un computer Linux

Per installare l'agente in un computer Linux:

1. Trasferire il bundle appropriato (x86 o x64) nel computer Linux mediante scp/sftp.
2. Installare il bundle usando l'argomento --install.

    ```sudo sh ./omsagent-<version>.universal.x64.sh --install -w <workspace id> -s <workspace key>```

#### <a name="install-the-agent-on-a-machine-monitored-by-system-center-operations-manager"></a>Installare l'agente in un computer monitorato tramite System Center Operations Manager

Per i computer monitorati da Operations Manager 2012 R2 o versioni successive, non è necessario installare l'agente MMA. Mapping dei servizi dispone di un'integrazione con Operations Manager che sfrutta l'MMA Operations Manager per raccogliere i dati sulle dipendenze necessari. È possibile abilitare l'integrazione usando il materiale sussidiario disponibile [qui](https://docs.microsoft.com/azure/azure-monitor/insights/service-map-scom#prerequisites). Si noti tuttavia che sarà necessario installare l'agente di dipendenza su tali computer.

### <a name="install-the-dependency-agent"></a>Installare Dependency Agent
1. Per installare Dependency Agent in un computer Windows, fare doppio clic sul file di installazione e seguire la procedura guidata.
2. Per installare Dependency Agent in un computer Linux, procedere all'installazione come utente ROOT usando il comando seguente:

    ```sh InstallDependencyAgent-Linux64.bin```

Altre informazioni sul supporto di Dependency Agent per sistemi operativi [Windows](../azure-monitor/insights/service-map-configure.md#supported-windows-operating-systems) e [Linux](../azure-monitor/insights/service-map-configure.md#supported-linux-operating-systems).

[Altre informazioni](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure#installation-script-examples) sul modo in cui è possibile usare gli script per installare l'agente di dipendenza.

## <a name="refine-the-group-based-on-dependency-visualization"></a>Ridefinire il gruppo in base alla visualizzazione delle dipendenze
Dopo aver installato gli agenti in tutti i computer del gruppo, è possibile visualizzare le dipendenze e ridefinirle seguendo i passaggi seguenti.

1. Nel progetto di Azure Migrate, in **Gestisci**, fare clic su  **Gruppi** e selezionare il gruppo.
2. Nella pagina relativa al gruppo fare clic su  **Visualizza dipendenze** per aprire la mappa delle dipendenze del gruppo.
3. La mappa delle dipendenze del gruppo mostra i dettagli seguenti:
   - Connessioni TCP in entrata (client) e in uscita (server) a/da tutti i computer che fanno parte del gruppo
       - I computer dipendenti in cui non sono installati l'agente MMA e l'agente Dependency Agent sono raggruppati in base ai numeri di porta
       - I computer dipendenti in cui sono installati l'agente MMA e l'agente Dependency Agent sono visualizzati in caselle separate
   - I processi in esecuzione sul computer: è possibile espandere ogni casella di computer per visualizzare i processi
   - Proprietà quali: nome di dominio completo, sistema operativo, indirizzo MAC di ogni computer e così via. Fare clic su ogni casella macchina per visualizzare questi dettagli

     ![Visualizzazione delle dipendenze del gruppo](./media/how-to-create-group-dependencies/view-group-dependencies.png)

3. Per visualizzare le dipendenze a un livello più dettagliato, fare clic sull'intervallo di tempo per modificarlo. Per impostazione predefinita, l'intervallo è un'ora. È possibile modificare l'intervallo di tempo oppure specificare le date di inizio e fine e la durata.

   > [!NOTE]
   >    Attualmente, l'interfaccia utente di visualizzazione delle dipendenze non supporta la selezione di un intervallo di tempo più lungo di un'ora. Usare monitoraggio di Azure registra [eseguire query sui dati delle dipendenze](https://docs.microsoft.com/azure/migrate/how-to-create-a-group) su periodi di tempo.

4. Verificare i computer dipendenti, i processi in esecuzione in ciascun computer e identificare i computer da aggiungere o rimuovere dal gruppo.
5. Premere CTRL+clic per selezionare contemporaneamente più computer sulla mappa e aggiungerle o rimuoverle dal gruppo.
    - È possibile aggiungere solo i computer che sono stati individuati.
    - L'aggiunta e la rimozione dei computer di un gruppo invalidano le precedenti valutazioni eseguite per il gruppo.
    - Quando si modifica il gruppo, è possibile creare facoltativamente una nuova valutazione.
5. Fare clic su **OK** per salvare il gruppo.

    ![Aggiungere o rimuovere computer](./media/how-to-create-group-dependencies/add-remove.png)

Se si vogliono verificare le dipendenze di un computer specifico che viene visualizzato nella mappa delle dipendenze del gruppo, [configurare il mapping delle dipendenze del computer](how-to-create-group-machine-dependencies.md).

## <a name="query-dependency-data-from-azure-monitor-logs"></a>Eseguire query sui dati delle dipendenze dai log di monitoraggio di Azure

I dati sulle dipendenze acquisiti da mapping dei servizi sono disponibili per l'esecuzione di query nell'area di lavoro di Log Analitica associato al progetto Azure Migrate. [Altre informazioni](https://docs.microsoft.com/azure/azure-monitor/insights/service-map#log-analytics-records) sulle tabelle di dati di mapping dei servizi per eseguire una query in Monitoraggio di Azure Registra. 

Per eseguire la query Kusto:

1. Dopo aver installato gli agenti, accedere al portale e fare clic su **Panoramica**.
2. In **Panoramica** , andare alla sezione **Elementi fondamentali** del progetto e fare clic sul nome dell'area di lavoro accanto a **Spazio di lavoro OMS** .
3. Nella pagina dell'area di lavoro Log Analytics, fare clic su **Generale** > **Log**.
4. Scrivere la query per raccogliere dati sulle dipendenze mediante i log di monitoraggio di Azure. Trovare le query di esempio nella sezione successiva.
5. Eseguire la query facendo clic su Esegui. 

[Altre informazioni](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal) sulla scrittura di query Kusto. 

## <a name="sample-azure-monitor-logs-queries"></a>Monitoraggio di Azure di esempio registra le query

Di seguito sono esempi di query è possibile usare per estrarre i dati sulle dipendenze. È possibile modificare le query per estrarre i punti dati preferito. È disponibile un elenco completo dei campi nel record di dati delle dipendenze [qui](https://docs.microsoft.com/azure/azure-monitor/insights/service-map#log-analytics-records). Trovare altri esempi di query [qui](https://docs.microsoft.com/azure/azure-monitor/insights/service-map#sample-log-searches).

### <a name="summarize-inbound-connections-on-a-set-of-machines"></a>Riepilogare le connessioni in ingresso in un set di computer

Si noti che i record nella tabella di metriche relative alla connessione VMConnection, non rappresentano le connessioni di rete fisica. Più connessioni di rete fisiche sono raggruppate in una connessione logica. [Altre informazioni](https://docs.microsoft.com/azure/azure-monitor/insights/service-map#connections) sulla connessione di rete fisica come i dati vengono aggregati in un singolo record logico in VMConnection. 

```
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

#### <a name="summarize-volume-of-data-sent-and-received-on-inbound-connections-between-a-set-of-machines"></a>Riepilogare volume dei dati inviati e ricevuti nelle connessioni in ingresso tra un set di computer

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
- [Altre informazioni](https://docs.microsoft.com/azure/migrate/resources-faq#dependency-visualization) sulle domande frequenti sulla visualizzazione delle dipendenze.
- [Altre informazioni](concepts-assessment-calculation.md) sul modo in cui vengono calcolate le valutazioni.
