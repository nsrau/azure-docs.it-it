---
title: File di inclusione
description: File di inclusione
services: digital-twins
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
ms.topic: include
ms.date: 10/02/2019
ms.custom: include file
ms.openlocfilehash: f67f271ee02e38e168ddfaa84ed1309321cf8702
ms.sourcegitcommit: 4f7dce56b6e3e3c901ce91115e0c8b7aab26fb72
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/04/2019
ms.locfileid: "71949107"
---
> [!NOTE]
> Per le richieste multipart in genere sono necessari tre elementi:
> * Un'intestazione **Content-Type**:
>   * `application/json; charset=utf-8`
>   * `multipart/form-data; boundary="USER_DEFINED_BOUNDARY"`
> * Un'informazione **Content-Disposition**:
>   * `form-data; name="metadata"`
> * Il contenuto del file da caricare
>
> Il contenuto di **Content-Type** e **Content-Disposition** varia in base allo scenario.

Le richieste multipart possono essere effettuate a livello di programmazione (tramite C#), mediante un client REST o con uno strumento come [Postman](https://docs.microsoft.com/azure/digital-twins/how-to-configure-postman#make-a-multipart-post-request). Gli strumenti client REST possono avere vari livelli di supporto per le richieste multipart complesse. Le impostazioni di configurazione possono inoltre variare leggermente da strumento a strumento. Verificare quale strumento è più adatto alle proprie esigenze.

> [!IMPORTANT]
> Le richieste multipart inviate alle API Gestione di Gemelli digitali di Azure sono in genere suddivise in due parti:
> * Metadati BLOB, ad esempio un tipo MIME associato, dichiarati da **Content-Type** e/o **Content-Disposition**
> * Contenuto BLOB che include il contenuto non strutturato di un file da caricare
>
> Per le richieste **PATCH** non è necessaria nessuna delle due parti, mentre entrambe sono necessarie per le operazioni **POST** o di creazione.

Il [codice sorgente dell'avvio rapido relativo all'occupazione](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/api/update.cs) contiene esempi C# completi che illustrano come effettuare richieste multipart alle API di gestione di Gemelli digitali di Azure.