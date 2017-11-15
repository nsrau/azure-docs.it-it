---
title: Archiviare i dati di monitoraggio di Azure | Microsoft Docs
description: Archiviare i dati di metrica e log generati all'interno di Azure per un account di archiviazione.
author: johnkemnetz
manager: orenr
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.service: monitoring-and-diagnostics
ms.topic: tutorial
ms.date: 09/25/2017
ms.author: johnkem
ms.custom: mvc
ms.openlocfilehash: f19cf8fddd9ffcf08b8ce18db070a7482ce012df
ms.sourcegitcommit: 6a6e14fdd9388333d3ededc02b1fb2fb3f8d56e5
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/07/2017
---
# <a name="archive-azure-monitoring-data"></a>Archiviare i dati di monitoraggio di Azure

I diversi livelli dell'ambiente Azure producono dati di metrica e log che possono essere archiviati in un account di archiviazione di Azure. È consigliabile eseguire questa operazione per mantenere una cronologia dei dati di monitoraggio nel tempo in un archivio non ricercabile e poco costoso alla fine del periodo di conservazione dei dati in Log Analytics o in Monitoraggio di Azure. Questa esercitazione illustra il processo di configurazione dell'ambiente di Azure al fine di archiviare i dati in un account di archiviazione.

> [!div class="checklist"]
> * Creare un account di archiviazione per conservare i dati di monitoraggio
> * Indirizzarvi i registri della sottoscrizione 
> * Indirizzarvi i dati della risorsa 
> * Indirizzarvi i dati della macchina virtuale, ovvero del sistema operativo guest 
> * Visualizzare i dati di monitoraggio nell'account di archiviazione 
> * Pulire le risorse 

Se non si ha una sottoscrizione di Azure, creare un account [gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="sign-in-to-the-azure-portal"></a>Accedere al portale di Azure

Accedere al [portale di Azure](https://portal.azure.com/).

## <a name="create-a-storage-account"></a>Creare un account di archiviazione

È necessario innanzitutto configurare un account di archiviazione in cui verranno archiviati i dati di monitoraggio. A questo scopo, [eseguire la procedura seguente](../storage/common/storage-create-storage-account.md).

## <a name="route-subscription-logs-to-the-storage-account"></a>Indirizzare i log della sottoscrizione all'account di archiviazione

A questo punto si è pronti per iniziare a configurare l'ambiente di Azure per indirizzare i dati di monitoraggio verso un account di archiviazione. È innanzitutto necessario configurare i dati a livello di sottoscrizione, ovvero quelli contenuti nel log attività di Azure, affinché vengano instradati all'account di archiviazione. Il [**log attività di Azure**](monitoring-overview-activity-logs.md) contiene una cronologia degli eventi a livello di sottoscrizione in Azure. È possibile visualizzarlo nel portale di Azure per determinare *chi* ha creato, aggiornato o eliminato *quali* risorse e *quando* è stato fatto.

1. Fare clic sul pulsante **Monitoraggio** nell'elenco di navigazione a sinistra, quindi su **Log attività**.

   ![Sezione Log attività](media/monitor-tutorial-archive-monitoring-data/activity-log-home.png)

2. Nella sezione Log attività visualizzata, fare clic sul pulsante **Esporta**.

3. Nella sezione **Esporta log attività** visualizzata selezionare la casella **Esporta in un account di archiviazione** e fare clic su **Selezionare un account di archiviazione**.

   ![Esportazione del log attività](media/monitor-tutorial-archive-monitoring-data/activity-log-export.png)

4. Nella sezione visualizzata usare il menu a discesa **Account di archiviazione** per selezionare il nome dell'account di archiviazione creato nel passaggio precedente **Creare un account di archiviazione**, quindi fare clic su **OK**.

   ![Scegliere un account di archiviazione](media/monitor-tutorial-archive-monitoring-data/activity-log-storage.png)

5. Impostare il dispositivo di scorrimento **Retention (days)** (Conservazione (giorni)) su 30. Questo dispositivo di scorrimento consente di impostare un numero di giorni per conservare i dati di monitoraggio nell'account di archiviazione. Monitoraggio di Azure elimina automaticamente i dati precedenti al numero di giorni specificato. Se il valore di conservazione è zero giorni, i dati vengono conservati all'infinito.

6. Fare clic su **Salva** e chiudere questa sezione.

Il monitoraggio dei dati nella sottoscrizione viene trasmesso all'account di archiviazione.

## <a name="route-resource-data-to-the-storage-account"></a>Indirizzare i dati della risorsa all'account di archiviazione

Ora vengono configurati i dati a livello di risorsa, ovvero le metriche delle risorse e i log di diagnostica, da indirizzare all'account di archiviazione tramite la configurazione delle **impostazioni di diagnostica per le risorse**.

1. Fare clic sul pulsante **Monitoraggio** nell'elenco di navigazione a sinistra, quindi su **Impostazioni di diagnostica**. Di seguito viene visualizzato un elenco di tutte le risorse nella sottoscrizione che genera dati di monitoraggio tramite Monitoraggio di Azure. Se nell'elenco non sono presenti risorse, è possibile [creare un'app per la logica](../logic-apps/logic-apps-create-a-logic-app.md) prima di procedere in modo da avere una risorsa per cui è possibile configurare un'impostazione di diagnostica.

2. Fare clic su una risorsa nell'elenco e quindi fare clic su **Attiva diagnostica**.
   
   ![Attivare la diagnostica](media/monitor-tutorial-archive-monitoring-data/diagnostic-settings-turn-on.png)

   Se è stata già configurata un'impostazione, vengono visualizzate le impostazioni esistenti e un pulsante per **aggiungere un'impostazione di diagnostica**. Fare clic su questo pulsante.

   Un'impostazione di diagnostica della risorsa è una definizione di *quali* dati di monitoraggio devono essere indirizzati da una particolare risorsa e *dove* tali dati di monitoraggio devono essere inseriti.

3. Nella sezione che viene visualizzata assegnare un **nome** all'impostazione e selezionare la casella **Archivia in un account di archiviazione**.

   ![Sezione sulle impostazioni di diagnostica](media/monitor-tutorial-archive-monitoring-data/diagnostic-settings-home.png)

4. Fare clic sul pulsante **Configura** in **Archivia in un account di archiviazione** e selezionare l'account di archiviazione creato nella sezione precedente. Fare clic su **OK**.

   ![Account di archiviazione per le impostazioni di diagnostica](media/monitor-tutorial-archive-monitoring-data/diagnostic-settings-storage.png)

5. Selezionare tutte le caselle in **Log** e **Metrica**. A seconda del tipo di risorsa, si può avere solo una di queste opzioni. Queste caselle di controllo consentono di controllare le categorie di log e dati di metrica disponibili per il tipo di risorsa che vengono inviate alla destinazione selezionata, in questo caso, un account di archiviazione.

   ![Categorie di impostazioni di diagnostica](media/monitor-tutorial-archive-monitoring-data/diagnostic-settings-categories.png)
   
6. Impostare il dispositivo di scorrimento **Retention (days)** (Conservazione (giorni)) su 30. Questo dispositivo di scorrimento consente di impostare un numero di giorni per conservare i dati di monitoraggio nell'account di archiviazione. Monitoraggio di Azure elimina automaticamente i dati precedenti al numero di giorni specificato. Se il valore di conservazione è zero giorni, i dati vengono conservati all'infinito.

7. Fare clic su **Salva**.

Il monitoraggio dei dati nella risorsa viene quindi trasmesso all'account di archiviazione.

## <a name="route-virtual-machine-guest-os-data-to-the-storage-account"></a>Indirizzare i dati della macchina virtuale, ovvero del sistema operativo guest, all'account di archiviazione

1. Se non si dispone già di una macchina virtuale nella sottoscrizione, [crearne una](../virtual-machines/windows/quick-create-portal.md).

2. Nell'elenco di navigazione a sinistra del portale fare clic su **Macchine virtuali**.

3. Nell'elenco delle macchine virtuali che viene visualizzato, fare clic sulla macchina virtuale creata.

4. Nella sezione che viene visualizzata fare clic su **Impostazioni di diagnostica** nel riquadro di spostamento a sinistra. Questa sezione consente di configurare l'estensione del monitoraggio pronta all'uso in Monitoraggio di Azure per la macchina virtuale e indirizza i dati generati da Windows o Linux all'account di archiviazione.

   ![Passare alle impostazioni di diagnostica](media/monitor-tutorial-archive-monitoring-data/guest-navigation.png)

5. Fare clic su **Abilita monitoraggio a livello di guest** nella sezione che viene visualizzata.

   ![Abilitare le impostazioni di diagnostica](media/monitor-tutorial-archive-monitoring-data/guest-enable.png)

6. Dopo aver salvato correttamente l'impostazione di diagnostica, la scheda **Panoramica** mostra un elenco di dati raccolti e il relativo percorso di archiviazione. Fare clic sulla sezione **Contatori di prestazioni** per esaminare l'insieme di contatori delle prestazioni di Windows raccolto.

   ![Impostazione dei contatori delle prestazioni](media/monitor-tutorial-archive-monitoring-data/guest-perf-counters.png)
   
7. Fare clic sulla scheda **Log** e selezionare le caselle di controllo per i log a livello di **Informazioni** nei log Applicazione e Sistema.

   ![Impostazioni dei log](media/monitor-tutorial-archive-monitoring-data/guest-logs.png)

8. Fare clic sulla scheda **Agente** e in **Account di archiviazione** fare clic sul nome dell'account di archiviazione indicato.

   ![Aggiornare l'account di archiviazione](media/monitor-tutorial-archive-monitoring-data/guest-storage-home.png)

9. Nella sezione visualizzata scegliere l'account di archiviazione creato nel passaggio precedente **Creare un account di archiviazione**.

10. Fare clic su **Salva**.

Il monitoraggio dei dati nella macchina virtuale viene quindi trasmesso all'account di archiviazione.

## <a name="view-the-monitoring-data-in-the-storage-account"></a>Visualizzare i dati di monitoraggio nell'account di archiviazione

Se è stata seguita la procedura precedente, il processo di trasmissione dei dati all'account di archiviazione è iniziato.

1. Per alcuni tipi di dati, ad esempio, il log attività, è necessario avere un'attività che genera un evento nell'account di archiviazione. Per generare un'attività nel log attività, seguire [queste istruzioni](./monitor-quick-audit-notify-action-in-subscription.md). Potrebbe essere necessario attendere fino a cinque minuti prima che l'evento venga visualizzato nell'account di archiviazione.

2. Nel portale passare alla sezione **Account di archiviazione** cercandola sulla barra di navigazione a sinistra.

3. Identificare l'account di archiviazione creato nella sezione precedente e fare clic su di esso.

4. Fare clic su **BLOB**, quindi sul contenitore con etichetta **insights-operational-logs** e infine sul contenitore con etichetta **name=default**. Questo è il contenitore che contiene il log attività. Il monitoraggio dei dati è suddiviso in contenitori identificati per ID di risorsa, solo l'ID di sottoscrizione per il log attività, quindi in base a data e ora. Il formato completo per tali BLOB è:

   insights-operational-logs/name=default/resourceId=/SUBSCRIPTIONS/{ID sottoscrizione}/y={anno a quattro cifre}/m={mese a due cifre}/d={giorno a due cifre}/h={ora a due cifre in formato 24 ore}/m=00/PT1H.json

5. Passare al file PT1H.json facendo clic sui contenitori per l'ID di risorsa, data e ora. Fare clic sul file PT1H.json e quindi su **Download**. Ogni BLOB PT1H.json contiene un BLOB JSON di eventi che si sono verificati nell'ora specificata nell'URL BLOB (ad esempio, h=12). Durante l'ora attuale, gli eventi vengono aggiunti al file PT1H.json man mano che si verificano. Il valore dei minuti (m=00) è sempre 00, perché gli eventi del log vengono suddivisi in singoli BLOB per ogni ora.

   È ora possibile visualizzare l'evento JSON che è stato archiviato nell'account di archiviazione. Per i log di diagnostica della risorsa, il formato per i BLOB è:

   insights-logs-{nome categoria log}/resourceId=/{ID risorsa}/y={anno numerico a quattro cifre}/m={mese numerico a due cifre}/d={giorno numerico a due cifre}/h={ora in formato 24 ore a due cifre}/m=00/PT1H.json

6. I dati di monitoraggio del sistema operativo guest vengono archiviati nelle tabelle. Tornare alla home dell'account di archiviazione e fare clic su **Tabelle**. Sono presenti tabelle per le metriche, contatori delle prestazioni e log di eventi.

Ora sono stati configurati correttamente i dati di monitoraggio da archiviare in un account di archiviazione.

## <a name="clean-up-resources"></a>Pulire le risorse

1. Tornare alla sezione **Esporta log attività** del passaggio precedente **Indirizzare i log della sottoscrizione all'account di archiviazione** e fare clic su **Reimposta**.

2. Passare alla sezione **Impostazioni di diagnostica**, fare clic sulla risorsa in cui è stata creata un'impostazione di diagnostica nel passaggio precedente **Indirizzare i log della sottoscrizione all'account di archiviazione**, quindi individuare l'impostazione creata, fare clic sul pulsante **Modifica impostazione** e quindi su **Elimina**.

3. Passare alla sezione **Impostazioni di diagnostica** nella macchina virtuale configurata nel passaggio precedente **Indirizzare i dati della macchina virtuale, ovvero del sistema operativo guest, all'account di archiviazione** e nella scheda **Agente** fare clic su **Rimuovi**, nella sezione **Rimuovi agente di Diagnostica di Azure**.

4. Passare all'account di archiviazione creato nel passaggio precedente **Creare un account di archiviazione** e fare clic su **Elimina account di archiviazione**. Digitare il nome dell'account di archiviazione e quindi fare clic su **Elimina**.

5. Se è stata creata una macchina virtuale o un'app per la logica nei passaggi precedenti, eliminarle.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato descritto come configurare i dati di monitoraggio dell'ambiente di Azure, ovvero sottoscrizione, risorse e sistema operativo guest, da archiviare in un account di archiviazione. 


> [!div class="checklist"]
> * Creare un account di archiviazione per conservare i dati di monitoraggio
> * Indirizzarvi i registri della sottoscrizione 
> * Indirizzarvi i dati della risorsa 
> * Indirizzarvi i dati della macchina virtuale, ovvero del sistema operativo guest 
> * Visualizzare i dati di monitoraggio nell'account di archiviazione 
> * Pulire le risorse 

Per sfruttare al meglio i dati e derivare informazioni aggiuntive, inviare i dati anche a Log Analytics.

> [!div class="nextstepaction"]
> [Introduzione a Log Analytics](../log-analytics/log-analytics-get-started.md)
