---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 02/27/2020
ms.author: mimart
ms.openlocfilehash: f4944881e6eff3e9a56a74bc997c280f9fff6be8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78186998"
---
## <a name="ropc-flow-notes"></a>Note sul flusso ROPC
In Azure Active Directory B2C (Azure AD B2C) sono supportate le opzioni seguenti:

- **Client nativo**: l'interazione dell'utente durante l'autenticazione avviene quando il codice viene eseguito in un dispositivo lato utente. Il dispositivo può essere un'applicazione mobile in esecuzione in un sistema operativo nativo, ad esempio Android e iOS.
- **Flusso client pubblico**: nella chiamata API vengono inviate solo le credenziali utente, raccolte da un'applicazione. Le credenziali dell'applicazione non vengono inviate.
- **Aggiunta di nuove attestazioni**: il contenuto del token ID può essere modificato per aggiungere nuove attestazioni.

I flussi seguenti non sono supportati:

- **Da server a server**: il sistema di protezione delle identità richiede un indirizzo IP affidabile raccolto dal chiamante (il client nativo) come parte dell'interazione. In una chiamata API lato server viene usato solo l'indirizzo IP del server. Se viene superata una soglia dinamica di autenticazioni non riuscite, il sistema di protezione delle identità può identificare un indirizzo IP ripetuto come un utente malintenzionato.
- **Flusso client riservato**: l'ID client dell'applicazione viene convalidato, ma non viene convalidato il segreto dell'applicazione.

Quando si usa il flusso ROPC, tenere presente quanto segue:

- ROPC non funziona quando si verifica un'interruzione del flusso di autenticazione che richiede l'interazione dell'utente. Ad esempio, quando una password è scaduta o deve essere modificata, è necessaria l'autenticazione a più fattori o quando è necessario raccogliere più informazioni durante l'accesso, ad esempio il consenso dell'utente.
- ROPC supporta solo account locali. Gli utenti non possono accedere con provider di identità federati come Microsoft, Google +, Twitter, AD FS o Facebook.
- La gestione delle sessioni, inclusa l'opzione Mantieni l'accesso (KMSI), non è applicabile.
