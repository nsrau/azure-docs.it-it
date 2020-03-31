---
title: Connettersi a Dropbox
description: Automatizza le attività e i flussi di lavoro che caricano e gestiscono i file in Dropbox usando le app per la logica di Azure
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 03/01/2019
tags: connectors
ms.openlocfilehash: 8f54f832884b172761f62b16db29d2f0abd0dd46
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75665752"
---
# <a name="upload-and-manage-files-in-dropbox-by-using-azure-logic-apps"></a>Caricare e gestire file in Dropbox usando le app per la logica di AzureUpload and manage files in Dropbox by using Azure Logic Apps

Con il connettore Dropbox e le app per la logica di Azure, puoi creare flussi di lavoro automatizzati che caricano e gestiscono i file nel tuo account Dropbox. 

Questo articolo mostra come connettersi a Dropbox dalla tua app per la logica, quindi aggiungere il trigger Dropbox **Quando viene creato un file** e ottenere il contenuto del file Dropbox utilizzando l'azione **percorso.**

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, [iscriversi per creare un account Azure gratuito](https://azure.microsoft.com/free/).

* Un [account Dropbox,](https://www.dropbox.com/)a cui puoi iscriverti gratuitamente. Le credenziali dell'account sono necessarie per creare una connessione tra l'app per la logica e l'account Dropbox.

* Conoscenza di base di [come creare le app per la logica](../logic-apps/quickstart-create-first-logic-app-workflow.md). Per questo esempio, è necessaria un'app per la logica vuota.

## <a name="add-trigger"></a>Aggiunta di trigger

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Nella casella di ricerca scegliere **Tutti**. Nella casella di ricerca immettere "dropbox" come filtro.
Nell'elenco di trigger selezionare questo trigger: **When a file is created** (Quando viene creato un file)

   ![Selezionare un trigger Dropbox](media/connectors-create-api-dropbox/select-dropbox-trigger.png)

1. Accedere con le credenziali dell'account Dropbox e autorizzare l'accesso ai dati di Dropbox delle App per la logica di Azure.

1. Specificare le informazioni richieste per il trigger. 

   In questo esempio, selezionare la cartella in cui si desidera tenere traccia della creazione dei file. Per sfogliare le cartelle, scegliere l'icona della cartella accanto alla casella **Cartella.**

## <a name="add-action"></a>Aggiungere un'azione

Aggiungere ora un'azione che ottiene il contenuto da qualsiasi nuovo file.

1. Nel trigger scegliere **Passaggio successivo**. 

1. Nella casella di ricerca scegliere **Tutti**. Nella casella di ricerca immettere "dropbox" come filtro.
Dall'elenco delle azioni, selezionare questa azione: **Ottenere il contenuto del file utilizzando il percorso**

1. Se non hai ancora autorizzato le app per la logica di Azure ad accedere a Dropbox, autorizzi l'accesso ora.

1. Per individuare il percorso del file che **File Path** si desidera utilizzare, scegliere il pulsante con i puntini di sospensione (**... ).** 

   È inoltre possibile fare clic all'interno della casella **Percorso file** e nell'elenco del contenuto dinamico selezionare **Percorso file**, il cui valore è disponibile come output del trigger aggiunto nella sezione precedente.

1. Al termine, salvare l'app per la logica.

1. Per attivare l'app per la logica, crea un nuovo file in Dropbox.

## <a name="connector-reference"></a>Informazioni di riferimento sui connettori

Per informazioni tecniche, ad esempio su trigger, azioni e limiti indicati nel file Swagger del connettore, vedere la [pagina di riferimento del connettore](/connectors/dropbox/).

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su altri [connettori di App per la logica](../connectors/apis-list.md)
