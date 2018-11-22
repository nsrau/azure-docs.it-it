---
title: Come aggiungere BLOB agli oggetti in Gemelli digitali di Azure | Microsoft Docs
description: Informazioni su come aggiungere BLOB agli oggetti in Gemelli digitali di Azure
author: kingdomofends
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 11/13/2018
ms.author: adgera
ms.openlocfilehash: 0929a4a63eee35d21723c980887d6b4217898682
ms.sourcegitcommit: db2cb1c4add355074c384f403c8d9fcd03d12b0c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/15/2018
ms.locfileid: "51688333"
---
# <a name="how-to-add-blobs-to-objects-in-azure-digital-twins"></a>Come aggiungere BLOB agli oggetti in Gemelli digitali di Azure

I BLOB sono rappresentazioni non strutturate di tipi di file comuni, ad esempio immagini e log. I BLOB tengono traccia del tipo di dati che rappresentano tramite l'uso di un tipo MIME (ad esempio, "image/jpeg") e dei metadati (nome, descrizione, tipo e così via).

Gemelli digitali di Azure supporta il collegamento dei BLOB a dispositivi, spazi e utenti. I BLOB possono rappresentare un'immagine del profilo di un utente, una foto del dispositivo, un video, una mappa o un log.

> [!NOTE]
> Questo articolo presuppone che:
> * L'istanza sia configurata correttamente per ricevere le richieste dell'API Gestione.
> * L'utente abbia eseguito l'autenticazione correttamente usando un client REST preferito.

## <a name="uploading-blobs-an-overview"></a>Caricamento dei BLOB: panoramica

Vengono usate richieste multipart per caricare i BLOB in endpoint specifici con le relative funzionalità.

> [!IMPORTANT]
> Per le richieste multipart sono necessarie tre informazioni fondamentali. Per Gemelli digitali di Azure si tratta delle informazioni seguenti:
> * Un'intestazione **Content-Type**:
>   * `application/json; charset=utf-8`
>   * `multipart/form-data; boundary="USER_DEFINED_BOUNDARY"`.
> * Un'informazione **Content-Disposition**: `form-data; name="metadata"`.
> * Il contenuto del file da caricare.
>
> Il contenuto esatto di **Content-Type** e **Content-Disposition** possono variare in base alle scenario.

Ogni richiesta multipart è suddivisa in diversi blocchi. Le richieste multipart inviate alle API Gestione di Gemelli digitali di Azure sono suddivise in **due** (**2**) parti di questo tipo:

1. La prima parte è obbligatoria e contiene metadati BLOB, ad esempio un tipo MIME associato in base a **Content-Type** e **Content-Disposition** indicati in precedenza.

1. La seconda parte include il contenuto BLOB effettivo, ovvero il contenuto non strutturato del file.  

Per le richieste **PATCH** non è necessaria nessuna delle due parti, mentre entrambe sono necessarie per le operazioni **POST** o di creazione.

### <a name="blob-metadata"></a>Metadati BLOB

Oltre a **Content-Type** e **Content-Disposition**, le richieste multipart devono specificare anche il corpo JSON corretto. Il tipo di corpo JSON da inviare dipende dal tipo di operazione di richiesta HTTP eseguita.

I quattro schemi JSON principali usati sono i seguenti:

![BLOB correlati agli spazi][1]

Questi schemi di modello sono illustrati in dettaglio nella documentazione di Swagger fornita.

[!INCLUDE [Digital Twins Swagger](../../includes/digital-twins-swagger.md)]

Per informazioni sull'uso della documentazione di riferimento fornita, vedere [Come usare Swagger](./how-to-use-swagger.md).

### <a name="examples"></a>Esempi

[!INCLUDE [Digital Twins Management API](../../includes/digital-twins-management-api.md)]

Per creare un richiesta POST che carica un file di testo come BLOB e lo associa a uno spazio:

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

| Valore del parametro | Sostituire con |
| --- | --- |
| *USER_DEFINED_BOUNDARY* | Nome di un limite di contenuto multipart |

Di seguito è illustrata un'implementazione .NET dello stesso caricamento BLOB tramite la classe [MultipartFormDataContent](https://docs.microsoft.com/dotnet/api/system.net.http.multipartformdatacontent):

```csharp
//Supply your metaData in a suitable format
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

Di seguito è riportata una descrizione dettagliata degli endpoint core e delle specifiche funzionalità.

### <a name="devices"></a>Dispositivi

È possibile collegare i BLOB ai dispositivi. L'immagine seguente, che illustra la documentazione di riferimento di Swagger per le API Gestione, elenca gli endpoint API correlati ai dispositivi per il consumo di BLOB e tutti i parametri di percorso necessari per passarli:

![BLOB correlati ai dispositivi][2]

Ad esempio, per aggiornare o creare un BLOB e collegarlo a un dispositivo, viene inviata una richiesta PATCH al seguente percorso:

```plaintext
YOUR_MANAGEMENT_API_URL/devices/blobs/YOUR_BLOB_ID
```

| Parametro | Sostituire con |
| --- | --- |
| *YOUR_BLOB_ID* | ID del BLOB desiderato |

Le richieste corrette restituiranno un oggetto JSON DeviceBlob nella risposta. Gli oggetti DeviceBlob sono conformi allo schema JSON seguente:

| Attributo | Tipo | Descrizione | Esempi |
| --- | --- | --- | --- |
| **DeviceBlobType** | string | Categoria di BLOB che può essere collegata a un dispositivo | `Model` e `Specification` |
| **DeviceBlobSubtype*** | string | Sottocategoria di BLOB più dettagliata rispetto a DeviceBlobType | `PhysicalModel`, `LogicalModel`, `KitSpecification` e `FunctionalSpecification` |

> [!TIP]
> La tabella precedente consente di gestire i dati della richiesta restituiti.

### <a name="spaces"></a>Spazi

I BLOB possono essere collegati anche agli spazi. L'immagine seguente visualizza l'elenco di tutti gli endpoint API correlati agli spazi responsabili della gestione dei BLOB insieme a tutti i parametri di percorso necessari per passarli:

![BLOB correlati agli spazi][3]

Ad esempio, per restituire un BLOB collegato a uno spazio, inviare una richiesta GET al seguente percorso:

```plaintext
YOUR_MANAGEMENT_API_URL/spaces/blobs/YOUR_BLOB_ID
```

| Parametro | Sostituire con |
| --- | --- |
| *YOUR_BLOB_ID* | ID del BLOB desiderato |

L'invio di una richiesta PATCH allo stesso endpoint consentirà di aggiornare una descrizione dei metadati e di creare una nuova versione del BLOB. La richiesta HTTP viene effettuata tramite il metodo PATCH insieme a tutti i metadati e i dati modulo multipart.

Se le operazioni riescono, verrà restituito un oggetto SpaceBlob conforme allo schema seguente che può essere usato per consumare i dati restituiti:

| Attributo | Tipo | Descrizione | Esempi |
| --- | --- | --- | --- |
| **SpaceBlobType** | string | Categoria di BLOB che può essere collegata a uno spazio | `Map` e `Image` |
| **SpaceBlobSubtype** | string | Sottocategoria di BLOB più dettagliata rispetto a SpaceBlobType | `GenericMap`, `ElectricalMap`, `SatelliteMap` e `WayfindingMap` |

### <a name="users"></a>Utenti

I BLOB possono essere collegati a modelli utente, ad esempio a un'immagine del profilo. L'immagine seguente illustra endpoint API pertinenti correlati agli utenti e tutti i parametri di percorso necessari, ad esempio `id`:

![BLOB correlati agli utenti][4]

Ad esempio, per recuperare un BLOB collegato a un utente, inviare una richiesta GET con tutti i dati modulo necessari:

```plaintext
YOUR_MANAGEMENT_API_URL/users/blobs/YOUR_BLOB_ID
```

| Parametro | Sostituire con |
| --- | --- |
| *YOUR_BLOB_ID* | ID del BLOB desiderato |

Gli oggetti JSON (UserBlob) restituiti saranno conformi ai modelli JSON seguenti:

| Attributo | Tipo | Descrizione | Esempi |
| --- | --- | --- | --- |
| **UserBlobType** | string | Categoria di BLOB che può essere collegata a un utente | `Image` e `Video` |
| **UserBlobSubtype** |  string | Sottocategoria di BLOB più dettagliata rispetto a UserBlobType | `ProfessionalImage`, `VacationImage` e `CommercialVideo` |

## <a name="common-errors"></a>Errori comuni

Informazioni di intestazione corrette non incluse:

```JSON
{
    "error": {
        "code": "400.600.000.000",
        "message": "Invalid media type in first section."
    }
}
```

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulla documentazione di riferimento di Swagger di Gemelli digitali di Azure, vedere [Come usare Swagger di Gemelli digitali](how-to-use-swagger.md).

<!-- Images -->
[1]: media/how-to-add-blobs/blob-models.PNG
[2]: media/how-to-add-blobs/blobs-device-api.PNG
[3]: media/how-to-add-blobs/blobs-space-api.PNG
[4]: media/how-to-add-blobs/blobs-users-api.PNG
