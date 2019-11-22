---
title: "Esercitazione: Ridimensionare un'applicazione in Azure Spring Cloud | Microsoft Docs"
description: In questa esercitazione si apprenderà come ridimensionare un'applicazione in Azure Spring Cloud nel portale di Azure
ms.service: spring-cloud
ms.topic: tutorial
ms.author: jeconnoc
author: jpconnock
ms.date: 10/06/2019
ms.openlocfilehash: cce7562c74577f6fd545bcaed3ee3e0968fd40b4
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/16/2019
ms.locfileid: "74132900"
---
# <a name="tutorial-scale-an-application-in-azure-spring-cloud"></a>Esercitazione: Ridimensionare un'applicazione in Azure Spring Cloud

Questa esercitazione illustra come ridimensionare qualsiasi applicazione di microservizi usando il dashboard di Azure Spring cloud nel portale di Azure. Aumentare e ridurre le prestazioni dell'applicazione modificando il numero di CPU virtuali (vCPU) e la quantità di memoria. Aumentare e ridurre le prestazioni dell'applicazione modificando il numero di istanze dell'applicazione. Al termine, si saprà come apportare rapide modifiche manuali a ogni applicazione nel servizio. La scalabilità diventa effettiva entro pochi secondi e non richiede alcuna modifica del codice o ridistribuzione.

## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione, sono necessari:
* Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare. 
* Istanza del servizio Azure Spring Cloud distribuita.  Per iniziare, seguire la [guida dell'avvio rapido](spring-cloud-quickstart-launch-app-cli.md).
* Almeno un'applicazione già creata nell'istanza del servizio.


## <a name="navigate-to-the-scale-page-in-the-azure-portal"></a>Passare alla pagina Piano nel portale di Azure

1. Accedere al [Portale di Azure](https://portal.azure.com).

1. Passare alla pagina **Panoramica** di Azure Spring Cloud.

1. Selezionare il gruppo di risorse contenente il servizio.

1. Passare alla scheda **App** sotto l'intestazione **Impostazioni** nel menu a sinistra.

1. Selezionare l'applicazione da ridimensionare. In questo esempio verrà ridimensionata l'applicazione denominata "account-service". Verrà visualizzata la pagina **Panoramica** dell'applicazione.

1. Passare alla scheda **Piano** sotto l'intestazione **Impostazioni** nel menu a sinistra. Verranno visualizzate le opzioni per gli attributi di ridimensionamento illustrati nella sezione seguente.

## <a name="scale-your-application"></a>Ridimensionare l'applicazione

È possibile modificare gli attributi di ridimensionamento. Tenere presente le note seguenti.

* **CPU**: Il numero massimo consentito è 4 CPU per ogni istanza dell'applicazione. Il numero totale di CPU per un'applicazione corrisponderà al valore impostato qui moltiplicato per il numero di istanze dell'applicazione.

* **Memoria/GB**: La quantità massima di memoria consentita è di 8 GB per ogni istanza dell'applicazione.  La quantità totale di memoria per un'applicazione corrisponderà al valore impostato qui moltiplicato per il numero di istanze dell'applicazione.

* **Numero di istanze**: È possibile aggiungere un massimo di 20 istanze nel livello Standard. Questo valore modifica il numero di istanze in esecuzione separate dell'applicazione di microservizi.

Assicurarsi di fare clic sul pulsante **Salva** per applicare le impostazioni di ridimensionamento.

![Ridimensionare il servizio nel portale di Azure](media/spring-cloud-tutorial-scale-manual/scale-up-out.png)

Dopo alcuni secondi, le modifiche apportate verranno visualizzate nella pagina **Panoramica**, con ulteriori dettagli disponibili nella scheda **Istanze dell'applicazione**. Il ridimensionamento non richiede alcuna modifica del codice o ridistribuzione.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato illustrato come ridimensionare manualmente le applicazioni Azure Spring Cloud.  Per informazioni su come monitorare l'applicazione, proseguire con l'esercitazione successiva.

> [!div class="nextstepaction"]
> [Come monitorare l'applicazione](spring-cloud-tutorial-distributed-tracing.md)
