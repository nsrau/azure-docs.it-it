---
title: Problemi noti e risoluzione per il servizio di Azure Machine Learning
description: Ottenere un elenco dei problemi, delle soluzioni alternative e delle risoluzioni
services: machine-learning
author: j-martens
ms.author: jmartens
ms.reviewer: mldocs
ms.service: machine-learning
ms.component: core
ms.topic: article
ms.date: 09/24/2018
ms.openlocfilehash: d84040dc440c373ae9bae6dbac7a95109a387ba7
ms.sourcegitcommit: 51a1476c85ca518a6d8b4cc35aed7a76b33e130f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2018
ms.locfileid: "47162747"
---
# <a name="known-issues-and-troubleshooting-azure-machine-learning-service"></a>Problemi noti e risoluzione per il servizio di Azure Machine Learning
 
Questo articolo consente di trovare e correggere errori o guasti riscontrati durante l'uso del servizio di Azure Machine Learning. 


## <a name="databricks"></a>Databricks

Problemi di Databricks e Azure Machine Learning.

1. Raccomandazione cluster Databricks:
   
   Creare cluster Azure Databricks v.4.x con Python 3. Si consiglia un cluster di concorrenza elevata.
 
1. Errore di installazione di AML SDK in Databricks quando sono installati altri pacchetti.

   Alcuni pacchetti, come `psutil upgrade libs`, possono causare conflitti. Per evitare errori di installazione, installare i pacchetti con versione lib di blocco. Questo problema è correlato a Databricks e non ad AML SDK. Esempio:
   ```python
   pstuil cryptography==1.5 pyopenssl==16.0.0 ipython=2.2.0
   ```

## <a name="gather-diagnostics-information"></a>Raccogliere informazioni di diagnostica
In alcuni casi può essere utile fornire le informazioni di diagnostica quando si richiede supporto. Qui si trovano i file di log:

## <a name="resource-quotas"></a>Quote di risorse

Informazioni sulle [quote di risorse](how-to-manage-quotas.md) che si potrebbero incontrare quando si lavora con Azure Machine Learning.

## <a name="get-more-support"></a>Richiedere supporto tecnico

È possibile inviare le richieste di supporto e ottenere assistenza dal supporto tecnico, i forum e altro ancora. [Altre informazioni](support-for-aml-services.md)
