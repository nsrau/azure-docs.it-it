---
title: Connettersi a una VM Windows Server | Microsoft Docs
description: Informazioni su come connettersi e accedere a una VM Windows mediante il portale di Azure e il modello di distribuzione Resource Manager.
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ef62b02e-bf35-468d-b4c3-71b63fe7f409
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: get-started-article
ms.date: 10/20/2017
ms.author: cynthn
ms.openlocfilehash: 2b748e4fc536a933dc41f9f673a534598b7343b8
ms.sourcegitcommit: 4ed3fe11c138eeed19aef0315a4f470f447eac0c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/23/2017
---
# <a name="how-to-connect-and-log-on-to-an-azure-virtual-machine-running-windows"></a>Come connettersi e accedere a una macchina virtuale di Azure che esegue Windows
Per avviare una sessione di Desktop remoto (RDP) da un desktop di Windows, sarà necessario usare il pulsante **Connetti** nel portale di Azure. Effettuare la connessione alla macchina virtuale, quindi accedere al sistema.

Se si sta tentando di effettuare la connessione a una macchina virtuale Windows da un Mac, è necessario installare un client RDP per Mac come [Desktop remoto Microsoft](https://itunes.apple.com/app/microsoft-remote-desktop/id715768417).

## <a name="connect-to-the-virtual-machine"></a>Connettersi alla macchina virtuale
1. Accedere al [portale di Azure](https://portal.azure.com/), se questa operazione non è già stata eseguita.
2. Nel menu a sinistra fare clic su **Macchine virtuali**.
3. Selezionare la macchina virtuale dall'elenco.
4. Nella pagina della macchina virtuale fare clic su **Connetti**.
   
    ![Screenshot del portale di Azure che illustra come connettersi alla VM.](./media/connect-logon/connect.png)
   
   > [!TIP]
   > Se il pulsante **Connetti** nel portale è disattivato e non si è connessi ad Azure tramite una connessione [Express Route](../../expressroute/expressroute-introduction.md) o [VPN da sito a sito](../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md), per poter usare il protocollo RDP è necessario creare un indirizzo IP pubblico e assegnarlo alla VM. Altre informazioni sono disponibili in [Indirizzi IP pubblici in Azure](../../virtual-network/virtual-network-ip-addresses-overview-arm.md).
   > 
   > 

## <a name="log-on-to-the-virtual-machine"></a>Accesso alla macchina virtuale
[!INCLUDE [virtual-machines-log-on-win-server](../../../includes/virtual-machines-log-on-win-server.md)]

## <a name="next-steps"></a>Passaggi successivi
In caso di problemi quando si cerca di eseguire la connessione, vedere [Risolvere i problemi di connessioni Desktop remoto](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). In questo articolo viene illustrato come diagnosticare e risolvere i problemi più comuni.

