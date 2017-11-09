---
title: Gestire i log di flusso dei gruppi di sicurezza di rete con Network Watcher e Grafana | Microsoft Docs
description: Gestire e analizzare i log di flusso dei gruppi di sicurezza di rete in Azure con Network Watcher e Grafana.
services: network-watcher
documentationcenter: na
author: kumudD
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/15/2017
ms.author: kumud
ms.openlocfilehash: 44cf074223c88b8fa539144c0d948e68ae6cbd13
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="manage-and-analyze-network-security-group-flow-logs-using-network-watcher-and-grafana"></a>Gestire e analizzare i log di flusso dei gruppi di sicurezza di rete con Network Watcher e Grafana

I [log di flusso dei gruppi di sicurezza di rete (NSG)](network-watcher-nsg-flow-logging-overview.md) contengono informazioni utili per comprendere il traffico IP in ingresso e in uscita nelle interfacce di rete. Questi log mostrano i flussi in ingresso e in uscita in base alle regole NSG, alla scheda di interfaccia di rete a cui si applica il flusso, a informazioni a 5 tuple sul flusso (IP di origine/destinazione, porta di origine/destinazione e protocollo) e al fatto che il traffico sia stato consentito o rifiutato.

È possibile avere molti gruppi di sicurezza di rete in rete con i log di flusso abilitati. Questa quantità di dati di log rende più difficile l'analisi e il reperimento delle informazioni dettagliate nei log. Questo articolo offre una soluzione per gestire centralmente i log di flusso dei gruppi di sicurezza di rete usando Grafana, uno strumento grafico open source, ElasticSearch, un motore di analisi e di ricerca distribuita e Logstash, una pipeline di elaborazione di dati lato server open-source.  

## <a name="scenario"></a>Scenario

I log di flusso dei gruppi di sicurezza di rete sono abilitati tramite Network Watcher e vengono archiviati nell'archiviazione BLOB di Azure. Per connettersi ed elaborare log di flusso dall'archiviazione BLOB e inviarli a ElasticSearch viene usato un plug-in Logstash.  Una volta che i log di flusso vengono archiviati in ElasticSearch possono essere analizzati e visualizzati in dashboard personalizzati in Grafana.

![Grafana Network Watcher gruppo di sicurezza di rete](./media/network-watcher-nsg-grafana/network-watcher-nsg-grafana-fig1.png)

## <a name="installation-steps"></a>Procedura di installazione

### <a name="enable-network-security-group-flow-logging"></a>Abilitare la registrazione dei flussi dei gruppi di sicurezza di rete

Per questo scenario, è necessario abilitare la registrazione dei flussi dei gruppi di sicurezza di rete in almeno un gruppo di sicurezza di rete nel proprio account. Per istruzioni in proposito, vedere [Introduzione alla registrazione dei flussi per i gruppi di sicurezza di rete](network-watcher-nsg-flow-logging-overview.md).

### <a name="setup-considerations"></a>Considerazioni sull'installazione

In questo esempio Grafana, ElasticSearch e Logstash sono configurati in un Server Ubuntu 16.04 LTS distribuito in Azure. Per l'esecuzione dei tre componenti che sono tutti in esecuzione nella stessa macchina virtuale viene usata l'installazione minima. Questo programma di installazione deve essere usato solo per i carichi di lavoro non critici e di test. Logstash Elasticsearch e Grafana possono essere progettati per la scalabilità in modo indipendente su numerose istanze. Per altre informazioni vedere la documentazione relativa ognuno di questi componenti.

### <a name="install-logstash"></a>Installare Logstash

Logstash consente di rendere flat i log di flusso in formato JSON a un livello di tupla dei flussi.

1. Per installare Logstash, eseguire questi comandi:

    ```bash
    curl -L -O https://artifacts.elastic.co/downloads/logstash/logstash-5.2.0.deb
    sudo dpkg -i logstash-5.2.0.deb
    ```

2. Configurare Logstash per analizzare i log di flusso e inviarli a ElasticSearch. Creare un file logstash.conf usando:

    ```bash
    sudo touch /etc/logstash/conf.d/logstash.conf
    ```

3. Aggiungere il contenuto seguente al file. Modificare il nome e la chiave di accesso dell'account di archiviazione in modo da riflettere i dettagli dell'account di archiviazione:

    ```bash
    input {
      azureblob
      {
        storage_account_name => "mystorageaccount"
        storage_access_key => "VGhpcyBpcyBhIGZha2Uga2V5Lg=="
        container => "insights-logs-networksecuritygroupflowevent"
        codec => "json"
        # Refer https://docs.microsoft.com/azure/network-watcher/network-watcher-read-nsg-flow-logs
        # Typical numbers could be 21/9 or 12/2 depends on the nsg log file types
        file_head_bytes => 12
        file_tail_bytes => 2
        # Enable / tweak these settings when event is too big for codec to handle.
        # break_json_down_policy => "with_head_tail"
        # break_json_batch_count => 2
      }
    }
    filter {
      split { field => "[records]" }
      split { field => "[records][properties][flows]"}
      split { field => "[records][properties][flows][flows]"}
      split { field => "[records][properties][flows][flows][flowTuples]"}

      mutate {
        split => { "[records][resourceId]" => "/"}
        add_field => { "Subscription" => "%{[records][resourceId][2]}"
          "ResourceGroup" => "%{[records][resourceId][4]}"
          "NetworkSecurityGroup" => "%{[records][resourceId][8]}" 
        }
        convert => {"Subscription" => "string"}
        convert => {"ResourceGroup" => "string"}
        convert => {"NetworkSecurityGroup" => "string"}
        split => { "[records][properties][flows][flows][flowTuples]" => "," }
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
      elasticsearch {
        hosts => "localhost"
        index => "nsg-flow-logs"
      }
    }
    ```

Il file di configurazione di Logstash è composto da tre parti, ovvero input, filtro e output. La sezione input indica l'origine di input dei log che verranno elaborati da Logstash. In questo caso occorre usare un plug-in "azureblob" (che verrà installato nei passaggi successivi) che consentirà di accedere ai file JSON dei log di flusso dei gruppi di sicurezza di rete archiviati nell'archiviazione BLOB. 

La sezione filtro rende quindi flat tutti i file di log di flusso in modo che ogni tupla dei flussi e le relative proprietà associate diventino un evento Logstash separato.

La sezione di output inoltra infine ogni evento Logstash al server ElasticSearch. È possibile modificare il file di configurazione Logstash in base alle esigenze specifiche.

### <a name="install-the-logstash-input-plugin-for-azure-blob-storage"></a>Installare il plug-in di input Logstash per l'archiviazione BLOB di Azure

Questo plug-in Logstash consentirà di accedere direttamente ai log dei flussi dall'account di archiviazione BLOB designato. Per installare questo plug-in, nella directory di installazione Logstash predefinita (in questo caso /usr/share/logstash/bin) eseguire il comando seguente:

```bash
cd /usr/share/logstash/bin
sudo ./logstash-plugin install logstash-input-azureblob
```

Per altre informazioni su questo plug-in vedere [Logstash input plugin for Azure Storage Blobs](https://github.com/Azure/azure-diagnostics-tools/tree/master/Logstash/logstash-input-azureblob) (Plug-in di input Logstash per i BLOB del servizio di archiviazione di Azure).

### <a name="install-elasticsearch"></a>Installare ElasticSearch

È possibile usare lo script seguente per installare ElasticSearch. Per informazioni sull'installazione di ElasticSearch vedere [Elastic Stack](https://www.elastic.co/guide/en/elastic-stack/current/index.html).

```bash
apt-get install apt-transport-https openjdk-8-jre-headless uuid-runtime pwgen -y
wget -qO - https://packages.elastic.co/GPG-KEY-elasticsearch | apt-key add -
echo "deb https://packages.elastic.co/elasticsearch/5.x/debian stable main" | tee -a /etc/apt/sources.list.d/elasticsearch-5.x.list
apt-get update && apt-get install elasticsearch
sed -i s/#cluster.name:.*/cluster.name:\ grafana/ /etc/elasticsearch/elasticsearch.yml
systemctl daemon-reload
systemctl enable elasticsearch.service
systemctl start elasticsearch.service
```

### <a name="install-grafana"></a>Installare Grafana

Per installare ed eseguire Grafana usare questi comandi:

```bash
wget https://s3-us-west-2.amazonaws.com/grafana-releases/release/grafana_4.5.1_amd64.deb
sudo apt-get install -y adduser libfontconfig
sudo dpkg -i grafana_4.5.1_amd64.deb
sudo service grafana-server start
```

Per altre informazioni sull'installazione vedere [Installing on Debian / Ubuntu](http://docs.grafana.org/installation/debian/) (Installazione su Debian/Ubuntu).

#### <a name="add-the-elasticsearch-server-as-a-data-source"></a>Aggiungere il server ElasticSearch come un'origine dati

È necessario quindi aggiungere l'indice ElasticSearch contenente i log di flusso come origine dati. È possibile aggiungere un'origine dati selezionando **Aggiungi origine dati** e completando il modulo con le informazioni pertinenti. Un esempio di questa configurazione è reperibile nella schermata seguente:

![Aggiungere l'origine dati](./media/network-watcher-nsg-grafana/network-watcher-nsg-grafana-fig2.png)

#### <a name="create-a-dashboard"></a>Creare un dashboard

Ora che Grafana è stata configurata correttamente per leggere l'indice ElasticSearch contenente i log di flusso dei gruppi di sicurezza di rete è possibile creare e personalizzare i dashboard. Per creare un nuovo dashboard, selezionare **Create your first dashboard** (Crea il primo dashboard). La configurazione per i grafi di esempio seguente mostra flussi segmentati per regola NSG:

![Grafo del dashboard](./media/network-watcher-nsg-grafana/network-watcher-nsg-grafana-fig3.png)

La schermata seguente illustra un grafo e un grafico che mostrano i flussi principali e la loro frequenza. I flussi vengono visualizzati anche dalla regola NSG e dai flussi per decisione. Grafana è altamente personalizzabile, pertanto è consigliabile creare dashboard in base alle esigenze di monitoraggio specifiche. L'esempio seguente illustra un dashboard tipico:

![Grafo del dashboard](./media/network-watcher-nsg-grafana/network-watcher-nsg-grafana-fig4.png)

## <a name="conclusion"></a>Conclusioni

Grazie all'integrazione di Network Watcher con ElasticSearch e Grafana è ora possibile gestire e visualizzare in modo semplice e centralizzato i log di flusso dei gruppi di sicurezza di rete e altri dati. Grafana ha numerose altre potenti funzionalità grafiche che possono essere usate anche per gestire ulteriormente i log di flusso e capire meglio il traffico di rete. Dopo aver configurato e connesso ad Azure un'istanza di Grafana è possibile continuare a esplorare le altre funzionalità che offre.

## <a name="next-steps"></a>Passaggi successivi

- Ulteriori informazioni sull'uso di [Network Watcher](network-watcher-monitoring-overview.md).

