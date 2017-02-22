---
title: Creare cluster Hadoop basati su Windows in HDInsight | Documentazione Microsoft
description: Informazioni su come creare cluster Hadoop basati su Windows in HDInsight tramite il portale di Azure.
services: hdinsight
documentationcenter: 
tags: azure-portal
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: c8689ef3-f56f-4708-8a3a-cc00abc54e8d
ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/18/2017
ms.author: jgao
translationtype: Human Translation
ms.sourcegitcommit: ede2e4ec5f3414d1c8a17f4c120011eba0d9a6ca
ms.openlocfilehash: a9d7a4372693ad021a898fae9818f2d037d42547


---
# <a name="create-windows-based-hadoop-clusters-in-hdinsight-using-the-azure-portal"></a>Creare cluster Hadoop basati su Windows in HDInsight tramite il portale di Azure

[!INCLUDE [selector](../../includes/hdinsight-selector-create-clusters.md)]

Informazioni su come creare cluster Hadoop basati su Windows in HDInsight tramite il portale di Azure. 

Le informazioni contenute in questo articolo si applicano solo ai cluster HDInsight basati su Windows. Per informazioni sulla creazione di cluster basati su Linux, vedere [Creare cluster Hadoop in HDInsight usando il portale di Azure](hdinsight-hadoop-create-linux-clusters-portal.md).

> [!IMPORTANT]
> Linux è l'unico sistema operativo usato in HDInsight versione 3.4 o successiva. Per altre informazioni, vedere [HDInsight deprecato in Windows](hdinsight-component-versioning.md#hdi-version-32-and-33-nearing-deprecation-date).

## <a name="prerequisites"></a>Prerequisiti:
[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Per poter eseguire le istruzioni descritte nell'articolo è necessario disporre di:

* Una sottoscrizione di Azure. Vedere [Ottenere una versione di valutazione gratuita di Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

### <a name="access-control-requirements"></a>Requisiti di controllo di accesso
[!INCLUDE [access-control](../../includes/hdinsight-access-control-requirements.md)]

## <a name="create-clusters"></a>Creare i cluster
**Per creare un cluster HDInsight**

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Fare clic su **NUOVO**, su **Intelligence e analisi** e quindi su **HDInsight**.
3. Digitare o selezionare i valori seguenti:
   
   * **Nome cluster**: assegnare un nome al cluster.
   * **Sottoscrizione**: selezionare la sottoscrizione di Azure usata per questo cluster.
   * **Configurazione cluster**:

     * **Tipo di cluster**: selezionare **Hadoop** per questa esercitazione.
     * **Sistema operativo**: selezionare **Windows** per questa esercitazione.
     * **Versione**: selezionare **Hadoop 2.7.0 (HDI 3.3)**.  Si tratta della versione più recente per i cluster Hadoop basati su Windows.
     * **Livello cluster**: selezionare **STANDARD** per questa esercitazione.

   * **Credenziali**:

     * **Nome utente dell'account di accesso del cluster**: il nome predefinito è **admin**. 
     * **Password dell'account di accesso del cluster**: immettere una password.
     * **Abilita desktop remoto**: Desktop remoto non è necessario per questa esercitazione.

   * **Origine dati**:

     * **Metodo di selezione**: selezionare **Da tutte le sottoscrizioni**.
     * **Selezionare un account di archiviazione**: fare clic su **Crea nuovo** e quindi immettere un nome per l'account di archiviazione predefinito.
     * **Contenitore predefinito**: per impostazione predefinita, il contenitore predefinito usa lo stesso nome del cluster.
     * **Località**: scegliere una località vicina.  Questa località viene usata per il cluster e per l'account di archiviazione predefinito.
   * **Dimensioni del cluster**:

     * **Numero di nodi del ruolo di lavoro**: questa esercitazione richiede un solo nodo.
   * **Configurazioni avanzate**: è possibile ignorare questa parte per questa esercitazione.
   * **Gruppo di risorse**: selezionare **Crea nuovo** e immettere un nome.

4. Selezionare **Aggiungi al dashboard** e quindi fare clic su **Crea**. Se si seleziona **Aggiungi alla Schermata iniziale**, verrà aggiunto un riquadro per il cluster alla Schermata iniziale del portale. La creazione di un cluster richiede 15 - 20 minuti. Usare il riquadro nella Schermata iniziale o la voce **Notifiche** a sinistra della pagina per verificare il processo di provisioning.
5. Al termine della creazione, fare clic sul riquadro per il cluster dalla Schermata iniziale per avviare il pannello del cluster. Il pannello del cluster fornisce informazioni essenziali sui cluster, ad esempio nome, gruppo di risorse che a cui appartiene, percorso, sistema operativo, URL per il dashboard del cluster e così via.

## <a name="customize-clusters"></a>Personalizzare i cluster
* Vedere [Personalizzare cluster HDInsight tramite Bootstrap](hdinsight-hadoop-customize-cluster-bootstrap.md).
* Vedere l'articolo su come [personalizzare cluster HDInsight basati su Windows tramite un'azione script](hdinsight-hadoop-customize-cluster.md).

## <a name="next-steps"></a>Passaggi successivi
Questo articolo ha spiegato vari modi per creare un cluster HDInsight. Per altre informazioni, vedere gli articoli seguenti:

* [Introduzione all'uso di Azure HDInsight](hdinsight-hadoop-linux-tutorial-get-started.md) - Informazioni su come iniziare a usare il cluster HDInsight
* [Inviare processi Hadoop a livello di codice](hdinsight-submit-hadoop-jobs-programmatically.md) - Informazioni su come inviare processi a HDInsight a livello di codice
* [Gestire cluster Hadoop in HDInsight con il portale di Azure](hdinsight-administer-use-management-portal.md)




<!--HONumber=Jan17_HO3-->


