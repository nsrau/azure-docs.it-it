---
title: 'Avvio rapido: Creare un pool di Apache Spark serverless (anteprima) con Synapse Studio'
description: Per creare un pool di Apache Spark serverless con Synapse Studio, eseguire i passaggi descritti in questa guida.
services: synapse-analytics
author: saveenr
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: spark
ms.date: 10/16/2020
ms.author: saveenr
ms.reviewer: jrasnick
ms.openlocfilehash: 313ad0c620fe06158e96c208ae265702134b58d1
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93324156"
---
# <a name="quickstart-create-a-serverless-apache-spark-pool-preview-using-synapse-studio"></a>Avvio rapido: Creare un pool di Apache Spark serverless (anteprima) con Synapse Studio

Azure Synapse Analytics offre vari motori di analisi che consentono di inserire, trasformare, modellare, analizzare e gestire i dati. Un pool di Apache Spark offre funzionalità di calcolo open source per i Big Data. Dopo aver creato un pool di Apache Spark nell'area di lavoro Synapse, è possibile caricare, modellare, elaborare e gestire i dati per ottenere informazioni dettagliate.  

Questa guida introduttiva descrive i passaggi per creare un pool di Apache Spark in un'area di lavoro di Synapse tramite Synapse Studio.

> [!IMPORTANT]
> La fatturazione delle istanze di Spark viene calcolata con ripartizione proporzionale al minuto, indipendentemente dal fatto che siano in uso o meno. Assicurarsi di arrestare l'istanza di Spark dopo averla usata oppure impostare un timeout breve. Per altre informazioni, vedere la sezione **Pulire le risorse** di questo articolo.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito prima di iniziare](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Prerequisiti

- Sottoscrizione di Azure: [creare un account gratuito](https://azure.microsoft.com/free/)
- [Area di lavoro Synapse](./quickstart-create-workspace.md)

## <a name="sign-in-to-the-azure-portal"></a>Accedere al portale di Azure

Accedere al [portale di Azure](https://portal.azure.com/)

## <a name="navigate-to-the-synapse-workspace"></a>Accedere all'area di lavoro di Synapse

1. Passare all'area di lavoro di Synapse in cui verrà creato il pool di Apache Spark digitando il nome del servizio (o direttamente il nome della risorsa) nella barra di ricerca.

    ![Barra di ricerca del portale di Azure con le aree di lavoro di Synapse immesse.](media/quickstart-create-sql-pool/create-sql-pool-00a.png)
1. Nell'elenco delle aree di lavoro digitare il nome (o parte del nome) dell'area di lavoro da aprire. Per questo esempio verrà usata un'area di lavoro denominata **contosoanalytics**.

    ![Elenco delle aree di lavoro di Synapse filtrate per mostrare quelle che contengono il nome Contoso.](media/quickstart-create-sql-pool/create-sql-pool-00b.png)

## <a name="launch-synapse-studio"></a>Avviare Synapse Studio 

Nella pagina di panoramica dell'area di lavoro selezionare l' **URL Web dell'area di lavoro** per aprire Synapse Studio.

![Panoramica dell'area di lavoro di Synapse del portale di Azure con l'opzione Avvia Synapse Studio evidenziata.](media/quickstart-create-apache-spark-pool/create-spark-pool-studio-20.png)

## <a name="create-the-apache-spark-pool-in-synapse-studio"></a>Creare il pool di Apache Spark in Synapse Studio

1. Nella home page di Synapse Studio passare all' **hub di gestione** nel riquadro di navigazione a sinistra selezionando l'icona **Gestisci**.

    ![Home page di Synapse Studio con la sezione dell'hub di gestione evidenziata.](media/quickstart-create-apache-spark-pool/create-spark-pool-studio-21.png)

1. Nell'hub di gestione passare alla sezione **Pool di Apache Spark** per visualizzare l'elenco corrente dei pool di Apache Spark disponibili nell'area di lavoro.
    
    ![Hub di gestione di Synapse Studio con lo spostamento al pool di Apache Spark selezionato](media/quickstart-create-apache-spark-pool/create-spark-pool-studio-22.png)

1. Selezionare **+ Nuovo** per visualizzare la procedura guidata per la creazione del nuovo pool di Apache Spark. 

1. Immettere i dettagli seguenti nella scheda **Informazioni di base** :

    | Impostazione | Valore consigliato | Descrizione |
    | :------ | :-------------- | :---------- |
    | **Nome del pool di Apache Spark** | contosospark | Si tratta del nome che avrà il pool di Apache Spark. |
    | **Dimensioni nodo** | Piccole (4 vCPU/32 GB) | Per questa guida di avvio rapido, impostare questa opzione sulle dimensioni minime per ridurre i costi |
    | **Autoscale** | Disabled | In questa guida introduttiva la scalabilità automatica non è necessaria. |
    | **Numero di nodi** | 8 | Usare dimensioni ridotte per limitare i costi per questa guida introduttiva|
       
    ![Scheda Informazioni di base del nuovo pool di Apache Spark di Synapse Studio](media/quickstart-create-apache-spark-pool/create-spark-pool-studio-24.png)
    
    > [!IMPORTANT]
    > Si noti che esistono limitazioni specifiche per i nomi che è possibile usare per i pool di Apache Spark. I nomi possono contenere solo lettere o numeri, devono essere costituiti da un massimo di 15 caratteri, devono iniziare con una lettera, non possono contenere parole riservate e devono essere univoci nell'area di lavoro.

1. Nella scheda successiva (Impostazioni aggiuntive) lasciare invariate tutte le impostazioni predefinite.

1. Per il momento non verranno aggiunti tag, quindi selezionare **Rivedi e crea**.

1. Nella scheda **Rivedi e crea** verificare che i dettagli siano corretti in base a quanto specificato in precedenza, quindi selezionare **Crea**. 

    ![Creazione del nuovo pool di Apache Spark di Synapse Studio](media/quickstart-create-apache-spark-pool/create-spark-pool-studio-26.png)

1. Il pool di Apache Spark avvierà il processo di provisioning.

1. Al termine del provisioning, il nuovo pool di Apache Spark verrà visualizzato nell'elenco.
    
    ![Nuovo pool di Apache Spark di Synapse Studio visualizzato nell'elenco](media/quickstart-create-apache-spark-pool/create-spark-pool-studio-28.png)

## <a name="clean-up-apache-spark-pool-resources-using-synapse-studio"></a>Eliminare le risorse del pool di Apache Spark tramite Synapse Studio

Per eliminare il pool di Apache Spark dall'area di lavoro tramite Synapse Studio, eseguire queste operazioni.
> [!WARNING]
> L'eliminazione di un pool SQL determina la rimozione del motore di analisi dall'area di lavoro. Non sarà più possibile connettersi al pool di Spark e tutti i notebook, le query e le pipeline che lo usano non funzioneranno più.

Per eliminare il pool di Apache Spark, procedere come segue:

1. Passare al pool di Apache Spark nell'hub di gestione in Synapse Studio.
1. Selezionare i puntini di sospensione accanto al pool di Apache da eliminare (in questo caso **contosospark** ) per visualizzare i comandi per il pool di Apache Spark.

    ![Elenco di pool di Apache Spark con il pool creato di recente selezionato.](media/quickstart-create-apache-spark-pool/create-spark-pool-studio-29.png)

1. Premere **CANC**.
1. Confermare l'eliminazione e fare clic sul pulsante **Elimina**.
1. Quando il processo viene completato, il pool di Apache Spark non sarà più elencato nelle risorse dell'area di lavoro. 

## <a name="next-steps"></a>Passaggi successivi

- Vedere [Avvio rapido: Creare un pool di Apache Spark in Synapse Studio con strumenti Web](quickstart-apache-spark-notebook.md).
- Vedere [Avvio rapido: Creare un pool di Apache Spark con il portale di Azure](quickstart-create-apache-spark-pool-portal.md).
