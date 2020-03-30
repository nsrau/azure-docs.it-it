---
title: Generazione ed esportazione di certificati per le connessioni VPN utente WAN virtuale di AzureAzure Virtual WAN
description: Creare un certificato radice autofirmato, esportare la chiave pubblica e generare certificati client con PowerShell in Windows 10 o Windows Server 2016.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 03/18/2020
ms.author: cherylmc
ms.openlocfilehash: 0303bac88f34c895a4a680cd5bff0e9d1513d2e5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80059944"
---
# <a name="generate-and-export-certificates-for-user-vpn-connections"></a>Generare ed esportare certificati per le connessioni VPN utente

Le connessioni VPN utente (da punto a sito) utilizzano i certificati per l'autenticazione. Questo articolo illustra come creare un certificato radice autofirmato e generare i certificati client usando PowerShell in Windows 10 o Windows Server 2016.

È necessario eseguire i passaggi descritti in questo articolo in un computer che esegue Windows 10 o Windows Server 2016. I cmdlet di PowerShell usati per generare i certificati fanno parte del sistema operativo e non funzionano in altre versioni di Windows. Il computer con Windows 10 o Windows Server 2016 è richiesto solo per generare i certificati. Dopo aver generato i certificati, è possibile caricarli o installarli in qualsiasi sistema operativo client supportato.

[!INCLUDE [Export public key](../../includes/vpn-gateway-generate-export-certificates-include.md)]

## <a name="next-steps"></a>Passaggi successivi

Continuare con i passaggi della rete WAN virtuale per la [connessione VPN dell'utente](virtual-wan-about.md)
