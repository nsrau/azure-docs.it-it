---
title: Requisiti di sistema Microsoft Azure Data Box Heavy | Microsoft Docs
description: Informazioni sui requisiti di software e di rete per la Azure Data Box Heavy
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: article
ms.date: 07/03/2019
ms.author: alkohli
ms.openlocfilehash: 6097dba9193fce4c8158137b488080107881994a
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79260072"
---
# <a name="azure-data-box-heavy-system-requirements"></a>Requisiti di sistema Azure Data Box Heavy

Questo articolo descrive i requisiti di sistema importanti per il dispositivo Azure Data Box Heavy e per i client che si connettono al dispositivo. Si consiglia di rivedere attentamente le informazioni prima di distribuire il Data Box Heavy e quindi di richiamarle in modo necessario durante la distribuzione e l'operazione successiva.

I requisiti di sistema includono:

* **Requisiti software per gli host che si connettono a data box Heavy** : descrive le piattaforme supportate, i browser per l'interfaccia utente Web locale, i client SMB e gli eventuali requisiti aggiuntivi per gli host che possono connettersi al data box.
* **Requisiti di rete per la data box Heavy** : fornisce informazioni sui requisiti di rete per il funzionamento ottimale del dispositivo data box Heavy.

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

Il data center disponga di una rete ad alta velocità. Per la velocità di copia più veloce, le connessioni 2 40-GbE possono essere utilizzate in parallelo (una per nodo). Se 40-GbE non è disponibile, è consigliabile disporre di almeno 2 10-GbE connessioni (uno per nodo).

### <a name="port-requirements"></a>Requisiti per le porte

La tabella seguente elenca le porte che devono essere aperte nel firewall per consentire il traffico SMB o NFS. In questa tabella, *in* o *in ingresso* fa riferimento alla direzione da cui le richieste client in ingresso accedono al dispositivo. *Out* In uscita *o in uscita si* intende la direzione in cui il dispositivo data box Heavy invia i dati all'esterno, oltre la distribuzione: ad esempio, in uscita verso Internet.

[!INCLUDE [data-box-port-requirements](../../includes/data-box-port-requirements.md)]

## <a name="next-steps"></a>Passaggi successivi

* [Distribuire Azure Data Box](data-box-deploy-ordered.md)
