---
title: Eseguire la migrazione di anteprima delle knowledge base - Qna Maker
titleSuffix: Azure Cognitive Services
description: Come importare una knowledge base
services: cognitive-services
author: tulasim88
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: article
ms.date: 09/12/2018
ms.author: tulasim
ms.openlocfilehash: 0cb8a185407c7b180a170f1f9b9d76aa28a24de5
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/24/2018
ms.locfileid: "47031629"
---
# <a name="migrate-a-knowledge-base-using-export-import"></a>Eseguire la migrazione di una knowledge base utilizzando la funzione di importazione ed esportazione
QnA Maker ha annunciato General Availability il 7 maggio 2018 in occasione della conferenza \\\build\. QnA Maker GA ha una nuova architettura basata su Azure. Le knowledge base create con QnA Maker Free Preview dovranno essere migrate a QnA Maker GA. QnA Maker Preview sarà deprecato a novembre 2018. Per ulteriori informazioni sulle modifiche apportate a QnA Maker GA, vedere il [post di blog](https://aka.ms/qnamakerga-blog) con l'annuncio di QnA Maker GA.

QnA Maker include ora un [modello di determinazione prezzi](https://azure.microsoft.com/en-us/pricing/details/cognitive-services/qna-maker/).

Prerequisiti
> [!div class="checklist"]
> * Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.
> * Configurare un nuovo [servizio QnA Maker](../How-To/set-up-qnamaker-service-azure.md)

## <a name="migrate-a-knowledge-base-from-qna-maker-preview-portal"></a>Eseguire la migrazione di una knowledge base dal portale QnA Maker Preview
1. Accedere al [portale QnA Maker Preview](https://aka.ms/qnamaker-old-portal
) e fare clic su **Servizi personali**.
2. Selezionare la knowledge base a cui si desidera eseguire la migrazione facendo clic sull'icona di modifica.

    ![Modificare la knowledge base](../media/qnamaker-how-to-migrate-kb/preview-editkb.png)

3. Fare clic su **Scarica knowledge base** per scaricare un file .tsv che contiene il contenuto della knowledge Base: domande, risposte, metadati e i nomi delle fonti di dati da cui sono stati estratti.

    ![Scaricare una knowledge base](../media/qnamaker-how-to-migrate-kb/preview-download.png)

4. Accedere al [portale di QnA Maker](https://qnamaker.ai) con le credenziali di Azure e fare clic su **Crea nuovo servizio**.

    ![Creare la knowledge base ](../media/qnamaker-how-to-create-kb/create-new-service.png)
    
5. Se non è già stato creato un servizio QnA Maker, selezionare **Create a QnA service** (Crea servizio QnA). In caso contrario, scegliere un servizio QnA Maker negli elenchi a discesa nel passaggio 2. Selezionare il servizio QnA Maker che ospiterà la knowledge base.

    ![Configurare il servizio QnA](../media/qnamaker-how-to-create-kb/setup-qna-resource.png)

6. Creare una knowledge base vuota 

    ![Impostare le origini dati](../media/qnamaker-how-to-create-kb/set-data-sources.png)

    - Assegnare un **nome** al servizio. Sono supportati nomi duplicati e caratteri speciali.
    - Ignorare il caricamento di file o URL per utilizzare i dati dalla knowledge base di anteprima. Per ora, si creerà una knowledge base vuota.

7. Selezionare **Create**.

    ![Creare la knowledge base](../media/qnamaker-how-to-create-kb/create-kb.png)

8. In questa nuova knowledge base, aprire la scheda **Impostazioni** e fare clic su **Importa knowledge base**. Vengono importate le domande, le risposte e i metadati e conservati i nomi delle fonti di dati da cui sono stati estratti.

   ![Importare una knowledge base](../media/qnamaker-how-to-migrate-kb/Import.png)

9. **Testare** la nuova knowledge base mediante il pannello di Test. Leggere le informazioni su come [testare la knowledge base](../How-To/test-knowledge-base.md).
10. **Pubblicare** la knowledge base. Leggere le informazioni su come [pubblicare la knowledge base](../How-To/publish-knowledge-base.md).
11. Utilizza l'endpoint sotto all’applicazione o al codice bot. Vedere di seguito come [creare un bot QnA](../Tutorials/create-qna-bot.md).

    ![Valori QnA Maker](../media/qnamaker-tutorials-create-bot/qnamaker-settings-kbid-key.PNG)

A questo punto, tutti i contenuti della knowledge base, domande, risposte e metadati, insieme ai nomi dei file sorgente e agli URL, vengono importati nella nuova knowledge base. 

## <a name="chatlogs-and-alterations"></a>Chatlog e modifiche
Le modifiche (sinonimi) non vengono importate automaticamente. Usare la [API V2](https://aka.ms/qnamaker-v2-apis) per esportare le modifiche dallo stack di anteprima e le [API V4](https://aka.ms/qnamaker-v4-apis) per sostituire il nuovo stack.

Non è possibile eseguire la migrazione di chatlog, poiché il nuovo stack usa Application Insights per l'archiviazione dei chatlog. È tuttavia possibile scaricare chatlog dal [portale di anteprima](https://aka.ms/qnamaker-old-portal).

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Modificare una knowledge base](../How-To/edit-knowledge-base.md)
