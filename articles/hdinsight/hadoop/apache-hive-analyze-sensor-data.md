---
title: Analizzare i dati dei sensori mediante Apache Hive e Apache Hadoop - Azure HDInsight
description: Informazioni su come analizzare i dati dei sensori usando Console query di Apache Hive con HDInsight (Hadoop) e quindi visualizzare i dati in Microsoft Excel mediante PowerView.
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: conceptual
ms.date: 04/14/2017
ROBOTS: NOINDEX
ms.openlocfilehash: b9c8f1af612c220534e45c5c66651f0ad8600826
ms.sourcegitcommit: c94cf3840db42f099b4dc858cd0c77c4e3e4c436
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/19/2018
ms.locfileid: "53628187"
---
# <a name="analyze-sensor-data-using-the-apache-hive-query-console-on-apache-hadoop-in-hdinsight"></a>Analizzare i dati dei sensori mediante Console query di Apache Hive su Apache Hadoop in HDInsight

Informazioni su come analizzare i dati dei sensori usando Console query di Apache Hive con HDInsight (Apache Hadoop) e quindi visualizzare i dati in Microsoft Excel mediante Power View.

> [!IMPORTANT]  
> I passaggi descritti in questo documento funzionano solo con i cluster HDInsight basati su Windows. HDInsight è disponibile in Windows solo per le versioni precedenti a HDInsight 3.4. Linux è l'unico sistema operativo usato in HDInsight versione 3.4 o successiva. Per altre informazioni, vedere la sezione relativa al [ritiro di HDInsight in Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement).


In questo esempio, usare Hive per elaborare i dati cronologici e identificare i problemi con i sistemi di riscaldamento e condizionamento dell'aria. In particolare, identificare sistemi che non sono in grado di mantenere in modo affidabile una temperatura impostata eseguendo le operazioni seguenti:

* Creare tabelle HIVE per eseguire query su dati archiviati in file con valori delimitati da virgole (CSV).
* Creare query HIVE per analizzare i dati.
* Usare Microsoft Excel per connettersi a HDInsight per recuperare i dati analizzati.
* Usare Power View per visualizzare i dati.

![Diagramma dell'architettura della soluzione](./media/apache-hive-analyze-sensor-data/hvac-architecture.png)

## <a name="prerequisites"></a>Prerequisiti

* Un cluster in HDInsight (Hadoop): per informazioni sulla creazione di un cluster, vedere [Creare cluster Apache Hadoop in HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).
* Microsoft Excel 2013

  > [!NOTE]  
  > Microsoft Excel viene usato per la visualizzazione dei dati con [Power View](https://support.office.com/Article/Power-View-Explore-visualize-and-present-your-data-98268d31-97e2-42aa-a52b-a68cf460472e?ui=en-US&rs=en-US&ad=US).

* [Microsoft Hive ODBC Driver](https://www.microsoft.com/download/details.aspx?id=40886)

## <a name="to-run-the-sample"></a>Per eseguire l'esempio

1. Aprire un Web browser e passare all'URL seguente: 

         https://<clustername>.azurehdinsight.net

    Sostituire `<clustername>` con il nome del cluster HDInsight.

    Quando richiesto, eseguire l'autenticazione con il nome utente e la password dell'amministratore usati durante il provisioning di questo cluster.

2. Nella pagina Web visualizzata fare clic sulla scheda **Guida introduttiva** e quindi sull'esempio **Analisi dei dati dei sensori** nella categoria **Soluzioni con dati di esempio**.

    ![Immagine della raccolta introduttiva](./media/apache-hive-analyze-sensor-data/getting-started-gallery.png)

3. Seguire le istruzioni fornite nella pagina Web per completare l'esempio.
