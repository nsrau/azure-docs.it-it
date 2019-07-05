---
title: Come compilare campi specifici per un'applicazione personalizzata | Microsoft Docs
description: Materiale sussidiario su come compilare campi specifici quando si registra un'applicazione personalizzata con Azure AD
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: ryanwi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9bccaa28d34ebff47c7de73a4d9b3d8296ae9fef
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/29/2019
ms.locfileid: "67476132"
---
# <a name="how-to-fill-out-specific-fields-for-a-custom-developed-application"></a>Come compilare campi specifici per un'applicazione personalizzata

Questo articolo include una breve descrizione di tutti i campi disponibili nel modulo di registrazione dell'applicazione del [Portale di Azure](https://portal.azure.com).

## <a name="register-a-new-application"></a>Registrare una nuova applicazione

-   Per registrare una nuova applicazione, passare al [Portale di Azure](https://portal.azure.com).

-   Nel riquadro di spostamento sinistro fare clic su **Azure Active Directory**.

-   Scegliere **Registrazioni per l'app** e fare clic su **Aggiungi**.

-   Si apre il modulo di registrazione dell'applicazione.

## <a name="fields-in-the-application-registration-form"></a>Campi nel modulo di registrazione dell'applicazione


| Campo            | Descrizione                                                                              |
|------------------|------------------------------------------------------------------------------------------|
| NOME             | Il nome dell'applicazione. Deve essere minimo di quattro caratteri.                |
| Tipi di account supportati| Selezionare gli account che si desidera l'applicazione per il supporto: in questa directory dell'organizzazione solo, conti in qualsiasi directory dell'organizzazione o account in qualsiasi directory dell'organizzazione e gli account Microsoft personali.  |
| (Facoltativo) URI di reindirizzamento | Selezionare il tipo di app, che raggiungono **Web** oppure **client pubblico (per dispositivi mobili e desktop)** , quindi immettere l'URI di reindirizzamento (o URL di risposta) per l'applicazione. Per le applicazioni Web, specificare l'URL di base dell'app. Ad esempio, http://localhost:31544 potrebbe essere l'URL per un'app Web eseguita sul computer locale. Gli utenti possono usare questo URL per accedere a un'applicazione client Web. Per le applicazioni client pubbliche, specificare l'URI usato da Azure AD per restituire le risposte dei token. Immettere un valore specifico per l'applicazione, ad esempio myapp://auth. Per esempi specifici per le applicazioni Web o per le applicazioni native, vedere le [guide introduttive](https://docs.microsoft.com/azure/active-directory/develop/#quickstarts).|

Dopo aver compilato i campi sopra elencati, l'applicazione è registrata nel portale di Azure e si verrà reindirizzati alla pagina di panoramica dell'applicazione. Le pagine di impostazioni nel riquadro sinistro sotto **Gestisci** avere più campi per personalizzare l'applicazione. Nelle tabelle seguenti descrivono tutti i campi. Viene visualizzato solo un subset di questi campi, a seconda se è stata creata un'applicazione web o un'applicazione client pubblica.

### <a name="overview"></a>Panoramica
| Campo           | Descrizione        |
|-----------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ID applicazione  | Quando si registra un'applicazione, Azure AD assegna un ID all'applicazione. L'ID dell'applicazione può essere usato per identificare in modo univoco l'applicazione nelle richieste di autenticazione ad Azure AD, nonché per accedere a risorse, ad esempio l'API Graph.                                                          |
| URI dell'ID dell'app      | Deve essere un URI univoco, in genere nel formato **https://&lt;nome\_tenant&gt;/&lt;nome\_applicazione&gt;.** Durante il flusso di concessione di autorizzazione, viene utilizzato come identificatore univoco per specificare la risorsa che deve essere emesso il token. Diventa anche l'attestazione "aud" nel token di accesso emesso. |

### <a name="branding"></a>Personalizzazione

| Campo           | Descrizione        |
|-----------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Carica nuovo logo | Questo campo consente di caricare un logo per l'applicazione. Il logo deve essere in formato BMP, jpg o PNG, e le dimensioni del file devono essere minore di 100 KB. Le dimensioni dell'immagine devono essere 215 x 215 pixel, con le dimensioni dell'immagine centrale di 94 x 94 pixel.|
| URL della home page   | Questo è l'URL di accesso specificato durante la registrazione dell'applicazione.|

### <a name="authentication"></a>Authentication

| Campo           | Descrizione        |
|-----------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| URL di chiusura sessione      | Si tratta dell'URL di disconnessione di disconnessione singolo. Azure AD invia una richiesta di disconnessione a questo URL quando l'utente cancella la sessione di Azure AD usando qualsiasi altra applicazione registrata.|
| Tipi di account supportati  | Questa opzione specifica se l'applicazione può essere usata da più tenant. In genere, ciò significa che le organizzazioni esterne possono usare l'applicazione registrandola nel proprio tenant e concedendo accesso ai dati dell'organizzazione.|
| URL di reindirizzamento      | Il reindirizzamento o risposta, gli URL sono gli endpoint in cui Azure AD restituisce eventuali token richiesti dall'applicazione. Per le applicazioni native, questo è il punto in cui l'utente deve essere inviato in seguito a un'autorizzazione riuscita. Azure AD controlla che l'URI dell'applicazione fornisce nella richiesta OAuth 2.0 di reindirizzamento corrisponda a uno dei valori registrati nel portale.|

### <a name="certificates-and-secrets"></a>I segreti e certificati

| Campo           | Descrizione        |
|-----------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Segreti client            | È possibile creare client segreti o chiavi, a livello di programmazione accedere ad API web protette da Azure AD senza alcuna interazione dell'utente. Dal **nuovo segreto client** pagina, immettere una descrizione della chiave e la data di scadenza e Salva per generare la chiave. Assicurarsi di salvarla in un luogo sicuro, poiché non sarà possibile accedervi successivamente.             |

## <a name="next-steps"></a>Passaggi successivi
[Gestione di applicazioni con Azure Active Directory](../manage-apps/what-is-application-management.md)
