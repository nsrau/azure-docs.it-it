---
title: Caricare file da dispositivi ad archiviazione di Azure | Microsoft Docs
description: Come configurare i caricamenti di file dai dispositivi nel cloud. Dopo aver configurato i caricamenti dei file, implementare i caricamenti dei file nei dispositivi.
services: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 08/06/2020
ms.topic: how-to
ms.service: iot-central
ms.openlocfilehash: 6b717fd15b25ae4abd2af3520dba2e72f8f9f3a4
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/18/2020
ms.locfileid: "88556296"
---
# <a name="upload-files-from-your-devices-to-the-cloud"></a>Caricare i file dai dispositivi nel cloud

*Questo argomento si applica agli amministratori e agli sviluppatori di dispositivi.*

IoT Central consente di caricare file multimediali e altri file da dispositivi connessi all'archiviazione cloud. Configurare la funzionalità di caricamento dei file nell'applicazione IoT Central e quindi implementare i caricamenti di file nel codice del dispositivo.

## <a name="prerequisites"></a>Prerequisiti

Per configurare i caricamenti dei file, è necessario essere un amministratore dell'applicazione IoT Central.

Per archiviare i file caricati sono necessari un account e un contenitore di archiviazione di Azure. Se non si dispone di un account di archiviazione e di un contenitore da usare, creare un [nuovo account di archiviazione nel portale di Azure](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM).

## <a name="configure-device-file-uploads"></a>Configurare i caricamenti dei file del dispositivo

Per configurare i caricamenti dei file del dispositivo:

1. Passare alla sezione **Amministrazione** nell'applicazione.

1. Selezionare **caricamento file del dispositivo**.

1. Selezionare l'account di archiviazione e il contenitore da usare. Se l'account di archiviazione si trova in una sottoscrizione di Azure diversa dall'applicazione, immettere una stringa di connessione dell'account di archiviazione.

1. Se necessario, modificare il timeout di caricamento per impostare il tempo di validità di una richiesta di caricamento per. I valori validi sono compresi tra 1 e 24 ore.

1. Selezionare **Salva**. Quando lo stato è **configurato**, si è pronti per caricare i file dai dispositivi.

:::image type="content" source="media/howto-configure-file-uploads/file-upload-configuration.png" alt-text="Configurare il caricamento di file nell'applicazione":::

## <a name="disable-device-file-uploads"></a>Disabilitare i caricamenti dei file del dispositivo

Se si desidera disabilitare i caricamenti dei file del dispositivo nell'applicazione IoT Central:

1. Passare alla sezione **Amministrazione** nell'applicazione.

1. Selezionare **caricamento file del dispositivo**.

1. Selezionare **Elimina**.

## <a name="upload-a-file-from-a-device"></a>Caricare un file da un dispositivo

IoT Central USA la funzionalità di caricamento dei file dell'hub Internet per consentire ai dispositivi di caricare i file. Per il codice di esempio in cui viene illustrato come caricare file da un dispositivo, vedere l' [esempio IOT Central file upload Device](https://docs.microsoft.com/samples/iot-for-all/iotc-file-upload-device/iotc-file-upload-device/).

## <a name="next-steps"></a>Passaggi successivi

Ora che si è appreso come configurare e implementare i caricamenti dei file del dispositivo in IoT Central, un passaggio successivo suggerito consiste nell'acquisire ulteriori caricamenti dei file del dispositivo:

- [Caricare file da un dispositivo al cloud con l'hub IoT (.NET)](../../iot-hub/iot-hub-csharp-csharp-file-upload.md)
- [Caricare file da un dispositivo al cloud con l'hub IoT (Java)](../../iot-hub/iot-hub-java-java-file-upload.md)
- [Caricare file da un dispositivo al cloud con l'hub IoT (Node.js)](../../iot-hub/iot-hub-node-node-file-upload.md)
- [Caricare i file dal dispositivo al cloud con l'hub Internet (Python)](../../iot-hub/iot-hub-python-python-file-upload.md)
