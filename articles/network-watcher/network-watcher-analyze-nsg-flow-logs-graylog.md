---
title: Analizzare i log di flusso dei gruppi di sicurezza di rete in Azure - Graylog | Microsoft Docs
description: Informazioni su come gestire e analizzare i log di flusso dei gruppi di sicurezza di rete in Azure con Network Watcher e Graylog.
services: network-watcher
documentationcenter: na
author: mareat
manager: vitinnan
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/19/2017
ms.author: mareat
ms.openlocfilehash: 8d82ffa84c3d75ec3acd102a2de2bdce3718a995
ms.sourcegitcommit: a5f16c1e2e0573204581c072cf7d237745ff98dc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/11/2017
---
# <a name="manage-and-analyze-network-security-group-flow-logs-in-azure-using-network-watcher-and-graylog"></a>Gestire e analizzare i log di flusso dei gruppi di sicurezza di rete in Azure con Network Watcher e Graylog

[I log di flusso dei gruppi di sicurezza di rete](network-watcher-nsg-flow-logging-overview.md) contengono informazioni che è possibile usare per conoscere il traffico IP in ingresso e in uscita per le interfacce di rete di Azure. I log di flusso indicano i flussi in ingresso e in uscita in base a regole del gruppo di sicurezza, l'interfaccia di rete cui si applica il flusso, informazioni su 5 tuple relative al flusso (IP di origine/destinazione, porta di origine/destinazione, protocollo) e se il traffico è consentito o meno.

Nella rete possono essere presenti molti gruppi di sicurezza per cui la registrazione del flusso è abilitata e di conseguenza l'analisi e l'acquisizione di informazioni approfondite dai log può risultare difficile. Questo articolo offre una soluzione per la gestione centralizzata dei log di flusso dei gruppi di sicurezza di rete grazie a Graylog, uno strumento open source di analisi e di gestione dei log, e a Logstash, una pipeline open source di elaborazione dei dati lato server.

## <a name="scenario"></a>Scenario

I log di flusso dei gruppi di sicurezza di rete vengono abilitati tramite Network Watcher e il contenuto viene inviato ad Archiviazione BLOB di Azure. Per connettersi ed elaborare log di flusso dall'archiviazione BLOB e per inviarli a Graylog, viene usato un plug-in Logstash. Dopo l'archiviazione in Graylog, i log di flusso possono essere analizzati e visualizzati in dashboard personalizzati.

![Flusso di lavoro Graylog]](./media/network-watcher-analyze-nsg-flow-logs-graylog/workflow.png)

## <a name="installation-steps"></a>Procedura di installazione

### <a name="enable-network-security-group-flow-logging"></a>Abilitare la registrazione dei flussi dei gruppi di sicurezza di rete

Per questo scenario, è necessario abilitare la registrazione dei flussi dei gruppi di sicurezza di rete in almeno un gruppo di sicurezza di rete nel proprio account. Per istruzioni in proposito, vedere [Introduzione alla registrazione dei flussi per i gruppi di sicurezza di rete](network-watcher-nsg-flow-logging-overview.md).

### <a name="setting-up-graylog"></a>Configurazione di Graylog

In questo esempio Graylog e Logstash sono configurati in un server Ubuntu 14.04, distribuito in Azure.

- Per istruzioni dettagliate sull'installazione in Ubuntu, vedere la [documentazione](http://docs.graylog.org/en/2.2/pages/installation/os/ubuntu.html) di Graylog.
- Verificare di configurare anche l'interfaccia Web di Graylog in base alla [documentazione](http://docs.graylog.org/en/2.2/pages/configuration/web_interface.html#configuring-webif).

Questo esempio usa l'installazione minima di Graylog, ovvero una singola istanza di Graylog, ma è possibile configurare l'architettura di Graylog per applicare la scalabilità tra risorse a seconda delle esigenze del sistema e di produzione. Per altre informazioni sulle considerazioni in termini di architettura o per una guida sull'architettura più approfondita, vedere la [documentazione](http://docs.graylog.org/en/2.2/pages/architecture.html) e la [guida all'architettura](https://www.slideshare.net/Graylog/graylog-engineering-design-your-architecture) di Graylog.

Graylog può essere installato in molti modi, in base alla piattaforma in uso e alle proprie preferenze. Per un elenco completo dei metodi di installazione possibili, vedere la [documentazione](http://docs.graylog.org/en/2.2/pages/installation.html) ufficiale di Graylog. L'applicazione server Graylog viene eseguita su distribuzioni Linux e deve rispettare i requisiti seguenti:

-   Oracle Java SE 8 o versione successiva - [Documentazione di installazione di Oracle](http://docs.oracle.com/javase/8/docs/technotes/guides/install/install_overview.html)
-   Elastic Search 2.x (2.1.0 o versione successiva) - [Documentazione di installazione di Elasticsearch](https://www.elastic.co/guide/en/elasticsearch/reference/2.4/_installation.html)
-   MongoDB 2.4 o versione successiva - [Documentazione di installazione di MongoDB](https://docs.mongodb.com/manual/administration/install-on-linux/)

### <a name="install-logstash"></a>Installare Logstash

Logstash consente di rendere flat i log di flusso in formato JSON a un livello di tupla dei flussi. In questo modo l'organizzazione e la ricerca dei log di flusso in Graylog risultano più semplice.

1.  Per installare Logstash, eseguire questi comandi:

    ```bash
    curl -L -O https://artifacts.elastic.co/downloads/logstash/logstash-5.2.0.deb
    sudo dpkg -i logstash-5.2.0.deb
    ```

2.  Configurare Logstash per analizzare i log di flusso e inviarli a Graylog. Creare un file logstash.conf:

    ```bash
    sudo touch /etc/logstash/conf.d/logstash.conf
    ```

3.  Aggiungere il contenuto seguente al file. Modificare i valori evidenziati in modo da riflettere i dettagli del proprio account di archiviazione:

    ```
    input {
        azureblob
        {
            storage_account_name => "mystorageaccount"
            storage_access_key => "NrUZmx7pJSKaRJzvQbeiZWi5nBRWOTr7Wwr9DrvK7YtDBrADYxT1y0oEExtSlkDnGRt7qcRiZzEBCCyRYND8SxSt"
            container => "insights-logs-networksecuritygroupflowevent"
            registry_create_policy => "start_over"
            codec => "json"
            file_head_bytes => 21
            file_tail_bytes => 9
            # Possible options: `do_not_break`, `with_head_tail`, `without_head_tail`
            break_json_down_policy  => 'with_head_tail'
            break_json_batch_count => 2
            interval => 5
        }
    }
    
    filter {
        split { field => "[records]" }
        split { field => "[records][properties][flows]"}
        split { field => "[records][properties][flows][flows]"}
        split { field => "[records][properties][flows][flows][flowTuples]"
    }
    
     mutate {
        split => { "[records][resourceId]" => "/"}
        add_field =>{
                    "Subscription" => "%{[records][resourceId][2]}"
                    "ResourceGroup" => "%{[records][resourceId][4]}"
                    "NetworkSecurityGroup" => "%{[records][resourceId][8]}"
        }
        convert => {"Subscription" => "string"}
        convert => {"ResourceGroup" => "string"}
        convert => {"NetworkSecurityGroup" => "string"}
        split => { "[records][properties][flows][flows][flowTuples]" => ","}
        add_field => {
                    "unixtimestamp" => "%{[records][properties][flows][flows][flowTuples][0]}"
                    "srcIp" => "%{[records][properties][flows][flows][flowTuples][1]}"
                    "destIp" => "%{[records][properties][flows][flows][flowTuples][2]}"
                    "srcPort" => "%{[records][properties][flows][flows][flowTuples][3]}"
                    "destPort" => "%{[records][properties][flows][flows][flowTuples][4]}"
                    "protocol" => "%{[records][properties][flows][flows][flowTuples][5]}"
                    "trafficflow" => "%{[records][properties][flows][flows][flowTuples][6]}"
                    "traffic" => "%{[records][properties][flows][flows][flowTuples][7]}"
        }
        add_field => {
                    "time" => "%{[records][time]}"
                    "systemId" => "%{[records][systemId]}"
                    "category" => "%{[records][category]}"
                    "resourceId" => "%{[records][resourceId]}"
                    "operationName" => "%{[records][operationName}}"
                    "Version" => "%{[records][properties][Version}}"
                    "rule" => "%{[records][properties][flows][rule]}"
                    "mac" => "%{[records][properties][flows][flows][mac]}"
        }
        convert => {"unixtimestamp" => "integer"}
        convert => {"srcPort" => "integer"}
        convert => {"destPort" => "integer"}
        add_field => { "message" => "%{Message}" }
    }
        date {
            match => ["unixtimestamp" , "UNIX"]
        }
    }
    output {
        stdout { codec => rubydebug }
        udp {
            host => "127.0.0.1"
            port => 12201
        }
    }
    ```
Il file di configurazione di Logstash è composto da tre parti, ovvero input, filtro e output. La sezione input indica l'origine di input dei log che verranno elaborati da Logstash. In questo caso occorre usare un plug-in di input per BLOB di Azure (installato nei passaggi successivi) che consente di accedere ai file JSON dei log di flusso dei gruppi di sicurezza di rete presenti nell'archiviazione BLOB.

La sezione filtro rende quindi flat tutti i file di log di flusso in modo che ogni tupla dei flussi e le relative proprietà associate diventino un evento Logstash separato.

La sezione di output inoltra infine ogni evento Logstash al server Graylog. Modificare il file di configurazione Logstash in base alle esigenze specifiche.

   > [!NOTE]
   > Il file di configurazione precedente presuppone che il server Graylog sia stato configurato per l'indirizzo IP di loopback dell'host locale 127.0.0.1. In caso contrario, sarà necessario modificare il parametro host nella sezione di output per l'indirizzo IP corretto.

Per altre istruzioni sull'installazione di Logstash, vedere la [documentazione](https://www.elastic.co/guide/en/beats/libbeat/5.2/logstash-installation.html) di Logtash.

### <a name="install-the-logstash-input-plug-in-for-azure-blob-storage"></a>Installare il plug-in di input Logstash per Archiviazione BLOB di Azure

Il plug-in Logstash consente di accedere direttamente ai log di flusso dall'account di archiviazione BLOB designato. Per installare il plug-in, nella directory di installazione di Logstash predefinita (in questo caso /usr/share/logstash/bin) eseguire questo comando:

```bash
cd /usr/share/logstash/bin
sudo ./logstash-plugin install logstash-input-azureblob
```

Per altre informazioni sul plug-in, vedere la [documentazione](https://github.com/Azure/azure-diagnostics-tools/tree/master/Logstash/logstash-input-azureblob).

### <a name="set-up-connection-from-logstash-to-graylog"></a>Configurare la connessione da Logstash a Graylog

Dopo aver stabilito una connessione con i log di flusso tramite Logstash e configurato il server Graylog, è necessario configurare Graylog per accettare i file di log in ingresso.

1.  Spostarsi sull'interfaccia Web del server Graylog tramite l'URL configurato. Per accedere all'interfaccia, indirizzare il browser all'URL `http://<graylog-server-ip>:9000/`

2.  Per spostarsi sulla pagina di configurazione, selezionare il menu a discesa **System** (Sistema) nella barra di spostamento superiore a destra e quindi fare clic su **Inputs** (Input).
    In alternativa, accedere all'URL `http://<graylog-server-ip>:9000/system/inputs`

    ![Introduzione](./media/network-watcher-analyze-nsg-flow-logs-graylog/getting-started.png)

3.  Per avviare il nuovo input, selezionare *GELF UDP* nel menu a discesa **Select input** (Seleziona input) e quindi compilare il modulo. GELF è l'acronimo di Graylog Extended Log Format. Il formato GELF viene sviluppato da Graylog. Per altre informazioni sui vantaggi relativi, vedere la [documentazione](http://docs.graylog.org/en/2.2/pages/gelf.html) di Graylog.

    Verificare di associare l'input all'indirizzo IP su cui è stato configurato il server Graylog. L'indirizzo IP deve corrispondere al campo **host** dell'output UDP del file di configurazione di Logstash. La porta predefinita deve essere *12201*. Verificare che la porta corrisponda al campo **port** nell'output UDP indicato nel file di configurazione di Logstash.

    ![Input](./media/network-watcher-analyze-nsg-flow-logs-graylog/inputs.png)

    Dopo che l'input è stato avviato, deve essere visualizzato nella sezione **Local inputs** (Input locali), come illustrato nella figura seguente:

    ![](./media/network-watcher-analyze-nsg-flow-logs-graylog/local-inputs.png)

    Per altre informazioni sugli input dei messaggi di Graylog, vedere la [documentazione](http://docs.graylog.org/en/2.2/pages/sending_data.html#what-are-graylog-message-inputs).

4.  Dopo aver eseguito tali configurazioni, è possibile avviare Logstash per iniziare a leggere i log di flusso con il comando seguente:

    `sudo systemctl start logstash.service`

### <a name="search-through-graylog-messages"></a>Cercare i messaggi in Graylog

Dopo aver consentito al server Graylog di raccogliere i messaggi, è possibile eseguire una ricerca. Per controllare i messaggi inviati al server Graylog, nella pagina **Inputs** (Input) fare clic sul pulsante "**Show received messages**" (Visualizza messaggi ricevuti) dell'input GELF UDP creato. Si verrà reindirizzati a una schermata analoga alla figura seguente: 

![Istogramma](./media/network-watcher-analyze-nsg-flow-logs-graylog/histogram.png)

Se si fa clic sul collegamento blu "% {Message}" (% Messaggio), si espande ogni messaggio per visualizzare i parametri di ogni tupla del flusso, come illustrato nella figura seguente:

![Messaggi](./media/network-watcher-analyze-nsg-flow-logs-graylog/messages.png)

Per impostazione predefinita, se non si seleziona un campo di messaggio specifico in cui eseguire la ricerca, vengono inclusi tutti i campi. Se si intende eseguire la ricerca di messaggi specifici, ad esempio tuple di flusso da un indirizzo IP specifico di origine, è possibile usare il linguaggio di query di ricerca Graylog come descritto nella [documentazione](http://docs.graylog.org/en/2.2/pages/queries.html)


## <a name="analyze-network-security-group-flow-logs-using-graylog"></a>Analizzare i log di flusso dei gruppi di sicurezza di rete tramite Graylog

Ora che Graylog è configurato ed è in esecuzione, è possibile usarne alcune funzionalità per comprendere meglio i dati dei log di flusso, ad esempio usando dashboard per creare visualizzazioni specifiche dei dati.

### <a name="create-a-dashboard"></a>Creare un dashboard

1.  Nella barra di spostamento superiore, selezionare **Dashboards** (Dashboard) o accedere all'URL `http://<graylog-server-ip>:9000/dashboards/`

2.  Fare clic sul pulsante verde **Create dashboard** (Crea dashboard) e compilare il breve modulo inserendo il titolo e la descrizione del dashboard. Fare clic sul pulsante **Save** (Salva) per creare il nuovo dashboard. Verrà visualizzato un dashboard come illustrato nella figura seguente:

    ![Dashboard](./media/network-watcher-analyze-nsg-flow-logs-graylog/dashboards.png)

### <a name="add-widgets"></a>Aggiungere widget

È possibile fare clic sul titolo del dashboard per visualizzarlo, ma attualmente è vuoto poiché non è ancora stato aggiunto alcun widget. Un tipo di widget semplice e pratico da aggiungere al dashboard è costituito dai grafici **Quick Values** (Valori rapidi) che consentono di visualizzare un elenco di valori del campo selezionato e la relativa distribuzione.

1.  Spostarsi sui risultati della ricerca dell'input UDP che riceve i log di flusso selezionando **Search** (Cerca) nella barra di spostamento superiore.

2.  Sotto il pannello **Search result** (Risultati ricerca) sul lato sinistro della schermata individuare la scheda **Fields** (Campi) in cui sono elencati i vari campi di ogni messaggio delle tuple di flusso in ingresso.

3.  Selezionare i parametri desiderati in base ai quali eseguire la visualizzazione (in questo esempio è stato selezionato l'indirizzo IP di origine). Per visualizzare l'elenco dei possibili widget, fare clic sulla freccia a discesa blu a sinistra del campo e quindi selezionare **Quick values** (Valori rapidi) per generare il widget. Verrà visualizzata una schermata analoga alla figura seguente:

    ![IP di origine](./media/network-watcher-analyze-nsg-flow-logs-graylog/srcip.png)

4.  In questo punto è possibile selezionare il pulsante **Add to dashboard** (Aggiungi a dashboard) nell'angolo superiore destro del widget e quindi selezionare il dashboard corrispondente da aggiungere.

5.  Spostarsi sul dashboard per visualizzare il widget aggiunto.

    È possibile aggiungere un'ampia gamma di altri widget, ad esempio istogrammi e conteggi per il dashboard per tenere traccia di metriche importanti, ad esempio il dashboard di esempio illustrato nella figura seguente:

    ![Dashboard di log di flusso](./media/network-watcher-analyze-nsg-flow-logs-graylog/flowlogs-dashboard.png)

    Per altre informazioni sui dashboard e sugli altri tipi di widget, vedere la [documentazione](http://docs.graylog.org/en/2.2/pages/dashboards.html) di Graylog.

Grazie all'integrazione di Network Watcher con Graylog, è ora disponibile un modo semplice e centralizzato per gestire e visualizzare i log di flusso dei gruppi di sicurezza di rete. Graylog offre una vasta gamma di altre funzionalità potenti, ad esempio flussi e avvisi, che possono essere usate anche per gestire ulteriormente i log di flusso e conoscere meglio il traffico di rete. Dopo aver configurato e connesso ad Azure un'istanza di Graylog, è possibile continuare a esplorare le altre funzionalità che offre.

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come visualizzare i log di flusso dei gruppi di sicurezza di rete con Power BI, vedere [Visualizzare i log dei flussi dei gruppi di sicurezza di rete con Power BI](network-watcher-visualize-nsg-flow-logs-power-bi.md).
