---
title: Che cos'è l'apprendimento automatico responsabile (anteprima)
titleSuffix: Azure Machine Learning
description: Informazioni sull'apprendimento automatico responsabile e su come usarlo in Azure Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: luquinta
author: luisquintanilla
ms.date: 08/05/2020
ms.openlocfilehash: f2ece76ef3594cadfa1fcda89259ee18eb1f9084
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93324858"
---
# <a name="what-is-responsible-machine-learning-preview"></a>Che cos'è l'apprendimento automatico responsabile? (anteprima)

In questo articolo verranno illustrate le procedure di Machine Learning (ML) responsabili e i modi in cui è possibile metterle in pratica con Azure Machine Learning.

## <a name="responsible-machine-learning-principles"></a>Principi di apprendimento automatico responsabile

Durante lo sviluppo e l'uso di sistemi di intelligenza artificiale, l'attendibilità deve essere un fattore essenziale. Attendibilità nella piattaforma, nel processo e nei modelli. Microsoft, l'apprendimento automatico responsabile comprende i valori e i principi seguenti:

- Comprensione dei modelli di Machine Learning
  - Interpretazione e spiegazione del comportamento dei modelli
  - Valutazione e mitigazione dell'iniquità dei modelli
- Protezione delle persone e dei loro dati
  - Prevenzione dell'esposizione dei dati con privacy differenziale
  - Utilizzare i dati crittografati tramite la crittografia omomorfe
- Controllo del processo di Machine Learning end-to-end
  - Documentazione del ciclo di vita di Machine Learning con fogli dati

:::image type="content" source="media/concept-responsible-ml/responsible-ml-pillars.png" alt-text="Pilastri ML responsabili: interpretabilità, privacy differenziale, crittografia omomorfe, audit trail-Azure Machine Learning":::

Con la crescente integrazione dell'intelligenza artificiale e dei sistemi autonomi nel tessuto della società, è importante impegnarsi in modo proattivo per prevenire e mitigare le conseguenza impreviste di queste tecnologie.

## <a name="interpret-and-explain-model-behavior"></a>Interpretazione e spiegazione del comportamento dei modelli

I sistemi difficili da spiegare o opaco possono essere problematici perché rendono difficile per gli stakeholder come sviluppatori di sistemi, legislatori, utenti e decision maker aziendali comprendere il motivo per cui i sistemi prendono determinate decisioni. Alcuni sistemi di intelligenza artificiale sono più facilmente spiegabili di altri e talvolta è necessario accettare compromessi tra un sistema con accuratezza maggiore e uno più interpretabile.

Per creare sistemi di intelligenza artificiale interpretabili, è possibile usare [InterpretML](https://github.com/interpretml/interpret), un pacchetto open source realizzato da Microsoft. [InterpretML può essere usato all'interno di Azure Machine Learning](how-to-machine-learning-interpretability.md) per [interpretare e spiegare i modelli di Machine Learning](how-to-machine-learning-interpretability-aml.md), inclusi i [modelli di Machine Learning automatizzati](how-to-machine-learning-interpretability-automl.md).

## <a name="mitigate-fairness-in-machine-learning-models"></a>Attenuare l'equità nei modelli di Machine Learning

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

## <a name="work-on-encrypted-data-with-homomorphic-encryption"></a>Usare i dati crittografati con la crittografia omomorfe

Nelle soluzioni di calcolo e archiviazione cloud tradizionali, il cloud deve avere accesso non crittografato ai dati dei clienti per il calcolo. Questo accesso espone i dati agli operatori cloud. La privacy dei dati si basa sui criteri di controllo degli accessi implementati dal cloud e considerati attendibili dal cliente.

La crittografia omomorfica consente di eseguire calcoli su dati crittografati senza richiedere l'accesso a una chiave privata o di decrittografia. I risultati dei calcoli sono crittografati e possono essere resi noti solo dal proprietario della chiave privata. Con la crittografia omomorfe, gli operatori cloud non avranno mai accesso non crittografato ai dati in cui vengono archiviati e calcolati. I calcoli vengono eseguiti direttamente sui dati crittografati. La privacy dei dati si basa sulla crittografia all'avanguardia e il proprietario dei dati controlla tutte le versioni delle informazioni. Per ulteriori informazioni sulla crittografia omomorfe in Microsoft, vedere [Microsoft Research](https://www.microsoft.com/research/project/homomorphic-encryption/).

Per iniziare a usare la crittografia omomorfe in Azure Machine Learning, usare le associazioni Python per [inferenza crittografata](https://pypi.org/project/encrypted-inference/) per [Microsoft Seal](https://github.com/microsoft/SEAL). Microsoft SEAL è una libreria open source di crittografia omomorfe che consente di eseguire aggiunte e moltiplicazioni su numeri interi crittografati o numeri reali. Per ulteriori informazioni su Microsoft SEAL, vedere la pagina relativa al [centro architetture di Azure](/azure/architecture/solution-ideas/articles/homomorphic-encryption-seal) o al [progetto Microsoft Research](https://www.microsoft.com/research/project/microsoft-seal/).

Vedere l'esempio seguente per informazioni [su come distribuire un servizio Web di inferenza crittografato in Azure Machine Learning](how-to-homomorphic-encryption-seal.md).

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

- Per ulteriori informazioni sulle procedure consigliate, vedere il [Toolkit responsabile dell'innovazione](/azure/architecture/guide/responsible-innovation/) .
- Altre informazioni sul set di linee guida [ABOUT ML](https://www.partnershiponai.org/about-ml/) per la documentazione dei sistemi di Machine Learning.