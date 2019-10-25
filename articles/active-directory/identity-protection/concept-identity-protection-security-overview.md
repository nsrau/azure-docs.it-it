---
title: Panoramica della sicurezza di Azure Active Directory (Azure AD) Identity Protection
description: Scopri come la panoramica sulla sicurezza ti offre informazioni dettagliate sul comportamento di sicurezza dell'organizzazione.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 10/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: adc4acf10fee1b8b2c5e3af18389a49d84e2df14
ms.sourcegitcommit: 7efb2a638153c22c93a5053c3c6db8b15d072949
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/24/2019
ms.locfileid: "72887234"
---
# <a name="azure-active-directory-identity-protection---security-overview"></a>Azure Active Directory Identity Protection - Panoramica della sicurezza

La [Panoramica della sicurezza](https://aka.ms/IdentityProtectionRefresh) nella portale di Azure fornisce informazioni approfondite sul comportamento di sicurezza dell'organizzazione. Consente di identificare gli attacchi potenziali e comprendere l'efficacia dei criteri.

"Panoramica della sicurezza" è diviso in linea di massima in due sezioni:

- Tendenze, a sinistra, fornisce una sequenza temporale dei rischi nell'organizzazione.
- Riquadri, a destra, evidenzia i principali problemi in corso nell'organizzazione e suggerisce come intervenire rapidamente.

![Panoramica della sicurezza](./media/concept-identity-protection-security-overview/01.png)
  
## <a name="trends"></a>Tendenze

### <a name="new-risky-users-detected"></a>Nuovi utenti a rischio rilevati

Questo grafico mostra il numero dei nuovi utenti a rischio rilevati nel periodo di tempo selezionato. È possibile filtrare la visualizzazione di questo grafico per livello di rischio utente (basso, medio, elevato). Passare il mouse sugli incrementi delle date in UTC per visualizzare il numero di utenti a rischio rilevati per quel giorno. Facendo clic su questo grafico si accede al report "Utenti a rischio". Per salvaguardare gli utenti a rischio, provare a modificarne la password.

### <a name="new-risky-sign-ins-detected"></a>Nuovi accessi a rischio rilevati

Questo grafico mostra il numero dei nuovi accessi a rischio rilevati nel periodo di tempo selezionato. È possibile filtrare la visualizzazione di questo grafico in base al tipo di rischio di accesso (in tempo reale o aggregato) e al livello di rischio di accesso (basso, medio, elevato). Gli accessi non protetti sono accessi a rischio con esito positivo in tempo reale a cui non è stata inviata una richiesta di autenticazione a più fattori. (Nota: gli accessi rischiosi a causa di rilevamenti offline non possono essere protetti in tempo reale tramite criteri di rischio di accesso). Passare il mouse sugli incrementi delle date in UTC per visualizzare il numero di accessi a rischio rilevati per quel giorno. Facendo clic su questo grafico si accede al report "Accessi a rischio".

## <a name="tiles"></a>Riquadri
 
### <a name="high-risk-users"></a>Utenti ad alto rischio

Il riquadro "Utenti ad alto rischio", mostra il numero più recente di utenti con elevata probabilità di identità compromessa. Questi ultimi dovrebbero rappresentare una priorità per l'analisi. Facendo clic sul riquadro "Utenti ad alto rischio" si viene reindirizzati a una vista filtrata del report "Utenti a rischio" che mostra solo gli utenti con un alto livello di rischio. Usando questo report, è possibile acquisire altre informazioni e salvaguardare questi utenti tramite la reimpostazione della password.

![Panoramica della sicurezza](./media/concept-identity-protection-security-overview/02.png)

### <a name="medium-risk-users"></a>Utenti a medio rischio
Il riquadro "Utenti a medio rischio", mostra il numero più recente di utenti con media probabilità di identità compromessa. Facendo clic sul riquadro "Utenti a medio rischio" si viene reindirizzati a una vista filtrata del report "Utenti a rischio" che mostra solo gli utenti con un livello di rischio medio. Usando questo report, è possibile effettuare un'ulteriore analisi e salvaguardare questi utenti.

### <a name="unprotected-risky-sign-ins"></a>Accessi a rischio non protetti

Il riquadro "accessi a rischio" non protetto Mostra il numero di accessi a rischio in tempo reale di una settimana che non sono stati bloccati o con autenticazione a più fattori in base a criteri di accesso condizionale, criteri di rischio per la protezione delle identità o multi-factor authentication per utente. Si tratta di accessi con esito positivo potenzialmente compromessi a cui non è stata inviata una richiesta di autenticazione a più fattori. Per proteggere tali accessi in futuro, applicare un criterio di rischio di accesso. Facendo clic sul riquadro "Accessi a rischio non protetti" si viene reindirizzati al pannello di configurazione dei criteri di rischio di accesso in cui configurare il criterio per richiedere l'autenticazione a più fattori per un accesso che presenta un livello di rischio specificato.

### <a name="legacy-authentication"></a>Autenticazione legacy

Il riquadro "Autenticazione legacy" mostra il numero di autenticazioni legacy dell'ultima settimana nell'organizzazione. I protocolli di autenticazione legacy non supportano i metodi di sicurezza moderni, ad esempio l'autenticazione a più fattori. Per evitare l'autenticazione legacy, è possibile applicare un criterio di accesso condizionale. Facendo clic sul riquadro "Autenticazione Legacy" si viene reindirizzati al riquadro "Identity Secure Score".

### <a name="identity-secure-score"></a>Identity Secure Score

Identity Secure Score valuta e confronta il comportamento di sicurezza con i modelli del settore. Se si fa clic sul riquadro "Identity Secure Score (Preview)", verrà reindirizzato al pannello "Identity Secure Score", in cui è possibile ottenere altre informazioni sul miglioramento del comportamento di sicurezza.

## <a name="next-steps"></a>Passaggi successivi

- [Che cos'è il rischio](concept-identity-protection-risks.md)

- [Criteri disponibili per attenuare i rischi](concept-identity-protection-policies.md)
