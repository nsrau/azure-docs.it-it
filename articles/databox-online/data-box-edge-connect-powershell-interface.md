---
title: Connettersi e gestire il dispositivo perimetrale casella dei dati di Microsoft Azure tramite l'interfaccia Windows PowerShell | Microsoft Docs
description: Viene descritto come connettersi e quindi gestire bordo casella dei dati tramite l'interfaccia Windows PowerShell.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 03/29/2019
ms.author: alkohli
ms.openlocfilehash: a3096729b2430adf0fd884fc03e3b051b17f5b51
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2019
ms.locfileid: "58660461"
---
# <a name="manage-an-azure-data-box-edge-device-via-windows-powershell"></a>Gestire un dispositivo perimetrale di casella dei dati di Azure tramite Windows PowerShell

La soluzione Azure bordo casella dei dati consente di elaborare i dati e inviarli in rete in Azure. Questo articolo descrive alcune delle attività di configurazione e gestione per dispositivo Edge casella dei dati. È possibile usare il portale di Azure, interfaccia utente web locale o l'interfaccia di Windows PowerShell per gestire il dispositivo.

Questo articolo è incentrato sulle attività eseguite tramite l'interfaccia di PowerShell.

Questo articolo include le seguenti procedure:

- Connettersi all'interfaccia di PowerShell
- Avviare una sessione di supporto
- Creare un pacchetto di supporto
- Caricamento del certificato
- Reimposta il dispositivo
- Informazioni sul dispositivo di visualizzazione
- Ottenere i log di calcolo
- Monitorare e risolvere i moduli di calcolo

## <a name="connect-to-the-powershell-interface"></a>Connettersi all'interfaccia di PowerShell

[!INCLUDE [Connect to admin runspace](../../includes/data-box-edge-gateway-connect-minishell.md)]

## <a name="create-a-support-package"></a>Creare un pacchetto di supporto

[!INCLUDE [Create a support package](../../includes/data-box-edge-gateway-create-support-package.md)]

## <a name="upload-certificate"></a>Caricamento del certificato

[!INCLUDE [Upload certificate](../../includes/data-box-edge-gateway-upload-certificate.md)]

È anche possibile caricare i certificati di IoT Edge per abilitare una connessione sicura tra il dispositivo IoT Edge e i dispositivi downstream che si connetta a esso. Esistono tre certificati di IoT Edge (*PEM* formato) che è necessario installare:

- Certificato CA radice o il proprietario della CA
- Certificato CA del dispositivo
- Certificato di chiave dispositivo

Nell'esempio seguente viene illustrato l'utilizzo di questo cmdlet per installare i certificati di IoT Edge:

```
Set-HcsCertificate -Scope IotEdge -RootCACertificateFilePath "\\hcfs\root-ca-cert.pem" -DeviceCertificateFilePath "\\hcfs\device-ca-cert.pem\" -DeviceKeyFilePath "\\hcfs\device-key-cert.pem" -Credential "username/password"
```

Per altre informazioni sui certificati, vedere [certificati Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/iot-edge-certs) oppure [installare certificati in un gateway](https://docs.microsoft.com/azure/iot-edge/how-to-create-transparent-gateway#install-certificates-on-the-gateway).

## <a name="view-device-information"></a>Informazioni sul dispositivo di visualizzazione
 
[!INCLUDE [View device information](../../includes/data-box-edge-gateway-view-device-info.md)]

## <a name="reset-your-device"></a>Reimposta il dispositivo

[!INCLUDE [Reset your device](../../includes/data-box-edge-gateway-deactivate-device.md)]

## <a name="get-compute-logs"></a>Ottenere i log di calcolo

Se il ruolo di calcolo viene configurato nel dispositivo, è anche possibile ottenere i log di calcolo tramite l'interfaccia PowerShell.

1. [Connettersi all'interfaccia di PowerShell](#connect-to-the-powershell-interface).
2. Usare il `Get-AzureDataBoxEdgeComputeRoleLogs` per ottenere i log di calcolo per il dispositivo.

    Nell'esempio seguente viene illustrato l'utilizzo di questo cmdlet:

    ```
    Get-AzureDataBoxEdgeComputeRoleLogs -Path "\\hcsfs\logs\myacct" -Credential "username/password" -RoleInstanceName "IotRole" -FullLogCollection
    ```
    Ecco una descrizione dei parametri utilizzati per il cmdlet:
    - `Path`: Specificare un percorso di rete per la condivisione in cui si desidera creare il pacchetto di log di calcolo.
    - `Credential`: Fornire il nome utente e la password per la condivisione di rete.
    - `RoleInstanceName`: Specificare questa stringa `IotRole` per questo parametro.
    - `FullLogCollection`: Questo parametro assicura che il pacchetto di log conterrà tutti i log di calcolo. Per impostazione predefinita, il pacchetto di log contiene solo un subset dei log.

## <a name="monitor-and-troubleshoot-compute-modules"></a>Monitorare e risolvere i moduli di calcolo

[!INCLUDE [Monitor and troubleshoot compute modules](../../includes/data-box-edge-monitor-troubleshoot-compute.md)]


## <a name="next-steps"></a>Passaggi successivi

- Distribuire rapidamente [Azure Data Box Edge](data-box-edge-deploy-prep.md) nel portale di Azure.
