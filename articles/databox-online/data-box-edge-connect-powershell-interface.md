---
title: Connettersi e gestire il dispositivo Microsoft Azure Data Box Edge tramite l'interfaccia di Windows PowerShell Documenti Microsoft
description: Viene descritto come connettersi e quindi gestire Data Box Edge tramite l'interfaccia di Windows PowerShell.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 06/25/2019
ms.author: alkohli
ms.openlocfilehash: f49396331a31f7ca9eaf453dc8bf6880da2e0da8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79265480"
---
# <a name="manage-an-azure-data-box-edge-device-via-windows-powershell"></a>Gestire un dispositivo Azure Data Box Edge tramite Windows PowerShellManage an Azure Data Box Edge device via Windows PowerShell

Azure Data Box Edge solution lets you process data and send it over the network to Azure. In questo articolo vengono descritte alcune delle attività di configurazione e gestione per il dispositivo Data Box Edge. È possibile usare il portale di Azure, l'interfaccia utente Web locale o l'interfaccia di Windows PowerShell per gestire il dispositivo.

Questo articolo è incentrato sulle attività eseguite tramite l'interfaccia di PowerShell.This article focuses on the tasks you do using the PowerShell interface.

In questo articolo sono incluse le seguenti procedure:

- Connettersi all'interfaccia di PowerShellConnect to the PowerShell interface
- Creare un pacchetto di supporto
- Caricamento del certificato
- Reimpostare il dispositivo
- Visualizzare le informazioni sul dispositivo
- Ottenere i log di calcoloGet compute logs
- Monitorare e risolvere i problemi relativi ai moduli di calcolo

## <a name="connect-to-the-powershell-interface"></a>Connettersi all'interfaccia di PowerShellConnect to the PowerShell interface

[!INCLUDE [Connect to admin runspace](../../includes/data-box-edge-gateway-connect-minishell.md)]

## <a name="create-a-support-package"></a>Creare un pacchetto di supporto

[!INCLUDE [Create a support package](../../includes/data-box-edge-gateway-create-support-package.md)]

## <a name="upload-certificate"></a>Caricamento del certificato

[!INCLUDE [Upload certificate](../../includes/data-box-edge-gateway-upload-certificate.md)]

Puoi anche caricare i certificati IoT Edge per abilitare una connessione sicura tra il dispositivo IoT Edge e i dispositivi downstream che potrebbero connettersi ad esso. Sono disponibili tre certificati IoT Edge (formato *.pem)* che è necessario installare:

- Certificato CA radice o CA proprietario
- Certificato CA del dispositivo
- Certificato chiave dispositivo

Nell'esempio seguente viene illustrato l'utilizzo di questo cmdlet per installare i certificati IoT Edge:

```
Set-HcsCertificate -Scope IotEdge -RootCACertificateFilePath "\\hcfs\root-ca-cert.pem" -DeviceCertificateFilePath "\\hcfs\device-ca-cert.pem\" -DeviceKeyFilePath "\\hcfs\device-key-cert.pem" -Credential "username"
```
Quando si esegue questo cmdlet, verrà richiesto di fornire la password per la condivisione di rete.

Per altre informazioni sui certificati, vedere [Certificati di Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/iot-edge-certs) o Installare certificati in un [gateway.](https://docs.microsoft.com/azure/iot-edge/how-to-create-transparent-gateway)

## <a name="view-device-information"></a>Visualizzare le informazioni sul dispositivo
 
[!INCLUDE [View device information](../../includes/data-box-edge-gateway-view-device-info.md)]

## <a name="reset-your-device"></a>Reset your device (Reimpostare il dispositivo)

[!INCLUDE [Reset your device](../../includes/data-box-edge-gateway-deactivate-device.md)]

## <a name="get-compute-logs"></a>Ottenere i log di calcoloGet compute logs

Se il ruolo di calcolo è configurato nel dispositivo, è anche possibile ottenere i log di calcolo tramite l'interfaccia di PowerShell.If the compute role is configured on your device, you can also get the compute logs via the PowerShell interface.

1. [Connettersi all'interfaccia](#connect-to-the-powershell-interface)di PowerShell .
2. Usare `Get-AzureDataBoxEdgeComputeRoleLogs` il per ottenere i log di calcolo per il dispositivo.

    Nell'esempio seguente viene illustrato l'utilizzo di questo cmdlet:

    ```powershell
    Get-AzureDataBoxEdgeComputeRoleLogs -Path "\\hcsfs\logs\myacct" -Credential "username" -FullLogCollection
    ```

    Di seguito è riportata una descrizione dei parametri utilizzati per il cmdlet:
    - `Path`: fornire un percorso di rete alla condivisione in cui si desidera creare il pacchetto del log di calcolo.
    - `Credential`: fornire il nome utente per la condivisione di rete. Quando si esegue questo cmdlet, è necessario fornire la password di condivisione.
    - `FullLogCollection`: questo parametro garantisce che il pacchetto di log conterrà tutti i log di calcolo. Per impostazione predefinita, il pacchetto di log contiene solo un sottoinsieme di log.

## <a name="monitor-and-troubleshoot-compute-modules"></a>Monitorare e risolvere i problemi relativi ai moduli di calcolo

[!INCLUDE [Monitor and troubleshoot compute modules](../../includes/data-box-edge-monitor-troubleshoot-compute.md)]

## <a name="exit-the-remote-session"></a>Uscire dalla sessione remota

Per uscire dalla sessione remota di PowerShell, chiudere la finestra di PowerShell.To exit the remote PowerShell session, close the PowerShell window.

## <a name="next-steps"></a>Passaggi successivi

- Distribuire rapidamente [Azure Data Box Edge](data-box-edge-deploy-prep.md) nel portale di Azure.
