---
title: 'Azure AD: panoramica per la reimpostazione password self-service | Documentazione Microsoft'
description: "Utilità della reimpostazione della password self-service di Azure AD per l'organizzazione"
services: active-directory
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
ms.reviewer: sahenry
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2017
ms.author: joflore
ms.custom: it-pro
ms.openlocfilehash: f577ee95c34f9bc3065c026bc943d30622b9d658
ms.sourcegitcommit: dfd49613fce4ce917e844d205c85359ff093bb9c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/31/2017
---
# <a name="azure-ad-self-service-password-reset-for-the-it-professional"></a>Reimpostazione self-service delle password di Azure AD per i professionisti IT

La reimpostazione della password self-service di Azure Active Directory (Azure AD) consente agli utenti di reimpostare le password in modo autonomo quando e dove necessario, offrendo agli amministratori il controllo della modalità di reimpostazione. Gli utenti non devono più contattare il supporto tecnico per reimpostare la password.

* **Modifica della password self-service**: l'utente conosce la password, ma intende modificarla.
* **Reimpostazione della password self-service**: l'utente non è in grado di accedere e vuole reimpostare la password usando uno o più dei seguenti metodi di autenticazione convalidati.
   * Messaggio di testo su un telefono cellulare convalidato
   * Chiamata a un telefono cellulare o dell'ufficio
   * Messaggio di posta elettronica a un account di posta elettronica secondario convalidato
   * Risposte alle domande di sicurezza
* **Sblocco dell'account self-service**: l'utente non è in grado di accedere con la password, è stato bloccato e vuole sbloccare l'account senza l'intervento dell'amministratore, usando i metodi di autenticazione.

## <a name="why-choose-azure-ad-self-service-password-reset"></a>Perché scegliere la Reimpostazione self-service della password di Azure AD

* **Ridurre i costi**, perché la reimpostazione delle password tramite il supporto tecnico rappresenta in genere il 20% delle chiamate di assistenza di un'organizzazione IT. 
* **Migliorare le esperienze degli utenti finali** e **ridurre i volumi di supporto tecnico** fornendo agli utenti la possibilità di risolvere i problemi relativi alla propria password in una sola volta senza chiamare un supporto tecnico o aprire una richiesta di supporto.
* **Mobilità dell'unità** poiché gli utenti possono reimpostare le password da qualsiasi posizione.
* **Conservare il controllo** dei criteri di sicurezza. Gli amministratori possono continuare ad applicare i criteri che hanno attualmente.

Se si è pronti, è possibile iniziare a usare la SSPR di Azure AD usando la nostra [guida introduttiva di avvio rapido](active-directory-passwords-getting-started.md) e vedere come gli utenti reimpostano rapidamente le proprie password.

## <a name="azure-ad-self-service-password-reset-availability"></a>Disponibilità della Reimpostazione self-service della password di Azure AD

La Reimpostazione self-service delle password di Azure AD è disponibile in tre livelli a seconda della sottoscrizione.

* **Azure AD Free**: gli amministratori di reti solo cloud possono reimpostare la password personale.
* **Azure AD Basic** o una **sottoscrizione di Office365 a pagamento**: gli utenti solo cloud possono reimpostare la password.
* **Azure AD Premium** - qualsiasi utente o amministratore, inclusi gli utenti di reti solo cloud, federati o sincronizzati tramite password, può reimpostare la password personale. Le password locali richiedono che sia abilitato il writeback delle password.

## <a name="azure-ad-pricing-sla-updates-and-roadmap"></a>Prezzi, contratto di servizio, aggiornamenti e guida di orientamento di Azure AD

Informazioni più dettagliate su licenze, prezzi e piani futuri sono disponibili nei siti seguenti.

* [**Dettagli sui prezzi di Azure AD**](https://azure.microsoft.com/pricing/details/active-directory/)
* [**Prezzi di Office 365**](https://products.office.com/compare-all-microsoft-office-products?tab=2)
* [**Contratti di servizio di Azure**](https://azure.microsoft.com/support/legal/sla/)
* [**Contratto di servizio per Microsoft Online Services**](http://go.microsoft.com/fwlink/?LinkID=272026&clcid=0x409)
* [**Aggiornamenti di Azure**](https://azure.microsoft.com/updates/)
* [**Guida di orientamento di Azure**](https://www.microsoft.com/cloud-platform/roadmap-recently-available)

## <a name="next-steps"></a>Passaggi successivi

* Quando si è pronti per iniziare a usare la reimpostazione della password self-service, vedere [Configurare la reimpostazione della password self-service di Azure AD](active-directory-passwords-getting-started.md).
* Pianificare la distribuzione della reimpostazione della password self-service seguendo le istruzioni disponibili nella [**Guida all'implementazione**](active-directory-passwords-best-practices.md).
* [Reimpostare o modificare la password](active-directory-passwords-update-your-own-password.md).
* [Registrarsi per la reimpostazione della password self-service](active-directory-passwords-reset-register.md).