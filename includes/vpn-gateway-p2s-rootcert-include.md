---
title: File di inclusione
description: File di inclusione
services: vpn-gateway
author: WenJason
ms.service: vpn-gateway
ms.topic: include
origin.date: 12/11/2018
ms.date: 12/24/2018
ms.author: v-jay
ms.custom: include file
ms.openlocfilehash: 4c8e7e5272f180c482ca7fdd44302f49eb888b25
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60320132"
---
Usare un certificato radice generato tramite una soluzione aziendale (opzione consigliata) oppure generare un certificato autofirmato. Dopo avere creato il certificato radice, esportare i dati del certificato pubblico, non la chiave privata, come file CER X.509 con codifica in base 64. Caricare quindi i dati del certificato pubblico nel server di Azure.

* **Certificato aziendale:** Se si tratta di una soluzione aziendale, è possibile usare la catena di certificati esistente. Acquisire il file con estensione cer per il certificato radice che si vuole usare.
* **Certificato radice autofirmato:** Se non si usa una soluzione aziendale per la creazione di certificati, creare un certificato radice autofirmato. In caso contrario, i certificati creati non saranno compatibili con le connessioni da punto a sito e i client riceveranno un errore di connessione quando provano a connettersi. È possibile usare Azure PowerShell, MakeCert oppure OpenSSL. I passaggi negli articoli seguenti descrivono come generare un certificato radice autofirmato compatibile:

  * [Istruzioni per Windows 10 PowerShell ](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md): queste istruzioni richiedono Windows 10 e PowerShell per generare i certificati. I certificati client generati dal certificato radice possono essere installati in qualsiasi client da punto a sito supportato.
  * [Istruzioni per MakeCert](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site-makecert.md): se non si ha accesso a un computer con Windows 10, è possibile usare MakeCert per generare i certificati. Sebbene MakeCert sia deprecato, è comunque possibile usarlo per generare i certificati. I certificati client generati dal certificato radice possono essere installati in qualsiasi client da punto a sito supportato.
  * [Istruzioni per Linux](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site-linux.md)
