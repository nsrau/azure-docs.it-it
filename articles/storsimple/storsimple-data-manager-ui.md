---
title: Interfaccia utente di Microsoft Azure StorSimple Data Manager | Microsoft Docs
description: Questo articolo illustra come usare l'interfaccia utente del servizio StorSimple Data Manager
services: storsimple
documentationcenter: NA
author: alkohli
manager: jeconnoc
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 01/16/2018
ms.author: alkohli
ms.openlocfilehash: d704cf8e6840c6a7b0a637c404d421f9f1497c46
ms.sourcegitcommit: 7edfa9fbed0f9e274209cec6456bf4a689a4c1a6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/17/2018
---
# <a name="manage-the-storsimple-data-manager-service-in-azure-portal"></a>Gestire il servizio StorSimple Data Manager nel portale di Azure

Questo articolo illustra come usare l'interfaccia utente di StorSimple Data Manager per trasformare i dati che risiedono sui dispositivi StorSimple serie 8000. I dati trasformati possono quindi essere impiegati da altri servizi di Azure, ad esempio Servizi multimediali di Azure, Azure HDInsight, Azure Machine Learning e Ricerca di Azure.


## <a name="use-storsimple-data-transformation"></a>Usare la trasformazione dati di StorSimple

StorSimple Data Manager è la risorsa all'interno della quale vengono create istanze di trasformazione dei dati. Il servizio Trasformazione dati consente di trasformare i dati dal formato StorSimple al formato nativo in BLOB o file di Azure. Per trasformare i dati in formato nativo di StorSimple, è necessario specificare i dettagli relativi al dispositivo StorSimple serie 8000 e i dati da trasformare.

### <a name="create-a-storsimple-data-manager-service"></a>Creare un servizio StorSimple Data Manager

Eseguire la procedura seguente per creare un servizio StorSimple Data Manager.

1. Usare le credenziali dell'account Microsoft per accedere al [portale di Azure](https://portal.azure.com/).

2. Fare clic su **+ Crea una risorsa** e cercare StorSimple Data Manager.

    ![Creare un servizio StorSimple Data Manager 1](./media/storsimple-data-manager-ui/create-service-1.png)

3. Fare clic su StorSimple Data Manager, quindi su **Crea**.
    
    ![Creare un servizio StorSimple Data Manager 2](./media/storsimple-data-manager-ui/create-service-3.png)

3. Per il nuovo servizio, specificare quanto segue:

    1. Fornire un **nome servizio** univoco per StorSimple Data Manager. Si tratta di un nome descrittivo che può essere utilizzato per identificare il servizio. Il nome può contenere da 3 a 24 caratteri che possono essere lettere, numeri e trattini. Il nome deve iniziare e terminare con una lettera o un numero.

    2. Scegliere una **sottoscrizione** dall'elenco a discesa. La sottoscrizione viene collegata all'account di fatturazione. Se si dispone di una sola sottoscrizione, questo campo viene popolato automaticamente (e non è selezionabile).

    3. Selezionare un gruppo di risorse esistente o crearne uno nuovo. Per altre informazioni, vedere [Azure resource groups](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-infrastructure-resource-groups-guidelines/) (Gruppi di risorse di Azure).

    4. Specificare il **percorso** del servizio che ospita gli account di archiviazione e il servizio StorSimple Data Manager. Il servizio Gestione dispositivi StorSimple, il servizio Data Manager e l'account di archiviazione associato devono trovarsi tutti nelle aree supportate.
    
    5. Per inserire un collegamento al servizio nel dashboard, selezionare **Aggiungi al dashboard**.
    
    6. Fare clic su **Crea**.

    ![Creare un servizio StorSimple Data Manager 3](./media/storsimple-data-manager-ui/create-service-4.png)

La creazione del servizio richiede alcuni minuti. Al termine della creazione del servizio, verranno visualizzati una notifica e il nuovo servizio.

### <a name="create-a-data-transformation-job-definition"></a>Creare una definizione di processo di trasformazione dati

All'interno di un servizio StorSimple Data Manager è necessario creare una definizione di processo di trasformazione dati. Tale definizione specifica i dettagli dei dati di StorSimple da spostare in un account di archiviazione nel formato nativo. Dopo aver creato una definizione del processo, è possibile eseguire nuovamente questo processo con altre impostazioni di runtime.

Per creare una definizione del processo, seguire questa procedura.

1. Passare al servizio creato. Passare a **Gestione > Definizioni processi**.

2. Fare clic su **+ Definizione processo**.

    ![Fare clic su + Definizione processo](./media/storsimple-data-manager-ui/create-job-definition-1.png)

3. Specificare un nome per la definizione del processo. La lunghezza del nome deve essere compresa tra 3 e 63 caratteri. Il nome può contenere lettere maiuscole e minuscole, numeri e trattini.

4. Specificare un percorso in cui eseguire il processo. Questo percorso può essere diverso da quello in cui viene distribuito il servizio.

5. Fare clic su **Origine** per specificare il repository dei dati di origine.

    ![Configurare il repository dei dati di origine](./media/storsimple-data-manager-ui/create-job-definition-2.png)

6. Poiché si tratta di un nuovo servizio Data Manager, non sono presenti repository di dati configurati. In **Configura origine dati** specificare i dettagli del dispositivo StorSimple serie 8000 e i dati di interesse.

   Per aggiungere Gestione dispositivi StorSimple come repository dei dati, fare clic su **Aggiungi nuovo** nell'elenco a discesa dei repository dei dati e quindi fare clic su **Aggiungi repository dei dati**.

    ![Aggiungere un nuovo repository dei dati](./media/storsimple-data-manager-ui/create-job-definition-3.png)
  
    1. Scegliere **StorSimple Manager serie 8000** come tipo di repository dei dati.
    
    2. Immettere un nome descrittivo per il repository dei dati di origine.
    
    3. Nell'elenco a discesa, scegliere una sottoscrizione associata al servizio Gestione dispositivi StorSimple.
    
    4. Specificare il nome del servizio Gestione dispositivi StorSimple in **Risorsa**.

    5. Immettere un valore in **Chiave DEK del servizio** per il servizio Gestione dispositivi StorSimple. 

    ![Configurare il repository dei dati di origine 1](./media/storsimple-data-manager-ui/create-job-definition-4.png)

    Fare clic su **OK** al termine dell'operazione. In questo modo viene salvato il repository dei dati. Riutilizzare queste impostazioni di Gestione dispositivi StorSimple in altre definizioni del processo, in modo da non dover immettere nuovamente tali parametri. Dopo aver fatto clic su **OK** attendere pochi secondi prima che il repository dei dati di origine creato venga visualizzato nell'elenco a discesa.

7. Nell'elenco a discesa **Repository dei dati** selezionare il repository dei dati creato. 

    1. Immettere il nome del dispositivo StorSimple serie 8000 contenente i dati di interesse.

    2. Specificare il nome del volume che si trova nel dispositivo StorSimple contenente i dati di interesse.

    3. Nella sottosezione **Filtro** immettere la directory radice contenente i dati di interesse nel formato _\DirectoryRadice\Dati_. Le lettere di unità, ad esempio _\C:\Dati_, non sono supportate. È inoltre possibile aggiungere qualsiasi filtro file qui.

    4. Il servizio di trasformazione dati funziona sui dati inseriti in Azure tramite snapshot. Quando si esegue questo processo, è possibile scegliere di completare un backup a ogni esecuzione del processo (per lavorare sui dati più recenti) o usare l'ultimo backup esistente nel cloud (se si lavora con alcuni dati archiviati).

    5. Fare clic su **OK**.

    ![Configurare il repository dei dati di origine 2](./media/storsimple-data-manager-ui/create-job-definition-8.png)

8. Successivamente, è necessario configurare il repository dei dati di destinazione. Scegliere gli account di archiviazione per l'inserimento dei file nei BLOB di tale account. Nell'elenco a discesa selezionare **Aggiungi nuovo** e quindi **Configura impostazioni**.

9. Selezionare il tipo di repository di destinazione che si vuole aggiungere e gli altri parametri associati.

    Se si seleziona una destinazione di tipo account di archiviazione, è possibile specificare un nome descrittivo, una sottoscrizione (selezionare la stessa del servizio o un'altra) e un account di archiviazione.
        ![Configurare il repository dei dati di destinazione 1](./media/storsimple-data-manager-ui/create-job-definition-10.png)

    Quando viene eseguito il processo, viene creata una coda di archiviazione. Questa coda viene popolata con i messaggi relativi ai BLOB trasformati non appena pronti. Il nome di questa coda corrisponde a quello della definizione di processo.
    
10. Dopo aver aggiunto il repository dei dati, attendere alcuni minuti.
    
    1. Selezionare il repository creato come destinazione nell'elenco a discesa **Target account name** (Nome account di destinazione).

    2. Scegliere il tipo di archiviazione, ad esempio BLOB o file. Specificare il nome del contenitore di archiviazione in cui si trovano i dati trasformati. Fare clic su **OK**.

        ![Configurare l'account di archiviazione del repository dei dati di destinazione](./media/storsimple-data-manager-ui/create-job-definition-16.png)

11. È anche possibile selezionare l'opzione per ottenere una stima della durata del processo prima di eseguirlo. Fare clic su **OK** per creare la definizione di processo. La definizione del processo è ora impostata. È possibile usare questa definizione più volte tramite l'interfaccia utente con altre impostazioni di runtime.

    ![Definizione del processo completata](./media/storsimple-data-manager-ui/create-job-definition-13.png)

    La definizione del processo appena creata viene aggiunta all'elenco delle definizioni del processo per questo servizio.

### <a name="run-the-job-definition"></a>Eseguire la definizione di processo

Per spostare i dati da StorSimple all'account di archiviazione specificato nella definizione del processo, è necessario eseguire tale definizione. In fase di esecuzione, è possibile specificare alcuni parametri in modo diverso. Attenersi alla procedura seguente:

1. Fare clic sul servizio StorSimple Data Manager e passare a **Gestione > Definizioni processi**. Selezionare e fare clic sulla definizione del processo che si vuole eseguire.
     
     ![Avviare l'esecuzione del processo 1](./media/storsimple-data-manager-ui/start-job-run1.png)

2. Fare clic su **Esegui adesso**.
     
     ![Avviare l'esecuzione del processo 2](./media/storsimple-data-manager-ui/start-job-run2.png)

3. Fare clic su **Impostazioni di esecuzione** per modificare le impostazioni che si desidera cambiare per l'esecuzione del processo. Fare clic su **OK** quindi fare clic su **Esegui** per avviare il processo.

    ![Avviare l'esecuzione del processo 3](./media/storsimple-data-manager-ui/start-job-run3.png)

4. Per monitorare il processo, passare a **Processi** in StorSimple Data Manager. Oltre al monitoraggio nel pannello **Processi** è possibile anche restare in ascolto sulla coda di archiviazione in cui viene aggiunto un messaggio ogni volta che un file viene spostato da StorSimple all'account di archiviazione.

    ![Avviare l'esecuzione del processo 4](./media/storsimple-data-manager-ui/start-job-run4.png)


## <a name="next-steps"></a>Passaggi successivi

[Usare .NET SDK per avviare i processi di StorSimple Data Manager](storsimple-data-manager-dotnet-jobs.md).