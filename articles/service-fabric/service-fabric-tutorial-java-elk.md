---
title: Monitorare le applicazioni in Service Fabric usando ELK in Azure | Microsoft Docs
description: In questa esercitazione si apprenderà come configurare ELK e monitorare le applicazioni di Service Fabric.
services: service-fabric
documentationcenter: java
author: suhuruli
manager: msfussell
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: java
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/26/2018
ms.author: suhuruli
ms.custom: mvc
ms.openlocfilehash: 689207339db0250d42fc64c33f43c42c18317d41
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/21/2019
ms.locfileid: "58313006"
---
# <a name="tutorial-monitor-your-service-fabric-applications-using-elk"></a>Esercitazione: Monitorare le applicazioni di Service Fabric con ELK

Questa è la quarta di una serie di esercitazioni. Illustra come usare ELK (Elasticsearch, Logstash e Kibana) per monitorare le applicazioni di Service Fabric in esecuzione in Azure.

Nella quarta parte della serie si apprenderà come:
> [!div class="checklist"]
> * Configurare il server ELK in Azure
> * Configurare Logstash per la ricezione di log da Hub eventi
> * Visualizzare i log di piattaforme e applicazioni in Kibana

In questa serie di esercitazioni si apprenderà come:
> [!div class="checklist"]
> * [Compilare un'applicazione Reliable Services di Service Fabric per Java](service-fabric-tutorial-create-java-app.md)
> * [Distribuire l'applicazione ed eseguirne il debug in un cluster locale](service-fabric-tutorial-debug-log-local-cluster.md)
> * [Distribuire l'applicazione in un cluster di Azure](service-fabric-tutorial-java-deploy-azure.md)
> * Configurare il monitoraggio e la diagnostica per l'applicazione
> * [Configurare CI/CD](service-fabric-tutorial-java-jenkins.md)

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare questa esercitazione:

* Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Configurare l'applicazione per l'invio di log al percorso specificato nella [seconda parte](service-fabric-tutorial-debug-log-local-cluster.md).
* Completare la [terza parte](service-fabric-tutorial-java-deploy-azure.md) e configurare un cluster di Service Fabric in esecuzione per l'invio di log a Hub eventi.
* I criteri di Hub eventi con autorizzazione 'Listen' e la chiave primaria associata della terza serie.

## <a name="download-the-voting-sample-application"></a>Scaricare l'applicazione di voto di esempio

Se l'applicazione di voto di esempio non è stata compilata nella [parte 1 di questa serie di esercitazioni](service-fabric-tutorial-create-java-app.md), è possibile scaricarla. In una finestra di comando eseguire il comando seguente per clonare il repository dell'app di esempio nel computer locale.

```bash
git clone https://github.com/Azure-Samples/service-fabric-java-quickstart
```

## <a name="create-an-elk-server-in-azure"></a>Creare un server ELK in Azure

Per questa esercitazione è possibile usare un ambiente ELK preconfigurato. Se ne è già disponibile uno, passare alla sezione **Configurare Logstash**. Se non è disponibile un ambiente, crearne uno in Azure seguendo questa procedura.

1. Creare un server ELK certificato da [Bitnami](https://ms.portal.azure.com/#create/bitnami.elk4-6) in Azure. Ai fini dell'esercitazione non è necessario seguire specifiche particolari per la creazione di questo server.

2. Accedere alla risorsa nel portale di Azure e passare alla scheda **Diagnostica di avvio** nella sezione **Supporto e risoluzione dei problemi**. Fare quindi clic sulla scheda **Log seriale**.

    ![Diagnostica di avvio](./media/service-fabric-tutorial-java-elk/bootdiagnostics.png)
3. Eseguire una ricerca nei log per trovare la password necessaria per accedere all'istanza Kibana. Il frammento di codice sarà simile al seguente:

    ```bash
    [   25.932766] bitnami[1496]: #########################################################################
    [   25.948656] bitnami[1496]: #                                                                       #
    [   25.962448] bitnami[1496]: #        Setting Bitnami application password to '[PASSWORD]'           #
    [   25.978137] bitnami[1496]: #        (the default application username is 'user')                   #
    [   26.004770] bitnami[1496]: #                                                                       #
    [   26.029413] bitnami[1496]: #########################################################################
    ```

4. Fare clic su sul pulsante Connetti nella pagina Panoramica del server nel portale di Azure per ottenere i dati di accesso.

    ![Connessione alla macchina virtuale](./media/service-fabric-tutorial-java-elk/vmconnection.png)

5. Usare SSH per connettersi al server che ospita l'immagine ELK con il comando seguente

    ```bash
    ssh [USERNAME]@[CONNECTION-IP-OF-SERVER]

    Example: ssh testaccount@104.40.63.157
    ```

## <a name="set-up-elk"></a>Configurare ELK

1. Il primo passaggio consiste nel caricare l'ambiente ELK

    ```bash
    sudo /opt/bitnami/use_elk
    ```

2. Se si usa un ambiente esistente, è necessario eseguire questo comando per arrestare il servizio Logstash

    ```bash
    sudo /opt/bitnami/ctlscript.sh stop logstash
    ```

3. Eseguire questo comando per installare il plug-in Logstash per Hub eventi.

    ```bash
    logstash-plugin install logstash-input-azureeventhub
    ```

4. Creare o modificare il file di configurazione Logstash esistente con il contenuto seguente: In caso di creazione del file, se si usa l'immagine ELK Bitnami in Azure è necessario creare il file in ```/opt/bitnami/logstash/conf/access-log.conf```.

    ```json
    input
    {
        azureeventhub
        {
            key => "[RECEIVER-POLICY-KEY-FOR-EVENT-HUB]"
            username => "[RECEIVER-POLICY-NAME]"
            namespace => "[EVENTHUB-NAMESPACENAME]"
            eventhub => "[EVENTHUB-NAME]"
            partitions => 4
        }
    }

    output {
         elasticsearch {
             hosts => [ "127.0.0.1:9200" ]
         }
     }
    ```

5. Per verificare la configurazione, eseguire questo comando:

    ```bash
    /opt/bitnami/logstash/bin/logstash -f /opt/bitnami/logstash/conf/ --config.test_and_exit
    ```

6. Avviare il servizio Logstash

    ```bash
    sudo /opt/bitnami/ctlscript.sh start logstash
    ```

7. Controllare Elasticsearch per confermare la ricezione dei dati

    ```bash
    curl 'localhost:9200/_cat/indices?v'
    ```

8. Accedere al dashboard Kibana all'indirizzo **http:\//IP-SERVER** e immettere il nome utente e la password per Kibana. Se è stata usata l'immagine ELK in Azure, il nome utente predefinito è 'user' e la password è quella ottenuta dalla **diagnostica di avvio**.

    ![Kibana](./media/service-fabric-tutorial-java-elk/kibana.png)

## <a name="next-steps"></a>Passaggi successivi

Questa esercitazione illustra come:

> [!div class="checklist"]
> * Rendere operativo il server ELK in Azure
> * Configurare il server per la ricezione delle informazioni di diagnostica dal cluster di Service Fabric

Passare all'esercitazione successiva:
> [!div class="nextstepaction"]
> [Configurare l'integrazione continua e la distribuzione continua con Jenkins](service-fabric-tutorial-java-jenkins.md)
