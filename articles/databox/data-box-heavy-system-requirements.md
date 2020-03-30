---
title: Requisiti di sistema per i dati di Microsoft Azure Data Box Heavy Documenti Microsoft
description: Informazioni sui requisiti software e di rete per Azure Data Box Heavy
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: article
ms.date: 07/03/2019
ms.author: alkohli
ms.openlocfilehash: 6097dba9193fce4c8158137b488080107881994a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79260072"
---
# <a name="azure-data-box-heavy-system-requirements"></a>Requisiti di sistema di Azure Data Box HeavyAzure Data Box Heavy system requirements

Questo articolo descrive i requisiti di sistema importanti per il dispositivo Azure Data Box Heavy e per i client che si connettono al dispositivo. È consigliabile esaminare attentamente le informazioni prima di distribuire Data Box Heavy, quindi fare riferimento ad esso in base alle esigenze durante la distribuzione e l'operazione successiva.

I requisiti di sistema includono:

* Requisiti software per gli host che **si connettono a Data Box Heavy:** descrivono le piattaforme supportate, i browser per l'interfaccia utente Web locale, i client SMB e gli eventuali requisiti aggiuntivi per gli host che possono connettersi alla casella dei dati.
* **Requisiti** di rete per Data Box Heavy - fornisce informazioni sui requisiti di rete per il funzionamento ottimale del dispositivo Data Box Heavy.

## <a name="software-requirements"></a>Requisiti software

I requisiti software includono le informazioni sui sistemi operativi supportati, sui browser supportati per l'interfaccia utente Web locale e sui client SMB.

### <a name="supported-operating-systems-for-clients"></a>Sistemi operativi supportati per i client

[!INCLUDE [data-box-supported-os-clients](../../includes/data-box-supported-os-clients.md)]

### <a name="supported-file-systems-for-linux-clients"></a>File system supportati per client Linux

[!INCLUDE [data-box-supported-file-systems-clients](../../includes/data-box-supported-file-systems-clients.md)]

### <a name="supported-storage-accounts"></a>Account di archiviazione supportati

[!INCLUDE [data-box-supported-storage-accounts](../../includes/data-box-supported-storage-accounts.md)]

### <a name="supported-storage-types"></a>Tipi di archivio supportati

[!INCLUDE [data-box-supported-storage-types](../../includes/data-box-supported-storage-types.md)]

### <a name="supported-web-browsers"></a>Web browser supportati

[!INCLUDE [data-box-supported-web-browsers](../../includes/data-box-supported-web-browsers.md)]

## <a name="networking-requirements"></a>Requisiti di rete

Il data center disponga di una rete ad alta velocità. Per velocità di copia più veloci, due connessioni da 40 GbE possono essere utilizzate in parallelo (una per nodo). Se non sono disponibili 40 GbE, è consigliabile disporre di almeno due connessioni da 10 GbE (una per nodo).

### <a name="port-requirements"></a>Requisiti delle porte

Nella tabella seguente sono elencate le porte che devono essere aperte nel firewall per consentire il traffico SMB o NFS. In questa tabella, *in* o *in ingresso* fa riferimento alla direzione da cui le richieste client in ingresso accedono al dispositivo. *In* *uscita* o in uscita si riferisce alla direzione in cui il dispositivo Data Box Heavy invia i dati esternamente, oltre la distribuzione, ad esempio in uscita verso Internet.

[!INCLUDE [data-box-port-requirements](../../includes/data-box-port-requirements.md)]

## <a name="next-steps"></a>Passaggi successivi

* [Distribuire Azure Data Box](data-box-deploy-ordered.md)
