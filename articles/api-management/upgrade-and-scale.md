---
title: Aggiornare e ridimensionare un'istanza di Gestione API di Azure | Microsoft Docs
description: Questo argomento descrive come aggiornare e ridimensionare un'istanza di Gestione API di Azure.
services: api-management
documentationcenter: 
author: vladvino
manager: anneta
editor: 
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 08/17/2017
ms.author: apimpm
ms.openlocfilehash: cf5cd03bd9147ac81b07dc453a919a3c74de44fe
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="upgrade-and-scale-an-api-management-instance"></a>Aggiornare e ridimensionare un'istanza di Gestione API 

I clienti possono ridimensionare un'istanza di Gestione API aggiungendo o rimuovendo unità. Un'**unità**, costituita da risorse di Azure dedicate, ha una capacità di carico specifica espressa sotto forma di chiamate API al mese. La velocità e la latenza effettive possono variare in modo significativo a seconda di fattori quali il numero e la percentuale di connessioni simultanee, la tipologia e il numero di criteri configurati, le dimensioni delle richieste e delle risposte e la latenza del back-end. 

La capacità e il prezzo di ogni unità dipende dal **livello** a cui l'unità appartiene. È possibile scegliere tra tre livelli: **Developer**, **Standard** e **Premium**. Se è necessario aumentare la capacità di un servizio all'interno di un livello, è consigliabile aggiungere un'unità. Se il livello attualmente selezionato nell'istanza di Gestione API non consente di aggiungere altre unità, è necessario eseguire l'aggiornamento a un livello superiore. 

Il prezzo di ogni unità, la possibilità di aggiungere o rimuovere unità, la disponibilità di alcune funzionalità (ad esempio, la distribuzione in più aree) dipendono dal livello scelto per l'istanza di Gestione API. L'articolo con i [dettagli sui prezzi](https://azure.microsoft.com/pricing/details/api-management/) illustra il prezzo unitario per ogni livello e le funzionalità disponibili corrispondenti. 

>[!NOTE]
>I valori relativi alla capacità unitaria per ogni livello indicati nell'articolo con i [dettagli sui prezzi](https://azure.microsoft.com/pricing/details/api-management/) sono approssimativi. Per ottenere valori più precisi, è necessario esaminare uno scenario realistico per le API in uso. Vedere la sezione "Come pianificare la capacità" più avanti.

## <a name="prerequisites"></a>Prerequisiti

Per eseguire i passaggi descritti in questo articolo, è necessario disporre di:

+ Una sottoscrizione di Azure attiva.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ Un'istanza di Gestione API. Per altre informazioni, vedere [Create an Azure API Management instance](get-started-create-service-instance.md) (Creare un'istanza di Gestione API di Azure).

## <a name="how-to-plan-for-capacity"></a>Come pianificare la capacità

Per capire se il numero di unità in uso è sufficiente per gestire il traffico, eseguire test basati sui carichi di lavoro previsti. 

Come affermato in precedenza, il numero di richieste al secondo che Gestione API è in grado di gestire dipende da un gran numero di variabili, ad esempio il criterio di connessione, le dimensioni della richiesta e della risposta, i criteri configurati per ogni API e il numero dei client che inviano richieste.

È possibile rilevare la capacita in uso in qualsiasi momento tramite **Metriche**, che usa le funzionalità di Monitoraggio di Azure.

### <a name="use-the-azure-portal-to-examine-metrics"></a>Esaminare le metriche tramite il portale di Azure 

1. Nel [portale di Azure](https://portal.azure.com/), passare all'istanza di Gestione API.
2. Selezionare **Metriche**.
3. Selezionare la metrica **Capacità** in **Metriche disponibili**. 

    La metrica Capacità offre un'indicazione di massima della capacità in uso nel tenant. Come test, è possibile aumentare più volte il carico, rilevando il carico di picco. È anche possibile impostare un avviso collegato alle metriche che si attivi se si verifica qualcosa di imprevisto, ad esempio, se l'istanza di Gestione API supera la capacità definita per più di 5 minuti. 

    >[!TIP]
    > È possibile configurare l'avviso in modo da ricevere una notifica quando il servizio sta esaurendo la capacità oppure in modo da attivare un'app per la logica che effettui automaticamente il ridimensionamento aggiungendo un'unità.

## <a name="upgrade-and-scale"></a>Aggiornare e ridimensionare 

Come affermato in precedenza, è possibile scegliere tra tre livelli: **Developer**, **Standard** e **Premium**. Il livello **Developer** deve essere usato per valutare il servizio. Non deve essere usato in produzione. Il livello **Developer** non è accompagnato da un contratto di servizio e non può essere ridimensionato tramite l'aggiunta o la rimozione di unità. 

I livelli **Standard** e **Premium** sono livelli di produzione. Hanno un contratto di servizio e possono essere ridimensionati. Il livello **Standard** può essere ridimensionato fino a un massimo di quattro unità. Al livello **Premium** è possibile aggiungere un numero qualsiasi di unità. 

Il livello **Premium** consente di distribuire un'unica istanza di Gestione API in un numero qualsiasi di aree di Azure. Nel momento in cui viene creato un servizio Gestione API, l'istanza contiene una sola unità e si trova in un'unica area di Azure. L'area iniziale viene designata come area **primaria**. È possibile aggiungere facilmente altre aree. Quando si aggiunge un'area, si specifica il numero di unità da allocare. È ad esempio possibile allocare una sola unità nell'area **primaria** e cinque unità in altre aree. Ciò consente un'allocazione su misura a seconda del traffico in ogni area. Per altre informazioni, vedere [Come distribuire un'istanza del servizio Gestione API di Azure in più aree di Azure](api-management-howto-deploy-multi-region.md).

È possibile effettuare aggiornamenti e downgrade da e verso qualsiasi livello. Si noti che il downgrade può rimuovere alcune funzionalità. Se ad esempio si effettua il downgrade dal livello Premium al livello Standard, possono essere rimosse le reti virtuali o la distribuzione in più aree.

>[!NOTE]
>Il processo di aggiornamento o ridimensionamento può richiedere da 15 a 30 minuti. Al termine viene inviata una notifica.

### <a name="use-the-azure-portal-to-upgrade-and-scale"></a>Eseguire l'aggiornamento e il ridimensionamento tramite il portale di Azure

1. Nel [portale di Azure](https://portal.azure.com/), passare all'istanza di Gestione API.
2. Selezionare **Piani e prezzi**.
3. Selezionare il livello desiderato.
4. Specificare il numero di **unità** da aggiungere. A tale scopo, è possibile usare il dispositivo di scorrimento o digitare il numero di unità.<br/>
    Se si sceglie il livello **Premium**, è prima necessario selezionare un'area.
5. Premere **Salva**.

## <a name="next-steps"></a>Passaggi successivi

[Come distribuire un'istanza del servizio Gestione API di Azure in più aree di Azure](api-management-howto-deploy-multi-region.md)

