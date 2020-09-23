---
title: Pull Docker per il contenitore di integrità
titleSuffix: Azure Cognitive Services
description: Comando Docker pull per Analisi del testo per il contenitore di integrità
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 07/07/2020
ms.author: aahi
ms.openlocfilehash: b9452baf0301233a1cd6e05621a89d013d32c9e0
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90906092"
---
Compilare e inviare il [modulo di richiesta di contenitori di servizi cognitivi](https://aka.ms/cognitivegate) per richiedere l'accesso al contenitore.
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
