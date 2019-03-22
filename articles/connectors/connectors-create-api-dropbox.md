---
title: Connettersi a Dropbox - App per la logica di Azure
description: Caricare e gestire i file con le API REST di Dropbox e App per la logica di Azure
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.date: 03/01/2019
tags: connectors
ms.openlocfilehash: 5a1bfe8ca38fc23f09b13195fb8ca5bd443a4afd
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/21/2019
ms.locfileid: "58314417"
---
# <a name="upload-and-manage-files-in-dropbox-by-using-azure-logic-apps"></a>Caricare e gestire i file in Dropbox usando le App per la logica di Azure

Con il connettore Dropbox e App per la logica di Azure, è possibile creare flussi di lavoro automatizzati che carica e gestire i file nell'account Dropbox. 

Questo articolo illustra come connettersi a Dropbox dall'app per la logica e quindi aggiungere Dropbox **quando viene creato un file** trigger e Dropbox **Ottieni contenuto file tramite percorso** azione.

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, <a href="https://azure.microsoft.com/free/" target="_blank">iscriversi per creare un account Azure gratuito</a>.

* Oggetto [account Dropbox](https://www.dropbox.com/), che è possibile iscriversi gratuitamente. Le credenziali dell'account sono necessarie per la creazione di una connessione tra l'app per la logica e all'account Dropbox.

* Conoscenza di base di [come creare le app per la logica](../logic-apps/quickstart-create-first-logic-app-workflow.md). Per questo esempio, è necessaria un'app per la logica vuota.

## <a name="add-trigger"></a>Aggiunta di trigger

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Nella casella di ricerca scegliere **Tutti**. Nella casella di ricerca immettere "dropbox" come filtro.
Nell'elenco di trigger selezionare questo trigger: **Quando viene creato un file**

   ![Selezionare un trigger Dropbox](media/connectors-create-api-dropbox/select-dropbox-trigger.png)

1. Accedere con le credenziali dell'account Dropbox e autorizzare l'accesso ai dati di Dropbox delle App per la logica di Azure.

1. Specificare le informazioni richieste per il trigger. 

   In questo esempio, selezionare la cartella in cui si desidera tenere traccia di creazione dei file. Per esplorare le cartelle, scegliere l'icona della cartella accanto al **cartella** casella.

## <a name="add-action"></a>Aggiungere un'azione

Aggiungere ora un'azione che ottiene il contenuto da qualsiasi file nuovo.

1. Nel trigger scegliere **Passaggio successivo**. 

1. Nella casella di ricerca scegliere **Tutti**. Nella casella di ricerca immettere "dropbox" come filtro.
Nell'elenco di azioni selezionare questa azione: **Ottieni contenuto file in base al percorso**

1. Se già non sono autorizzate le App per la logica di Azure per accedere a Dropbox, autorizzare l'accesso a questo punto.

1. Per passare al percorso del file da usare, accanto al **percorso File** scegliere i puntini di sospensione (**...** ) pulsante. 

   È anche possibile fare clic all'interno di **percorso File** e dall'elenco del contenuto dinamico, selezionare **percorso File**, il cui valore è disponibile come output del trigger è stato aggiunto nella sezione precedente.

1. Al termine, salvare l'app per la logica.

1. Per attivare app per la logica, creare un nuovo file in Dropbox.

## <a name="connector-reference"></a>Informazioni di riferimento sui connettori

Per informazioni tecniche, ad esempio trigger, azioni e i limiti, come descritto dalla definizione OpenAPI del connettore (in precedenza Swagger), vedere la [pagina di riferimento del connettore](/connectors/dropbox/).

## <a name="get-support"></a>Supporto

* In caso di domande, visitare il [forum di App per la logica di Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Per votare o inviare idee relative alle funzionalità, visitare il [sito dei commenti e suggerimenti degli utenti di App per la logica](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su altri [connettori di App per la logica](../connectors/apis-list.md)
