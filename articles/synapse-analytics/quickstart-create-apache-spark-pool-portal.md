---
title: 'Avvio rapido: creare un pool Apache Spark (anteprima) con il portale di Azure'
description: Per creare un nuovo pool Apache Spark con il portale di Azure, seguire i passaggi descritti in questa guida.
services: synapse-analytics
author: julieMSFT
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: ''
ms.date: 04/15/2020
ms.author: jrasnick
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 04a413ab171ea65c72182928ad4897530cb00e15
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83665408"
---
# <a name="quickstart-create-a-new-apache-spark-pool-preview-using-the-azure-portal"></a>Guida introduttiva: Creare un pool di Apache Spark con il portale di Azure

Azure Synapse Analytics offre vari motori di analisi che consentono di inserire, trasformare, modellare, analizzare e distribuire i dati. Un pool di Apache Spark offre funzionalità di calcolo open source per i Big Data. Dopo aver creato un pool Apache Spark nell'area di lavoro di Synapse, è possibile caricare, modellare, elaborare e distribuire i dati per ottenere dati analitici in modo più rapido.

Questa guida di avvio rapido illustra come usare il portale di Azure per creare un pool di Apache Spark in un'area di lavoro Synapse.

> [!IMPORTANT]
> La fatturazione delle istanze di Spark viene calcolata con ripartizione proporzionale al minuto, indipendentemente dal fatto che siano in uso o meno. Assicurarsi di arrestare l'istanza di Spark dopo averla usata oppure impostare un timeout breve. Per altre informazioni, vedere la sezione **Pulire le risorse** di questo articolo.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito prima di iniziare](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Prerequisiti

- Sottoscrizione di Azure: [creare un account gratuito](https://azure.microsoft.com/free/)
- [Area di lavoro di Synapse Analytics](quickstart-create-workspace.md)

## <a name="sign-in-to-the-azure-portal"></a>Accedere al portale di Azure

Accedere al [portale di Azure](https://portal.azure.com/)

## <a name="navigate-to-the-synapse-workspace"></a>Accedere all'area di lavoro Synapse 
1. Passare all'area di lavoro Synapse in cui verrà creato il pool Apache Spark digitando il nome del servizio (o direttamente il nome della risorsa) nella barra di ricerca.
![Barra di ricerca del portale di Azure con le aree di lavoro di Synapse immesse](media/quickstart-create-sql-pool/create-sql-pool-00a.png).
1. Dall'elenco delle aree di lavoro, digitare il nome (o parte del nome) dell'area di lavoro da aprire. Per questo esempio verrà usata un'area di lavoro denominata **contosoanalytics**.
![Elenco delle aree di lavoro Synapse filtrate per mostrare quelle che contengono il nome Contoso.](media/quickstart-create-sql-pool/create-sql-pool-00b.png)


## <a name="create-new-apache-spark-pool"></a>Creare un nuovo pool di Apache Spark

1. Nell'area di lavoro Synapse in cui creare il pool di Apache Spark selezionare **Nuovo pool di Apache Spark**.
    ![Panoramica dell'area di lavoro di Synapse con un riquadro rosso intorno al comando per la creazione di un nuovo pool di Apache Spark](media/quickstart-create-apache-spark-pool/create-spark-pool-portal-01.png)
2. Immettere i dettagli seguenti nella scheda **Informazioni di base**:

    |Impostazione | Valore consigliato | Descrizione |
    | :------ | :-------------- | :---------- |
    | **Nome del pool di Apache Spark** | Un nome di pool valido | Si tratta del nome che avrà il pool di Apache Spark. |
    | **Dimensioni nodo** | Piccole (4 vCPU/32 GB) | Per questa guida di avvio rapido, impostare questa opzione sulle dimensioni minime per ridurre i costi |
    | **Autoscale** | Disabled | La scalabilità automatica non è necessaria per questo argomento di avvio rapido |
    | **Numero di nodi** | 5 | Usare dimensioni ridotte per limitare i costi per questo argomento di avvio rapido |


    ![Flusso di creazione del pool di Apache Spark - Scheda Informazioni di base.](media/quickstart-create-apache-spark-pool/create-spark-pool-portal-02.png)
    > [!IMPORTANT]
    > Si noti che esistono limitazioni specifiche per i nomi che è possibile usare per i pool di Apache Spark. I nomi possono contenere solo lettere o numeri, devono essere costituiti da un massimo di 15 caratteri, devono iniziare con una lettera, non possono contenere parole riservate e devono essere univoci nell'area di lavoro.

3. Selezionare **Avanti: Impostazioni aggiuntive** ed esaminare le impostazioni predefinite. Non modificare le impostazioni predefinite.
    ![Flusso di creazione del pool di Apache Spark - Scheda Impostazioni aggiuntive.](media/quickstart-create-apache-spark-pool/create-spark-pool-portal-03.png)

4. Selezionare **Avanti: tag**. Non aggiungere alcun tag.
    ![Flusso di creazione del pool di Apache Spark - Scheda Impostazioni aggiuntive.](media/quickstart-create-apache-spark-pool/create-spark-pool-03-tags.png)

5. Selezionare **Rivedi e crea**.

6. Verificare che i dettagli siano corretti in base a quanto specificato in precedenza, quindi selezionare **Crea**.
    ![Flusso di creazione del pool di Apache Spark - Scheda Verificare le impostazioni.](media/quickstart-create-apache-spark-pool/create-spark-pool-portal-05.png)

7. A questo punto, verrà avviato il flusso di provisioning delle risorse e verrà indicato quando è completato.
    ![Flusso di creazione del pool di Apache Spark - Provisioning delle risorse.](media/quickstart-create-apache-spark-pool/create-spark-pool-portal-06.png)

8. Al termine del provisioning, tornando nell'area di lavoro viene visualizzata una nuova voce per il pool di Apache Spark appena creato.
    ![Flusso di creazione del pool di Apache Spark - Provisioning delle risorse.](media/quickstart-create-apache-spark-pool/create-spark-pool-portal-07.png)

9. A questo punto, non sono in esecuzione risorse, non sono presenti addebiti per Spark e sono stati creati i metadati sulle istanze di Spark da creare.

## <a name="clean-up-resources"></a>Pulire le risorse

Seguire questa procedura per eliminare il pool di Apache Spark dall'area di lavoro.
> [!WARNING]
> Con l'eliminazione di un pool di Apache Spark, viene rimosso anche il motore di analisi dall'area di lavoro. Non sarà più possibile connettersi al pool di Apache Spark e tutte le query, le pipeline e i notebook che lo usano non funzioneranno più.

Per eliminare il pool di Apache Spark, procedere come segue:

1. Passare al pannello dei pool di Apache Spark nell'area di lavoro.
2. Selezionare il pool di Apache Spark da eliminare (in questo caso, **contosospark**).
3. Premere **CANC**.
 ![Elenco di pool di Apache Spark con il pool creato di recente selezionato.](media/quickstart-create-apache-spark-pool/create-spark-pool-portal-08.png)
4. Confermare l'eliminazione e premere il pulsante **Elimina**.
 ![Finestra di dialogo di conferma per l'eliminazione del pool di Apache Spark selezionato.](media/quickstart-create-apache-spark-pool/create-spark-pool-portal-10.png)
5. Quando il processo viene completato, il pool di Apache Spark non sarà più elencato nelle risorse dell'area di lavoro.

## <a name="next-steps"></a>Passaggi successivi

- Vedere [Avvio rapido: Creare un notebook Apache Spark](quickstart-apache-spark-notebook.md).
- Vedere [Avvio rapido: Creare un pool SQL Synapse con il portale di Azure](quickstart-create-sql-pool-portal.md).
