---
title: Generare ed esportare i certificati per le connessioni VPN utente | Rete WAN virtuale di Azure
description: Creare un certificato radice autofirmato, esportare la chiave pubblica e generare certificati client per le connessioni VPN utente usando PowerShell in Windows 10 o Windows Server 2016.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 09/22/2020
ms.author: cherylmc
ms.openlocfilehash: 2205f170ee846d4db94db7f524a1c424cfbc8f7b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91328039"
---
# <a name="generate-and-export-certificates-for-user-vpn-connections"></a>Generare ed esportare i certificati per le connessioni VPN utente

Le connessioni VPN utente (da punto a sito) usano i certificati per l'autenticazione. Questo articolo illustra come creare un certificato radice autofirmato e generare i certificati client usando PowerShell in Windows 10 o Windows Server 2016.

È necessario eseguire i passaggi descritti in questo articolo in un computer che esegue Windows 10 o Windows Server 2016. I cmdlet di PowerShell usati per generare i certificati fanno parte del sistema operativo e non funzionano in altre versioni di Windows. Il computer con Windows 10 o Windows Server 2016 è richiesto solo per generare i certificati. Dopo aver generato i certificati, è possibile caricarli o installarli in qualsiasi sistema operativo client supportato.

[!INCLUDE [Export public key](../../includes/vpn-gateway-generate-export-certificates-include.md)]

## <a name="next-steps"></a>Passaggi successivi

Continua con i [passaggi della rete WAN virtuale per la connessione VPN utente](virtual-wan-about.md)
