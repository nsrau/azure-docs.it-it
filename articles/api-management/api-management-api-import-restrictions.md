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
ms.openlocfilehash: d4229a3ecbcf8aa044eb448290c243e9920bd5cb
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/03/2018
---
# <a name="api-import-restrictions-and-known-issues"></a>Problemi noti e limitazioni dell'importazione dell'API
## <a name="about-this-list"></a>Informazioni sull'elenco
Quando si importa un'API è possibile riscontrare delle restrizioni o identificare dei problemi che è necessario correggere per completare l'importazione. L'articolo illustra questi aspetti, organizzati in base al formato di importazione dell'API.

## <a name="open-api"></a>Aprire l'API/Swagger
Se si ricevono errori durante l'importazione del documento Open API, assicurarsi che sia stato convalidato: usare la finestra di progettazione nel portale di Azure (Progettazione - Front End - Aprire l'editor della specifica API) o usare uno strumento di terze parti come <a href="http://www.swagger.io">Swagger Editor</a>.

* **Nome host** Gestione API richiede un attributo per il nome host.
* **Percorso base** Gestione API richiede un attributo per il percorso base.
* **Schemi** Gestione API richiede una matrice di schemi.

> [!IMPORTANT]
> Vedere questo [documento](https://blogs.msdn.microsoft.com/apimanagement/2018/03/28/important-changes-to-openapi-import-and-export/) per informazioni importanti e suggerimenti correlati all'importazione OpenAPI.

## <a name="wsdl"></a>WSDL
I file WSDL vengono usati per generare le API SOAP pass-through o come back-end dell'API SOAP-REST.

* **WSDL:Import**: le API che usano questo attributo non sono attualmente supportate da Gestione API. I clienti devono unire gli elementi importati in un solo documento.
* **Messaggi con più parti**: questo tipo di messaggi non è attualmente supportato da Gestione API.
* I servizi SOAP **wsHttpBinding di WCF** creati con Windows Communication Foundation devono usare basicHttpBinding in quanto wsHttpBinding non è supportato.
* **MTOM**: i servizi che usano MTOM <em>potrebbero</em> funzionare. Al momento il supporto ufficiale non è previsto.
* Gestione API non supporta i tipi **ricorsivi** che sono definiti in modo ricorsivo, ad esempio che fanno riferimento a una matrice di se stessi.

## <a name="wadl"> </a>WADL
Attualmente non sono noti problemi di importazione del formato WADL.
