---
title: Criteri dei rischio - Azure Active Directory Identity Protection
description: Abilitare e configurare i criteri di rischio in Azure Active Directory Identity Protection
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 01/06/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4ffa08f7ebf013d42d6da0589ce0f1ccc97289de
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75707006"
---
# <a name="how-to-configure-and-enable-risk-policies"></a>Procedura: Configurare e abilitare i criteri di rischioHow To: Configure and enable risk policies

Come abbiamo appreso nell'articolo precedente, i criteri di [protezione delle identità](concept-identity-protection-policies.md) sono disponibili due criteri di rischio che è possibile abilitare nella directory. 

- Criteri di rischio di accesso
- Criteri di rischio utente

![Pagina Panoramica della sicurezza per abilitare i criteri di rischio per utenti e accesso](./media/howto-identity-protection-configure-risk-policies/identity-protection-security-overview.png)

Entrambi i criteri funzionano per automatizzare la risposta ai rilevamenti dei rischi nell'ambiente e consentire agli utenti di correggere autonomamente quando viene rilevato il rischio. 

> [!VIDEO https://www.youtube.com/embed/zEsbbik-BTE]

## <a name="prerequisites"></a>Prerequisiti 

Se l'organizzazione desidera consentire agli utenti di correggere autonomamente quando vengono rilevati rischi, gli utenti devono essere registrati sia per la reimpostazione della password self-service che per Azure Multi-Factor Authentication. Ti consigliamo di [abilitare l'esperienza combinata](../authentication/howto-registration-mfa-sspr-combined.md) di registrazione delle informazioni di sicurezza per un'esperienza ottimale. Consentire agli utenti di correggere autonomamente li riporta a uno stato produttivo più rapidamente senza richiedere l'intervento dell'amministratore. Gli amministratori possono comunque visualizzare questi eventi e analizzarli dopo il fatto. 

## <a name="choosing-acceptable-risk-levels"></a>Scelta di livelli di rischio accettabili

Le organizzazioni devono decidere il livello di rischio che sono disposte ad accettare il bilanciamento dell'esperienza utente e la posizione di sicurezza. 

Microsoft consiglia di impostare la soglia dei criteri di rischio utente su **Alta** e i criteri di rischio di accesso su **Medio e superiore**.

La scelta di una soglia **alta** riduce la frequenza di attivazione dei criteri e riduce al minimo l'impatto sugli utenti. Tuttavia, esclude i rilevamenti dei rischi **di livello basso** e **medio** dal criterio, che potrebbe non impedire a un utente malintenzionato di sfruttare un'identità compromessa. La selezione di una soglia **bassa** introduce ulteriori interruzioni dell'utente, ma ha aumentato il livello di sicurezza.

## <a name="exclusions"></a>Esclusioni

Tutti i criteri consentono di escludere gli utenti, ad esempio [l'accesso di emergenza o gli account di amministratore di break-glass](../users-groups-roles/directory-emergency-access.md). Le organizzazioni possono determinare la necessità di escludere altri account da criteri specifici in base alle modalità di utilizzo degli account. Tutte le esclusioni devono essere riesaminate regolarmente per vedere se sono ancora applicabili.

I [percorsi](../conditional-access/location-condition.md) di rete attendibili configurati vengono utilizzati da Identity Protection in alcuni rilevamenti dei rischi per ridurre i falsi positivi.

## <a name="enable-policies"></a>Abilitare i criteri

I passaggi seguenti illustrano come abilitare i criteri di rischio utente e di accesso al rischio di accesso.

1. Passare al [portale di Azure](https://portal.azure.com).
1. **Security** > Passare a**Panoramica**di**Protezione** > identità di sicurezza di **Azure Active Directory** > .
1. Selezionare **Configura criteri di rischio utente**.
   1. In **Assegnazioni**
      1. **Utenti:** scegliere **Tutti gli utenti** o Seleziona individui e **gruppi** se si limita l'implementazione.
         1. Facoltativamente, è possibile scegliere di escludere gli utenti dal criterio.
      1. **Condizioni Rischio** - **utente** Raccomandazione da Microsoft è di impostare questa opzione su **Alta**.
   1. In **Controlli**
      1. **Accesso** - Il consiglio di Microsoft è quello di **consentire l'accesso** e richiedere la **modifica della password**.
   1. **Applica criteri** - **su**
   1. **Salva:** questa azione consente di tornare alla pagina **Panoramica.**
1. Selezionare **Configura criteri di rischio di accesso**.
   1. In **Assegnazioni**
      1. **Utenti:** scegliere **Tutti gli utenti** o Seleziona individui e **gruppi** se si limita l'implementazione.
         1. Facoltativamente, è possibile scegliere di escludere gli utenti dal criterio.
      1. **Condizioni** - **di rischio di accesso** Consiglia a Microsoft è di impostare questa opzione su Medio e **superiore**.
   1. In **Controlli**
      1. **Accesso** - Si consiglia a Microsoft di **consentire l'accesso** e **richiedere l'autenticazione**a più fattori .
   1. **Applica criteri** - **su**
   1. **Salva**

## <a name="next-steps"></a>Passaggi successivi

- [Abilitare i criteri di registrazione di Azure Multi-Factor AuthenticationEnable Azure Multi-Factor Authentication registration policy](howto-identity-protection-configure-mfa-policy.md)

- [Che cosa sono i rischi?](concept-identity-protection-risks.md)

- [Analizzare i rilevamenti degli eventi di rischio](howto-identity-protection-investigate-risk.md)

- [Simulare rilevamenti degli eventi di rischio](howto-identity-protection-simulate-risk.md)
