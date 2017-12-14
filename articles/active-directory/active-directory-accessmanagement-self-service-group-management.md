---
title: Configurazione della gestione degli accessi alle applicazioni self-service in Azure Active Directory | Microsoft Docs
description: Creare e gestire gruppi di sicurezza o gruppi di Office 365 in Azure Active Directory e richiedere l'appartenenza al gruppo di sicurezza o al gruppo di Office 365
services: active-directory
documentationcenter: 
author: curtand
manager: mtillman
editor: 
ms.assetid: 904d5c70-c34a-46c4-a9a7-d1efecf4821c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/07/2017
ms.author: curtand
ms.reviewer: kairaz.contractor
ms.custom: oldportal;it-pro;
ms.openlocfilehash: 4798c9f9b289f8fa089498398079565c35ccdd2a
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/11/2017
---
# <a name="set-up-azure-active-directory-for-self-service-group-management"></a>Configurare Azure Active Directory per la gestione dei gruppi self-service
Gli utenti possono creare e gestire gruppi di sicurezza o gruppi di Office 365 in Azure Active Directory (Azure AD). Gli utenti possono anche richiedere l'appartenenza a gruppi di sicurezza o gruppi di Office 365, che il proprietario del gruppo può quindi approvare o rifiutare. Il controllo giornaliero dell'appartenenza al gruppo può essere delegato a persone che conoscono il contesto aziendale pertinente. Le funzionalità di gestione self-service dei gruppi sono disponibili solo per i gruppi di sicurezza e i gruppi di Office 365, ma non per i gruppi di protezione abilitati alla posta o le liste di distribuzione.

La gestione dei gruppi self-service riguarda attualmente due scenari principali, ovvero gestione dei gruppi delegata e gestione dei gruppi self-service.

* **Gestione delegata dei gruppi** : un esempio è costituito da un amministratore che gestisce l'accesso a un'applicazione SaaS usata dall'azienda. In questo caso la gestione dei diritti di accesso diventa più onerosa e pertanto l'amministratore chiede al titolare dell'organizzazione di creare un nuovo gruppo. L'amministratore assegna l'accesso per l'applicazione al nuovo gruppo e aggiunge al gruppo tutte le persone che accedono già all'applicazione. Il titolare dell'organizzazione può quindi aggiungere altri utenti, per i quali il provisioning nell'applicazione viene effettuato automaticamente. Il titolare dell'organizzazione non deve attendere che l'amministratore gestisca l'accesso degli utenti. Se l'amministratore concede la stessa autorizzazione a un manager di un diverso gruppo aziendale, anche tale persona può gestire l'accesso dei propri utenti. Né il titolare dell'organizzazione né il manager possono visualizzare o gestire gli utenti dell'altro. L'amministratore può comunque visualizzare tutti gli utenti che hanno accesso all'applicazione e, se necessario, revocare i diritti di accesso.
* **Gestione self-service dei gruppi** Un esempio di questo scenario è costituito da due utenti che hanno entrambi siti di SharePoint Online configurati in modo indipendente e che vogliono concedere al team dell'altro l'accesso al proprio sito. A questo scopo, possono creare un gruppo in Azure AD che può essere selezionato da ognuno in SharePoint Online per consentire l'accesso al proprio sito. Se un utente desidera accedere, effettua la richiesta dal pannello di accesso e dopo aver ottenuto l'approvazione potrà accedere automaticamente a entrambi i siti di SharePoint Online. Se successivamente uno degli utenti decide che tutte le persone che accedono al sito devono poter accedere anche a una determinata applicazione SaaS, l'amministratore dell'applicazione SaaS può aggiungere i diritti di accesso per l'applicazione al sito di SharePoint Online. A questo punto, tutte le richieste approvate garantiranno l'accesso ai due siti di SharePoint Online e anche all'applicazione SaaS.

## <a name="make-a-group-available-for-user-self-service"></a>Rendere disponibile un gruppo per gli utenti in modalità self-service
1. Accedere all'[interfaccia di amministrazione di Azure AD](https://aad.portal.azure.com) con un account amministratore globale per la directory.
2. Selezionare **Utenti e gruppi** e quindi **Impostazioni di gruppo**.
3. Impostare **Gestione gruppi self-service abilitata** su **Sì**.
4. Impostare **Gli utenti possono creare gruppi di sicurezza** o **Gli utenti possono creare gruppi di Office 365** su **Sì**.
  * Quando queste impostazioni sono abilitate, tutti gli utenti nella directory possono creare nuovi gruppi di sicurezza e aggiungere membri a tali gruppi. Questi nuovi gruppi saranno visibili anche nel pannello di accesso per tutti gli altri utenti. Se consentito dall'impostazione dei criteri per il gruppo, gli altri utenti potranno creare richieste di partecipazione a questi gruppi. 
  * Quando queste impostazioni sono disabilitate, gli utenti non possono creare gruppi né modificare i gruppi esistenti di cui sono proprietari. Possono tuttavia gestire l'appartenenza a tali gruppi e approvare le richieste di partecipazione provenienti da altri utenti.

È anche possibile usare le opzioni **Utenti che possono gestire gruppi di sicurezza** e **Utenti che possono gestire gruppi di Office 365** per ottenere un controllo di accesso più granulare sulla gestione dei gruppi self-service per gli utenti. Quando l'opzione **Users can create groups** (Gli utenti possono creare gruppi) è abilitata, tutti gli utenti nel tenant possono creare nuovi gruppi e aggiungervi membri. Impostando l'opzione su **In parte**, si restringe la gestione dei gruppi a un gruppo limitato di utenti. Quando questa opzione è impostata su **In parte**, è necessario aggiungere gli utenti al gruppo SSGMSecurityGroupsUsers perché possano creare nuovi gruppi e aggiungervi membri. Impostando le opzioni **Utenti che possono usare la modalità self-service per i gruppi di sicurezza** e **Utenti che possono gestire gruppi di Office 365** su **Tutti**, si consente a tutti gli utenti nel tenant di creare nuovi gruppi.

È anche possibile usare l'opzione **Gruppo che può gestire i gruppi di sicurezza** o **Gruppi che possono gestire gruppi di Office 365** per specificare un singolo gruppo i cui membri possono usare la modalità self-service.

## <a name="next-steps"></a>Passaggi successivi
Questi articoli forniscono informazioni aggiuntive su Azure Active Directory.

* [Gestire l'accesso alle risorse tramite i gruppi di Azure Active Directory](active-directory-manage-groups.md)
* [Cmdlet di Azure Active Directory per la configurazione delle impostazioni di gruppo](active-directory-accessmanagement-groups-settings-cmdlets.md)
* [Indice di articoli per la gestione di applicazioni in Azure Active Directory](active-directory-apps-index.md)
* [Informazioni su Azure Active Directory](active-directory-whatis.md)
* [Integrare le identità locali con Azure Active Directory](active-directory-aadconnect.md)
