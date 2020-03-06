---
title: Eseguire la migrazione delle knowledge base - QnA Maker
titleSuffix: Azure Cognitive Services
description: Il processo di migrazione consiste nell'esportazione del contenuto di una knowledge base e nella relativa importazione in un'altra knowledge base.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 01/28/2020
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: 942932c229ace82a0bf66da7a5421f936b028088
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78302560"
---
# <a name="migrate-a-knowledge-base-using-export-import"></a>Eseguire la migrazione di una knowledge base utilizzando la funzione di importazione ed esportazione

Il processo di migrazione consiste nell'esportazione del contenuto di una knowledge base e nella relativa importazione in un'altra knowledge base.

## <a name="prerequisites"></a>Prerequisiti

* Prima di iniziare, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Configurare un nuovo [servizio QnA Maker](../How-To/set-up-qnamaker-service-azure.md)

## <a name="migrate-a-knowledge-base-from-qna-maker"></a>Eseguire la migrazione di una knowledge base da QnA Maker
1. Accedere al [portale di QnA Maker](https://qnamaker.ai).
1. Selezionare la Knowledge base Origin che si desidera migrare.

1. Nella pagina **Impostazioni** selezionare **Esporta Knowledge base** per scaricare un file con estensione TSV che contiene il contenuto della Knowledge base di origine: domande, risposte, metadati, richieste di completamento e i nomi delle origini dati da cui sono stati estratti.

1. Selezionare **Crea una Knowledge base** dal menu in alto e quindi creare una Knowledge base _vuota_ . È vuota perché quando viene creata, non verrà aggiunto alcun URL o file. Questi vengono aggiunti durante il passaggio di importazione, dopo la creazione.

    Configurare la Knowledge base. Imposta solo il nuovo nome della Knowledge base. Sono supportati nomi duplicati e caratteri speciali.

    Non selezionare nulla nel passaggio 4 perché questi valori verranno sovrascritti quando si importa il file.

1. Nel passaggio 5 Selezionare **Crea**.

1. In questa nuova knowledge base aprire la scheda **Settings** (Impostazioni) e fare clic su **Import knowledge base** (Importa knowledge base). Questa operazione importa le domande, le risposte, i metadati, le richieste di completamento e mantiene i nomi delle origini dati da cui sono stati estratti.

   > [!div class="mx-imgBorder"]
   > [![importa Knowledge base](../media/qnamaker-how-to-migrate-kb/Import.png)](../media/qnamaker-how-to-migrate-kb/Import.png#lightbox)

1. **Testare** la nuova knowledge base mediante il pannello di Test. Leggere le informazioni su come [testare la knowledge base](../How-To/test-knowledge-base.md).

1. **Pubblicare** la Knowledge base e creare un bot di chat. Leggere le informazioni su come [pubblicare la knowledge base](../Quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base).

## <a name="chat-logs-and-alterations"></a>Chatlog e modifiche
Le modifiche (sinonimi) senza distinzione tra maiuscole e minuscole non vengono importate automaticamente. Usare le [API V4](https://go.microsoft.com/fwlink/?linkid=2092179) per spostare le modifiche nella nuova Knowledge base.

Non è possibile eseguire la migrazione dei chatlog, poiché la nuova knowledge base usa Application Insights per l'archiviazione dei chatlog.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Modificare una knowledge base](../How-To/edit-knowledge-base.md)
