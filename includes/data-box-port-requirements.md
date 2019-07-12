---
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: include
ms.date: 07/11/2019
ms.author: alkohli
ms.openlocfilehash: 4a3925752d1af5e43d5984b06c0a68aa9faa214b
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67839754"
---
| N. porta| In ingresso/In uscita | Ambito porta| Obbligatoria| Note |   |
|--------|-----|-----|-----------|----------|-----------|
| TCP 80 (HTTP)|In ingresso|LAN|Yes|Questa porta viene usata per connettersi ad archiviazione di dati finestra Blog le API REST su HTTP. Se non ci si connette alle API REST, ciò reindirizza automaticamente all'interfaccia utente web locale oltre 8443. |
| TCP 443 (HTTPS)|In ingresso|LAN|Sì|Questa porta viene usata per connettersi ad archiviazione di dati finestra Blog le API REST su HTTPS. Se non ci si connette alle API REST, ciò reindirizza automaticamente all'interfaccia utente web locale oltre 8443. |
| TCP 8443 (HTTPS-Alt)|In ingresso|LAN|Sì|Si tratta di una porta alternativa per HTTPS e viene usato durante la connessione all'interfaccia utente web locale per la gestione dei dispositivi. |
| TCP 445 (SMB)|Out/In|LAN|In alcuni casi<br>Vedere le note|Questa porta è obbligatoria solo se ci si connette tramite SMB. |
| TCP 2049 (NFS)|Out/In|LAN|In alcuni casi<br>Vedere le note|Questa porta è obbligatoria solo se ci si connette tramite NFS. |
| TCP 111 (NFS)|Out/In|LAN|In alcuni casi<br>Vedere le note|Questa porta viene utilizzata per il mapping rpcbind o la porta e obbligatorio solo se ci si connette tramite NFS.  |
