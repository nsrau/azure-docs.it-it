---
title: Confrontare Active Directory con Azure Active Directory
description: Questo documento mette a confronto Active Directory Domain Services (aggiunge) a Azure Active Directory (AD). Vengono descritti i concetti chiave in entrambe le soluzioni di identità e viene illustrato come è diverso o simile.
services: active-directory
author: martincoetzer
manager: daveba
tags: azuread
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: fundamentals
ms.date: 02/26/2020
ms.author: martinco
ms.openlocfilehash: 3c8cf84aa697512b6d1147bf853d30761792370b
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/19/2020
ms.locfileid: "92207309"
---
# <a name="compare-active-directory-to-azure-active-directory"></a>Confrontare Active Directory con Azure Active Directory

Azure Active Directory è la prossima evoluzione delle soluzioni di gestione delle identità e dell'accesso per il cloud. Microsoft ha introdotto Active Directory Domain Services in Windows 2000 per offrire alle organizzazioni la possibilità di gestire più componenti e sistemi dell'infrastruttura locale usando una singola identità per ogni utente.

Azure AD adotta questo approccio al livello successivo offrendo alle organizzazioni una soluzione IDaaS (Identity As a Service) per tutte le app nel cloud e in locale.

La maggior parte degli amministratori IT ha familiarità con Active Directory Domain Services concetti. Nella tabella seguente vengono descritte le differenze e le analogie tra Active Directory concetti e Azure Active Directory.

|Concetto|Active Directory (AD)|Azure Active Directory |
|:-|:-|:-|
|**Utenti**|||
|Provisioning: utenti | Le organizzazioni creano utenti interni manualmente o usano un sistema di provisioning interno o automatizzato, ad esempio la Microsoft Identity Manager, per l'integrazione con un sistema HR.|Le organizzazioni di Active Directory esistenti usano [Azure ad Connect](../hybrid/how-to-connect-sync-whatis.md) per sincronizzare le identità nel cloud.</br> Azure AD aggiunge il supporto per la creazione automatica di utenti da [sistemi HR cloud](../saas-apps/workday-tutorial.md). </br>Azure AD possibile eseguire il provisioning delle identità nelle app Saas [abilitate per SCIM](../app-provisioning/use-scim-to-provision-users-and-groups.md) per fornire automaticamente le app con i dettagli necessari per consentire l'accesso agli utenti. |
|Provisioning: identità esterne| Le organizzazioni creano utenti esterni manualmente come utenti normali in una foresta di Active Directory esterna dedicata, causando un sovraccarico amministrativo per gestire il ciclo di vita delle identità esterne (utenti guest)| Azure AD fornisce una classe speciale di identità per supportare le identità esterne. [Azure ad B2B](/azure/active-directory/b2b/) gestirà il collegamento all'identità dell'utente esterno per assicurarsi che siano valide. |
| Gestione e gruppi di diritti| Gli amministratori rendono gli utenti membri dei gruppi. I proprietari di app e risorse consentono quindi ai gruppi di accedere alle app o alle risorse.| I [gruppi](./active-directory-groups-create-azure-portal.md) sono disponibili anche in Azure ad e gli amministratori possono anche usare i gruppi per concedere le autorizzazioni alle risorse. In Azure AD gli amministratori possono assegnare l'appartenenza ai gruppi manualmente oppure utilizzare una query per includere in modo dinamico gli utenti a un gruppo. </br> Gli amministratori possono usare la [gestione dei diritti](../governance/entitlement-management-overview.md) in Azure ad per concedere agli utenti l'accesso a una raccolta di app e risorse usando i flussi di lavoro e, se necessario, i criteri basati sul tempo. |
| Gestione amministratori|Le organizzazioni utilizzeranno una combinazione di domini, unità organizzative e gruppi in Active Directory per delegare i diritti amministrativi per gestire la directory e le risorse che controlla.| Azure AD fornisce [ruoli predefiniti](./active-directory-users-assign-role-azure-portal.md) con il sistema di controllo degli accessi in base al ruolo Azure AD (Azure ad RBAC), con supporto limitato per la [creazione di ruoli personalizzati](../users-groups-roles/roles-custom-overview.md) per delegare l'accesso con privilegi al sistema di identità, alle app e alle risorse che controlla.</br>La gestione dei ruoli può essere migliorata con [Privileged Identity Management (PIM)](../privileged-identity-management/pim-configure.md) per fornire accesso just-in-time, con restrizioni temporali o in base al flusso di lavoro ai ruoli con privilegi. |
| Gestione delle credenziali| Le credenziali in Active Directory si basano su password, autenticazione del certificato e autenticazione basata su smart card. Le password vengono gestite con criteri password basati su lunghezza, scadenza e complessità della password.|Azure AD usa la [protezione con password](../authentication/concept-password-ban-bad.md) intelligente per il cloud e l'ambiente locale. La protezione include il blocco intelligente più il blocco di frasi e sostituzioni di password comuni e personalizzate. </br>Azure AD migliora significativamente la sicurezza [tramite l'autenticazione](../authentication/concept-mfa-howitworks.md) a più fattori e le tecnologie con [password](../authentication/concept-authentication-passwordless.md) , ad esempio FIDO2. </br>Azure AD riduce i costi di supporto fornendo agli utenti un sistema di [reimpostazione della password self-service](../authentication/concept-sspr-howitworks.md) . |
| **App**|||
| App dell'infrastruttura|Active Directory costituisce la base per molti componenti locali dell'infrastruttura, ad esempio DNS, DHCP, IPSec, Wi-Fi, server dei criteri di rete e accesso VPN|In un nuovo ambiente cloud, Azure AD, è il nuovo piano di controllo per l'accesso alle app rispetto a quello basato sui controlli di rete. Quando gli utenti eseguono[l'autenticazione, l'accesso condizionale (CA)](../conditional-access/overview.md)consente di controllare quali utenti avranno accesso alle app in condizioni obbligatorie.|
| App tradizionali e Legacy| La maggior parte delle app locali usa LDAP, l'autenticazione Windows-Integrated (NTLM e Kerberos) o l'autenticazione basata su intestazione per controllare l'accesso agli utenti.| Azure AD possibile fornire l'accesso a questi tipi di app locali usando [Azure ad agenti proxy di applicazione](../manage-apps/application-proxy.md) in esecuzione in locale. L'uso di questo metodo Azure AD può autenticare Active Directory utenti locali usando Kerberos mentre si esegue la migrazione o deve coesistere con le app legacy. |
| App SaaS|Active Directory non supporta le app SaaS in modo nativo e richiede il sistema federativo, ad esempio AD FS.|Le app SaaS che supportano OAuth2, SAML e WS- \* Authentication possono essere integrate per usare Azure ad per l'autenticazione. |
| App line-of-business (LOB) con autenticazione moderna|Le organizzazioni possono usare AD FS con Active Directory per supportare le app LOB che richiedono l'autenticazione moderna.| Le app LOB che richiedono l'autenticazione moderna possono essere configurate per usare Azure AD per l'autenticazione. |
| Servizi di livello intermedio/daemon|I servizi in esecuzione in ambienti locali usano in genere gli account del servizio Active Directory o gli account del servizio gestito del gruppo (gMSA) per l'esecuzione. Queste app erediteranno quindi le autorizzazioni dell'account del servizio.| Azure AD fornisce le [identità gestite](../managed-identities-azure-resources/index.yml) per eseguire altri carichi di lavoro nel cloud. Il ciclo di vita di queste identità è gestito da Azure AD ed è associato al provider di risorse, pertanto non può essere usato per altri scopi per ottenere l'accesso backdoor.|
| **Dispositivi**|||
| Mobile|Active Directory non supporta in modo nativo i dispositivi mobili senza soluzioni di terze parti.| La soluzione di gestione dei dispositivi mobili Microsoft, Microsoft Intune, è integrata con Azure AD. Microsoft Intune fornisce informazioni sullo stato del dispositivo al sistema di identità da valutare durante l'autenticazione. |
| Desktop di Windows|Active Directory offre la possibilità di aggiungere a un dominio i dispositivi Windows per gestirli con Criteri di gruppo, System Center Configuration Manager o altre soluzioni di terze parti.|I dispositivi Windows possono essere [aggiunti a Azure ad](../devices/index.yml). L'accesso condizionale può verificare se un dispositivo è Azure AD Unito come parte del processo di autenticazione. I dispositivi Windows possono essere gestiti anche con [Microsoft Intune](/intune/what-is-intune). In questo caso, l'accesso condizionale considera se un dispositivo è conforme (ad esempio, patch di sicurezza aggiornate e firme antivirus) prima di consentire l'accesso alle app.|
| Server Windows| Active Directory offre funzionalità di gestione complesse per i server Windows locali che usano Criteri di gruppo o altre soluzioni di gestione.| Le macchine virtuali di Windows Server in Azure possono essere gestite con [Azure ad Domain Services](../../active-directory-domain-services/index.yml). È possibile usare le [identità gestite](../managed-identities-azure-resources/index.yml) quando le macchine virtuali devono accedere alla directory o alle risorse del sistema di identità.|
| Carichi di lavoro Linux/UNIX|Active Directory non supporta in modo nativo non Windows senza soluzioni di terze parti, anche se i computer Linux possono essere configurati per l'autenticazione con Active Directory come area di autenticazione Kerberos.|Le macchine virtuali Linux/Unix possono usare [identità gestite](../managed-identities-azure-resources/index.yml) per accedere al sistema di identità o alle risorse. Per alcune organizzazioni, migrare questi carichi di lavoro in tecnologie contenitore cloud, che possono usare anche identità gestite.|

## <a name="next-steps"></a>Passaggi successivi

- [Che cos'è Azure Active Directory?](./active-directory-whatis.md)
- [Confrontare soluzioni Active Directory Domain Services autogestite, Azure Active Directory e Azure Active Directory Domain Services gestite](../../active-directory-domain-services/compare-identity-solutions.md)
- [Domande frequenti su Azure Active Directory](./active-directory-faq.md)
- [Novità di Azure Active Directory](./whats-new.md)
