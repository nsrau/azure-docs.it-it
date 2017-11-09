---
title: Salvare i messaggi dell'hub IoT nell'archivio dati di Azure | Microsoft Docs
description: Usare il routing dei messaggi dell'hub IoT per salvare i messaggi dell'hub IoT nel servizio Archiviazione BLOB di Azure. I messaggi dell'hub IoT contengono informazioni, come i dati di sensori, inviate dal dispositivo IoT.
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: archiviazione dei dati IoT, archiviazione dei dati di sensori IoT
ms.assetid: 62fd14fd-aaaa-4b3d-8367-75c1111b6269
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/04/2017
ms.author: xshi
ms.openlocfilehash: aa33800de82b27d4819fe0eade127c2a40e3a493
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="save-iot-hub-messages-that-contain-sensor-data-to-your-azure-blob-storage"></a>Salvare i messaggi dell'hub IoT che contengono dati di sensori nel servizio Archiviazione BLOB di Azure

![Diagramma end-to-end](media/iot-hub-store-data-in-azure-table-storage/1_route-to-storage.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

## <a name="what-you-learn"></a>Contenuto dell'esercitazione

Si apprenderà come creare un account di archiviazione di Azure e un'app per le funzioni di Azure per archiviare i messaggi dell'hub IoT nell'archiviazione tabelle di Azure.

## <a name="what-you-do"></a>Operazioni da fare

- Creare un account di archiviazione di Azure
- Preparare l'hub IoT per il routing dei messaggi nel servizio di archiviazione.

## <a name="what-you-need"></a>Elementi necessari

- [Configurare il dispositivo](iot-hub-raspberry-pi-kit-node-get-started.md) in modo da soddisfare i requisiti seguenti:
  - Una sottoscrizione di Azure attiva
  - Un hub IoT nella sottoscrizione 
  - Un'applicazione in esecuzione che invia messaggi all'hub IoT di Azure

## <a name="create-an-azure-storage-account"></a>Creare un account di archiviazione di Azure

1. Nel [portale di Azure](https://portal.azure.com/) fare clic su **Nuova** > **Archiviazione** > **Account di archiviazione** > **Crea**.

2. Immettere le informazioni necessarie per l'account di archiviazione:

   ![Creare un account di archiviazione nel portale di Azure](media\iot-hub-store-data-in-azure-table-storage\1_azure-portal-create-storage-account.png)

   * **Nome**: nome dell'account di archiviazione. Il nome deve essere univoco a livello globale.

   * **Gruppo di risorse**: usare lo stesso gruppo di risorse usato da hub IoT.

   * **Aggiungi al dashboard**: selezionare questa opzione per semplificare l'accesso all'hub IoT dal dashboard.

3. Fare clic su **Crea**.

## <a name="prepare-your-iot-hub-to-route-messages-to-storage"></a>Preparare l'hub IoT per il routing dei messaggi nel servizio di archiviazione

L'hub IoT supporta a livello nativo il routing dei messaggi nel servizio di archiviazione di Azure sotto forma di BLOB.

### <a name="add-storage-as-a-custom-endpoint"></a>Aggiungere risorse di archiviazione come endpoint personalizzato

Passare all'hub IoT nel portale di Azure. Fare clic su **Endpoint** > **Aggiungi**. Assegnare un nome all'endpoint e selezionare **Contenitore di archiviazione di Azure** come tipo di endpoint. Utilizzare la selezione per selezionare l'account di archiviazione creato nella sezione precedente. Creare un contenitore di archiviazione, selezionarlo e quindi fare clic su **OK**.

  ![Creare un endpoint personalizzato nell'hub IoT](media\iot-hub-store-data-in-azure-table-storage\2_custom-storage-endpoint.png)

### <a name="add-a-route-to-route-data-to-storage"></a>Aggiungere una route per eseguire il routing dei dati alla risorsa di archiviazione

Fare clic su **Route** > **Aggiungi** e quindi immettere il nome della route. Selezionare **Messaggi del dispositivo** come origine dati e selezionare l'endpoint della risorsa di archiviazione creata come endpoint nella route. Immettere `true` come stringa di query e quindi fare clic su **Salva**.

  ![Creare una route nell'hub IoT](media\iot-hub-store-data-in-azure-table-storage\3_create-route.png)
  
### <a name="add-a-route-for-hot-path-telemetry-optional"></a>Aggiungere una route per i dati di telemetria con percorso prioritario (facoltativo)

Per impostazione predefinita, l'hub IoT esegue il routing all'endpoint predefinito di tutti i messaggi che non corrispondono ad altre route. Poiché tutti i messaggi relativi ai dati di telemetria ora corrispondono alla regola che esegue il routing dei messaggi alla risorsa di archiviazione, è necessario aggiungere un'altra route per i messaggi da scrivere nell'endpoint predefinito. Non è previsto alcun costo aggiuntivo per il routing dei messaggi a più endpoint.

> [!NOTE]
> Se non vengono eseguite altre operazioni di elaborazione dei messaggi relativi ai dati di telemetria, è possibile ignorare questo passaggio.

Fare clic su **Aggiungi** nel riquadro Route e immettere un nome per la route. Selezionare **Messaggi del dispositivo** come origine dei dati ed **venti** come endpoint. Immettere `true` come stringa di query e quindi fare clic su **Salva**.

  ![Creare una route con percorso prioritario nell'hub IoT](media\iot-hub-store-data-in-azure-table-storage\4_hot-path-route.png)

## <a name="verify-your-message-in-your-storage-container"></a>Verificare il messaggio nel contenitore di archiviazione

1. Eseguire l'applicazione di esempio nel dispositivo per inviare messaggi all'hub IoT.

2. [Scaricare e installare Azure Storage Explorer](http://storageexplorer.com/).

3. Aprire Storage Explorer, fare clic su **Add an Azure Account** (Aggiungi un account Azure)  >  **Accedi** e quindi accedere all'account Azure.

4. Fare clic sulla sottoscrizione di Azure in uso > **Account di archiviazione** > account di archiviazione in uso > **contenitori BLOB** > contenitore BLOB in uso.

   Nel contenitore BLOB saranno visibili i messaggi inviati dal dispositivo all'hub IoT.

## <a name="next-steps"></a>Passaggi successivi

È stato creato correttamente l'account di archiviazione di Azure ed è stato eseguito il routing dei messaggi dall'hub IoT a un contenitore BLOB in tale account di archiviazione.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
