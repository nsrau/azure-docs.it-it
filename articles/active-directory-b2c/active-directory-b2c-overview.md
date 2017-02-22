---
title: 'Azure Active Directory B2C: panoramica | Microsoft Docs'
description: Sviluppo di applicazioni di utenti con Azure Active Directory B2C
services: active-directory-b2c
documentationcenter: 
author: swkrish
manager: mbaldwin
editor: bryanla
ms.assetid: c465dbde-f800-4f2e-8814-0ff5f5dae610
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 12/06/2016
ms.author: swkrish
translationtype: Human Translation
ms.sourcegitcommit: f37fa3a212a0dca46c76b3928cf78756722f0340
ms.openlocfilehash: 2c6765e28f40210730823a9af0b6fb80a5ddc4d1


---
# <a name="azure-active-directory-b2c-sign-up-and-sign-in-consumers-in-your-applications"></a>Azure Active Directory B2C: iscrizione e accesso degli utenti alle applicazioni
Azure Active Directory B2C è una soluzione di gestione identità cloud completa per utenti Web e applicazioni per dispositivi mobili. È un servizio globale a disponibilità elevata adattabile a centinaia di milioni di identità di utenti. Basato su una piattaforma sicura aziendale, Azure Active Directory B2C mantiene le applicazioni, l'azienda e gli utenti protetti.

In passato, gli sviluppatori di applicazioni che desideravano iscriversi e accedere ai consumer nelle proprie applicazioni dovevano scrivere il proprio codice. E dovevano utilizzare database locali o sistemi per archiviare nomi utente e password. Azure Active Directory B2C offre agli sviluppatori un modo migliore per integrare la gestione delle identità utente nelle applicazioni con l'aiuto di una piattaforma sicura, basata su standard e un set completo di criteri estendibili. Quando si usa Azure Active Directory B2C, gli utenti possono iscriversi alle applicazioni usando gli account di social networking esistenti (Facebook, Google, Amazon, LinkedIn) o creando nuove credenziali (indirizzo di posta elettronica e password o nome utente e password). Queste ultime sono chiamate "account locali".

## <a name="get-started"></a>Introduzione
Per compilare un'applicazione che accetta l'iscrizione e l'accesso dell'utente, prima di tutto sarà necessario registrarla con un tenant di Azure Active Directory B2C. Per ottenere il tenant, seguire la procedura illustrata in [Azure Active Directory B2C: creare un tenant di Azure AD B2C](active-directory-b2c-get-started.md).

È possibile scrivere l'applicazione per il servizio Azure Active Directory B2C scegliendo di inviare direttamente messaggi di protocollo, con [OAuth 2.0 o Open ID Connect](active-directory-b2c-reference-protocols.md), oppure usando le librerie per eseguire automaticamente l'operazione. Scegliere la piattaforma preferita nella tabella seguente e iniziare.

[!INCLUDE [active-directory-b2c-quickstart-table](../../includes/active-directory-b2c-quickstart-table.md)]

## <a name="whats-new"></a>Novità
Visitare spesso questa pagina per rimanere aggiornati sulle future modifiche apportate ad Azure Active Directory B2C. Gli aggiornamenti saranno anche resi noti su Twitter con @AzureAD.

* Informazioni sul [framework di criteri estendibile](active-directory-b2c-reference-policies.md) e sui tipi di criteri che è possibile creare e usare nelle applicazioni.
* [Blog sul servizio](https://blogs.msdn.microsoft.com/azureadb2c/) , da aggiungere ai segnalibri, per notifiche su problemi secondari, azioni di mitigazione, aggiornamenti e stato del servizio. Continuare a monitorare anche il [dashboard di stato di Azure](https://azure.microsoft.com/status/) .
* [Limitazioni, restrizioni e vincoli correnti del servizio](active-directory-b2c-limitations.md).
* [Esempio di codice](https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect-aspnetcore-b2c) con Azure AD B2C e ASP.NET Core.

## <a name="how-to-articles"></a>Procedure
Informazioni su come usare funzionalità specifiche di Azure Active Directory B2C:

* Configurare gli account [Facebook](active-directory-b2c-setup-fb-app.md), [Google+](active-directory-b2c-setup-goog-app.md), [Microsoft](active-directory-b2c-setup-msa-app.md), [Amazon](active-directory-b2c-setup-amzn-app.md) e [LinkedIn](active-directory-b2c-setup-li-app.md) per l'uso nelle applicazioni destinate agli utenti.
* [Utilizzare attributi personalizzati per raccogliere informazioni sugli utenti](active-directory-b2c-reference-custom-attr.md).
* [Abilitare Azure Multi-Factor Authentication nelle applicazioni destinate agli utenti](active-directory-b2c-reference-mfa.md).
* [Configurare la reimpostazione password self-service per gli utenti](active-directory-b2c-reference-sspr.md).
* [Personalizzare l'aspetto delle pagine di iscrizione e di accesso e di altre pagine destinate agli utenti](active-directory-b2c-reference-ui-customization.md) gestite da Azure Active Directory B2C.
* [Usare l'API Graph di Azure Active Directory per creare, leggere, aggiornare ed eliminare utenti a livello di codice](active-directory-b2c-devquickstarts-graph-dotnet.md) nel tenant di Azure Active Directory B2C.

## <a name="next-steps"></a>Passaggi successivi
I collegamenti seguenti sono utili per un'esplorazione più approfondita del servizio:

* Vedere [Prezzi di Azure Active Directory B2C](https://azure.microsoft.com/pricing/details/active-directory-b2c/).
* Vedere i [codici di esempio](https://azure.microsoft.com/en-us/resources/samples/?service=active-directory&term=b2c) per Azure Active Directory B2C. 
* Ottenere supporto in Stack Overflow usando i tag [azure-active-directory](http://stackoverflow.com/questions/tagged/azure-active-directory) o [adal](http://stackoverflow.com/questions/tagged/adal).
* Inviare commenti e suggerimenti usando il [forum dedicato](https://feedback.azure.com/forums/169401-azure-active-directory/). Specificare "AzureADB2C:" nel titolo del post per permetterci di individuarlo.
* Vedere le [informazioni di riferimento sui protocolli di Azure AD B2C](active-directory-b2c-reference-protocols.md).
* Vedere [Azure AD B2C: informazioni di riferimento sui token](active-directory-b2c-reference-tokens.md).
* Vedere [Azure Active Directory B2C: domande frequenti](active-directory-b2c-faqs.md).
* [Inviare richieste di supporto per Azure Active Directory B2C](active-directory-b2c-support.md).

## <a name="get-security-updates-for-our-products"></a>Ottenere aggiornamenti della sicurezza per i prodotti
È consigliabile ricevere notifiche in caso di problemi di sicurezza. A tale scopo, visitare [questa pagina](https://technet.microsoft.com/security/dd252948) e sottoscrivere gli avvisi di sicurezza.




<!--HONumber=Feb17_HO1-->


