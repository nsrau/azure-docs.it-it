---
title: Usare nodi perimetrali vuoti in HDInsight | Microsoft Docs
description: Come aggiungere un nodo perimetrale vuoto al cluster HDInsight che può essere usato come un client e per testare oppure ospitare le applicazioni HDInsight.
services: hdinsight
editor: cgronlun
manager: jhubbard
author: mumian
tags: azure-portal
documentationcenter: ''

ms.service: hdinsight
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/14/2016
ms.author: jgao

---
# Usare nodi perimetrali vuoti in HDInsight
Informazioni su come aggiungere un nodo perimetrale vuoto a un cluster HDInsight basato su Linux. Un nodo perimetrale vuoto è una macchina virtuale Linux con gli stessi strumenti client installati e configurati nei nodi head, ma senza servizi Hadoop in esecuzione. Il nodo perimetrale può essere usato per accedere al cluster e per testare e ospitare le applicazioni client.

È possibile aggiungere un nodo perimetrale vuoto a un cluster HDInsight esistente o a un nuovo cluster quando lo si crea. L'aggiunta di un nodo perimetrale vuoto si esegue usando un modello di Azure Resource Manager. L'esempio seguente illustra come eseguire questa operazione tramite un modello:

    "resources": [
        {
            "name": "[concat(parameters('clusterName'),'/', variables('applicationName'))]",
            "type": "Microsoft.HDInsight/clusters/applications",
            "apiVersion": "[variables('clusterApiVersion')]",
            "properties": {
                "marketPlaceIdentifier": "EmptyNode",
                "computeProfile": {
                    "roles": [{
                        "name": "edgenode",
                        "targetInstanceCount": 1,
                        "hardwareProfile": {
                            "vmSize": "[parameters('edgeNodeSize')]"
                        }
                    }]
                },
                "installScriptActions": [{
                    "name": "[concat('emptynode','-' ,uniquestring(variables('applicationName')))]",
                    "uri": "https://raw.githubusercontent.com/hdinsight/Iaas-Applications/master/EmptyNode/scripts/EmptyNodeSetup.sh",
                    "roles": ["edgenode"]
                }],
                "uninstallScriptActions": [],
                "httpsEndpoints": [],
                "applicationType": "CustomApplication"
            }
        }
    ],

Come illustrato nell'esempio, è possibile chiamare un'[azione script](hdinsight-hadoop-customize-cluster-linux.md) per eseguire una configurazione aggiuntiva, ad esempio l'installazione di [Apache Hue](hdinsight-hadoop-hue-linux.md) nel nodo perimetrale.

Dopo aver creato un nodo perimetrale, è possibile connettersi al nodo stesso tramite SSH ed eseguire gli strumenti client per accedere al cluster Hadoop in HDInsight.

## Aggiungere un nodo perimetrale a un cluster esistente
In questa sezione si userà un modello di Resource Manager per aggiungere un nodo perimetrale a un cluster HDInsight esistente. Il modello di Resource Manager è disponibile in [GitHub](https://github.com/hdinsight/Iaas-Applications/tree/master/EmptyNode). Il modello di Resource Manager chiama uno script di azione script disponibile in https://raw.githubusercontent.com/hdinsight/Iaas-Applications/master/EmptyNode/scripts/EmptyNodeSetup.sh. Lo script non esegue alcuna azione. Viene usato per illustrare la chiamata all'azione script da un modello di Resource Manager.

**Per aggiungere un nodo perimetrale a un cluster esistente**

1. Creare un cluster HDInsight, se non ne è ancora disponibile uno. Vedere [Esercitazione di Hadoop: Introduzione all'uso di Hadoop basato su Linux in HDInsight](hdinsight-hadoop-linux-tutorial-get-started.md).
2. Fare clic sull'immagine seguente per accedere ad Azure e aprire il modello di Azure Resource Manager nel portale di Azure.
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fhdinsight%2FIaas-Applications%2Fmaster%2FEmptyNode%2Fazuredeploy.json" target="_blank"><img src="https://acom.azurecomcdn.net/80C57D/cdn/mediahandler/docarticles/dpsmedia-prod/azure.microsoft.com/en-us/documentation/articles/hdinsight-hbase-tutorial-get-started-linux/20160201111850/deploy-to-azure.png" alt="Deploy to Azure"></a>
3. Configurare le proprietà seguenti:
   
   * CLUSTERNAME: immettere il nome di un cluster HDInsight esistente.
   * EDGENODESIZE: selezionare una delle dimensioni di macchina virtuale.
   * EDGENODEPREFIX: il valore predefinito è **new**. Se si usa il valore predefinito, il nome del nodo perimetrale sarà **new-edgenode**. È possibile personalizzare il prefisso nel portale. Si può anche personalizzare il nome completo nel modello.
4. Fare clic su **OK** per salvare le modifiche.
5. In **Gruppo di risorse** selezionare un gruppo di risorse.
6. Fare clic su **Rivedere le note legali** e quindi fare clic su **Acquista**.
7. Selezionare **Aggiungi al dashboard** e quindi fare clic su **Crea**.

## Aggiungere un nodo perimetrale durante la creazione di un cluster
In questa sezione si userà un modello di Resource Manager per creare un cluster HDInsight con un nodo perimetrale. Il modello di Resource Manager è disponibile in un [contenitore di archiviazione BLOB](http://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-hadoop-cluster-in-hdinsight-with-edge-node.json) pubblico. Il modello di Resource Manager chiama uno script di azione script disponibile in https://raw.githubusercontent.com/hdinsight/Iaas-Applications/master/EmptyNode/scripts/EmptyNodeSetup.sh. Lo script non esegue alcuna azione. Viene usato per illustrare la chiamata all'azione script da un modello di Resource Manager.

**Per aggiungere un nodo perimetrale a un cluster esistente**

1. Creare un cluster HDInsight, se non ne è ancora disponibile uno. Vedere [Esercitazione di Hadoop: Introduzione all'uso di Hadoop basato su Linux in HDInsight](hdinsight-hadoop-linux-tutorial-get-started.md).
2. Fare clic sull'immagine seguente per accedere ad Azure e aprire il modello di Azure Resource Manager nel portale di Azure.
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-linux-based-hadoop-cluster-in-hdinsight-with-edge-node.json" target="_blank"><img src="https://acom.azurecomcdn.net/80C57D/cdn/mediahandler/docarticles/dpsmedia-prod/azure.microsoft.com/en-us/documentation/articles/hdinsight-hbase-tutorial-get-started-linux/20160201111850/deploy-to-azure.png" alt="Deploy to Azure"></a>
3. Configurare le proprietà seguenti:
   
   * CLUSTERNAME: immettere un nome per il nuovo cluster da creare.
   * CLUSTERLOGINUSERNAME: immettere il nome utente HTTP di Hadoop. Il nome predefinito è **admin**.
   * CLUSTERLOGINUSERNAME: immettere la password utente HTTP di Hadoop.
   * SSHUSERNAME: immettere il nome utente SSH. Il nome predefinito è **sshuser**.
   * SSHPASSWORD: immettere la password utente SSH.
   * LOCATION: immettere il percorso del nome del gruppo di risorse, del cluster e dell'account di archiviazione predefinito.
   * CLUSTERTYPE: il valore predefinito è **hadoop**.
   * CLUSTERWORKERNODECOUNT: il valore predefinito è 2.
   * EDGENODESIZE: selezionare una delle dimensioni di macchina virtuale.
   * EDGENODEPREFIX: il valore predefinito è **new**. Se si usa il valore predefinito, il nome del nodo perimetrale sarà **new-edgenode**. È possibile personalizzare il prefisso nel portale. Si può anche personalizzare il nome completo nel modello.
4. Fare clic su **OK** per salvare le modifiche.
5. In **Gruppo di risorse** immettere un nome per il nuovo gruppo di risorse.
6. Fare clic su **Rivedere le note legali** e quindi fare clic su **Acquista**.
7. Selezionare **Aggiungi al dashboard** e quindi fare clic su **Crea**.

## Accedere a un nodo perimetrale
L'endpoint ssh del nodo perimetrale è <NomeNodoPerimetrale>.<NomeCluster>-ssh.azurehdinsight.net:22. Ad esempio, new-edgenode.myedgenode0914-ssh.azurehdinsight.net:22.

Il nodo perimetrale viene visualizzato come un'applicazione nel portale di Azure. Il portale fornisce le informazioni per accedere al nodo perimetrale con SSH.

**Per verificare l'endpoint SSH del nodo perimetrale**

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Aprire il cluster HDInsight con un nodo perimetrale.
3. Fare clic su **Applicazioni** nel pannello del cluster. Verrà visualizzato il nodo perimetrale. Il nome predefinito è **new-edgenode**.
4. Fare clic sul nodo perimetrale. Verrà visualizzato l'endpoint SSH.

**Usare Hive nel nodo perimetrale**

1. Usare SSH per connettersi al nodo perimetrale. Vedere [Usare SSH con Hadoop basato su Linux in HDInsight da Linux, Unix o OS X](hdinsight-hadoop-linux-use-ssh-unix.md) e [Usare SSH con Hadoop basato su Linux in HDInsight da Windows](hdinsight-hadoop-linux-use-ssh-windows.md).
2. Dopo essersi connessi al nodo perimetrale tramite SSH, usare il comando seguente per aprire la console di Hive:
   
        hive
3. Eseguire il comando seguente per visualizzare le tabelle Hive nel cluster:
   
        show tables;

## Eliminare un nodo perimetrale
È possibile eliminare un nodo perimetrale dal portale di Azure.

**Per accedere a un nodo perimetrale**

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Aprire il cluster HDInsight con un nodo perimetrale.
3. Fare clic su **Applicazioni** nel pannello del cluster. Verrà visualizzato un elenco di nodi perimetrali.
4. Fare clic con il pulsante destro del mouse sul nodo perimetrale che si vuole eliminare e quindi scegliere **Elimina**.
5. Fare clic su **Sì** per confermare.

## Passaggi successivi
In questo articolo si è appreso come aggiungere un nodo perimetrale e come accedervi. Per altre informazioni, vedere gli articoli seguenti:

* [Installare applicazioni HDInsight](hdinsight-apps-install-applications.md): informazioni su come installare un'applicazione HDInsight nei cluster.
* [Installare applicazioni HDInsight personalizzate](hdinsight-apps-install-custom-applications.md): informazioni su come distribuire in HDInsight un'applicazione HDInsight non pubblicata.
* [Pubblicare applicazioni HDInsight](hdinsight-apps-publish-applications.md): informazioni su come pubblicare applicazioni HDInsight personalizzate in Azure Marketplace.
* [MSDN: Install an HDInsight application](https://msdn.microsoft.com/library/mt706515.aspx) (MSDN: Installare un'applicazione HDInsight): informazioni su come definire le applicazioni HDInsight.
* [Personalizzare cluster HDInsight basati su Linux tramite Azioni script](hdinsight-hadoop-customize-cluster-linux.md): informazioni su come usare Azioni script per installare applicazioni aggiuntive.
* [Creare cluster Hadoop basati su Linux in HDInsight tramite modelli ARM](hdinsight-hadoop-create-linux-clusters-arm-templates.md): informazioni su come chiamare i modelli di Azure Resource Manager per creare cluster HDInsight.

<!---HONumber=AcomDC_0914_2016-->