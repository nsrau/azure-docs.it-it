---
title: Aggiornare e ridimensionare un'istanza di Gestione API di Azure | Microsoft Docs
description: Questo argomento descrive come aggiornare e ridimensionare un'istanza di Gestione API di Azure.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: anneta
editor: ''
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 04/20/2020
ms.author: apimpm
ms.openlocfilehash: 466af29a3f073f73d4a0fc62d74709cd59f82425
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/26/2020
ms.locfileid: "96183788"
---
# <a name="upgrade-and-scale-an-azure-api-management-instance"></a>Aggiornare e ridimensionare un'istanza di Gestione API di Azure  

I clienti possono ridimensionare un'istanza di gestione API di Azure aggiungendo e rimuovendo unità. Un'**unità**, costituita da risorse di Azure dedicate, ha una capacità di carico specifica espressa sotto forma di numero di chiamate API al mese. Questo numero non rappresenta un limite di chiamate, ma piuttosto un valore della velocità effettiva massima per consentire la pianificazione della capacità approssimativa. La velocità e la latenza effettive variano in modo significativo a seconda di fattori quali il numero e la percentuale di connessioni simultanee, la tipologia e il numero di criteri configurati, le dimensioni delle richieste e delle risposte e la latenza del back-end.

La capacità e il prezzo di ogni unità dipende dal **livello** a cui l'unità appartiene. È possibile scegliere tra quattro livelli: **Developer**, **Basic**, **Standard** e **Premium**. Se è necessario aumentare la capacità di un servizio all'interno di un livello, è consigliabile aggiungere un'unità. Se il livello attualmente selezionato nell'istanza di gestione API non consente l'aggiunta di altre unità, è necessario eseguire l'aggiornamento a un livello superiore.

Il prezzo di ogni unità e le funzionalità disponibili (ad esempio, la distribuzione in più aree) dipendono dal livello scelto per l'istanza di gestione API. L'articolo con i [dettagli sui prezzi](https://azure.microsoft.com/pricing/details/api-management/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) illustra il prezzo unitario per ogni livello e le funzionalità disponibili corrispondenti. 

>[!NOTE]
>I valori relativi alla capacità unitaria per ogni livello indicati nell'articolo con i [dettagli sui prezzi](https://azure.microsoft.com/pricing/details/api-management/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) sono approssimativi. Per ottenere valori più precisi, è necessario esaminare uno scenario realistico per le API in uso. Vedere l'articolo [Capacità di un'istanza di Gestione API di Azure](api-management-capacity.md).

## <a name="prerequisites"></a>Prerequisiti

Per eseguire i passaggi in questo articolo è necessario:

+ Avere una sottoscrizione di Azure attiva.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ Disporre di un'istanza di gestione API. Per altre informazioni, vedere [Create an Azure API Management instance](get-started-create-service-instance.md) (Creare un'istanza di Gestione API di Azure).

+ Comprendere il concetto di [Capacità di un'istanza di Gestione API di Azure](api-management-capacity.md).

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="upgrade-and-scale"></a>Aggiornare e ridimensionare  

È possibile scegliere tra quattro livelli: **Developer**, **Basic**,  **standard** e **Premium**. Il livello **Developer** deve essere usato per valutare il servizio. Non deve essere usato in produzione. Il livello **Developer** non è accompagnato da un contratto di servizio e non può essere ridimensionato tramite l'aggiunta o la rimozione di unità. 

**Basic**, **standard** e **Premium** sono livelli di produzione con contratto di contratto e possono essere ridimensionati. Il livello **Basic** è il livello più economico con un contratto di contratto e può essere scalato fino a due unità. il livello **standard** può essere ridotto a un massimo di quattro unità. Al livello **Premium** è possibile aggiungere un numero qualsiasi di unità.

Il livello **Premium** consente di distribuire un'unica istanza di Gestione API di Azure in un numero qualsiasi di aree di Azure. Nel momento in cui viene creato un servizio Gestione API di Azure, l'istanza contiene una sola unità e si trova in un'unica area di Azure. L'area iniziale viene designata come area **primaria**. È possibile aggiungere facilmente altre aree. Quando si aggiunge un'area, si specifica il numero di unità da allocare. È ad esempio possibile allocare una sola unità nell'area **primaria** e cinque unità in altre aree. È possibile personalizzare il numero di unità in base al traffico di ogni area. Per altre informazioni, vedere [Come distribuire un'istanza del servizio Gestione API di Azure in più aree di Azure](api-management-howto-deploy-multi-region.md).

È possibile effettuare aggiornamenti e downgrade da e verso qualsiasi livello. L'aggiornamento o il downgrade può rimuovere alcune funzionalità, ad esempio reti virtuali o la distribuzione in più aree, quando si effettua il downgrade a standard o Basic dal livello Premium.

> [!NOTE]
> Il processo di aggiornamento o ridimensionamento può richiedere da 15 a 45 minuti. Si riceverà una notifica al termine dell'operazione.

> [!NOTE]
> Il servizio gestione API nel livello **consumo** si ridimensiona automaticamente in base al traffico.

## <a name="scale-your-api-management-service"></a>Ridimensionare il servizio gestione API

![Ridimensionare il servizio gestione API in portale di Azure](./media/upgrade-and-scale/portal-scale.png)

1. Passare al servizio gestione API nella [portale di Azure](https://portal.azure.com/).
2. Scegliere **percorsi** dal menu.
3. Fare clic sulla riga con la posizione in cui si vuole ridimensionare.
4. Specificare il nuovo numero di **unità** : utilizzare il dispositivo di scorrimento o digitare il numero.
5. Fare clic su **Applica**.

## <a name="change-your-api-management-service-tier"></a>Modificare il livello di servizio gestione API

1. Passare al servizio gestione API nella [portale di Azure](https://portal.azure.com/).
2. Fare clic sul piano **tariffario** nel menu.
3. Selezionare il livello di servizio desiderato nell'elenco a discesa. Usare il dispositivo di scorrimento per specificare la scala del servizio gestione API dopo la modifica.
4. Fare clic su **Salva**.

## <a name="downtime-during-scaling-up-and-down"></a>Tempo di inattività durante il ridimensionamento
Se si esegue il ridimensionamento da o verso il livello Developer, si verifica un tempo di inattività. In caso contrario, non sono previsti tempi di inattività. 

## <a name="compute-isolation"></a>Isolamento del calcolo
Se i requisiti di sicurezza includono l' [isolamento di calcolo](../azure-government/azure-secure-isolation-guidance.md#compute-isolation), è possibile usare il piano tariffario **isolato** . Questo livello garantisce che le risorse di calcolo di un'istanza del servizio gestione API utilizzino l'intero host fisico e forniscano il livello di isolamento necessario per supportare, ad esempio, i carichi di lavoro IL5 (Department of Defense Impact Level 5) degli Stati Uniti. Per ottenere l'accesso al livello isolato, [creare un ticket di supporto](../azure-portal/supportability/how-to-create-azure-support-request.md). 



## <a name="next-steps"></a>Passaggi successivi

- [Come distribuire un'istanza del servizio Gestione API di Azure in più aree di Azure](api-management-howto-deploy-multi-region.md)
- [Come ridimensionare automaticamente un'istanza del servizio di Gestione API di Azure](api-management-howto-autoscale.md)
- [Ottimizza e Risparmia sulla spesa per il cloud](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)