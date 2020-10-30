---
title: includere file
description: includere file
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/28/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 59d18c89a8f66f781f01d1dace9d8968df17a626
ms.sourcegitcommit: 4f4a2b16ff3a76e5d39e3fcf295bca19cff43540
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93041547"
---
Ottenere il file con estensione cer per il certificato radice. È possibile usare un certificato radice generato tramite una soluzione aziendale (scelta consigliata) oppure generare un certificato autofirmato. Dopo avere creato il certificato radice, esportare i dati del certificato pubblico, non la chiave privata, come file CER X.509 con codifica in base 64. Il file viene caricato in un secondo momento in Azure.

* **Certificato aziendale:** Se si tratta di una soluzione aziendale, è possibile usare la catena di certificati esistente. Acquisire il file con estensione cer per il certificato radice che si vuole usare.
* **Certificato radice autofirmato:** Se non si usa una soluzione aziendale per la creazione di certificati, creare un certificato radice autofirmato. In caso contrario, i certificati creati non saranno compatibili con le connessioni da punto a sito e i client riceveranno un errore di connessione quando provano a connettersi. È possibile usare Azure PowerShell, MakeCert oppure OpenSSL. I passaggi negli articoli seguenti descrivono come generare un certificato radice autofirmato compatibile:

  * [Istruzioni per Windows 10 PowerShell](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md): queste istruzioni richiedono Windows 10 e PowerShell per generare i certificati. I certificati client generati dal certificato radice possono essere installati in qualsiasi client da punto a sito supportato.
  * [Istruzioni per MakeCert](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site-makecert.md): se non si ha accesso a un computer con Windows 10, è possibile usare MakeCert per generare i certificati. Sebbene MakeCert sia deprecato, è comunque possibile usarlo per generare i certificati. I certificati client generati dal certificato radice possono essere installati in qualsiasi client da punto a sito supportato.
  * [Istruzioni per Linux](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site-linux.md).