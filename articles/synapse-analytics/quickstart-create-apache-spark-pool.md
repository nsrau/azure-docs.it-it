---
title: 'Avvio rapido: Creare un pool di Apache Spark (anteprima)'
description: Seguire i passaggi di questa guida per creare un nuovo pool di Apache Spark per un'area di lavoro di Azure Synapse Analytics.
services: synapse-analytics
author: malvenko
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: ''
ms.date: 04/15/2020
ms.author: josels
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 1457549fef3a78356c8b1af6be620fdf30ddab46
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81420625"
---
# <a name="quickstart-create-a-new-apache-spark-pool-preview"></a>Guida introduttiva: Creare un pool di Apache Spark (anteprima)

Synapse Analytics offre vari motori di analisi che consentono di inserire, trasformare, modellare, analizzare e gestire i dati. Un pool di Apache Spark offre funzionalità di calcolo open source per i Big Data. Dopo aver creato un pool di Apache Spark nell'area di lavoro Synapse, è possibile caricare, modellare, elaborare e gestire i dati per ottenere informazioni dettagliate.

Questa guida di avvio rapido illustra come usare il portale di Azure per creare un pool di Apache Spark in un'area di lavoro Synapse.

> [!IMPORTANT]
> La fatturazione delle istanze di Spark viene calcolata con ripartizione proporzionale al minuto, indipendentemente dal fatto che siano in uso o meno. Assicurarsi di arrestare l'istanza di Spark dopo averla usata oppure impostare un timeout breve. Per altre informazioni, vedere la sezione **Pulire le risorse** di questo articolo.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito prima di iniziare](https:/azure.microsoft.com/free/).

## <a name="prerequisites"></a>Prerequisiti

- Sottoscrizione di Azure: [creare un account gratuito](https:/azure.microsoft.com/free/)
- [Area di lavoro di Synapse Analytics](quickstart-create-workspace.md)

## <a name="sign-in-to-the-azure-portal"></a>Accedere al portale di Azure

Accedere al [portale di Azure](https:/portal.azure.com/)

## <a name="create-new-apache-spark-pool"></a>Creare un nuovo pool di Apache Spark

1. Nell'area di lavoro Synapse in cui creare il pool di Apache Spark fare clic su **Nuovo pool di Apache Spark**.
![Panoramica dell'area di lavoro di Synapse con un riquadro rosso intorno al comando per la creazione di un nuovo pool di Apache Spark](media/quickstart-create-apache-spark-pool/quickstart-create-spark-pool-01.png)
2. Immettere i dettagli seguenti nella scheda **Informazioni di base**:

    |Impostazione | Valore consigliato | Descrizione |
    | :------ | :-------------- | :---------- |
    | **Nome del pool di Apache Spark** | Un nome di pool valido | Si tratta del nome che avrà il pool di Apache Spark. |
    | **Dimensioni nodo** | Piccole (4 vCPU/32 GB) | Per questa guida di avvio rapido, impostare questa opzione sulle dimensioni minime per ridurre i costi |
    | **Autoscale** | Attivato | Lasciare l'impostazione predefinita |
    | **Numero di nodi** | 3 - 40 | Lasciare l'impostazione predefinita |
    ||||

    ![Flusso di creazione del pool di Apache Spark - Scheda Informazioni di base.](media/quickstart-create-apache-spark-pool/quickstart-create-spark-pool-02.png)
    > [!IMPORTANT]
    > Si noti che esistono limitazioni specifiche per i nomi che è possibile usare per i pool di Apache Spark. I nomi possono contenere solo lettere o numeri, devono essere costituiti da un massimo di 15 caratteri, devono iniziare con una lettera, non possono contenere parole riservate e devono essere univoci nell'area di lavoro.

3. Fare clic su **Avanti: Impostazioni aggiuntive** ed esaminare le impostazioni predefinite. Non modificare le impostazioni predefinite.
![Flusso di creazione del pool di Apache Spark - Scheda Impostazioni aggiuntive.](media/quickstart-create-apache-spark-pool/quickstart-create-spark-pool-03.png)

4. Fare clic su **Avanti: Tag**. Non aggiungere alcun tag.
![Flusso di creazione del pool di Apache Spark - Scheda Impostazioni aggiuntive.](media/quickstart-create-apache-spark-pool/quickstart-create-spark-pool-03-tags.png)

5. Fare clic su **Rivedi e crea**.

6. Verificare che i dettagli siano corretti in base a quanto specificato in precedenza, quindi fare clic su **Crea**.
![Flusso di creazione del pool di Apache Spark - Scheda Verificare le impostazioni.](media/quickstart-create-apache-spark-pool/quickstart-create-spark-pool-05.png)

7. A questo punto, il flusso di provisioning delle risorse viene avviato, indicando quando è completato ![Flusso di creazione del pool di Apache Spark - Provisioning delle risorse.](media/quickstart-create-apache-spark-pool/quickstart-create-spark-pool-06.png)

8. Al termine del provisioning, tornando nell'area di lavoro viene visualizzata una nuova voce per il pool di Apache Spark appena creato.
 ![Flusso di creazione del pool di Apache Spark - Provisioning delle risorse.](media/quickstart-create-apache-spark-pool/quickstart-create-spark-pool-07.png)

9. A questo punto, non sono in esecuzione risorse, non sono presenti addebiti per Spark e sono stati creati i metadati sulle istanze di Spark da creare.

## <a name="clean-up-resources"></a>Pulire le risorse

Seguire questa procedura per eliminare il pool di Apache Spark dall'area di lavoro.
> [!WARNING]
> Con l'eliminazione di un pool di Apache Spark, viene rimosso anche il motore di analisi dall'area di lavoro. Non sarà più possibile connettersi al pool di Apache Spark e tutte le query, le pipeline e i notebook che lo usano non funzioneranno più.

Per eliminare il pool di Apache Spark, procedere come segue:

1. Passare al pannello dei pool di Apache Spark nell'area di lavoro.
2. Selezionare il pool di Apache da eliminare (in questo caso, **contosospark**)
3. Premere **CANC**.
 ![Elenco di pool di Apache Spark con il pool creato di recente selezionato.](media/quickstart-create-apache-spark-pool/quickstart-create-spark-pool-08.png)
4. Confermare l'eliminazione e premere il pulsante **Elimina**.
 ![Finestra di dialogo di conferma per l'eliminazione del pool di Apache Spark selezionato.](media/quickstart-create-apache-spark-pool/quickstart-create-spark-pool-10.png)
5. Quando il processo viene completato, il pool di Apache Spark non sarà più elencato nelle risorse dell'area di lavoro.

Una volta creato, il pool SQL è disponibile nell'area di lavoro per il caricamento di dati, l'elaborazione di flussi, la lettura dal lake e così via.

## <a name="next-steps"></a>Passaggi successivi

- Vedere [Avvio rapido: Creare un pool di Apache Spark in Synapse Studio con strumenti Web](spark/apache-spark-notebook-create-spark-use-sql.md).
- Vedere [Avvio rapido: Creare un pool SQL Synapse con il portale di Azure](quickstart-create-sql-pool.md).
