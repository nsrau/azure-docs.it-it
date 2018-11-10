---
title: Ambienti di sviluppo per Azure Machine Learning | Microsoft Docs
description: Panoramica degli ambienti di sviluppo che è possibile usare con il servizio Azure Machine Learning. Python 3 è l'unico requisito per l'ambiente di sviluppo, ma è consigliabile usare anche ambienti Conda. Come strumenti di sviluppo si consigliano Jupyter Notebook, Azure Notebooks e IDE/editor di codice.
services: machine-learning
author: rastala
ms.author: roastala
manager: cgronlun
ms.service: machine-learning
ms.component: core
ms.reviewer: larryfr
ms.topic: conceptual
ms.date: 9/24/2018
ms.openlocfilehash: 4d25e147044053aa76afe2da482b71c24efc2325
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/07/2018
ms.locfileid: "51242875"
---
# <a name="development-environment-for-azure-machine-learning"></a>Ambiente di sviluppo per Azure Machine Learning 

Panoramica degli ambienti di sviluppo che è possibile usare con il servizio Azure Machine Learning. 

Il servizio Azure Machine Learning è indipendente dalla piattaforma e non richiede un ambiente di sviluppo specifico. Richiede __Python 3__ ed è consigliabile usare __Conda__ per creare ambienti isolati. __Se si dispone già di un ambiente di sviluppo che soddisfa tali requisiti__, è possibile ignorare questo documento e passare al documento sulla [Configurazione dell'ambiente di sviluppo](how-to-configure-environment.md).

Nella parte restante di questo documento sono descritti gli ambienti di sviluppo consigliati:

* __Notebook di Jupyter__
* __Azure Notebooks__
* __IDE (Integrated Development Environment) ed editor di codice__
* __Macchina virtuale di data science__

Un confronto tra questi ambienti è difficile, perché sia i notebook che gli IDE possono essere estesi. Ad esempio, alcuni IDE possono essere usati come client per Jupyter Notebook. In genere i __notebook__ sono progettati per la __sperimentazione interattiva__ e la __visualizzazione__. __Gli IDE e gli editor di codice__ forniscono strumenti per __migliorare la qualità del codice__ e per l'__integrazione con sistemi esterni__, ad esempio per il controllo della versione.

> [!TIP]
> L'uso di un ambiente non esclude l'uso dell'altro. I notebook e gli IDE non sono altro che strumenti e possono essere combinati in base alle esigenze. Ad esempio, è possibile iniziare a sperimentare in un notebook e quindi eseguire l'esportazione in uno script Python per la modifica e il debug in un ambiente IDE.
>
> Per questo Data Science Virtual Machine offre sia Jupyter Notebook che diversi IDE Python tra i più diffusi.

## <a name="jupyter-notebooks"></a>Notebook di Jupyter

Jupyter Notebook fa parte del [progetto Jupyter](https://jupyter.org/). Il loro scopo principale è quello di fornire un'esperienza di codifica interattiva in cui si creano documenti che combinano codice attivo con testi narrativi e grafica. È possibile installare Jupyter Notebook in un'ampia gamma di piattaforme.

L'installazione di Jupyter Notebook in locale consente di installare e configurare l'ambiente in base alle proprie esigenze. Si ha tuttavia la responsabilità della gestione del sistema.

## <a name="azure-notebooks"></a>Notebook di Azure

[Azure Notebooks](https://notebooks.azure.com) (anteprima) è un ambiente notebook nel cloud di Azure. Si basa su Jupyter e fornisce un ambiente in cui sono già caricate le librerie più diffuse. L'SDK di Azure Machine Learning è già installato in Azure Notebooks, consentendo in tal modo di iniziare a sperimentare senza necessità o quasi di operazioni di configurazione.

Azure Notebooks semplifica inoltre il processo di condivisione dei notebook. È possibile condividere un URL dei notebook, rendere pubblica la propria libreria o condividere contenuti sui social media dall'interfaccia di Azure Notebooks.

Lo svantaggio di Azure Notebooks è che non si ha il controllo completo dell'ambiente e si potrebbe non essere in grado di installare software personalizzato necessario. È anche un ambiente condiviso, pertanto i notebook possono essere eseguiti più lentamente rispetto a un'installazione di Jupyter Notebook dedicata.

## <a name="ides-and-code-editors"></a>IDE ed editor di codice

Vi sono molti IDE ed editor di codice che funzionano con Azure Machine Learning. L'unico requisito software è l'SDK di Azure Machine Learning, che può essere installato mediante l'utilità `pip`.

È consigliabile usare [Visual Studio Code](https://code.visualstudio.com/), che fornisce strumenti sia per il linguaggio Python che per Azure Machine Learning. È disponibile per le piattaforme Windows, macOS e Linux.

## <a name="data-science-virtual-machine"></a>Macchina virtuale di data science

Data Science Virtual Machine (DSVM) è una combinazione degli ambienti precedenti. È una macchina virtuale nella piattaforma Azure dove sono preinstallati Jupyter Notebook, Visual Studio Code e l'SDK di Azure Machine Learning. Poiché il software necessario è preinstallato nell'immagine della VM, è possibile iniziare a sperimentare con Azure Machine Learning subito dopo aver creato la macchina virtuale.

DSVM consente di selezionare le risorse di calcolo necessarie, come CPU, GPU e memoria. Vi sono anche preinstallati altri editor, come PyCharm, nonché software di apprendimento automatico di uso diffuso, come TensorFlow, Keras e PyTorch. Se il software di cui si ha necessità non è installato, è possibile installarlo autonomamente.

Per altre informazioni sul software preinstallato, vedere il [documento introduttivo a Data Science Virtual Machine](../data-science-virtual-machine/overview.md).

## <a name="next-steps"></a>Passaggi successivi

Dopo queste informazioni sugli ambienti di sviluppo, si passerà a vedere [come configurare un ambiente di sviluppo](how-to-configure-environment.md).

