---
title: Condivisione esterna di Office 365 e Collaborazione B2B di Azure Active Directory | Documentazione Microsoft
description: Informazioni sul mapping delle attestazioni per Collaborazione B2B in Azure Active Directory
services: active-directory
documentationcenter: 
author: sasubram
manager: femila
editor: 
tags: 
ms.assetid: 
ms.service: active-directory
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: identity
ms.date: 02/06/2017
ms.author: sasubram
translationtype: Human Translation
ms.sourcegitcommit: d3f68340592d9032999ecb5cc16ac1fedcce4c33
ms.openlocfilehash: 716d19be09085db91c17d7ed76422e05d164d137


---

# <a name="office-365-external-sharing-and-azure-active-directory-b2b-collaboration"></a>Condivisione esterna di Office 365 e Collaborazione B2B di Azure Active Directory

La condivisione esterna in Office&365;, con OneDrive, SharePoint Online, gruppi unificati e così via, e quella tramite Collaborazione B2B di Azure Active Directory (Azure AD) tecnicamente si equivalgono. Tutti i tipi di condivisione esterna, ad eccezione di OneDrive/SharePoint Online, inclusi gli **utenti guest in gruppi unificati** fanno già uso dell'API di invito di Collaborazione B2B di Azure AD per la condivisione.

OneDrive/SharePoint Online (ODSP) ha una gestione degli inviti separata perché il relativo supporto per la condivisione esterna è stato avviato prima che fosse supportata implicitamente come parte dell'infrastruttura di Azure AD. Nel tempo la condivisione esterna di ODSP ha accumulato diverse funzionalità e milioni di utenti, che fanno uso del modello di condivisione incorporato nel prodotto. Si sta lavorando all'onboarding di ODSP nelle API di invito di Collaborazione B2B di Azure AD, a cui si fa riferimento in questa documentazione, per unificare tutti i vantaggi end-to-end e aggiungervi tutte le innovazioni alle esperienze di Azure AD. Nel frattempo, rimangono alcune sottili differenze di funzionamento tra la condivisione esterna di ODSP quella di Collaborazione B2B di Azure AD:

1. ODSP aggiunge l'utente alla directory dopo che l'utente ha riscattato l'invito. Fino a quando l'utente non riscatta l'invito, quindi, non viene effettivamente visualizzato nel portale di Azure AD. Se nel frattempo un utente viene invitato da un altro sito, viene generato un nuovo invito. Tuttavia, quando si usa Collaborazione B2B, l'utente viene aggiunto immediatamente all'invito in modo che sia visualizzato ovunque.

2. L'esperienza di riscatto in ODSP è diversa da quella di Collaborazione B2B.

3. Dopo che l'utente ha riscattare l'invito, le esperienze sono simili.

4. È possibile selezionare gli utenti invitati di Collaborazione B2B dalle finestre di dialogo di condivisione di ODSP. Dopo aver riscattato l'invito, gli utenti invitati di ODSP vengono visualizzati anche in Azure AD.

5. Per usare la condivisione esterna in ODSP insieme a Collaborazione B2B in modo gestito e controllati da un amministratore, impostare la condivisione esterna di ODSP in modo da **consentire la condivisione solo con gli utenti esterni già presenti nella directory**. Gli utenti possono accedere a siti condivisi esternamente e scegliere tra i collaboratori esterni che l'amministratore ha aggiunto. L'amministratore può aggiungere i collaboratori esterni tramite le API di invito di Collaborazione B2B.

![impostazione di condivisione esterna di ODSP](media/active-directory-b2b-o365-external-user/odsp-sharing-setting.png)

## <a name="next-steps"></a>Passaggi successivi

Vedere gli altri articoli su Azure AD B2B Collaboration.

* [Che cos'è Azure AD B2B Collaboration?](active-directory-b2b-what-is-azure-ad-b2b.md)
* [Proprietà dell'utente di Collaborazione B2B](active-directory-b2b-user-properties.md)
* [Aggiunta di un utente di Collaborazione B2B a un ruolo](active-directory-b2b-add-guest-to-role.md)
* [Delegare gli inviti a Collaborazione B2B](active-directory-b2b-delegate-invitations.md)
* [Gruppi dinamici e Collaborazione B2B](active-directory-b2b-dynamic-groups.md)
* [Codici ed esempi di PowerShell per Collaborazione B2B](active-directory-b2b-code-samples.md)
* [Configurare app SaaS per Collaborazione B2B](active-directory-b2b-configure-saas-apps.md)
* [Token utente per Collaborazione B2B](active-directory-b2b-user-token.md)
* [Mapping delle attestazioni utente per Collaborazione B2B](active-directory-b2b-claims-mapping.md)
* [Limitazioni correnti di Collaborazione B2B](active-directory-b2b-current-limitations.md)



<!--HONumber=Feb17_HO1-->


