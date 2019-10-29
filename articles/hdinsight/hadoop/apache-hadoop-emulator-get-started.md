---
title: Informazioni su come usare un Apache Hadoop sandbox, Emulator-Azure HDInsight
description: "Per iniziare ad apprendere l'uso dell'ecosistema Apache Hadoop, è possibile impostare un ambiente sandbox Hadoop di Hortonworks in una macchina virtuale Azure. "
keywords: emulatore hadoop, sandbox hadoop
ms.reviewer: jasonh
author: hrasheed-msft
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 05/29/2019
ms.author: hrasheed
ms.openlocfilehash: 47ee66393e3e1678576b12a70b767f35cb3bc635
ms.sourcegitcommit: 38251963cf3b8c9373929e071b50fd9049942b37
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73044761"
---
# <a name="get-started-with-an-apache-hadoop-sandbox-an-emulator-on-a-virtual-machine"></a>Introduzione a un Apache Hadoop sandbox, un emulatore in una macchina virtuale

Informazioni su come installare l'ambiente sandbox Apache Hadoop da Hortonworks in una macchina virtuale per acquisire familiarità con l'ecosistema di Hadoop. L'ambiente sandbox è un ambiente di sviluppo locale per informazioni su Hadoop, Hadoop Distributed File System (HDFS) e l'invio di processi. Dopo aver acquisito familiarità con Hadoop è possibile iniziare a usare Hadoop in Azure creando un cluster HDInsight. Per altre informazioni sulle attività iniziali, vedere l'articolo [Introduzione all'uso di Hadoop basato su Linux in HDInsight](apache-hadoop-linux-tutorial-get-started.md).

## <a name="prerequisites"></a>Prerequisiti

* [Oracle VirtualBox](https://www.virtualbox.org/). Scaricare e installare l'app da [qui](https://www.virtualbox.org/wiki/Downloads).

## <a name="download-and-install-the-virtual-machine"></a>Scaricare e installare la macchina virtuale

1. Passare al [download di Cloudera](https://www.cloudera.com/downloads/hortonworks-sandbox/hdp.html).

1. Fare clic su **VirtualBox** in **scegliere il tipo di installazione** per scaricare la versione più recente di Hortonworks sandbox in una macchina virtuale. Accedere o completare il modulo di interesse del prodotto.

1. Fare clic sul pulsante **HDP sandbox (Latest)** per avviare il download.

Per istruzioni sulla configurazione della sandbox, vedere la [Guida alla distribuzione e all'installazione di sandbox](https://hortonworks.com/tutorial/sandbox-deployment-and-install-guide/section/1/).

Per scaricare una versione precedente di HDP sandbox, vedere i collegamenti nelle **versioni precedenti**.

## <a name="start-the-virtual-machine"></a>Avviare la macchina virtuale

1. Aprire Oracle VM VirtualBox.
1. Scegliere **Import Appliance** dal menu **File** e quindi specificare l'immagine di Hortonworks Sandbox.
1. Selezionare Hortonworks Sandbox, fare clic su **Start** e quindi su **Normal Start**. Al termine del processo di avvio della macchina virtuale, vengono visualizzate le istruzioni di accesso.

    ![avvio normale di VirtualBox Manager](./media/apache-hadoop-emulator-get-started/virtualbox-normal-start.png)

1. Aprire un Web browser e passare all'URL visualizzato, in genere `http://127.0.0.1:8888`.

## <a name="set-sandbox-passwords"></a>Impostare le password Sandbox

1. Dal passaggio **introduttivo** della pagina di Sandbox di Hortonworks, selezionare **View Advanced Options** (Visualizza opzioni avanzate). Utilizzare le informazioni in questa pagina per accedere alla sandbox con SSH. Utilizzare il nome e la password forniti.

   > [!NOTE]
   > Se non è stato installato un client SSH, è possibile usare l'SSH basato sul Web fornito dalla macchina virtuale all'indirizzo **http://localhost:4200/** .

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

* [Acquisire dimestichezza con Sandbox di Hortonworks](https://hortonworks.com/hadoop-tutorial/learning-the-ropes-of-the-hortonworks-sandbox/)

* [Esercitazione di Hadoop: introduzione a HDP](https://hortonworks.com/hadoop-tutorial/hello-world-an-introduction-to-hadoop-hcatalog-hive-and-pig/)
