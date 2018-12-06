---
title: Novità in Azure Machine Learning Studio | Microsoft Docs
description: Nuove funzionalità disponibili in Azure Machine Learning Studio.
services: machine-learning
documentationcenter: ''
author: ericlicoding
ms.custom: (previous ms.author=yahajiza, author=YasinMSFT)
ms.author: amlstudiodocs
manager: hjerez
editor: cgronlun
ms.assetid: ddc716ed-2615-4806-bf27-6c9a5662a7f2
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/28/2018
ms.openlocfilehash: c93bb9a2fa7991df265b4767585d920c137a436d
ms.sourcegitcommit: a08d1236f737915817815da299984461cc2ab07e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/26/2018
ms.locfileid: "52311144"
---
# <a name="whats-new-in-azure-machine-learning-studio"></a>Novità in Azure Machine Learning Studio

## <a name="october-2018"></a>Ottobre 2018

Il motore del linguaggio R nel modulo [Execute R Script](https://docs.microsoft.com/azure/machine-learning/studio-module-reference/execute-r-script) ha aggiunto una nuova versione di runtime di R, ovvero Microsoft R Open (MRO) 3.4.4. MRO 3.4.4 è basato su CRAN R 3.4.4 open source e pertanto è compatibile con i pacchetti che funzionano con tale versione di R. Per altre informazioni sui pacchetti R supportati, vedere l'articolo [R Packages supported by Azure Machine Learning Studio](https://docs.microsoft.com/azure/machine-learning/studio-module-reference/r-packages-supported-by-azure-machine-learning#bkmk_List) (Pacchetti R supportati da Azure Machine Learning Studio).

## <a name="march-2017"></a>Marzo 2017 
Questa versione degli aggiornamenti di Microsoft Azure Machine Learning offre la funzionalità seguente:

* Capacità dedicata per i processi BES di Azure Machine Learning

    L'elaborazione di pool Batch in Machine Learning usa il [servizio Azure Batch](../../batch/batch-technical-overview.md) per fornire la scalabilità gestita dal cliente per il servizio di esecuzione Batch di Azure Machine Learning. L'elaborazione di pool Batch consente di creare pool Azure Batch a cui è possibile inviare i processi batch e eseguirli in modo prevedibile.

    Per altre informazioni, vedere [Servizio Azure Batch per i processi di Machine Learning](dedicated-capacity-for-bes-jobs.md).


## <a name="august-2016"></a>Agosto 2016 
Questa versione degli aggiornamenti di Microsoft Azure Machine Learning offre le funzionalità seguenti:
* I servizi Web classici possono ora essere gestiti nel nuovo portale dei [servizi Web di Microsoft Azure Machine Learning](https://services.azureml.net/), che consente di gestire tutti gli aspetti del servizio Web da un'unica posizione e:    
  * Fornisce le [statistiche di uso](manage-new-webservice.md)del servizio Web.
  * Semplifica il test delle chiamate di richiesta remota di Azure Machine Learning tramite dati di esempio.
  * Offre una nuova pagina di test del servizio Esecuzione batch con la cronologia di invio di processi e dati di esempio.
  * Consente una gestione più semplice degli endpoint.

## <a name="july-2016"></a>Luglio 2016 
Questa versione degli aggiornamenti di Microsoft Azure Machine Learning offre le funzionalità seguenti:
* I servizi Web vengono ora gestiti come risorse di Azure gestite tramite le interfacce di [Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md) consentendo i miglioramenti seguenti:
  * Sono disponibili nuove [API REST](https://msdn.microsoft.com/library/azure/Dn950030.aspx) per la distribuzione e la gestione dei servizi Web basati su Resource Manager.
  * Il nuovo portale dei [servizi Web di Microsoft Azure Machine Learning](https://services.azureml.net/) consente di gestire tutti gli aspetti del servizio Web da un'unica posizione.
* Incorpora un nuovo modello di distribuzione dei servizi Web basato sulla sottoscrizione, con più aree usando API basate su Resource Manager che si avvalgono del provider di risorse di Resource Manager per i servizi Web.
* Introduce nuovi [piani tariffari](https://azure.microsoft.com/pricing/details/machine-learning/) e capacità di gestione dei piani avvalendosi del nuovo provider di risorse di Resource Manager per la fatturazione.
  * È ora possibile [distribuire il servizio Web in più aree](how-to-deploy-to-multiple-regions.md) senza dover creare una sottoscrizione in ogni area.
* Fornisce le [statistiche di uso](manage-new-webservice.md)del servizio Web.
* Semplifica il test delle chiamate di richiesta remota di Azure Machine Learning tramite dati di esempio.
* Offre una nuova pagina di test del servizio Esecuzione batch con la cronologia di invio di processi e dati di esempio.

Inoltre, Machine Learning Studio è stato aggiornato in modo da consentire ancora la distribuzione del modello di servizio Web classico, oltre alla distribuzione del nuovo modello di servizio Web. 

