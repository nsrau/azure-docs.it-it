---
title: Criteri di rischio-Azure Active Directory Identity Protection
description: Abilitare e configurare i criteri di rischio in Azure Active Directory Identity Protection
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: how-to
ms.date: 06/05/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 49f3e082969b7483601088cd976d8cc30d500017
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/22/2020
ms.locfileid: "92367466"
---
# <a name="how-to-configure-and-enable-risk-policies"></a>Procedura: configurare e abilitare i criteri di rischio

Come illustrato nell'articolo precedente, i [criteri di protezione delle identità](concept-identity-protection-policies.md) hanno due criteri di rischio che è possibile abilitare nella directory. 

- Criteri di rischio di accesso
- Criteri di rischio utente

![Pagina Panoramica sicurezza per abilitare i criteri di rischio di accesso e utente](./media/howto-identity-protection-configure-risk-policies/identity-protection-security-overview.png)

Entrambi i criteri funzionano per automatizzare la risposta ai rilevamenti dei rischi nell'ambiente e consentire agli utenti di eseguire autonomamente la correzione quando viene rilevato il rischio. 

> [!VIDEO https://www.youtube.com/embed/zEsbbik-BTE]

## <a name="prerequisites"></a>Prerequisiti 

Se l'organizzazione desidera consentire agli utenti di eseguire autonomamente la correzione quando vengono rilevati rischi, gli utenti devono essere registrati per la reimpostazione della password self-service e per Azure Multi-Factor Authentication. Per un'esperienza ottimale, è consigliabile [abilitare l'esperienza di registrazione delle informazioni di sicurezza combinata](../authentication/howto-registration-mfa-sspr-combined.md) . Consentire agli utenti di eseguire autonomamente il monitoraggio e l'aggiornamento a uno stato produttivo più rapidamente senza richiedere l'intervento dell'amministratore. Gli amministratori possono comunque visualizzare questi eventi ed esaminarli dopo il fatto. 

## <a name="choosing-acceptable-risk-levels"></a>Scelta di livelli di rischio accettabili

È necessario che le organizzazioni decidano il livello di rischio di essere disposti ad accettare il bilanciamento dell'esperienza utente e del comportamento di sicurezza. 

Microsoft consiglia di impostare la soglia dei criteri di rischio utente su **alta** e i criteri di rischio di accesso su **medium e versioni successive**.

La scelta di una soglia **alta** riduce la frequenza di attivazione dei criteri e riduce al minimo l'impatto sugli utenti. Tuttavia, esclude i rilevamenti di rischio **basso** e **medio** dai criteri, che potrebbero non impedire a un utente malintenzionato di sfruttare un'identità compromessa. La selezione di una soglia **bassa** introduce interrupt utente aggiuntivi, ma un miglioramento del comportamento di sicurezza.

## <a name="exclusions"></a>Esclusioni

Tutti i criteri consentono di escludere gli utenti, ad esempio gli [account di accesso di emergenza o gli account amministratore critici](../roles/security-emergency-access.md). Le organizzazioni possono determinare che devono escludere altri account da criteri specifici in base al modo in cui vengono usati gli account. Tutte le esclusioni devono essere esaminate regolarmente per verificare se sono ancora applicabili.

I [percorsi di rete](../conditional-access/location-condition.md) attendibili configurati vengono usati da Identity Protection in alcuni rilevamenti dei rischi per ridurre i falsi positivi.

## <a name="enable-policies"></a>Abilita criteri

Per abilitare il rischio utente e i criteri di rischio di accesso, completare i passaggi seguenti.

1. Passare al [portale di Azure](https://portal.azure.com).
1. Passare a **Azure Active Directory**  >  Panoramica di**Security**  >  **Identity Protection**  >  **Overview**.
1. Selezionare **criteri di rischio utente**.
   1. In **assegnazioni**
      1. **Utenti** : scegliere **tutti gli utenti** o **selezionare singoli utenti e gruppi** in caso di limitazione dell'implementazione.
         1. Facoltativamente, è possibile scegliere di escludere gli utenti dai criteri.
      1. **Condizioni**  -  di **Rischio utente** Microsoft consiglia di impostare questa opzione su **High**.
   1. Sotto **controlli**
      1. **Accesso** : Microsoft consiglia di **consentire l'accesso** e **richiedere la modifica della password**.
   1. **Imponi criteri**  -  **Il**
   1. **Salva** : questa azione consente di tornare alla pagina **Panoramica** .
1. Selezionare i **criteri di rischio di accesso**.
   1. In **assegnazioni**
      1. **Utenti** : scegliere **tutti gli utenti** o **selezionare singoli utenti e gruppi** in caso di limitazione dell'implementazione.
         1. Facoltativamente, è possibile scegliere di escludere gli utenti dai criteri.
      1. **Condizioni**  -  di **Rischio di accesso** Microsoft consiglia di impostare questa opzione su **media e versioni successive**.
   1. Sotto **controlli**
      1. **Accesso** : Microsoft consiglia di **consentire l'accesso** e **richiedere l'autenticazione**a più fattori.
   1. **Imponi criteri**  -  **Il**
   1. **Salva**

## <a name="next-steps"></a>Passaggi successivi

- [Abilitare i criteri di registrazione per l'autenticazione a più fattori di Azure](howto-identity-protection-configure-mfa-policy.md)

- [Che cosa sono i rischi?](concept-identity-protection-risks.md)

- [Analizzare i rilevamenti degli eventi di rischio](howto-identity-protection-investigate-risk.md)

- [Simulare rilevamenti degli eventi di rischio](howto-identity-protection-simulate-risk.md)
