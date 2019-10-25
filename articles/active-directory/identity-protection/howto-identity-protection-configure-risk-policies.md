---
title: Come configurare e abilitare i criteri di rischio in Azure Active Directory Identity Protection
description: Abilitare e configurare i criteri di rischio in Azure Active Directory Identity Protection
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
ms.openlocfilehash: 79f919633f6b1912ef07b7ff636eb60fb3d5859f
ms.sourcegitcommit: 7efb2a638153c22c93a5053c3c6db8b15d072949
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/24/2019
ms.locfileid: "72886961"
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

Tutti i criteri consentono di escludere gli utenti, ad esempio l' [accesso di emergenza o gli account amministratore break-Glass](../users-groups-roles/directory-emergency-access.md). Le organizzazioni possono determinare che devono escludere altri account da criteri specifici in base al modo in cui vengono usati gli account. Tutte le esclusioni devono essere esaminate regolarmente per verificare se sono ancora applicabili.

## <a name="enable-policies"></a>Abilita criteri

Per abilitare il rischio utente e i criteri di rischio di accesso, completare i passaggi seguenti.

1. Passare al [portale di Azure](https://portal.azure.com).
1. Passare a **Azure Active Directory** > **sicurezza** > **Identity Protection** > **Panoramica**.
1. Selezionare **Configura criteri di rischio utente**.
   1. In **assegnazioni**
      1. **Utenti** : scegliere **tutti gli utenti** o **selezionare singoli utenti e gruppi** in caso di limitazione dell'implementazione.
         1. Facoltativamente, è possibile scegliere di escludere gli utenti dai criteri.
      1. **Condizioni** - la raccomandazione di Microsoft per il **rischio utente** è quella di impostare questa opzione su **alta**.
   1. Sotto **controlli**
      1. **Accesso** : Microsoft consiglia di **consentire l'accesso** e **richiedere la modifica della password**.
   1. **Imponi - criteri** **in**
   1. **Salva** : questa azione consente di tornare alla pagina **Panoramica** .
1. Selezionare **Configura criteri di rischio di accesso**.
   1. In **assegnazioni**
      1. **Utenti** : scegliere **tutti gli utenti** o **selezionare singoli utenti e gruppi** in caso di limitazione dell'implementazione.
         1. Facoltativamente, è possibile scegliere di escludere gli utenti dai criteri.
      1. **Condizioni** - la raccomandazione Microsoft per il **rischio di accesso** è quella di impostare questa opzione su **media e versioni successive**.
   1. Sotto **controlli**
      1. **Accesso** : Microsoft consiglia di **consentire l'accesso** e **richiedere l'autenticazione**a più fattori.
   1. **Imponi - criteri** **in**
   1. **Salva**

## <a name="next-steps"></a>Passaggi successivi

- [Abilitare i criteri di registrazione di Azure Multi-Factor Authentication](howto-identity-protection-configure-mfa-policy.md)

- [Che cos'è il rischio](concept-identity-protection-risks.md)

- [Esaminare i rilevamenti di rischio](howto-identity-protection-investigate-risk.md)

- [Simulare i rilevamenti di rischio](howto-identity-protection-simulate-risk.md)
