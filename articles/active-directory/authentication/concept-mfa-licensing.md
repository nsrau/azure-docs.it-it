---
title: Azure AD Multi-Factor Authentication versioni e piani a consumo
description: Informazioni sulle Azure AD client Multi-Factor Authentication e sui diversi metodi e versioni disponibili.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 06/15/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 204a656194af9473defc8cf983526dd2b8266021
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/18/2020
ms.locfileid: "94839812"
---
# <a name="features-and-licenses-for-azure-ad-multi-factor-authentication"></a>Funzionalità e licenze per Azure AD Multi-Factor Authentication

Per proteggere gli account utente nell'organizzazione, è consigliabile usare l'autenticazione a più fattori. Questa funzionalità è importante soprattutto per gli account dotati di accesso con privilegi alle risorse. Le funzionalità di base di autenticazione a più fattori sono disponibili per Microsoft 365 e amministratori di Azure Active Directory (Azure AD) senza costi aggiuntivi. Se si desidera aggiornare le funzionalità per gli amministratori o estendere l'autenticazione a più fattori al resto degli utenti, è possibile acquistare Azure AD Multi-Factor Authentication in diversi modi.

> [!IMPORTANT]
> Questo articolo illustra in dettaglio i diversi modi in cui Azure AD Multi-Factor Authentication possono essere concessi in licenza e usati. Per dettagli specifici su prezzi e fatturazione, vedere la [pagina relativa ai prezzi di Azure AD multi-factor authentication](https://azure.microsoft.com/pricing/details/multi-factor-authentication/).

## <a name="available-versions-of-azure-ad-multi-factor-authentication"></a>Versioni disponibili di Azure AD Multi-Factor Authentication

Azure AD Multi-Factor Authentication possono essere usati e concessi in licenza in diversi modi, a seconda delle esigenze dell'organizzazione. È possibile che si abbia già diritto a usare Azure AD Multi-Factor Authentication a seconda della licenza Azure AD, EMS o Microsoft 365 attualmente disponibile. La tabella seguente illustra in dettaglio i diversi modi per ottenere Azure AD Multi-Factor Authentication e alcune delle funzionalità e i casi d'uso per ognuno di essi.

| Utenti di | Capacità e casi d'uso |
| --- | --- |
| Microsoft 365 Business Premium e EMS o Microsoft 365 E3 e E5 | EMS E3, Microsoft 365 E3 e Microsoft 365 Business Premium includono Azure AD Premium P1. EMS E5 o Microsoft 365 E5 include Azure AD Premium P2. È possibile usare le stesse funzionalità di accesso condizionale indicate nelle sezioni seguenti per fornire agli utenti l'autenticazione a più fattori. |
| Azure AD Premium P1 | È possibile usare l'[Accesso condizionale di Azure AD](../conditional-access/howto-conditional-access-policy-all-users-mfa.md) per richiedere agli utenti di eseguire l'autenticazione a più fattori durante determinati scenari o eventi in base alle esigenze aziendali. |
| Azure AD Premium P2 | Offre la posizione di sicurezza più avanzata e l'esperienza utente migliorata. Aggiunge l'[accesso condizionale basato sul rischio](../conditional-access/howto-conditional-access-policy-risk.md) alle funzionalità di Azure AD Premium P1 che si adattano ai modelli dell'utente e riduce al minimo le richieste di autenticazione a più fattori. |
| Tutti i piani di Microsoft 365 | Azure AD Multi-Factor Authentication può essere [abilitata](howto-mfa-userstates.md)per ogni utente o abilitata o disabilitata per tutti gli utenti che usano le [impostazioni predefinite di sicurezza](../fundamentals/concept-fundamentals-security-defaults.md). La gestione di Azure AD Multi-Factor Authentication viene tramite il portale di Microsoft 365. Per migliorare l'esperienza utente, eseguire l'aggiornamento ad Azure AD Premium P1 o P2 e usare l'accesso condizionale. Per altre informazioni, vedere [proteggere le risorse di Microsoft 365 con l'autenticazione a più fattori](/microsoft-365/admin/security-and-compliance/set-up-multi-factor-authentication). |
| Azure AD Gratuito | Per abilitare l'autenticazione a più fattori per tutti gli utenti, è possibile usare le [impostazioni predefinite di sicurezza](../fundamentals/concept-fundamentals-security-defaults.md) . Non si dispone di un controllo granulare degli utenti o degli scenari abilitati, ma viene fornito un ulteriore passaggio di sicurezza.<br /> Anche quando le impostazioni predefinite di sicurezza non vengono usate per abilitare l'autenticazione a più fattori per tutti, è possibile configurare gli utenti assegnati al ruolo di *amministratore globale per Azure AD* per l'uso dell'autenticazione a più fattori. Questa funzionalità del livello gratuito garantisce che gli account amministratore critici siano protetti dall'autenticazione a più fattori. |

## <a name="feature-comparison-of-versions"></a>Confronto tra le funzionalità delle versioni

Nella tabella seguente viene fornito un elenco delle funzionalità disponibili nelle varie versioni di Azure AD Multi-Factor Authentication. Pianificare le esigenze di protezione dell'autenticazione utente, quindi determinare quale approccio soddisfa tali requisiti. Ad esempio, anche se Azure AD Free fornisce impostazioni predefinite di sicurezza che forniscono Azure AD Multi-Factor Authentication, è possibile usare solo l'app Mobile Authenticator per la richiesta di autenticazione, non una telefonata o un SMS. Questo approccio può costituire una limitazione se non è possibile verificare che l'app di autenticazione per dispositivi mobili sia installata nel dispositivo personale di un utente.

| Funzionalità | Azure AD Free - Impostazioni predefinite per la sicurezza | Azure AD Free - Amministratori globali di Azure AD | App di Microsoft 365 | Azure AD Premium P1 o P2 |
| --- |:---:|:---:|:---:|:---:|
| Protezione degli account amministratore tenant di Azure AD con MFA | ● | ● (solo per l'account *amministratore globale di Azure AD*) | ● | ● |
| App per dispositivi mobili come secondo fattore | ● | ● | ● | ● |
| Chiamata telefonica come secondo fattore | | ● | ● | ● |
| SMS come secondo fattore | | ● | ● | ● |
| Controllo amministrativo sui metodi di verifica | | ● | ● | ● |
| Avviso di illecito | | | | ● |
| Report MFA | | | | ● |
| Messaggi di saluto personalizzati per le telefonate | | | | ● |
| ID chiamante personalizzato per le telefonate | | | | ● |
| Indirizzi IP attendibili | | | | ● |
| Memorizzazione di MFA per dispositivi attendibili | | ● | ● | ● |
| MFA per applicazioni locali | | | | ● |

## <a name="purchase-and-enable-azure-ad-multi-factor-authentication"></a>Acquistare e abilitare Azure AD Multi-Factor Authentication

Per utilizzare Azure AD Multi-Factor Authentication, effettuare la registrazione o acquistare un livello Azure AD idoneo. Azure AD è disponibile in quattro edizioni: gratuita, Microsoft 365 app, Premium P1 e Premium P2.

L'edizione Free è inclusa in una sottoscrizione Azure. Vedere la [sezione riportata di seguito](#azure-ad-free-tier) per informazioni su come usare le impostazioni predefinite di sicurezza o proteggere gli account con il ruolo di *amministratore globale di Azure AD*.

Le edizioni di Azure AD Premium sono disponibili tramite il rappresentante Microsoft, il [programma di licenza Open Volume](https://www.microsoft.com/licensing/licensing-programs/open-license.aspx) e il [programma Cloud Solution Provider](https://go.microsoft.com/fwlink/?LinkId=614968&clcid=0x409). I sottoscrittori di Azure e Microsoft 365 possono acquistare Azure Active Directory Premium P1 e P2 anche online. [Accedere](https://portal.office.com/Commerce/Catalog.aspx) per acquistare.

Dopo aver acquistato il livello Azure AD necessario, [pianificare e distribuire Azure AD multi-factor authentication](howto-mfa-getstarted.md).

### <a name="azure-ad-free-tier"></a>Livello Azure AD Free

Tutti gli utenti in un tenant di Azure AD Free possono usare Azure AD Multi-Factor Authentication usando le impostazioni predefinite di sicurezza. L'app di autenticazione mobile è l'unico metodo che può essere usato per Azure AD Multi-Factor Authentication quando si usano i valori predefiniti di sicurezza Azure AD Free.

* [Altre informazioni sulle impostazioni predefinite per la sicurezza di Azure AD](../fundamentals/concept-fundamentals-security-defaults.md)
* [Abilitare le impostazioni predefinite per la sicurezza per gli utenti in Azure AD Free](../fundamentals/concept-fundamentals-security-defaults.md#enabling-security-defaults)

Se non si vuole abilitare Azure AD Multi-Factor Authentication per tutti gli utenti, è invece possibile scegliere di proteggere solo gli account utente con il ruolo di *amministratore Azure ad globale* . Questo approccio fornisce richieste di autenticazione aggiuntive per gli account amministratore critici. È possibile abilitare Azure AD Multi-Factor Authentication in uno dei modi seguenti, a seconda del tipo di account in uso:

* Se si usa un account Microsoft, [registrarsi per l'autenticazione a più fattori](https://support.microsoft.com/help/12408/microsoft-account-about-two-step-verification).
* Se non si usa un account Microsoft, [attivare l'autenticazione a più fattori per un utente o un gruppo in Azure AD](howto-mfa-userstates.md).

## <a name="next-steps"></a>Passaggi successivi

* Per ulteriori informazioni sui costi, vedere [Azure AD multi-factor authentication prezzi](https://azure.microsoft.com/pricing/details/multi-factor-authentication/).
* [Informazioni sull'accesso condizionale](../conditional-access/overview.md)

