---
title: Creazione di endpoint del servizio Web in Machine Learning | Documentazione Microsoft
description: Creazione di endpoint del servizio Web in Azure Machine Learning
services: machine-learning
documentationcenter: ''
author: YasinMSFT
ms.author: yahajiza
manager: hjerez
editor: cgronlun
ms.assetid: 4657fc1b-5228-4950-a29e-bc709259f728
ms.service: machine-learning
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 10/04/2016
ms.openlocfilehash: fac284e9f0c852306d99733a879fc13c85f07768
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/23/2018
---
# <a name="creating-endpoints"></a>Creazione di endpoint
> [!NOTE]
>  Questo argomento descrive le tecniche applicabili a un servizio Web di Machine Learning **classico**.
> 
> 

Quando si creano servizi Web da vendere ai propri clienti, è necessario fornire dei modelli con training per ogni cliente che sono ancora collegati all'esperimento da cui è stato creato il servizio Web. Inoltre, qualsiasi aggiornamento all'esperimento deve essere applicato in maniera selettiva a un endpoint, senza sovrascrivere le personalizzazioni.

Per farlo, Azure Machine Learning consente di creare più endpoint per un servizio Web distribuito. Ciascun endpoint nel servizio Web viene indirizzato, limitato e gestito in maniera indipendente. Ciascun endpoint rappresnta un URL univoco e una chiave di autorizzazione che è possibile distribuire ai clienti.

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="adding-endpoints-to-a-web-service"></a>Aggiunta di endpoint a un servizio Web
Esistono due modi per aggiungere un endpoint a un servizio Web.

* A livello di codice
* Mediante il portale dei servizi Web di Azure Machine Learning

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
3. Fare clic su **Nuovo**.
4. Immettere un nome e una descrizione per il nuovo endpoint. I nomi degli endpoint devono contenere al massimo 24 caratteri alfanumerici (con lettere minuscole). Selezionare il livello di registrazione e indicare se i dati di esempio sono abilitati. Per altre informazioni sulla registrazione, vedere [Abilitare la registrazione per i servizi Web di Machine Learning](web-services-logging.md).

## <a name="next-steps"></a>Passaggi successivi
[Come usare un servizio Web di Azure Machine Learning](consume-web-services.md).

