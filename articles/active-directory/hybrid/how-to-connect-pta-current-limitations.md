---
title: 'Azure AD Connect: Autenticazione pass-through - Limitazioni correnti | Microsoft Docs'
description: Questo articolo descrive le limitazioni correnti dell'autenticazione pass-through di Azure Active Directory (Azure AD).
services: active-directory
keywords: Autenticazione pass-through di Azure AD Connect, installare Active Directory, componenti necessari per Azure AD, SSO, Single Sign-On
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/04/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: de42312c580b6bd5b4a5148fd877d55aa00e8804
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/13/2019
ms.locfileid: "56204594"
---
# <a name="azure-active-directory-pass-through-authentication-current-limitations"></a>Autenticazione pass-through di Azure Active Directory: Limitazioni correnti

>[!IMPORTANT]
>L'autenticazione pass-through di Azure Active Directory (Azure AD) è una funzionalità gratuita che può essere usata senza acquistare edizioni a pagamento. L'autenticazione pass-through è disponibile solo nell'istanza di Azure AD a livello globale, non nel [cloud Microsoft Azure Germania](https://www.microsoft.de/cloud-deutschland) o nel [cloud Microsoft Azure per enti pubblici](https://azure.microsoft.com/features/gov/).

## <a name="supported-scenarios"></a>Scenari supportati

Sono supportati gli scenari seguenti:

- Accessi utente alle applicazioni basate su Web browser.
- Accessi utente ai client di Outlook usando protocolli legacy, ad esempio Exchange ActiveSync, EAS, SMTP, POP e IMAP.
- Accessi utente ad applicazioni legacy client di Office e di Office stesso che supportano l'[autenticazione moderna](https://aka.ms/modernauthga), ovvero Office 2010, 2013 e Office 2016.
- Accessi utente alle applicazioni di protocolli legacy, ad esempio PowerShell versione 1.0 e altre.
- Azure AD Join per dispositivi Windows 10.
- Password di app per Multi-Factor Authentication.

## <a name="unsupported-scenarios"></a>Scenari non supportati

_Non_ sono supportati gli scenari seguenti:

- Rilevamento di utenti con [credenziali perse](../reports-monitoring/concept-risk-events.md#leaked-credentials).
- Azure AD Domain Services necessita della sincronizzazione dell'hash delle password per l'abilitazione nel tenant. I tenant che usano _solo_ l'autenticazione pass-through non funzionano per gli scenari che richiedono Azure AD Domain Services.
- L'autenticazione pass-through non è integrata con [Azure AD Connect Health](whatis-hybrid-identity-health.md).

>[!IMPORTANT]
>Come soluzione alternativa _solo_ per gli scenari non supportati, (ad eccezione dell'integrazione Azure AD Connect Health) abilitare la sincronizzazione dell'hash delle password nella pagina [Funzionalità facoltative](how-to-connect-install-custom.md#optional-features) della procedura guidata di Azure AD Connect.

>[!NOTE]
Abilitando questa funzionalità è possibile effettuare il failover dell'autenticazione se il funzionamento dell'infrastruttura locale è interrotto. Il failover dall'autenticazione pass-through alla sincronizzazione dell'hash delle password non avviene automaticamente. È necessario cambiare il metodo di accesso manualmente con Azure AD Connect. Se il server che esegue Azure AD Connect smette di funzionare, è necessario chiedere assistenza al supporto tecnico Microsoft per disattivare l'autenticazione pass-through.

## <a name="next-steps"></a>Passaggi successivi
- [Avvio rapido](how-to-connect-pta-quick-start.md): come iniziare a usare l'autenticazione pass-through di Azure AD.
- [Eseguire la migrazione da AD FS all'autenticazione pass-through](https://aka.ms/ADFSTOPTADPDownload): una guida dettagliata per la migrazione da AD FS (o altre tecnologie federative) per l'autenticazione pass-through.
- [Blocco intelligente](../authentication/howto-password-smart-lockout.md): informazioni su come configurare la funzionalità di blocco intelligente nel tenant per proteggere gli account utente.
- [Approfondimento tecnico](how-to-connect-pta-how-it-works.md): informazioni sul funzionamento dell'autenticazione pass-through.
- [Domande frequenti](how-to-connect-pta-faq.md): trovare risposte alle domande frequenti sulla funzionalità di autenticazione pass-through.
- [Risolvere i problemi](tshoot-connect-pass-through-authentication.md): informazioni su come risolvere i problemi comuni relativi alla funzionalità di autenticazione pass-through.
- [Approfondimento sulla sicurezza](how-to-connect-pta-security-deep-dive.md): informazioni tecniche approfondite sulla funzionalità Autenticazione pass-through.
- [Seamless SSO di Azure AD](how-to-connect-sso.md): altre informazioni su questa funzionalità complementare.
- [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect): come usare il forum di Azure Active Directory per inviare richieste di nuove funzionalità.
