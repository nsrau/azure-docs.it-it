---
title: Sviluppo con le API v3 - Azure | Microsoft Docs
description: Questo articolo illustra le regole che si applicano a entità e le API durante lo sviluppo con servizi multimediali v3.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 05/02/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 4c5b30ab075bbca22b6a58ccf65e55d332820937
ms.sourcegitcommit: 6f043a4da4454d5cb673377bb6c4ddd0ed30672d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/08/2019
ms.locfileid: "65406555"
---
# <a name="developing-with-media-services-v3-apis"></a>Sviluppo con servizi multimediali v3 API

Questo articolo illustra le regole che si applicano a entità e le API durante lo sviluppo con servizi multimediali v3.

## <a name="accessing-the-azure-media-services-api"></a>L'accesso a API dei servizi di servizi multimediali di Azure

Per essere autorizzati ad accedere alle risorse e all'API di Servizi multimediali, è innanzitutto necessario essere autenticati. Servizi multimediali supporta [Azure Active Directory (Azure AD)-base](../../active-directory/fundamentals/active-directory-whatis.md) l'autenticazione. Due opzioni di autenticazione comuni sono:
 
* **Autenticazione dell'entità servizio** : usato per autenticare un servizio (ad esempio: web App, App per le funzioni, App per la logica, API e microservizi). Le applicazioni che in genere usano questo metodo di autenticazione sono app che eseguono servizi daemon, servizi di livello intermedio o processi pianificati. Ad esempio, per il Web sono applicazioni sarà sempre un livello intermedio che si connette a servizi multimediali con un'entità servizio.
* **L'autenticazione utente** : usato per autenticare una persona che usa l'app per interagire con risorse di servizi multimediali. L'applicazione interattiva deve prima richiedere all'utente le credenziali. Un esempio è un'app della console di gestione usata dagli utenti autorizzati per monitorare i processi di codifica o lo streaming live.

L'API servizi multimediali è necessario che l'utente o applicazione che effettua l'API REST richiede hanno accesso alla risorsa dell'account di servizi multimediali e usare una **collaboratore** oppure **proprietario** ruolo. L'API è possibile accedere con il **Reader** ruolo ma solo **ottenere** o **elenco**   operazioni saranno disponibili. Per altre informazioni, vedere [controllo di accesso basato sui ruoli per gli account di servizi multimediali](rbac-overview.md).

Anziché creare un'entità servizio, prendere in considerazione usando identità gestite per le risorse di Azure per accedere all'API servizi multimediali tramite Azure Resource Manager. Per altre informazioni sulle identità gestita per le risorse di Azure, vedere [What ' s identità gestita per le risorse di Azure](../../active-directory/managed-identities-azure-resources/overview.md).

### <a name="azure-ad-service-principal"></a>Entità servizio di Azure AD 

Se si sta creando un'applicazione Azure AD e un servizio come entità, l'applicazione deve essere nel proprio tenant. Dopo aver creato l'applicazione, assegnare all'app **Contributor** oppure **proprietario** ruolo l'accesso all'account di servizi multimediali. 

Se non si è sicuri se si dispone delle autorizzazioni per creare un'applicazione Azure AD, vedere [autorizzazioni necessarie](../../active-directory/develop/howto-create-service-principal-portal.md#required-permissions).

Nella figura seguente, i numeri rappresentano il flusso delle richieste in ordine cronologico:

![App di livello intermedio](./media/use-aad-auth-to-access-ams-api/media-services-principal-service-aad-app1.png)

1. Un'app di livello intermedio richiede un token di accesso di Azure AD con i parametri seguenti:  

   * Endpoint tenant di Azure AD.
   * URI di risorsa per Servizi multimediali.
   * URI di risorsa per Servizi multimediali REST.
   * Valori dell'applicazione Azure AD: l'ID client e il segreto client.
   
   Per ottenere tutti i valori necessari, vedere [accesso API servizi multimediali di Azure con la CLI di Azure](access-api-cli-how-to.md)

2. Il token di accesso di Azure AD viene inviato al livello intermedio.
4. Il livello intermedio invia una richiesta all'API REST di Servizi multimediali di Azure con il token di Azure AD.
5. Il livello intermedio ottiene nuovamente i dati da Servizi multimediali.

### <a name="samples"></a>Campioni

Vedere i seguenti esempi che illustrano come connettersi con l'entità servizio di Azure AD:

* [Connettersi con REST](media-rest-apis-with-postman.md)  
* [Connettersi con Java](configure-connect-java-howto.md)
* [Connettersi con .NET](configure-connect-dotnet-howto.md)
* [Connettersi con Node.js](configure-connect-nodejs-howto.md)
* [Connettersi con Python](configure-connect-python-howto.md)

## <a name="naming-conventions"></a>Convenzioni di denominazione

I nomi delle risorse di Servizi multimediali di Azure v3 (ad esempio, asset, processi e trasformazioni) sono soggetti ai vincoli di denominazione di Azure Resource Manager. In conformità con Azure Resource Manager, i nomi delle risorse sono sempre univoci. Di conseguenza, per i nomi delle risorse è possibile usare qualsiasi stringa di identificatore univoco (ad esempio, GUID). 

I nomi delle risorse di Servizi multimediali non possono includere "<", ">", "%", "&", ":", "&#92;", "?", "/", "*", "+", ".", virgolette singole o caratteri di controllo. Sono consentiti tutti gli altri caratteri. La lunghezza massima di un nome di risorsa è di 260 caratteri. 

Per altre informazioni sulla denominazione di Azure Resource Manager, vedere: [Requisiti di denominazione](https://github.com/Azure/azure-resource-manager-rpc/blob/master/v1.0/resource-api-reference.md#arguments-for-crud-on-resource) e [Convenzioni di denominazione](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions).

## <a name="long-running-operations"></a>Operazioni con esecuzione prolungata

Le operazioni contrassegnate con `x-ms-long-running-operation` in servizi multimediali di Azure [swagger file](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01/streamingservice.json) sono lunghe operazioni in esecuzione. 

Per informazioni dettagliate su come tenere traccia delle operazioni asincrone, vedere [operazioni asincrone](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations#monitor-status-of-operation).

Servizi multimediali ha le seguenti operazioni a esecuzione prolungata:

* Creare LiveEvent
* Aggiornamento Live
* Elimina LiveEvent
* Avvia Live
* Arresta Live
* Reimpostare LiveEvent
* Creare LiveOutput
* Elimina LiveOutput
* Creare un'entità StreamingEndpoint
* Aggiornare l'entità StreamingEndpoint
* Eliminare l'entità StreamingEndpoint
* Avviare un'entità StreamingEndpoint
* Arrestare entità StreamingEndpoint
* Scalabilità StreamingEndpoint

## <a name="filtering-ordering-paging-of-media-services-entities"></a>Applicazione di filtri, ordinamento e restituzione di più pagine delle entità di Servizi multimediali

Vedere [filtro, ordinamento, paging delle entità di servizi multimediali di Azure](entities-overview.md)

## <a name="ask-questions-give-feedback-get-updates"></a>Porre domande, fornire feedback, ottenere aggiornamenti

Consultare l'articolo [Community di Servizi multimediali di Azure](media-services-community.md) per esaminare i diversi modi in cui è possibile porre domande, fornire feedback e ottenere aggiornamenti su Servizi multimediali.

## <a name="next-steps"></a>Passaggi successivi

[Inizia a sviluppare con servizi multimediali v3 API usando gli SDK/tools](developers-guide.md)
