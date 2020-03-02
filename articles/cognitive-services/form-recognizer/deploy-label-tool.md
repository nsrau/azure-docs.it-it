---
title: Come distribuire lo strumento di assegnazione di etichette di esempio per il form Recognizer
titleSuffix: Azure Cognitive Services
description: Informazioni sui diversi modi in cui è possibile distribuire lo strumento di assegnazione di etichette di esempio di form Recognizer per facilitare l'apprendimento supervisionato.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 02/28/2020
ms.author: pafarley
ms.openlocfilehash: fa419d7dd9668ac2ce8f2b0eb904117c7e22692d
ms.sourcegitcommit: 1fa2bf6d3d91d9eaff4d083015e2175984c686da
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/01/2020
ms.locfileid: "78207844"
---
# <a name="deploy-the-sample-labeling-tool"></a>Distribuire lo strumento di assegnazione di etichette di esempio

Lo strumento di assegnazione di etichette di esempio di form Recognizer è un'applicazione che viene eseguita in un contenitore docker. Fornisce un'interfaccia utente utile che è possibile usare per etichettare manualmente i documenti del modulo ai fini dell'apprendimento supervisionato. La Guida introduttiva [con le etichette di training](./quickstarts/label-tool.md) Mostra come eseguire lo strumento nel computer locale, che è lo scenario più comune. 

In questa guida vengono illustrati i modi alternativi per distribuire ed eseguire lo strumento di assegnazione di etichette di esempio. 

## <a name="deploy-with-azure-container-instances"></a>Distribuire con istanze di contenitore di Azure

È possibile eseguire lo strumento etichetta in un contenitore di app Web docker. Per prima cosa, [creare una nuova risorsa dell'app Web](https://ms.portal.azure.com/#create/Microsoft.WebSite) nel portale di Azure. Compilare il modulo con i dettagli della sottoscrizione e del gruppo di risorse. Immettere le seguenti informazioni nei campi obbligatori:
* **Publish**: contenitore Docker
* **Operazione** in esecuzione Sistema: Linux

Nella pagina successiva compilare i campi seguenti per l'installazione del contenitore docker:

* **Opzioni**: singolo contenitore
* **Origine immagine**: Azure container Registry
* **Tipo di accesso**: Public
* **Immagine e tag**: MCR.Microsoft.com/Azure-cognitive-Services/Custom-Form/labeltool:Latest

I passaggi seguenti sono facoltativi. Al termine della distribuzione dell'app, è possibile eseguirla e accedere allo strumento Label online.

### <a name="connect-to-azure-ad-for-authorization"></a>Connetti a Azure AD per l'autorizzazione

Si consiglia di connettere l'app Web ad Azure Active Directory (AAD), in modo che solo un utente con le proprie credenziali possa accedere e usare l'app. Seguire le istruzioni riportate in [configurare l'app del servizio app](https://docs.microsoft.com/azure/app-service/configure-authentication-provider-aad) per connettersi ad AAD.

## <a name="next-steps"></a>Passaggi successivi

Tornare alla Guida introduttiva [con etichette](./quickstarts/label-tool.md) di training per apprendere come usare lo strumento per etichettare manualmente i dati di training ed eseguire l'apprendimento supervisionato.
