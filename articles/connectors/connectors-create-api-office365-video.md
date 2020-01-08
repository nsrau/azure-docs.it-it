---
title: Connettersi a Office 365 video
description: Automatizzare le attività e i flussi di lavoro che gestiscono i video in Office 365 video usando app per la logica di Azure
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 05/18/2016
tags: connectors
ms.openlocfilehash: 8ac6b7b411e7f42dd076c5b16e7b500a819c617f
ms.sourcegitcommit: ff9688050000593146b509a5da18fbf64e24fbeb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/06/2020
ms.locfileid: "75665786"
---
# <a name="manage-videos-in-office365-video-by-using-azure-logic-apps"></a>Gestire video in Office365 video usando app per la logica di Azure

Connettersi a Office 365 Video per ottenere informazioni su un video di Office 365, recuperare un elenco di video e altro ancora. Con Office 365 Video è possibile:

* Creare un flusso aziendale basato sui dati ottenuti da Office 365 Video. 

* Usare azioni per controllare lo stato del portale video, recuperare l'elenco di tutti video in un canale e altro ancora. Queste azioni ottengono una risposta e quindi rendono l'output disponibile per altre azioni. 

Ad esempio, è possibile usare il connettore di ricerca di Bing per cercare video di Office 365 e quindi usare il connettore Office 365 Video per ottenere informazioni relative a un video specifico. Se il video soddisfa le esigenze, è possibile pubblicarlo su Facebook.

Per iniziare subito a creare un'app per la logica, vedere [Creare un'app per la logica](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="connect-to-office365-video"></a>Video sulla connessione a Office365

Quando si aggiunge questo connettore alle app per la logica, è necessario accedere all'account Office 365 Video e consentire alle app per la logica di connettersi all'account.

> [!INCLUDE [Steps to create a connection to Office 365 Video](../../includes/connectors-create-api-office365video.md)]

Dopo aver creato la connessione, immettere le proprietà di Office 365 Video, ad esempio, il nome del tenant o l'ID canale. 

## <a name="connector-specific-details"></a>Dettagli specifici del connettore

Per visualizzare eventuali azioni e trigger definiti in Swagger ed eventuali limiti, vedere i [dettagli del connettore](/connectors/office365videoconnector/).

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su altri [connettori di App per la logica](../connectors/apis-list.md)