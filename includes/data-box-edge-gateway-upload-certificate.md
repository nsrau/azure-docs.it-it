---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 06/26/2019
ms.author: alkohli
ms.openlocfilehash: 09d9b5bbf3f9ca7a4eef37891d03c9c865e7f74b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "67448620"
---
Un certificato SSL appropriato garantisce l'invio di informazioni crittografate al server giusto. Oltre alla crittografia, il certificato consente anche l'autenticazione. È possibile caricare il proprio certificato SSL attendibile tramite l'interfaccia di PowerShell del dispositivo.

1. [Connettersi all'interfaccia](#connect-to-the-powershell-interface)di PowerShell .
2. Utilizzare `Set-HcsCertificate` il cmdlet per caricare il certificato. Quando richiesto, specificare i seguenti parametri:

   - `CertificateFilePath`- Percorso della condivisione che contiene il file del certificato in formato *pfx.*
   - `CertificatePassword`- Una password utilizzata per proteggere il certificato.
   - `Credentials`- Nome utente per accedere alla condivisione che contiene il certificato. Fornire la password alla condivisione di rete quando richiesto.

     Nell'esempio seguente viene illustrato l'utilizzo di questo cmdlet:

     ```
     Set-HcsCertificate -Scope LocalWebUI -CertificateFilePath "\\myfileshare\certificates\mycert.pfx" -CertificatePassword "mypassword" -Credential "Username"
     ```

