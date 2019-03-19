---
title: Connettersi e gestire il dispositivo perimetrale casella dei dati di Microsoft Azure tramite l'interfaccia Windows PowerShell | Microsoft Docs
description: Viene descritto come connettersi e quindi gestire bordo casella dei dati tramite l'interfaccia Windows PowerShell.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 03/05/2019
ms.author: alkohli
ms.openlocfilehash: b3effdbace2be582bfe85d0402088f8aa0d96fe7
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57556453"
---
# <a name="manage-an-azure-data-box-edge-device-via-windows-powershell-preview"></a>Gestire un dispositivo perimetrale di casella dei dati di Azure tramite Windows PowerShell (anteprima)

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

> [!IMPORTANT]
> Azure Edge di finestra di dati è attualmente in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate.
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="connect-to-the-powershell-interface"></a>Connettersi all'interfaccia di PowerShell

[!INCLUDE [Connect to admin runspace](../../includes/data-box-edge-gateway-connect-minishell.md)]

## <a name="start-a-support-session"></a>Avviare una sessione di supporto

[!INCLUDE [Connect to support runspace](../../includes/data-box-edge-gateway-connect-support.md)]

## <a name="create-a-support-package"></a>Creare un pacchetto di supporto

[!INCLUDE [Create a support package](../../includes/data-box-edge-gateway-create-support-package.md)]

## <a name="upload-certificate"></a>Caricamento del certificato

[!INCLUDE [Upload certificate](../../includes/data-box-edge-gateway-upload-certificate.md)]

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


## <a name="next-steps"></a>Passaggi successivi

- Distribuire rapidamente [Azure Data Box Edge](data-box-edge-deploy-prep.md) nel portale di Azure.
