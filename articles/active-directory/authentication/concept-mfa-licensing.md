---
title: Versioni e piani di consumo di Azure Multi-Factor AuthenticationAzure Multi-Factor Authentication versions and consumption plans
description: Informazioni sul client Azure Multi-Factor Authentication e sui diversi metodi e versioni disponibili.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 02/20/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: e74a7ab0c003aaf9d90211484b39f8322cd9c329
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77648003"
---
# <a name="features-and-licenses-for-azure-multi-factor-authentication"></a>Funzionalità e licenze per Azure Multi-Factor Authentication

Per proteggere gli account utente nell'organizzazione, è necessario utilizzare l'autenticazione a più fattori. Questa funzionalità è importante soprattutto per gli account dotati di accesso con privilegi alle risorse. Le funzionalità di autenticazione a più fattori di base sono disponibili per gli amministratori di Office 365 e Azure Active Directory (Azure AD) senza costi aggiuntivi. Se si desidera aggiornare le funzionalità per gli amministratori o estendere l'autenticazione a più fattori agli altri utenti, è possibile acquistare Azure Multi-Factor Authentication in diversi modi.

> [!IMPORTANT]
> Questo articolo descrive in dettaglio i diversi modi in cui Azure Multi-Factor Authentication può essere concesso in licenza e da usare. Per informazioni dettagliate sui prezzi e la fatturazione, vedere la pagina dei prezzi di [Azure Multi-Factor Authentication.](https://azure.microsoft.com/pricing/details/multi-factor-authentication/)

## <a name="available-versions-of-azure-multi-factor-authentication"></a>Versioni disponibili di Azure Multi-Factor Authentication

Azure Multi-Factor Authentication può essere usato e concesso in licenza in diversi modi a seconda delle esigenze dell'organizzazione. È possibile che si abbia già il diritto di usare Azure Multi-Factor Authentication a seconda della licenza di Azure AD, Office 365, EMS o Microsoft 365 attualmente disponibile. Nella tabella seguente vengono descritti in dettaglio i diversi modi per ottenere Azure Multi-Factor Authentication e alcune delle funzionalità e dei casi d'uso per ognuno.

| Se sei un utente di | Funzionalità e casi d'uso |
| --- | --- |
| EMS o Microsoft 365 E3 ed E5 | EMS E3 o Microsoft 365 E3 (che include EMS e Office 365), include Azure AD Premium P1. EMS E5 o Microsoft 365 E5 include Azure AD Premium P2. È possibile utilizzare le stesse funzionalità di accesso condizionale indicate nelle sezioni seguenti per fornire l'autenticazione a più fattori agli utenti. |
| Azure AD Premium P1 | È possibile usare [l'accesso condizionale](../conditional-access/overview.md) di Azure AD per richiedere agli utenti l'autenticazione a più fattori durante determinati scenari o eventi in base ai requisiti aziendali. |
| Azure AD Premium P2 | Fornisce la posizione di sicurezza più forte e una migliore esperienza utente. Aggiunge [l'accesso condizionale basato sul rischio](../conditional-access/howto-conditional-access-policy-risk.md) alle funzionalità di Azure AD Premium P1 che si adatta ai modelli degli utenti e riduce al minimo le richieste di autenticazione a più fattori. |
| Office 365 Business Premium, E3 o E5 | Azure Multi-Factor Authentication è abilitato o disabilitato per tutti gli utenti per tutti gli eventi di accesso. Non è possibile abilitare l'autenticazione a più fattori solo per un sottoinsieme di utenti o solo in determinati scenari. La gestione è tramite il portale di Office 365. Per un'esperienza utente migliorata, eseguire l'aggiornamento ad Azure AD Premium P1 o P2 e usare l'accesso condizionale. Per ulteriori informazioni, vedere [proteggere le risorse di Office 365 con l'autenticazione a più fattori](https://support.office.com/article/Set-up-multi-factor-authentication-for-Office-365-users-8f0454b2-f51a-4d9c-bcde-2c48e41621c6). |
| Azure AD Gratuito | È possibile utilizzare [le impostazioni predefinite](../fundamentals/concept-fundamentals-security-defaults.md) di sicurezza per abilitare l'autenticazione a più fattori per tutti gli utenti, ogni volta che viene effettuata una richiesta di autenticazione. Non si dispone di un controllo granulare degli utenti o degli scenari abilitati, ma fornisce un passaggio di sicurezza aggiuntivo.<br /> Anche quando le impostazioni predefinite di sicurezza non vengono usate per abilitare l'autenticazione a più fattori per tutti gli utenti, gli utenti assegnati al ruolo amministratore globale di *Azure AD* possono essere configurati per l'uso dell'autenticazione a più fattori. Questa funzionalità del livello gratuito assicura che gli account amministratore critici siano protetti dall'autenticazione a più fattori. |

## <a name="feature-comparison-of-versions"></a>Confronto tra le funzionalità delle versioni

La tabella seguente indica un elenco delle funzionalità disponibili nelle varie versioni di Azure Multi-Factor Authentication. Pianificare le esigenze per la protezione dell'autenticazione utente, quindi determinare quale approccio soddisfa tali requisiti. Ad esempio, anche se Azure AD Free fornisce impostazioni predefinite di sicurezza che forniscono Azure Multi-Factor Authentication, solo l'app di autenticazione mobile può essere usata per la richiesta di autenticazione, non per una chiamata telefonica o SMS. Questo approccio può essere una limitazione se non è possibile garantire che l'app di autenticazione per dispositivi mobili sia installata nel dispositivo personale di un utente.

| Funzionalità | Azure AD Free - Impostazioni predefinite di sicurezzaAzure AD Free - Security defaults | Azure AD Free - Azure AD Global Administrators | Office 365 Business Premium, E3 o E5 | Azure AD Premium P1 o P2 |
| --- |:---:|:---:|:---:|:---:|
| Proteggere gli account di amministratore tenant di Azure AD con l'autenticazione a più fattoriProtect Azure AD tenant admin accounts with MFA | ● | ( solo account amministratore globale di*Azure AD)* | ● | ● |
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

> [!IMPORTANT]
> A partire da marzo 2019, le opzioni di chiamata telefonica non sono più disponibili per gli utenti di Azure Multi-Factor Authentication e Azure Self-Service Password Reset nei tenant di valutazione/ Azure AD. I messaggi SMS non sono interessati da questa modifica. Le chiamate telefoniche continuano a essere disponibili per gli utenti nei tenant di Azure AD Premium P1 o P2 o usano Office 365 Business Premium, E3 o E5.

## <a name="purchase-and-enable-azure-multi-factor-authentication"></a>Acquistare e abilitare Azure Multi-Factor Authentication

Per usare Azure Multi-Factor Authentication, registrarsi o acquistare un livello di Azure AD idoneo. Azure AD è disponibile in quattro edizioni: edizione gratuita per le app di Office 365 (per i clienti di Office 365 Business Premium E3 o E5), Premium P1 e Premium P2.

L'edizione gratuita è inclusa in una sottoscrizione di Azure.The Free edition is included with an Azure subscription. Vedere la [sezione seguente](#azure-ad-free-tier) per informazioni su come usare le impostazioni predefinite di sicurezza o proteggere gli account con il ruolo amministratore globale di *Azure AD.*

Le edizioni Premium di Azure AD sono disponibili tramite il rappresentante Microsoft, il programma [Open Volume License Program](https://www.microsoft.com/licensing/licensing-programs/open-license.aspx)e il programma Cloud Solution [Providers](https://go.microsoft.com/fwlink/?LinkId=614968&clcid=0x409). I sottoscrittori di Azure e Office 365 possono acquistare Azure Active Directory Premium P1 e P2 anche online. [Accedi](https://portal.office.com/Commerce/Catalog.aspx) per acquistare.

> [!IMPORTANT]
> Le licenze basate sul consumo non sono più disponibili per i nuovi clienti a partire dal 1 settembre 2018. I clienti esistenti che utilizzano il modello basato sul consumo possono continuare a usare per utente abilitato o per fatturazione tramite autenticazione.

Dopo aver acquistato il livello Azure AD richiesto, [pianificare e distribuire Azure Multi-Factor Authentication.](howto-mfa-getstarted.md)

### <a name="azure-ad-free-tier"></a>Livello Gratuito di Azure ADAzure AD Free tier

Tutti gli utenti in un tenant gratuito di Azure AD possono usare l'autenticazione a più fattori di Azure tramite l'uso delle impostazioni predefinite di sicurezza. Queste impostazioni predefinite di sicurezza abilitano l'autenticazione a più fattori di Azure per tutti gli utenti, ogni volta che accedono. L'app di autenticazione mobile è l'unico metodo che può essere usato per Azure Multi-Factor Authentication quando si usano le impostazioni predefinite di sicurezza gratuito di Azure AD.

* [Altre informazioni sulle impostazioni predefinite di sicurezza di Azure ADLearn more about Azure AD security defaults](../fundamentals/concept-fundamentals-security-defaults.md)
* [Abilitare le impostazioni predefinite di sicurezza per gli utenti in Azure AD FreeEnable security defaults for users in Azure AD Free](../fundamentals/concept-fundamentals-security-defaults.md#enabling-security-defaults)

Se non si vuole abilitare Azure Multi-Factor Authentication per tutti gli utenti e ogni evento di accesso, è invece possibile scegliere di proteggere gli account utente solo con il ruolo amministratore globale di *Azure AD.* Questo approccio fornisce richieste di autenticazione aggiuntive per gli account amministratore critici. Si abilita Azure Multi-Factor Authentication in uno dei modi seguenti, a seconda del tipo di account usato:

* Se si utilizza un account Microsoft, [registrarsi per l'autenticazione a più fattori](https://support.microsoft.com/help/12408/microsoft-account-about-two-step-verification).
* Se non si usa un account Microsoft, [attivare l'autenticazione a più fattori per un utente o un gruppo in Azure AD.](howto-mfa-userstates.md)

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sui costi, vedere Prezzi di Azure Multi-Factor Authentication .For more information on costs, see [Azure Multi-Factor Authentication pricing](https://azure.microsoft.com/pricing/details/multi-factor-authentication/).
