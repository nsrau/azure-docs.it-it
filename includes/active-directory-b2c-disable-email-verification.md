---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 03/11/2020
ms.author: mimart
ms.openlocfilehash: 4e03d076fc9ed7d87326a4c8b4f8bf6c0b21c750
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "79126725"
---
## <a name="disable-email-verification"></a>Disabilitare la verifica tramite posta elettronica

Per impostazione predefinita, Azure Active Directory B2C (Azure AD B2C) verifica l'indirizzo di posta elettronica del cliente per gli account locali (account per gli utenti che effettuano l'iscrizione con l'indirizzo di posta elettronica o il nome utente). Azure AD B2C garantisce indirizzi di posta elettronica validi richiedendo ai clienti di verificarli durante il processo di iscrizione. Impedisce inoltre agli attori malintenzionati di usare processi automatizzati per generare account illeciti nelle applicazioni.

Alcuni sviluppatori di applicazioni preferiscono ignorare la verifica tramite posta elettronica durante il processo di iscrizione e i clienti possono invece verificare il proprio indirizzo di posta elettronica in un secondo momento. A tale scopo, è possibile configurare Azure AD B2C in modo da disabilitare la verifica tramite posta elettronica. Questa operazione consente di creare un processo di iscrizione più semplice e offre agli sviluppatori la flessibilità necessaria per distinguere i clienti che hanno verificato il proprio indirizzo di posta elettronica dai clienti che non lo hanno fatto.

> [!WARNING]
> Disabilitando la verifica tramite posta elettronica nel processo di iscrizione si potrebbe ricevere posta indesiderata. Se si disattiva la verifica predefinita della posta elettronica fornita dal Azure AD B2C, si consiglia di implementare un sistema di verifica sostitutivo.
