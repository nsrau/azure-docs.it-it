---
title: Eseguire la migrazione delle knowledge base - QnA Maker
description: Il processo di migrazione consiste nell'esportazione del contenuto di una knowledge base e nella relativa importazione in un'altra knowledge base.
ms.topic: article
ms.date: 03/25/2020
ms.openlocfilehash: 13e5e79bf4eaf6ec59e41b3e12aa1bb23f2c1578
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80258091"
---
# <a name="migrate-a-knowledge-base-using-export-import"></a>Eseguire la migrazione di una knowledge base utilizzando la funzione di importazione ed esportazione

La migrazione è il processo di creazione di una nuova Knowledge Base da una Knowledge Base esistente. È possibile eseguire questa operazione per diversi motivi:You may do this for several reasons:

* processo di backup e ripristino
* Pipeline CI/CD
* spostare le regioni

La migrazione di una Knowledge Base richiede l'esportazione da una Knowledge Base esistente, quindi l'importazione in un'altra.

## <a name="prerequisites"></a>Prerequisiti

* Prima di iniziare, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Configurare un nuovo [servizio QnA Maker](../How-To/set-up-qnamaker-service-azure.md)

## <a name="migrate-a-knowledge-base-from-qna-maker"></a>Eseguire la migrazione di una knowledge base da QnA Maker
1. Accedere al [portale di QnA Maker](https://qnamaker.ai).
1. Selezionare la Knowledge Base di origine di cui si desidera eseguire la migrazione.

1. Nella pagina **Impostazioni** selezionare **Esporta Knowledge Base** per scaricare un file con estensione tsv contenente il contenuto della Knowledge Base di origine: domande, risposte, metadati, prompt di completamento e i nomi delle origini dati da cui sono stati estratti.

1. Selezionare **Crea una Knowledge Base** dal menu superiore, quindi creare una Knowledge Base _vuota._ È vuoto perché quando lo crei, non aggiungerai URL o file. Questi vengono aggiunti durante il passaggio di importazione, dopo la creazione.

    Configurare la Knowledge Base. Impostare solo il nuovo nome della Knowledge Base. Sono supportati nomi duplicati e caratteri speciali.

    Non selezionare nulla dal passaggio 4 perché tali valori verranno sovrascritti quando si importa il file.

1. Nel passaggio 5 selezionare **Crea**.

1. In questa nuova knowledge base aprire la scheda **Settings** (Impostazioni) e fare clic su **Import knowledge base** (Importa knowledge base). In questo modo vengono importate le domande, le risposte, i metadati, le istruzioni di completamento e vengono mantenuti i nomi delle origini dati da cui sono stati estratti.

   > [!div class="mx-imgBorder"]
   > [![Importare una knowledge base](../media/qnamaker-how-to-migrate-kb/Import.png)](../media/qnamaker-how-to-migrate-kb/Import.png#lightbox)

1. **Testare** la nuova knowledge base mediante il pannello di Test. Leggere le informazioni su come [testare la knowledge base](../How-To/test-knowledge-base.md).

1. **Pubblicare** la Knowledge Base e creare un bot di chat. Leggere le informazioni su come [pubblicare la knowledge base](../Quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base).

## <a name="programmatically-migrate-a-knowledge-base-from-qna-maker"></a>Eseguire la migrazione di una Knowledge Base da QnA MakerProgrammatically migrate a knowledge base from QnA Maker

The migration process is programmatically available using the following REST APIs:

**Esporta**

* [Scarica l'API della Knowledge Base](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/download)

**Importa**

* [Sostituisci API (ricarica con lo stesso ID knowledge base)](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/replace)
* [Crea API (carica con nuovo ID knowledge base)](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/create)


## <a name="chat-logs-and-alterations"></a>Chatlog e modifiche
Le modifiche (sinonimi) senza distinzione tra maiuscole e minuscole non vengono importate automaticamente. Usare le [API V4](https://go.microsoft.com/fwlink/?linkid=2092179) per spostare le modifiche nella nuova Knowledge Base.

Non è possibile eseguire la migrazione dei chatlog, poiché la nuova knowledge base usa Application Insights per l'archiviazione dei chatlog.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Modificare una knowledge base](../How-To/edit-knowledge-base.md)
