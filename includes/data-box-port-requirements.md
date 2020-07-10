---
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: include
ms.date: 07/11/2019
ms.author: alkohli
ms.openlocfilehash: 948f5399ceb931d701341c2cf378482ede94979d
ms.sourcegitcommit: 3541c9cae8a12bdf457f1383e3557eb85a9b3187
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/09/2020
ms.locfileid: "86200318"
---
| N. porta| In ingresso/In uscita | Ambito porta| Obbligatorio| Note |
|--------|-----|-----|-----------|----------|
| TCP 80 (HTTP)|In|LAN|Sì|Questa porta viene usata per connettersi a Data Box le API REST di archiviazione Blog su HTTP. Se non ci si connette alle API REST, viene automaticamente reindirizzato all'interfaccia utente Web locale su 8443. |
| TCP 443 (HTTPS)|In|LAN|Sì|Questa porta viene usata per connettersi a Data Box le API REST di archiviazione Blog su HTTPS. Se non ci si connette alle API REST, viene automaticamente reindirizzato all'interfaccia utente Web locale su 8443. |
| TCP 8443 (HTTPS-ALT)|In|LAN|Sì|Si tratta di una porta alternativa per HTTPS e viene usata quando ci si connette all'interfaccia utente Web locale per la gestione dei dispositivi. |
| TCP 445 (SMB)|Out/In|LAN|In alcuni casi<br>Vedere le note|Questa porta è obbligatoria solo se ci si connette tramite SMB. |
| TCP 2049 (NFS)|Out/In|LAN|In alcuni casi<br>Vedere le note|Questa porta è obbligatoria solo se ci si connette tramite NFS. |
| TCP 111 (NFS)|Out/In|LAN|In alcuni casi<br>Vedere le note|Questa porta viene usata per il mapping di rpcbind/Port e obbligatoria solo se ci si connette tramite NFS.  |
