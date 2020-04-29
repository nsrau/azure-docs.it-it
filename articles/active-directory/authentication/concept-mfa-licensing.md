---
title: Versioni di Azure Multi-Factor Authentication e piani di utilizzo
description: Informazioni sul client Azure a più fattori e sui diversi metodi e versioni disponibili.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77648003"
---
# <a name="features-and-licenses-for-azure-multi-factor-authentication"></a>Funzionalità e licenze per Azure Multi-Factor Authentication

Per proteggere gli account utente nell'organizzazione, è consigliabile usare l'autenticazione a più fattori. Questa funzionalità è importante soprattutto per gli account dotati di accesso con privilegi alle risorse. Le funzionalità di base di autenticazione a più fattori sono disponibili per gli amministratori di Office 365 e Azure Active Directory (Azure AD) senza costi aggiuntivi. Se si vuole aggiornare le funzionalità per gli amministratori o estendere l'autenticazione a più fattori al resto degli utenti, è possibile acquistare Multi-Factor Authentication di Azure in diversi modi.

> [!IMPORTANT]
> Questo articolo illustra i diversi modi in cui è possibile concedere in licenza e usare Azure Multi-Factor Authentication. Per dettagli specifici su prezzi e fatturazione, vedere la [pagina dei prezzi di Azure multi-factor authentication](https://azure.microsoft.com/pricing/details/multi-factor-authentication/).

## <a name="available-versions-of-azure-multi-factor-authentication"></a>Versioni disponibili di Azure Multi-Factor Authentication

Azure Multi-Factor Authentication può essere usato e concesso in licenza in diversi modi, a seconda delle esigenze dell'organizzazione. È possibile che si abbia già diritto a usare Azure Multi-Factor Authentication a seconda del Azure AD, Office 365, EMS o della licenza Microsoft 365 attualmente disponibile. La tabella seguente illustra in dettaglio i diversi modi per ottenere Azure Multi-Factor Authentication e alcune delle funzionalità e i casi d'uso per ognuno di essi.

| Se sei un utente di | Funzionalità e casi d'uso |
| --- | --- |
| EMS o Microsoft 365 E3 e E5 | EMS E3 o Microsoft 365 E3, che include EMS e Office 365, include Azure AD Premium P1. EMS E5 o Microsoft 365 E5 include Azure AD Premium P2. È possibile utilizzare le stesse funzionalità di accesso condizionale indicate nelle sezioni seguenti per fornire agli utenti l'autenticazione a più fattori. |
| Azure AD Premium P1 | È possibile usare [Azure ad l'accesso condizionale](../conditional-access/overview.md) per richiedere agli utenti di eseguire l'autenticazione a più fattori durante determinati scenari o eventi in base ai requisiti aziendali. |
| Azure AD Premium P2 | Fornisce la posizione di sicurezza più avanzata e l'esperienza utente migliorata. Aggiunge l' [accesso condizionale basato sul rischio](../conditional-access/howto-conditional-access-policy-risk.md) alle funzionalità Azure ad Premium P1 che si adattano ai modelli dell'utente e riduce al minimo le richieste di autenticazione a più fattori. |
| Office 365 Business Premium, E3 o E5 | Azure Multi-Factor Authentication è abilitato o disabilitato per tutti gli utenti, per tutti gli eventi di accesso. Non è possibile abilitare l'autenticazione a più fattori solo per un subset di utenti o solo in determinati scenari. La gestione avviene tramite il portale di Office 365. Per migliorare l'esperienza utente, eseguire l'aggiornamento a Azure AD Premium P1 o P2 e usare l'accesso condizionale. Per altre informazioni, vedere [proteggere le risorse di Office 365 con l'autenticazione a più fattori](https://support.office.com/article/Set-up-multi-factor-authentication-for-Office-365-users-8f0454b2-f51a-4d9c-bcde-2c48e41621c6). |
| Azure AD Gratuito | Per abilitare l'autenticazione a più fattori per tutti gli utenti, è possibile usare le [impostazioni predefinite di sicurezza](../fundamentals/concept-fundamentals-security-defaults.md) , ogni volta che viene effettuata una richiesta di autenticazione. Non si dispone di un controllo granulare degli utenti o degli scenari abilitati, ma fornisce un ulteriore passaggio di sicurezza.<br /> Anche quando le impostazioni predefinite di sicurezza non vengono usate per abilitare l'autenticazione a più fattori per tutti, gli utenti assegnati al ruolo di *amministratore globale Azure ad* possono essere configurati per l'uso dell'autenticazione a più fattori. Questa funzionalità del livello gratuito garantisce che gli account di amministratore critico siano protetti da autenticazione a più fattori. |

## <a name="feature-comparison-of-versions"></a>Confronto tra le funzionalità delle versioni

La tabella seguente indica un elenco delle funzionalità disponibili nelle varie versioni di Azure Multi-Factor Authentication. Pianificare le esigenze di protezione dell'autenticazione utente e quindi determinare quale approccio soddisfi tali requisiti. Ad esempio, anche se Azure AD Free fornisce impostazioni predefinite di sicurezza che forniscono Multi-Factor Authentication di Azure, è possibile usare solo l'app Mobile Authenticator per la richiesta di autenticazione, non una telefonata o un SMS. Questo approccio può costituire una limitazione se non è possibile verificare che l'app di autenticazione per dispositivi mobili sia installata nel dispositivo personale di un utente.

| Funzionalità | Azure AD Free-impostazioni predefinite di sicurezza | Amministratori globali di Azure AD Free Azure AD | Office 365 Business Premium, E3 o E5 | Azure AD Premium P1 o P2 |
| --- |:---:|:---:|:---:|:---:|
| Proteggi gli account amministratore tenant Azure AD | ● | ● (Solo*Azure ad account amministratore globale* ) | ● | ● |
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
> A partire da marzo del 2019, le opzioni per le chiamate telefoniche non sono più disponibili per gli utenti di Azure Multi-Factor Authentication e la reimpostazione della password self-service di Azure in tenant di Azure AD Free/versione di valutazione. I messaggi SMS non sono interessati da questa modifica. Le chiamate telefoniche continuano a essere disponibili per gli utenti in Azure AD Premium tenant P1 o P2 o per usi o per Office 365 Business Premium, E3 o E5.

## <a name="purchase-and-enable-azure-multi-factor-authentication"></a>Acquistare e abilitare Multi-Factor Authentication di Azure

Per usare Multi-Factor Authentication di Azure, effettuare la registrazione o acquistare un livello di Azure AD idoneo. Azure AD è disponibile in quattro edizioni: gratuita, Office 365 Apps Edition (per Office 365 Business Premium E3 o E5 Customers), Premium P1 e Premium P2.

L'edizione gratuita è inclusa in una sottoscrizione di Azure. Vedere la [sezione seguente](#azure-ad-free-tier) per informazioni su come usare le impostazioni predefinite di sicurezza o proteggere gli account con il ruolo di *amministratore globale Azure ad* .

Le edizioni Azure AD Premium sono disponibili tramite il rappresentante Microsoft, il [programma di contratti multilicenza Open](https://www.microsoft.com/licensing/licensing-programs/open-license.aspx)e il [programma Cloud Solution Provider](https://go.microsoft.com/fwlink/?LinkId=614968&clcid=0x409). I sottoscrittori di Azure e Office 365 possono acquistare Azure Active Directory Premium P1 e P2 anche online. [Accedi](https://portal.office.com/Commerce/Catalog.aspx) per acquistare.

> [!IMPORTANT]
> Le licenze basate sul consumo non sono più disponibili per i nuovi clienti a partire dal 1 ° settembre 2018. I clienti esistenti che usano il modello basato sul consumo possono continuare a usare ogni utente abilitato o la fatturazione per autenticazione.

Dopo aver acquistato il livello di Azure AD necessario, [pianificare e distribuire multi-factor authentication di Azure](howto-mfa-getstarted.md).

### <a name="azure-ad-free-tier"></a>Livello Azure AD Free

Tutti gli utenti in un tenant di Azure AD Free possono usare Azure multi-factor authentication tramite l'uso delle impostazioni predefinite di sicurezza. Queste impostazioni predefinite di sicurezza abilitano l'autenticazione a più fattori di Azure per tutti gli utenti, ogni volta che accedono. L'app di autenticazione mobile è l'unico metodo che può essere usato per Multi-Factor Authentication di Azure quando si usano Azure AD Free impostazioni predefinite di sicurezza.

* [Altre informazioni sulle impostazioni predefinite di sicurezza di Azure AD](../fundamentals/concept-fundamentals-security-defaults.md)
* [Abilita impostazioni predefinite sicurezza per gli utenti in Azure AD Free](../fundamentals/concept-fundamentals-security-defaults.md#enabling-security-defaults)

Se non si vuole abilitare Multi-Factor Authentication di Azure per tutti gli utenti e ogni evento di accesso, è possibile scegliere di proteggere solo gli account utente con il ruolo di *amministratore di Azure ad globale* . Questo approccio fornisce richieste di autenticazione aggiuntive per gli account amministratore critici. Per abilitare Multi-Factor Authentication di Azure, è possibile usare uno dei modi seguenti, a seconda del tipo di account usato:

* Se si usa un account Microsoft, effettuare la [registrazione per l'autenticazione a più fattori](https://support.microsoft.com/help/12408/microsoft-account-about-two-step-verification).
* Se non si usa un account Microsoft, [attivare l'autenticazione a più fattori per un utente o un gruppo in Azure ad](howto-mfa-userstates.md).

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sui costi, vedere [prezzi di Azure multi-factor authentication](https://azure.microsoft.com/pricing/details/multi-factor-authentication/).
