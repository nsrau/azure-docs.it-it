---
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: include
ms.date: 09/06/2020
ms.author: alkohli
ms.openlocfilehash: b9ff5968b4bb406f1a96780985b5c6fe64ca976c
ms.sourcegitcommit: 59ea8436d7f23bee75e04a84ee6ec24702fb2e61
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/07/2020
ms.locfileid: "89505910"
---
| N. porta| In ingresso/In uscita | Ambito porta| Obbligatoria| Note |
|--------|-----|-----|-----------|----------|
| TCP 80 (HTTP)|In|LAN|Sì|Questa porta viene usata per connettersi alle API REST dell'archiviazione BLOB Data Box su HTTP. Se non ci si connette alle API REST, viene automaticamente reindirizzato all'interfaccia utente Web locale su 8443. |
| TCP 443 (HTTPS)|In|LAN|Sì|Questa porta viene usata per connettersi alle API REST dell'archiviazione BLOB Data Box su HTTPS. Se non ci si connette alle API REST, viene automaticamente reindirizzato all'interfaccia utente Web locale su 8443. |
| TCP 8443 (HTTPS-ALT)|In|LAN|Sì|Si tratta di una porta alternativa per HTTPS e viene usata quando ci si connette all'interfaccia utente Web locale per la gestione dei dispositivi. |
| TCP 445 (SMB)|Out/In|LAN|In alcuni casi<br>Vedere le note|Questa porta è obbligatoria solo se ci si connette tramite SMB. |
| TCP 2049 (NFS)|Out/In|LAN|In alcuni casi<br>Vedere le note|Questa porta è obbligatoria solo se ci si connette tramite NFS. |
| TCP 111 (NFS)|Out/In|LAN|In alcuni casi<br>Vedere le note|Questa porta viene usata per il mapping di rpcbind/Port e obbligatoria solo se ci si connette tramite NFS.  |
