---
title: Ridimensionare HiveServer2 in Azure HDInsight
description: Ridimensionare orizzontalmente HiveServer2 nei cluster HDInsight di Azure usando nodi perimetrali per aumentare la tolleranza di errore e la disponibilità.
services: hdinsight
ms.service: hdinsight
ms.topic: conceptual
ms.reviewer: hrasheed-msft
ms.author: kecheung
author: kcheeeung
ms.date: 08/12/2020
ms.openlocfilehash: d0a0df4791492c1c9f0d600630d723024c46c1b8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88227397"
---
# <a name="scale-hiveserver2-on-azure-hdinsight-clusters-for-high-availability"></a>Ridimensionare HiveServer2 nei cluster HDInsight di Azure per la disponibilità elevata

Informazioni su come distribuire un HiveServer2 aggiuntivo nel cluster per aumentare la disponibilità e la distribuzione del carico. Quando si aumentano le dimensioni nodo Head, è anche possibile usare i nodi perimetrali per distribuire HiveServer2. 

> [!NOTE]
> A seconda dell'utilizzo, l'aumento del numero di HiveServer2 può aumentare il numero di connessioni al metastore Hive. Assicurarsi anche che il database SQL di Azure sia dimensionato correttamente.

## <a name="prerequisites"></a>Prerequisiti

Per usare questa guida, è necessario comprendere l'articolo seguente:
- [Usare i nodi perimetrali vuoti sui cluster Apache Hadoop in HDInsight](hdinsight-apps-use-edge-node.md)

## <a name="install-hiveserver2"></a>Installare HiveServer2

In questa sezione verrà installato un HiveServer2 aggiuntivo sugli host di destinazione.

1. Aprire Ambari nel browser e fare clic sull'host di destinazione.

:::image type="content" source="media/hdinsight-apps-install-hiveserver2/hdinsight-install-hiveserver2-a.png" alt-text="Menu hosts di Ambari.":::

2. Fai clic sul pulsante Aggiungi e fai clic su HiveServer2

:::image type="content" source="media/hdinsight-apps-install-hiveserver2/hdinsight-install-hiveserver2-b.png" alt-text="Menu hosts di Ambari.":::

3. Confermare e il processo viene eseguito. Ripetere 1-3 per tutti gli host desiderati.

4. Al termine dell'installazione, riavviare tutti i servizi con le configurazioni non aggiornate e avviare HiveServer2.

:::image type="content" source="media/hdinsight-apps-install-hiveserver2/hdinsight-install-hiveserver2-c.png" alt-text="Menu hosts di Ambari.":::

## <a name="next-steps"></a>Passaggi successivi

In questo articolo si è appreso come installare HiveServer2 nel cluster. Per altre informazioni sui nodi e le applicazioni perimetrali, vedere gli articoli seguenti:

* [Installare il nodo perimetrale](hdinsight-apps-use-edge-node.md): informazioni su come installare un nodo perimetrale nel cluster HDInsight.
* [Installare applicazioni HDInsight](hdinsight-apps-install-applications.md): informazioni su come installare un'applicazione HDInsight nei cluster.
* [Limiti di connessione DTU SQL di Azure](../azure-sql/database/resource-limits-dtu-single-databases.md): informazioni sui limiti del database SQL di Azure con DTU.
* [Limiti di connessione Vcore SQL di Azure](../azure-sql/database/resource-limits-vcore-elastic-pools.md): informazioni sui limiti del database SQL di Azure con vcore.
