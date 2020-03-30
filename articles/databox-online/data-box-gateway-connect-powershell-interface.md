---
title: Usare Windows PowerShell per connettersi e gestire il dispositivo Azure Data Box GatewayUse Windows PowerShell to connect to and manage Azure Data Box Gateway device
description: Viene descritto come connettersi e quindi gestire Gateway casella dati tramite l'interfaccia di Windows PowerShell.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 08/02/2019
ms.author: alkohli
ms.openlocfilehash: 6c9f3455a07001a8d1b9b8a1e84f2af3392b5690
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79260215"
---
# <a name="manage-an-azure-data-box-gateway-device-via-windows-powershell"></a>Gestire un dispositivo Azure Data Box Gateway tramite Windows PowerShellManage an Azure Data Box Gateway device via Windows PowerShell

Azure Data Box Gateway solution lets you send data over the network to Azure. In questo articolo vengono descritte alcune delle attività di configurazione e gestione per il dispositivo Gateway dati. È possibile usare il portale di Azure, l'interfaccia utente Web locale o l'interfaccia di Windows PowerShell per gestire il dispositivo.

Questo articolo è incentrato sulle attività eseguite tramite l'interfaccia di PowerShell.This article focuses on the tasks you do using the PowerShell interface.

In questo articolo sono incluse le seguenti procedure:

- Connettersi all'interfaccia di PowerShellConnect to the PowerShell interface
- Creare un pacchetto di supporto
- Caricamento del certificato
- Avvio in un ambiente non DHCP
- Visualizzare le informazioni sul dispositivo

## <a name="connect-to-the-powershell-interface"></a>Connettersi all'interfaccia di PowerShellConnect to the PowerShell interface

[!INCLUDE [Connect to admin runspace](../../includes/data-box-edge-gateway-connect-minishell.md)]

## <a name="create-a-support-package"></a>Creare un pacchetto di supporto

[!INCLUDE [Create a support package](../../includes/data-box-edge-gateway-create-support-package.md)]

## <a name="upload-certificate"></a>Caricamento del certificato

[!INCLUDE [Upload certificate](../../includes/data-box-edge-gateway-upload-certificate.md)]

## <a name="boot-up-in-non-dhcp-environment"></a>Avvio in un ambiente non DHCP

[!INCLUDE [Boot up in non-DHCP environment](../../includes/data-box-edge-gateway-boot-non-dhcp.md)]

## <a name="view-device-information"></a>Visualizzare le informazioni sul dispositivo

[!INCLUDE [View device information](../../includes/data-box-edge-gateway-view-device-info.md)]

## <a name="next-steps"></a>Passaggi successivi

- Distribuire [Azure Data Box Gateway](data-box-gateway-deploy-prep.md) nel portale di Azure.
