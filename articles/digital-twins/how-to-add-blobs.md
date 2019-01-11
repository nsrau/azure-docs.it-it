---
title: Come aggiungere BLOB agli oggetti in Gemelli digitali di Azure | Microsoft Docs
description: Informazioni su come aggiungere BLOB agli oggetti in Gemelli digitali di Azure.
author: kingdomofends
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 12/28/2018
ms.author: adgera
ms.custom: seodec18
ms.openlocfilehash: 604093dcec048b0991bbc9beac3ef998cc47e351
ms.sourcegitcommit: 803e66de6de4a094c6ae9cde7b76f5f4b622a7bb
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/02/2019
ms.locfileid: "53974517"
---
# <a name="add-blobs-to-objects-in-azure-digital-twins"></a>Aggiungere BLOB agli oggetti in Gemelli digitali di Azure

I BLOB sono rappresentazioni non strutturate di tipi di file comuni, ad esempio immagini e log. I BLOB tengono traccia del tipo di dati che rappresentano tramite l'uso di un tipo MIME (ad esempio, "image/jpeg") e dei metadati (nome, descrizione, tipo e così via).

Gemelli digitali di Azure supporta il collegamento dei BLOB a dispositivi, spazi e utenti. I BLOB possono rappresentare un'immagine del profilo di un utente, una foto del dispositivo, un video, una mappa o un log.

[!INCLUDE [Digital Twins Management API familiarity](../../includes/digital-twins-familiarity.md)]

## <a name="uploading-blobs-an-overview"></a>Caricamento dei BLOB: panoramica

È possibile usare richieste multipart per caricare i BLOB e le relative funzionalità in endpoint specifici.

> [!IMPORTANT]
> Per le richieste multipart sono necessarie tre informazioni:
> * Un'intestazione **Content-Type**:
>   * `application/json; charset=utf-8`
>   * `multipart/form-data; boundary="USER_DEFINED_BOUNDARY"`
> * Un'informazione **Content-Disposition**: `form-data; name="metadata"`
> * Il contenuto del file da caricare
>
> Le informazioni **Content-Type** e **Content-Disposition** possono variare in base agli scenari d'uso.

Le richieste multipart inviate alle API Gestione di Gemelli digitali di Azure sono suddivise in due parti:

* Metadati BLOB, ad esempio un tipo MIME associato, come indicato nelle informazioni **Content-Type** e **Content-Disposition**

* Contenuto del BLOB (contenuto non strutturato del file)  

Per le richieste **PATCH** non è necessaria nessuna delle due parti, mentre entrambe sono necessarie per le operazioni **POST** o di creazione.

### <a name="blob-metadata"></a>Metadati BLOB

Oltre a **Content-Type** e **Content-Disposition**, le richieste multipart devono specificare il corpo JSON corretto. Il corpo JSON da inviare dipende dal tipo di operazione di richiesta HTTP che viene eseguita.

I quattro schemi JSON principali sono i seguenti:

![Schemi JSON][1]

La documentazione di Swagger descrive in dettaglio questi schemi del modello.

[!INCLUDE [Digital Twins Swagger](../../includes/digital-twins-swagger.md)]

Per informazioni sull'uso della documentazione di riferimento, vedere [Come usare Swagger](./how-to-use-swagger.md).

### <a name="examples"></a>Esempi

[!INCLUDE [Digital Twins Management API](../../includes/digital-twins-management-api.md)]

Per creare un richiesta **POST** che carica un file di testo come BLOB e lo associa a uno spazio:

```plaintext
POST YOUR_MANAGEMENT_API_URL/spaces/blobs HTTP/1.1
Content-Type: multipart/form-data; boundary="USER_DEFINED_BOUNDARY"

--USER_DEFINED_BOUNDARY
Content-Type: application/json; charset=utf-8
Content-Disposition: form-data; name="metadata"

{
  "ParentId": "54213cf5-285f-e611-80c3-000d3a320e1e",
  "Name": "My First Blob",
  "Type": "Map",
  "SubType": "GenericMap",
  "Description": "A well chosen description",
  "Sharing": "None"
}
--USER_DEFINED_BOUNDARY
Content-Disposition: form-data; name="contents"; filename="myblob.txt"
Content-Type: text/plain

This is my blob content. In this case, some text, but I could also be uploading a picture, an JSON file, a firmware zip, etc.

--USER_DEFINED_BOUNDARY--
```

| Valore | Sostituire con |
| --- | --- |
| USER_DEFINED_BOUNDARY | Nome di un limite di contenuto multipart |

Il codice seguente è un'implementazione .NET dello stesso caricamento di BLOB tramite la classe [MultipartFormDataContent](https://docs.microsoft.com/dotnet/api/system.net.http.multipartformdatacontent):

```csharp
//Supply your metadata in a suitable format
var multipartContent = new MultipartFormDataContent("USER_DEFINED_BOUNDARY");

var metadataContent = new StringContent(JsonConvert.SerializeObject(metaData), Encoding.UTF8, "application/json");
metadataContent.Headers.ContentType = MediaTypeHeaderValue.Parse("application/json; charset=utf-8");
multipartContent.Add(metadataContent, "metadata");

var fileContents = new StringContent("MY_BLOB.txt");
fileContents.Headers.ContentType = MediaTypeHeaderValue.Parse("text/plain");
multipartContent.Add(fileContents, "contents");

var response = await httpClient.PostAsync("spaces/blobs", multipartContent);
```

## <a name="api-endpoints"></a>Endpoint API

Le sezioni seguenti descrivono i principali endpoint API correlati ai BLOB e le relative funzionalità.

### <a name="devices"></a>Dispositivi

È possibile associare i BLOB a dispositivi. L'immagine seguente illustra la documentazione di riferimento di Swagger per le API Gestione. Specifica gli endpoint API associati al dispositivo per l'uso di BLOB ed eventuali parametri di percorso richiesti da passare al loro interno.

![BLOB correlati ai dispositivi][2]

Ad esempio, per aggiornare o creare un BLOB e collegarlo a un dispositivo inviare una richiesta **PATCH** al seguente percorso:

```plaintext
YOUR_MANAGEMENT_API_URL/devices/blobs/YOUR_BLOB_ID
```

| Parametro | Sostituire con |
| --- | --- |
| *YOUR_BLOB_ID* | ID del BLOB desiderato |

Le richieste corrette restituiscono un oggetto JSON **DeviceBlob** nella risposta. Gli oggetti **DeviceBlob** sono conformi allo schema JSON seguente:

| Attributo | type | DESCRIZIONE | Esempi |
| --- | --- | --- | --- |
| **DeviceBlobType** | string | Categoria di BLOB che può essere collegata a un dispositivo | `Model` e `Specification` |
| **DeviceBlobSubtype** | string | Sottocategoria di BLOB più specifica rispetto a **DeviceBlobType** | `PhysicalModel`, `LogicalModel`, `KitSpecification` e `FunctionalSpecification` |

> [!TIP]
> La tabella precedente consente di gestire i dati della richiesta restituiti.

### <a name="spaces"></a>Spazi

È anche possibile associare i BLOB a spazi. L'immagine seguente elenca tutti gli endpoint API di spazi responsabili della gestione di BLOB. Elenca anche i parametri di percorso da passare in tali endpoint.

![BLOB correlati agli spazi][3]

Ad esempio, per restituire un BLOB collegato a uno spazio, inviare una richiesta **GET** al seguente percorso:

```plaintext
YOUR_MANAGEMENT_API_URL/spaces/blobs/YOUR_BLOB_ID
```

| Parametro | Sostituire con |
| --- | --- |
| *YOUR_BLOB_ID* | ID del BLOB desiderato |

L'invio di una richiesta **PATCH** allo stesso endpoint consente aggiornare una descrizione dei metadati e di creare una nuova versione del BLOB. La richiesta HTTP viene effettuata tramite il metodo **PATCH** con tutti i metadati e i dati modulo multipart necessari.

Le operazioni riuscite restituiscono un oggetto **SpaceBlob** conforme allo schema seguente. È possibile usarlo per i dati restituiti.

| Attributo | type | DESCRIZIONE | Esempi |
| --- | --- | --- | --- |
| **SpaceBlobType** | string | Categoria di BLOB che può essere collegata a uno spazio | `Map` e `Image` |
| **SpaceBlobSubtype** | string | Sottocategoria di BLOB più specifica rispetto a **SpaceBlobType** | `GenericMap`, `ElectricalMap`, `SatelliteMap` e `WayfindingMap` |

### <a name="users"></a>Utenti

È possibile collegare BLOB a modelli utente (ad esempio per associare un'immagine del profilo). L'immagine seguente illustra gli endpoint API utente pertinenti e i parametri di percorso necessari, ad esempio `id`:

![BLOB correlati agli utenti][4]

Ad esempio, per recuperare un BLOB collegato a un utente, inviare una richiesta **GET** con tutti i dati modulo necessari:

```plaintext
YOUR_MANAGEMENT_API_URL/users/blobs/YOUR_BLOB_ID
```

| Parametro | Sostituire con |
| --- | --- |
| *YOUR_BLOB_ID* | ID del BLOB desiderato |

Il codice JSON (oggetti **UserBlob**) restituito è conforme ai modelli JSON seguenti:

| Attributo | type | DESCRIZIONE | Esempi |
| --- | --- | --- | --- |
| **UserBlobType** | string | Categoria di BLOB che può essere collegata a un utente | `Image` e `Video` |
| **UserBlobSubtype** |  string | Sottocategoria di BLOB più specifica rispetto a **UserBlobType** | `ProfessionalImage`, `VacationImage` e `CommercialVideo` |

## <a name="common-errors"></a>Errori comuni

Un errore comune è quello di non includere le informazioni di intestazione corrette:

```JSON
{
    "error": {
        "code": "400.600.000.000",
        "message": "Invalid media type in first section."
    }
}
```

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulla documentazione di riferimento di Swagger per Gemelli digitali di Azure, vedere [Usare Swagger per Gemelli digitali](how-to-use-swagger.md).

<!-- Images -->
[1]: media/how-to-add-blobs/blob-models.PNG
[2]: media/how-to-add-blobs/blobs-device-api.PNG
[3]: media/how-to-add-blobs/blobs-space-api.PNG
[4]: media/how-to-add-blobs/blobs-users-api.PNG
