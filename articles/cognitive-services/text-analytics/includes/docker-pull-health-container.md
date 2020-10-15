---
title: Pull Docker per il contenitore di integrità
titleSuffix: Azure Cognitive Services
description: Comando Docker pull per Analisi del testo per il contenitore di integrità
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 07/07/2020
ms.author: aahi
ms.openlocfilehash: 519625f6468372ec7ace523dae7648212f4f3203
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91779746"
---
Compilare e inviare il [modulo di richiesta di contenitori di servizi cognitivi](https://aka.ms/csgate) per richiedere l'accesso al contenitore.
Il modulo richiede informazioni sull'utente, sull'azienda e sullo scenario utente per cui si userà il contenitore. Dopo l'invio del modulo, il team di Servizi cognitivi di Azure lo esaminerà per verificare che siano soddisfatti i criteri di accesso al registro contenitori privato.

> [!IMPORTANT]
> * Nel modulo è necessario usare un indirizzo di posta elettronica associato a un ID sottoscrizione di Azure.
> * La risorsa di Azure usata per eseguire il contenitore deve essere stata creata con l'ID sottoscrizione di Azure approvato. 
> * Controllare la posta elettronica (cartella posta in arrivo e cartelle indesiderate) per gli aggiornamenti sullo stato dell'applicazione da Microsoft.

Usare il comando Docker login con le credenziali fornite nel messaggio di posta elettronica di onboarding per connettersi al registro contenitori privato per i contenitori di servizi cognitivi.


```Docker
docker login containerpreview.azurecr.io -u <username> -p <password>
```

Usare il [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) comando per scaricare l'immagine del contenitore dal registro contenitori privato.

```
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-healthcare:latest
```
