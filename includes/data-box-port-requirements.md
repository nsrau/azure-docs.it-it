---
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: include
ms.date: 07/11/2019
ms.author: alkohli
ms.openlocfilehash: 4a3925752d1af5e43d5984b06c0a68aa9faa214b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "67839754"
---
| N. porta| In ingresso/In uscita | Ambito porta| Obbligatoria| Note |   |
|--------|-----|-----|-----------|----------|-----------|
| TCP 80 (HTTP)|In ingresso|LAN|Sì|Questa porta viene usata per connettersi alle API REST di archiviazione di Data Box Blog tramite HTTP. Se non ci si connette alle API REST, questo reindirizza automaticamente all'interfaccia utente Web locale nel periodo 8443.If not connecting to REST APIs, this automatically redirects to local web UI over 8443. |
| TCP 443 (HTTPS)|In ingresso|LAN|Sì|Questa porta viene usata per connettersi alle API REST di archiviazione di Data Box Blog su HTTPS. Se non ci si connette alle API REST, questo reindirizza automaticamente all'interfaccia utente Web locale nel periodo 8443.If not connecting to REST APIs, this automatically redirects to local web UI over 8443. |
| TCP 8443 (HTTPS-Alt)|In ingresso|LAN|Sì|Si tratta di una porta alternativa per HTTPS e viene utilizzata per la connessione all'interfaccia utente Web locale per la gestione dei dispositivi. |
| TCP 445 (SMB)|Out/In|LAN|In alcuni casi<br>Vedere le note|Questa porta è necessaria solo se ci si connette tramite SMB. |
| TCP 2049 (NFS)|Out/In|LAN|In alcuni casi<br>Vedere le note|Questa porta è necessaria solo se ci si connette tramite NFS. |
| TCP 111 (NFS)|Out/In|LAN|In alcuni casi<br>Vedere le note|Questa porta viene utilizzata per il mapping rpcbind/porta e necessaria solo se ci si connette tramite NFS.  |
