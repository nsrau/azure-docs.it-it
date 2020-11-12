---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 12/16/2019
ms.author: alkohli
ms.openlocfilehash: 417fbdea3f46dfb3e90ab4890cec5e88c5aa4e07
ms.sourcegitcommit: 4bee52a3601b226cfc4e6eac71c1cb3b4b0eafe2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/11/2020
ms.locfileid: "94523986"
---
| N. porta| In ingresso/In uscita | Ambito porta| Necessario | Note |
|---------|-----------|-----------|----------|-------|
| TCP 80 (HTTP)|In uscita|WAN |No|La porta in uscita viene usata per consentire all'accesso Internet di recuperare gli aggiornamenti. <br>Il proxy Web in uscita è configurabile dall'utente. |
| TCP 443 (HTTPS)|In uscita|WAN|Sì|La porta in uscita viene usata per accedere ai dati nel cloud.<br>Il proxy Web in uscita è configurabile dall'utente.|
| UDP 123 (NTP)|In uscita|WAN|In alcuni casi<br>Vedere le note|Questa porta è obbligatoria solo se si usa un server NTP basato su Internet.  |   
| UDP 53 (DNS)|In uscita|WAN|In alcuni casi<br>Vedere le note|Questa porta è obbligatoria solo se si usa un server DNS basato su Internet.<br>È consigliabile l'uso di un server DNS locale. |
| TCP 5985 (WinRM)|Out/In|LAN|In alcuni casi<br>Vedere le note|Questa porta è necessaria per connettersi al dispositivo tramite PowerShell remoto su HTTP.  |
| UDP 67 (DHCP)|In uscita|LAN|In alcuni casi<br>Vedere le note|Questa porta è obbligatoria solo se si usa un server DHCP locale.  |
| TCP 80 (HTTP)|Out/In|LAN|Sì|Questa è la porta in ingresso per l'interfaccia utente locale nel dispositivo per la gestione locale. <br>L'accesso all'interfaccia utente locale tramite HTTP esegue il reindirizzamento automatico a HTTPS.  |
| TCP 443 (HTTPS)|Out/In|LAN|Sì|Questa è la porta in ingresso per l'interfaccia utente locale nel dispositivo per la gestione locale. Questa porta viene usata anche per connettere Azure Resource Manager alle API locali del dispositivo, per connettere l'archiviazione BLOB tramite le API REST e al servizio token di sicurezza (STS) per l'autenticazione tramite token di accesso e di aggiornamento.|
| TCP 445 (SMB)|In|LAN|In alcuni casi<br>Vedere le note|Questa porta è obbligatoria solo se ci si connette tramite SMB. |
| TCP 2049 (NFS)|In|LAN|In alcuni casi<br>Vedere le note|Questa porta è obbligatoria solo se ci si connette tramite NFS. |


