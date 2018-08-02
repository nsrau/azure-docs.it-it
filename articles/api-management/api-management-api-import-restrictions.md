---
title: Limitazioni e problemi noti dell'importazione dell'API di Gestione API di Azure | Microsoft Docs
description: Dettagli di problemi noti e limitazioni relative all'importazione in Gestione API di Azure con i formati Open API, WSDL o WADL.
services: api-management
documentationcenter: ''
author: vladvino
manager: vlvinogr
editor: ''
ms.assetid: 7a5a63f0-3e72-49d3-a28c-1bb23ab495e2
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/29/2017
ms.author: apipm
ms.openlocfilehash: a9f4a4ed4a8771f32a4d66aed2457a43abb92a63
ms.sourcegitcommit: 068fc623c1bb7fb767919c4882280cad8bc33e3a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/27/2018
ms.locfileid: "39295330"
---
# <a name="api-import-restrictions-and-known-issues"></a>Problemi noti e limitazioni dell'importazione dell'API
## <a name="about-this-list"></a>Informazioni sull'elenco
Quando si importa un'API è possibile riscontrare delle restrizioni o identificare dei problemi che è necessario correggere per completare l'importazione. L'articolo illustra questi aspetti, organizzati in base al formato di importazione dell'API.

## <a name="open-api"> </a>OpenAPI/Swagger
Se si ottengono errori durante l'importazione del documento OpenAPI, assicurarsi che sia stato convalidato: usare la finestra di progettazione nel portale di Azure (Progettazione - Front End - Editor della specifica OpenAPI) o usare uno strumento di terze parti come <a href="http://editor.swagger.io">Swagger Editor</a>.

* È supportato solo il formato JSON per OpenAPI.
* I parametri obbligatori per percorso e query devono avere nomi univoci. In OpenAPI un nome di parametro deve essere univoco solo entro la posizione, ad esempio il percorso, la query o l'intestazione.  In Gestione API, invece, le operazioni possono essere discriminate da parametri sia di percorso che di query (cosa che OpenAPI non supporta). Pertanto è necessario che i nomi dei parametri siano univoci all'interno dell'intero modello di URL.
* Gli schemi a cui viene fatto riferimento tramite le proprietà **$ref** non possono contenere altre proprietà **$ref**.
* I puntatori **$ref** non possono fare riferimento a file esterni.
* **x-ms-paths** e **x-servers** sono le uniche estensioni supportate.
* Le estensioni personalizzate vengono ignorate in fase di importazione e non vengono salvate o mantenute per l'esportazione.

> [!IMPORTANT]
> Vedere questo [documento](https://blogs.msdn.microsoft.com/apimanagement/2018/04/11/important-changes-to-openapi-import-and-export/) per informazioni importanti e suggerimenti correlati all'importazione OpenAPI.

## <a name="wsdl"></a>WSDL
I file WSDL vengono usati per generare le API SOAP pass-through o come back-end dell'API SOAP-REST.
* **Associazioni SOAP**: solo le associazioni SOAP con stile di codifica "document" e "literal" sono supportate. Non è disponibile il supporto per lo stile di codifica SOAP "rpc".
* **WSDL:Import**: questo attributo non è supportato. I clienti devono unire le importazioni in un solo documento.
* **Messaggi con più parti**: questo tipo di messaggi non è supportato.
* **wsHttpBinding di WCF**: i servizi SOAP creati con Windows Communication Foundation devono usare basicHttpBinding in quanto wsHttpBinding non è supportato.
* **MTOM**: i servizi che usano MTOM <em>potrebbero</em> funzionare. Al momento il supporto ufficiale non è previsto.
* **Ricorsione**: i tipi definiti in modo ricorsivo, che, ad esempio, fanno riferimento a una matrice di se stessi, non sono supportati da APIM.

## <a name="wadl"> </a>WADL
Attualmente non sono noti problemi di importazione del formato WADL.
