---
title: Campi di registrazione portale di Azure per app personalizzate
description: Linee guida per la registrazione di un'applicazione sviluppata personalizzata con Azure AD
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: ryanwi
ms.collection: M365-identity-device-management
ms.openlocfilehash: ded0ba36a8eef7adb1be5b5f07408b9f79359f44
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/10/2019
ms.locfileid: "74965637"
---
# <a name="azure-portal-registration-fields-for-custom-developed-apps"></a>Campi di registrazione portale di Azure per app personalizzate

Questo articolo include una breve descrizione di tutti i campi disponibili nel modulo di registrazione dell'applicazione del [Portale di Azure](https://portal.azure.com).

## <a name="register-a-new-application"></a>Registrare una nuova applicazione

-   Per registrare una nuova applicazione, passare al [Portale di Azure](https://portal.azure.com).

-   Nel riquadro di spostamento sinistro fare clic su **Azure Active Directory**.

-   Scegliere **Registrazioni per l'app** e fare clic su **Aggiungi**.

-   Si apre il modulo di registrazione dell'applicazione.

## <a name="fields-in-the-application-registration-form"></a>Campi nel modulo di registrazione dell'applicazione

| Campo            | Description                                                                              |
|------------------|------------------------------------------------------------------------------------------|
| name             | Il nome dell'applicazione. Deve essere minimo di quattro caratteri.                |
| Tipi di account supportati| Consente di selezionare gli account che l'applicazione deve supportare: solo gli account in questa directory dell'organizzazione, gli account in qualsiasi directory organizzativa o gli account in qualsiasi directory organizzativa e account Microsoft personali.  |
| URI di reindirizzamento (facoltativo) | Selezionare il tipo di app che si sta compilando, il client **Web** o **pubblico (Mobile & desktop)** e quindi immettere l'URI di reindirizzamento (o URL di risposta) per l'applicazione. Per le applicazioni Web, specificare l'URL di base dell'app. Ad esempio, http://localhost:31544 potrebbe essere l'URL per un'app Web eseguita sul computer locale. Gli utenti possono usare questo URL per accedere a un'applicazione client Web. Per le applicazioni client pubbliche, specificare l'URI usato da Azure AD per restituire le risposte dei token. Immettere un valore specifico per l'applicazione, ad esempio myapp://auth. Per visualizzare esempi specifici per le applicazioni Web o native, consultare le [guide introduttive](https://docs.microsoft.com/azure/active-directory/develop).|

Dopo aver compilato i campi sopra indicati, l'applicazione viene registrata nella portale di Azure e si viene reindirizzati alla pagina Panoramica dell'applicazione. Le pagine di impostazioni nel riquadro sinistro in **Gestisci** hanno più campi per personalizzare l'applicazione. Le tabelle seguenti descrivono tutti i campi. Verrà visualizzato solo un subset di questi campi, a seconda che sia stata creata un'applicazione Web o un'applicazione client pubblica.

### <a name="overview"></a>Panoramica

| Campo           | Description        |
|-----------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ID applicazione  | Quando si registra un'applicazione, Azure AD assegna un ID all'applicazione. L'ID dell'applicazione può essere usato per identificare in modo univoco l'applicazione nelle richieste di autenticazione ad Azure AD, nonché per accedere a risorse, ad esempio l'API Graph.                                                          |
| URI dell'ID dell'app      | Deve essere un URI univoco, in genere nel formato **https://&lt;nome\_tenant&gt;/&lt;nome\_applicazione&gt;.** Viene usato durante il flusso di concessione dell'autorizzazione come identificatore univoco per specificare la risorsa per cui deve essere emesso il token. Diventa anche l'attestazione "aud" nel token di accesso emesso. |

### <a name="branding"></a>Personalizzazione

| Campo           | Description        |
|-----------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Carica nuovo logo | Questo campo consente di caricare un logo per l'applicazione. Il logo deve essere in formato BMP, jpg o PNG e le dimensioni del file devono essere inferiori a 100 KB. Le dimensioni dell'immagine devono essere 215 x 215 pixel, con le dimensioni dell'immagine centrale di 94 x 94 pixel.|
| URL della home page   | Questo è l'URL di accesso specificato durante la registrazione dell'applicazione.|

### <a name="authentication"></a>Authentication

| Campo           | Description        |
|-----------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| URL di chiusura sessione      | Si tratta dell'URL di disconnessione Single Sign-out. Azure AD invia una richiesta di disconnessione a questo URL quando l'utente cancella la sessione di Azure AD usando qualsiasi altra applicazione registrata.|
| Tipi di account supportati  | Questa opzione specifica se l'applicazione può essere usata da più tenant. In genere, ciò significa che le organizzazioni esterne possono usare l'applicazione registrandola nel proprio tenant e concedendo accesso ai dati dell'organizzazione.|
| URL di reindirizzamento      | Gli URL di reindirizzamento, o risposta, sono gli endpoint in cui Azure AD restituisce i token richiesti dall'applicazione. Per le applicazioni native, questo è il punto in cui l'utente deve essere inviato in seguito a un'autorizzazione riuscita. Azure AD verifica che l'URI di reindirizzamento fornito dall'applicazione nella richiesta OAuth 2,0 corrisponda a uno dei valori registrati nel portale.|

### <a name="certificates-and-secrets"></a>Certificati e segreti

| Campo           | Description        |
|-----------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Segreti client            | È possibile creare segreti client, o chiavi, per accedere a livello di codice alle API Web protette da Azure AD senza alcuna interazione dell'utente. Nella pagina **nuovo segreto client** immettere una descrizione della chiave e la data di scadenza e salvare per generare la chiave. Assicurarsi di salvarla in un luogo sicuro, poiché non sarà possibile accedervi successivamente.             |

## <a name="next-steps"></a>Passaggi successivi

[Gestione di applicazioni con Azure Active Directory](../manage-apps/what-is-application-management.md)
