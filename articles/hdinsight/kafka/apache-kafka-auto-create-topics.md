---
title: Abilitare la creazione automatica di argomenti in Apache Kafka - Azure HDInsight
description: Informazioni su come configurare Apache Kafka in HDInsight per creare automaticamente gli argomenti. È possibile configurare Kafka impostando auto.create.topics.enable su true tramite Ambari o in fase di creazione di cluster tramite i modelli di Resource Manager o PowerShell.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 04/18/2018
ms.openlocfilehash: 5d990a1c39495090fed1c78f1ddf5e879490e6c4
ms.sourcegitcommit: dd69b3cda2d722b7aecce5b9bd3eb9b7fbf9dc0a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/12/2019
ms.locfileid: "70960679"
---
# <a name="how-to-configure-apache-kafka-on-hdinsight-to-automatically-create-topics"></a>Come configurare Apache Kafka in HDInsight per creare automaticamente gli argomenti

Per impostazione predefinita, [Apache Kafka](https://kafka.apache.org/) in HDInsight non abilita la creazione automatica di argomenti. È possibile abilitarla per i cluster esistenti con [Apache Ambari](https://ambari.apache.org/). È possibile anche abilitare la creazione automatica di argomenti quando si crea un nuovo cluster Kafka tramite un modello di Azure Resource Manager.

## <a name="apache-ambari-web-ui"></a>Interfaccia utente Web di Apache Ambari

Per abilitare la creazione automatica di argomenti in un cluster esistente tramite l'interfaccia utente Web Ambari, procedere come segue:

1. Dal [portale di Azure](https://portal.azure.com) selezionare il cluster Kafka.

2. Da __Panoramica del cluster__ selezionare __Dashboard cluster__. 

    ![Immagine del portale con il dashboard del cluster selezionato](./media/apache-kafka-auto-create-topics/kafka-cluster-overview.png)

3. Selezionare quindi __Dashboard cluster HDInsight__. Quando viene chiesto, eseguire l'autenticazione usando le credenziali di accesso (amministratore) per il cluster.

    ![Immagine della voce del dashboard del cluster HDInsight](./media/apache-kafka-auto-create-topics/hdinsight-cluster-dashboard.png)

3. Selezionare il servizio Kafka nell'elenco a sinistra della pagina.

    ![Elenco di servizi](./media/apache-kafka-auto-create-topics/hdinsight-service-list.png)

4. Selezionare Configs (Configurazioni) nella parte centrale della pagina.

    ![Scheda di configurazione dei servizi](./media/apache-kafka-auto-create-topics/hdinsight-service-config.png)

5. Nel campo Filter (Filtro) immettere il valore `auto.create`. 

    ![Immagine del campo di filtro](./media/apache-kafka-auto-create-topics/hdinsight-filter-field.png)

    In questo modo, l'elenco di proprietà verrà filtrato e verrà visualizzata l'impostazione `auto.create.topics.enable`.

6. Cambiare il valore di `auto.create.topics.enable` impostandolo su `true` e quindi selezionare Save (Salva). Aggiungere una nota e quindi selezionare di nuovo Save (Salva).

    ![Immagine della voce auto.create.topics.enable](./media/apache-kafka-auto-create-topics/auto-create-topics-enable.png)

7. Selezionare il servizio Kafka, __Restart__ (Riavvia) e quindi __Restart all affected__ (Riavvia tutti gli elementi interessati). Quando viene chiesto, selezionare __Confirm restart all__ (Conferma riavvio di tutte le istanze).

    ![Immagine della selezione di riavvio](./media/apache-kafka-auto-create-topics/restart-all-affected.png)

> [!NOTE]  
> È possibile anche impostare i valori Ambari tramite l'API REST Ambari. In genere questa operazione è più difficile perché sono necessarie più chiamate REST per recuperare la configurazione corrente, modificarla e così via. Per altre informazioni, vedere il documento [Gestire i cluster HDInsight usando l'API REST di Apache Ambari](../hdinsight-hadoop-manage-ambari-rest-api.md).

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

## <a name="next-steps"></a>Fasi successive

In questo documento è stato appreso come abilitare la creazione automatica di argomenti per Apache Kafka in HDInsight. Per altre informazioni sull'utilizzo di Kafka, vedere i collegamenti seguenti:

* [Analizzare i log di Apache Kafka](apache-kafka-log-analytics-operations-management.md)
* [Replicare i dati tra cluster Apache Kafka](apache-kafka-mirroring.md)
