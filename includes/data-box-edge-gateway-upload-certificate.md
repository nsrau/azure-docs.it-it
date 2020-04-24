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
Un certificato SSL appropriato garantisce che vengano inviate informazioni crittografate al server appropriato. Oltre alla crittografia, il certificato consente anche l'autenticazione. È possibile caricare il proprio certificato SSL attendibile tramite l'interfaccia di PowerShell del dispositivo.

1. [Connettersi all'interfaccia di PowerShell](#connect-to-the-powershell-interface).
2. Usare il `Set-HcsCertificate` cmdlet per caricare il certificato. Quando richiesto, specificare i parametri seguenti:

   - `CertificateFilePath`: Percorso della condivisione che contiene il file del certificato in formato *PFX* .
   - `CertificatePassword`: Password usata per proteggere il certificato.
   - `Credentials`-Username per accedere alla condivisione che contiene il certificato. Quando richiesto, fornire la password alla condivisione di rete.

     Nell'esempio seguente viene illustrato l'utilizzo di questo cmdlet:

     ```
     Set-HcsCertificate -Scope LocalWebUI -CertificateFilePath "\\myfileshare\certificates\mycert.pfx" -CertificatePassword "mypassword" -Credential "Username"
     ```

