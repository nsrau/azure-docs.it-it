---
title: Copiare gli esempi di data science di altre persone - Azure Machine Learning | Microsoft Docs
description: 'Il segreto commerciale dell''analisi scientifica dei dati: far svolgere il proprio lavoro ad altre persone. Esempi di machine learning della raccolta di Azure AI.'
keywords: esempi di analisi scientifica dei dati, esempio di Machine Learning, algoritmo di clustering
services: machine-learning
documentationcenter: na
author: cjgronlund
manager: jhubbard
editor: cjgronlund
ms.assetid: ec2be823-c325-4ad8-b8b2-3e664f1a44b4
ms.service: machine-learning
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/03/2018
ms.author: cgronlun
ms.openlocfilehash: d7cc0deabf6ce19e7a2b90a474e6c890fa5b76ae
ms.sourcegitcommit: 4bd369fc472dced985239aef736fece42fecfb3b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/04/2018
---
# <a name="copy-other-peoples-work-to-do-data-science"></a>Copiare il lavoro di altre persone per l'analisi scientifica dei dati
## <a name="video-5-data-science-for-beginners-series"></a>Video 5: Analisi scientifica dei dati per principianti
Uno dei segreti commerciali di analisi scientifica dei dati è quello di far svolgere il proprio lavoro ad altre persone. Trovare un esempio di algoritmo di clustering nella raccolta di Azure da utilizzare per la propria esperimento di machine learning.

> [!IMPORTANT]
> È stato rinominato Cortana Intelligence Gallery **raccolta di Azure AI**. Di conseguenza, testo e immagini in questa trascrizione variano leggermente dal video, che utilizza il nome precedente.
>

Per trarre il meglio dalla serie è consigliabile guardare tutti i video. [L'elenco dei video è disponibile qui](#other-videos-in-this-series)
<br>

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/data-science-for-beginners-series-copy-other-peoples-work-to-do-data-science/player]
>
>

## <a name="other-videos-in-this-series"></a>Altri video della serie
*Analisi scientifica dei dati per principianti* è una rapida introduzione all'analisi scientifica dei dati in cinque brevi video.

* Video 1: [5 domande a cui può rispondere l'analisi scientifica dei dati](data-science-for-beginners-the-5-questions-data-science-answers.md) *(5 min e 14 sec)*
* Video 2: [Verifica della preparazione dei dati per l'analisi scientifica dei dati](data-science-for-beginners-is-your-data-ready-for-data-science.md) *(4 min e 56 sec)*
* Video 3: [Porre una domanda a cui è possibile rispondere con i dati](data-science-for-beginners-ask-a-question-you-can-answer-with-data.md) *(4 min e 17 sec)*
* Video 4: [Prevedere una risposta con un modello semplice](data-science-for-beginners-predict-an-answer-with-a-simple-model.md) *(7 min e 42 sec)*
* Video 5: Copiare il lavoro di altre persone per l'analisi scientifica dei dati

## <a name="transcript-copy-other-peoples-work-to-do-data-science"></a>Trascrizione: Copiare il lavoro di altre persone per l'analisi scientifica dei dati
Benvenuti al quinto video della serie "Data Science for Beginners" (Data Science per principianti).

In questo video si scoprirà dove trovare esempi che è possibile usare come punto di partenza per la propria attività. Per ottenere il massimo da questo video, è consigliabile guardare prima i video precedenti di questa serie.

Uno dei segreti commerciali di analisi scientifica dei dati è quello di far svolgere il proprio lavoro ad altre persone.

## <a name="find-examples-in-the-azure-ai-intelligence-gallery"></a>Trovare gli esempi nella raccolta di Intelligence di Azure

Microsoft dispone di un servizio basato su cloud denominato [Azure Machine Learning Studio](https://azure.microsoft.com/services/machine-learning-studio/) che hai di prova gratuitamente. Offre un'area di lavoro in cui si possono provare i diversi algoritmi di Machine Learning e, una volta creata la soluzione personalizzata, si potrà avviarla come servizio Web.

Parte di questo servizio è un elemento denominato il  **[raccolta di Azure AI](https://gallery.cortanaintelligence.com/)**. Contiene risorse, come una raccolta di esperimenti di Azure Machine Learning, ovvero modelli, creati da persone che li hanno poi messi a disposizione di altri utenti. Questi esperimenti sono un ottimo modo per sfruttare le idee e il lavoro di altri utenti per iniziare a creare soluzioni personalizzate. La raccolta è disponibile per chiunque voglia prenderne visione.

![Raccolta di Azure AI](./media/data-science-for-beginners-copy-other-peoples-work-to-do-data-science/azure-ai-gallery.png)

Se si fa clic su **Experiments** (Esperimenti) nella parte superiore, verranno visualizzati numerosi esperimenti tra i più recenti e diffusi disponibili nella raccolta. È possibile cercare nel resto degli esperimenti facendo clic su **Browse All** (Esplora tutto) nella parte superiore dello schermo e quindi immettere i termini di ricerca e scegliere i filtri di ricerca.

## <a name="find-and-use-a-clustering-algorithm-example"></a>Trovare e usare un esempio di algoritmo di clustering
Si supponga, ad esempio, di voler vedere un esempio del funzionamento del clustering. Si cercheranno quindi gli esperimenti relativi a **"clustering sweep"**.

![Cercare esperimenti di clustering](./media/data-science-for-beginners-copy-other-peoples-work-to-do-data-science/search-for-clustering-experiments.png)

Eccone uno interessante che qualcuno ha messo a disposizione nella raccolta.

![Esperimento di clustering](./media/data-science-for-beginners-copy-other-peoples-work-to-do-data-science/clustering-experiment.png)

Fare clic su tale esperimento. Verrà visualizzata una pagina Web che descrive le operazioni eseguite da questo collaboratore, insieme ad alcuni dei relativi risultati.

![Pagina di descrizione dell'esperimento di clustering](./media/data-science-for-beginners-copy-other-peoples-work-to-do-data-science/clustering-experiment-description-page.png)

Si noti il collegamento **Open in Studio**(Apri in Studio).

![Pulsante Open in Studio](./media/data-science-for-beginners-copy-other-peoples-work-to-do-data-science/open-in-studio.png)

È possibile fare clic su questo collegamento per passare direttamente ad **Azure Machine Learning Studio**. Crea una copia dell'esperimento e la inserisce nella propria area di lavoro. Sono inclusi i set di dati del collaboratore, tutto il lavoro di elaborazione eseguito, tutti gli algoritmi usati e come sono stati salvati i risultati.

![Aprire un esperimento di raccolta in Machine Learning Studio - esempio di algoritmo di clustering](./media/data-science-for-beginners-copy-other-peoples-work-to-do-data-science/cluster-experiment-open-in-studio.png)

Ora è disponibile un punto di partenza. Si possono sostituire i dati esistenti con i propri e apportare le modifiche necessarie al modello. Questo approccio consente un avvio rapido e la possibilità di usare il lavoro di persone altamente competenti.

## <a name="find-experiments-that-demonstrate-machine-learning-techniques"></a>Trovare esperimenti che illustrano le tecniche di Machine Learning
Esistono altri esperimenti nel [raccolta di Azure AI](https://gallery.cortanaintelligence.com) che sono stati forniti in modo specifico per fornire esempi pratici per utenti nuovi per l'analisi scientifica dei dati. Ad esempio, nella raccolta è disponibile un esperimento che illustra come gestire i valori mancanti ([Methods for handling missing values](https://gallery.cortanaintelligence.com/Experiment/Methods-for-handling-missing-values-1)(Metodi per la gestione dei valori mancanti). Descrive 15 diversi modi per sostituire i valori vuoti e illustra i vantaggi di ogni metodo e quando usarlo.

![Esperimenti della raccolta aperti in Machine Learning Studio - Metodi per i valori mancanti](./media/data-science-for-beginners-copy-other-peoples-work-to-do-data-science/experiment-methods-for-handling-missing-values.png)

[Raccolta di Azure AI](https://gallery.cortanaintelligence.com) è possibile trovare gli esperimenti di lavoro che è possibile utilizzare come punto di partenza per soluzioni personalizzate.

Assicurarsi di guardare gli altri video della serie "Analisi scientifica dei dati per principianti" in Microsoft Azure Machine Learning.

## <a name="next-steps"></a>Passaggi successivi
* [È possibile effettuare il primo esperimento di analisi scientifica dei dati con Azure Machine Learning](create-experiment.md)
* [Leggere l'Introduzione all'apprendimento automatico in Microsoft Azure](what-is-machine-learning.md)
