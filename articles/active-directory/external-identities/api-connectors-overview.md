---
title: Informazioni sui connettori API nei flussi di iscrizione self-service-Azure AD
description: Usare i connettori API Azure Active Directory (Azure AD) per personalizzare ed estendere i flussi utente di iscrizione self-service usando le API Web.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 06/16/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: cdb6e85b6d81de3d4b88ba315ddd35bd5b37ae7a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88165210"
---
# <a name="use-api-connectors-to-customize-and-extend-self-service-sign-up"></a>Usare i connettori API per personalizzare ed estendere l'iscrizione self-service 

## <a name="overview"></a>Panoramica 
Gli sviluppatori o gli amministratori IT possono usare i connettori API per integrare i [flussi utente di iscrizione self-service](self-service-sign-up-overview.md) con sistemi esterni sfruttando le API Web. Ad esempio, è possibile usare i connettori API per:

- Eseguire [**l'integrazione con un flusso di lavoro di approvazione personalizzato**](self-service-sign-up-add-approvals.md). Connettersi a un sistema di approvazione personalizzato per la gestione della creazione dell'account.
- [**Eseguire la verifica dell'identità**](code-samples-self-service-sign-up.md#identity-verification). Usare un servizio di verifica dell'identità per aggiungere un livello di sicurezza aggiuntivo per le decisioni di creazione degli account.
- **Convalidare i dati di input utente**. Convalidare i dati utente in formato non corretto o non valido. È ad esempio possibile convalidare i dati forniti dall'utente in base ai dati esistenti in un archivio dati esterno o in un elenco di valori consentiti. Se non è valido, è possibile richiedere a un utente di fornire dati validi o impedire all'utente di continuare il flusso di iscrizione.
- **Sovrascrivi attributi utente**. Riformattare o assegnare un valore a un attributo raccolto dall'utente. Ad esempio, se un utente immette il nome in lettere tutte minuscole o tutte maiuscole, è possibile formattare il nome con solo la prima lettera maiuscola. 
<!-- - **Enrich user data**. Integrate with your external cloud systems that store user information to integrate them with the sign-up flow. For example, your API can receive the user's email address, query a CRM system, and return the user's loyalty number. Returned claims can be used to pre-fill form fields or return additional data in the application token.  -->
- **Eseguire la logica di business personalizzata**. È possibile attivare eventi downstream nei sistemi cloud per inviare notifiche push, aggiornare i database aziendali, gestire le autorizzazioni, controllare i database ed eseguire altre azioni personalizzate.

Un connettore API fornisce Azure Active Directory con le informazioni necessarie per chiamare l'endpoint API definendo l'URL e l'autenticazione dell'endpoint HTTP. Dopo aver configurato un connettore API, è possibile abilitarlo per un passaggio specifico in un flusso utente. Quando un utente raggiunge tale passaggio nel flusso di iscrizione, il connettore API viene richiamato e viene materializzato come una richiesta HTTP POST all'API, inviando le informazioni utente ("claims") come coppie chiave-valore in un corpo JSON. La risposta dell'API può influire sull'esecuzione del flusso utente. Ad esempio, la risposta API può impedire a un utente di iscriversi, chiedere all'utente di immettere nuovamente le informazioni oppure sovrascrivere e aggiungere gli attributi utente.

## <a name="where-you-can-enable-an-api-connector-in-a-user-flow"></a>Dove è possibile abilitare un connettore API in un flusso utente

Esistono due posizioni in un flusso utente in cui è possibile abilitare un connettore API:

- Dopo aver eseguito l'accesso con un provider di identità
- Prima della creazione dell'utente

> [!IMPORTANT]
> In entrambi i casi, i connettori delle API vengono richiamati durante l' **iscrizione**dell'utente e non l'accesso.

### <a name="after-signing-in-with-an-identity-provider"></a>Dopo aver eseguito l'accesso con un provider di identità

Un connettore API in questo passaggio del processo di iscrizione viene richiamato immediatamente dopo l'autenticazione dell'utente con un provider di identità (Google, Facebook, Azure AD). Questo passaggio precede la ***pagina raccolta attributi***, che è il form presentato all'utente per raccogliere gli attributi utente. Di seguito sono riportati alcuni esempi di scenari di connettore API che è possibile abilitare in questo passaggio:

- Utilizzare la posta elettronica o l'identità federata fornita dall'utente per cercare le attestazioni in un sistema esistente. Restituire queste attestazioni dal sistema esistente, precompilare la pagina della raccolta di attributi e renderle disponibili per restituire il token.
- Verificare se l'utente è incluso in un elenco Consenti o nega e controllare se è possibile continuare con il flusso di iscrizione.

### <a name="before-creating-the-user"></a>Prima della creazione dell'utente

Un connettore API in questo passaggio del processo di iscrizione viene richiamato dopo la pagina della raccolta di attributi, se disponibile. Questo passaggio viene sempre richiamato prima della creazione di un account utente in Azure AD. Di seguito sono riportati alcuni esempi di scenari che è possibile abilitare in questa fase durante l'iscrizione:

- Convalidare i dati di input dell'utente e richiedere a un utente di inviare nuovamente i dati.
- Blocca l'iscrizione di un utente in base ai dati immessi dall'utente.
- Eseguire la verifica dell'identità.
- Eseguire una query sui sistemi esterni per i dati esistenti sull'utente per restituirli nel token dell'applicazione o archiviarli in Azure AD.

<!-- > [!IMPORTANT]
> If an invalid response is returned or another error occurs (for example, a network error), the user will be redirected to the app with the error re -->

## <a name="next-steps"></a>Passaggi successivi
- Informazioni su come [aggiungere un connettore API a un flusso utente](self-service-sign-up-add-api-connector.md)
- Informazioni su come [aggiungere un sistema di approvazione personalizzato all'iscrizione self-service](self-service-sign-up-add-approvals.md)