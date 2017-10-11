---
title: Interfaccia utente di Microsoft Azure StorSimple Data Manager | Documentazione Microsoft
description: Questo articolo descrive come usare l'interfaccia utente del servizio StorSimple Data Manager (anteprima privata)
services: storsimple
documentationcenter: NA
author: vidarmsft
manager: syadav
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/22/2016
ms.author: vidarmsft
ms.openlocfilehash: 53a8599df2c647613122cd791b680e2e658586b0
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2017
---
# <a name="manage-using-the-storsimple-data-manager-service-ui-private-preview"></a>Gestire tramite l'interfaccia utente del servizio StorSimple Data Manager (anteprima privata)

Questo articolo illustra come usare l'interfaccia utente di StorSimple Data Manager per eseguire la trasformazione dei dati che risiedono sui dispositivi StorSimple serie 8000. I dati trasformati possono quindi essere impiegati da altri servizi di Azure, ad esempio Servizi multimediali di Azure, Azure HDInsight, Azure Machine Learning e Ricerca di Azure. 


## <a name="use-storsimple-data-transformation"></a>Usare la trasformazione dati di StorSimple

StorSimple Data Manager è la risorsa all'interno della quale è possibile creare istanze di trasformazione dati. Il servizio di trasformazione dei dati consente di spostare dati dal dispositivo StorSimple locale ai BLOB in Archiviazione di Azure. Di conseguenza, nel flusso di lavoro è necessario specificare i dettagli relativi al dispositivo StorSimple e i dati di interesse da spostare nell'account di archiviazione.

### <a name="create-a-storsimple-data-manager-service"></a>Creare un servizio StorSimple Data Manager

Eseguire la procedura seguente per creare un servizio StorSimple Data Manager.

1. Per creare un servizio StorSimple Data Manager, fare riferimento a [https://aka.ms/HybridDataManager](https://aka.ms/HybridDataManager)

2. Fare clic sull'icona **+** e ricercare StorSimple Data Manager. Fare clic sul servizio StorSimple Data Manager, quindi su **Crea**.

3. Se la sottoscrizione è abilitata per la creazione di questo servizio, viene visualizzato il pannello seguente.

    ![Creare una risorsa StorSimple Data Manager](./media/storsimple-data-manager-ui/create-new-data-manager-service.png)

4. Immettere i dati necessari e fare clic su **Crea**. Il percorso specificato deve essere quello che ospita gli account di archiviazione e il servizio StorSimple Manager. Attualmente sono supportate solo le aree Europa occidentale e Stati Uniti occidentali. Di conseguenza, il servizio StorSimple Manager, il servizio Data Manager e l'account di archiviazione associato devono essere tutti nelle aree supportate indicate in precedenza. La creazione del servizio richiede circa un minuto.

### <a name="create-a-data-transformation-job-definition"></a>Creare una definizione di processo di trasformazione dati

All'interno di un servizio StorSimple Data Manager è necessario creare una definizione di processo di trasformazione dati. Tale definizione specifica i dettagli dei dati da spostare in un account di archiviazione nel formato nativo. 

Eseguire la procedura seguente per creare una nuova definizione di processo di trasformazione dati.

1.  Passare al servizio creato. Fare clic su **+ Job Definition** (+ Definizione processo).

    ![Fare clic su + Definizione processo](./media/storsimple-data-manager-ui/click-add-job-definition.png)

2. Si apre il pannello della nuova definizione processo. Assegnare un nome alla definizione di processo e fare clic su **Origine**. Nel pannello **Configure data source** (Configura origine dati) specificare i dettagli del dispositivo StorSimple e i dati di interesse.

    ![Creare la definizione di processo](./media/storsimple-data-manager-ui//create-new-job-deifnition.png)

3. Poiché si tratta di un nuovo servizio Data Manager, non sono presenti repository di dati configurati. Per aggiungere il servizio StorSimple Manager come un repository di dati, fare clic su **Aggiungi nuovo** nell'elenco a discesa dei repository di dati e quindi fare clic su **Add Data Repository** (Aggiungi repository di dati).

4. Scegliere **StorSimple Manager serie 8000** come tipo di repository e immettere le proprietà del servizio **StorSimple Manager**. Nel campo **ID risorsa** è necessario immettere il numero che precede **:** nella chiave di registrazione del servizio StorSimple Manager.

    ![Creare un'origine dati](./media/storsimple-data-manager-ui/create-new-data-source.png)

5.  Fare clic su **OK** al termine dell'operazione. In questo modo sarà possibile salvare il repository di dati e usare questo servizio StorSimple Manager in altre definizioni di processo senza dover immettere nuovamente tali parametri. Dopo aver fatto clic su **OK** attendere pochi secondi prima che StorSimple Manager venga visualizzato nell'elenco a discesa.

6.  Nel pannello **Configure data source** (Configura origine dati) immettere il nome del dispositivo e il nome del volume contenente i dati di interesse.

7.  Nella sottosezione **Filtro** immettere la directory radice contenente i dati di interesse (questo campo deve iniziare con `\`). È inoltre possibile aggiungere qualsiasi filtro file qui.

8.  Il servizio di trasformazione dati funziona sui dati inseriti in Azure tramite snapshot. Quando si esegue questo processo, è possibile scegliere di completare un backup a ogni esecuzione del processo (per lavorare sui dati più recenti) o usare l'ultimo backup esistente nel cloud (se si lavora con alcuni dati archiviati).

    ![Dettagli nuova origine dati](./media/storsimple-data-manager-ui/new-data-source-details.png)

9. Successivamente, è necessario configurare le impostazioni di destinazione. Sono previsti 2 tipi di destinazioni supportate: account di Archiviazione di Azure e account di Servizi multimediali di Azure. Scegliere gli account di archiviazione per l'inserimento dei file nei BLOB di tale account. Scegliere l'account dei servizi multimediali per l'inserimento dei file negli asset di tale account. Anche in questo caso è necessario aggiungere un repository. Nell'elenco a discesa selezionare **Aggiungi nuovo** e quindi **Configura impostazioni**.

    ![Creare un sink dati](./media/storsimple-data-manager-ui/create-new-data-sink.png)

10. In questo caso, è possibile selezionare il tipo di repository che si desidera aggiungere e gli altri parametri a esso associati. In entrambi i casi, quando viene eseguito il processo, viene creata una coda di archiviazione. Questa coda viene popolata con i messaggi relativi ai BLOB trasformati non appena pronti. Il nome di questa coda corrisponde a quello della definizione di processo. Se si seleziona **Servizi multimediali** come tipo di repository, è quindi possibile immettere anche le credenziali dell'account di archiviazione in cui viene creata la coda.

    ![Dettagli nuovo sink dati](./media/storsimple-data-manager-ui/new-data-sink-details.png)

11. Dopo aver aggiunto il repository di dati, operazione che richiede alcuni secondi, questo verrà incluso nell'elenco a discesa nel **nome dell'account di destinazione**.  Scegliere la destinazione desiderata.

12. Fare clic su **OK** per creare la definizione di processo. La definizione di processo è ora impostata. È possibile usare questa definizione più volte tramite l'interfaccia utente.

    ![Aggiungere una nuova definizione di processo](./media/storsimple-data-manager-ui/add-new-job-definition.png)

### <a name="run-the-job-definition"></a>Eseguire la definizione di processo

Quando si desidera spostare i dati da StorSimple all'account di archiviazione specificato nella definizione di processo, è necessario richiamarlo. Ogni volta che si richiama il processo, è prevista una determinata flessibilità di modifica dei parametri. Attenersi alla procedura seguente:

1. Fare clic sul servizio StorSimple Data Manager e passare a **Monitoraggio**. Fare clic su **Esegui adesso**.

    ![Attivare la definizione di processo](./media/storsimple-data-manager-ui/run-now.png)

2. Scegliere la definizione di processo che si desidera eseguire. Fare clic su **Impostazioni di esecuzione** per modificare le impostazioni che si desidera cambiare per l'esecuzione del processo.

    ![Impostazioni di esecuzione del processo](./media/storsimple-data-manager-ui/run-settings.png)

3. Fare clic su **OK** quindi fare clic su **Esegui** per avviare il processo. Per monitorare il processo, passare alla pagina **Processi** in StorSimple Data Manager.

    ![Elenco e stato dei processi](./media/storsimple-data-manager-ui/jobs-list-and-status.png)

4. Oltre al monitoraggio nel pannello **Processi** è possibile anche restare in ascolto sulla coda di archiviazione in cui viene aggiunto un messaggio ogni volta che un file viene spostato da StorSimple all'account di archiviazione.


## <a name="next-steps"></a>Passaggi successivi

[Usare .NET SDK per avviare i processi di StorSimple Data Manager](storsimple-data-manager-dotnet-jobs.md).