---
title: Procedure consigliate e consigli di Azure Active Directory B2BAzure Active Directory B2B best practices and recommendations
description: Informazioni sulle procedure consigliate e sui consigli per l'accesso degli utenti guest business-to-business (B2B) in Azure Active Directory.Learn best practices and recommendations for business-to-business (B2B) guest user access in Azure Active Directory.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 12/18/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisol
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 54f5721ef606b6ea916f5a00031c58f5e2adeb0e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80050857"
---
# <a name="azure-active-directory-b2b-best-practices"></a>Procedure consigliate per Il B2B di Azure Active DirectoryAzure Active Directory B2B best practices
Questo articolo contiene consigli e procedure consigliate per la collaborazione business-to-business (B2B) in Azure Active Directory (Azure AD).

   > [!IMPORTANT]
   > **A partire dal 31 marzo 2021,** Microsoft non supporterà più il riscatto degli inviti creando account e tenant di Azure AD non gestiti per scenari di collaborazione B2B. In preparazione, incoraggiamo i clienti a scegliere [l'autenticazione monouso del passcode via e-mail.](one-time-passcode.md) Accogliamo con favore il tuo feedback su questa funzione di anteprima pubblica e siamo entusiasti di creare ancora più modi per collaborare.

## <a name="b2b-recommendations"></a>Raccomandazioni B2B
| Recommendation | Commenti |
| --- | --- |
| Per un'esperienza di accesso ottimale, eseguire la federazione con i provider di identitàFor an optimal sign-in experience, federate with identity providers | Quando possibile, eseguire la federazione diretta con i provider di identità per consentire agli utenti invitati di accedere alle app e alle risorse condivise senza dover creare account Microsoft (MSA) o Azure AD. Puoi utilizzare la [funzione di federazione di Google](google-federation.md) per consentire agli utenti guest B2B di accedere con i loro account Google. In alternativa, è possibile utilizzare la [funzionalità di federazione diretta (anteprima)](direct-federation.md) per configurare la federazione diretta con qualsiasi organizzazione il cui provider di identità (IdP) supporta il protocollo SAML 2.0 o WS-Fed. |
| Utilizzare la funzionalità Dividi una tantum (anteprima) via e-mail per gli ospiti B2B che non possono eseguire l'autenticazione con altri mezzi | La funzionalità di passcode monouso (anteprima) di posta elettronica autentica gli utenti guest B2B quando non possono essere autenticati tramite altri mezzi come Azure AD, un account Microsoft (MSA) o la federazione di Google.The [Email one-time passcode (preview)](one-time-passcode.md) feature authenticates B2B guest users when they can't be authenticated by other means like Azure AD, a Microsoft account (MSA) or Google federation. Quando l'utente guest riscatta un invito o accede a una risorsa condivisa, può richiedere un codice temporaneo, che viene inviato all'indirizzo di posta elettronica. Quindi immette tale codice per continuare ad accedere. |
| Aggiungere informazioni personalizzate distintive dell'azienda alla pagina di accesso | È possibile personalizzare la pagina di accesso in modo che sia più intuitiva per gli utenti guest B2B. Scopri come aggiungere il branding aziendale per accedere e le [pagine del Pannello di accesso](../fundamentals/customize-branding.md). |
| Aggiungere l'informativa sulla privacy all'esperienza di riscatto degli utenti guest B2B | Per continuare, è possibile aggiungere l'URL dell'informativa sulla privacy dell'organizzazione al processo di riscatto dell'invito al primo invito, in modo che un utente invitato acconsenta alle condizioni sulla privacy per continuare. Vedere Procedura: Aggiungere le informazioni sulla [privacy dell'organizzazione in Azure Active Directory.](https://aka.ms/adprivacystatement) |
| Utilizzare la funzionalità di invito in blocco (anteprima) per invitare più utenti guest B2B contemporaneamente | Invitare più utenti guest all'organizzazione contemporaneamente usando la funzionalità di invito all'anteprima in blocco nel portale di Azure.Invite multiple guest users to your organization at the same time by using the bulk invite preview feature in the Azure portal. Questa funzionalità consente di caricare un file CSV per creare utenti guest B2B e inviare inviti in blocco. Consultate [Esercitazione per l'invito in blocco di utenti B2B.](tutorial-bulk-invite.md) |
| Applicare criteri di accesso condizionale per Multi-Factor Authentication (MFA) | È consigliabile applicare i criteri di autenticazione a più fattori sulle app che si desidera condividere con gli utenti B2B partner. In questo modo, l'autenticazione a più fattori verrà applicata in modo coerente alle app nel tenant, indipendentemente dal fatto che l'organizzazione partner utilizzi l'autenticazione a più fattori. Vedere Accesso condizionale per gli utenti di [Collaborazione B2B.](conditional-access.md) |
| Se si impostano criteri di accesso condizionale basati su dispositivo, utilizzare gli elenchi di esclusione per consentire l'accesso agli utenti B2BIf you're enforcing device-based Conditional Access policies, use exclusion lists to allow access to B2B users | Se i criteri di accesso condizionale basati su dispositivo sono abilitati nell'organizzazione, i dispositivi degli utenti guest B2B verranno bloccati perché non sono gestiti dall'organizzazione. È possibile creare elenchi di esclusione contenenti utenti partner specifici per escluderli dai criteri di accesso condizionale basati su dispositivo. Vedere Accesso condizionale per gli utenti di [Collaborazione B2B.](conditional-access.md) |
| Usare un URL specifico del tenant quando si forniscono collegamenti diretti agli utenti guest B2BUse a tenant-specific URL when providing direct links to your B2B guest users | In alternativa all'e-mail di invito, puoi fornire a un ospite un collegamento diretto all'app o al portale. Questo collegamento diretto deve essere specifico del tenant, ovvero deve includere un ID tenant o un dominio verificato in modo che l'ospite possa essere autenticato nel tenant, in cui si trova l'app condivisa. Consultate [Esperienza di riscatto per l'utente ospite.](redemption-experience.md) |
| Quando si sviluppa un'app, usare UserType per determinare l'esperienza utente guestWhen developing an app, use UserType to determine guest user experience  | Se si sviluppa un'applicazione e si desidera fornire esperienze diverse per gli utenti tenant e gli utenti guest, usare la proprietà UserType.If you're developing an application and you want to provide different experiences for tenant users and guest users, use the UserType property. L'attestazione UserType non è attualmente inclusa nel token. Le applicazioni devono utilizzare l'API Microsoft Graph per eseguire una query nella directory per l'utente per ottenere il proprio UserType.Applications should use the Microsoft Graph API to query the directory for the user to get their UserType. |
| Modificare la proprietà UserType *solo* se la relazione dell'utente con l'organizzazione viene modificata | Sebbene sia possibile usare PowerShell per convertire la proprietà UserType per un utente da membro a ospite (e viceversa), è necessario modificare questa proprietà solo se la relazione dell'utente con l'organizzazione cambia. Vedere [Proprietà di un utente guest B2B](user-properties.md).|

## <a name="next-steps"></a>Passaggi successivi

[Gestire la condivisione B2B](delegate-invitations.md)
