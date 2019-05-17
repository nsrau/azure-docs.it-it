---
title: Eseguire la migrazione delle knowledge base - QnA Maker
titleSuffix: Azure Cognitive Services
description: Il processo di migrazione consiste nell'esportazione del contenuto di una knowledge base e nella relativa importazione in un'altra knowledge base.
services: cognitive-services
author: tulasim88
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 04/08/2019
ms.author: tulasim
ms.custom: seodec18
ms.openlocfilehash: 8ff3c497372a761bd8a02ae81bc897c8ee297bd0
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/16/2019
ms.locfileid: "65794876"
---
# <a name="migrate-a-knowledge-base-using-export-import"></a>Eseguire la migrazione di una knowledge base utilizzando la funzione di importazione ed esportazione

Il processo di migrazione consiste nell'esportazione del contenuto di una knowledge base e nella relativa importazione in un'altra knowledge base. 

## <a name="prerequisites"></a>Prerequisiti

* Prima di iniziare, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Configurare un nuovo [servizio QnA Maker](../How-To/set-up-qnamaker-service-azure.md)

## <a name="migrate-a-knowledge-base-from-qna-maker"></a>Eseguire la migrazione di una knowledge base da QnA Maker
1. Accedere al [portale di QnA Maker](https://qnamaker.ai).
1. Selezionare la knowledge base da spostare.

1. Nella pagina **Settings** (Impostazioni) selezionare **Export knowledge base** (Esporta knowledge base) per scaricare un file con estensione tsv con il contenuto della knowledge base: domande, risposte, metadati e i nomi delle origini dati da cui sono stati estratti.

1. Selezionare **Create a knowledge base** (Crea una knowledge base) nel menu in alto e quindi creare una knowledge base vuota. 

    ![Impostare le origini dati](../media/qnamaker-how-to-create-kb/set-data-sources.png)

    - Assegnare un **nome** al servizio. Sono supportati nomi duplicati e caratteri speciali.

1. Selezionare **Create**.

    ![Creare la knowledge base](../media/qnamaker-how-to-create-kb/create-kb.png)

1. In questa nuova knowledge base aprire la scheda **Settings** (Impostazioni) e fare clic su **Import knowledge base** (Importa knowledge base). Vengono importate le domande, le risposte e i metadati e conservati i nomi delle fonti di dati da cui sono stati estratti.

   ![Importare una knowledge base](../media/qnamaker-how-to-migrate-kb/Import.png)

1. **Testare** la nuova knowledge base mediante il pannello di Test. Leggere le informazioni su come [testare la knowledge base](../How-To/test-knowledge-base.md).
1. **Pubblicare** la knowledge base. Leggere le informazioni su come [pubblicare la knowledge base](../Quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base).
1. Usare l'endpoint nell'applicazione o nel codice bot. Vedere di seguito come [creare un bot QnA](../Tutorials/create-qna-bot.md).

    ![Valori QnA Maker](../media/qnamaker-how-to-migrate-kb/qnamaker-settings-kbid-key.png)

    A questo punto, tutti i contenuti della knowledge base, domande, risposte e metadati, insieme ai nomi dei file sorgente e agli URL, vengono importati nella nuova knowledge base. 

## <a name="chat-logs-and-alterations"></a>Chatlog e modifiche
Le modifiche (sinonimi) senza distinzione tra maiuscole e minuscole non vengono importate automaticamente. Usare la [V4 API](https://go.microsoft.com/fwlink/?linkid=2092179) per spostare le modifiche nella nuova knowledge base.

Non è possibile eseguire la migrazione dei chatlog, poiché la nuova knowledge base usa Application Insights per l'archiviazione dei chatlog. 

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Modificare una knowledge base](../How-To/edit-knowledge-base.md)
