---
title: Cronologia delle versioni dell'agente di protezione password di Azure AD locale
description: Documentazione della cronologia delle versioni e delle modifiche di comportamento
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: article
ms.date: 10/30/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: jsimmons
ms.openlocfilehash: c52c84a1311c30c19356bb8a1287b203faf476fc
ms.sourcegitcommit: ae45eacd213bc008e144b2df1b1d73b1acbbaa4c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/01/2018
ms.locfileid: "50742906"
---
# <a name="preview--azure-ad-password-protection-agent-version-history"></a>Anteprima: cronologia delle versioni dell'agente di protezione password di Azure AD

|     |
| --- |
| La protezione password di Azure AD è una funzionalità in anteprima pubblica di Azure Active Directory. Per altre informazioni sulle funzioni in anteprima, vedere [Condizioni per l'utilizzo supplementari per le anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).|
|     |

## <a name="12100"></a>1.2.10.0

Data di rilascio: 17/08/2018

Correzioni:

* Register-AzureADPasswordProtectionProxy e Register-AzureADPasswordProtectionForest ora supportano l'autenticazione a più fattori
* Register-AzureADPasswordProtectionProxy richiede un controller di dominio WS2012 o versioni successive nel dominio per evitare errori di crittografia.
* Il servizio agente del controller di dominio è più affidabile per le richieste dei nuovi criteri password da Azure all'avvio.
* Il servizio agente del controller di dominio richiederà i nuovi criteri password da Azure ogni ora, se necessario, ma adesso in base a un'ora di inizio selezionata in modo casuale.
* Il servizio agente del controller di dominio non causerà più un ritardo indefinito nell'annuncio di un nuovo controller di dominio in caso di installazione in un server prima della relativa promozione come replica.
* Il servizio agente del controller di dominio rispetterà ora l'impostazione di configurazione "Abilita la password di protezione in Windows Server Active Directory"
* I programmi di installazione dell'agente del controller di dominio e del proxy supporteranno ora entrambi l'aggiornamento sul posto per l'aggiornamento a versioni future.

> [!WARNING]
> L'aggiornamento sul porto dalla versione 1.1.10.3 non è supportato e genererà un errore di installazione. Per eseguire l'aggiornamento alla versione 1.2.10 o versioni successive, è prima di tutto necessario disinstallare completamente il software del servizio agente del controller di dominio e del servizio proxy, quindi installare la nuova versione da zero. È richiesta una nuova registrazione del servizio proxy di protezione password di Azure AD.  Non è necessario ripetere la registrazione della foresta.

> [!NOTE]
> Per gli aggiornamenti sul posto del software dell'agente del controller di dominio sarà richiesto un riavvio.

* Il servizio agente del controller di dominio e il servizio proxy supportano ora l'esecuzione in un server configurato per usare solo algoritmi conformi a FIPS.
* Registrazione migliorata
* Correzioni minori per prestazioni e affidabilità

## <a name="11103"></a>1.1.10.3

Data di rilascio: 15/06/2018

Versione di anteprima pubblica iniziale

## <a name="next-steps"></a>Passaggi successivi

[Distribuire la protezione delle password di Azure AD](howto-password-ban-bad-on-premises-deploy.md)
