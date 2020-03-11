---
title: Introduzione all'autenticazione di Azure AD tramite il portale di Azure
description: Informazioni su come usare il portale di Azure per accedere all'autenticazione di Azure Active Directory (Azure AD) per usare l'API Servizi multimediali di Azure.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 03/09/2020
ms.author: juliako
ms.openlocfilehash: 03643ea3f96448e5ec9693cd8ce9589472e8e3f2
ms.sourcegitcommit: 72c2da0def8aa7ebe0691612a89bb70cd0c5a436
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/10/2020
ms.locfileid: "79082745"
---
# <a name="use-the-azure-portal-to-get-credentials-to-access-media-services-api"></a>Usare il portale di Azure per ottenere le credenziali per accedere all'API di servizi multimediali  

Quando si usa l'autenticazione Azure AD per accedere all'API servizi multimediali di Azure, sono disponibili due opzioni di autenticazione:

- **Autenticazione basata su entità servizio**. Consente di autenticare un servizio. Le applicazioni che solitamente usano questo metodo di autenticazione sono app che eseguono servizi daemon, servizi di livello intermedio o processi pianificati, ad esempio App Web, app per le funzioni, app per la logica, API o microservizi.
- **Autenticazione utente**. Consente di autenticare una persona che usa l'app per interagire con le risorse di Servizi multimediali. L'applicazione interattiva deve prima richiedere all'utente le credenziali. Un esempio è un'app della console di gestione usata dagli utenti autorizzati per monitorare i processi di codifica o lo streaming live. 

Questo articolo illustra come usare la portale di Azure per ottenere le credenziali per accedere all'API di servizi multimediali.

## <a name="prerequisites"></a>Prerequisites

- Un account Azure. Se non si dispone di un account, iniziare con una [versione di valutazione gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/). 
- Account di Servizi multimediali. Per altre informazioni, vedere [Creare un account Servizi multimediali di Azure con il portale di Azure](create-account-portal.md).

## <a name="api-access"></a>Accesso all'API 

La pagina di **accesso all'API** consente di selezionare il metodo di autenticazione che si vuole usare per connettersi all'API. La pagina fornisce anche i valori necessari per la connessione all'API.

1. Nel [portale di Azure ](https://portal.azure.com/) selezionare l'account Servizi multimediali.
2. Scegliere come connettersi all'API Servizi multimediali.
3. In **Connetti all'API di servizi multimediali**selezionare la versione dell'API servizi multimediali a cui si vuole connettersi (v3 è la versione più recente del servizio).

## <a name="service-principal-authentication--recommended"></a>Autenticazione dell'entità servizio (scelta consigliata)

Autentica un servizio usando un'app Azure Active Directory (Azure AD) e il segreto. Questa opzione è consigliata per tutti i servizi di livello intermedio che chiamano l'API di servizi multimediali. Esempi sono app Web, funzioni, app per la logica, API e microservizi. Questo è il metodo di autenticazione consigliato.

### <a name="manage-your-azure-ad-app-and-secret"></a>Gestisci l'app Azure AD e il segreto

La sezione **Gestisci l'app AAD e il segreto** consente di selezionare o creare una nuova app Azure ad e generare un segreto. Per motivi di sicurezza, non è possibile visualizzare il segreto dopo che il pannello è stato chiuso. L'applicazione usa l'ID applicazione e il segreto per l'autenticazione per ottenere un token valido per servizi multimediali.

Assicurarsi di disporre di autorizzazioni sufficienti per registrare un'applicazione con il tenant di Azure AD e assegnare l'applicazione a un ruolo nella sottoscrizione di Azure. Per ulteriori informazioni, vedere [autorizzazioni obbligatorie](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#required-permissions).

### <a name="connect-to-media-services-api"></a>Connettersi all'API di servizi multimediali

L' **API Connetti a servizi multimediali** fornisce i valori usati per connettere l'applicazione dell'entità servizio. È possibile ottenere valori di testo o copiare i blocchi JSON o XML.

## <a name="user-authentication"></a>Autenticazione utente

Questa opzione può essere usata per autenticare un dipendente o un membro di un Azure Active Directory che usa un'app per interagire con le risorse di servizi multimediali. L'applicazione interattiva deve prima richiedere all'utente le credenziali. Questo metodo di autenticazione deve essere usato solo per le applicazioni di gestione.

### <a name="connect-to-media-services-api"></a>Connettersi all'API di servizi multimediali

Copiare le credenziali per connettere l'applicazione utente dalla sezione **connettersi all'API di servizi multimediali** . È possibile ottenere valori di testo o copiare i blocchi JSON o XML.

## <a name="next-steps"></a>Passaggi successivi

[Esercitazione: caricare, codificare e trasmettere in streaming video con servizi multimediali V3](stream-files-tutorial-with-api.md).
