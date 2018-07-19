---
title: Salvare i messaggi dell'hub IoT nell'archivio dati di Azure | Microsoft Docs
description: Usare il routing dei messaggi dell'hub IoT per salvare i messaggi dell'hub IoT nel servizio Archiviazione BLOB di Azure. I messaggi dell'hub IoT contengono informazioni, come i dati di sensori, inviate dal dispositivo IoT.
author: rangv
manager: ''
keywords: archiviazione dei dati IoT, archiviazione dei dati di sensori IoT
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 04/11/2018
ms.author: rangv
ms.openlocfilehash: 84355453a5cb8d8f42abdcbde5432651c9c035b0
ms.sourcegitcommit: 0b4da003fc0063c6232f795d6b67fa8101695b61
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/05/2018
ms.locfileid: "37856291"
---
# <a name="save-iot-hub-messages-that-contain-sensor-data-to-your-azure-blob-storage"></a>Salvare i messaggi dell'hub IoT che contengono dati di sensori nel servizio Archiviazione BLOB di Azure

![Diagramma end-to-end](./media/iot-hub-store-data-in-azure-table-storage/1_route-to-storage.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

## <a name="what-you-learn"></a>Contenuto dell'esercitazione

Si apprenderà come creare un account di archiviazione di Azure e un'app per le funzioni di Azure per archiviare i messaggi dell'hub IoT nell'archiviazione Blob di Azure.

## <a name="what-you-do"></a>Operazioni da fare

- Creare un account di archiviazione di Azure
- Configurare l'hub IoT per il routing dei messaggi del servizio di archiviazione.

## <a name="what-you-need"></a>Elementi necessari

- [Configurare il dispositivo](iot-hub-raspberry-pi-kit-node-get-started.md) in modo da soddisfare i requisiti seguenti:
  - Una sottoscrizione di Azure attiva
  - Un hub IoT nella sottoscrizione 
  - Un'applicazione in esecuzione che invia messaggi all'hub IoT di Azure

## <a name="create-an-azure-storage-account"></a>Creare un account di archiviazione di Azure

1. Nel [portale di Azure](https://portal.azure.com/) fare clic su **Crea una risorsa** >  **Archiviazione** > **Account di archiviazione**.

2. Immettere le informazioni necessarie per l'account di archiviazione:

   ![Creare un account di archiviazione nel portale di Azure](./media/iot-hub-store-data-in-azure-table-storage/1_azure-portal-create-storage-account.png)

   * **Nome**: nome dell'account di archiviazione. Il nome deve essere univoco a livello globale.

   * **Tipo di account**: scegliere `Storage (general purpose v1)`.

   * **Posizione**: scegliere la stessa posizione che usa l'hub IoT.

   * **Replica**: scegliere `Locally-redundant storage (LRS)`.

   * **Prestazioni**: scegliere `Standard`.

   * **Trasferimento sicuro obbligatorio**: scegliere `Disabled`.

   * **Sottoscrizione**: selezionare una sottoscrizione di Azure.

   * **Gruppo di risorse**: usare lo stesso gruppo di risorse usato da hub IoT.

   * **Aggiungi al dashboard**: selezionare questa opzione per semplificare l'accesso all'hub IoT dal dashboard.

3. Fare clic su **Crea**.

## <a name="prepare-your-iot-hub-to-route-messages-to-storage"></a>Preparare l'hub IoT per il routing dei messaggi nel servizio di archiviazione

L'hub IoT supporta a livello nativo il routing dei messaggi nel servizio di archiviazione di Azure sotto forma di BLOB. Per altre informazioni sugli endpoint personalizzati dell'hub IoT di Azure, è possibile fare riferimento all'[elenco di endpoint dell'hub IoT incorporati](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-endpoints#custom-endpoints).

### <a name="add-storage-as-a-custom-endpoint"></a>Aggiungere risorse di archiviazione come endpoint personalizzato

1. Passare all'hub IoT nel portale di Azure. 

2. Fare clic su **Endpoint** > **Aggiungi**. 

3. Assegnare un nome all'endpoint e selezionare **Contenitore di archiviazione di Azure** come tipo di endpoint. 

4. Utilizzare la selezione per selezionare l'account di archiviazione creato nella sezione precedente. Creare un contenitore di archiviazione, selezionarlo e quindi fare clic su **OK**.

   ![Creare un endpoint personalizzato nell'hub IoT](./media/iot-hub-store-data-in-azure-table-storage/2_custom-storage-endpoint.png)

### <a name="add-a-route-to-route-data-to-storage"></a>Aggiungere una route per eseguire il routing dei dati alla risorsa di archiviazione

1. Fare clic su **Route** > **Aggiungi** e quindi immettere il nome della route. 

2. Selezionare **Messaggi del dispositivo** come origine dati e selezionare l'endpoint della risorsa di archiviazione creata come endpoint nella route. 

3. Immettere `true` come stringa di query e quindi fare clic su **Salva**.

   ![Creare una route nell'hub IoT](./media/iot-hub-store-data-in-azure-table-storage/3_create-route.png)
  
### <a name="add-a-route-for-hot-path-telemetry-optional"></a>Aggiungere una route per i dati di telemetria con percorso prioritario (facoltativo)

Per impostazione predefinita, l'hub IoT esegue il routing all'endpoint predefinito di tutti i messaggi che non corrispondono ad altre route. Poiché tutti i messaggi relativi ai dati di telemetria ora corrispondono alla regola che esegue il routing dei messaggi alla risorsa di archiviazione, è necessario aggiungere un'altra route per i messaggi da scrivere nell'endpoint predefinito. Non è previsto alcun costo aggiuntivo per il routing dei messaggi a più endpoint.

> [!NOTE]
> Se non vengono eseguite altre operazioni di elaborazione dei messaggi relativi ai dati di telemetria, è possibile ignorare questo passaggio.

1. Fare clic su **Aggiungi** nel riquadro Route e immettere un nome per la route. 

2. Selezionare **Messaggi del dispositivo** come origine dei dati ed **venti** come endpoint. 

3. Immettere `true` come stringa di query e quindi fare clic su **Salva**.

  ![Creare una route con percorso prioritario nell'hub IoT](./media/iot-hub-store-data-in-azure-table-storage/4_hot-path-route.png)

## <a name="verify-your-message-in-your-storage-container"></a>Verificare il messaggio nel contenitore di archiviazione

1. Eseguire l'applicazione di esempio nel dispositivo per inviare messaggi all'hub IoT.

2. [Scaricare e installare Azure Storage Explorer](http://storageexplorer.com/).

3. Aprire Storage Explorer, fare clic su **Add an Azure Account** (Aggiungi un account Azure)  >  **Accedi** e quindi accedere all'account Azure.

4. Fare clic sulla sottoscrizione di Azure in uso > **Account di archiviazione** > account di archiviazione in uso > **contenitori BLOB** > contenitore BLOB in uso.

   Nel contenitore BLOB saranno visibili i messaggi inviati dal dispositivo all'hub IoT.

## <a name="clean-up-resources"></a>Pulire le risorse 

In questa esercitazione è stato aggiunto un account di archiviazione e in seguito il routing per i messaggi dall'hub IoT per essere scritti nell'account di archiviazione. Per pulire le risorse create, rimuovere le informazioni di routing e quindi eliminare l'account di archiviazione. 

1. Accedere al [portale di Azure](https://portal.azure.com).

2. Fare clic su **Gruppi di risorse** e selezionare il gruppo di risorse che è stato utilizzato. Viene visualizzato l'elenco delle risorse nel gruppo. 

   > [!NOTE]
   > Se si desidera rimuovere tutte le risorse nel gruppo di risorse, fare clic su **Elimina** per eliminare il gruppo di risorse, quindi seguire le istruzioni. In questo modo verranno rimossi tutti gli elementi nel gruppo di risorse e la pulitura risulterà terminata, quindi sarà possibile passare alla sezione successiva.

3. Fare clic sull'hub IoT che è stato usato per questa esercitazione. 

4. Nel riquadro dell'hub IoT, fare clic su **Route**. Selezionare la casella di controllo accanto alla regola di routing che è stata aggiunta, quindi fare clic su **Elimina**. Quando viene richiesto se si è certi di voler eliminare tale route, fare clic su **Sì**.

   ![Rimuovere la regola di routing](./media/iot-hub-store-data-in-azure-table-storage/cleanup-remove-routing.png)

   Chiudere il riquadro di routing. Tornare al riquadro del gruppo di risorse.

5. Fare nuovamente clic sull'hub IoT. 

6. Nel riquadro dell'hub IoT fare clic su **Endpoint**. Selezionare la casella di controllo accanto all'endpoint che è stato aggiunto per il contenitore di archiviazione, quindi fare clic su **Elimina**. Quando viene richiesto se si è certi di voler eliminare tale route, fare clic su **Sì**.

    ![Rimuovere endpoint](./media/iot-hub-store-data-in-azure-table-storage/cleanup-remove-endpoint.png)

    Chiudere il riquadro Endpoint. Tornare al riquadro del gruppo di risorse. 

7.  Fare clic sull'account di archiviazione impostato per questa esercitazione. 

8.  Nel riquadro account di archiviazione, fare clic su **Elimina** per rimuovere l'account di archiviazione. Viene visualizzato il riquadro **Elimina account di archiviazione**.

   ![Rimuovere account di archiviazione](./media/iot-hub-store-data-in-azure-table-storage/cleanup-remove-storageaccount.png)

8.  Digitare il nome dell'account di archiviazione, quindi fare clic su **Elimina** nella parte inferiore del riquadro. 

## <a name="next-steps"></a>Passaggi successivi

È stato creato correttamente l'account di archiviazione di Azure ed è stato eseguito il routing dei messaggi dall'hub IoT a un contenitore BLOB in tale account di archiviazione.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
