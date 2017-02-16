---
title: "Guida per più aree geografiche | Documentazione Microsoft"
description: Informazioni su come creare un&quot;area di lavoro e pubblicare un servizio web in un&quot;area di Azure diversa da quella degli Stati Uniti centro meridionali (SCUS).
services: machine-learning
documentationcenter: 
author: tedway
manager: jhubbard
editor: rmca14
tags: 
ms.assetid: ed0ca8a8-fa53-4e56-b824-2d7e44641967
ms.service: machine-learning
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/17/2016
ms.author: tedway; neerajkh
translationtype: Human Translation
ms.sourcegitcommit: ec9d29701915d237686625fbc3abec9827fda467
ms.openlocfilehash: 22c30bfea89e3c650d81e1ebae20c9d151e6f36c


---
# <a name="multi-geo-help-documentation"></a>Guida per più aree geografiche
Questo articolo illustra come creare un'area di lavoro e pubblicare un servizio Web in altre aree geografiche di Azure.  La [pagina sui prodotti Azure per area geografica](https://azure.microsoft.com/en-us/regions/services/) fornisce un elenco delle aree in cui Azure Machine Learning è disponibile.

## <a name="create-a-workspace"></a>Creare un'area di lavoro
1. Accedere al portale di Microsoft Azure classico.
2. Fare clic su **+ NUOVO** > **SERVIZI DATI** > **MACHINE LEARNING** > **CREAZIONE RAPIDA**.  In **INDIRIZZO** selezionare un'altra area, ad esempio **Asia sud-orientale**.
   ![Guida per più aree geografiche immagine 1][1]
3. Selezionare l'area di lavoro, quindi fare clic su **Accedi a ML Studio**.
   ![Guida per più aree geografiche immagine 2][2]
4. Ora si dispone di un'area di lavoro in un'altra area geografica che può essere utilizzata come qualsiasi altra area di lavoro. Per passare da un’area di lavoro ad un’altra, cercare in alto a destra dello schermo. Fare clic sul menu a discesa, selezionare l'area geografica e quindi selezionare l'area di lavoro. Tutto è locale rispetto all'area geografica dell'area di lavoro.  Ad esempio, tutti i servizi Web creati da un'area di lavoro saranno nella stessa area geografica in cui si trova l'area di lavoro.
   ![Guida per più aree geografiche immagine 3][3]

## <a name="open-an-experiment-from-gallery"></a>Aprire un esperimento dalla raccolta
Se si apre un esperimento dalla raccolta, è inoltre possibile selezionare in quale area geografica si desidera copiare l’esperimento.

![Guida per più aree geografiche immagine 4][4a]

## <a name="web-service-management"></a>Gestione del servizio Web
Per gestire a livello di programmazione i servizi web, ad esempio per la ripetizione della formazione, usare l'indirizzo specifico dell'area geografica:

* https://asiasoutheast.management.azureml.net
* https://europewest.management.azureml.net

### <a name="things-to-note"></a>Punti da notare
1. In questo modo è possibile copiare solo esperimenti tra aree di lavoro che appartengono alla stessa area geografica. Se è necessario copiare un esperimento fra aree di lavoro che appartengono ad aree diverse, è possibile usare il commandlet di [PowerShell](http://aka.ms/amlps) [*Copy-AmlExperiment*](https://github.com/hning86/azuremlps/blob/master/README.md#copy-amlexperiment). Un'altra soluzione alternativa consiste nel pubblicare l'esperimento nella raccolta in modalità non in elenco, quindi aprirlo nell'area di lavoro dall'altra area.
2. Il selettore dell’area visualizzerà solo le aree di lavoro di una determinata area geografica alla volta.  
3. Un’area di lavoro con accesso libero o Guest (anonimo) verrà creata e ospitata in Stati Uniti centro-meridionali.  
4. I servizi Web distribuiti da un'area di lavoro nell’Asia sudorientale verranno anche ospitati in Asia sudorientale.  

## <a name="more-information"></a>Altre informazioni
Aggiungere una domanda sul [forum di Azure Machine Learning](https://social.msdn.microsoft.com/Forums/azure/home?forum=MachineLearning).

<!--Image references-->
[1]: ./media/machine-learning-multi-geo/multi-geo_1.png
[2]: ./media/machine-learning-multi-geo/multi-geo_2.png
[3]: ./media/machine-learning-multi-geo/multi-geo_3.png
[4a]: ./media/machine-learning-multi-geo/multi-geo_4a.png



<!--HONumber=Dec16_HO2-->


