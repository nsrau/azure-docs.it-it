---
title: Abilitare la creazione automatica di argomenti in Apache Kafka - Azure HDInsight
description: Informazioni su come configurare Apache Kafka in HDInsight per creare automaticamente gli argomenti. È possibile configurare Kafka impostando auto.create.topics.enable su true tramite Ambari o in fase di creazione di cluster tramite i modelli di Resource Manager o PowerShell.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 10/25/2019
ms.openlocfilehash: 7ec7d15806927306b12624962facbafddf2ce08b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "73242372"
---
# <a name="how-to-configure-apache-kafka-on-hdinsight-to-automatically-create-topics"></a>Come configurare Apache Kafka in HDInsight per creare automaticamente gli argomenti

Per impostazione predefinita, [Apache Kafka](https://kafka.apache.org/) in HDInsight non consente la creazione automatica degli argomenti. È possibile abilitarla per i cluster esistenti con [Apache Ambari](https://ambari.apache.org/). È possibile anche abilitare la creazione automatica di argomenti quando si crea un nuovo cluster Kafka tramite un modello di Azure Resource Manager.

## <a name="apache-ambari-web-ui"></a>Interfaccia utente Web di Apache Ambari

Per abilitare la creazione automatica di argomenti in un cluster esistente tramite l'interfaccia utente Web Ambari, procedere come segue:

1. Dal [portale di Azure](https://portal.azure.com)selezionare il cluster Kafka.From the Azure portal, select your Kafka cluster.

1. Da **Dashboard cluster**, selezionare Home **Ambari**.

    ![Immagine del portale con il dashboard del cluster selezionato](./media/apache-kafka-auto-create-topics/azure-portal-cluster-dashboard-ambari.png)

    Quando viene chiesto, eseguire l'autenticazione usando le credenziali di accesso (amministratore) per il cluster. In alternativa, è possibile connettersi `https://CLUSTERNAME.azurehdinsight.net/` ad `CLUSTERNAME` Amabri direttamente da dove è il nome del cluster Kafka.

1. Selezionare il servizio Kafka nell'elenco a sinistra della pagina.

    ![Scheda elenco dei servizi Apache Ambari](./media/apache-kafka-auto-create-topics/hdinsight-service-list.png)

1. Selezionare Configs (Configurazioni) nella parte centrale della pagina.

    ![Scheda Config del servizio Apache Ambari](./media/apache-kafka-auto-create-topics/hdinsight-service-config.png)

1. Nel campo Filter (Filtro) immettere il valore `auto.create`.

    ![Campo filtro di ricerca Apache Ambari](./media/apache-kafka-auto-create-topics/hdinsight-filter-field.png)

    In questo modo, l'elenco di proprietà verrà filtrato e verrà visualizzata l'impostazione `auto.create.topics.enable`.

1. Modificare il `auto.create.topics.enable` valore `true`di , in , quindi selezionare **Salva**. Aggiungere una nota, quindi selezionare di nuovo **Salva.**

    ![Immagine della voce auto.create.topics.enable](./media/apache-kafka-auto-create-topics/auto-create-topics-enable.png)

1. Selezionare il servizio Kafka, __Restart__ (Riavvia) e quindi __Restart all affected__ (Riavvia tutti gli elementi interessati). Quando richiesto, selezionare __Conferma riavviare tutti i__file .

    ![Apache Ambari riavviare tutti gli interessati](./media/apache-kafka-auto-create-topics/restart-all-affected.png)

> [!NOTE]  
> È possibile anche impostare i valori Ambari tramite l'API REST Ambari. Questo è in genere più difficile, in quanto è necessario effettuare più chiamate REST per recuperare la configurazione corrente, modificarla e così via. Per altre informazioni, vedere gestire i cluster HDInsight usando il documento [dell'API REST Apache Ambari.For more information, see the Manage HDInsight clusters using the Apache Ambari REST API](../hdinsight-hadoop-manage-ambari-rest-api.md) document.

## <a name="resource-manager-templates"></a>Modelli di Gestione risorse

Quando si crea un cluster Kafka usando un modello di Azure Resource Manager, è possibile impostare direttamente `auto.create.topics.enable` aggiungendo l'impostazione in un `kafka-broker`. Il frammento JSON seguente illustra come impostare questo valore su `true`:

```json
"clusterDefinition": {
    "kind": "kafka",
    "configurations": {
    "gateway": {
        "restAuthCredential.isEnabled": true,
        "restAuthCredential.username": "[parameters('clusterLoginUserName')]",
        "restAuthCredential.password": "[parameters('clusterLoginPassword')]"
    },
    "kafka-broker": {
        "auto.create.topics.enable": "true"
    }
}
```

## <a name="next-steps"></a>Passaggi successivi

In questo documento è stato appreso come abilitare la creazione automatica di argomenti per Apache Kafka in HDInsight. Per altre informazioni sull'utilizzo di Kafka, vedere i collegamenti seguenti:

* [Analizzare i log di Apache Kafka](apache-kafka-log-analytics-operations-management.md)
* [Replicare i dati tra cluster Apache Kafka](apache-kafka-mirroring.md)
