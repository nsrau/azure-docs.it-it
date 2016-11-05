---
title: Confronto tra le funzionalità per la gestione di identità esterne con Azure Active Directory | Microsoft Docs
description: Confronto tra la collaborazione B2B di Azure Active Directory, B2C e app multi-tenant per il supporto di autenticazione e autorizzazione per identità esterne.
services: active-directory
documentationcenter: ''
author: arvindsuthar
manager: cliffdi
editor: ''
tags: ''

ms.service: active-directory
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: identity
ms.date: 02/24/2016
ms.author: asuthar

---
# Confronto tra le funzionalità per la gestione di identità esterne con Azure Active Directory
Oltre a gestire l'accesso della forza lavoro mobile alle app SaaS, Azure Active Directory (Azure AD) consente all'organizzazione di condividere risorse con partner commerciali e distribuire applicazioni ad aziende e consumatori.

## Sviluppo di applicazioni per le aziende
Se si offre un servizio o un'applicazione, ad esempio un servizio di gestione delle retribuzioni, alle aziende, Azure AD offre la piattaforma delle identità che consente di sviluppare applicazioni in grado di integrarsi perfettamente con milioni di organizzazioni che hanno già configurato Azure AD come parte della distribuzione di Office 365 o altri servizi aziendali.

**Esempio:** un distributore di prodotti farmaceutici fornisce medicinali e sistemi informativi al settore sanitario. La società ha l'esigenza di offrire un'applicazione di analisi agli ambulatori medici e di consentire ai clienti di gestire le proprie identità. Il distributore sceglie Azure AD come piattaforma delle identità per l'applicazione di gestione degli ambulatori, fornendo identità di Azure AD ai clienti durante l'accesso, se necessario. Per altre informazioni, vedere [AGuida per gli sviluppatori di Azure Active Directory](active-directory-developers-guide.md).

## Abilitazione dell'accesso dei partner commerciali alle risorse aziendali
Se sono presenti partner commerciali o altri utenti all'esterno della società che devono accedere alle risorse aziendali, ad esempio un sito di SharePoint o il sistema ERP, Azure AD consente agli amministratori di concedere a utenti esterni, che possono esistere o non esistere in Azure AD, l'accesso Single Sign-On alle applicazioni aziendali. Ciò permette un miglioramento della sicurezza, perché gli utenti perdono l'accesso quando lasciano l'organizzazione partner e i criteri di accesso vengono controllati all'interno della propria organizzazione. Si ottiene anche una semplificazione dell'amministrazione, perché non è necessario gestire una directory per i partner esterni o relazioni federative per ogni partner.

**Esempio:** una società di creazione di immagini offre servizi di creazione di immagini a rivenditori e gestisce la più ampia rete di chioschi di stampa al dettaglio. La società sceglie Azure AD per consentire a migliaia di utenti presso i partner commerciali al dettaglio di usare le proprie credenziali per scaricare i materiali marketing più recenti del partner e riordinare forniture per l'elaborazione di foto dalla Extranet del fornitore aziendale. Per altre informazioni, vedere [Collaborazione B2B di Azure AD](active-directory-b2b-what-is-azure-ad-b2b.md).

## Sviluppo di applicazioni per i consumatori
Se è necessario pubblicare applicazioni online in modo sicuro ed economicamente conveniente, ad esempio nel caso di un negozio al dettaglio, per milioni di consumatori, Azure AD offre una piattaforma che consente l'accesso tramite social media e nome utente/password, inscrizione self-service con un marchio specifico e reimpostazione della password self-service per gli utenti dell'applicazione. Ciò semplifica le operazioni per i consumatori, riducendo al tempo stesso il carico per gli sviluppatori.

**Esempio:** la principale catena di negozi sportivi in franchising del mondo deve interagire direttamente con 450 milioni di fan. A questo scopo, ha creato un'app per dispositivi mobili usando Azure AD per l'autenticazione degli utenti e l'archiviazione dei profili. I fan ottengono una registrazione e un accesso semplificato tramite l'uso di account di social media come Facebook oppure possono usare nomi utente/password tradizionali per un'esperienza semplice su telefoni iOS, Android e Windows. L'uso della piattaforma consolidata di Azure AD come base ha consentito di ridurre significativamente il codice personalizzato, offrendo al gruppo di franchising un marchio personalizzato e riducendo i problemi relativi a sicurezza, violazione dei dati e ridimensionamento. Per altre informazioni, vedere [Azure AD B2C](https://azure.microsoft.com/documentation/services/active-directory-b2c/).

## Confronto delle funzionalità di Azure AD
Le complessità di ogni scenario già illustrato in questo articolo sono risolte da funzionalità disponibili in Azure AD. Questa tabella indica in modo chiaro le funzionalità più rilevanti per ogni scenario:

| **Prendere in considerazione questo prodotto...** | [App SaaS multi-tenant di Azure AD](active-directory-developers-guide.md) | [Collaborazione B2B di Azure AD](active-directory-b2b-what-is-azure-ad-b2b.md) | [Azure AD B2C](https://azure.microsoft.com/documentation/services/active-directory-b2c/) |
| --- | --- | --- | --- |
| **Se occorre fornire...** |Un servizio ad aziende |Accesso partner alle app |Un servizio ai consumatori |
| **Scenario simile a...** |Distributore di prodotti farmaceutici |Società di creazione di immagini |Catena di negozi sportivi in franchising |
| **App da distribuire per...** |Gestione di ambulatori medici |Extranet di fornitori |Fan di calcio |
| **Destinatari...** |Ambulatori medici |Partner commerciali approvati |Chiunque abbia un indirizzo di posta elettronica |
| **Accessibile tramite...** |Consenso dell'amministratore dei clienti |Invito del proprio amministratore |Iscrizione del consumatore |

<!---HONumber=AcomDC_0224_2016-->