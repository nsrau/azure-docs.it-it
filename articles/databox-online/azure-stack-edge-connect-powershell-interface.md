---
title: Connettersi e gestire il dispositivo Microsoft Azure Stack Edge Pro tramite l'interfaccia di Windows PowerShell | Microsoft Docs
description: Viene descritto come connettersi e gestire Azure Stack Edge Pro tramite l'interfaccia di Windows PowerShell.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 09/30/2020
ms.author: alkohli
ms.openlocfilehash: 93678735237c25b19d04b7d901583ba785d7f594
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91613544"
---
# <a name="manage-an-azure-stack-edge-pro-fpga-device-via-windows-powershell"></a>Gestire un dispositivo FPGA Pro Azure Stack Edge tramite Windows PowerShell

Azure Stack soluzione Pro Edge consente di elaborare i dati e inviarli tramite la rete ad Azure. Questo articolo descrive alcune delle attività di configurazione e gestione per il dispositivo Azure Stack Edge Pro. È possibile usare la portale di Azure, l'interfaccia utente Web locale o l'interfaccia di Windows PowerShell per gestire il dispositivo.

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

È anche possibile caricare certificati IoT Edge per abilitare una connessione sicura tra il dispositivo IoT Edge e i dispositivi downstream che possono connettersi a esso. Sono disponibili tre file (formato*PEM* ) che è necessario installare:

- Certificato CA radice o CA proprietario
- Certificato CA del dispositivo
- Chiave privata del dispositivo 

Nell'esempio seguente viene illustrato l'utilizzo di questo cmdlet per installare i certificati IoT Edge:

```
Set-HcsCertificate -Scope IotEdge -RootCACertificateFilePath "\\hcfs\root-ca-cert.pem" -DeviceCertificateFilePath "\\hcfs\device-ca-cert.pem\" -DeviceKeyFilePath "\\hcfs\device-private-key.pem" -Credential "username"
```
Quando si esegue questo cmdlet, verrà richiesto di specificare la password per la condivisione di rete.

Per altre informazioni sui certificati, vedere [Azure IOT Edge Certificates](https://docs.microsoft.com/azure/iot-edge/iot-edge-certs) o [Install certificates on a gateway](https://docs.microsoft.com/azure/iot-edge/how-to-create-transparent-gateway).

## <a name="view-device-information"></a>Visualizza informazioni sul dispositivo
 
[!INCLUDE [View device information](../../includes/data-box-edge-gateway-view-device-info.md)]

## <a name="reset-your-device"></a>Reset your device (Reimpostare il dispositivo)

[!INCLUDE [Reset your device](../../includes/data-box-edge-gateway-deactivate-device.md)]

## <a name="get-compute-logs"></a>Ottenere i log di calcolo

Se il ruolo di calcolo è configurato nel dispositivo, è anche possibile ottenere i log di calcolo tramite l'interfaccia di PowerShell.

1. [Connettersi all'interfaccia di PowerShell](#connect-to-the-powershell-interface).
2. Usare `Get-AzureDataBoxEdgeComputeRoleLogs` per ottenere i log di calcolo per il dispositivo.

    Nell'esempio seguente viene illustrato l'utilizzo di questo cmdlet:

    ```powershell
    Get-AzureDataBoxEdgeComputeRoleLogs -Path "\\hcsfs\logs\myacct" -Credential "username" -FullLogCollection
    ```

    Ecco una descrizione dei parametri usati per il cmdlet:
    - `Path`: Specificare un percorso di rete per la condivisione in cui si vuole creare il pacchetto di log di calcolo.
    - `Credential`: Specificare il nome utente per la condivisione di rete. Quando si esegue questo cmdlet, sarà necessario specificare la password di condivisione.
    - `FullLogCollection`: Questo parametro garantisce che il pacchetto di log conterrà tutti i log di calcolo. Per impostazione predefinita, il pacchetto di log contiene solo un subset di log.

## <a name="monitor-and-troubleshoot-compute-modules"></a>Monitorare e risolvere i problemi dei moduli di calcolo

[!INCLUDE [Monitor and troubleshoot compute modules](../../includes/azure-stack-edge-monitor-troubleshoot-compute.md)]

## <a name="exit-the-remote-session"></a>Uscire dalla sessione remota

Per uscire dalla sessione remota di PowerShell, chiudere la finestra di PowerShell.

## <a name="next-steps"></a>Passaggi successivi

- Distribuire [Azure Stack Edge Pro](azure-stack-edge-deploy-prep.md) nel portale di Azure.
