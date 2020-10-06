---
title: Requisiti di sistema di Microsoft Azure Data Box | Microsoft Docs
description: Informazioni sui requisiti di sistema importanti per la Azure Data Box e per i client che si connettono al Data Box.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 10/02/2020
ms.author: alkohli
ms.openlocfilehash: 5f1623ef4dde59e816e3afe5a5f5894c49469580
ms.sourcegitcommit: d9ba60f15aa6eafc3c5ae8d592bacaf21d97a871
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/06/2020
ms.locfileid: "91767879"
---
# <a name="azure-data-box-system-requirements"></a>Requisiti di sistema di Azure Data Box

Questo articolo descrive i requisiti di sistema importanti per la Microsoft Azure Data Box e per i client che si connettono al Data Box. È consigliabile esaminare attentamente le informazioni prima di distribuire il Data Box e quindi farvi riferimento in base alle esigenze durante la distribuzione e il funzionamento.

I requisiti di sistema includono:

* **Requisiti software:** Per gli host che si connettono al Data Box, descrive i sistemi operativi supportati, i protocolli di trasferimento di file, gli account di archiviazione, i tipi di archiviazione e i browser per l'interfaccia utente Web locale.
* **Requisiti di rete:** Per la Data Box, descrive i requisiti di connessione di rete e porta per un funzionamento ottimale del Data Box.


## <a name="software-requirements"></a>Requisiti software

I requisiti software includono i sistemi operativi supportati, i protocolli di trasferimento di file, gli account di archiviazione, i tipi di archiviazione e i browser per l'interfaccia utente Web locale.

### <a name="supported-operating-systems-for-clients"></a>Sistemi operativi supportati per i client

[!INCLUDE [data-box-supported-os-clients](../../includes/data-box-supported-os-clients.md)]


### <a name="supported-file-transfer-protocols-for-clients"></a>Protocolli di trasferimento di file supportati per i client

[!INCLUDE [data-box-supported-file-systems-clients](../../includes/data-box-supported-file-systems-clients.md)]

> [!IMPORTANT] 
> La connessione a condivisioni di Data Box non è supportata tramite REST per gli ordini di esportazione. 

### <a name="supported-storage-accounts"></a>Account di archiviazione supportati

[!INCLUDE [data-box-supported-storage-accounts](../../includes/data-box-supported-storage-accounts.md)]

### <a name="supported-storage-types"></a>Tipi di archivio supportati

[!INCLUDE [data-box-supported-storage-types](../../includes/data-box-supported-storage-types.md)]

### <a name="supported-web-browsers"></a>Web browser supportati

[!INCLUDE [data-box-supported-web-browsers](../../includes/data-box-supported-web-browsers.md)]

## <a name="networking-requirements"></a>Requisiti di rete

Il data center disponga di una rete ad alta velocità. È consigliabile avere una connessione di almeno 10 GbE. Se non è disponibile una connessione 10 GbE, è possibile usare un collegamento dati da 1 GbE per copiare i dati, ma le velocità di copia sono interessate.

### <a name="port-requirements"></a>Requisiti delle porte

La tabella seguente elenca le porte che devono essere aperte nel firewall per consentire il traffico SMB o NFS. In questa tabella, *in (in* *ingresso*) si riferisce alla direzione da cui il client in ingresso richiede l'accesso al dispositivo. *Out* *(o in uscita)* si riferisce alla direzione in cui il dispositivo data box invia i dati all'esterno, oltre la distribuzione: ad esempio, in uscita verso Internet.

[!INCLUDE [data-box-port-requirements](../../includes/data-box-port-requirements.md)]


## <a name="next-steps"></a>Passaggi successivi

* [Distribuire Azure Data Box](data-box-deploy-ordered.md)
