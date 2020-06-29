---
title: Esercitazione su un'app Web - Configurazione dell'applicazione client
description: Questa esercitazione illustra i passaggi per la registrazione di un'applicazione pubblica in preparazione della distribuzione di un'applicazione Web
services: healthcare-apis
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: tutorial
ms.reviewer: mihansen
ms.author: cavoeg
author: caitlinv39
ms.date: 01/03/2020
ms.openlocfilehash: 58e21e46edfe1d03d42bf2202bcf1f22282631a9
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/24/2020
ms.locfileid: "84870454"
---
# <a name="client-application-registration"></a>Registrazione dell'applicazione client
Nell'esercitazione precedente è stata distribuita e configurata l'API di Azure per FHIR. Ora che è stata configurata l'API di Azure per FHIR, verrà registrata un'applicazione client pubblica. Per informazioni dettagliate o per la risoluzione dei problemi, è possibile leggere l'intera guida pratica su come [registrare un'app client pubblica](register-public-azure-ad-client-app.md), ma di seguito vengono illustrati i passaggi principali per questa esercitazione.

1. Passare ad Azure Active Directory
1. Selezionare **Registrazione dell'app** --> **Nuova registrazione**
1. Assegnare un nome all'applicazione e impostare l'URI di reindirizzamento su https://www.getpostman.com/oauth2/callback


![Registrazione dell'applicazione client](media/tutorial-web-app/reg-public-app.png)

## <a name="client-application-settings"></a>Impostazioni applicazione client
Dopo aver registrato l'applicazione client, copiare l'ID applicazione (client) dalla pagina Panoramica. Questo valore sarà necessario in un secondo momento quando si accede al client.

![Copiare l'ID app](media/tutorial-web-app/app-id.png)

Impostare quindi le opzioni di autenticazione corrette. Selezionare **Autenticazione** nel menu a sinistra. Selezionare le caselle **Token di accesso** e **Token ID**. È anche possibile configurare l'URI di reindirizzamento in preparazione della creazione dell'applicazione Web nella quarta parte di questa esercitazione. A tale scopo, aggiungere https://\<WEB-APP-NAME>.azurewebsites.net all'elenco di URI di reindirizzamento. Se si sceglie un nome diverso durante il passaggio in cui si [scrive l'app Web](tutorial-web-app-write-web-app.md), sarà necessario tornare indietro e aggiornare questo valore.

![Impostazioni di autenticazione dell'app](media/tutorial-web-app/app-authentication.png)

Dopo aver configurato l'autenticazione corretta, impostare le autorizzazioni dell'API. 
1. Selezionare **Autorizzazioni API** e fare clic su **Aggiungi un'autorizzazione**
1. In **API usate dall'organizzazione** cercare le API Azure Healthcare
1. Selezionare **user_impersonation** e quindi fare clic su **Aggiungi autorizzazioni**

## <a name="next-steps"></a>Passaggi successivi
È ora disponibile un'applicazione client pubblica. Nell'esercitazione successiva verranno illustrate le procedure per eseguire i test e ottenere l'accesso a questa applicazione tramite Postman.

>[!div class="nextstepaction"]
>[Testare l'applicazione client in Postman](tutorial-web-app-test-postman.md)
