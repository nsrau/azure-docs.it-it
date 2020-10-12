---
title: Versioni di Azure Multi-Factor Authentication e piani di utilizzo
description: Informazioni sul client Azure Multi-Factor Authentication e sui diversi metodi e versioni disponibili.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 06/15/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 829a82cb94e5c2a7e7cbc9190047c368299612cd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90052750"
---
# <a name="features-and-licenses-for-azure-multi-factor-authentication"></a>Funzionalità e licenze per Azure Multi-Factor Authentication

Per proteggere gli account utente nell'organizzazione, è consigliabile usare l'autenticazione a più fattori. Questa funzionalità è importante soprattutto per gli account dotati di accesso con privilegi alle risorse. Le funzionalità di base di autenticazione a più fattori sono disponibili per Microsoft 365 e amministratori di Azure Active Directory (Azure AD) senza costi aggiuntivi. Per aggiornare le funzionalità per gli amministratori o estendere l'autenticazione a più fattori agli altri utenti, è possibile acquistare Azure Multi-Factor Authentication in molti modi.

> [!IMPORTANT]
> Questo articolo illustra i diversi modi in cui è possibile concedere in licenza e usare Azure Multi-Factor Authentication. Per dettagli specifici sui prezzi e sulla fatturazione, vedere la [pagina dei prezzi di Multi-Factor Authentication](https://azure.microsoft.com/pricing/details/multi-factor-authentication/).

## <a name="available-versions-of-azure-multi-factor-authentication"></a>Versioni disponibili di Azure Multi-Factor Authentication

Azure Multi-Factor Authentication può essere usato e concesso in licenza in diversi modi, a seconda delle esigenze dell'organizzazione. È possibile che si abbia già diritto a usare Azure Multi-Factor Authentication a seconda della licenza di Azure AD, EMS o Microsoft 365 attualmente disponibile. La tabella seguente illustra in dettaglio i diversi modi per ottenere Azure Multi-Factor Authentication e alcune delle funzionalità e dei casi d'uso per ognuno di essi.

| Utenti di | Capacità e casi d'uso |
| --- | --- |
| Microsoft 365 Business Premium e EMS o Microsoft 365 E3 e E5 | EMS E3, Microsoft 365 E3 e Microsoft 365 Business Premium includono Azure AD Premium P1. EMS E5 o Microsoft 365 E5 include Azure AD Premium P2. È possibile usare le stesse funzionalità di accesso condizionale indicate nelle sezioni seguenti per fornire agli utenti l'autenticazione a più fattori. |
| Azure AD Premium P1 | È possibile usare l'[Accesso condizionale di Azure AD](../conditional-access/howto-conditional-access-policy-all-users-mfa.md) per richiedere agli utenti di eseguire l'autenticazione a più fattori durante determinati scenari o eventi in base alle esigenze aziendali. |
| Azure AD Premium P2 | Offre la posizione di sicurezza più avanzata e l'esperienza utente migliorata. Aggiunge l'[accesso condizionale basato sul rischio](../conditional-access/howto-conditional-access-policy-risk.md) alle funzionalità di Azure AD Premium P1 che si adattano ai modelli dell'utente e riduce al minimo le richieste di autenticazione a più fattori. |
| Tutti i piani di Microsoft 365 | Azure Multi-Factor Authentication può essere [abilitato](howto-mfa-userstates.md)per ogni utente o abilitato o disabilitato per tutti gli utenti che usano le [impostazioni predefinite di sicurezza](../fundamentals/concept-fundamentals-security-defaults.md). La gestione del Multi-Factor Authentication di Azure è tramite il portale di Microsoft 365. Per migliorare l'esperienza utente, eseguire l'aggiornamento ad Azure AD Premium P1 o P2 e usare l'accesso condizionale. Per altre informazioni, vedere [proteggere le risorse di Microsoft 365 con l'autenticazione a più fattori](/microsoft-365/admin/security-and-compliance/set-up-multi-factor-authentication). |
| Azure AD Gratuito | Per abilitare l'autenticazione a più fattori per tutti gli utenti, è possibile usare le [impostazioni predefinite di sicurezza](../fundamentals/concept-fundamentals-security-defaults.md) . Non si dispone di un controllo granulare degli utenti o degli scenari abilitati, ma viene fornito un ulteriore passaggio di sicurezza.<br /> Anche quando le impostazioni predefinite di sicurezza non vengono usate per abilitare l'autenticazione a più fattori per tutti, è possibile configurare gli utenti assegnati al ruolo di *amministratore globale per Azure AD* per l'uso dell'autenticazione a più fattori. Questa funzionalità del livello gratuito garantisce che gli account amministratore critici siano protetti dall'autenticazione a più fattori. |

## <a name="feature-comparison-of-versions"></a>Confronto tra le funzionalità delle versioni

La tabella seguente indica un elenco delle funzionalità disponibili nelle varie versioni di Azure Multi-Factor Authentication. Pianificare le esigenze di protezione dell'autenticazione utente, quindi determinare quale approccio soddisfa tali requisiti. Ad esempio, sebbene Azure AD Free offra impostazioni predefinite di sicurezza che forniscono Azure Multi-Factor Authentication, è possibile usare solo l'app per dispositivi mobili Microsoft Authenticator per la richiesta di autenticazione e non una telefonata o un SMS. Questo approccio può costituire una limitazione se non è possibile verificare che l'app di autenticazione per dispositivi mobili sia installata nel dispositivo personale di un utente.

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

## <a name="purchase-and-enable-azure-multi-factor-authentication"></a>Acquistare e abilitare Azure Multi-Factor Authentication

Per usare Azure Multi-Factor Authentication, effettuare la registrazione o acquistare un livello di Azure AD idoneo. Azure AD è disponibile in quattro edizioni: gratuita, Microsoft 365 app, Premium P1 e Premium P2.

L'edizione Free è inclusa in una sottoscrizione Azure. Vedere la [sezione riportata di seguito](#azure-ad-free-tier) per informazioni su come usare le impostazioni predefinite di sicurezza o proteggere gli account con il ruolo di *amministratore globale di Azure AD*.

Le edizioni di Azure AD Premium sono disponibili tramite il rappresentante Microsoft, il [programma di licenza Open Volume](https://www.microsoft.com/licensing/licensing-programs/open-license.aspx) e il [programma Cloud Solution Provider](https://go.microsoft.com/fwlink/?LinkId=614968&clcid=0x409). I sottoscrittori di Azure e Microsoft 365 possono acquistare Azure Active Directory Premium P1 e P2 anche online. [Accedere](https://portal.office.com/Commerce/Catalog.aspx) per acquistare.

Dopo aver acquistato il livello di Azure AD necessario, [pianificare e distribuire Azure Multi-Factor Authentication](howto-mfa-getstarted.md).

### <a name="azure-ad-free-tier"></a>Livello Azure AD Free

Tutti gli utenti di un tenant gratuito di Azure AD possono usare Azure Multi-Factor Authentication tramite le impostazioni predefinite di sicurezza. L'app di autenticazione mobile è l'unico metodo che può essere usato per Azure Multi-Factor Authentication quando si usano le impostazioni predefinite di sicurezza di Azure AD Free.

* [Altre informazioni sulle impostazioni predefinite per la sicurezza di Azure AD](../fundamentals/concept-fundamentals-security-defaults.md)
* [Abilitare le impostazioni predefinite per la sicurezza per gli utenti in Azure AD Free](../fundamentals/concept-fundamentals-security-defaults.md#enabling-security-defaults)

Se non si vuole abilitare Multi-Factor Authentication di Azure per tutti gli utenti, è invece possibile scegliere di proteggere solo gli account utente con il ruolo di *amministratore di Azure ad globale* . Questo approccio fornisce richieste di autenticazione aggiuntive per gli account amministratore critici. Per abilitare Azure Multi-Factor Authentication è possibile usare uno dei modi seguenti, a seconda del tipo di account usato:

* Se si usa un account Microsoft, [registrarsi per l'autenticazione a più fattori](https://support.microsoft.com/help/12408/microsoft-account-about-two-step-verification).
* Se non si usa un account Microsoft, [attivare l'autenticazione a più fattori per un utente o un gruppo in Azure AD](howto-mfa-userstates.md).

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni sui costi, vedere [Prezzi di Azure Multi-Factor Authentication](https://azure.microsoft.com/pricing/details/multi-factor-authentication/).
* [Informazioni sull'accesso condizionale](../conditional-access/overview.md)

