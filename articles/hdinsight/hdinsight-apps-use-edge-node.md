---
title: Usare i nodi perimetrali vuoti sui cluster Hadoop in HDInsight - Azure | Documentazione Microsoft
description: "Come aggiungere un nodo perimetrale vuoto al cluster HDInsight che può essere usato come un client, quindi testare oppure ospitare le applicazioni HDInsight."
services: hdinsight
editor: cgronlun
manager: jhubbard
author: mumian
tags: azure-portal
documentationcenter: 
ms.assetid: cdc7d1b4-15d7-4d4d-a13f-c7d3a694b4fb
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/15/2017
ms.author: jgao
ms.openlocfilehash: d529e66c80c6a3e305ffdf9a2df181a976e07127
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/18/2017
---
# <a name="use-empty-edge-nodes-on-hadoop-clusters-in-hdinsight"></a>Usare i nodi perimetrali vuoti sui cluster Hadoop in HDInsight

Informazioni su come aggiungere un nodo perimetrale vuoto a un cluster HDInsight. Un nodo perimetrale vuoto è una macchina virtuale Linux con gli stessi strumenti client installati e configurati nei nodi head, ma senza servizi Hadoop in esecuzione. Il nodo perimetrale può essere usato per accedere al cluster e per testare e ospitare le applicazioni client. 

È possibile aggiungere un nodo perimetrale vuoto a un cluster HDInsight esistente o a un nuovo cluster quando lo si crea. L'aggiunta di un nodo perimetrale vuoto si esegue usando un modello di Azure Resource Manager.  L'esempio seguente illustra come eseguire questa operazione tramite un modello:

    "resources": [
        {
            "name": "[concat(parameters('clusterName'),'/', variables('applicationName'))]",
            "type": "Microsoft.HDInsight/clusters/applications",
            "apiVersion": "2015-03-01-preview",
            "dependsOn": [ "[concat('Microsoft.HDInsight/clusters/',parameters('clusterName'))]" ],
            "properties": {
                "marketPlaceIdentifier": "EmptyNode",
                "computeProfile": {
                    "roles": [{
                        "name": "edgenode",
                        "targetInstanceCount": 1,
                        "hardwareProfile": {
                            "vmSize": "Standard_D3"
                        }
                    }]
                },
                "installScriptActions": [{
                    "name": "[concat('emptynode','-' ,uniquestring(variables('applicationName')))]",
                    "uri": "[parameters('installScriptAction')]",
                    "roles": ["edgenode"]
                }],
                "uninstallScriptActions": [],
                "httpsEndpoints": [],
                "applicationType": "CustomApplication"
            }
        }
    ],

Come illustrato nell'esempio, è possibile chiamare facoltativamente un'[azione script](hdinsight-hadoop-customize-cluster-linux.md) per eseguire configurazioni aggiuntive, ad esempio per installare [Apache Hue](hdinsight-hadoop-hue-linux.md) nel nodo perimetrale. Lo script dell'azione script deve essere pubblicamente accessibile sul web.  Ad esempio, se lo script viene archiviato in Archiviazione di Azure, usare contenitori o BLOB pubblici.

Le dimensioni della macchina virtuale del nodo perimetrale devono soddisfare i requisiti di dimensioni per le macchine virtuali del nodo di lavoro del cluster HDInsight. Per conoscere le dimensioni consigliate per le macchine virtuali dei nodi di lavoro, vedere [Creare cluster Hadoop in HDInsight](hdinsight-hadoop-provision-linux-clusters.md#cluster-types).

Dopo aver creato un nodo perimetrale, è possibile connettersi al nodo stesso tramite SSH ed eseguire gli strumenti client per accedere al cluster Hadoop in HDInsight.

> [!WARNING] 
> L’utilizzo di un nodo perimetrale vuoto con HDInsight è attualmente in anteprima. I componenti personalizzati che vengono installati nel nodo perimetrale ricevono supporto ragionevole a livello commerciale da Microsoft. Ciò può portare alla risoluzione dei problemi riscontrati. In alternativa, è possibile fare riferimento alle risorse della community per ulteriore assistenza. Di seguito sono indicati alcuni dei siti più attivi per ricevere assistenza dalla community:
>
> * [Forum MSDN per HDInsight](https://social.msdn.microsoft.com/Forums/azure/home?forum=hdinsight)
> * [http://stackoverflow.com](http://stackoverflow.com).
>
> Se si utilizza una tecnologia Apache, è possibile ottenere assistenza tramite i siti di progetto Apache su [http://apache.org](http://apache.org), ad esempio il sito [Hadoop](http://hadoop.apache.org/).

## <a name="add-an-edge-node-to-an-existing-cluster"></a>Aggiungere un nodo perimetrale a un cluster esistente
In questa sezione si userà un modello di Resource Manager per aggiungere un nodo perimetrale a un cluster HDInsight esistente.  Il modello di Resource Manager è disponibile in [GitHub](https://azure.microsoft.com/en-us/resources/templates/101-hdinsight-linux-add-edge-node/). Il modello di Resource Manager chiama un'azione script disponibile in https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-hdinsight-linux-add-edge-node/scripts/EmptyNodeSetup.sh. Lo script non esegue alcuna azione.  Per illustrare la chiamata all'azione script da un modello di Resource Manager.

**Per aggiungere un nodo perimetrale a un cluster esistente**

1. Creare un cluster HDInsight, se non ne è ancora disponibile uno.  Vedere [Esercitazione su Hadoop: introduzione all'uso di Hadoop in HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md).
2. Fare clic sull'immagine seguente per accedere ad Azure e aprire il modello di Azure Resource Manager nel portale di Azure. 
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-linux-add-edge-node%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-apps-use-edge-node/deploy-to-azure.png" alt="Deploy to Azure"></a>
3. Configurare le proprietà seguenti:
   
   * **Sottoscrizione**: selezionare la sottoscrizione di Azure usata per creare il cluster.
   * **Gruppo di risorse**: selezionare il gruppo di risorse usato per il cluster HDInsight esistente.
   * **Località**: selezionare la località del cluster HDInsight esistente.
   * **Nome cluster**: immettere il nome di un cluster HDInsight esistente.
   * **Edge Node Size** (Dimensioni nodo perimetrale): selezionare una delle dimensioni di VM. Le dimensioni della macchina virtuale devono soddisfare i requisiti di dimensione per le macchine virtuali del nodo di lavoro. Per conoscere le dimensioni consigliate per le macchine virtuali dei nodi di lavoro, vedere [Creare cluster Hadoop in HDInsight](hdinsight-hadoop-provision-linux-clusters.md#cluster-types).
   * **Edge Node Prefix** (Prefisso nodo perimetrale): il valore predefinito è **new**.  Se si usa il valore predefinito, il nome del nodo perimetrale sarà **new-edgenode**.  È possibile personalizzare il prefisso nel portale. Si può anche personalizzare il nome completo nel modello.

4. Selezionare **Accetto le condizioni riportate sopra** e quindi fare clic su **Acquista** per creare il nodo perimetrale.

>[!IMPORTANT]
> Assicurarsi di selezionare il gruppo di risorse di Azure per il cluster HDInsight esistente.  In caso contrario, verrà visualizzato il messaggio di errore "Non è possibile eseguire l'operazione richiesta su una risorsa annidata. La risorsa padre '&lt;NomeCluster>' non è stata trovata".

## <a name="add-an-edge-node-when-creating-a-cluster"></a>Aggiungere un nodo perimetrale durante la creazione di un cluster
In questa sezione si userà un modello di Resource Manager per creare un cluster HDInsight con un nodo perimetrale.  Il modello di Resource Manager è disponibile nella [raccolta dei modelli di avvio rapido di Azure](https://azure.microsoft.com/documentation/templates/101-hdinsight-linux-with-edge-node/). Il modello di Resource Manager chiama un'azione script disponibile in https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-hdinsight-linux-with-edge-node/scripts/EmptyNodeSetup.sh. Lo script non esegue alcuna azione.  Per illustrare la chiamata all'azione script da un modello di Resource Manager.

**Per aggiungere un nodo perimetrale a un cluster esistente**

1. Creare un cluster HDInsight, se non ne è ancora disponibile uno.  Vedere [Introduzione all'uso di Hadoop in HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md).
2. Fare clic sull'immagine seguente per accedere ad Azure e aprire il modello di Azure Resource Manager nel portale di Azure. 
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-linux-with-edge-node%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-apps-use-edge-node/deploy-to-azure.png" alt="Deploy to Azure"></a>
3. Configurare le proprietà seguenti:
   
   * **Sottoscrizione**: selezionare la sottoscrizione di Azure usata per creare il cluster.
   * **Gruppo di risorse**: creare un nuovo gruppo di risorse usato per il cluster.
   * **Posizione**: selezionare una posizione per il gruppo di risorse.
   * **Nome cluster**: immettere un nome per il nuovo cluster da creare.
   * **Nome utente dell'account di accesso del cluster**: immettere il nome utente HTTP di Hadoop.  Il nome predefinito è **admin**.
   * **Password dell'account di accesso del cluster**: immettere la password utente HTTP di Hadoop.
   * **Nome utente SSH**: immettere il nome utente SSH. Il nome predefinito è **sshuser**.
   * **Password SSH**: immettere la password utente SSH.
   * **Install Script Action** (Installa azione script): per eseguire questa esercitazione mantenere il valore predefinito.
     
     Alcune proprietà sono state impostate come hardcoded nel modello: il tipo di cluster, il numero di nodi del ruolo di lavoro del cluster e le dimensioni e il nome del nodo perimetrale.
4. Selezionare **Accetto le condizioni riportate sopra** e quindi fare clic su **Acquista** per creare il cluster con il nodo perimetrale.

## <a name="access-an-edge-node"></a>Accedere a un nodo perimetrale
L'endpoint SSH del nodo perimetrale è &lt;NomeNodoPerimetrale>.&lt;NomeCluster>-ssh.azurehdinsight.net:22.  Ad esempio, new-edgenode.myedgenode0914-ssh.azurehdinsight.net:22.

Il nodo perimetrale viene visualizzato come un'applicazione nel portale di Azure.  Il portale fornisce le informazioni per accedere al nodo perimetrale con SSH.

**Per verificare l'endpoint SSH del nodo perimetrale**

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Aprire il cluster HDInsight con un nodo perimetrale.
3. Fare clic su **Applicazioni** nel pannello del cluster. Verrà visualizzato il nodo perimetrale.  Il nome predefinito è **new-edgenode**.
4. Fare clic sul nodo perimetrale. Verrà visualizzato l'endpoint SSH.

**Usare Hive nel nodo perimetrale**

1. Usare SSH per connettersi al nodo perimetrale. Per altre informazioni, vedere [Usare SSH con HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

2. Dopo essersi connessi al nodo perimetrale tramite SSH, usare il comando seguente per aprire la console di Hive:
   
        hive
3. Eseguire il comando seguente per visualizzare le tabelle Hive nel cluster:
   
        show tables;

## <a name="delete-an-edge-node"></a>Eliminare un nodo perimetrale
È possibile eliminare un nodo perimetrale dal portale di Azure.

**Per accedere a un nodo perimetrale**

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Aprire il cluster HDInsight con un nodo perimetrale.
3. Fare clic su **Applicazioni** nel pannello del cluster. Verrà visualizzato un elenco di nodi perimetrali.  
4. Fare clic con il pulsante destro del mouse sul nodo perimetrale che si vuole eliminare e quindi scegliere **Elimina**.
5. Fare clic su **Sì** per confermare.

## <a name="next-steps"></a>Passaggi successivi
In questo articolo si è appreso come aggiungere un nodo perimetrale e come accedervi. Per altre informazioni, vedere gli articoli seguenti:

* [Installare applicazioni HDInsight](hdinsight-apps-install-applications.md): informazioni su come installare un'applicazione HDInsight nei cluster.
* [Installare applicazioni HDInsight personalizzate](hdinsight-apps-install-custom-applications.md): informazioni su come distribuire in HDInsight un'applicazione HDInsight non pubblicata.
* [Pubblicare applicazioni HDInsight](hdinsight-apps-publish-applications.md): informazioni su come pubblicare applicazioni HDInsight personalizzate in Azure Marketplace.
* [MSDN: Install an HDInsight application](https://msdn.microsoft.com/library/mt706515.aspx)(MSDN: Installare un'applicazione HDInsight): informazioni su come definire le applicazioni HDInsight.
* [Personalizzare cluster HDInsight basati su Linux tramite Azioni script](hdinsight-hadoop-customize-cluster-linux.md): informazioni su come usare Azioni script per installare applicazioni aggiuntive.
* [Creare cluster Hadoop basati su Linux in HDInsight tramite modelli ARM](hdinsight-hadoop-create-linux-clusters-arm-templates.md): informazioni su come chiamare i modelli di Azure Resource Manager per creare cluster HDInsight.

