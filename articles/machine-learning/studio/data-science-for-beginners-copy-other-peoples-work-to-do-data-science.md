---
title: Copiare gli esempi di data science altrui
titleSuffix: ML Studio (classic) Azure
description: "Il segreto commerciale dell'analisi scientifica dei dati: far svolgere il proprio lavoro ad altre persone. Esempi di Machine Learning dalla raccolta di intelligenza artificiale per Azure."
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: sdgilley
ms.author: sgilley
ms.custom: seodec18
ms.date: 03/22/2019
ms.openlocfilehash: 6004a3092856b0dbe55e044676b6a93e77debb1e
ms.sourcegitcommit: 6c2c97445f5d44c5b5974a5beb51a8733b0c2be7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/05/2019
ms.locfileid: "73619515"
---
# <a name="copy-other-peoples-work-to-do-data-science"></a>Copiare il lavoro di altre persone per l'analisi scientifica dei dati
## <a name="video-5-data-science-for-beginners-series"></a>Video 5: Analisi scientifica dei dati per principianti
Uno dei segreti commerciali di analisi scientifica dei dati è quello di far svolgere il proprio lavoro ad altre persone. È necessario individuare un esempio di algoritmo di clustering nella raccolta di intelligenza artificiale per Azure da usare per l'esperimento di Machine Learning.

> [!IMPORTANT]
> **Cortana Intelligence Gallery** è stato rinominato in **Raccolta di intelligenza artificiale per Azure**. Di conseguenza, il testo e le immagini di questa trascrizione possono variare leggermente rispetto al video, in cui è usato il nome precedente.
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

## <a name="find-examples-in-the-azure-ai-gallery"></a>Trovare gli esempi nella raccolta di intelligenza artificiale per Azure

Microsoft dispone di un servizio basato sul cloud denominato [Azure Machine Learning Studio (classico)](https://azure.microsoft.com/services/machine-learning-studio/). Offre un'area di lavoro in cui si possono provare i diversi algoritmi di Machine Learning e, una volta creata la soluzione personalizzata, si potrà avviarla come servizio Web.

Parte di questo servizio è un componente chiamato **[Raccolta di intelligenza artificiale per Azure](https://gallery.azure.ai/)** . Contiene risorse, tra cui una raccolta di esperimenti di Azure Machine Learning Studio (classico) o modelli, che gli utenti hanno creato e contribuito ad utilizzare. Questi esperimenti sono un ottimo modo per sfruttare le idee e il lavoro di altri utenti per iniziare a creare soluzioni personalizzate. La raccolta è disponibile per chiunque voglia prenderne visione.

![Raccolta di intelligenza artificiale per Azure](./media/data-science-for-beginners-copy-other-peoples-work-to-do-data-science/azure-ai-gallery.png)

Se si fa clic su **Experiments** (Esperimenti) nella parte superiore, verranno visualizzati numerosi esperimenti tra i più recenti e diffusi disponibili nella raccolta. È possibile cercare nel resto degli esperimenti facendo clic su **Browse All** (Esplora tutto) nella parte superiore dello schermo e quindi immettere i termini di ricerca e scegliere i filtri di ricerca.

## <a name="find-and-use-a-clustering-algorithm-example"></a>Trovare e usare un esempio di algoritmo di clustering
Si supponga, ad esempio, di voler vedere un esempio del funzionamento del clustering. Si cercheranno quindi gli esperimenti relativi a **"clustering sweep"** .

![Cercare esperimenti di clustering](./media/data-science-for-beginners-copy-other-peoples-work-to-do-data-science/search-for-clustering-experiments.png)

Eccone uno interessante che qualcuno ha messo a disposizione nella raccolta.

![Esperimento di clustering](./media/data-science-for-beginners-copy-other-peoples-work-to-do-data-science/clustering-experiment.png)

Fare clic su tale esperimento. Verrà visualizzata una pagina Web che descrive le operazioni eseguite da questo collaboratore, insieme ad alcuni dei relativi risultati.

![Pagina di descrizione dell'esperimento di clustering](./media/data-science-for-beginners-copy-other-peoples-work-to-do-data-science/clustering-experiment-description-page.png)

Si noti il collegamento **Open in studio (classico)** .

![Pulsante Apri in studio (classico)](./media/data-science-for-beginners-copy-other-peoples-work-to-do-data-science/open-in-studio.png)

Posso fare clic su di esso per fare clic su **Azure Machine Learning Studio (classico)** . Crea una copia dell'esperimento e la inserisce nella propria area di lavoro. Sono inclusi i set di dati del collaboratore, tutto il lavoro di elaborazione eseguito, tutti gli algoritmi usati e come sono stati salvati i risultati.

![Aprire un esperimento della raccolta nell'esempio di algoritmo di clustering Machine Learning Studio (classico)](./media/data-science-for-beginners-copy-other-peoples-work-to-do-data-science/cluster-experiment-open-in-studio.png)

Ora è disponibile un punto di partenza. Si possono sostituire i dati esistenti con i propri e apportare le modifiche necessarie al modello. Questo approccio consente un avvio rapido e la possibilità di usare il lavoro di persone altamente competenti.

## <a name="find-experiments-that-demonstrate-machine-learning-techniques"></a>Trovare esperimenti che illustrano le tecniche di Machine Learning
Esistono altri esperimenti in [Raccolta di intelligenza artificiale per Azure](https://gallery.azure.ai) messi a disposizione appositamente per fornire esempi di procedure per gli utenti che non hanno familiarità con l'analisi scientifica dei dati. Ad esempio, nella raccolta è disponibile un esperimento che illustra come gestire i valori mancanti ([Methods for handling missing values](https://gallery.azure.ai/Experiment/Methods-for-handling-missing-values-1)(Metodi per la gestione dei valori mancanti). Descrive 15 diversi modi per sostituire i valori vuoti e illustra i vantaggi di ogni metodo e quando usarlo.

![Esperimenti della raccolta aperti in Machine Learning Studio (classico)-metodi per i valori mancanti](./media/data-science-for-beginners-copy-other-peoples-work-to-do-data-science/experiment-methods-for-handling-missing-values.png)

In [Raccolta di intelligenza artificiale per Azure](https://gallery.azure.ai) è possibile trovare esperimenti funzionanti che si possono usare come punto di partenza per le proprie soluzioni.

Assicurarsi di consultare gli altri video in "analisi scientifica dei dati per principianti" da Microsoft Azure Machine Learning Studio (classico).

## <a name="next-steps"></a>Passaggi successivi
* [Provare il primo esperimento di data science con Azure Machine Learning Studio (classico)](create-experiment.md)
* [Leggere l'Introduzione all'apprendimento automatico in Microsoft Azure](/azure/machine-learning/preview/overview-what-is-azure-ml)
