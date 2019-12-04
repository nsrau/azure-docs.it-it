---
title: Connetti a box
description: Creare e gestire i file con le API REST di Box e App per la logica di Azure
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 11/07/2016
tags: connectors
ms.openlocfilehash: 38e061e918d445de07961af1789891f44c59090a
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74789871"
---
# <a name="create-and-manage-files-in-box-with-azure-logic-apps"></a>Creare e gestire file in Box con le App per la logica di Azure

Questo articolo illustra come creare e gestire i file in Box all'interno di un'app per la logica con il connettore Box. In questo modo, è possibile creare app per la logica che automatizzano le attività e i flussi di lavoro per la gestione dei file e altre azioni, ad esempio:

* Creare il flusso aziendale in base ai dati ottenuti da Box.

* Attivare attività e flussi di lavoro automatizzati quando viene creato o aggiornato un file.

* Eseguire un'azione che copia un file o Elimina un file.

  Quando queste azioni ottengono una risposta, rendono l'output disponibile per altre azioni. 
  Ad esempio, quando un file viene modificato in Box, è possibile usare il file nella posta elettronica tramite Office 365.

## <a name="prerequisites"></a>Prerequisiti

* Un [Account Box](https://www.box.com/home)

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, [iscriversi per creare un account Azure gratuito](https://azure.microsoft.com/free/). 

* L'app per la logica in cui si intende accedere all'account Box. Per avviare l'app per la logica con un trigger Box, è necessaria un'[app per la logica vuota](../logic-apps/quickstart-create-first-logic-app-workflow.md).

* Conoscenza di base di [come creare le app per la logica](../logic-apps/quickstart-create-first-logic-app-workflow.md).
Se non si ha familiarità con le app per la logica, consultare [Informazioni su App per la logica di Azure](../logic-apps/logic-apps-overview.md).

## <a name="connector-reference"></a>Informazioni di riferimento sui connettori

Per informazioni tecniche, ad esempio trigger, azioni e limiti, come descritto dal file OpenAPI (in precedenza spavalderia) del connettore, vedere la [pagina di riferimento del connettore](/connectors/box/).

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su altri [connettori di App per la logica](../connectors/apis-list.md)