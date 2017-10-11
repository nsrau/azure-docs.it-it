---
title: Criteri di accesso condizionale dei dispositivi di Azure Active Directory per i servizi di Office 365| Microsoft Docs
description: "Informazioni su come eseguire il provisioning dei criteri di accesso condizionale dei dispositivi per rendere le risorse aziendali più sicure, mantenendo la conformità degli utenti e l'accesso ai servizi."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 8664c0bb-bba1-4012-b321-e9c8363080a0
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/02/2017
ms.author: markvi
ms.reviewer: calebb
ms.openlocfilehash: 10a4a23af08c31ea107e196ae441fefd39c7cabc
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/18/2017
---
# <a name="active-directory-conditional-access-device-policies-for-office-365-services"></a>Criteri di accesso condizionale dei dispositivi di Active Directory per i servizi di Office 365

L'accesso condizionale richiede più elementi per funzionare, tra cui un utente con autenticazione a più fattori, un dispositivo autenticato e un dispositivo conforme. Questo articolo si concentra principalmente sulle condizioni basate sul dispositivo che l'organizzazione può usare per consentire il controllo dell'accesso ai servizi di Office 365. 

Gli utenti aziendali vogliono accedere a servizi di Office 365 come Exchange e SharePoint Online al lavoro o a scuola dai propri dispositivi personali. È importante che l'accesso sia sicuro. È possibile eseguire il provisioning dei criteri di accesso condizionale dei dispositivi per rendere le risorse aziendali più sicure, concedendo l'accesso ai servizi agli utenti che usano i dispositivi conformi. I criteri di accesso condizionale per Office 365 possono essere configurati nel portale di accesso condizionale di Microsoft Intune.

Azure Active Directory (Azure AD) applica criteri di accesso condizionale per consentire la protezione dell'accesso ai servizi di Office 365. È possibile creare criteri di accesso condizionale che impediscano a un utente che usa un dispositivo non conforme di accedere a un servizio di Office 365. Per poter accedere al servizio l'utente deve risultare conforme ai criteri dei dispositivi applicati della società. In alternativa, è possibile creare criteri che richiedano agli utenti di registrare i propri dispositivi per accedere a un servizio di Office 365. I criteri possono essere applicati a tutti gli utenti di un'organizzazione o limitati ad alcuni gruppi di destinazione. È possibile aggiungere altri gruppi di destinazione a un criterio in un secondo tempo.

La registrazione dei dispositivi degli utenti nel servizio Registrazione dispositivo di Azure AD costituisce un prerequisito per l'applicazione dei criteri per i dispositivi. È possibile scegliere di attivare l'autenticazione a più fattori per i dispositivi registrati con il servizio Registrazione dispositivo di Azure AD. L'autenticazione a più fattori è consigliabile per il servizio Registrazione dispositivo di Azure Active Directory. Quando è attivata l'autenticazione a più fattori, agli utenti che registrano i propri dispositivi con il servizio Registrazione dispositivo di Azure AD viene richiesta un'autenticazione basata su un secondo fattore.

## <a name="how-does-a-conditional-access-policy-work"></a>Come funzionano i criteri di accesso condizionale

Quando un utente richiede l'accesso a un servizio di Office 365 da una piattaforma del dispositivo supportata, Azure AD autentica l'utente e il dispositivo. Azure AD concede l'accesso al servizio solo se l'utente è conforme ai criteri impostati per il servizio. Per gli utenti dei dispositivi non registrati sono disponibili le istruzioni necessarie per eseguire la registrazione e ottenere la conformità per accedere ai servizi aziendali di Office 365. Agli utenti dei dispositivi iOS e Android viene richiesto di eseguire la registrazione con l'applicazione Portale aziendale di Microsoft Intune. Quando un utente esegue la registrazione, il dispositivo viene registrato in Azure AD per la conformità e la gestione dei dispositivi. È necessario usare il servizio Registrazione dispositivo di Azure AD con Microsoft Intune per la gestione dei dispositivi mobili per i servizi di Office 365. La registrazione del dispositivo è obbligatoria per gli utenti che accedono ai servizi di Office 365 quando sono applicati i criteri per i dispositivi.

Eseguita la registrazione, il dispositivo diventa attendibile. Azure AD offre all'utente autenticato l'accesso Single Sign-On alle applicazioni aziendali. Azure AD applica i criteri di accesso condizionale per concedere l'accesso a un servizio non solo la prima volta che l'utente richiede l'accesso, ma ogni volta che l'utente rinnova una richiesta di accesso. L'accesso ai servizi viene negato in caso di modifica delle credenziali di accesso, di furto o smarrimento del dispositivo o di mancato rispetto delle condizioni dei criteri al momento della richiesta di rinnovo.

## <a name="deployment-considerations"></a>Considerazioni sulla distribuzione

È necessario usare il servizio Registrazione dispositivo di Azure AD per registrare i dispositivi.

Nella fase di autenticazione degli utenti locali è obbligatorio usare Active Directory Federation Services (AD FS) (versione 1.0 e successive). L'autenticazione a più fattori per Workplace Join ha esito negativo se il provider di identità non supporta l'autenticazione a più fattori. Ad esempio, non è possibile usare l'autenticazione a più fattori con AD FS 2.0. Verificare che la sessione locale di AD FS funzioni con l'autenticazione a più fattori e che sia in uso un metodo di autenticazione a più fattori valido prima di attivare l'autenticazione a più fattori per il servizio Registrazione dispositivo di Azure AD. Ad esempio, AD FS in Windows Server 2012 R2 offre funzionalità per l'autenticazione a più fattori. È anche necessario impostare un metodo di autenticazione valido aggiuntivo (autenticazione a più fattori) nel server AD FS prima di attivare l'autenticazione a più fattori per il servizio Registrazione dispositivo di Azure AD. Per altre informazioni sui metodi di autenticazione a più fattori supportati in AD FS, vedere [Configurare altri metodi di autenticazione per AD FS](/windows-server/identity/ad-fs/operations/configure-additional-authentication-methods-for-ad-fs).

## <a name="next-steps"></a>Passaggi successivi

*   Per le risposte alle domande più comuni, vedere [Domande frequenti sull'accesso condizionale di Azure Active Directory](active-directory-conditional-faqs.md).
