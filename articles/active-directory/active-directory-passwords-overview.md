---
title: 'Azure AD: panoramica per la reimpostazione password self-service | Documentazione Microsoft'
description: "Utilità della reimpostazione della password self-service di Azure AD per l'organizzazione"
services: active-directory
documentationcenter: 
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: sahenry
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/11/2018
ms.author: joflore
ms.custom: it-pro
ms.openlocfilehash: 074a22d0d3d6f4218be431409dcb0e516d226335
ms.sourcegitcommit: 562a537ed9b96c9116c504738414e5d8c0fd53b1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/12/2018
---
# <a name="azure-ad-self-service-password-reset-for-the-it-professional"></a>Reimpostazione self-service delle password di Azure AD per i professionisti IT

Con la reimpostazione self-service della password di Azure Active Directory (Azure AD), gli utenti possono reimpostare le password in autonomia quando e dove lo desiderano. Allo stesso tempo, gli amministratori possono controllare come viene reimpostata la password di un utente. Gli utenti non devono più contattare l'help desk per reimpostare la password. La reimpostazione self-service della password di Azure AD include:

* **Modifica self-service della password**: l'utente conosce la password, ma vuole cambiarla.
* **Reimpostazione self-service della password**: l'utente non riesce ad accedere e vuole reimpostare la password usando uno o più dei seguenti metodi di autenticazione convalidati:
   * Invio di un SMS a un telefono cellulare convalidato.
   * Chiamata a un numero convalidato di telefono cellulare o dell'ufficio.
   * Invio di un messaggio di posta elettronica a un account di posta elettronica secondario convalidato.
   * Risposte alle domande di sicurezza.
* **Sblocco self-service dell'account**: l'utente non riesce ad accedere con la sua password ed è stato bloccato. L'utente desidera sbloccare l'account senza l'intervento dell'amministratore tramite i suoi metodi di autenticazione.

## <a name="why-choose-azure-ad-sspr"></a>Perché scegliere la reimpostazione self-service della password di Azure AD

La reimpostazione self-service della password di Azure AD consente di:

* **Ridurre i costi**, perché le reimpostazioni delle password tramite help desk rappresentano in genere il 20% delle chiamate di supporto di un'organizzazione IT. 
* **Migliorare l'esperienza utente finale** e **ridurre i volumi all'help desk** concedendo agli utenti finali la possibilità di risolvere i problemi con le loro password. Non è necessario chiamare l'help desk o aprire una richiesta di supporto.
* **Mobilità dell'unità** poiché gli utenti possono reimpostare le password da qualsiasi posizione.
* **Conservare il controllo** dei criteri di sicurezza. Gli amministratori possono continuare ad applicare i criteri che hanno attualmente.

Se si è pronti, è possibile iniziare a usare la reimpostazione self-service della password di Azure AD usando le [istruzioni per l'avvio rapido](active-directory-passwords-getting-started.md). È possibile consentire agli utenti di reimpostare rapidamente le loro password.

## <a name="azure-ad-sspr-availability"></a>Disponibilità della reimpostazione self-service della password di Azure AD

La reimpostazione self-service della password di Azure AD è disponibile in tre livelli a seconda della sottoscrizione:

* **Azure AD Free**: gli amministratori di reti solo cloud possono reimpostare le loro password personali.
* **Azure AD Basic** o una **sottoscrizione di Office365 a pagamento**: gli utenti di reti solo cloud possono reimpostare le loro password personali.
* **Azure AD Premium**: qualsiasi utente o amministratore, inclusi gli utenti di reti solo cloud, federati o sincronizzati tramite password, può reimpostare la sua password personale. Le password locali richiedono che sia abilitato il writeback delle password.

## <a name="azure-ad-pricing-sla-updates-and-roadmap"></a>Prezzi, contratto di servizio, aggiornamenti e guida di orientamento di Azure AD

Informazioni più dettagliate su licenze, prezzi e piani futuri sono disponibili nei siti seguenti:

* [Dettagli sui prezzi di Azure AD](https://azure.microsoft.com/pricing/details/active-directory/)
* [Prezzi di Office 365](https://products.office.com/compare-all-microsoft-office-products?tab=2)
* [Contratti di servizio di Azure](https://azure.microsoft.com/support/legal/sla/)
* [Contratto di servizio per Microsoft Online Services](http://go.microsoft.com/fwlink/?LinkID=272026&clcid=0x409)
* [Aggiornamenti di Azure](https://azure.microsoft.com/updates/)
* [Roadmap per Azure](https://www.microsoft.com/cloud-platform/roadmap-recently-available)

## <a name="next-steps"></a>Passaggi successivi

* Quando si è pronti per iniziare a usare la reimpostazione della password self-service, vedere [Configurare la reimpostazione self-service della password di Azure AD](active-directory-passwords-getting-started.md).
* Pianificare la distribuzione della reimpostazione self-service della password seguendo le istruzioni disponibili nella [Guida all'implementazione](active-directory-passwords-best-practices.md).
* [Reimpostare o modificare la password](active-directory-passwords-update-your-own-password.md).
* [Registrarsi per la reimpostazione della password self-service](active-directory-passwords-reset-register.md).
