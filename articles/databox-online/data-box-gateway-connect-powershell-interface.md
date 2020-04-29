---
title: Usare Windows PowerShell per connettersi e gestire Azure Data Box Gateway dispositivo
description: Viene descritto come connettersi e gestire Data Box Gateway tramite l'interfaccia di Windows PowerShell.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 08/02/2019
ms.author: alkohli
ms.openlocfilehash: 6c9f3455a07001a8d1b9b8a1e84f2af3392b5690
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79260215"
---
# <a name="manage-an-azure-data-box-gateway-device-via-windows-powershell"></a>Gestire un dispositivo Azure Data Box Gateway tramite Windows PowerShell

Azure Data Box Gateway soluzione consente di inviare dati attraverso la rete ad Azure. Questo articolo descrive alcune delle attività di configurazione e gestione per il dispositivo Data Box Gateway. È possibile usare la portale di Azure, l'interfaccia utente Web locale o l'interfaccia di Windows PowerShell per gestire il dispositivo.

Questo articolo è incentrato sulle attività da eseguire usando l'interfaccia di PowerShell.

Questo articolo include le procedure seguenti:

- Connettersi all'interfaccia di PowerShell
- Creare un pacchetto di supporto
- Caricamento del certificato
- Avvio in un ambiente non DHCP
- Visualizza informazioni sul dispositivo

## <a name="connect-to-the-powershell-interface"></a>Connettersi all'interfaccia di PowerShell

[!INCLUDE [Connect to admin runspace](../../includes/data-box-edge-gateway-connect-minishell.md)]

## <a name="create-a-support-package"></a>Creare un pacchetto di supporto

[!INCLUDE [Create a support package](../../includes/data-box-edge-gateway-create-support-package.md)]

## <a name="upload-certificate"></a>Caricamento del certificato

[!INCLUDE [Upload certificate](../../includes/data-box-edge-gateway-upload-certificate.md)]

## <a name="boot-up-in-non-dhcp-environment"></a>Avvio in un ambiente non DHCP

[!INCLUDE [Boot up in non-DHCP environment](../../includes/data-box-edge-gateway-boot-non-dhcp.md)]

## <a name="view-device-information"></a>Visualizza informazioni sul dispositivo

[!INCLUDE [View device information](../../includes/data-box-edge-gateway-view-device-info.md)]

## <a name="next-steps"></a>Passaggi successivi

- Distribuire [Azure Data Box Gateway](data-box-gateway-deploy-prep.md) nel portale di Azure.
