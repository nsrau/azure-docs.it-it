---
title: Visualizzare le applicazioni del tenant - Azure Active Directory | Microsoft Docs
description: Usare il portale di Azure per visualizzare le applicazioni nel tenant di Azure Active Directory (Azure AD).
services: active-directory
documentationcenter: ''
author: barbkess
manager: daveba
editor: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 07/25/2018
ms.author: barbkess
ms.reviewer: arvinh
ms.custom: it-pro
ms.openlocfilehash: ce998cd23ac3606ca9269e1e3316e6679fe0a7af
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/23/2019
ms.locfileid: "54462862"
---
# <a name="view-your-azure-active-directory-tenant-applications"></a>Visualizzare le applicazioni del tenant di Azure Active Directory

Questa guida introduttiva usa il portale di Azure per visualizzare le applicazioni nel tenant di Azure Active Directory (Azure AD).

## <a name="before-you-begin"></a>Prima di iniziare

Per osservare i risultati, è necessario avere almeno un'applicazione nel tenant di Azure AD. Per aggiungere un'applicazione, vedere la guida introduttiva [Aggiungere un'applicazione](add-application-portal.md).

Accedere al [portale di Azure](https://portal.azure.com) come amministratore globale per il tenant di Azure AD, amministratore applicazione cloud o amministratore applicazione.

## <a name="find-the-list-of-tenant-applications"></a>Trovare l'elenco delle applicazioni del tenant

Le applicazioni del tenant di Azure AD sono visualizzabili nella sezione **Applicazioni aziendali** del portale di Azure.

Per trovare le applicazioni del tenant:

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic su **Azure Active Directory** nel pannello di spostamento a sinistra. 

2. Nel pannello Azure Active Directory fare clic su **Applicazioni aziendali**. 

3. Nel menu a discesa **Tipo di applicazione** selezionare **Tutte le applicazioni** e fare clic su **Applica**. Verrà visualizzato un campione casuale delle applicazioni del tenant.

    ![App aziendali](media/view-applications-portal/open-enterprise-apps.png)
   
4. Per visualizzare altre applicazioni, fare clic su **Mostra altro** nella parte inferiore dell'elenco. A seconda del numero di applicazioni presenti nel tenant, potrebbe essere più semplice [cercare un'applicazione specifica](#search-for-a-tenant-application) anziché scorrere l'elenco.

## <a name="select-viewing-options"></a>Selezionare le opzioni di visualizzazione

In questa sezione selezionare le opzioni in base a ciò che si intende cercare.

1. È possibile visualizzare le applicazioni in base alle opzioni specificate per **Tipo di applicazione**, **Stato dell'applicazione** e **Visibilità applicazione**. 

    ![Opzioni per la ricerca](media/view-applications-portal/search-options.png)

2. In **Tipo di applicazione** scegliere una di queste opzioni:

    - **Applicazioni aziendali** mostra le applicazioni non Microsoft.
    - **Applicazioni Microsoft** mostra le applicazioni Microsoft.
    - **Tutte le applicazioni** mostra le applicazioni sia Microsoft che non Microsoft.

3. In **Stato dell'applicazione** scegliere **Qualsiasi**, **Disabilitato** o **Abilitato**. L'opzione **Qualsiasi** include sia le applicazioni disabilitate sia quelle abilitate.

4. In **Visibilità applicazione** scegliere **Qualsiasi** o **Nascosto**. L'opzione **Nascosto** mostra le applicazioni presenti nel tenant ma non visibili agli utenti.

5. Dopo aver scelto le opzioni desiderate, fare clic su **Applica**.
 

## <a name="search-for-a-tenant-application"></a>Cercare un'applicazione del tenant

Per cercare un'applicazione specifica:

1. Nel menu **Tipo di applicazione** selezionare **Tutte le applicazioni** e fare clic su **Applica**.

2. Immettere il nome dell'applicazione che si vuole trovare. Se l'applicazione è stata aggiunta al tenant di Azure AD, verrà visualizzata nei risultati della ricerca. Questo esempio mostra che l'applicazione GitHub non è stata aggiunta alle applicazioni del tenant.

    ![Cercare un'applicazione](media/view-applications-portal/search-for-tenant-application.png)

3. Provare a immettere le prime lettere del nome di un'applicazione.  Questo esempio mostra tutte le applicazioni il cui nome inizia con **Sales**.

    ![Eseguire la ricerca con un prefisso](media/view-applications-portal/search-by-prefix.png)

## <a name="next-steps"></a>Passaggi successivi

In questa guida introduttiva si è appreso come visualizzare le applicazioni nel tenant di Azure AD e come filtrare l'elenco delle applicazioni per tipo, stato e visibilità dell'applicazione. È stato anche illustrato come cercare un'applicazione specifica.

Dopo aver trovato l'applicazione cercata, si può continuare ad [aggiungere altre applicazioni al tenant](add-application-portal.md) oppure fare clic sull'applicazione per visualizzare o modificare le proprietà e le opzioni di configurazione. Ad esempio, è possibile configurare l'accesso Single Sign-On. 

> [!div class="nextstepaction"]
> [Configurare l'accesso Single Sign-On](configure-single-sign-on-portal.md)


