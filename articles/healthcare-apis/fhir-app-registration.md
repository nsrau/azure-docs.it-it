---
title: Registrare le app di Azure Active Directory per l'API di Azure per FHIR
description: Questa esercitazione descrive quali sono le applicazioni che devono essere registrate per l'API di Azure per FHIR e il server FHIR per Azure.
services: healthcare-apis
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: overview
ms.reviewer: dseven
ms.author: matjazl
author: matjazl
ms.date: 10/13/2019
ms.openlocfilehash: 22f31cf3911b5ea24e8798fb226e389071fadd0b
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/05/2020
ms.locfileid: "87848979"
---
# <a name="register-the-azure-active-directory-apps-for-azure-api-for-fhir"></a>Registrare le app di Azure Active Directory per l'API di Azure per FHIR

Quando si configura l'API di Azure per FHIR o il server FHIR per Azure (OSS), è possibile scegliere tra numerose opzioni di configurazione. Per il server open source è necessario creare una registrazione dell'applicazione della risorsa personalizzata. Per l'API di Azure per FHIR, questa applicazione della risorsa viene creata automaticamente.

## <a name="application-registrations"></a>Registrazioni delle applicazioni

Per consentire a un'applicazione di interagire con Azure AD, deve essere registrata. Nel contesto del server FHIR, esistono due tipologie di registrazione dell'applicazione da esaminare:

1. Registrazioni dell'applicazione della risorsa.
1. Registrazioni dell'applicazione client.

Le **applicazioni della risorsa** sono rappresentazioni in Azure AD di un'API o di una risorsa protetta tramite Azure AD, in questo caso l'API di Azure per FHIR. Un'applicazione della risorsa per l'API di Azure per FHIR verrà creata automaticamente quando si effettua il provisioning del servizio, ma se si usa il server open source, sarà necessario [registrare un'applicazione della risorsa](register-resource-azure-ad-client-app.md) in Azure AD. A questa applicazione della risorsa sarà assegnato un URI dell'identificatore. È consigliabile che questo URI corrisponda all'URI del server FHIR. Questo URI deve essere usato come `Audience` per il server FHIR. Un'applicazione client può richiedere l'accesso a questo server FHIR quando richiede un token.

Le *applicazioni client* sono registrazioni dei client che richiederanno i token. Spesso in OAuth 2.0 viene fatta distinzione tra almeno tre tipi diversi di applicazioni:

1. **Client riservati**, noti anche come app Web in Azure AD. I client riservati sono applicazioni che usano il [flusso del codice di autorizzazione](https://docs.microsoft.com/azure/active-directory/develop/v1-protocols-oauth-code) per ottenere un token per conto di un utente connesso che presenta credenziali valide. Sono denominati client riservati perché possono mantenere un segreto e presenteranno questo segreto ad Azure AD durante lo scambio del codice di autenticazione per un token. Poiché i client riservati possono eseguire l'autenticazione usando il segreto client, sono considerati più attendibili rispetto ai client pubblici, possono avere token di durata maggiore e ottenere un token di aggiornamento. Vedere le informazioni dettagliate su come [registrare un client riservato](register-confidential-azure-ad-client-app.md). Si noti che è importante registrare l'URL di risposta a cui il client riceverà il codice di autorizzazione.
1. **Client pubblici**. Sono i client che non possono mantenere un segreto. In genere si tratta di un'applicazione per dispositivi mobili o di un'applicazione JavaScript a pagina singola, in cui un segreto nel client può essere individuato da un utente. Anche i client pubblici usano il flusso del codice di autorizzazione, ma non sono autorizzati a presentare un segreto quando ottengono un token, i token sono di durata più breve e non ottengono un token di aggiornamento. Vedere le informazioni dettagliate su come [registrare un client pubblico](register-public-azure-ad-client-app.md).
1. Client del servizio. Questi client ottengono i token per conto di se stessi, non per conto di un utente, usando il [flusso delle credenziali client](https://docs.microsoft.com/azure/active-directory/develop/v1-oauth2-client-creds-grant-flow). Rappresentano in genere applicazioni che accedono al server FHIR in modo non interattivo, ad esempio per un processo di inserimento. Quando si usa un client del servizio, non è necessario avviare il processo di recupero di un token con una chiamata all'endpoint `/authorize`. Un client del servizio può passare direttamente all'endpoint `/token` e presentare l'ID client e il segreto client per ottenere un token. Vedere le informazioni dettagliate su come [registrare un client del servizio](register-service-azure-ad-client-app.md)

## <a name="next-steps"></a>Passaggi successivi

In questa panoramica sono stati esaminati i tipi di registrazioni delle applicazioni che potrebbero essere necessari per usare un'API FHIR.

In base alla configurazione corrente, vedere le guide pratiche per la registrazione dell'applicazione

* [Registrare un'applicazione della risorsa](register-resource-azure-ad-client-app.md)
* [Registrare un'applicazione client riservata](register-confidential-azure-ad-client-app.md)
* [Registrare un'applicazione client pubblica](register-public-azure-ad-client-app.md)
* [Registrare un'applicazione del servizio](register-service-azure-ad-client-app.md)

Dopo aver registrato le applicazioni, è possibile distribuire l'API di Azure per FHIR.

>[!div class="nextstepaction"]
>[Distribuire l'API di Azure per FHIR](fhir-paas-powershell-quickstart.md)