---
title: Eseguire la migrazione delle knowledge base - QnA Maker
description: Il processo di migrazione consiste nell'esportazione del contenuto di una knowledge base e nella relativa importazione in un'altra knowledge base.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: how-to
ms.date: 11/09/2020
ms.openlocfilehash: 4378a648b8b7a545c9e4b638d08592aa32fff3aa
ms.sourcegitcommit: 0dcafc8436a0fe3ba12cb82384d6b69c9a6b9536
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/10/2020
ms.locfileid: "94427667"
---
# <a name="migrate-a-knowledge-base-using-export-import"></a>Eseguire la migrazione di una knowledge base utilizzando la funzione di importazione ed esportazione

La migrazione è il processo di creazione di una nuova Knowledge base da una Knowledge Base esistente. Questa operazione può essere eseguita per diversi motivi:

* processo di backup e ripristino
* Pipeline CI/CD
* Sposta aree

Per eseguire la migrazione di una Knowledge base è necessario esportare da una Knowledge Base esistente e quindi importarla in un'altra.

> [!NOTE]
> Seguire le istruzioni seguenti per eseguire la migrazione della Knowledge Base esistente a una nuova QnA Maker gestita (anteprima).

## <a name="prerequisites"></a>Prerequisiti

* Prima di iniziare, creare un [account gratuito](https://azure.microsoft.com/free/cognitive-services/).
* Configurare un nuovo [servizio QnA Maker](../How-To/set-up-qnamaker-service-azure.md)

## <a name="migrate-a-knowledge-base-from-qna-maker"></a>Eseguire la migrazione di una knowledge base da QnA Maker
1. Accedere al [portale di QnA Maker](https://qnamaker.ai).
1. Selezionare la Knowledge base Origin che si desidera migrare.

1. Nella pagina **Impostazioni** selezionare **Esporta Knowledge base** per scaricare un file con estensione TSV che contiene il contenuto della Knowledge base di origine: domande, risposte, metadati, richieste di completamento e i nomi delle origini dati da cui sono stati estratti. Gli ID QnA esportati con domande e risposte possono essere usati per aggiornare una coppia di QnA specifica tramite l' [API di aggiornamento](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/update). L'ID QnA per una coppia di QnA specifica rimane invariato tra più operazioni di esportazione.

1. Selezionare **Crea una Knowledge base** dal menu in alto e quindi creare una Knowledge base _vuota_ . È vuota perché quando viene creata, non verrà aggiunto alcun URL o file. Questi vengono aggiunti durante il passaggio di importazione, dopo la creazione.

    Configurare la Knowledge base. Imposta solo il nuovo nome della Knowledge base. Sono supportati nomi duplicati e caratteri speciali.

    Non selezionare nulla nel passaggio 4 perché questi valori verranno sovrascritti quando si importa il file.

1. Nel passaggio 5 Selezionare **Crea**.

1. In questa nuova knowledge base aprire la scheda **Settings** (Impostazioni) e fare clic su **Import knowledge base** (Importa knowledge base). Questa operazione importa le domande, le risposte, i metadati, le richieste di completamento e mantiene i nomi delle origini dati da cui sono stati estratti. **Le coppie di QnA create nella nuova Knowledge base avranno lo stesso ID QnA presente nel file esportato**. Ciò consente di creare una replica esatta della Knowledge base.

   > [!div class="mx-imgBorder"]
   > [![Importare una knowledge base](../media/qnamaker-how-to-migrate-kb/Import.png)](../media/qnamaker-how-to-migrate-kb/Import.png#lightbox)

1. **Testare** la nuova knowledge base mediante il pannello di Test. Leggere le informazioni su come [testare la knowledge base](../How-To/test-knowledge-base.md).

1. **Pubblicare** la Knowledge base e creare un bot di chat. Leggere le informazioni su come [pubblicare la knowledge base](../Quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base).

## <a name="programmatically-migrate-a-knowledge-base-from-qna-maker"></a>Eseguire la migrazione a livello di codice di una Knowledge base da QnA Maker

Il processo di migrazione è disponibile a livello di codice usando le API REST seguenti:

**Export**

* [Scarica API Knowledge base](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker4.0/knowledgebase/download)

**Importa**

* [Sostituisci API (ricarica con lo stesso ID Knowledge base)](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker4.0/knowledgebase/replace)
* [Crea API (carica con nuovo ID Knowledge base)](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker4.0/knowledgebase/create)


## <a name="chat-logs-and-alterations"></a>Chatlog e modifiche
Le modifiche (sinonimi) senza distinzione tra maiuscole e minuscole non vengono importate automaticamente. Usare le [API V4](https://go.microsoft.com/fwlink/?linkid=2092179) per spostare le modifiche nella nuova Knowledge base.

Non è possibile eseguire la migrazione dei chatlog, poiché la nuova knowledge base usa Application Insights per l'archiviazione dei chatlog.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Modificare una knowledge base](../How-To/edit-knowledge-base.md)
