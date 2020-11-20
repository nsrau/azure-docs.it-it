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
ms.openlocfilehash: a0b2c9548f9c1289ae0abd61a72d7146a3bbca29
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2020
ms.locfileid: "94965160"
---
Compilare e inviare il [modulo di richiesta di servizi cognitivi](https://aka.ms/csgate) per richiedere l'accesso alla analisi del testo per l'anteprima pubblica dell'integrità.  Questa applicazione è valida sia per il contenitore che per l'anteprima pubblica dell'API Web ospitata.
Il modulo richiede informazioni sull'utente, sull'azienda e sullo scenario utente per cui si userà il contenitore. Dopo l'invio del modulo, il team di Servizi cognitivi di Azure lo esaminerà per verificare che siano soddisfatti i criteri di accesso al registro contenitori privato.

> [!IMPORTANT]
> * Nel modulo è necessario usare un indirizzo di posta elettronica associato a un ID sottoscrizione di Azure.
> * La risorsa di Azure usata per eseguire il contenitore deve essere stata creata con l'ID sottoscrizione di Azure approvato. 
> * Controllare la posta elettronica (cartella posta in arrivo e cartelle indesiderate) per gli aggiornamenti sullo stato dell'applicazione da Microsoft.

Una volta approvata, verrà inviato un messaggio di posta elettronica con le credenziali per accedere al registro contenitori privato.  Usare il comando Docker login con le credenziali fornite nel messaggio di posta elettronica di onboarding per connettersi al registro contenitori privato per i contenitori di servizi cognitivi.


```Docker
docker login containerpreview.azurecr.io -u <username> -p <password>
```

Usare il [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) comando per scaricare l'immagine del contenitore dal registro contenitori privato.

```
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-healthcare:latest
```
