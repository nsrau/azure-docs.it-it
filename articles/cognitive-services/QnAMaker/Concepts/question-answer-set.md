---
title: Knowledge Base di progettazione - QnA Maker
description: Una Knowledge Base di QnA Maker è costituita da un set di domande e risposte (QnA) e metadati facoltativi associati a ogni coppia QnA.
ms.topic: conceptual
ms.date: 01/27/2020
ms.openlocfilehash: cb71de0f6a3e372d8c1c1fede67ba0c0354532ca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "76844320"
---
# <a name="question-and-answer-set-concepts"></a>Concetti relativi ai set di domande e risposte

Una Knowledge Base è costituita da set di domande e risposte (QnA).  Ogni set ha una risposta e un set contiene tutte le informazioni associate a tale _risposta._ Una risposta può essere simile a una riga di database o a un'istanza della struttura di dati.

## <a name="question-and-answer-sets"></a>Set di domande e risposte

Le impostazioni **richieste** in un set di domande e risposte (QnA) sono:

* una **domanda** - testo della query utente, utilizzato per l'apprendimento automatico di QnA Maker, per allineare con il testo della domanda dell'utente con parole diverse, ma la stessa risposta
* la **risposta** - la risposta del set è la risposta che viene restituita quando una query utente viene soddisfatta con la domanda associata

Ogni set è rappresentato da un **ID**.

Le impostazioni **facoltative** per un set includono:The optional settings for a set include:

* **Forme alternative della domanda** - questo aiuta QnA Maker a restituire la risposta corretta per una più ampia varietà di formulazioni di domande
* **Metadati**: I metadati sono tag associati a una coppia QnA e sono rappresentati come coppie chiave-valore. I tag di metadati vengono usati per filtrare le coppie di QnA e limitano il set su cui viene eseguita la query corrispondente.
* **Messaggi di richiesta a**più turni , utilizzati per continuare una conversazione a più turni

![Knowledge base di QnA Maker](../media/qnamaker-concepts-knowledgebase/knowledgebase.png)

## <a name="editorially-add-to-knowledge-base"></a>Aggiunta di contenuti a livello editoriale alla knowledge base

Se non si dispone di contenuto preesistente per popolare la Knowledge Base, è possibile aggiungere set QnA editorialmente nel portale QnA Maker. Informazioni su come aggiornare la Knowledge Base sono disponibili [qui](../How-To/edit-knowledge-base.md).

## <a name="editing-your-knowledge-base-locally"></a>Modificare la knowledge base in locale

Dopo aver creato una knowledge base, si consiglia di apportare modifiche al testo della knowledge base nel [portale di QnA Maker](https://qnamaker.ai), invece di esportare e reimportare tramite file locali. Tuttavia, potrebbe essere talvolta necessario modificare una knowledge base in locale.

Esportare la knowledge base dalla pagina **Impostazioni** e modificarla con Microsoft Excel. Se si sceglie di utilizzare un'altra applicazione per modificare il file esportato, l'applicazione potrebbe introdurre errori di sintassi perché non è completamente compatibile con TSV. File TSV Microsoft Excel, in genere non introducono eventuali errori di formattazione.

Dopo aver completato le modifiche, reimportare il file TSV dalla pagina **Impostazioni**. In questo modo si sostituirà completamente la knowledge base corrente con la knowledge base importata.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Ciclo di vita della knowledge base in QnA Maker](./development-lifecycle-knowledge-base.md)