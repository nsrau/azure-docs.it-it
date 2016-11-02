<properties
    pageTitle="Soluzione Contenitori in Log Analytics | Microsoft Azure"
    description="La soluzione Contenitori in Log Analytics consente di visualizzare e gestire gli host di contenitori Docker in un'unica posizione."
    services="log-analytics"
    documentationCenter=""
    authors="bandersmsft"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="log-analytics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/10/2016"
    ms.author="banders"/>




# <a name="containers-(preview)-solution-log-analytics"></a>Soluzione Contenitori (anteprima) in Log Analytics

Questo articolo descrive come configurare e usare la soluzione Contenitori in Log Analytics per visualizzare e gestire gli host di contenitori Docker in un'unica posizione. Docker è un sistema di virtualizzazione software usato per creare contenitori che consentono di automatizzare la distribuzione del software nell'infrastruttura IT.

Con la soluzione è possibile visualizzare i contenitori in esecuzione negli host di contenitori e le immagini in esecuzione nei contenitori. È possibile visualizzare informazioni di controllo dettagliate che indicano i comandi usati con i contenitori. È anche possibile risolvere i problemi dei contenitori visualizzando i log centralizzati ed eseguendo ricerche al loro interno senza dover visualizzare gli host Docker in remoto. È possibile trovare contenitori che consumano una quantità eccessiva di risorse in un host. È anche possibile visualizzare informazioni centralizzate su utilizzo di CPU, memoria, archiviazione e rete e sulle prestazioni dei contenitori.

## <a name="installing-and-configuring-the-solution"></a>Installazione e configurazione della soluzione

Usare le informazioni seguenti per installare e configurare la soluzione.

Aggiungere la soluzione Contenitori all'area di lavoro di OMS usando la procedura descritta nell'articolo [Aggiungere soluzioni di Log Analytics dalla Raccolta soluzioni](log-analytics-add-solutions.md).

Esistono due metodi per installare e usare Docker con OMS:

- Nei sistemi operativi Linux supportati installare ed eseguire Docker e quindi installare e configurare l'agente OMS per Linux
- In CoreOS installare ed eseguire Docker e quindi configurare OMSAgent per l'esecuzione all'interno di un contenitore

Esaminare le versioni di Docker e Linux supportate per l'host di contenitori in [GitHub](https://github.com/Microsoft/OMS-docker).

>[AZURE.IMPORTANT] Docker deve essere in esecuzione **prima** di installare l'[agente OMS per Linux](log-analytics-linux-agents.md) negli host di contenitori. Se l'agente è stato installato prima di installare Docker, è necessario reinstallare l'agente di OMS per Linux. Per altre informazioni su Docker, vedere il [sito Web di Docker](https://www.docker.com).

Sono necessarie le seguenti impostazioni negli host di contenitori prima di poter monitorare i contenitori.

## <a name="configure-settings-for-the-linux-container-host"></a>Configurare le impostazioni per l'host di contenitori Linux

Dopo aver installato Docker, usare le impostazioni seguenti per l'host di contenitori per configurare l'agente per l'uso con Docker. CoreOS non supporta questo metodo di configurazione.

### <a name="to-configure-settings-for-the-container-host---systemd-(suse,-opensuse,-centos-7.x,-rhel-7.x,-and-ubuntu-15.x-and-higher)"></a>Per configurare le impostazioni per l'host di contenitori - systemd (SUSE, openSUSE, CentOS 7.x, RHEL 7.x e Ubuntu 15.x e versioni successive)

1. Modificare docker.service per aggiungere il codice seguente:

    ```
    [Service]
    ...
    Environment="DOCKER_OPTS=--log-driver=fluentd --log-opt fluentd-address=localhost:25225"
    ...
    ```

2. Aggiungere $DOCKER\_OPTS in &quot;ExecStart=/usr/bin/docker daemon&quot; nel file docker.service. Vedere l'esempio seguente.

    ```
    [Service]
    Environment="DOCKER_OPTS=--log-driver=fluentd --log-opt fluentd-address=localhost:25225"
    ExecStart=/usr/bin/docker daemon -H fd:// $DOCKER_OPTS
    ```

3. Riavviare il servizio Docker. Ad esempio:

    ```
    sudo systemctl restart docker.service
    ```

### <a name="to-configure-settings-for-the-container-host---upstart-(ubuntu-14.x)"></a>Per configurare le impostazioni per l'host di contenitori - Upstart (Ubuntu 14.x)

1. Modificare /etc/default/docker e aggiungere il codice seguente:

    ```
    DOCKER_OPTS="--log-driver=fluentd --log-opt fluentd-address=localhost:25225"
    ```

2. Salvare il file e quindi riavviare i servizi Docker e OMS.

    ```
    sudo service docker restart
    ```

### <a name="to-configure-settings-for-the-container-host---amazon-linux"></a>Per configurare le impostazioni per l'host di contenitori - Amazon Linux

1. Modificare /etc/sysconfig/docker e aggiungere il codice seguente:

    ```
    OPTIONS="--log-driver=fluentd --log-opt fluentd-address=localhost:25225"
    ```

2. Salvare il file e quindi riavviare il servizio Docker.

    ```
    sudo service docker restart
    ```

## <a name="configure-settings-for-coreos-containers"></a>Configurare le impostazioni per i contenitori CoreOS

Dopo aver installato Docker, usare le impostazioni seguenti per CoreOS per eseguire Docker e creare un contenitore. Con questo metodo di configurazione è possibile usare qualsiasi versione supportata di Linux, incluso CoreOS. Saranno necessari l'[ID area di lavoro e la chiave di OMS](log-analytics-linux-agents.md).

### <a name="to-use-oms-for-all-containers-with-coreos"></a>Per usare OMS per tutti i contenitori con CoreOS

- Avviare il contenitore OMS da monitorare. Modificare usando l'esempio seguente.

  ```
sudo docker run --privileged -d -v /var/run/docker.sock:/var/run/docker.sock -e WSID="your workspace id" -e KEY="your key" -h=`hostname` -p 127.0.0.1:25224:25224/udp -p 127.0.0.1:25225:25225 --name="omsagent" --log-driver=none --restart=always microsoft/oms
```

### <a name="switching-from-using-an-installed-agent-to-one-in-a-container"></a>Passaggio dall'uso di un agente installato a un agente in un contenitore

Se in precedenza veniva usato l'agente installato direttamente e si vuole usare invece un agente in esecuzione in un contenitore, è prima necessario rimuovere OMSAgent. Per altre informazioni, vedere i [passaggi per installare l'agente OMS per Linux](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md).

## <a name="containers-data-collection-details"></a>Informazioni dettagliate sulla raccolta di dati dei contenitori

La soluzione Contenitori raccoglie le varie metriche delle prestazioni e i vari dati di log da host di contenitori e contenitori usando gli agenti OMS per Linux abilitati e da OMSAgent in esecuzione nei contenitori.

La tabella seguente illustra i metodi di raccolta dei dati e altri dettagli sulla modalità di raccolta dei dati per i contenitori.

| Piattaforma | Agente OMS per Linux | Agente SCOM | Archiviazione di Azure | SCOM obbligatorio? | Dati dell'agente SCOM inviati con il gruppo di gestione | Frequenza della raccolta |
|---|---|---|---|---|---|---|
| Linux|![Sì](./media/log-analytics-containers/oms-bullet-green.png)|![No](./media/log-analytics-containers/oms-bullet-red.png)|![No](./media/log-analytics-containers/oms-bullet-red.png)|            ![No](./media/log-analytics-containers/oms-bullet-red.png)|![No](./media/log-analytics-containers/oms-bullet-red.png)| ogni 3 minuti|


La tabella seguente illustra esempi di tipi di dati raccolti dalla soluzione Contenitori:

| Tipo di dati | Fields |
| --- | --- |
| Prestazioni per host e contenitori | Computer, ObjectName, CounterName &#40;%Processor Time, Disk Reads MB, Disk Writes MB, Memory Usage MB, Network Receive Bytes, Network Send Bytes, Processor Usage sec, Network&#41;, CounterValue,TimeGenerated, CounterPath, SourceSystem |
| Inventario contenitori | TimeGenerated, Computer, container name, ContainerHostname, Image, ImageTag, ContinerState, ExitCode, EnvironmentVar, Command, CreatedTime, StartedTime, FinishedTime, SourceSystem, ContainerID, ImageID |
| Inventario delle immagini dei contenitori | TimeGenerated, Computer, Image, ImageTag, ImageSize, VirtualSize, Running, Paused, Stopped, Failed, SourceSystem, ImageID, TotalContainer |
| Log contenitori | TimeGenerated, Computer, image ID, container name, LogEntrySource, LogEntry, SourceSystem, ContainerID |
| Log servizio contenitori | TimeGenerated, Computer, TimeOfCommand, Image, Command, SourceSystem, ContainerID |

## <a name="monitor-containers"></a>Monitorare i contenitori

Dopo aver abilitato la soluzione nel portale di OMS, verrà visualizzato il riquadro **Containers** (Contenitori) con le informazioni di riepilogo sugli host di contenitori e i contenitori in esecuzione negli host.

![Riquadro Containers (Contenitori)](./media/log-analytics-containers/containers-title.png)

Il riquadro visualizza una panoramica del numero di contenitori nell'ambiente e indica se i contenitori presentano errori, sono in esecuzione oppure sono stati arrestati.

### <a name="using-the-containers-dashboard"></a>Uso del dashboard Containers (Contenitori)

Fare clic sul riquadro **Containers** (Contenitori). Le visualizzazioni sono organizzate in base agli elementi seguenti:

- Eventi dei contenitori
- Errori
- Stato dei contenitori
- Inventario delle immagini dei contenitori
- Prestazioni di CPU e memoria

Ogni pannello nel dashboard è una rappresentazione visiva di una ricerca eseguita sui dati raccolti.

![Dashboard Containers (Contenitori)](./media/log-analytics-containers/containers-dash01.png)

![Dashboard Containers (Contenitori)](./media/log-analytics-containers/containers-dash02.png)

Nel pannello **Containers Status** (Stato contenitori) fare clic sull'area superiore come illustrato di seguito.

![Stato dei contenitori](./media/log-analytics-containers/containers-status.png)

Si aprirà Ricerca log con informazioni sugli host e sui contenitori in esecuzione al loro interno.

![Ricerca log per i contenitori](./media/log-analytics-containers/containers-log-search.png)

A questo punto è possibile modificare la query di ricerca per trovare specifiche informazioni di interesse. Per altre informazioni sulle ricerche log, vedere [Ricerche nei log in Log Analytics](log-analytics-log-searches.md).

È ad esempio possibile modificare la query di ricerca per visualizzare tutti i contenitori arrestati anziché i contenitori in esecuzione sostituendo **Running** con **Stopped** nella query.

## <a name="troubleshoot-by-finding-a-failed-container"></a>Risolvere i problemi cercando un contenitore con errori

OMS contrassegna un contenitore come **Non riuscito** se il contenitore è stato terminato con un codice di uscita diverso da zero. È possibile visualizzare una panoramica degli errori nell'ambiente nel pannello **Contenitori non riusciti**.

### <a name="to-find-failed-containers"></a>Per trovare i contenitori non riusciti

1. Fare clic sul pannello **Container Events** (Eventi dei contenitori).  
  ![eventi dei contenitori](./media/log-analytics-containers/containers-events.png)
2. Si aprirà Ricerca log con lo stato di contenitori. Vedere l'esempio seguente.  
  ![stato dei contenitori](./media/log-analytics-containers/containers-container-state.png)
3. Fare quindi clic sul valore Non riuscito per visualizzare altre informazioni, ad esempio le dimensioni dell'immagine e il numero di immagini arrestate e non riuscite. Espandere **mostra dettagli** per visualizzare l'ID immagine.  
  ![contenitori non riusciti](./media/log-analytics-containers/containers-state-failed.png)
4. Trovare quindi il contenitore che esegue questa immagine. Digitare il codice seguente nella query di ricerca.
  `Type=ContainerInventory <ImageID>` Consente di visualizzare i log. È possibile scorrere per visualizzare il contenitore non riuscito.  
  ![contenitori non riusciti](./media/log-analytics-containers/containers-failed04.png)


## <a name="search-logs-for-container-data"></a>Ricerca dei dati dei contenitori nei log

Nella risoluzione di un errore specifico può essere utile vedere dove l'errore si verifica nell'ambiente. I tipi di log seguenti consentono di creare query per ottenere le informazioni necessarie.

- **ContainerInventory**: usare questo tipo per ottenere informazioni sul percorso dei contenitori, i relativi nomi e le immagini che eseguono.
- **ContainerImageInventory**: usare questo tipo per trovare informazioni organizzate per immagine e visualizzare le informazioni sulle immagini, ad esempio ID o dimensioni.
- **ContainerLog**: usare questo tipo per trovare informazioni e voci specifiche del registro errori.
- **ContainerServiceLog**: usare questo tipo per trovare informazioni di audit trail per il daemon Docker, ad esempio comandi di avvio, arresto, eliminazione o pull.

### <a name="to-search-logs-for-container-data"></a>Per cercare i dati dei contenitori nei log

- Scegliere un'immagine non riuscita di recente e trovare i relativi registri degli errori. Iniziare cercando il nome di un contenitore che esegue l'immagine con una ricerca **ContainerInventory**. Cercare ad esempio `Type=ContainerInventory ubuntu Failed`  
    ![Cercare contenitori Ubuntu](./media/log-analytics-containers/search-ubuntu.png)

  Prendere nota del nome del contenitore accanto a **Name** e cercare questi log. In questo esempio si tratta di `Type=ContainerLog adoring_meitner`.

**Visualizzare le informazioni sulle prestazioni**

Quando si inizia a creare query, può essere utile comprendere prima le operazioni possibili. Per visualizzare ad esempio tutti i dati sulle prestazioni, provare con una query generica digitando la query di ricerca seguente.

```
Type=Perf
```

![prestazioni dei contenitori](./media/log-analytics-containers/containers-perf01.png)



È possibile visualizzare i dati in un formato più grafico facendo clic sulla parola **Metrics** (Metriche) nei risultati.

![prestazioni dei contenitori](./media/log-analytics-containers/containers-perf02.png)



È possibile limitare i dati sulle prestazioni visualizzati a un contenitore specifico digitando il relativo nome a destra della query.

```
Type=Perf <containerName>
```

Verrà visualizzato l'elenco delle metriche delle prestazioni raccolte per un singolo contenitore.

![prestazioni dei contenitori](./media/log-analytics-containers/containers-perf03.png)

## <a name="example-log-search-queries"></a>Esempio di query di ricerca log

Spesso è utile compilare query iniziando con qualche esempio da modificare in funzione dell'ambiente. Come punto di partenza è possibile provare a usare il pannello **Query rilevanti** per compilare query più avanzate.

![Query sui contenitori](./media/log-analytics-containers/containers-queries.png)

## <a name="saving-log-search-queries"></a>Salvataggio delle query di ricerca log

Il salvataggio di query è una funzionalità standard di Log Analytics. Le query salvate potranno essere riusate rapidamente in futuro.

Dopo aver creato una query che si ritiene utile, salvarla facendo clic su **Preferiti** nella parte superiore della pagina Ricerca log. Sarà possibile accedere facilmente alla query in seguito dalla pagina **Dashboard**.

## <a name="next-steps"></a>Passaggi successivi

- [Eseguire ricerche nei log](log-analytics-log-searches.md) per visualizzare i record di dati dettagliati per i contenitori.



<!--HONumber=Oct16_HO2-->


