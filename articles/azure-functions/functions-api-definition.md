---
title: Metadati OpenAPI in Funzioni di Azure | Microsoft Docs
description: Panoramica del supporto OpenAPI in Funzioni di Azure
services: functions
author: alexkarcher-msft
manager: jeconnoc
ms.assetid: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 03/23/2017
ms.author: alkarche
ms.openlocfilehash: 6d11961f06a75341e633c7a8963e6b83ed37cf13
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58076735"
---
# <a name="openapi-20-metadata-support-in-azure-functions-preview"></a>Supporto per metadati OpenAPI 2.0 in Funzioni di Azure (anteprima)
Il supporto per metadati OpenAPI 2.0 (in precedenza Swagger) in Funzioni di Azure è una funzionalità di anteprima che consente di scrivere una definizione di OpenAPI 2.0 all'interno di un'app per le funzioni. Con l'app per le funzioni è poi possibile ospitare quel file.

> [!IMPORTANT]
> La funzionalità di anteprima di OpenAPI è attualmente disponibile solo nella versione 1.x del runtime. [Qui](./functions-versions.md#creating-1x-apps) è possibile trovare informazioni su come creare un'app per le funzioni 1.x.

I [metadati OpenAPI](https://swagger.io/) consentono a una funzione che ospita un'API REST di essere usata da una vasta gamma di altri software, tra cui le offerte Microsoft, quali PowerApps e la [funzionalità app per le api di Servizio app di Azure ](../app-service/overview.md), gli strumenti di sviluppo di terze parti come [Postman](https://www.getpostman.com/docs/importing_swagger) e [molti altri pacchetti](https://swagger.io/tools/).

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

>[!TIP]
>È consigliabile iniziare con l'[esercitazione introduttiva](./functions-api-definition-getting-started.md) e tornare successivamente a questo documento per approfondire le funzionalità specifiche.

## <a name="enable"></a>Abilitare il supporto per la definizione OpenAPI
È possibile configurare tutte le impostazioni OpenAPI nella pagina **Definizione API** in **Funzionalità della piattaforma** per l'app per le funzioni.

> [!NOTE]
> La funzionalità Definizione dell'API della funzione non è attualmente supportata per il runtime beta.

Per abilitare la generazione di una definizione OpenAPI ospitata e una definizione di avvio rapido, impostare **Origine della definizione dell'API** su **Funzione (anteprima)**. L'**URL esterno** consente alla funzione di usare una definizione OpenAPI ospitata altrove.

## <a name="generate-definition"></a>Generare una struttura Swagger dai metadati della funzione
Per iniziare a scrivere la prima definizione OpenAPI può essere utile usare un modello. La funzione del modello di definizione crea una definizione OpenAPI di tipo sparse usando tutti i metadati contenuti nel file function.json per ognuna delle funzioni trigger HTTP. Le altre informazioni relative all'API, ad esempio i modelli di richiesta e risposta, devono essere inserite nella [specifica OpenAPI](https://swagger.io/specification/).

Per istruzioni dettagliate, vedere l'[esercitazione introduttiva](./functions-api-definition-getting-started.md).

### <a name="templates"></a>Modelli disponibili

|NOME| DESCRIZIONE |
|:-----|:-----|
|Definizione generata|Una definizione OpenAPI con la quantità massima di informazioni deducibili dai metadati esistenti della funzione.|

### <a name="quickstart-details"></a>Metadati inclusi nella definizione generata

La tabella seguente include le impostazioni del portale di Azure e i dati corrispondenti in function.json, dei quali viene eseguito il mapping nella struttura Swagger generata.

|Swagger.json|Interfaccia utente del portale|Function.json|
|:----|:-----|:-----|
|[Host](https://swagger.io/specification/#fixed-fields-15)|**Impostazioni dell'app per le funzioni** > **Impostazioni del servizio app** > **Panoramica** > **URL**|*Non presente*
|[Percorsi](https://swagger.io/specification/#paths-object-29)|**Integrazione** > **Metodi HTTP selezionati**|Binding: Route
|[Elemento del percorso](https://swagger.io/specification/#path-item-object-32)|**Integrazione** > **Modello di route**|Binding: Metodi
|[Sicurezza](https://swagger.io/specification/#security-scheme-object-112)|**Chiavi**|*Non presente*|
|operationID*|**Route + Verbi consentiti**|Route + verbi consentiti|

\*L'ID dell'operazione è necessario soltanto per l'integrazione di PowerApps e Flow.
> [!NOTE]
> L'estensione x-ms-summary indica il nome visualizzato in App per la logica, PowerApps e Flow.
>
> Per altre informazioni, vedere [Customize your Swagger definition for PowerApps](https://powerapps.microsoft.com/tutorials/customapi-how-to-swagger/) (Personalizzare la definizione Swagger per PowerApps).

## <a name="CICD"></a>Usare CI/CD per impostare una definizione dell'API

 È necessario abilitare l'hosting di definizione dell'API nel portale prima di attivare il controllo del codice sorgente per modificare la definizione dal codice sorgente. Seguire queste istruzioni:

1. Passare a **Definizione API (anteprima)** nelle impostazioni dell'app per le funzioni.
   1. Impostare **Origine della definizione dell'API** su **Funzione**.
   1. Fare clic su **Genera modello di definizione dell'API** e su **Salva** per creare una definizione del modello da modificare in un secondo momento.
   1. Prendere nota dell'URL di definizione dell'API e della chiave.
1. [Impostare l'integrazione continuata e la distribuzione continua (CI/CD)](https://docs.microsoft.com/azure/azure-functions/functions-continuous-deployment#continuous-deployment-requirements).
2. Modificare il file swagger.json nel controllo del codice sorgente in \site\wwwroot\.azurefunctions\swagger\swagger.json.

A questo punto, le modifiche apportate al file swagger.json nell'archivio sono ospitate dall'app per le funzioni con l'URL di definizione dell'API e la chiave annotate nel passaggio 1.c.

## <a name="next-steps"></a>Passaggi successivi
* [Esercitazione introduttiva](functions-api-definition-getting-started.md). Provare la procedura dettagliata per comprendere il funzionamento di una definizione OpenAPI.
* [Repository GitHub di Funzioni di Azure](https://github.com/Azure/Azure-Functions/). Controllare l'archivio di Funzioni di Azure per inviare commenti e suggerimenti relativi all'anteprima del supporto della definizione dell'API. È anche possibile segnalare un problema di GitHub su eventuali aggiornamenti.
* [Informazioni di riferimento per sviluppatori su Funzioni di Azure](functions-reference.md). Informazioni sulla codifica delle funzioni e sulla definizione di trigger e associazioni.
