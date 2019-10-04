---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/25/2019
ms.author: alkohli
ms.openlocfilehash: 98f9e0377e560fa0bba2fd470ff01431b2ed21d9
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/18/2019
ms.locfileid: "67180587"
---
| N. porta| In ingresso/In uscita | Ambito porta| Obbligatoria|   Note |   |
|--------|-----|-----|-----------|----------|-----------|
| TCP 80 (HTTP)|In uscita|WAN |No|La porta in uscita viene usata per consentire all'accesso Internet di recuperare gli aggiornamenti. <br>Il proxy Web in uscita è configurabile dall'utente. |
| TCP 443 (HTTPS)|In uscita|WAN|Yes|La porta in uscita viene usata per accedere ai dati nel cloud.<br>Il proxy Web in uscita è configurabile dall'utente.|
| UDP 123 (NTP)|In uscita|WAN|In alcuni casi<br>Vedere le note|Questa porta è obbligatoria solo se si usa un server NTP basato su Internet.  |   
| UDP 53 (DNS)|In uscita|WAN|In alcuni casi<br>Vedere le note|Questa porta è obbligatoria solo se si usa un server DNS basato su Internet.<br>È consigliabile l'uso di un server DNS locale. |
| TCP 5985 (WinRM)|Out/In|LAN|In alcuni casi<br>Vedere le note|Questa porta è necessaria per connettersi al dispositivo tramite PowerShell remoto su HTTP.  |
| UDP 67 (DHCP)|In uscita|LAN|In alcuni casi<br>Vedere le note|Questa porta è obbligatoria solo se si usa un server DHCP locale.  |
| TCP 80 (HTTP)|Out/In|LAN|Yes|Questa è la porta in ingresso per l'interfaccia utente locale nel dispositivo per la gestione locale. <br>L'accesso all'interfaccia utente locale tramite HTTP esegue il reindirizzamento automatico a HTTPS.  |
| TCP 443 (HTTPS)|Out/In|LAN|Yes|Questa è la porta in ingresso per l'interfaccia utente locale nel dispositivo per la gestione locale. |
| TCP 445 (SMB)|In ingresso|LAN|In alcuni casi<br>Vedere le note|Questa porta è obbligatoria solo se ci si connette tramite SMB. |
| TCP 2049 (NFS)|In ingresso|LAN|In alcuni casi<br>Vedere le note|Questa porta è obbligatoria solo se ci si connette tramite NFS. |
