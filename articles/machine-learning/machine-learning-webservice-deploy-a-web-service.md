---
title: Distribuire un nuovo servizio Web in Azure Machine Learning | Documentazione Microsoft
description: Il flusso di lavoro di implementazione di un servizio Web basato su ARM
services: machine-learning
documentationcenter: 
author: vDonGlover
manager: raymondl
editor: 
ms.assetid: a358b04f-0d08-4d50-820e-eeac971854cf
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/13/2017
ms.author: v-donglo
ROBOTS: NOINDEX, NOFOLLOW
redirect_url: machine-learning-publish-a-machine-learning-web-service
translationtype: Human Translation
ms.sourcegitcommit: 0813611f581a68efb8f09a1e041cfbe429bf0c5c
ms.openlocfilehash: 902be47eb59444a1214b096be10525743f406d1c


---
# <a name="deploy-a-new-web-service"></a>Distribuire un nuovo servizio Web
Microsoft Azure Machine Learning offre ora servizi Web basati su [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) consentendo nuove opzioni del piano di fatturazione e la distribuzione del servizio Web in più aree.

Il flusso di lavoro generale per distribuire un servizio Web usando i servizi Web di Microsoft Azure Machine Learning è:

* Creare un esperimento predittivo
* distribuirlo
* configurare il relativo nome
* piano di fatturazione
* eseguirne il test
* usarlo.

L'immagine seguente illustra il flusso di lavoro.

![Flusso di lavoro per la distribuzione di un servizio Web][1]

## <a name="deploy-web-service-from-studio"></a>Distribuire il servizio Web da Studio
Per distribuire un esperimento come nuovo servizio Web. Accedere a Machine Learning Studio e creare un nuovo servizio Web predittivo. 

**Nota**: se un esperimento è già stato distribuito come servizio Web classico non è possibile distribuirlo come un nuovo servizio Web.

Fare clic su **Run** (Esegui) nella parte inferiore dell'area di disegno dell'esperimento e quindi fare clic su **Deploy Web Service** (Distribuisci servizio Web) e su **Deploy Web Service [New]** (Distribuisci servizio Web [Nuovo]). Verrà aperta la pagina di distribuzione del portale di gestione dei servizi Web di Machine Learning.

## <a name="machine-learning-web-service-manager-deploy-experiment-page"></a>Pagina Deploy Experiment (Sperimentazione distribuzione) di Machine Learning Web Service Manager
Nella pagina Deploy Experiment (Sperimentazione distribuzione) immettere un nome per il servizio Web.
Selezionare un piano tariffario. Se è disponibile un piano tariffario, è possibile selezionarlo; in caso contrario è necessario creare un nuovo piano tariffario per il servizio. 

1. Nell'elenco a discesa **Price Plan** (Piano tariffario) selezionare un piano esistente o l'opzione **Select new plan** (Seleziona nuovo piano).
2. In **Plan Name**(Nome piano) digitare un nome che identifica il piano di fatturazione.
3. Selezionare uno dei **livelli del piano mensile**. Si noti che per impostazione predefinita i livelli di piano vengono impostati sui piani per l'area predefinita e il servizio Web viene distribuito in tale area.

Fare clic su **Distribuisci** e verrà visualizzata la pagina Avvio rapido per il servizio Web.

## <a name="quickstart-page"></a>Pagina Avvio rapido
La pagina Avvio rapido del servizio Web offre indicazioni e accesso alle attività più comuni eseguite dopo la creazione di un nuovo servizio Web. Da qui è possibile accedere facilmente alle pagine di **test** e di **consumo**.

## <a name="testing-your-web-service"></a>Test del servizio Web
Dalla pagina Avvio rapido fare clic su Test web service (Test servizio Web) nelle attività comuni.   

Per eseguire il test del servizio Web come servizio di richiesta-risposta (RRS):

* Fare clic su **Test** nella barra dei menu.
* Fare clic su **Request-Response**(Richiesta-risposta).
* Immettere i valori appropriati per le colonne di input dell'esperimento.
* Fare clic su Test **Request-Response**(Test Richiesta-risposta).

I risultati verranno visualizzati sul lato destro della pagina.

Per testare un servizio di esecuzione batch (BES), si userà un file CSV:

* Fare clic su **Test** nella barra dei menu.
* Fare clic su **Batch**.
* Sotto l'input fare clic su Sfoglia e passare al file di dati di esempio.
* Fare clic su **Test**.

Lo stato del test viene visualizzato in **Test Batch Jobs**(Test Processi batch).

## <a name="consuming-your-web-service"></a>Uso del servizio Web
Quando viene pubblicato come servizio Web, gli esperimenti Azure Machine Learning forniscono un'API REST che può essere utilizzata da un'ampia gamma di dispositivi e piattaforme. Infatti, la semplice API REST accetta e risponde con messaggi in formato JSON. Il portale di Azure Machine Learning fornisce il codice che può essere utilizzato per chiamare il servizio Web in R, c# e Python.

Nella pagina relativa all'uso è possibile trovare:

* La chiave API e l'URI per l'uso del servizio Web nelle app.
* I modelli di app Web e di Excel per avviare il processo di uso.
* Codice di esempio in C#, Python e R per iniziare.

Per altre informazioni sull'uso dei servizi Web, vedere [Come usare un servizio Web di Azure Machine Learning distribuito da un esperimento di Machine Learning](machine-learning-consume-web-services.md).

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sull'utilizzo dei servizi Web, vedere:

[Come utilizzare un servizio Web di Azure Machine Learning pubblicato da un esperimento di Machine Learning](machine-learning-consume-web-services.md)

[Servizi Web di Azure Machine Learning: distribuzione e uso](machine-learning-deploy-consume-web-service-guide.md)

<!--Image references-->
[1]: ./media/machine-learning-webservice-deploy-a-web-service/armdeploymentworkflow.png


<!--links-->



<!--HONumber=Jan17_HO4-->


