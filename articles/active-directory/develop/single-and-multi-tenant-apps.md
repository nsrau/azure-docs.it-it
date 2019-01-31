---
title: App a tenant singolo e multi-tenant in Azure Active Directory
description: Informazioni sulle funzionalità e le differenze tra le app a tenant singolo e multi-tenant in Azure AD.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/24/2018
ms.author: celested
ms.reviewer: justhu
ms.custom: aaddev
ms.openlocfilehash: 1e9509cfe94153eb7820023075495db75c6a3b9e
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/28/2019
ms.locfileid: "55097028"
---
# <a name="tenancy-in-azure-active-directory"></a>Tenancy in Azure Active Directory

Azure Active Directory (Azure AD) organizza gli oggetti come utenti e app in gruppi chiamati *tenant*. I tenant consentono a un amministratore di impostare criteri per gli utenti all'interno dell'organizzazione e le app di proprietà dell'organizzazione per soddisfare i criteri operativi e di sicurezza. 

## <a name="who-can-sign-in-to-your-app"></a>Chi può accedere all'applicazione?

In fase di sviluppo delle app, gli sviluppatori possono scegliere di configurare un'app come multi-tenant o a tenant singolo durante la registrazione dell'app nel [portale di Azure](https://portal.azure.com).
* Le app a tenant singolo sono disponibili solo nel tenant in cui sono state registrate, noto anche come tenant home.
* Le app multi-tenant sono disponibili per gli utenti sia nel rispettivo tenant home che in altri tenant.

Nel portale di Azure è possibile configurare l'app come multi-tenant o a tenant singolo impostando i destinatari come indicato di seguito.

| Audience | Tenant singolo/multi-tenant | Utenti autorizzati a effettuare l'accesso | 
|----------|--------| ---------|
| Account solo in questa directory | Tenant singolo | Tutti gli account utente e guest della directory possono usare l'applicazione o l'API.<br>*Usare questa opzione se i destinatari sono interni all'organizzazione.* |
| Account in qualsiasi directory di Azure AD | Multi-tenant | Tutti gli utenti e gli utenti guest con un account Microsoft aziendale o dell'istituto di istruzione possono usare l'applicazione o l'API. Sono inclusi gli istituti di istruzione e le aziende che usano Office 365.<br>*Usare questa opzione se i destinatari sono clienti aziendali o di istituti di istruzione.* |
| Account in qualsiasi directory di Azure AD e account Microsoft personali (ad esempio Skype, Xbox, Outlook.com) | Multi-tenant | Tutti gli utenti con un account aziendale o dell'istituto di istruzione o con un account Microsoft personale possono usare l'applicazione o l'API. Sono inclusi gli istituiti di istruzione e le aziende che usano Office 365, nonché gli account personali usati per accedere a servizi come Xbox e Skype.<br>*Usare questa opzione per rivolgersi alla gamma più ampia di account Microsoft.* | 

## <a name="best-practices-for-multi-tenant-apps"></a>Procedure consigliate per app multi-tenant

Creare ottime app multi-tenant può risultare difficile a causa del numero di diversi criteri che gli amministratori IT possono impostare nei tenant. Se si sceglie di creare un'app multi-tenant, seguire queste procedure consigliate:

* Testare l'app in un tenant che ha configurato [criteri di accesso condizionale](conditional-access-dev-guide.md).
* Seguire il principio dell'accesso utente con privilegi minimi per assicurarsi che l'app richieda solo le autorizzazioni effettivamente necessarie. Evitare di richiedere autorizzazioni che necessitino del consenso amministratore, perché in alcune organizzazioni potrebbe comportare il mancato acquisto dell'app. 
* Specificare nomi e descrizioni appropriati per tutte le autorizzazioni esposte come parte dell'app. In questo modo, utenti e amministratori sanno esattamente quello che stanno accettando quando tentano di usare le API dell'app. Per altre informazioni, vedere la sezione delle procedure consigliate nella [guida alle autorizzazioni](v1-permissions-and-consent.md).

## <a name="next-steps"></a>Passaggi successivi

* [Come convertire un'app in un'app multi-tenant](howto-convert-app-to-be-multi-tenant.md)
