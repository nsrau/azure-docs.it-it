---
title: Creazione di endpoint del servizio Web in Machine Learning | Documentazione Microsoft
description: Creazione di endpoint del servizio Web in Azure Machine Learning
services: machine-learning
documentationcenter: 
author: hiteshmadan
manager: padou
editor: cgronlun
ms.assetid: 4657fc1b-5228-4950-a29e-bc709259f728
ms.service: machine-learning
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 10/04/2016
ms.author: himad
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 143a12ef975aa915ab28a66e20df1b586925daab


---
# <a name="creating-endpoints"></a>Creazione di endpoint
> [!NOTE]
> Questo argomento descrive le tecniche applicabili a un servizio Web classico.
> 
> 

Quando si creano servizi Web da vendere ai propri clienti, è necessario fornire dei modelli con training per ogni cliente che sono ancora collegati all'esperimento da cui è stato creato il servizio Web. Inoltre, qualsiasi aggiornamento all'esperimento deve essere applicato in maniera selettiva a un endpoint, senza sovrascrivere le personalizzazioni.

Per farlo, Azure Machine Learning consente di creare più endpoint per un servizio Web distribuito. Ciascun endpoint nel servizio Web viene indirizzato, limitato e gestito in maniera indipendente. Ciascun endpoint rappresnta un URL univoco e una chiave di autorizzazione che è possibile distribuire ai clienti.

[!INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## <a name="adding-endpoints-to-a-web-service"></a>Aggiunta di endpoint a un servizio Web
Esistono tre modi per aggiungere un endpoint a un servizio Web.

* A livello di codice
* Mediante il portale dei servizi Web di Azure Machine Learning
* Mediante il portale di Azure classico

Dopo aver creato l'endpoint, è possibile utilizzarlo tramite le API sincrone, le API batch e i fogli di lavoro di Excel. Oltre ad aggiungere gli endpoint tramite questa interfaccia, è possibile utilizzare anche le API di gestione Endpoint a livello di codice aggiungere gli endpoint.

> [!NOTE]
> Se sono stati aggiunti altri endpoint al servizio Web, non è possibile eliminare l'endpoint predefinito.
> 
> 

## <a name="adding-an-endpoint-programmatically"></a>Aggiunta di un endpoint a livello di codice
È possibile aggiungere un endpoint al servizio Web a livello di codice mediante il codice di esempio [AddEndpoint](https://github.com/raymondlaghaeian/AML_EndpointMgmt/blob/master/Program.cs).

## <a name="adding-an-endpoint-using-the-azure-machine-learning-web-services-portal"></a>Aggiunta di un endpoint mediante il portale dei servizi Web di Azure Machine Learning
1. In Machine Learning Studio fare clic su Web Services (Servizi Web) nella colonna di spostamento a sinistra.
2. Nella parte inferiore del dashboard dei servizi Web, fare clic su **Gestisci endpoint**. Nel portale dei servizi Web di Azure Machine Learning viene visualizzata la pagina dedicata agli endpoint del servizio Web.
3. Fare clic su **New**.
4. Immettere un nome e una descrizione per il nuovo endpoint. I nomi degli endpoint devono contenere al massimo 24 caratteri alfanumerici (con lettere minuscole). Selezionare il livello di registrazione e indicare se i dati di esempio sono abilitati. Per altre informazioni sulla registrazione, vedere [Abilitare la registrazione per i servizi Web di Machine Learning](machine-learning-web-services-logging.md).

## <a name="adding-an-endpoint-using-the-azure-classic-portal"></a>Aggiunta di un endpoint usando il portale di Azure classico
1. Accedere al [portale di Azure classico](http://manage.windowsazure.com), fare clic su **Machine Learning** nella colonna sinistra. Fare clic sull'area di lavoro che contiene il servizio Web a cui si è interessati.
   
    ![Passare all'area di lavoro](./media/machine-learning-create-endpoint/figure-1.png)
2. Fare clic su **Servizi Web**.
   
    ![Passare ai servizi Web](./media/machine-learning-create-endpoint/figure-2.png)
3. Fare clic sul servizio Web a cui si è interessati per visualizzare l'elenco degli endpoint disponibili.
   
    ![Passare all'endpoint](./media/machine-learning-create-endpoint/figure-3.png)
4. Nella parte inferiore della pagina fare clic su **Aggiungi endpoint**. Inserire un nome e una descrizione, assicurarsi che non siano presenti altri endpoint con lo stesso nome nel servizio Web. Lasciare il livello di limitazione con il valore predefinito, a meno di avere esigenze particolari. Per altre informazioni sulle limitazioni, vedere [Ridimensionamento degli endpoint dell'API](machine-learning-scaling-webservice.md).
   
    ![Creare un endpoint](./media/machine-learning-create-endpoint/figure-4.png)

## <a name="next-steps"></a>Passaggi successivi
[Come usare un servizio Web di Azure Machine Learning pubblicato](machine-learning-consume-web-services.md).




<!--HONumber=Nov16_HO3-->


