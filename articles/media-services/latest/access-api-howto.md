---
title: Introduzione all'autenticazione di Azure ADGet started with Azure AD authentication
description: Informazioni su come accedere all'autenticazione di Azure Active Directory (Azure AD) per usare l'API Servizi multimediali di Azure.Learn how to access Azure Active Directory (Azure AD) authentication to consume the Azure Media Services API.
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
ms.date: 03/15/2020
ms.author: juliako
ms.openlocfilehash: 6e1ab30e8b4cf44f7d1f82fd94fb9bf854915557
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79478253"
---
# <a name="get-credentials-to-access-media-services-api"></a>Ottenere le credenziali per accedere all'API di Servizi multimedialiGet credentials to access Media Services API  

Quando si usa l'autenticazione di Azure AD per accedere all'API di Servizi multimediali di Azure, sono disponibili due opzioni di autenticazione:When you use Azure AD authentication to access the Azure Media Services API, you have two authentication options:

- **Autenticazione dell'entità servizio** (scelta consigliata)Service principal authentication (recommended)

    Consente di autenticare un servizio. Le applicazioni che solitamente usano questo metodo di autenticazione sono app che eseguono servizi daemon, servizi di livello intermedio o processi pianificati, ad esempio App Web, app per le funzioni, app per la logica, API o microservizi.
- **Autenticazione utente**

    Consente di autenticare una persona che usa l'app per interagire con le risorse di Servizi multimediali. L'applicazione interattiva deve prima richiedere all'utente le credenziali. Un esempio è un'app della console di gestione usata dagli utenti autorizzati per monitorare i processi di codifica o lo streaming live. 

Questo articolo descrive la procedura per ottenere le credenziali per accedere all'API di Servizi multimediali. Scegliere una delle seguenti schede.

## <a name="prerequisites"></a>Prerequisiti

- Un account Azure. Se non si dispone di un account, iniziare con una [versione di valutazione gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/). 
- Account di Servizi multimediali. Per altre informazioni, vedere [Creare un account Servizi multimediali di Azure con il portale di Azure](create-account-howto.md).

## <a name="use-the-azure-portal"></a>Usare il portale di Azure

### <a name="api-access"></a>Accesso all'API 

La pagina **Accesso API** consente di selezionare il metodo di autenticazione che si desidera utilizzare per connettersi all'API. La pagina fornisce anche i valori necessari per connettersi all'API.

1. Nel [portale di Azure ](https://portal.azure.com/) selezionare l'account Servizi multimediali.
2. Scegliere come connettersi all'API Servizi multimediali.
3. In **Connetti all'API Servizi multimediali**selezionare la versione dell'API di Servizi multimediali a cui si desidera connettersi (V3 è la versione più recente del servizio).

### <a name="service-principal-authentication--recommended"></a>Autenticazione dell'entità servizio (scelta consigliata)Service principal authentication (recommended)

Autentica un servizio usando un'app e un segreto di Azure Active Directory (Azure AD). Questa operazione è consigliata per tutti i servizi di livello intermedio che chiamano l'API di Servizi multimediali. Esempi sono App Web, Funzioni, App per la logica, API e microservizi. Questo è il metodo di autenticazione consigliato.

#### <a name="manage-your-azure-ad-app-and-secret"></a>Gestire l'app e il segreto di Azure ADManage your Azure AD app and secret

La sezione **Gestisci app e segreto AAD** consente di selezionare o creare una nuova app Azure AD e generare un segreto. Per motivi di sicurezza, il segreto non può essere visualizzato dopo la chiusura del pannello. L'applicazione utilizza l'ID applicazione e il segreto per l'autenticazione per ottenere un token valido per i servizi multimediali.

Assicurarsi di disporre di autorizzazioni sufficienti per registrare un'applicazione con il tenant di Azure AD e assegnare l'applicazione a un ruolo nella sottoscrizione di Azure.Make sure that you have sufficient permissions to register an application with your Azure AD tenant and to assign the application to a role in your Azure subscription. Per ulteriori informazioni, vedere [Autorizzazioni necessarie](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#required-permissions).

#### <a name="connect-to-media-services-api"></a>Connettersi all'API di Servizi multimedialiConnect to Media Services API

**L'API Connetti a Servizi multimediali** fornisce i valori utilizzati per connettere l'applicazione dell'entità servizio. È possibile ottenere valori di testo o copiare i blocchi JSON o XML.

### <a name="user-authentication"></a>Autenticazione utente

Questa opzione può essere usata per autenticare un dipendente o un membro di Azure Active Directory che usa un'app per interagire con le risorse di Servizi multimediali. L'applicazione interattiva deve prima richiedere all'utente le credenziali. Questo metodo di autenticazione deve essere utilizzato solo per le applicazioni di gestione.

#### <a name="connect-to-media-services-api"></a>Connettersi all'API di Servizi multimedialiConnect to Media Services API

Copiare le credenziali per connettere l'applicazione utente dalla sezione **Connetti all'API di Servizi multimediali.** È possibile ottenere valori di testo o copiare i blocchi JSON o XML.

[!INCLUDE [media-services-cli-instructions](../../../includes/media-services-cli-instructions.md)]

[!INCLUDE [media-services-v3-cli-access-api-include](../../../includes/media-services-v3-cli-access-api-include.md)]

---

## <a name="next-steps"></a>Passaggi successivi

[Esercitazione: Caricare, codificare e trasmettere video con Servizi multimediali v3.](stream-files-tutorial-with-api.md)
