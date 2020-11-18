---
title: 'Avvio rapido: Creare un pool SQL dedicato con Synapse Studio'
description: Per creare un pool SQL dedicato con Synapse Studio, seguire i passaggi descritti in questa guida.
services: synapse-analytics
author: julieMSFT
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: sql
ms.date: 10/16/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.openlocfilehash: f3ed5f3940a9b02701ad2f3c779b791e128e288c
ms.sourcegitcommit: 4bee52a3601b226cfc4e6eac71c1cb3b4b0eafe2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/11/2020
ms.locfileid: "94506346"
---
# <a name="quickstart-create-a-dedicated-sql-pool-using-synapse-studio"></a>Avvio rapido: Creare un pool SQL dedicato con Synapse Studio

Azure Synapse Analytics offre vari motori di analisi che consentono di inserire, trasformare, modellare e analizzare i dati. Un pool SQL dedicato offre funzionalità di calcolo e archiviazione basate su T-SQL. Dopo aver creato un pool SQL dedicato nell'area di lavoro di Synapse, è possibile caricare, modellare, elaborare e distribuire i dati per ottenere dati analitici in modo più rapido.

Questo argomento di avvio rapido descrive la procedura per creare un pool SQL dedicato in un’area di lavoro di Synapse con Synapse Studio.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito prima di iniziare](https://azure.microsoft.com/free/).


## <a name="prerequisites"></a>Prerequisiti

- Sottoscrizione di Azure: [creare un account gratuito](https://azure.microsoft.com/free/)
- [Area di lavoro Synapse](quickstart-create-workspace.md)

## <a name="sign-in-to-the-azure-portal"></a>Accedere al portale di Azure

Accedere al [portale di Azure](https://portal.azure.com/)

## <a name="navigate-to-the-synapse-workspace"></a>Accedere all'area di lavoro Synapse

1. Passare all'area di lavoro Synapse in cui verrà creato il pool SQL dedicato digitando il nome del servizio (o direttamente il nome della risorsa) nella barra di ricerca.

    ![Barra di ricerca del portale di Azure con le aree di lavoro di Synapse immesse.](media/quickstart-create-sql-pool/create-sql-pool-00a.png)
1. Nell'elenco delle aree di lavoro digitare il nome (o parte del nome) dell'area di lavoro da aprire. Per questo esempio verrà usata un'area di lavoro denominata **contosoanalytics**.

    ![Elenco delle aree di lavoro di Synapse filtrate per mostrare quelle che contengono il nome Contoso.](media/quickstart-create-sql-pool/create-sql-pool-00b.png)

## <a name="launch-synapse-studio"></a>Avviare Synapse Studio

1. Nella pagina di panoramica dell'area di lavoro selezionare l'**URL Web dell'area di lavoro** per avviare Synapse Studio.

    ![Panoramica dell'area di lavoro di Synapse nel portale di Azure con l'opzione URL Web dell'area di lavoro evidenziata.](media/quickstart-create-apache-spark-pool/create-spark-pool-studio-20.png)

## <a name="create-a-dedicated-sql-pool-in-synapse-studio"></a>Creare un pool SQL dedicato in Synapse Studio

1. Nella home page di Synapse Studio passare all'**hub di gestione** nel riquadro di navigazione a sinistra selezionando l'icona **Gestisci**.

    ![Home page di Synapse Studio con la sezione dell’hub di gestione evidenziata.](media/quickstart-create-apache-spark-pool/create-spark-pool-studio-21.png)

1. All’interno dell'hub di gestione, passare alla sezione dei **pool SQL** per visualizzare l'elenco corrente dei pool SQL disponibili nell'area di lavoro.

    ![Hub di gestione di Synapse Studio con la navigazione dei pool SQL evidenziata](media/quickstart-create-sql-pool/create-sql-pool-studio-22.png)

1. Selezionare il comando **+Nuovo** e verrà visualizzata la nuova procedura guidata di creazione dei pool SQL. 

    ![Elenco nell’hub di gestione di Synapse Studio dei pool SQL.](media/quickstart-create-sql-pool/create-sql-pool-studio-23.png)

1. Immettere i dettagli seguenti nella scheda **Informazioni di base**:

    | Impostazione | Valore consigliato | Descrizione |
    | :------ | :-------------- | :---------- |
    | **Nome del pool SQL** | contosoedw | Nome assegnato al pool SQL dedicato. |
    | **Livello di prestazioni** | DW100c | Per questa guida di avvio rapido, impostare questa opzione sulle dimensioni minime per ridurre i costi |

    ![Flusso di creazione del pool SQL - Scheda Informazioni di base.](media/quickstart-create-sql-pool/create-sql-pool-studio-24.png)
    > [!IMPORTANT]
    > Si noti che esistono limitazioni specifiche per i nomi che è possibile usare per i pool SQL dedicati. I nomi non possono contenere caratteri speciali, devono essere costituiti da un massimo di 15 caratteri, non possono contenere parole riservate e devono essere univoci nell'area di lavoro.

4. Nella scheda successiva **Impostazioni aggiuntive**, selezionare **nessuna** per eseguire il provisioning del pool SQL senza dati. Lasciare selezionate le regole di confronto predefinite.

    Per ripristinare il pool SQL dedicato da un punto di ripristino, selezionare **Punto di ripristino**. Per altre informazioni su come eseguire un ripristino, vedere [Procedura: Ripristinare un pool SQL dedicato esistente](backuprestore/restore-sql-pool.md)

    ![Flusso di creazione del pool SQL - Scheda Impostazioni aggiuntive.](media/quickstart-create-sql-pool/create-sql-pool-studio-25.png)

1. Per il momento non verranno aggiunti tag, quindi selezionare **Rivedi e crea**.

1. Nella scheda **Rivedi e crea**, verificare che i dettagli siano corretti in base a quanto specificato in precedenza, quindi premere **Crea**. 

    ![Flusso di creazione del pool SQL - Scheda Revisione impostazioni.](media/quickstart-create-sql-pool/create-sql-pool-studio-26.png)

1. A questo punto, verrà avviato il flusso di provisioning delle risorse.

1. Al termine del provisioning, tornando all'area di lavoro viene visualizzata una nuova voce per il pool SQL appena creato.

    ![Flusso di creazione del pool SQL - Provisioning delle risorse.](media/quickstart-create-sql-pool/create-sql-pool-studio-27.png)

1. Una volta creato, il pool SQL dedicato sarà disponibile nell'area di lavoro per il caricamento di dati, l'elaborazione di flussi, la lettura dal data lake e così via.

## <a name="clean-up-dedicated-sql-pool-using-synapse-studio"></a>Pulire il pool SQL dedicato con Synapse Studio    

Seguire questa procedura per eliminare il pool SQL dedicato dall'area di lavoro con Synapse Studio.
> [!WARNING]
> Con l'eliminazione di un pool SQL dedicato, viene rimosso anche il motore di analisi dall'area di lavoro. Non sarà più possibile connettersi al pool SQL dedicato e tutte le query, le pipeline e gli script che lo usano non funzioneranno più.

Per eliminare il pool SQL dedicato, procedere come segue:

1. Passare ai pool SQL nell'hub di gestione in Synapse Studio.
1. Selezionare i puntini di sospensione nel pool SQL dedicato da eliminare (in questo caso, **contosoedw**) per visualizzare i comandi relativi al pool SQL dedicato:

    ![Elenco di pool SQL con il pool creato di recente selezionato.](media/quickstart-create-sql-pool/create-sql-pool-studio-28.png)
1. Premere **CANC**.
1. Confermare l'eliminazione e fare clic sul pulsante **Elimina**.
1. Una volta completato il processo, il pool SQL dedicato non sarà più elencato nelle risorse dell'area di lavoro.

## <a name="next-steps"></a>Passaggi successivi 
- Vedere [Avvio rapido: Creare un notebook Apache Spark](quickstart-apache-spark-notebook.md).
- Vedere [Avvio rapido: Creare un pool SQL dedicato con il portale di Azure](quickstart-create-sql-pool-portal.md).
