---
title: 'Avvio rapido: creare un pool Synapse SQL (anteprima) con Synapse Studio'
description: Per creare un nuovo pool Synapse SQL con Synapse Studio, seguire i passaggi descritti in questa guida.
services: synapse-analytics
author: julieMSFT
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: sql
ms.date: 3/19/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.openlocfilehash: f93eb55b888c58ad111bd67b2011ba9c996b16bb
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/05/2020
ms.locfileid: "85960266"
---
# <a name="quickstart-create-a-synapse-sql-pool-preview-using-synapse-studio"></a>Avvio rapido: creare un pool Synapse SQL (anteprima) con Synapse Studio

Azure Synapse Analytics offre vari motori di analisi che consentono di inserire, trasformare, modellare e analizzare i dati. Un pool SQL offre funzionalità di calcolo e archiviazione basate su T-SQL. Dopo aver creato un pool SQL nell'area di lavoro di Synapse, è possibile caricare, modellare, elaborare e consegnare i dati per ottenere dati analitici in modo più rapido.

Questo argomento di avvio rapido descrive la procedura per creare un pool SQL in un’area di lavoro di Synapse con Synapse Studio.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito prima di iniziare](https://azure.microsoft.com/free/).


## <a name="prerequisites"></a>Prerequisiti

- Sottoscrizione di Azure: [creare un account gratuito](https://azure.microsoft.com/free/)
- [Area di lavoro Synapse](quickstart-create-workspace.md)

## <a name="sign-in-to-the-azure-portal"></a>Accedere al portale di Azure

Accedere al [portale di Azure](https://portal.azure.com/)

## <a name="navigate-to-the-synapse-workspace"></a>Accedere all'area di lavoro Synapse

1. Passare all'area di lavoro Synapse in cui verrà creato il pool SQL digitando il nome del servizio (o direttamente il nome della risorsa) nella barra di ricerca.
![Barra di ricerca del portale di Azure con le aree di lavoro di Synapse immesse](media/quickstart-create-sql-pool/create-sql-pool-00a.png).
1. Dall'elenco delle aree di lavoro, digitare il nome (o parte del nome) dell'area di lavoro da aprire. Per questo esempio verrà usata un'area di lavoro denominata **contosoanalytics**.
![Elenco delle aree di lavoro Synapse filtrate per mostrare quelle che contengono il nome Contoso.](media/quickstart-create-sql-pool/create-sql-pool-00b.png)

## <a name="launch-synapse-studio"></a>Avviare Synapse Studio

1. Nella panoramica dell'area di lavoro selezionare **Avvia Synapse Studio** per aprire il percorso in cui verrà creato il pool SQL. Digitare il nome del servizio o il nome della risorsa direttamente nella barra di ricerca.
![Panoramica dell'area di lavoro Synapse del portale di Azure con Avvia Synapse Studio evidenziata.](media/quickstart-create-apache-spark-pool/create-spark-pool-studio-20.png)

## <a name="create-a-sql-pool-in-synapse-studio"></a>Creare un pool SQL in Synapse Studio

1. Nella home page di Synapse Studio passare all'**hub di gestione** nel pannello di navigazione a sinistra selezionando l'icona **Gestisci**.
![Home page di Synapse Studio con la sezione dell’hub di gestione evidenziata.](media/quickstart-create-apache-spark-pool/create-spark-pool-studio-21.png)

1. All’interno dell'hub di gestione, passare alla sezione dei **pool SQL** per visualizzare l'elenco corrente dei pool SQL disponibili nell'area di lavoro.
![Hub di gestione di Synapse Studio con la navigazione dei pool SQL evidenziata](media/quickstart-create-sql-pool/create-sql-pool-studio-22.png)

1. Selezionare il comando **+Nuovo** e verrà visualizzata la nuova procedura guidata di creazione dei pool SQL. 
![Elenco nell’hub di gestione di Synapse Studio dei pool SQL.](media/quickstart-create-sql-pool/create-sql-pool-studio-23.png)

1. Immettere i dettagli seguenti nella scheda **Informazioni di base**:

    | Impostazione | Valore consigliato | Descrizione |
    | :------ | :-------------- | :---------- |
    | **Nome del pool SQL** | contosoedw | Si tratta del nome che avrà il pool SQL. |
    | **Livello di prestazioni** | DW100c | Per questa guida di avvio rapido, impostare questa opzione sulle dimensioni minime per ridurre i costi |

    ![Flusso di creazione del pool SQL - Scheda Informazioni di base.](media/quickstart-create-sql-pool/create-sql-pool-studio-24.png)
    > [!IMPORTANT]
    > Si noti che esistono limitazioni specifiche per i nomi che è possibile usare per i pool SQL. I nomi non possono contenere caratteri speciali, devono essere costituiti da un massimo di 15 caratteri, non possono contenere parole riservate e devono essere univoci nell'area di lavoro.

4. Nella scheda successiva **Impostazioni aggiuntive**, selezionare **nessuna** per eseguire il provisioning del pool SQL senza dati. Lasciare selezionate le regole di confronto predefinite.
![Flusso di creazione del pool SQL - Scheda Impostazioni aggiuntive.](media/quickstart-create-sql-pool/create-sql-pool-studio-25.png)

1. Non verranno aggiunti tag per il momento, quindi selezionare **Avanti: Rivedi e crea**.

1. Nella scheda **Rivedi e crea**, verificare che i dettagli siano corretti in base a quanto specificato in precedenza, quindi premere **Crea**. 
![Flusso di creazione del pool SQL - Scheda Revisione impostazioni.](media/quickstart-create-sql-pool/create-sql-pool-studio-26.png)

1. A questo punto, verrà avviato il flusso di provisioning delle risorse.

1. Al termine del provisioning, tornando all'area di lavoro viene visualizzata una nuova voce per il pool SQL appena creato.
 ![Flusso di creazione del pool SQL - Provisioning delle risorse.](media/quickstart-create-sql-pool/create-sql-pool-studio-27.png)

1. Una volta completata la creazione, il pool SQL sarà disponibile nell'area di lavoro per il caricamento di dati, l'elaborazione di flussi, la lettura dal lake e così via.

## <a name="clean-up-sql-pools-using-synapse-studio"></a>Creare pool SQL con Synapse Studio    

Seguire questa procedura per eliminare il pool SQL dall'area di lavoro con Synapse Studio.
> [!WARNING]
> Con l'eliminazione di un pool SQL, viene rimosso anche il motore di analisi dall'area di lavoro. Non sarà più possibile connettersi al pool e tutte le query, le pipeline e gli script che lo usano non funzioneranno più.

Per eliminare il pool SQL, procedere come segue:

1. Passare ai pool SQL nell'hub di gestione in Synapse Studio.
1. Selezionare i puntini di sospensione nel pool SQL da eliminare (in questo caso, **contosoedw**) per visualizzare i comandi per il pool SQL: ![Elenco di pool SQL con il pool creato di recente selezionato.](media/quickstart-create-sql-pool/create-sql-pool-studio-28.png)
1. Premere **CANC**.
1. Confermare l'eliminazione e fare clic sul pulsante **Elimina**.
 ![Finestra di dialogo di conferma per l'eliminazione del pool SQL selezionato.](media/quickstart-create-sql-pool/create-sql-pool-studio-29.png)
1. Quando il processo viene completato, il pool SQL non sarà più elencato nelle risorse dell'area di lavoro.

## <a name="next-steps"></a>Passaggi successivi 
- Vedere [Avvio rapido: Creare un notebook Apache Spark](quickstart-apache-spark-notebook.md).
- Vedere [Avvio rapido: Creare un pool Synapse SQL con il portale di Azure](quickstart-create-sql-pool-portal.md).