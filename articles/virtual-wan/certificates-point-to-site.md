---
title: Generare ed esportare i certificati per le connessioni VPN utente della rete WAN virtuale di Azure | Microsoft Docs
description: Creare un certificato radice autofirmato, esportare la chiave pubblica e generare certificati client con PowerShell in Windows 10 o Windows Server 2016.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 10/09/2019
ms.author: cherylmc
ms.openlocfilehash: 57d730a92c687a297a35b8cd6cccd955025694af
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73514914"
---
# <a name="generate-and-export-certificates-for-virtual-wan-user-vpn-connections"></a>Generare ed esportare i certificati per le connessioni VPN utente WAN virtuale

Le connessioni VPN utente usano i certificati per l'autenticazione. Questo articolo illustra come creare un certificato radice autofirmato e generare i certificati client usando PowerShell in Windows 10 o Windows Server 2016.

È necessario eseguire i passaggi descritti in questo articolo in un computer che esegue Windows 10 o Windows Server 2016. I cmdlet di PowerShell usati per generare i certificati fanno parte del sistema operativo e non funzionano in altre versioni di Windows. Il computer con Windows 10 o Windows Server 2016 è richiesto solo per generare i certificati. Dopo aver generato i certificati, è possibile caricarli o installarli in qualsiasi sistema operativo client supportato.

[!INCLUDE [Export public key](../../includes/vpn-gateway-generate-export-certificates-include.md)]

## <a name="next-steps"></a>Passaggi successivi

Continua con i [passaggi della rete WAN virtuale per la connessione VPN utente](virtual-wan-about.md)
