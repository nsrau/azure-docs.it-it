---
title: Requisiti di sistema casella pesante di Microsoft Azure dei dati | Microsoft Docs
description: Scopri il software e i requisiti di rete per la finestra pesante Azure dei dati
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: article
ms.date: 07/03/2019
ms.author: alkohli
ms.openlocfilehash: 6097dba9193fce4c8158137b488080107881994a
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67839787"
---
# <a name="azure-data-box-heavy-system-requirements"></a>Azure Data finestra elevati requisiti di sistema

Questo articolo descrive i requisiti di sistema importanti per il dispositivo Azure dati casella pesante e per i client che si connettono al dispositivo. È consigliabile che consultare le informazioni con attenzione prima di distribuire pesante finestra di dati e quindi fare riferimento ad esso in base alle esigenze durante la distribuzione e la successiva operazione.

I requisiti di sistema includono:

* **Requisiti software per l'host che si connettono a dati casella pesante** -descrive le piattaforme supportate, i browser per l'interfaccia utente web locale, i client SMB ed eventuali requisiti aggiuntivi per gli host che possono connettersi al Data Box.
* **Requisiti di rete per il pesante finestra dati** -fornisce informazioni sui requisiti di rete per il funzionamento ottimale del dispositivo dati casella pesante.

## <a name="software-requirements"></a>Requisiti software

I requisiti software includono le informazioni sui sistemi operativi supportati, sui browser supportati per l'interfaccia utente Web locale e sui client SMB.

### <a name="supported-operating-systems-for-clients"></a>Sistemi operativi supportati per i client

[!INCLUDE [data-box-supported-os-clients](../../includes/data-box-supported-os-clients.md)]

### <a name="supported-file-systems-for-linux-clients"></a>File system supportati per client Linux

[!INCLUDE [data-box-supported-file-systems-clients](../../includes/data-box-supported-file-systems-clients.md)]

### <a name="supported-storage-accounts"></a>Account di archiviazione supportati

[!INCLUDE [data-box-supported-storage-accounts](../../includes/data-box-supported-storage-accounts.md)]

### <a name="supported-storage-types"></a>Tipi di archiviazione supportati

[!INCLUDE [data-box-supported-storage-types](../../includes/data-box-supported-storage-types.md)]

### <a name="supported-web-browsers"></a>Web browser supportati

[!INCLUDE [data-box-supported-web-browsers](../../includes/data-box-supported-web-browsers.md)]

## <a name="networking-requirements"></a>Requisiti di rete

Il data center disponga di una rete ad alta velocità. Per la velocità di copia più veloce, due GbE di 40 connessioni possono essere utilizzate in parallelo (uno per ogni nodo). Se non è disponibile a 40 GbE, è consigliabile che siano configurate almeno due connessioni 10 GbE (uno per ogni nodo).

### <a name="port-requirements"></a>Requisiti delle porte

Nella tabella seguente elenca le porte che devono essere aperte nel firewall per consentire il traffico SMB o NFS. In questa tabella, *in* o *in ingresso* fa riferimento alla direzione da cui le richieste client in ingresso accedono al dispositivo. *Out* oppure *in uscita* fa riferimento alla direzione in cui il dispositivo dati casella pesante invia i dati all'esterno, oltre la distribuzione: ad esempio, in uscita a Internet.

[!INCLUDE [data-box-port-requirements](../../includes/data-box-port-requirements.md)]

## <a name="next-steps"></a>Passaggi successivi

* [Distribuire Azure Data Box](data-box-deploy-ordered.md)
