---
title: Come ottenere un tenant di Azure AD | Microsoft Docs
description: Come ottenere un tenant di Azure Active Directory per la registrazione e la creazione di applicazioni.
services: active-directory
documentationcenter: 
author: dstrockis
manager: mbaldwin
editor: 
ms.assetid: 1f4b24eb-ab4d-4baa-a717-2a0e5b8d27cd
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 09/28/2015
ms.author: dastrock
ms.custom: aaddev
ms.translationtype: Human Translation
ms.sourcegitcommit: c300ba45cd530e5a606786aa7b2b254c2ed32fcd
ms.openlocfilehash: b4232f2d27624e44a720047ad44991059316413e
ms.contentlocale: it-it
ms.lasthandoff: 04/14/2017


---
# <a name="how-to-get-an-azure-active-directory-tenant"></a>Come ottenere un tenant di Azure Active Directory
In Azure Active Directory (Azure AD), un [tenant](https://msdn.microsoft.com/library/azure/jj573650.aspx#BKMK_WhatIsAnAzureADTenant) rappresenta un'organizzazione.  Si tratta di un'istanza dedicata del servizio Azure AD che l'organizzazione riceve al momento dell'iscrizione a un servizio cloud Microsoft, ad esempio Azure, Microsoft Intune o Office 365 e che diventa di sua proprietà.  Ogni tenant di Azure AD è distinto e separato dagli altri tenant di Azure AD.  

Un tenant ospita gli utenti di un'azienda e le informazioni su di essi, ad esempio password dati del profilo utente, autorizzazioni e così via.  Contiene anche gruppi, applicazioni e altre informazioni relative all'azienda e alla sicurezza.

Per consentire agli utenti di Azure AD di accedere a un'applicazione, è necessario registrarla nel proprio tenant.  La pubblicazione di un'applicazione in un tenant di Azure AD è **assolutamente gratuita**.  Infatti, la maggior parte degli sviluppatori crea più tenant e applicazioni a fini di sperimentazione, sviluppo, staging e test.  Le organizzazioni che si iscrivono all'applicazione e che la utilizzano, possono facoltativamente scegliere se acquistare le licenze, nel caso in cui intendano usufruire delle funzionalità avanzate della directory.

Come ottenere un tenant di Azure AD  La procedura può variare nei casi in cui:

* [Si ha un abbonamento a Office 365](#use-an-existing-office-365-subscription)
* [Si ha una sottoscrizione di Azure precedente, associata a un account Microsoft](#use-an-msa-azure-subscription)
* [Si ha una sottoscrizione di Azure precedente, associata a un account aziendale](#use-an-organizational-azure-subscription)
* [Non si ha nessuna delle sottoscrizioni precedenti e si vuole iniziare da zero](#start-from-scratch)

## <a name="use-an-existing-office-365-subscription"></a>Usare una sottoscrizione di Office 365 esistente
Se si ha una sottoscrizione di Office 365 esistente, si ha già un tenant di Azure AD. È possibile accedere al [portale di Azure](https://portal.azure.com) con l'account Office 365 e iniziare a usare Azure AD.

## <a name="use-an-msa-azure-subscription"></a>Usare una sottoscrizione di Azure associata a un account Microsoft
Se in precedenza è stata creata una sottoscrizione di Azure usando il proprio account Microsoft personale, si ha già un tenant.  Quando si accede al [portale di Azure](https://portal.azure.com), si esegue automaticamente l'accesso al tenant predefinito. È possibile usare questo tenant in base alle esigenze, ma potrebbe essere necessario dover creare un account amministratore aziendale.

A tale scopo, seguire questa procedura.  In alternativa, è possibile creare un nuovo tenant e creare un amministratore in quel tenant seguendo una procedura simile.

1. Accedere al [portale di Azure](https://portal.azure.com) con l'account personale.
2. Passare alla sezione "Azure Active Directory" del portale, che si trova nella barra di spostamento a sinistra in **Altri servizi**.
3. Viene eseguito automaticamente l'accesso alla "directory predefinita". In caso contrario, è possibile passare a un'altra directory facendo clic sul nome dell'account nell'angolo superiore destro.
4. Nella sezione **Attività rapide** scegliere **Aggiungere un utente**.
5. Nel modulo Aggiungi utente specificare le informazioni seguenti:

   * Nome: scegliere un valore appropriato
   * Nome utente: scegliere un nome utente per questo amministratore
   * Profilo: specificare i valori appropriati per nome, cognome, posizione e reparto
   * Ruolo: Amministratore globale
6. Una volta completato il modulo per l'aggiunta per l'utente e ricevuta la password temporanea per il nuovo utente amministrativo, assicurarsi di prendere nota della password in quanto sarà necessario eseguire l'accesso con questo nuovo utente per cambiare la password. È anche possibile inviare la password direttamente all'utente, usando un indirizzo di posta elettronica alternativo.
7. Fare clic su **Crea** per creare il nuovo utente.
8. Per modificare la password temporanea, accedere a [https://login.microsoftonline.com](https://login.microsoftonline.com) con questo nuovo account utente e modificare la password quando richiesto.

## <a name="use-an-organizational-azure-subscription"></a>Usare una sottoscrizione di Azure aziendale
Se in precedenza è stata creata una sottoscrizione di Azure usando il proprio account aziendale, si ha già un tenant.  Nel [Portale di Azure](https://portal.azure.com) dovrebbe essere visualizzato un tenant quando si passa a "Altri servizi" e "Azure Active Directory".  È possibile usare questo tenant in base alle esigenze.

## <a name="start-from-scratch"></a>Iniziare da zero
Se quanto descritto finora è incomprensibile, non è il caso di preoccuparsi.  Passare a [https://account.windowsazure.com/organization](https://account.windowsazure.com/organization) per iscriversi ad Azure con un nuovo account aziendale.  Al termine della procedura verrà creato il proprio tenant di Azure AD con il nome di dominio scelto durante l'iscrizione.  Per trovare il tenant nel [portale di Azure](https://portal.azure.com), passare ad "Azure Active Directory" nella barra di spostamento a sinistra.

Durante la procedura di iscrizione ad Azure, verrà richiesto di indicare i dettagli della carta di credito.  Non c'è nulla di cui preoccuparsi, la pubblicazione di applicazioni in Azure AD o la creazione di nuovi tenant non vengono addebitate.

