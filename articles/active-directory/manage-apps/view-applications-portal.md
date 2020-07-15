---
title: "Avvio rapido: Visualizzare l'elenco delle applicazioni che usano il tenant di Azure Active Directory (Azure AD) per la gestione delle identità"
description: In questo argomento di avvio rapido si usa il portale di Azure per visualizzare l'elenco delle applicazioni registrate per l'uso del tenant di Azure Active Directory (Azure AD) per la gestione delle identità.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: quickstart
ms.date: 04/09/2019
ms.author: kenwith
ms.reviewer: arvinh
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 249cdbef6214eb8c66da841d9eae817bcf794996
ms.sourcegitcommit: bcb962e74ee5302d0b9242b1ee006f769a94cfb8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/07/2020
ms.locfileid: "86055393"
---
# <a name="quickstart-view-the-list-of-applications-that-are-using-your-azure-active-directory-azure-ad-tenant-for-identity-management"></a>Avvio rapido: Visualizzare l'elenco delle applicazioni che usano il tenant di Azure Active Directory (Azure AD) per la gestione delle identità

Iniziare a usare Azure AD come sistema di gestione delle identità e degli accessi (IAM) per le applicazioni usate dall'organizzazione. In questo argomento di avvio rapido verranno visualizzate le applicazioni, note anche come app, che sono già configurate per usare il tenant di Azure AD come provider di identità.

## <a name="prerequisites"></a>Prerequisiti

Per visualizzare le applicazioni che sono state registrate nel tenant di Azure AD, è necessario:

- Un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

>[!IMPORTANT]
>Per testare i passaggi di questo argomento di avvio rapido, è consigliabile usare un ambiente non di produzione.

## <a name="find-the-list-of-applications-in-your-tenant"></a>Trovare l'elenco di applicazioni nel tenant

Le applicazioni registrate nel tenant di Azure AD sono visualizzabili nella sezione **Applicazioni aziendali** del portale di Azure.

Per visualizzare le applicazioni registrate nel tenant:

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Nel riquadro di spostamento a sinistra selezionare **Azure Active Directory**.
3. Nel riquadro **Azure Active Directory** selezionare **Applicazioni aziendali**.
4. Nel menu a discesa **Tipo di applicazione** selezionare **Tutte le applicazioni** e scegliere **Applica**. Verrà visualizzato un campione casuale delle applicazioni del tenant.
5. Per visualizzare altre applicazioni, selezionare **Carica altro** nella parte inferiore dell'elenco. Se nel tenant sono presenti numerose applicazioni, potrebbe essere più semplice cercare un'applicazione specifica invece di scorrere l'elenco. La ricerca di un'applicazione specifica viene illustrata più avanti in questo argomento di avvio rapido.

## <a name="select-viewing-options"></a>Selezionare le opzioni di visualizzazione

Selezionare le opzioni in base a ciò che si intende cercare.

1. È possibile visualizzare le applicazioni per **Tipo di applicazione**, **Stato dell'applicazione** e **Visibilità applicazione**.
2. In **Tipo di applicazione** scegliere una di queste opzioni:
    - **Applicazioni aziendali** mostra le applicazioni non Microsoft.
    - **Applicazioni Microsoft** mostra le applicazioni Microsoft.
    - **Tutte le applicazioni** mostra le applicazioni sia Microsoft che non Microsoft.
3. In **Stato dell'applicazione** scegliere **Qualsiasi**, **Disabilitato** o **Abilitato**. L'opzione **Qualsiasi** include sia le applicazioni disabilitate sia quelle abilitate.
4. In **Visibilità applicazione** scegliere **Qualsiasi** o **Nascosto**. L'opzione **Nascosto** mostra le applicazioni presenti nel tenant ma non visibili agli utenti.
5. Dopo aver scelto le opzioni desiderate, selezionare **Applica**.

## <a name="search-for-an-application"></a>Cercare un'applicazione

Per cercare un'applicazione specifica:

1. Nel menu **Tipo di applicazione** selezionare **Tutte le applicazioni** e scegliere **Applica**.
2. Immettere il nome dell'applicazione che si vuole trovare. Se l'applicazione è stata aggiunta al tenant di Azure AD, viene visualizzata nei risultati della ricerca. Questo esempio mostra che l'applicazione GitHub non è stata aggiunta alle applicazioni del tenant.
    ![L'esempio illustra che un'app non è ancora stata aggiunta al tenant](media/view-applications-portal/search-for-tenant-application.png)
3. Provare a immettere le prime lettere del nome di un'applicazione. Questo esempio mostra tutte le applicazioni il cui nome inizia con **Sales**.
    ![L'esempio mostra tutte le app che iniziano con le vendite](media/view-applications-portal/search-by-prefix.png)

## <a name="next-steps"></a>Passaggi successivi

In questo argomento di avvio rapido si è appreso come visualizzare le applicazioni nel tenant di Azure AD. Si è appreso come filtrare l'elenco delle applicazioni per tipo, stato e visibilità dell'applicazione. È stato anche illustrato come cercare un'applicazione specifica.

- [Aggiungere un'app](add-application-portal.md)
- [Configurare un'app](add-application-portal-configure.md)
- [Configurare Single Sign-On](add-application-portal-setup-sso.md)
- [Eliminare un'app](delete-application-portal.md)
