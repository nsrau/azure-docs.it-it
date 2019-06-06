---
title: Inserire dati da Logstash in Esplora dati di Azure
description: In questo articolo descrive come inserire (caricare) i dati in Esplora dati di Azure da Logstash
author: tamirkamara
ms.author: takamara
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: 86f6732cbf2409d3c79a3d7709100e8af24988a0
ms.sourcegitcommit: 600d5b140dae979f029c43c033757652cddc2029
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/04/2019
ms.locfileid: "66494532"
---
# <a name="ingest-data-from-logstash-to-azure-data-explorer"></a>Inserire dati da Logstash in Esplora dati di Azure

[Logstash](https://www.elastic.co/products/logstash) è una pipeline di elaborazione dati lato server e open source che inserisce i dati da più origini contemporaneamente, li trasforma e quindi li invia all'accantonamento preferito. In questo articolo, si saranno inviare i dati a Esplora dati di Azure, che è un servizio di esplorazione dei dati rapido e a scalabilità elevata per dati di log e dati di telemetria. Inizialmente si creerà una tabella e il mapping dei dati in un cluster di test, quindi si istruirà Logstash a inviare i dati nella tabella e convalidare i risultati.

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione, creare un [account di Azure gratuito](https://azure.microsoft.com/free/) prima di iniziare.
* Un [cluster e un database di test](create-cluster-database-portal.md) di Esplora dati di Azure
* Istruzioni per l'installazione di [Logstash versione 6+](https://www.elastic.co/guide/en/logstash/current/installing-logstash.html)

## <a name="create-a-table"></a>Creare una tabella

Dopo aver creato un cluster e un database, è possibile creare una tabella.

1. Eseguire il comando seguente nella finestra della query di database per creare una tabella:

    ```Kusto
    .create table logs (timestamp: datetime, message: string)
    ```

1. Eseguire il comando seguente per verificare che la nuova tabella `logs` sia stata creata e che sia vuota:
    ```Kusto
    logs
    | count
    ```

## <a name="create-a-mapping"></a>Creare un mapping

Il mapping viene usato da Esplora dati di Azure per trasformare i dati in ingresso nello schema della tabella di destinazione. Il comando seguente crea un nuovo mapping denominato `basicmsg` che estrae le proprietà dai dati JSON in ingresso, come indicato dal `path`, e le inserisce nella `column`.

Eseguire il comando seguente nella finestra di query:

```Kusto
.create table logs ingestion json mapping 'basicmsg' '[{"column":"timestamp","path":"$.@timestamp"},{"column":"message","path":"$.message"}]'
```

## <a name="install-the-logstash-output-plugin"></a>Installare il plug-in output Logstash

Il plug-in di output Logstash comunica con Esplora dati di Azure e invia i dati al servizio.
Eseguire il comando seguente all'interno della directory radice di Logstash per installare il plug-in:

```sh
bin/logstash-plugin install logstash-output-kusto
```

## <a name="configure-logstash-to-generate-a-sample-dataset"></a>Configurare Logstash per generare un set di dati di esempio

Logstash può generare eventi di esempio che possono essere usati per verificare una pipeline end-to-end.
Se si usa già Logstash e si ha accesso al proprio flusso di eventi, passare alla sezione successiva. 

> [!NOTE]
> Se si usano i propri dati, modificare la tabella e gli oggetti di mapping definiti nei passaggi precedenti.

1. Modificare un nuovo file di testo che conterrà le impostazioni della pipeline obbligatorie (usando vi):

    ```sh
    vi test.conf
    ```

1. Incollare le impostazioni seguenti che istruiranno Logstash a generare 1000 eventi di test:

    ```ruby
    input {
        stdin { }
        generator {
            message => "Test Message 123"
            count => 1000
        }
    }
    ```

Questa configurazione include anche il plug-in di input `stdin` che consentirà di scrivere più messaggi autonomamente (assicurarsi di usare *Invio* per inviarli nella pipeline).

## <a name="configure-logstash-to-send-data-to-azure-data-explorer"></a>Configurare Logstash in moda da inviare i dati a Esplora dati di Azure

Incollare le impostazioni seguenti nello stesso file di configurazione usato nel passaggio precedente. Sostituire tutti i segnaposto con i valori pertinenti per l'installazione. Per altre informazioni, vedere [Creare un'applicazione AAD](/azure/kusto/management/access-control/how-to-provision-aad-app). 

```ruby
output {
    kusto {
            path => "/tmp/kusto/%{+YYYY-MM-dd-HH-mm-ss}.txt"
            ingest_url => "https://ingest-<cluster name>.kusto.windows.net/"
            app_id => "<application id>"
            app_key => "<application key/secret>"
            app_tenant => "<tenant id>"
            database => "<database name>"
            table => "<target table>" # logs as defined above
            mapping => "<mapping name>" # basicmsg as defined above
    }
}
```

| Nome parametro | Descrizione |
| --- | --- |
| **path** | Il plug-in Logstash scrive gli eventi in file temporanei prima di inviarli a Esplora dati di Azure. Questo parametro include un percorso in cui devono essere scritti i file e un'espressione di data per la rotazione dei file per attivare un'operazione di caricamento nel servizio Esplora dati di Azure.|
| **ingest_url** | Endpoint Kusto per le comunicazioni relative l'inserimento.|
| **app_id**,  **app_key** e **app_tenant**| Credenziali necessarie per connettersi a Esplora dati di Azure. Assicurarsi di usare un'applicazione con privilegi di inserimento. |
| **database**| Nome del database per inserire gli eventi. |
| **tabella** | Nome tabella di destinazione per posizionare gli eventi. |
| **mapping** | Il mapping viene usato per eseguire il mapping di una stringa json di eventi in ingresso nel formato di riga corretto (definisce quale proprietà inserire in quale colonna). |

## <a name="run-logstash"></a>Eseguire Logstash

A questo punto è possibile eseguire Logstash e testare le impostazioni.

1. Nella directory radice di Logstash, eseguire il comando seguente:

    ```sh
    bin/logstash -f test.conf
    ```

    Le informazioni saranno visualizzate sullo schermo oltre ai 1000 messaggi generati dalla configurazione di esempio. A questo punto, è possibile immettere anche altri messaggi manualmente.

1. Dopo alcuni minuti, eseguire la seguente query di Esplora dati per visualizzare i messaggi nella tabella definita:

    ```Kusto
    logs
    | order by timestamp desc
    ```

1. Selezionare CTRL+C per uscire da Logstash

## <a name="clean-up-resources"></a>Pulire le risorse

Eseguire il comando seguente nel database per pulire la tabella `logs`:

```Kusto
.drop table logs
```

## <a name="next-steps"></a>Passaggi successivi

* [Scrivere query](write-queries.md)