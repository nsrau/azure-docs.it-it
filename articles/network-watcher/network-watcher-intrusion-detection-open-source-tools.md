---
title: Eseguire il rilevamento di intrusioni di rete con Azure Network Watcher e strumenti open source | Microsoft Docs
description: Questo articolo descrive come usare Azure Network Watcher e strumenti open source per eseguire il rilevamento di intrusioni di rete
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: 
ms.assetid: 0f043f08-19e1-4125-98b0-3e335ba69681
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: aff1b5f9e8860d3b8dc09b37684bb8a4ac2bf134
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="perform-network-intrusion-detection-with-network-watcher-and-open-source-tools"></a>Eseguire il rilevamento di intrusioni di rete con Network Watcher e strumenti open source

Le acquisizioni di pacchetti sono un componente chiave per l'implementazione di sistemi di rilevamento di intrusioni (IDS, Intrusion Detection System) di rete e per l'esecuzione del monitoraggio della sicurezza di rete. Esistono diversi strumenti IDS open source che elaborano le acquisizioni di pacchetti e cercano le firme di possibili intrusioni di rete e di attività dannosa. Usando le acquisizioni di pacchetti fornite da Network Watcher, è possibile analizzare la rete per trovare eventuali intrusioni dannose o vulnerabilità.

Uno strumento open source di questo tipo è Suricata, un motore IDS che usa set di regole per monitorare il traffico di rete e attiva avvisi quando si verificano eventi sospetti. Suricata offre un motore a thread multipli e può quindi eseguire l'analisi del traffico di rete con maggiore velocità ed efficienza. Per altri dettagli su Suricata e sulle relative funzionalità, visitare il sito Web all'indirizzo https://suricata-ids.org/.

## <a name="scenario"></a>Scenario

Questo articolo illustra come configurare l'ambiente per eseguire il rilevamento di intrusioni di rete usando Network Watcher, Suricata ed Elastic Stack. Network Watcher fornisce le acquisizioni di pacchetti usate per eseguire il rilevamento di intrusioni di rete. Suricata elabora le acquisizioni di pacchetti e attiva avvisi in base ai pacchetti che corrispondono ai set di regole specificati delle minacce. Questi avvisi vengono archiviati in un file di log nel computer locale. Usando Elastic Stack, i log generati da Suricata possono essere indicizzati e usati per creare un dashboard Kibana, che offre una rappresentazione visiva dei log e consente di comprendere rapidamente le potenziali vulnerabilità della rete.  

![Scenario di applicazione Web semplice][1]

Entrambi gli strumenti open source possono essere configurati in una VM di Azure, consentendo di eseguire questa analisi nell'ambiente di rete di Azure.

## <a name="steps"></a>Passaggi

### <a name="install-suricata"></a>Installare Suricata

Per tutti gli altri metodi di installazione, visitare http://suricata.readthedocs.io/en/latest/install.html

1. Nel terminale della riga di comando della VM eseguire i comandi seguenti:

    ```
    sudo add-apt-repository ppa:oisf/suricata-stable
    sudo apt-get update
    sudo sudo apt-get install suricata
    ```

1. Per verificare l'installazione, eseguire il comando `suricata -h` per visualizzare l'elenco completo di comandi.

### <a name="download-the-emerging-threats-ruleset"></a>Scaricare il set di regole Emerging Threats

In questa fase, non sono disponibili regole per l'esecuzione di Suricata. È possibile creare le proprie regole se esistono minacce specifiche per la rete, che si vuole rilevare, oppure è possibile usare set di regole sviluppati di diversi provider, ad esempio Emerging Threats o le regole VRT di Snort. Qui viene usato il set di regole Emerging Threats accessibile gratuitamente:

Scaricare il set di regole e copiarle nella directory:

```
wget http://rules.emergingthreats.net/open/suricata/emerging.rules.tar.gz
tar zxf emerging.rules.tar.gz
sudo cp -r rules /etc/suricata/
```

### <a name="process-packet-captures-with-suricata"></a>Elaborare le acquisizioni di pacchetti con Suricata

Per elaborare le acquisizioni di pacchetti usando Suricata, eseguire questo comando:

```
sudo suricata -c /etc/suricata/suricata.yaml -r <location_of_pcapfile>
```
Per controllare gli avvisi risultanti, leggere il file fast.log:
```
tail -f /var/log/suricata/fast.log
```

### <a name="set-up-the-elastic-stack"></a>Configurare Elastic Stack

Anche se i log generati da Suricata contengono informazioni importanti su quanto avviene nella rete, la lettura e la comprensione di questi file di log non sono molto semplici. Connettendo Suricata con Elastic Stack, è possibile creare un dashboard Kibana che consente di cercare, creare grafici, analizzare e derivare informazioni dettagliate dai log.

#### <a name="install-elasticsearch"></a>Installare Elasticsearch

1. Elastic Stack versione 5.0 e successive richiede Java 8. Eseguire il comando `java -version` per controllare la versione in uso. Se Java non è installato, vedere la documentazione sul [sito Web Oracle](http://docs.oracle.com/javase/8/docs/technotes/guides/install/install_overview.html)
1. Scaricare il pacchetto binario corretto per il proprio sistema:

    ```
    curl -L -O https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-5.2.0.deb
    sudo dpkg -i elasticsearch-5.2.0.deb
    sudo /etc/init.d/elasticsearch start
    ```

    Per altri metodi di installazione, vedere [Elasticsearch Installation](https://www.elastic.co/guide/en/beats/libbeat/5.2/elasticsearch-installation.html) (Installazione di Elasticsearch)

1. Verificare che Elasticsearch sia in esecuzione con questo comando:

    ```
    curl http://127.0.0.1:9200
    ```

    La risposta visualizzata sarà simile a questa:

    ```
    {
    "name" : "Angela Del Toro",
    "cluster_name" : "elasticsearch",
    "version" : {
        "number" : "5.2.0",
        "build_hash" : "8ff36d139e16f8720f2947ef62c8167a888992fe",
        "build_timestamp" : "2016-01-27T13:32:39Z",
        "build_snapshot" : false,
        "lucene_version" : "6.1.0"
    },
    "tagline" : "You Know, for Search"
    }
    ```

Per altre istruzioni sull'installazione di Elasticsearch, vedere la pagina [Installation](https://www.elastic.co/guide/en/elasticsearch/reference/5.2/_installation.html) (Installazione)

### <a name="install-logstash"></a>Installare Logstash

1. Per installare Logstash, eseguire questi comandi:

    ```
    curl -L -O https://artifacts.elastic.co/downloads/logstash/logstash-5.2.0.deb
    sudo dpkg -i logstash-5.2.0.deb
    ```
1. Successivamente è necessario configurare Logstash per la lettura dall'output del file eve.json. Creare un file logstash.conf usando:

    ```
    sudo touch /etc/logstash/conf.d/logstash.conf
    ```

1. Aggiungere il contenuto seguente al file (verificare che il percorso al file eve.json sia corretto):

    ```ruby
    input {
    file {
        path => ["/var/log/suricata/eve.json"]
        codec =>  "json"
        type => "SuricataIDPS"
    }

    }

    filter {
    if [type] == "SuricataIDPS" {
        date {
        match => [ "timestamp", "ISO8601" ]
        }
        ruby {
        code => "
            if event.get('[event_type]') == 'fileinfo'
            event.set('[fileinfo][type]', event.get('[fileinfo][magic]').to_s.split(',')[0])
            end
        "
        }

        ruby{
        code => "
            if event.get('[event_type]') == 'alert'
            sp = event.get('[alert][signature]').to_s.split(' group ')
            if (sp.length == 2) and /\A\d+\z/.match(sp[1])
                event.set('[alert][signature]', sp[0])
            end
            end
            "
        }
    }

    if [src_ip]  {
        geoip {
        source => "src_ip"
        target => "geoip"
        #database => "/opt/logstash/vendor/geoip/GeoLiteCity.dat"
        add_field => [ "[geoip][coordinates]", "%{[geoip][longitude]}" ]
        add_field => [ "[geoip][coordinates]", "%{[geoip][latitude]}"  ]
        }
        mutate {
        convert => [ "[geoip][coordinates]", "float" ]
        }
        if ![geoip.ip] {
        if [dest_ip]  {
            geoip {
            source => "dest_ip"
            target => "geoip"
            #database => "/opt/logstash/vendor/geoip/GeoLiteCity.dat"
            add_field => [ "[geoip][coordinates]", "%{[geoip][longitude]}" ]
            add_field => [ "[geoip][coordinates]", "%{[geoip][latitude]}"  ]
            }
            mutate {
            convert => [ "[geoip][coordinates]", "float" ]
            }
        }
        }
    }
    }

    output {
    elasticsearch {
        hosts => "localhost"
    }
    }
    ```

1. Verificare di assegnare le autorizzazioni corrette al file eve.json in modo che Logstash possa inserire il file.
    
    ```
    sudo chmod 775 /var/log/suricata/eve.json
    ```

1. Per avviare Logstash, eseguire il comando:

    ```
    sudo /etc/init.d/logstash start
    ```

Per altre istruzioni sull'installazione di Logstash, vedere la [documentazione ufficiale](https://www.elastic.co/guide/en/beats/libbeat/5.2/logstash-installation.html)

### <a name="install-kibana"></a>Installare Kibana

1. Eseguire questi comandi per installare Kibana:

    ```
    curl -L -O https://artifacts.elastic.co/downloads/kibana/kibana-5.2.0-linux-x86_64.tar.gz
    tar xzvf kibana-5.2.0-linux-x86_64.tar.gz

    ```
1. Per eseguire Kibana, usare questi comandi:

    ```
    cd kibana-5.2.0-linux-x86_64/
    ./bin/kibana
    ```

1. Per visualizzare l'interfaccia Web di Kibana, passare a `http://localhost:5601`
1. Per questo scenario, il modello di indice usato per i log di Suricata è "logstash-*"

1. Per visualizzare il dashboard Kibana in remoto, creare una regola dei gruppi di sicurezza di rete in ingresso che consenta l'accesso alla **porta 5601**.

### <a name="create-a-kibana-dashboard"></a>Creare un dashboard Kibana

Per questo articolo, è stato fornito un dashboard di esempio per visualizzare tendenze e dettagli degli avvisi.

1. Scaricare il file del dashboard [qui](https://aka.ms/networkwatchersuricatadashboard), il file di visualizzazione [qui](https://aka.ms/networkwatchersuricatavisualization) e il file della ricerca salvato [qui](https://aka.ms/networkwatchersuricatasavedsearch).

1. Nella scheda **Management** (Gestione) di Kibana passare a **Saved Objects** (Oggetti salvati) e importare tutti e tre i file. Dalla scheda **Dashboard** è quindi possibile aprire e caricare il dashboard di esempio.

È anche possibile creare visualizzazioni e dashboard personalizzati per le metriche a cui si è interessati. Per altre informazioni sulla creazione di visualizzazioni Kibana, vedere la [documentazione ufficiale](https://www.elastic.co/guide/en/kibana/current/visualize.html) di Kibana.

![Dashboard Kibana][2]

### <a name="visualize-ids-alert-logs"></a>Visualizzare i log di avvisi IDS

Il dashboard di esempio offre diverse visualizzazioni dei log di avvisi di Suricata:

1. Alerts by GeoIP (Avvisi per IP geografico): mappa che illustra la distribuzione degli avvisi per paese di origine in base alla località geografica (determinata dall'IP)

    ![IP geografico][3]

1. Top 10 Alerts (Primi 10 avvisi): riepilogo dei 10 avvisi attivati più di frequente e relativa descrizione. Facendo clic su un singolo avviso, si accede alle informazioni del dashboard relative a quell'avviso specifico.

    ![Immagine 4][4]

1. Number of Alerts (Numero di avvisi): conteggio totale degli avvisi attivati dal set di regole

    ![Immagine 5][5]

1. Top 20 Source/Destination IPs/Ports (Primi 20 IP/porte di origine/destinazione): grafici a torta che illustrano i primi 20 IP e porte in cui sono stati attivati avvisi. È possibile filtrare specifici IP/porte per visualizzare quanti avvisi e di quale tipo sono stati attivati.

    ![Immagine 6][6]

1. Alert Summary (Riepilogo avvisi): tabella che riepiloga specifici dettagli di ogni singolo avviso. È possibile personalizzare questa tabella per visualizzare altri parametri di interesse per ogni avviso.

    ![Immagine 7][7]

Per altri documenti sulla creazione di visualizzazioni e dashboard personalizzati, vedere la [documentazione ufficiale di Kibana](https://www.elastic.co/guide/en/kibana/current/introduction.html).

## <a name="conclusion"></a>Conclusioni

Combinando le acquisizioni di pacchetti fornite da Network Watcher e da strumenti IDS open source come Suricata, è possibile eseguire il rilevamento di intrusioni di rete per svariate minacce. Questi dashboard consentono di trovare rapidamente tendenze e anomalie nella rete, oltre che esaminare i dati per trovare le cause radice degli avvisi, ad esempio agenti utenti malintenzionati o porte vulnerabili. Con i dati estratti, è possibile prendere decisioni informate su come reagire e proteggere la rete da qualsiasi tentativo di intrusione dannoso e creare regole per impedire intrusioni future nella rete.

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come attivare le acquisizioni di pacchetti in base agli avvisi, vedere [Use packet capture to do proactive network monitoring with Azure Functions](network-watcher-alert-triggered-packet-capture.md) (Usare l'acquisizione di pacchetti per eseguire il monitoraggio proattivo della rete con Funzioni di Azure)

Per informazioni su come visualizzare i log dei flussi dei gruppi di sicurezza di rete con Power BI, vedere [Visualizzare i log dei flussi dei gruppi di sicurezza di rete con Power BI](network-watcher-visualize-nsg-flow-logs-power-bi.md)



<!-- images -->
[1]: ./media/network-watcher-intrusion-detection-open-source-tools/figure1.png
[2]: ./media/network-watcher-intrusion-detection-open-source-tools/figure2.png
[3]: ./media/network-watcher-intrusion-detection-open-source-tools/figure3.png
[4]: ./media/network-watcher-intrusion-detection-open-source-tools/figure4.png
[5]: ./media/network-watcher-intrusion-detection-open-source-tools/figure5.png
[6]: ./media/network-watcher-intrusion-detection-open-source-tools/figure6.png
[7]: ./media/network-watcher-intrusion-detection-open-source-tools/figure7.png
