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
ms.date: 08/18/2018
ms.author: apimpm
ms.openlocfilehash: 2aef9b6f32d562776be3e3da25b017d5ff88c4c4
ms.sourcegitcommit: f6050791e910c22bd3c749c6d0f09b1ba8fccf0c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50024330"
---
# <a name="upgrade-and-scale-an-azure-api-management-instance"></a>Aggiornare e ridimensionare un'istanza di Gestione API di Azure  

I clienti possono ridimensionare un'istanza di Gestione API di Azure aggiungendo o rimuovendo unità. Un'**unità**, costituita da risorse di Azure dedicate, ha una capacità di carico specifica espressa sotto forma di numero di chiamate API al mese. Questo numero non rappresenta un limite di chiamate, ma piuttosto un valore della velocità effettiva massima per consentire la pianificazione della capacità approssimativa. La velocità e la latenza effettive variano in modo significativo a seconda di fattori quali il numero e la percentuale di connessioni simultanee, la tipologia e il numero di criteri configurati, le dimensioni delle richieste e delle risposte e la latenza del back-end.

La capacità e il prezzo di ogni unità dipende dal **livello** a cui l'unità appartiene. È possibile scegliere tra quattro livelli: **Developer**, **Basic**, **Standard** e **Premium**. Se è necessario aumentare la capacità di un servizio all'interno di un livello, è consigliabile aggiungere un'unità. Se il livello attualmente selezionato nell'istanza di Gestione API non consente di aggiungere altre unità, è necessario eseguire l'aggiornamento a un livello superiore.

Il prezzo di ogni unità e le funzionalità disponibili (ad esempio, la distribuzione in più aree) dipendono dal livello scelto per l'istanza di Gestione API. L'articolo con i [dettagli sui prezzi](https://azure.microsoft.com/pricing/details/api-management/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) illustra il prezzo unitario per ogni livello e le funzionalità disponibili corrispondenti. 

>[!NOTE]
>I valori relativi alla capacità unitaria per ogni livello indicati nell'articolo con i [dettagli sui prezzi](https://azure.microsoft.com/pricing/details/api-management/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) sono approssimativi. Per ottenere valori più precisi, è necessario esaminare uno scenario realistico per le API in uso. Vedere l'articolo [Capacità di un'istanza di Gestione API di Azure](api-management-capacity.md).

## <a name="prerequisites"></a>Prerequisiti

Per eseguire i passaggi in questo articolo è necessario:

+ Avere una sottoscrizione di Azure attiva.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ Avere un'istanza di Gestione API. Per altre informazioni, vedere [Create an Azure API Management instance](get-started-create-service-instance.md) (Creare un'istanza di Gestione API di Azure).

+ Comprendere il concetto di [Capacità di un'istanza di Gestione API di Azure](api-management-capacity.md).

## <a name="upgrade-and-scale"></a>Aggiornare e ridimensionare  

È possibile scegliere tra quattro livelli: **Developer**, **Basic**, **Standard** e **Premium**. Il livello **Developer** deve essere usato per valutare il servizio. Non deve essere usato in produzione. Il livello **Developer** non è accompagnato da un contratto di servizio e non può essere ridimensionato tramite l'aggiunta o la rimozione di unità. 

I livelli **Basic**, **Standard** e **Premium** sono livelli di produzione. Hanno un contratto di servizio e possono essere ridimensionati. Il livello **Basic** è il più conveniente con un contratto di servizio e può essere ridimensionato fino a un massimo di due unità, mentre il livello **Standard** può essere ridimensionato fino a un massimo di quattro unità. Al livello **Premium** è possibile aggiungere un numero qualsiasi di unità.

Il livello **Premium** consente di distribuire un'unica istanza di Gestione API di Azure in un numero qualsiasi di aree di Azure. Nel momento in cui viene creato un servizio Gestione API di Azure, l'istanza contiene una sola unità e si trova in un'unica area di Azure. L'area iniziale viene designata come area **primaria**. È possibile aggiungere facilmente altre aree. Quando si aggiunge un'area, si specifica il numero di unità da allocare. È ad esempio possibile allocare una sola unità nell'area **primaria** e cinque unità in altre aree. È possibile personalizzare il numero di unità in base al traffico di ogni area. Per altre informazioni, vedere [Come distribuire un'istanza del servizio Gestione API di Azure in più aree di Azure](api-management-howto-deploy-multi-region.md).

È possibile effettuare aggiornamenti e downgrade da e verso qualsiasi livello. L'aggiornamento o il downgrade può rimuovere alcune funzionalità. Se ad esempio si effettua il downgrade dal livello Premium al livello Standard o Basic, possono essere rimosse le reti virtuali o la distribuzione in più aree.

>[!NOTE]
>Il processo di aggiornamento o ridimensionamento può richiedere da 15 a 45 minuti. Al termine viene inviata una notifica.

## <a name="use-the-azure-portal-to-upgrade-and-scale"></a>Eseguire l'aggiornamento e il ridimensionamento tramite il portale di Azure

![Ridimensionare Gestione API nel portale di Azure](./media/upgrade-and-scale/portal-scale.png)

1. Nel [portale di Azure](https://portal.azure.com/) passare all'istanza di Gestione API.
2. Selezionare **Piani e prezzi** dal menu.
3. Selezionare il livello desiderato.
4. Specificare il numero di **unità** da aggiungere. A tale scopo, è possibile usare il dispositivo di scorrimento o digitare il numero di unità.  
    Se si sceglie il livello **Premium**, è prima necessario selezionare un'area.
5. Premere **Salva**.

## <a name="next-steps"></a>Passaggi successivi

- [Come distribuire un'istanza del servizio Gestione API di Azure in più aree di Azure](api-management-howto-deploy-multi-region.md)
- [Come ridimensionare automaticamente un'istanza del servizio di Gestione API di Azure](api-management-howto-autoscale.md)
