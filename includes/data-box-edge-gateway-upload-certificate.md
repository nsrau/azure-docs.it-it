---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/23/2019
ms.author: alkohli
ms.openlocfilehash: d7a9923d5bd9e357bcd75fae6e0a7d1bcd437a53
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2019
ms.locfileid: "64732649"
---
Un certificato SSL corretto assicura che si sta inviando informazioni crittografate a server appropriato. Oltre alla crittografia, il certificato consente anche l'autenticazione. È possibile caricare il proprio certificato SSL attendibile tramite l'interfaccia PowerShell del dispositivo.

1. [Connettersi all'interfaccia di PowerShell](#connect-to-the-powershell-interface).
2. Usare il `Set-HcsCertificate` cmdlet per caricare il certificato. Quando richiesto, specificare i parametri seguenti:

   - `CertificateFilePath` -Percorso della condivisione che contiene il file del certificato nel *PFX* formato.
   - `CertificatePassword` -Password utilizzata per proteggere il certificato.
   - `Credentials` -Username e password per accedere alla condivisione contenente il certificato.

     Nell'esempio seguente viene illustrato l'utilizzo di questo cmdlet:

     ```
     Set-HcsCertificate -Scope LocalWebUI -CertificateFilePath "\\myfileshare\certificates\mycert.pfx" -CertificatePassword "mypassword" -Credential "Username/Password"
     ```

