---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 02/11/2019
ms.author: alkohli
ms.openlocfilehash: 783ae29e9ca0c9a609d1d1d283525a952c2f7f33
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/12/2019
ms.locfileid: "56118691"
---
| N. porta| In ingresso/In uscita | Ambito porta| Obbligatoria|   Note |   |
|--------|-----|-----|-----------|----------|-----------|
| TCP 80 (HTTP)|Out/In|WAN  |No |La porta in uscita viene usata per consentire all'accesso Internet di recuperare gli aggiornamenti. <br>Il proxy Web in uscita è configurabile dall'utente. |
| TCP 443 (HTTPS)|Out/In|WAN|Sì|La porta in uscita viene usata per accedere ai dati nel cloud.<br>Il proxy Web in uscita è configurabile dall'utente.|
| UDP 123 (NTP)|In uscita|WAN|In alcuni casi<br>Vedere le note|Questa porta è obbligatoria solo se si usa un server NTP basato su Internet.  |   
| UDP 53 (DNS)|In uscita|LAN|In alcuni casi<br>Vedere le note|Questa porta è obbligatoria solo se si usa un server DNS basato su Internet.<br>È consigliabile l'uso di un server DNS locale. |
| TCP 5985 (WinRM)|Out/In|LAN|In alcuni casi<br>Vedere le note|Questa porta è necessaria per connettersi al dispositivo tramite PowerShell remoto su HTTP.  |
| UDP 67 (DHCP)|In uscita|LAN|In alcuni casi<br>Vedere le note|Questa porta è obbligatoria solo se si usa un server DHCP locale.  |
| TCP 80 (HTTP)|Out/In|LAN|Sì|Questa è la porta in ingresso per l'interfaccia utente locale nel dispositivo per la gestione locale. <br>L'accesso all'interfaccia utente locale tramite HTTP esegue il reindirizzamento automatico a HTTPS.  |
| TCP 443 (HTTPS)|Out/In|LAN|Sì|Questa è la porta in ingresso per l'interfaccia utente locale nel dispositivo per la gestione locale. |
| TCP 445 (SMB)|In ingresso|LAN|In alcuni casi<br>Vedere le note|Questa porta è obbligatoria solo se ci si connette tramite SMB. |
| TCP 2049 (NFS)|In ingresso|LAN|In alcuni casi<br>Vedere le note|Questa porta è obbligatoria solo se ci si connette tramite NFS. |

