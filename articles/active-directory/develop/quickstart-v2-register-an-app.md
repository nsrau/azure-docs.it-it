---
title: Registrare un'app con l'endpoint v2.0 di Azure AD | Microsoft Docs
description: Informazioni su come registrare un'app in Microsoft per abilitare l'accesso ai servizi Microsoft tramite l'endpoint v2.0 di Azure AD.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: bb2f701f-3bc3-4759-94a5-8b9d53a8a0b6
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 09/24/2018
ms.author: celested
ms.reviewer: lenalepa
ms.custom: aaddev
ms.openlocfilehash: b2dea11b6573be8f574bd18fa69ee76658d6d698
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/24/2018
ms.locfileid: "46975811"
---
# <a name="quickstart-register-an-app-with-the-azure-active-directory-v20-endpoint"></a>Guida introduttiva: registrare un'app con l'endpoint v2.0 di Azure Active Directory

[!INCLUDE [active-directory-develop-applies-v2](../../../includes/active-directory-develop-applies-v2.md)]

Per creare un'app che accetta sia account Microsoft personali (MSA) che account aziendali o dell'istituto di istruzione (Azure AD) è necessario registrare un'app con l'endpoint v2.0 di Azure Active Directory. Con gli account Azure AD o Microsoft non sarà possibile usare un'app esistente, ma è necessario crearne una nuova.

> [!NOTE]
> Non tutti gli scenari e le funzionalità di Azure AD sono supportati dall'endpoint 2.0. Per determinare se è consigliabile usare l'endpoint 2.0, leggere l'articolo relativo alle [limitazioni della versione 2.0](active-directory-v2-limitations.md).

## <a name="step-1-sign-in-to-the-microsoft-application-registration-portal"></a>Passaggio 1: Passare al portale di registrazione delle applicazioni Microsoft

1. Passare al portale di registrazione delle applicazioni Microsoft all'indirizzo [https://apps.dev.microsoft.com/](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList).
1. Accedere con un account Microsoft personale, aziendale o dell'istituto di istruzione. Se non si dispone di alcun account, iscriversi per creare un nuovo account personale.
1. Dopo aver completato l'operazione, è possibile esaminare l'elenco delle app Microsoft, che probabilmente sarà vuoto. A tale scopo, seguire questa procedura.

## <a name="step-2-register-an-app"></a>Passaggio 2: Registrare un'app

1. Selezionare **Aggiungi un'app**e attribuirle un nome.
    Il portale assegnerà all'app un ID applicazione univoco globale che verrà usato successivamente nel codice. Se l’app include un componente sul lato server che necessità di token di accesso per chiamare le API (ad esempio, Office, Azure o un’API Web), è opportuno creare anche un **segreto dell’applicazione** .
1. Successivamente, aggiungere le **piattaforme** usate dall'app.
    * Per le app basate sul Web, specificare un **URI di reindirizzamento** dove possono essere inviati i messaggi di accesso.
    * Per le app per dispositivi mobili, copiare l'URI di reindirizzamento predefinito creato automaticamente.
    * Per le API Web, viene creato automaticamente un ambito predefinito per accedere all'API Web.
        È possibile aggiungere altri ambiti tramite il pulsante **Aggiungi ambito**. È anche possibile aggiungere applicazioni preautorizzate a usare l'API Web usando il modulo **Applicazioni preautorizzate**.
1. Facoltativamente, personalizzare l'aspetto della pagina di accesso nella sezione del **profilo**. 
1. **Salvare** le modifiche prima di procedere.

> [!NOTE]
> Quando si registra un'applicazione usando [https://apps.dev.microsoft.com/](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList), l'applicazione viene registrata nel tenant principale dell'account usato per accedere al portale. Ciò significa che non è possibile registrare un'applicazione nel tenant di Azure AD usando un account Microsoft personale. Se si desidera registrare  in modo esplicito un'applicazione in un particolare tenant, accedere con un account creato nel tenant.

## <a name="next-steps"></a>Passaggi successivi

Dopo aver creato un'app Microsoft, è possibile completare una delle guide introduttive2 per v2.0. Di seguito sono elencati alcuni suggerimenti:

[!INCLUDE [active-directory-v2-quickstart-table](../../../includes/active-directory-v2-quickstart-table.md)]
