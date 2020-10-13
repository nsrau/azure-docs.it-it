---
title: Come eseguire l'integrazione con la piattaforma di identità Microsoft | Azure
titleSuffix: Microsoft identity platform
description: Scopri i vantaggi dell'integrazione dell'applicazione con la piattaforma di identità Microsoft e Ottieni risorse per funzionalità come l'accesso semplificato, la gestione delle identità, l'autenticazione a più fattori e il controllo degli accessi.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/01/2020
ms.author: ryanwi
ms.reviewer: jmprieur
ms.custom: aaddev, seoapril2019
ms.openlocfilehash: e9724bb2e5377f84904dc77c33375c39f3077dd4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91631410"
---
# <a name="integrating-with-microsoft-identity-platform"></a>Integrazione con la piattaforma di identità Microsoft

[!INCLUDE [active-directory-devguide](../../../includes/active-directory-devguide.md)]

Questo articolo illustra i vantaggi dell'integrazione dell'applicazione con la piattaforma di identità Microsoft e l'ottenimento delle risorse per l'integrazione. Microsoft Identity Platform and Azure Active Directory (AD) offre alle organizzazioni una gestione delle identità di livello aziendale per le applicazioni cloud. L'integrazione della piattaforma di identità Microsoft offre agli utenti un'esperienza di accesso semplificata e consente alle applicazioni di essere conformi ai criteri IT.

## <a name="how-to-integrate"></a>Modalità di integrazione

Esistono diversi modi per integrare l'applicazione con la piattaforma di identità Microsoft. Fare riferimento agli scenari seguenti a seconda dell'applicazione.

### <a name="support-microsoft-identity-platform-as-a-way-to-sign-in-to-your-application"></a>Supporto della piattaforma di identità Microsoft per accedere all'applicazione

**Ridurre i problemi di accesso e i costi di supporto.** Usando la piattaforma di identità Microsoft per accedere all'applicazione, gli utenti non avranno un nome e una password da ricordare. Gli sviluppatori avranno invece una password in meno da archiviare e proteggere. Anche il solo fatto di non dover gestire la reimpostazione delle password dimenticate costituisce un risparmio significativo. Microsoft Identity Platform Powers permette di accedere ad alcune delle applicazioni cloud più diffuse del mondo, tra cui Microsoft 365 e Microsoft Azure. Con centinaia di milioni di utenti di milioni di organizzazioni, è probabile che l'utente abbia già eseguito l'accesso alla piattaforma Microsoft Identity. Altre informazioni sull' [aggiunta del supporto per l'accesso alla piattaforma di identità Microsoft](./authentication-vs-authorization.md).

**Semplificare l'iscrizione all'applicazione.**  Durante l'iscrizione per l'applicazione, la piattaforma di identità Microsoft può inviare informazioni essenziali su un utente in modo che sia possibile compilare il modulo di iscrizione o eliminarlo completamente. Gli utenti possono effettuare l'iscrizione all'applicazione usando l'account Azure AD tramite un'esperienza di consenso nota simile a quelle dei social media e delle applicazioni per dispositivi mobili. Qualsiasi utente può iscriversi e accedere a un'applicazione integrata con Microsoft Identity Platform senza richiedere il coinvolgimento IT. Altre informazioni sulla [registrazione dell'applicazione per l'accesso con l'account Azure AD](../../app-service/configure-authentication-provider-aad.md) .

### <a name="browse-for-users-manage-user-provisioning-and-control-access-to-your-application"></a>Cerca utenti, Gestisci il provisioning utenti e controlla l'accesso all'applicazione

**Esplorare la directory per cercare gli utenti.**  Usare l'API Microsoft Graph per consentire agli utenti di cercare altri utenti all'interno dell'organizzazione quando invitano altri utenti o di concedere l'accesso, anziché richiedere di digitare indirizzi di posta elettronica. Gli utenti possono esplorare la directory tramite un'interfaccia simile a una rubrica e possono visualizzare anche i dettagli della gerarchia all'interno dell'organizzazione. Altre informazioni sull' [API Microsoft Graph](/graph/overview).

**Riutilizzare i gruppi e le liste di distribuzione di Active Directory che il cliente gestisce già.**   In Azure AD sono già presenti i gruppi che il cliente usa per la distribuzione della posta elettronica e per la gestione degli accessi. Usando l'API Microsoft Graph, riusare questi gruppi invece di richiedere al cliente di creare e gestire un set separato di gruppi nell'applicazione. Le informazioni sui gruppi possono anche essere inviate all'applicazione nei token di accesso. Altre informazioni sull' [API Microsoft Graph](/graph/overview).

**Usare la piattaforma Microsoft Identity per controllare chi ha accesso all'applicazione.**   Gli amministratori e i proprietari di applicazioni in Azure AD possono assegnare l'accesso alle applicazioni a utenti e gruppi specifici. Usando l'API di Microsoft Graph, è possibile leggere questo elenco e usarlo per controllare il provisioning e il deprovisioning delle risorse e dell'accesso all'interno dell'applicazione.

**Usare la piattaforma di identità Microsoft per il controllo degli accessi in base al ruolo.**  Gli amministratori e i proprietari dell'applicazione possono assegnare utenti e gruppi ai ruoli definiti quando si registra l'applicazione nella piattaforma di identità Microsoft. Le informazioni sui ruoli vengono inviate all'applicazione nei token di accesso e possono essere lette anche tramite l'API Microsoft Graph. Altre informazioni sull' [uso della piattaforma di identità Microsoft per l'autorizzazione](https://cloudblogs.microsoft.com/enterprisemobility/2014/12/18/azure-active-directory-now-with-group-claims-and-application-roles/).

### <a name="get-access-to-users-profile-calendar-email-contacts-files-and-more"></a>Ottenere l'accesso al profilo, al calendario, alla posta elettronica, ai contatti, ai file e altro ancora degli utenti

**Microsoft Identity Platform è il server di autorizzazione per Microsoft 365 e altri servizi aziendali Microsoft.**  Se si supporta la piattaforma di identità Microsoft per accedere all'applicazione o il supporto del collegamento degli account utente correnti a Azure AD account utente con OAuth 2,0, è possibile richiedere l'accesso in lettura e scrittura al profilo, al calendario, alla posta elettronica, ai contatti, ai file e ad altre informazioni dell'utente. In questo modo, è possibile inserire eventi nel calendario dell'utente oppure accedere in lettura e scrittura ai file in OneDrive. Altre informazioni sulle [api Microsoft 365](/graph/overview).

### <a name="promote-your-application-in-the-azure-and-microsoft-365-marketplaces"></a>Promuovere l'applicazione in Azure e Microsoft 365 Marketplace

**Promuovere l'applicazione per farla conoscere a milioni di organizzazioni che usano già Azure AD.**   Gli utenti che esplorano questi Marketplace usano già uno o più servizi cloud, di conseguenza rappresentano il pubblico di destinazione ideale per l'applicazione. Altre informazioni sulla promozione dell'applicazione in [Azure Marketplace](https://azure.microsoft.com/marketplace/partner-program/).

**Quando gli utenti si iscrivono per l'applicazione, verranno visualizzati nel pannello di accesso Azure AD e Microsoft 365 l'utilità di avvio delle app.**   Questo consente tornare all'applicazione in modo facile e veloce, migliorando così il coinvolgimento degli utenti. Altre informazioni sul [pannello di accesso di Azure AD](../user-help/my-apps-portal-end-user-access.md).

### <a name="secure-device-to-service-and-service-to-service-communication"></a>Comunicazione da dispositivo a servizio e da servizio a servizio sicura

**L'uso della piattaforma Microsoft Identity per la gestione delle identità di servizi e dispositivi riduce il codice che è necessario scrivere e consente di gestire l'accesso.**  I servizi e i dispositivi possono ottenere token dalla piattaforma di identità Microsoft usando OAuth e usare questi token per accedere alle API Web. Con la piattaforma di identità Microsoft è possibile evitare di scrivere codice di autenticazione complesso. Poiché le identità di servizi e dispositivi sono archiviate in Azure AD, il reparto IT può gestire le chiavi e le revoche in una posizione centralizzata, anziché in una posizione separata all'interno dell'applicazione.

## <a name="benefits-of-integration"></a>Vantaggi dell'integrazione

L'integrazione con la piattaforma di identità Microsoft offre vantaggi che non richiedono la scrittura di codice aggiuntivo.

### <a name="integration-with-enterprise-identity-management"></a>Integrazione con Enterprise Identity Management

**Conformità dell'applicazione ai criteri IT.**  Le organizzazioni integrano i sistemi di gestione delle identità aziendali con la piattaforma delle identità Microsoft. Pertanto, quando una persona lascia un'organizzazione, perderà automaticamente l'accesso all'applicazione senza che sia necessario eseguire ulteriori passaggi. Il reparto IT può gestire gli utenti che possono accedere all'applicazione e determinare i criteri di accesso necessari, ad esempio l'autenticazione a più fattori, riducendo così la necessità di scrivere codice per garantire la conformità con criteri aziendali complessi. Azure AD fornisce agli amministratori un log di controllo dettagliato con l'elenco degli utenti che hanno eseguito l'accesso all'applicazione e monitorare così l'utilizzo.

**Azure AD estende Active Directory nel cloud per consentire l'integrazione dell'applicazione con Active Directory.**   Molte organizzazioni in tutto il mondo usano Active Directory come sistema di gestione degli accessi e delle identità principale e richiedono pertanto che le applicazioni funzionino con Active Directory. L'integrazione con Azure AD integra l'app con Active Directory.

### <a name="advanced-security-features"></a>Funzionalità di sicurezza avanzate

**Autenticazione a più fattori.**  Microsoft Identity Platform fornisce l'autenticazione a più fattori nativa. Gli amministratori IT possono richiedere l'autenticazione a più fattori per l'accesso all'applicazione, evitando così di dover scrivere il codice per garantire questo tipo di supporto. Altre informazioni su [multi-factor authentication](https://azure.microsoft.com/documentation/services/multi-factor-authentication/).

**Rilevamento di attività di accesso con anomalie.**  La piattaforma di identità Microsoft elabora più di un miliardo di accessi al giorno, usando gli algoritmi di machine learning per rilevare attività sospette e inviare notifiche agli amministratori IT dei possibili problemi. Grazie al supporto per l'accesso alla piattaforma di identità Microsoft, l'applicazione ottiene il vantaggio di questa protezione. Altre informazioni sulla [visualizzazione del report di accesso di Azure Active Directory](../reports-monitoring/overview-reports.md).

**Accesso condizionale.**   Oltre all'autenticazione a più fattori, gli amministratori possono richiedere che vengano soddisfatte specifiche condizioni per consentire l'accesso degli utenti all'applicazione. Tra le condizioni che è possibile impostare, sono inclusi l'intervallo di indirizzi IP dei dispositivi client, l'appartenenza a gruppi specificati e lo stato del dispositivo usato per l'accesso. Altre informazioni su [Azure Active Directory l'accesso condizionale](../conditional-access/overview.md).

### <a name="easy-development"></a>Facile sviluppo

**Protocolli standard del settore.**   Microsoft si impegna a supportare gli standard del settore. La piattaforma Microsoft Identity supporta i protocolli standard del settore OAuth 2,0 e OpenID Connect 1,0. Altre informazioni sui [protocolli di autenticazione della piattaforma Microsoft Identity](active-directory-v2-protocols.md).

**Librerie open source.**   Per accelerare lo sviluppo, Microsoft fornisce librerie open source completamente supportate per le piattaforme e i linguaggi più diffusi. Il codice sorgente è concesso in licenza secondo i termini della licenza Apache, versione 2.0 con la libertà di personalizzarlo e di contribuire ai progetti. Ulteriori informazioni su [Microsoft Authentication Library (MSAL)](reference-v2-libraries.md).

### <a name="worldwide-presence-and-high-availability"></a>Presenza in tutto il mondo e disponibilità elevata

**Azure AD è distribuito nei data center di tutto il mondo ed è gestito e monitorato 24 ore al giorno.**  Azure AD è il sistema di gestione delle identità per Microsoft Azure e Microsoft 365 ed è distribuito in 28 Data Center in tutto il mondo. I dati della directory vengono replicati in almeno tre data center. I servizi di bilanciamento del carico globali garantiscono che l'utente acceda alla copia di Azure AD che contiene i dati dell'utente più vicina e reindirizza automaticamente le richieste ad altri data center nel caso in cui vengono rilevati problemi.

## <a name="next-steps"></a>Passaggi successivi

[Introduzione alla scrittura di codice](v2-overview.md#getting-started).

[Consentire agli utenti di accedere usando la piattaforma di identità Microsoft](./authentication-vs-authorization.md)