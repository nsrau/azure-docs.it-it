---
title: Usare un ambiente sandbox Hadoop per imparare a usare Hadoop | Microsoft Docs
description: "Per iniziare ad apprendere l&quot;uso dell&quot;ecosistema Handoop, è possibile impostare un ambiente sandbox Hadoop di Hortonworks in una macchina virtuale Azure. "
keywords: emulatore hadoop, sandbox hadoop
editor: cgronlun
manager: jhubbard
services: hdinsight
author: nitinme
documentationcenter: 
tags: azure-portal
ms.assetid: 6ad5bb58-8215-4e3d-a07f-07fcd8839cc6
ms.service: hdinsight
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/06/2017
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: 1618ed7971ffef0eae55b73b4bdd04f3f14195ba
ms.openlocfilehash: a070df78bf95173aa48da60d24d14d08d9be8d9a


---
# <a name="get-started-in-the-hadoop-ecosystem-with-a-hadoop-sandbox-on-a-virtual-machine"></a>Introduzione all'ecosistema Hadoop con un ambiente sandbox Hadoop su una macchina virtuale

Informazioni su come installare l'ambiente sandbox Hadoop da Hortonworks in una macchina virtuale per acquisire familiarità con l'ecosistema di Hadoop. L'ambiente sandbox è un ambiente di sviluppo locale per informazioni su Hadoop, Hadoop Distributed File System (HDFS) e l'invio di processi. Dopo aver acquisito familiarità con Hadoop è possibile iniziare a usare Hadoop in Azure creando un cluster HDInsight. Per altre informazioni sulle attività iniziali, vedere l'articolo [Introduzione all'uso di Hadoop basato su Linux in HDInsight](hdinsight-hadoop-linux-tutorial-get-started.md).

## <a name="prerequisites"></a>Prerequisiti
* [Oracle VirtualBox](https://www.virtualbox.org/). Scaricare e installare l'app da [qui](https://www.virtualbox.org/wiki/Downloads).



## <a name="download-and-install-the-virtual-machine"></a>Scaricare e installare la macchina virtuale
1. Passare ai [download di Hortonworks](http://hortonworks.com/downloads/#sandbox).
2. Fare clic su **DOWNLOAD FOR VIRTUALBOX** per scaricare la versione più recente di Hortonworks Sandbox in una VM. È necessario registrarsi con Hortonworks prima di avviare il download. Il download può richiedere da una a due ore a seconda della velocità della rete.
   
    ![Immagine di collegamento per scaricare Sandbox di Hortonworks per VirtualBox](./media/hdinsight-hadoop-emulator-get-started/download-sandbox.png)
3. Nella stessa pagina Web, fare clic sul collegamento **Import on Virtual Box** per scaricare un file PDF contenente le istruzioni di installazione per la macchina virtuale.

Per scaricare una versione precedente di sandbox HDP, espandere l'archivio:

![Archivio Hortonworks Sandbox](./media/hdinsight-hadoop-emulator-get-started/hortonworks-sandbox-archive.png)


## <a name="start-the-virtual-machine"></a>Avviare la macchina virtuale

1. Aprire Oracle VM VirtualBox.
2. Scegliere **Import Appliance** dal menu **File** e quindi specificare l'immagine di Hortonworks Sandbox.
1. Selezionare Hortonworks Sandbox, fare clic su **Start** e quindi su **Normal Start**. Al termine del processo di avvio della macchina virtuale, verranno visualizzate le istruzioni di accesso.
   
    ![Avvio normale](./media/hdinsight-hadoop-emulator-get-started/normal-start.png)
2. Aprire un Web browser e passare all'URL visualizzato, in genere http://127.0.0.1:8888.

## <a name="set-sandbox-passwords"></a>Impostare le password Sandbox

1. Dal passaggio **introduttivo** della pagina di Sandbox di Hortonworks, selezionare **View Advanced Options** (Visualizza opzioni avanzate). Utilizzare le informazioni in questa pagina per accedere a Sandbox con SSH. Utilizzare il nome e la password forniti.
   
   > [!NOTE]
   > Se non è stato installato un client SSH, è possibile usare l'SSH basato sul Web fornito dalla macchina virtuale all'indirizzo **http://localhost:4200/**.
   > 
   
    Al primo collegamento tramite SSH verrà richiesto di cambiare la password per l'account root. Immettere una nuova password da utilizzare per i futuri accesi tramite SSH.
2. Dopodiché immettere il comando seguente:
   
        ambari-admin-password-reset
   
    Quando richiesto, fornire una password per l'account di amministratore di Ambari. Questo verrà utilizzato quando si accede all'interfaccia utente Web di Ambari.

## <a name="use-hive-commands"></a>Usare i comandi Hive

1. Da una connessione SSH a Sandbox, utilizzare il comando seguente per avviare la shell di Hive:
   
        hive
2. Una volta avviata la shell, utilizzarla per visualizzare le tabelle fornite con Sandbox:
   
        show tables;
3. Usare il codice seguente per recuperare 10 righe dalla tabella `sample_07` :
   
        select * from sample_07 limit 10;

## <a name="next-steps"></a>Passaggi successivi
* [Informazioni su come utilizzare Visual Studio con Sandbox di Hortonworks](hdinsight-hadoop-emulator-visual-studio.md)
* [Acquisire dimestichezza con Sandbox di Hortonworks](http://hortonworks.com/hadoop-tutorial/learning-the-ropes-of-the-hortonworks-sandbox/)
* [Esercitazione di Hadoop: introduzione a HDP](http://hortonworks.com/hadoop-tutorial/hello-world-an-introduction-to-hadoop-hcatalog-hive-and-pig/)




<!--HONumber=Jan17_HO1-->


