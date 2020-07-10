---
title: Machine Learning responsabile (ML) Preview
titleSuffix: Azure Machine Learning
description: Informazioni su che cos'è Machine Learning responsabile e come usarlo in Azure Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: luquinta
author: luisquintanilla
ms.date: 07/09/2020
ms.openlocfilehash: 4f14d4a9207b3bd0ba242973443b8e756527fd70
ms.sourcegitcommit: 3541c9cae8a12bdf457f1383e3557eb85a9b3187
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/09/2020
ms.locfileid: "86201931"
---
# <a name="responsible-machine-learning-ml-preview"></a>Machine Learning responsabile (ML) Preview

In questo articolo viene spiegato che cos'è Machine Learning responsabile e come sfruttarlo a livello pratico con Azure Machine Learning.

Durante lo sviluppo e l'uso di sistemi di intelligenza artificiale, l'attendibilità deve essere un fattore essenziale. Attendibilità nella piattaforma, nel processo e nei modelli. Per Microsoft, Machine Learning responsabile comprende i valori e i principi seguenti:

- Comprensione dei modelli di Machine Learning
  - Interpretazione e spiegazione del comportamento dei modelli
  - Valutazione e mitigazione dell'iniquità dei modelli
- Protezione delle persone e dei loro dati
  - Prevenzione dell'esposizione dei dati con privacy differenziale  
- Controllo del processo di Machine Learning end-to-end
  - Documentazione del ciclo di vita di Machine Learning con fogli dati

:::image type="content" source="media/concept-responsible-ml/responsible-ml-pillars.png" alt-text="Concetti fondamentali di Machine Learning responsabile":::

Con la crescente integrazione dell'intelligenza artificiale e dei sistemi autonomi nel tessuto della società, è importante impegnarsi in modo proattivo per prevenire e mitigare le conseguenza impreviste di queste tecnologie.

## <a name="interpret-and-explain-model-behavior"></a>Interpretazione e spiegazione del comportamento dei modelli

I sistemi difficili da spiegare o opaco possono essere problematici perché rendono difficile per gli stakeholder come sviluppatori di sistemi, legislatori, utenti e decision maker aziendali comprendere il motivo per cui i sistemi prendono determinate decisioni. Alcuni sistemi di intelligenza artificiale sono più facilmente spiegabili di altri e talvolta è necessario accettare compromessi tra un sistema con accuratezza maggiore e uno più interpretabile.

Per creare sistemi di intelligenza artificiale interpretabili, è possibile usare [InterpretML](https://github.com/interpretml/interpret), un pacchetto open source realizzato da Microsoft. [InterpretML può essere usato all'interno di Azure Machine Learning](how-to-machine-learning-interpretability.md) per [interpretare e spiegare i modelli di Machine Learning](how-to-machine-learning-interpretability-aml.md), inclusi i [modelli di Machine Learning automatizzati](how-to-machine-learning-interpretability-automl.md).

## <a name="assess-and-mitigate-model-unfairness"></a>Valutazione e mitigazione dell'iniquità dei modelli

In un'era in cui i sistemi di intelligenza artificiale sono sempre più coinvolti nei processi decisionali quotidiani della società, è estremamente importante che questi sistemi siano ottimali nel fornire risultati equi per tutti.

L'iniquità dei sistemi di intelligenza artificiale può produrre le conseguenze impreviste seguenti:

- Negazione di opportunità, risorse o informazioni alle persone.
- Consolidamento di pregiudizi e stereotipi.

Molti aspetti di equità non possono essere acquisiti o rappresentati dalle metriche. Sono disponibili strumenti e procedure che consentono di migliorare l'equità nella progettazione e nello sviluppo di sistemi di intelligenza artificiale.

Due aspetti chiave nel ridurre l'iniquità dei sistemi di intelligenza artificiale sono la valutazione e la mitigazione. [FairLearn](https://github.com/fairlearn/fairlearn) è un pacchetto open source consigliato a questo scopo che può valutare e mitigare la potenziale iniquità dei sistemi di intelligenza artificiale. Per altre informazioni sull'equità e sul pacchetto FairLearn, vedere l'[articolo sull'equità in Machine Learning](./concept-fairness-ml.md).

## <a name="prevent-data-exposure-with-differential-privacy"></a>Prevenzione dell'esposizione dei dati con privacy differenziale

Quando i dati vengono usati per attività di analisi, è importante che restino privati e riservati per tutto il periodo di utilizzo. La privacy differenziale è un set di sistemi e procedure che consentono di preservare la sicurezza e la riservatezza dei dati personali.

Negli scenari tradizionali i dati non elaborati vengono archiviati in file e database. Quando gli utenti analizzano i dati, in genere usano dati non elaborati. Questo costituisce un problema, perché può comportare la violazione della privacy personale. La privacy differenziale tenta di risolvere questo problema aggiungendo "rumore" o casualità ai dati in modo che i singoli punti dati non risultino identificabili.

L'implementazione di sistemi di privacy differenziale presenta alcune difficoltà. [WhiteNoise](https://github.com/opendifferentialprivacy/whitenoise-core) è un progetto open source che contiene diversi componenti per la creazione di sistemi privati globali in modo differenziale. Per altre informazioni sulla privacy differenziale e sul progetto WhiteNoise, vedere l'articolo sul [mantenimento della privacy dei dati tramite privacy differenziale e WhiteNoise](./concept-differential-privacy.md).

> [!NOTE]
> Si noti che il Toolkit viene rinominato e verrà introdotto il nuovo nome nelle prossime settimane. 

## <a name="document-the-machine-learning-lifecycle-with-datasheets"></a>Documentazione del ciclo di vita di Machine Learning con fogli dati

La documentazione delle informazioni pertinenti nel processo di Machine Learning è un aspetto chiave per prendere decisioni responsabili in ogni fase. I fogli dati sono uno strumento per documentare gli asset di Machine Learning usati e creati come parte del ciclo di vita di Machine Learning.

I modelli tendono a essere considerati come "caselle opache" e spesso sono disponibili poche informazioni su di essi. Poiché i sistemi di Machine Learning stanno diventando più pervasivi e vengono usati per il processo decisionale, l'uso di fogli dati è un passo avanti verso lo sviluppo di sistemi di Machine Learning più responsabili.

Alcune informazioni sui modelli che può essere utile documentare come parte di un foglio dati:

- Uso previsto
- Architettura del modello
- Dati di training usati
- Dati di valutazione usati
- Metriche delle prestazioni del modello di training
- Informazioni sull'equità

Vedere l'esempio seguente per informazioni su come usare Azure Machine Learning SDK per implementare [fogli dati per i modelli](https://github.com/microsoft/MLOps/blob/master/pytorch_with_datasheet/model_with_datasheet.ipynb).

## <a name="additional-resources"></a>Risorse aggiuntive

- Usare la crittografia omomorfe per [distribuire un servizio Web di inferenza crittografato](how-to-homomorphic-encryption-seal.md).
- Altre informazioni sul set di linee guida [ABOUT ML](https://www.partnershiponai.org/about-ml/) per la documentazione dei sistemi di Machine Learning.
