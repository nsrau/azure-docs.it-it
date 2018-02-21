---
title: "Novità di Azure Machine Learning | Documentazione Microsoft"
description: "Nuove funzionalità disponibili in Azure Machine Learning."
services: machine-learning
documentationcenter: 
author: garyericson
manager: raymondl
editor: 
ms.assetid: ddc716ed-2615-4806-bf27-6c9a5662a7f2
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/28/2017
ms.author: raymondl
ms.openlocfilehash: 0e97a8906bf0e5ea790725efbef16b883138c87a
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/09/2018
---
# <a name="whats-new-in-azure-machine-learning"></a>Novità in Azure Machine Learning

### <a name="the-march-2017-release-of-microsoft-azure-machine-learning-updates-provides-the-following-feature"></a>La versione di marzo 2017 degli aggiornamenti di Microsoft Azure Machine Learning offre la funzionalità seguente:



* Capacità dedicata per i processi BES di Azure Machine Learning

    L'elaborazione di pool Batch in Machine Learning usa il [servizio Azure Batch](../../batch/batch-technical-overview.md) per fornire la scalabilità gestita dal cliente per il servizio di esecuzione Batch di Azure Machine Learning. L'elaborazione di pool Batch consente di creare pool Azure Batch a cui è possibile inviare i processi batch e eseguirli in modo prevedibile.

    Per altre informazioni, vedere [Servizio Azure Batch per i processi di Machine Learning](dedicated-capacity-for-bes-jobs.md).


### <a name="the-august-2016-release-of-microsoft-azure-machine-learning-updates-provide-the-following-features"></a>La versione di agosto 2016 degli aggiornamenti di Microsoft Azure Machine Learning offre le funzionalità seguenti:
* I servizi Web classici possono ora essere gestiti nel nuovo portale dei [servizi Web di Microsoft Azure Machine Learning](https://services.azureml.net/), che consente di gestire tutti gli aspetti del servizio Web da un'unica posizione e:    
  * Fornisce le [statistiche di uso](manage-new-webservice.md)del servizio Web.
  * Semplifica il test delle chiamate di richiesta remota di Azure Machine Learning tramite dati di esempio.
  * Offre una nuova pagina di test del servizio Esecuzione batch con la cronologia di invio di processi e dati di esempio.
  * Consente una gestione più semplice degli endpoint.

### <a name="the-july-2016-release-of-microsoft-azure-machine-learning-updates-provide-the-following-features"></a>La versione di luglio 2016 degli aggiornamenti di Microsoft Azure Machine Learning offre le funzionalità seguenti:
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

