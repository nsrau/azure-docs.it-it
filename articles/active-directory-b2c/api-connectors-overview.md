---
title: Informazioni sui connettori API in Azure AD B2C
description: Usare i connettori API Azure Active Directory (Azure AD) per personalizzare ed estendere i flussi utente di iscrizione usando le API Web.
services: active-directory-b2c
ms.service: active-directory
ms.subservice: B2C
ms.topic: how-to
ms.date: 10/15/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.custom: it-pro
ms.openlocfilehash: 94d6b0192b014396f8751e58f5620aec5c132203
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/24/2020
ms.locfileid: "92503879"
---
# <a name="use-api-connectors-to-customize-and-extend-sign-up-user-flows"></a>Usare i connettori API per personalizzare ed estendere i flussi utente di iscrizione

> [!IMPORTANT]
> Connettori API per l'iscrizione è una funzionalità di anteprima pubblica di Azure AD B2C. Per altre informazioni sulle anteprime, vedere [Condizioni per l'utilizzo supplementari per le anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="overview"></a>Panoramica 
Gli sviluppatori o gli amministratori IT possono usare i connettori API per integrare i flussi utente di iscrizione con le API Web per personalizzare l'esperienza di iscrizione. Con i connettori API, ad esempio, è possibile:

- **Convalidare i dati di input utente**. Convalidare i dati utente in formato non corretto o non valido. È ad esempio possibile convalidare i dati forniti dall'utente in base ai dati esistenti in un archivio dati esterno o in un elenco di valori consentiti. Se non è valido, è possibile richiedere a un utente di fornire dati validi o impedire all'utente di continuare il flusso di iscrizione.
- Eseguire **l'integrazione con un flusso di lavoro di approvazione personalizzato**. Connettersi a un sistema di approvazione personalizzato per la gestione e la limitazione della creazione dell'account.
- **Sovrascrivi attributi utente**. Riformattare o assegnare un valore a un attributo raccolto dall'utente. Ad esempio, se un utente immette il nome in lettere tutte minuscole o tutte maiuscole, è possibile formattare il nome con solo la prima lettera maiuscola. 
- **Eseguire la verifica dell'identità**. Usare un servizio di verifica dell'identità per aggiungere un livello di sicurezza aggiuntivo per le decisioni di creazione degli account.
- **Eseguire la logica di business personalizzata**. È possibile attivare eventi downstream nei sistemi cloud per inviare notifiche push, aggiornare i database aziendali, gestire le autorizzazioni, controllare i database ed eseguire altre azioni personalizzate.

Un connettore API fornisce Azure Active Directory con le informazioni necessarie per chiamare un'API, inclusi l'URL e l'autenticazione di un endpoint. Dopo aver configurato un connettore API, è possibile abilitarlo per un passaggio specifico in un flusso utente. Quando un utente raggiunge tale passaggio nel flusso di iscrizione, il connettore API viene richiamato e viene materializzato come una richiesta HTTP POST all'API, inviando le informazioni utente ("claims") come coppie chiave-valore in un corpo JSON. La risposta dell'API può influire sull'esecuzione del flusso utente. Ad esempio, la risposta API può impedire a un utente di iscriversi, chiedere all'utente di immettere nuovamente le informazioni oppure sovrascrivere e aggiungere gli attributi utente.

## <a name="where-you-can-enable-an-api-connector-in-a-user-flow"></a>Dove è possibile abilitare un connettore API in un flusso utente

Esistono due posizioni in un flusso utente in cui è possibile abilitare un connettore API:

- Dopo aver eseguito l'accesso con un provider di identità
- Prima della creazione dell'utente

> [!IMPORTANT]
> In entrambi i casi, i connettori delle API vengono richiamati durante l' **iscrizione**dell'utente e non l'accesso.

### <a name="after-signing-in-with-an-identity-provider"></a>Dopo aver eseguito l'accesso con un provider di identità

Un connettore API in questo passaggio del processo di iscrizione viene richiamato immediatamente dopo l'autenticazione dell'utente con un provider di identità (ad esempio Google, Facebook, & Azure AD). Questo passaggio precede la **_pagina raccolta attributi_**, che è il form presentato all'utente per raccogliere gli attributi utente. Questo passaggio non viene richiamato se un utente sta effettuando la registrazione con un account locale. Di seguito sono riportati alcuni esempi di scenari di connettore API che è possibile abilitare in questo passaggio:

- Utilizzare la posta elettronica o l'identità federata fornita dall'utente per cercare le attestazioni in un sistema esistente. Restituire queste attestazioni dal sistema esistente, precompilare la pagina della raccolta di attributi e renderle disponibili per restituire il token.
- Implementare un elenco Consenti o blocca basato sull'identità di social networking.

### <a name="before-creating-the-user"></a>Prima della creazione dell'utente

Un connettore API in questo passaggio del processo di iscrizione viene richiamato dopo la pagina della raccolta di attributi, se disponibile. Questo passaggio viene sempre richiamato prima della creazione di un account utente. Di seguito sono riportati alcuni esempi di scenari che è possibile abilitare in questa fase durante l'iscrizione:

- Convalidare i dati di input dell'utente e richiedere a un utente di inviare nuovamente i dati.
- Blocca l'iscrizione di un utente in base ai dati immessi dall'utente.
- Eseguire la verifica dell'identità.
- Eseguire una query sui sistemi esterni per i dati esistenti sull'utente per restituirli nel token dell'applicazione o archiviarli in Azure AD.


## <a name="next-steps"></a>Passaggi successivi
- Informazioni su come [aggiungere un connettore API a un flusso utente](add-api-connector.md)
- Inizia a usare gli [esempi](code-samples.md#api-connectors).
<!-- - Learn how to [add a custom approval system to self-service sign-up](add-approvals.md) -->