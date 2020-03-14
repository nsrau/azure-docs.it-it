---
title: Connettersi e gestire Microsoft Azure dispositivo Data Box Edge tramite l'interfaccia di Windows PowerShell | Microsoft Docs
description: Viene descritto come connettersi e gestire Data Box Edge tramite l'interfaccia di Windows PowerShell.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 06/25/2019
ms.author: alkohli
ms.openlocfilehash: f49396331a31f7ca9eaf453dc8bf6880da2e0da8
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79265480"
---
# <a name="manage-an-azure-data-box-edge-device-via-windows-powershell"></a>Gestire un dispositivo Azure Data Box Edge tramite Windows PowerShell

Azure Data Box Edge soluzione consente di elaborare i dati e inviarli tramite la rete ad Azure. Questo articolo descrive alcune delle attività di configurazione e gestione per il dispositivo Data Box Edge. È possibile usare la portale di Azure, l'interfaccia utente Web locale o l'interfaccia di Windows PowerShell per gestire il dispositivo.

Questo articolo è incentrato sulle attività da eseguire usando l'interfaccia di PowerShell.

Questo articolo include le procedure seguenti:

- Connettersi all'interfaccia di PowerShell
- Creare un pacchetto di supporto
- Caricamento del certificato
- Reimpostare il dispositivo
- Visualizza informazioni sul dispositivo
- Ottenere i log di calcolo
- Monitorare e risolvere i problemi dei moduli di calcolo

## <a name="connect-to-the-powershell-interface"></a>Connettersi all'interfaccia di PowerShell

[!INCLUDE [Connect to admin runspace](../../includes/data-box-edge-gateway-connect-minishell.md)]

## <a name="create-a-support-package"></a>Creare un pacchetto di supporto

[!INCLUDE [Create a support package](../../includes/data-box-edge-gateway-create-support-package.md)]

## <a name="upload-certificate"></a>Caricamento del certificato

[!INCLUDE [Upload certificate](../../includes/data-box-edge-gateway-upload-certificate.md)]

È anche possibile caricare IoT Edge certificati per abilitare una connessione sicura tra il dispositivo IoT Edge e i dispositivi downstream che possono connettersi ad esso. Sono disponibili tre certificati IoT Edge (formato*PEM* ) che è necessario installare:

- Certificato CA radice o CA proprietario
- Certificato CA del dispositivo
- Certificato chiave del dispositivo

Nell'esempio seguente viene illustrato l'utilizzo di questo cmdlet per installare i certificati IoT Edge:

```
Set-HcsCertificate -Scope IotEdge -RootCACertificateFilePath "\\hcfs\root-ca-cert.pem" -DeviceCertificateFilePath "\\hcfs\device-ca-cert.pem\" -DeviceKeyFilePath "\\hcfs\device-key-cert.pem" -Credential "username"
```
Quando si esegue questo cmdlet, verrà richiesto di specificare la password per la condivisione di rete.

Per altre informazioni sui certificati, vedere [Azure IOT Edge Certificates](https://docs.microsoft.com/azure/iot-edge/iot-edge-certs) o [Install certificates on a gateway](https://docs.microsoft.com/azure/iot-edge/how-to-create-transparent-gateway).

## <a name="view-device-information"></a>Visualizza informazioni sul dispositivo
 
[!INCLUDE [View device information](../../includes/data-box-edge-gateway-view-device-info.md)]

## <a name="reset-your-device"></a>Ripristinare il dispositivo

[!INCLUDE [Reset your device](../../includes/data-box-edge-gateway-deactivate-device.md)]

## <a name="get-compute-logs"></a>Ottenere i log di calcolo

Se il ruolo di calcolo è configurato nel dispositivo, è anche possibile ottenere i log di calcolo tramite l'interfaccia di PowerShell.

1. [Connettersi all'interfaccia di PowerShell](#connect-to-the-powershell-interface).
2. Usare il `Get-AzureDataBoxEdgeComputeRoleLogs` per ottenere i log di calcolo per il dispositivo.

    Nell'esempio seguente viene illustrato l'utilizzo di questo cmdlet:

    ```powershell
    Get-AzureDataBoxEdgeComputeRoleLogs -Path "\\hcsfs\logs\myacct" -Credential "username" -FullLogCollection
    ```

    Ecco una descrizione dei parametri usati per il cmdlet:
    - `Path`: specificare un percorso di rete per la condivisione in cui si vuole creare il pacchetto di log di calcolo.
    - `Credential`: specificare il nome utente per la condivisione di rete. Quando si esegue questo cmdlet, sarà necessario specificare la password di condivisione.
    - `FullLogCollection`: questo parametro garantisce che il pacchetto di log conterrà tutti i log di calcolo. Per impostazione predefinita, il pacchetto di log contiene solo un subset di log.

## <a name="monitor-and-troubleshoot-compute-modules"></a>Monitorare e risolvere i problemi dei moduli di calcolo

[!INCLUDE [Monitor and troubleshoot compute modules](../../includes/data-box-edge-monitor-troubleshoot-compute.md)]

## <a name="exit-the-remote-session"></a>Uscire dalla sessione remota

Per uscire dalla sessione remota di PowerShell, chiudere la finestra di PowerShell.

## <a name="next-steps"></a>Passaggi successivi

- Distribuire rapidamente [Azure Data Box Edge](data-box-edge-deploy-prep.md) nel portale di Azure.
