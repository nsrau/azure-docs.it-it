---
title: Connetti a Google Drive
description: Automatizzare i flussi di lavoro che creano e gestiscono file per Google Drive usando app per la logica di Azure
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 11/07/2016
tags: connectors
ms.openlocfilehash: 155a579438747ca7de67eba6449b8a0191e86e2c
ms.sourcegitcommit: ff9688050000593146b509a5da18fbf64e24fbeb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/06/2020
ms.locfileid: "75666840"
---
# <a name="create-and-manage-files-for-google-drive-by-using-azure-logic-apps"></a>Creare e gestire file per Google Drive usando app per la logica di Azure

Connettersi a Google Drive per creare file, recuperare righe e così via. Con Google Drive è possibile: 

* Creare il flusso aziendale in base ai dati ottenuti dalla ricerca. 
* Usare azioni per cercare immagini, notizie e così via. Queste azioni ottengono una risposta e quindi rendono l'output disponibile per altre azioni. È ad esempio possibile cercare un video e quindi usare Twitter per pubblicarlo in un feed Twitter.

Per iniziare subito a creare un'app per la logica, vedere [Creare un'app per la logica](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="create-the-connection-to-google-drive"></a>Creare una connessione a Google Drive

Quando si aggiunge questo connettore alle app per la logica, è necessario autorizzare le app per la logica per la connessione a Google Drive.

> [!INCLUDE [Steps to create a connection to googledrive](../../includes/connectors-create-api-googledrive.md)]

Dopo aver creato la connessione immettere le proprietà di Google Drive, ad esempio nome file o percorso cartella. 

## <a name="connector-specific-details"></a>Dettagli specifici del connettore

Per informazioni tecniche su trigger, azioni e limiti, descritti dalla descrizione di spavalderia del connettore, vedere la [pagina di riferimento del connettore](/connectors/googledrive/).

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su altri [connettori di App per la logica](apis-list.md)
