---
title: File di inclusione
description: File di inclusione
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 09/06/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: afee9450bc1485f781eb0d70b5d4dd2f50424573
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/10/2018
ms.locfileid: "44343145"
---
È possibile usare un certificato radice generato tramite una soluzione aziendale (opzione consigliata) oppure generare un certificato autofirmato. Dopo avere creato il certificato radice, esportare i dati del certificato pubblico (non la chiave privata) come file CER X.509 con codifica Base 64 e caricarli in Azure.

* **Certificato aziendale:** se si tratta di una soluzione aziendale, è possibile usare la catena di certificati esistente. Ottenere il file con estensione cer per il certificato radice che si vuole usare.
* **Certificato radice autofirmato:** se non si usa una soluzione aziendale per la creazione di certificati, è necessario creare un certificato radice autofirmato. È importante seguire i passaggi di uno dei due articoli seguenti relativi ai certificati da punto a sito (P2S). In caso contrario, i certificati creati non saranno compatibili con le connessioni da punto a sito e i client riceveranno un errore di connessione quando provano a connettersi. È possibile usare Azure PowerShell, MakeCert oppure OpenSSL. I passaggi negli articoli indicati generano un certificato compatibile.

  * [Istruzioni per Windows 10 PowerShell](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md): queste istruzioni richiedono Windows 10 e PowerShell per generare i certificati. I certificati client generati dal certificato radice possono essere installati in qualsiasi client da punto a sito supportato.
  * [Istruzioni per MakeCert](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site-makecert.md): se non si ha accesso a un computer con Windows 10, è possibile usare MakeCert per generare i certificati. MakeCert è deprecato, ma è comunque possibile usarlo per generare i certificati. I certificati client generati dal certificato radice possono essere installati in qualsiasi client da punto a sito supportato.
  * [Istruzioni per Linux](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site-makecert.md)
