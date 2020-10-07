---
title: Knowledge base di progettazione-QnA Maker
description: Una QnA Maker Knowledge base è costituita da un set di coppie di domande e risposte (QnA) e di metadati facoltativi associati a ogni coppia di QnA.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: reference
ms.date: 09/01/2020
ms.openlocfilehash: 363af72df89be9104e4d946bf8da0d44908382d4
ms.sourcegitcommit: ef69245ca06aa16775d4232b790b142b53a0c248
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/06/2020
ms.locfileid: "91776222"
---
# <a name="question-and-answer-pair-concepts"></a>Concetti delle coppie di domande e risposte

Una Knowledge base è costituita da coppie di domande e risposte (QnA).  Ogni coppia ha una sola risposta e una coppia contiene tutte le informazioni associate a tale _risposta_. Una risposta può somigliare vagamente a una riga di database o a un'istanza di struttura di dati.

## <a name="question-and-answer-pairs"></a>Coppie di domande e risposte

Le impostazioni **necessarie** in una coppia di domande e risposte (QnA) sono:

* una **domanda** -testo della query utente, usato per QnA Maker Machine Learning, per allinearsi con il testo della domanda dell'utente con una formulazione diversa, ma con la stessa risposta
* risposta **: la** risposta della coppia è la risposta che viene restituita quando una query utente corrisponde alla domanda associata

Ogni coppia è rappresentata da un **ID**.

Le impostazioni **facoltative** per una coppia includono:

* **Forme alternative della domanda** : Questo consente di QnA Maker restituire la risposta corretta per una più ampia gamma di formulazioni di domande
* **Metadati**: i metadati sono tag associati a una coppia di QnA e sono rappresentati come coppie chiave-valore. I tag di metadati vengono usati per filtrare le coppie di QnA e limitano il set su cui viene eseguita la query corrispondente.
* **Prompt a più turni**, usati per continuare una conversazione a più turni

![Knowledge base di QnA Maker](media/qnamaker-concepts-knowledgebase/knowledgebase.png)

## <a name="editorially-add-to-knowledge-base"></a>Aggiunta di contenuti a livello editoriale alla knowledge base

Se non si dispone di contenuto preesistente per popolare la Knowledge base, è possibile aggiungere le coppie QnA in modo editoriale nel portale di QnA Maker. Informazioni su come aggiornare la Knowledge Base sono disponibili [qui](How-To/edit-knowledge-base.md).

## <a name="editing-your-knowledge-base-locally"></a>Modificare la knowledge base in locale

Dopo aver creato una knowledge base, si consiglia di apportare modifiche al testo della knowledge base nel [portale di QnA Maker](https://qnamaker.ai), invece di esportare e reimportare tramite file locali. Tuttavia, potrebbe essere talvolta necessario modificare una knowledge base in locale.

Esportare la knowledge base dalla pagina **Impostazioni** e modificarla con Microsoft Excel. Se si sceglie di utilizzare un'altra applicazione per modificare il file esportato, l'applicazione può presentare errori di sintassi perché non è conforme completamente a TSV. File TSV Microsoft Excel, in genere non introducono eventuali errori di formattazione.

Dopo aver completato le modifiche, reimportare il file TSV dalla pagina **Impostazioni**. In questo modo si sostituirà completamente la knowledge base corrente con la knowledge base importata.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Ciclo di vita della knowledge base in QnA Maker](Concepts/development-lifecycle-knowledge-base.md)
