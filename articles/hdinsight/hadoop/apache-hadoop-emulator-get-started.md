---
title: "Informazioni sull’uso di una sandbox di Hadoop - emulatore - Azure HDInsight | Documentazione Microsoft"
description: "Per iniziare ad apprendere l'uso dell'ecosistema Handoop, è possibile impostare un ambiente sandbox Hadoop di Hortonworks in una macchina virtuale Azure. "
keywords: emulatore hadoop, sandbox hadoop
editor: cgronlun
manager: jhubbard
services: hdinsight
author: nitinme
documentationcenter: 
tags: azure-portal
ms.assetid: 6ad5bb58-8215-4e3d-a07f-07fcd8839cc6
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/28/2017
ms.author: nitinme
ms.openlocfilehash: 77973ac4224e439377f011a77106534b325b97f9
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/03/2017
---
# <a name="get-started-with-a-hadoop-sandbox-an-emulator-on-a-virtual-machine"></a>Introduzione a una sandbox di Hadoop, un emulatore in una macchina virtuale

Informazioni su come installare l'ambiente sandbox Hadoop da Hortonworks in una macchina virtuale per acquisire familiarità con l'ecosistema di Hadoop. L'ambiente sandbox è un ambiente di sviluppo locale per informazioni su Hadoop, Hadoop Distributed File System (HDFS) e l'invio di processi. Dopo aver acquisito familiarità con Hadoop è possibile iniziare a usare Hadoop in Azure creando un cluster HDInsight. Per altre informazioni sulle attività iniziali, vedere l'articolo [Introduzione all'uso di Hadoop basato su Linux in HDInsight](apache-hadoop-linux-tutorial-get-started.md).

## <a name="prerequisites"></a>Prerequisiti
* [Oracle VirtualBox](https://www.virtualbox.org/). Scaricare e installare l'app da [qui](https://www.virtualbox.org/wiki/Downloads).



## <a name="download-and-install-the-virtual-machine"></a>Scaricare e installare la macchina virtuale
1. Passare ai [download di Hortonworks](http://hortonworks.com/downloads/#sandbox).

2. Fare clic su **DOWNLOAD FOR VIRTUALBOX** per scaricare la versione più recente di Hortonworks Sandbox in una macchina virtuale. È necessario registrarsi con Hortonworks prima di avviare il download. Il download può richiedere da una a due ore a seconda della velocità della rete.
   
    ![Immagine di collegamento per scaricare Sandbox di Hortonworks per VirtualBox](./media/apache-hadoop-emulator-get-started/download-sandbox.png)
3. Nella stessa pagina Web, fare clic sul collegamento **Import on Virtual Box** per scaricare un file PDF contenente le istruzioni di installazione per la macchina virtuale.

Per scaricare una versione precedente di sandbox HDP, espandere l'archivio:

![Archivio Hortonworks Sandbox](./media/apache-hadoop-emulator-get-started/hortonworks-sandbox-archive.png)


## <a name="start-the-virtual-machine"></a>Avviare la macchina virtuale

1. Aprire Oracle VM VirtualBox.
2. Scegliere **Import Appliance** dal menu **File** e quindi specificare l'immagine di Hortonworks Sandbox.
1. Selezionare Hortonworks Sandbox, fare clic su **Start** e quindi su **Normal Start**. Al termine del processo di avvio della macchina virtuale, vengono visualizzate le istruzioni di accesso.
   
    ![Avvio normale](./media/apache-hadoop-emulator-get-started/normal-start.png)
2. Aprire un Web browser e passare all'URL visualizzato, in genere http://127.0.0.1:8888.

## <a name="set-sandbox-passwords"></a>Impostare le password Sandbox

1. Dal passaggio **introduttivo** della pagina di Sandbox di Hortonworks, selezionare **View Advanced Options** (Visualizza opzioni avanzate). Utilizzare le informazioni in questa pagina per accedere alla sandbox con SSH. Utilizzare il nome e la password forniti.
   
   > [!NOTE]
   > Se non è stato installato un client SSH, è possibile usare l'SSH basato sul Web fornito dalla macchina virtuale all'indirizzo **http://localhost:4200/**.
   > 
   
    Al primo collegamento tramite SSH viene richiesto di cambiare la password per l'account radice. Immettere una nuova password da usare quando si accede tramite SSH.

2. Dopodiché immettere il comando seguente:
   
        ambari-admin-password-reset
   
    Quando richiesto, fornire una password per l'account di amministratore di Ambari. Questo viene utilizzato quando si accede all'interfaccia utente Web di Ambari.

## <a name="use-hive-commands"></a>Usare i comandi Hive

1. Da una connessione SSH a Sandbox, utilizzare il comando seguente per avviare la shell di Hive:
   
        hive
2. Una volta avviata la shell, utilizzarla per visualizzare le tabelle fornite con Sandbox:
   
        show tables;
3. Usare il codice seguente per recuperare 10 righe dalla tabella `sample_07` :
   
        select * from sample_07 limit 10;

## <a name="next-steps"></a>Passaggi successivi
* [Informazioni su come utilizzare Visual Studio con Sandbox di Hortonworks](../hdinsight-hadoop-emulator-visual-studio.md)
* [Acquisire dimestichezza con Sandbox di Hortonworks](http://hortonworks.com/hadoop-tutorial/learning-the-ropes-of-the-hortonworks-sandbox/)
* [Esercitazione di Hadoop: introduzione a HDP](http://hortonworks.com/hadoop-tutorial/hello-world-an-introduction-to-hadoop-hcatalog-hive-and-pig/)

