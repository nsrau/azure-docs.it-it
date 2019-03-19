---
title: Connettersi e gestire il dispositivo Gateway finestra dati di Microsoft Azure tramite l'interfaccia di Windows PowerShell | Microsoft Docs
description: Viene descritto come connettersi e quindi gestire Gateway finestra di dati tramite l'interfaccia Windows PowerShell.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 03/05/2019
ms.author: alkohli
ms.openlocfilehash: 0ca570235ac2a87b62c5d0fcebbd24dc5186e37d
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57556513"
---
# <a name="manage-an-azure-data-box-gateway-device-via-windows-powershell"></a>Gestire un dispositivo Gateway casella dei dati di Azure tramite Windows PowerShell

La soluzione Azure Gateway finestra dati consente di inviare i dati attraverso la rete ad Azure. Questo articolo descrive alcune delle attività di configurazione e gestione per il dispositivo Gateway casella dei dati. È possibile usare il portale di Azure, interfaccia utente web locale o l'interfaccia di Windows PowerShell per gestire il dispositivo.

Questo articolo è incentrato sulle attività eseguite tramite l'interfaccia di PowerShell.

Questo articolo include le seguenti procedure:

- Connettersi all'interfaccia di PowerShell
- Avviare una sessione di supporto
- Creare un pacchetto di supporto
- Caricamento del certificato
- Avvio nell'ambiente non DHCP
- Informazioni sul dispositivo di visualizzazione

> [!IMPORTANT]
> Gateway di finestra di dati Azure è attualmente in anteprima pubblica.
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

## <a name="boot-up-in-non-dhcp-environment"></a>Avvio nell'ambiente non DHCP

[!INCLUDE [Boot up in non-DHCP environment](../../includes/data-box-edge-gateway-boot-non-dhcp.md)]

## <a name="view-device-information"></a>Informazioni sul dispositivo di visualizzazione

[!INCLUDE [View device information](../../includes/data-box-edge-gateway-view-device-info.md)]

## <a name="next-steps"></a>Passaggi successivi

- Distribuire [Azure Data Box Gateway](data-box-gateway-deploy-prep.md) nel portale di Azure.
