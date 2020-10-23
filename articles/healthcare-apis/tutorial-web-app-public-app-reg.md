---
title: Esercitazione su un'app Web - Configurazione dell'applicazione client
description: Questa esercitazione illustra i passaggi per la registrazione di un'applicazione pubblica in preparazione della distribuzione di un'applicazione Web
services: healthcare-apis
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: tutorial
ms.reviewer: matjazl
ms.author: cavoeg
author: caitlinv39
ms.date: 01/03/2020
ms.openlocfilehash: c4f6124227f5dd5c7735d300f71d5ae7d0c06b3a
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/13/2020
ms.locfileid: "91975774"
---
# <a name="client-application-registration"></a>Registrazione dell'applicazione client
Nell'esercitazione precedente è stata distribuita e configurata l'API di Azure per FHIR. Ora che è stata configurata l'API di Azure per FHIR, verrà registrata un'applicazione client pubblica. Per informazioni dettagliate o per la risoluzione dei problemi, è possibile leggere l'intera guida pratica su come [registrare un'app client pubblica](register-public-azure-ad-client-app.md), ma di seguito vengono illustrati i passaggi principali per questa esercitazione.

1. Passare ad Azure Active Directory
1. Selezionare **Registrazione dell'app** --> **Nuova registrazione**
1. Assegnare un nome all'applicazione
1. Selezionare **Client pubblico/nativo (per dispositivi mobili e desktop)** e impostare l'URL di reindirizzamento su https://www.getpostman.com/oauth2/callback.

   :::image type="content" source="media/tutorial-web-app/register-public-app.png" alt-text="Screenshot del riquadro Registra un'applicazione con un nome applicazione e un URL di reindirizzamento di esempio.":::

## <a name="client-application-settings"></a>Impostazioni applicazione client

Dopo aver registrato l'applicazione client, copiare l'ID applicazione (client) e l'ID tenant dalla pagina Panoramica. Questi due valori saranno necessari in un secondo momento per accedere al client.

:::image type="content" source="media/tutorial-web-app/client-id-tenant-id.png" alt-text="Screenshot del riquadro Registra un'applicazione con un nome applicazione e un URL di reindirizzamento di esempio.":::

### <a name="connect-with-web-app"></a>Stabilire la connessione con l'app Web

Se [l'app Web è stata scritta](tutorial-web-app-write-web-app.md) per connettersi con l'API di Azure per FHIR, occorre anche impostare le opzioni di autenticazione corrette. 

1. Nel menu a sinistra, in **Gestisci**, selezionare **Autenticazione**. 

1. Per aggiungere una nuova configurazione della piattaforma, selezionare **Web**.

1. Configurare l'URI di reindirizzamento in preparazione della creazione dell'applicazione Web nella quarta parte di questa esercitazione. A tale scopo, aggiungere `https://\<WEB-APP-NAME>.azurewebsites.net` all'elenco degli URI di reindirizzamento. Se si sceglie un nome diverso durante il passaggio in cui si [scrive l'app Web](tutorial-web-app-write-web-app.md), sarà necessario tornare indietro e aggiornare questo valore.

1. Selezionare le caselle di controllo **Token di accesso** e **Token ID**.

   :::image type="content" source="media/tutorial-web-app/web-app-authentication.png" alt-text="Screenshot del riquadro Registra un'applicazione con un nome applicazione e un URL di reindirizzamento di esempio.":::

## <a name="add-api-permissions"></a>Aggiungere autorizzazioni API

Dopo aver configurato l'autenticazione corretta, impostare le autorizzazioni API:

1. Selezionare **Autorizzazioni API** e fare clic su **Aggiungi un'autorizzazione**.
1. In **API usate dall'organizzazione** cercare le API di Azure per il settore sanitario.
1. Selezionare **user_impersonation** e fare clic su **Aggiungi autorizzazioni**.

:::image type="content" source="media/tutorial-web-app/api-permissions.png" alt-text="Screenshot del riquadro Registra un'applicazione con un nome applicazione e un URL di reindirizzamento di esempio.":::

## <a name="next-steps"></a>Passaggi successivi
È ora disponibile un'applicazione client pubblica. Nell'esercitazione successiva verranno illustrate le procedure per eseguire i test e ottenere l'accesso a questa applicazione tramite Postman.

>[!div class="nextstepaction"]
>[Testare l'applicazione client in Postman](tutorial-web-app-test-postman.md)
