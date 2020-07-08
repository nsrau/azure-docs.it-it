---
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: include
ms.date: 07/11/2019
ms.author: alkohli
ms.openlocfilehash: 4a3925752d1af5e43d5984b06c0a68aa9faa214b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "67839754"
---
| N. porta| In ingresso/In uscita | Ambito porta| Necessario| Note |   |
|--------|-----|-----|-----------|----------|-----------|
| TCP 80 (HTTP)|In|LAN|Sì|Questa porta viene usata per connettersi a Data Box le API REST di archiviazione Blog su HTTP. Se non ci si connette alle API REST, viene automaticamente reindirizzato all'interfaccia utente Web locale su 8443. |
| TCP 443 (HTTPS)|In|LAN|Sì|Questa porta viene usata per connettersi a Data Box le API REST di archiviazione Blog su HTTPS. Se non ci si connette alle API REST, viene automaticamente reindirizzato all'interfaccia utente Web locale su 8443. |
| TCP 8443 (HTTPS-ALT)|In|LAN|Sì|Si tratta di una porta alternativa per HTTPS e viene usata quando ci si connette all'interfaccia utente Web locale per la gestione dei dispositivi. |
| TCP 445 (SMB)|Out/In|LAN|In alcuni casi<br>Vedere le note|Questa porta è obbligatoria solo se ci si connette tramite SMB. |
| TCP 2049 (NFS)|Out/In|LAN|In alcuni casi<br>Vedere le note|Questa porta è obbligatoria solo se ci si connette tramite NFS. |
| TCP 111 (NFS)|Out/In|LAN|In alcuni casi<br>Vedere le note|Questa porta viene usata per il mapping di rpcbind/Port e obbligatoria solo se ci si connette tramite NFS.  |
