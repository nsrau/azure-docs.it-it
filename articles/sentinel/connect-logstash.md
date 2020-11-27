---
title: Connettere le origini dati tramite logstash ad Azure Sentinel | Microsoft Docs
description: Informazioni su come usare logstash per l'invio di log da origini dati esterne a Sentinel di Azure.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/10/2020
ms.author: yelevin
ms.openlocfilehash: 63b9d74fbbb1a79dd4f3d3e7c5fb094a372282e0
ms.sourcegitcommit: 5e2f5efba1957ba40bd951c3dcad42f4a00734ff
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/27/2020
ms.locfileid: "96299633"
---
# <a name="use-logstash-to-connect-data-sources-to-azure-sentinel"></a>Usare logstash per connettere le origini dati ad Azure Sentinel

> [!IMPORTANT]
> L'inserimento di dati con il plug-in di output logstash è attualmente disponibile in anteprima pubblica. Questa funzionalità viene fornita senza un contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Usando il nuovo plug-in di output di Azure Sentinel per il **motore di raccolta dei dati di logstash**, è ora possibile inviare qualsiasi tipo di log che si vuole tramite logstash direttamente nell'area di lavoro log Analytics in Sentinel di Azure. I log verranno inviati a una tabella personalizzata che verrà definita usando il plug-in di output.

Per ulteriori informazioni sull'utilizzo del motore di raccolta dati logstash, vedere [Introduzione a logstash](https://www.elastic.co/guide/en/logstash/current/getting-started-with-logstash.html).

## <a name="overview"></a>Panoramica

### <a name="architecture-and-background"></a>Architettura e sfondo

![Diagramma dell'architettura di log stash.](./media/connect-logstash/logstash-architecture.png)

Il motore logstash è costituito da tre componenti:

- Plug-in di input: raccolta personalizzata di dati da diverse origini.
- Plug-in di filtro: manipolazione e normalizzazione dei dati in base ai criteri specificati.
- Plug-in di output: invio personalizzato di dati raccolti ed elaborati a diverse destinazioni.

> [!NOTE]
> Azure Sentinel supporta solo il plug-in di output fornito. Non supporta plug-in di output di terze parti per Sentinel di Azure o qualsiasi altro plug-in logstash di qualsiasi tipo.

Il plug-in di output di Azure Sentinel per logstash invia dati in formato JSON all'area di lavoro Log Analytics, usando l'API REST dell'agente di raccolta dati HTTP Log Analytics. I dati vengono inseriti in log personalizzati.

- Altre informazioni sull' [API REST di log Analytics](/rest/api/loganalytics/create-request).
- Altre informazioni sui [log personalizzati](../azure-monitor/platform/data-sources-custom-logs.md).

## <a name="deploy-the-azure-sentinel-output-plugin-in-logstash"></a>Distribuire il plug-in di output di Azure Sentinel in logstash

### <a name="step-1-installation"></a>Passaggio 1: installazione

Il plug-in di output di Azure Sentinel è disponibile nella raccolta logstash.

- Seguire le istruzioni riportate nel documento logstash [uso dei plug](https://www.elastic.co/guide/en/logstash/current/working-with-plugins.html) -in per installare il plug-in **_[Microsoft-logstash-output-Azure-loganalytics](https://github.com/Azure/Azure-Sentinel/tree/master/DataConnectors/microsoft-logstash-output-azure-loganalytics)_* _.
   
- Se il sistema logstash non dispone di accesso a Internet, seguire le istruzioni nel documento di [gestione dei plug](https://www.elastic.co/guide/en/logstash/current/offline-plugins.html) -in logstash offline per preparare e usare un pacchetto di plug-in offline. Questa operazione richiederà la creazione di un altro sistema logstash con accesso a Internet.

### <a name="step-2-configuration"></a>Passaggio 2: configurazione

Usare le informazioni nella struttura logstash [di un documento del file di configurazione](https://www.elastic.co/guide/en/logstash/current/configuration-file-structure.html) e aggiungere il plug-in di output di Azure Sentinel alla configurazione con le chiavi e i valori seguenti. La sintassi corretta del file di configurazione viene visualizzata dopo la tabella.

| Nome campo | Tipo di dati | Descrizione |
|----------------|---------------|-----------------|
| `workspace_id` | string | Immettere il GUID dell'ID dell'area di lavoro. _ |
| `workspace_key` | string | Immettere il GUID della chiave primaria dell'area di lavoro. * |
| `custom_log_table_name` | string | Consente di impostare il nome della tabella in cui verranno inseriti i log. È possibile configurare solo un nome di tabella per ogni plug-in di output. La tabella dei log verrà visualizzata in Sentinel di Azure in **log**, in **tabelle** nella categoria **log personalizzati** con un `_CL` suffisso. |
| `endpoint` | string | Campo facoltativo. Per impostazione predefinita, si tratta dell'endpoint Log Analytics. Utilizzare questo campo per impostare un endpoint alternativo. |
| `time_generated_field` | string | Campo facoltativo. Questa proprietà esegue l'override del campo predefinito **TimeGenerated** in log Analytics. Immettere il nome del campo timestamp nell'origine dati. I dati nel campo devono essere conformi al formato ISO 8601 ( `YYYY-MM-DDThh:mm:ssZ` ) |
| `key_names` | array | Immettere un elenco di Log Analytics campi dello schema di output. Ogni elemento dell'elenco deve essere racchiuso tra virgolette singole e gli elementi separati da virgole e l'intero elenco racchiuso tra parentesi quadre. Vedi l'esempio seguente. |
| `plugin_flush_interval` | Numero | Campo facoltativo. Impostare per definire l'intervallo massimo (in secondi) tra le trasmissioni dei messaggi a Log Analytics. Il valore predefinito è 5. |
    | `amount_resizing` | boolean | True o false. Abilita o Disabilita il meccanismo di scalabilità automatica, che regola le dimensioni del buffer del messaggio in base al volume dei dati di log ricevuti. |
| `max_items` | Numero | Campo facoltativo. Si applica solo se è `amount_resizing` impostato su "false". Utilizzare per impostare un limite per le dimensioni del buffer dei messaggi (nei record). Il valore predefinito è 2000.  |

\* È possibile trovare l'ID dell'area di lavoro e la chiave primaria nella risorsa dell'area di lavoro, in **Gestione agenti**.

#### <a name="sample-configurations"></a>Configurazioni di esempio

Di seguito sono riportate alcune configurazioni di esempio che usano alcune opzioni diverse.

- Una configurazione di base che usa una pipe di input filebeat:

   ```ruby
    input {
        beats {
            port => "5044"
        }
    }
    filter {
    }
    output {
        microsoft-logstash-output-azure-loganalytics {
          workspace_id => "4g5tad2b-a4u4-147v-a4r7-23148a5f2c21" # <your workspace id>
          workspace_key => "u/saRtY0JGHJ4Ce93g5WQ3Lk50ZnZ8ugfd74nk78RPLPP/KgfnjU5478Ndh64sNfdrsMni975HJP6lp==" # <your workspace key>
          custom_log_table_name => "tableName"
        }
    }
   ```
    
- Una configurazione di base che usa una pipe di input TCP:

   ```ruby
    input {
        tcp {
            port => "514"
            type => syslog #optional, will effect log type in table
        }
    }
    filter {
    }
    output {
        microsoft-logstash-output-azure-loganalytics {
          workspace_id => "4g5tad2b-a4u4-147v-a4r7-23148a5f2c21" # <your workspace id>
          workspace_key => "u/saRtY0JGHJ4Ce93g5WQ3Lk50ZnZ8ugfd74nk78RPLPP/KgfnjU5478Ndh64sNfdrsMni975HJP6lp==" # <your workspace key>
          custom_log_table_name => "tableName"
        }
    }
   ```
  
- Una configurazione avanzata:

   ```ruby    
    input {
        tcp {
            port => 514
            type => syslog
        }
    }
    filter {
        grok {
            match => { "message" => "<%{NUMBER:PRI}>1 (?<TIME_TAG>[0-9]{4}-[0-9]{1,2}-[0-9]{1,2}T[0-9]{1,2}:[0-9]{1,2}:[0-9]{1,2})[^ ]* (?<HOSTNAME>[^ ]*) %{GREEDYDATA:MSG}" }
        }
    }
    output {
        microsoft-logstash-output-azure-loganalytics {
            workspace_id => "<WS_ID>"
            workspace_key => "${WS_KEY}"
            custom_log_table_name => "logstashCustomTable"
            key_names => ['PRI','TIME_TAG','HOSTNAME','MSG']
            plugin_flush_interval => 5
        }
    } 
   ```

   > [!NOTE]
   > Visitare il [repository GitHub](https://github.com/Azure/Azure-Sentinel/tree/master/DataConnectors/microsoft-logstash-output-azure-loganalytics) per il plug-in di output per ulteriori informazioni sulle impostazioni di funzionamento, configurazione e prestazioni interne.

### <a name="step-3-restart-logstash"></a>Passaggio 3: riavviare logstash

### <a name="step-4-view-incoming-logs-in-azure-sentinel"></a>Passaggio 4: visualizzare i log in ingresso in Sentinel di Azure

1. Verificare che i messaggi vengano inviati al plug-in di output.

1. Nel menu di navigazione di Azure Sentinel fare clic su **log**. Sotto l'intestazione **Tables** espandere la categoria **Custom logs** . Trovare e fare clic sul nome della tabella specificata (con un `_CL` suffisso) nella configurazione.

   :::image type="content" source="./media/connect-logstash/logstash-custom-logs-menu.png" alt-text="Screenshot dei log personalizzati Stash log.":::

1. Per visualizzare i record nella tabella, eseguire una query sulla tabella utilizzando il nome della tabella come schema.

   :::image type="content" source="./media/connect-logstash/logstash-custom-logs-query.png" alt-text="Screenshot di una query dei log personalizzati stash di log.":::

## <a name="monitor-output-plugin-audit-logs"></a>Monitorare i log di controllo del plug-in di output

Per monitorare la connettività e l'attività del plug-in di output di Azure Sentinel, abilitare il file di log logstash appropriato. Per il percorso del file di log, vedere il documento di [layout della directory logstash](https://www.elastic.co/guide/en/logstash/current/dir-layout.html#dir-layout) .

Se non vengono visualizzati dati in questo file di log, generare e inviare alcuni eventi localmente (tramite i plug-in di input e filtro) per assicurarsi che il plug-in di output riceva i dati. Azure Sentinel supporterà solo i problemi relativi al plug-in di output.

## <a name="next-steps"></a>Passaggi successivi

In questo documento si è appreso come usare logstash per connettere origini dati esterne a Sentinel di Azure. Per altre informazioni su Azure Sentinel, vedere gli articoli seguenti:
- Informazioni su come [ottenere visibilità sui dati e sulle potenziali minacce](quickstart-get-visibility.md).
- Inizia a rilevare le minacce con Azure Sentinel, usando regole [predefinite](tutorial-detect-threats-built-in.md) o [personalizzate](tutorial-detect-threats-custom.md) .
