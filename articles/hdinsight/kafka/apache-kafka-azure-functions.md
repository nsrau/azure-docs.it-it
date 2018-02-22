---
title: Usare Funzioni di Azure per inviare dati a Kafka in HDInsight | Microsoft Docs
description: Informazioni su come usare una funzione di Azure per scrivere dati in Kafka in HDInsight.
services: hdinsight
documentationcenter: 
author: Blackmist
manager: cgronlun
editor: cgronlun
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: 
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/09/2018
ms.author: larryfr
ms.openlocfilehash: c1c03cfcbcb7e0bfdb4a631b9e2ae568f0684069
ms.sourcegitcommit: b32d6948033e7f85e3362e13347a664c0aaa04c1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/13/2018
---
# <a name="use-kafka-on-hdinsight-from-an-azure-function-app"></a>Usare Kafka in HDInsight da un'app per le funzioni di Azure

Informazioni su come creare un'app per le funzioni di Azure che invia dati a Kafka in HDInsight.

[Funzioni di Azure](https://docs.microsoft.com/azure/azure-functions/) è un servizio di calcolo senza server. Consente di eseguire codice on demand senza dover effettuare il provisioning o la gestione dell'infrastruttura in modo esplicito.

[Apache Kafka](https://kafka.apache.org) è una piattaforma di streaming open source distribuita che può essere usata per compilare applicazioni e pipeline di dati in streaming in tempo reale. Kafka offre anche una funzionalità di broker di messaggi simile a una coda di messaggi, dove è possibile pubblicare e sottoscrivere flussi dei dati denominati. Kafka in HDInsight fornisce un servizio gestito, a scalabilità e disponibilità elevate nel cloud di Microsoft Azure.

Kafka in HDInsight non fornisce un'API sulla rete Internet pubblica. Per pubblicare o utilizzare dati da Kafka, è necessario connettersi al cluster Kafka usando una rete virtuale di Azure. Funzioni di Azure consente di creare in tutta comodità un endpoint pubblico che esegue il push dei dati in Kafka in HDInsight tramite un gateway di rete virtuale.

> [!NOTE]
> Questo documento è incentrato sui passaggi necessari per consentire a Funzioni di Azure di comunicare con Kafka in HDInsight. L'esempio in sé è solo un producer Kafka di base con cui si dimostra che la configurazione funziona.

## <a name="prerequisites"></a>prerequisiti

* Un'app per le funzioni di Azure. Per altre informazioni, vedere la documentazione [Creare la prima funzione](../../azure-functions/functions-create-first-azure-function.md).

* Una rete virtuale di Azure. Per usare Funzioni di Azure, la rete virtuale deve usare uno degli intervalli IP seguenti:

    * 10.0.0.0-10.255.255.255
    * 172.16.0.0-172.31.255.255
    * 192.168.0.0-192.168.255.255

    Per altre informazioni, vedere il documento [Integrare l'app in una rete virtuale di Azure](../../app-service/web-sites-integrate-with-vnet.md).

* Un cluster Kafka in HDInsight. Per informazioni sulla creazione di un cluster Kafka in HDInsight, vedere il documento [Creare un cluster Kafka](apache-kafka-get-started.md).

    > [!IMPORTANT]
    > Durante la configurazione del cluster, è necessario usare il passaggio __Impostazioni avanzate__ per selezionare la rete virtuale di Azure e la subnet usata da HDInsight. Selezionare la rete virtuale e la subnet create nel passaggio precedente.

    Per altre informazioni su Kafka e sulle reti virtuali, vedere il documento [Connettersi a Kafka tramite una rete virtuale](apache-kafka-connect-vpn-gateway.md).

## <a name="architecture"></a>Architecture

Kafka in HDInsight è contenuto in una rete virtuale di Azure. Funzioni di Azure può comunicare con la rete virtuale usando un gateway da punto a sito. Di seguito è riportato un diagramma di questa topologia di rete:

![Architettura della connessione di Funzioni di Azure a HDInsight](./media/apache-kafka-azure-functions/kafka-azure-functions.png)

## <a name="configure-kafka"></a>Configurare Kafka

Le informazioni di questa sezione preparano il cluster Kafka ad accettare i dati dalla funzione di Azure. Vengono illustrate le azioni di configurazione seguenti:

* Annunci pubblicitari IP
* Raccolta di indirizzi IP dei broker Kafka
* Creazione di un argomento Kafka

### <a name="configure-kafka-for-ip-advertising"></a>Configurare Kafka per la pubblicità IP

Per impostazione predefinita, Zookeeper restituisce il nome di dominio dei broker di Kafka ai client. Questa configurazione non funziona senza un server DNS, perché il client (Funzioni di Azure) non riesce a risolvere i nomi per la rete virtuale. Per questa configurazione usare la procedura seguente per configurare Kafka in HDInsight affinché possa creare pubblicità per gli indirizzi IP anziché per i nomi di dominio:

1. Tramite il Web browser, aprire https://CLUSTERNAME.azurehdinsight.net. Sostituire __CLUSTERNAME__ con il nome di Kafka nel cluster HDInsight.

    Quando richiesto, usare il nome utente HTTPS e la password per il cluster. Viene visualizzata l'interfaccia utente di Ambari Web per il cluster.

2. Per visualizzare le informazioni su Kafka, selezionare __Kafka__ dall'elenco a sinistra.

    ![Elenco di servizio con Kafka evidenziato](./media/apache-kafka-azure-functions/select-kafka-service.png)

3. Per visualizzare la configurazione di Kafka, selezionare __Configs__ (Configurazioni) nella parte centrale in alto.

    ![Collegamenti Configs (Configurazioni) per Kafka](./media/apache-kafka-azure-functions/select-kafka-config.png)

4. Per trovare la configurazione __kafka-env__, immettere `kafka-env` nel campo __Filtro__ in alto a destra.

    ![Configurazione Kafka per kafka-env](./media/apache-kafka-azure-functions/search-for-kafka-env.png)

5. Per configurare Kafka affinché generi pubblicità per gli indirizzi IP, aggiungere il testo seguente nella parte inferiore del campo __kafka-env-template__:

    ```
    # Configure Kafka to advertise IP addresses instead of FQDN
    IP_ADDRESS=$(hostname -i)
    echo advertised.listeners=$IP_ADDRESS
    sed -i.bak -e '/advertised/{/advertised@/!d;}' /usr/hdp/current/kafka-broker/conf/server.properties
    echo "advertised.listeners=PLAINTEXT://$IP_ADDRESS:9092" >> /usr/hdp/current/kafka-broker/conf/server.properties
    ```

6. Per configurare l'interfaccia per cui è in ascolto Kafka immettere `listeners` nel campo __Filtro__ in alto a destra.

7. Per configurare tutte le interfacce di rete per cui Kafka è in ascolto, modificare il valore nel campo __Listener__ su `PLAINTEXT://0.0.0.0:9092`.

8. Per salvare le modifiche alla configurazione usare il pulsante __Salva__. Immettere un messaggio di testo che descrive le modifiche. Selezionare __OK__ dopo aver salvato le modifiche.

    ![Pulsante per salvare la configurazione](./media/apache-kafka-azure-functions/save-button.png)

9. Per evitare errori al riavvio di Kafka, usare il pulsante __Service Actions__ (Azioni del servizio) e selezionare __Attiva modalità di manutenzione__. Per completare questa operazione selezionare OK.

    ![Azioni di servizio, con il pulsante di attivazione manutenzione evidenziato](./media/apache-kafka-azure-functions/turn-on-maintenance-mode.png)

10. Per riavviare Kafka, utilizzare il pulsante __Riavvia__ e selezionare __Restart All Affected__ (Riavviare tutti gli elementi interessati). Confermare il riavvio, quindi usare il pulsante __OK__ dopo aver completato l'operazione.

    ![Pulsante di riavvio con Restart all affected (Riavviare tutti gli elementi interessati) evidenziato](./media/apache-kafka-azure-functions/restart-button.png)

11. Per disabilitare la modalità di manutenzione, usare il pulsante __Service Actions__ (Azioni del servizio) e selezionare __Disattiva modalità di manutenzione__. Per completare questa operazione selezionare **OK**.

### <a name="get-the-kafka-broker-ip-address"></a>Ottenere l'indirizzo IP del broker Kafka

Per recuperare il nome di dominio completo e gli indirizzi IP dei nodi nel cluster Kafka, usare uno dei metodi seguenti:

```powershell
$resourceGroupName = "The resource group that contains the virtual network used with HDInsight"

$clusterNICs = Get-AzureRmNetworkInterface -ResourceGroupName $resourceGroupName | where-object {$_.Name -like "*node*"}

$nodes = @()
foreach($nic in $clusterNICs) {
    $node = new-object System.Object
    $node | add-member -MemberType NoteProperty -name "Type" -value $nic.Name.Split('-')[1]
    $node | add-member -MemberType NoteProperty -name "InternalIP" -value $nic.IpConfigurations.PrivateIpAddress
    $node | add-member -MemberType NoteProperty -name "InternalFQDN" -value $nic.DnsSettings.InternalFqdn
    $nodes += $node
}
$nodes | sort-object Type
```

```azurecli
az network nic list --resource-group <resourcegroupname> --output table --query "[?contains(name,'node')].{NICname:name,InternalIP:ipConfigurations[0].privateIpAddress,InternalFQDN:dnsSettings.internalFqdn}"
```

Questo comando presuppone che `<resourcegroupname>` sia il nome del gruppo di risorse di Azure che contiene la rete virtuale.

Nell'elenco di nomi restituiti selezionare l'indirizzo IP di un nodo di lavoro. Il nome di dominio completo interno del nodo inizia con le lettere `wn`. Questo indirizzo IP viene usato dalla funzione per comunicare con Kafka.

### <a name="create-a-kafka-topic"></a>Creare un argomento Kafka

Kafka archivia i dati in __argomenti__. Prima di inviare i dati a Kafka da una funzione di Azure, è necessario creare la funzione.

Per creare un argomento, seguire i passaggi illustrati nel documento [Creare un cluster Kafka](apache-kafka-get-started.md).

## <a name="create-a-function-that-sends-to-kafka"></a>Creare una funzione per l'invio a Kafka

> [!NOTE]
> I passaggi di questa sezione creano una funzione di JavaScript che usa il pacchetto [kafka-node](https://www.npmjs.com/package/kafka-node) per pubblicare i dati in Kafka:

1. Creare una nuova funzione __Webhook e API__ e selezionare __JavaScript__ come linguaggio. Per altre informazioni sulla creazione di nuove funzioni, vedere il documento [Creare la prima funzione](../../azure-functions/functions-create-first-azure-function.md#create-function).

2. Usare il codice seguente come corpo della funzione:

    ```javascript
    var kafka = require('kafka-node');

    // The topic and a Kafka broker host for your HDInsight cluster
    var topic = 'test';
    var brokerHost = '10.1.0.7:9092';
    // Create the client
    var client= new kafka.KafkaClient({kafkaHost: brokerHost});

    module.exports = function (context, req) {
        // Create the producer
        var producer = new kafka.Producer(client, {requireAcks: 1});

        context.log('JavaScript HTTP trigger function processed a request.');

        if (req.query.name || (req.body && req.body.name)) {
            var name = req.query.name || req.body.name
            context.res = {
                // status: 200, /* Defaults to 200 */
                body: "Hello " + name
            };
            // Create the payload, using the name as the body
            var payloads = [
                    { topic: topic, messages: [name]}
            ];
            context.log("calling producer.send");
            // Send the data to Kafka
            producer.send(payloads, function(err, data) {
                if(err) {
                    context.log(err);
                } else {
                    context.log(data);
                }
            });
        }
        else {
            context.res = {
                status: 400,
                body: "Please pass a name on the query string or in the request body"
            };
        }
        context.done();
    };
    ```

    Sostituire `'test'` con il nome dell'argomento Kafka creato nel cluster HDInsight.

    Sostituire `10.1.0.7` con l'indirizzo IP recuperato prima. Lasciare il valore `:9092`. 9092 è la porta su cui Kafka è in ascolto.

    Fare clic sul pulsante __Save__ per salvare le modifiche apportate.

    ![Editor con il pulsante Salva](./media/apache-kafka-azure-functions/function-editor.png)

3. A destra nell'editor di funzioni selezionare __Visualizza file__. Selezionare __+ Aggiungi__ e aggiungere un nuovo file denominato `package.json`. Questo file viene usato per specificare la dipendenza dal pacchetto `kafka-node`.

    ![Aggiungere un file](./media/apache-kafka-azure-functions/add-files.png)

4. Per modificare il nuovo file, selezionare `package.json` nell'elenco __Visualizza file__. Usare il testo seguente come contenuto del file:

    ```json
    {
    "name": "kafkatest",
    "version": "1.0.0",
    "description": "",
    "main": "index.js",
    "author": "",
    "license": "ISC",
    "dependencies": {
            "kafka-node": "^2.4.1"
        }
    }
    ```

    Fare clic sul pulsante __Save__ per salvare le modifiche apportate.

5. Per installare il pacchetto `kafka-node`, usare la sezione _Versione di Node e gestione dei pacchetti_ della [Guida per gli sviluppatori JavaScript di Funzioni di Azure](../../azure-functions/functions-reference-node.md#node-version-and-package-management).

    > [!NOTE]
    > Durante l'installazione del pacchetto kafka-node è possibile che vengano visualizzati diversi errori. È possibile ignorare questi errori.

## <a name="run-the-function"></a>Eseguire la funzione

A destra nell'editor di funzioni selezionare __Test__. Lasciare le impostazioni predefinite per il test e quindi selezionare __Esegui__. Mentre viene eseguito, il test passa un parametro `name` alla funzione. Questo parametro contiene un valore `Azure`, che la funzione inserisce in Kafka.

![Screenshot della finestra di dialogo del test](./media/apache-kafka-azure-functions/function-test-dialog.png)

Per visualizzare le informazioni registrate dalla funzione durante l'esecuzione del test, selezionare __Log__ nella parte inferiore della pagina. Eseguire di nuovo il test per generare le informazioni dei log.

![Esempio dell'output dei log della funzione](./media/apache-kafka-azure-functions/function-log.png)

## <a name="verify-the-data-is-in-kafka"></a>Verificare la presenza dei dati in Kafka

Per verificare che i dati siano arrivati nell'argomento Kafka, usare le informazioni della sezione _Produrre e utilizzare record_ del documento [Creare un cluster Kafka](apache-kafka-get-started.md#produce-and-consume-records). `kafka-console-consumer` legge i dati dall'argomento e visualizza un elenco di messaggi archiviati nell'argomento.

## <a name="next-steps"></a>Passaggi successivi

Usare i collegamenti seguenti per informazioni su come usare Apache Kafka in HDInsight:

* [Get started with Kafka on HDInsight](apache-kafka-get-started.md) (Introduzione a Kafka in HDInsight)

* [Usare MirrorMaker per creare una replica di Kafka in HDInsight](apache-kafka-mirroring.md)

* [Usare Apache Storm (anteprima) con Kafka in HDInsight](../hdinsight-apache-storm-with-kafka.md)

* [Use Apache Spark with Kafka on HDInsight](../hdinsight-apache-spark-with-kafka.md) (Usare Apache Spark con Kafka in HDInsight)

* [Connect to Kafka through an Azure Virtual Network](apache-kafka-connect-vpn-gateway.md) (Connettersi a Kafka tramite una rete virtuale di Azure)