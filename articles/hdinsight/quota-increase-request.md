---
title: Richiesta di aumento della quota core CPU - Azure HDInsight
description: Informazioni sul processo per richiedere un aumento dei core CPU allocati alla sottoscrizione.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 05/07/2020
ms.openlocfilehash: f8411176e0979bdb894983bcf866abd0e1109e21
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/02/2020
ms.locfileid: "89291668"
---
# <a name="requesting-quota-increases-for-azure-hdinsight"></a>Richieste di aumento della quota per Azure HDInsight

Le quote core CPU contribuiscono a garantire che l'uso delle risorse venga distribuito equamente tra tutti i clienti di una determinata area di Azure. Tuttavia, in alcuni casi, i requisiti aziendali potrebbero richiedere più risorse cluster rispetto a quelle consentite dalla quota corrente. In tali casi, è possibile richiedere un aumento della quota core di CPU per poter distribuire i cluster, che soddisfano i requisiti di elaborazione dei dati.

Quando si raggiunge un limite di quota, non è possibile distribuire nuovi cluster o aumentare le istanze dei cluster esistenti aggiungendo più nodi di lavoro. L'unico limite di quota è la quota di core di CPU esistente a livello di area per ogni sottoscrizione. Ad esempio, la sottoscrizione potrebbe avere un limite core di 30 CPU nell'area Stati Uniti orientali, con altri 30 core CPU consentiti negli Stati Uniti orientali 2.

## <a name="gather-required-information"></a>Raccogliere le informazioni necessarie

Se si è ricevuto un errore che indica che è stato raggiunto un limite di quota, usare il processo descritto in questa sezione per raccogliere informazioni importanti e inviare una richiesta di aumento della quota.

1. Determinare le dimensioni, la scala e il tipo della macchina virtuale del cluster desiderata.
1. Verificare i limiti di capacità di quota correnti della sottoscrizione. Per controllare i core disponibili, seguire la procedura riportata di seguito.

    1. Accedere al [portale di Azure](https://portal.azure.com/).
    1. Passare alla pagina **Panoramica** per il cluster di HDInsight.
    1. Nel menu a sinistra selezionare **Limiti di quota**. La pagina mostra il numero di core in uso, il numero di core disponibili e i core totali.

Per richiedere un aumento della quota, eseguire questa procedura:

1. Accedere al [portale di Azure](https://portal.azure.com/).
1. Selezionare **Guida e supporto** nell'angolo in basso a sinistra della pagina.

    ![pulsante di guida e supporto tecnico](./media/quota-increase-request/help-support-button.png)

1. Selezionare **Nuova richiesta di supporto**.
1. Nella pagina **Nuova richiesta di supporto** selezionare le opzioni seguenti nella scheda **Informazioni di base**:

   - **Tipo di problema**: **Limiti del servizio e della sottoscrizione (quote)**
   - **Sottoscrizione**: la sottoscrizione che si vuole modificare
   - **Tipo di quota**: **HDInsight**

     ![Creare una richiesta di supporto per aumentare la quota di core HDInsight](./media/quota-increase-request/hdinsight-quota-support-request.png)

1. Selezionare **Avanti: Soluzioni >>** .
1. Nella pagina **Dettagli** immettere una descrizione del problema, selezionare la gravità del problema, la modalità di contatto preferita e altri campi obbligatori. Usare il modello riportato di seguito per assicurarsi di fornire le informazioni necessarie. Le richieste di aumento della quota vengono valutate dal team di capacità di Azure e non dal team del prodotto HDInsight. Più complete sono le informazioni fornite, più probabile sarà l'approvazione della richiesta.

    ```
    I would like to request [SPECIFY DESIRED AMOUNT] on [DESIRED SKU] for [SUBSCRIPTION ID].
    
    My current quota on this subscription is [CURRENT QUOTA AMOUNT].
    
    I would like to use the extra cores for [DETAIL REASON].
    ```

    ![dettagli del problema](./media/quota-increase-request/problem-details.png)

1. Selezionare **Avanti: Rivedi e crea >>** .
1. Nella scheda **Rivedi e crea** selezionare **Crea**.

> [!NOTE]  
> Se è necessario aumentare la quota di HDInsight core in un'area privata, [inviare una richiesta di elenco approvato](https://aka.ms/canaryintwhitelist).

È possibile [contattare il supporto tecnico per richiedere un aumento dei limiti di quota](https://docs.microsoft.com/azure/azure-portal/supportability/resource-manager-core-quotas-request).

Sono previsti limiti di quota fissi. Ad esempio, una singola sottoscrizione di Azure può avere al massimo 10.000 core. Per informazioni dettagliate sui limiti, vedere [Sottoscrizione di Azure e limiti, quote e vincoli dei servizi](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits).

## <a name="next-steps"></a>Passaggi successivi

* [Configurare i cluster di HDInsight con Apache Hadoop, Spark, Kafka e altro](hdinsight-hadoop-provision-linux-clusters.md): Informazioni su come configurare i cluster HDInsight.
* [Monitorare le prestazioni del cluster](hdinsight-key-scenarios-to-monitor.md): informazioni sui principali scenari da monitorare per il cluster HDInsight che potrebbero influire sulla relativa capacità.
