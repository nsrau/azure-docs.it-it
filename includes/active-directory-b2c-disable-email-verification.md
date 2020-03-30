---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 03/11/2020
ms.author: mimart
ms.openlocfilehash: 4e03d076fc9ed7d87326a4c8b4f8bf6c0b21c750
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79126725"
---
## <a name="disable-email-verification"></a>Disabilitare la verifica tramite posta elettronica

Per impostazione predefinita, Azure Active Directory B2C (Azure AD B2C) verifica l'indirizzo di posta elettronica del cliente per gli account locali (account per gli utenti che si iscrivono con l'indirizzo di posta elettronica o il nome utente). Azure AD B2C garantisce indirizzi di posta elettronica validi richiedendo ai clienti di verificarli durante il processo di iscrizione. Impedisce inoltre agli attori malintenzionati di utilizzare processi automatizzati per generare account fraudolenti nelle applicazioni.

Alcuni sviluppatori di applicazioni preferiscono ignorare la verifica della posta elettronica durante il processo di iscrizione e chiedere invece ai clienti di verificare il proprio indirizzo di posta elettronica in un secondo momento. A tale scopo, è possibile configurare Azure AD B2C in modo da disabilitare la verifica tramite posta elettronica. In questo modo si crea un processo di iscrizione più agevole e offre agli sviluppatori la flessibilità necessaria per differenziare i clienti che hanno verificato il proprio indirizzo di posta elettronica dai clienti che non lo hanno fatto.

> [!WARNING]
> Disabilitando la verifica tramite posta elettronica nel processo di iscrizione si potrebbe ricevere posta indesiderata. Se si disabilita la verifica della posta elettronica fornita da Azure AD B2C predefinita, è consigliabile implementare un sistema di verifica sostitutivo.
