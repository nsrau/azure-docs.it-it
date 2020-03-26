---
title: "Esercitazione: Ridimensionare un'applicazione in Azure Spring Cloud | Microsoft Docs"
description: In questa esercitazione si apprenderà come ridimensionare un'applicazione con Azure Spring Cloud nel portale di Azure
ms.service: spring-cloud
ms.topic: tutorial
ms.author: brendm
author: bmitchell287
ms.date: 10/06/2019
ms.openlocfilehash: f08a3b5d0a03b0e898457bbb783dd5031c4b0f27
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/24/2020
ms.locfileid: "76277484"
---
# <a name="scale-an-application-in-azure-spring-cloud"></a>Ridimensionare un'applicazione in Azure Spring Cloud

Questa esercitazione illustra come ridimensionare qualsiasi applicazione di microservizi usando il dashboard di Azure Spring cloud nel portale di Azure.

Aumentare e ridurre le prestazioni dell'applicazione modificando il numero di CPU virtuali (vCPU) e la quantità di memoria. Aumentare e ridurre le prestazioni dell'applicazione modificando il numero di istanze dell'applicazione.

Al termine, si saprà come apportare rapide modifiche manuali a ogni applicazione nel servizio. Il ridimensionamento diventa effettivo entro pochi secondi e non richiede alcuna modifica del codice o ridistribuzione.

## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione, sono necessari:

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare. 
* Istanza del servizio Azure Spring Cloud distribuita.  Per iniziare, seguire la guida di [avvio rapido sulla distribuzione di un'app tramite l'interfaccia della riga di comando di Azure](spring-cloud-quickstart-launch-app-cli.md).
* Almeno un'applicazione già creata nell'istanza del servizio.

## <a name="navigate-to-the-scale-page-in-the-azure-portal"></a>Passare alla pagina Piano nel portale di Azure

1. Accedere al [portale di Azure](https://portal.azure.com).

1. Passare alla pagina **Panoramica** di Azure Spring Cloud.

1. Selezionare il gruppo di risorse contenente il servizio.

1. Selezionare la scheda **App** in **Impostazioni** nel menu a sinistra della pagina.

1. Selezionare l'applicazione da ridimensionare. In questo esempio selezionare l'applicazione denominata **account-service**. Verrà visualizzata la pagina **Panoramica** dell'applicazione.

1. Passare alla scheda **Piano** in **Impostazioni** nel menu a sinistra della pagina. Verranno visualizzate le opzioni per gli attributi di ridimensionamento illustrati nella sezione seguente.

## <a name="scale-your-application"></a>Ridimensionare l'applicazione

Se si modificano gli attributi di ridimensionamento, tenere presente quanto segue:

* **CPU**: il numero massimo consentito è 4 CPU per ogni istanza dell'applicazione. Il numero totale di CPU per un'applicazione corrisponde al valore impostato qui moltiplicato per il numero di istanze dell'applicazione.

* **Memoria/GB**: la quantità massima di memoria consentita è di 8 GB per ogni istanza dell'applicazione. La quantità totale di memoria per un'applicazione corrisponde al valore impostato qui moltiplicato per il numero di istanze dell'applicazione.

* **Numero di istanze**: nel livello Standard è possibile aumentare il numero di istanze fino a un massimo di 20. Questo valore modifica il numero di istanze in esecuzione separate dell'applicazione di microservizi.

Assicurarsi selezionare **Salva** per applicare le impostazioni di ridimensionamento.

![Scheda Piano nel portale di Azure](media/spring-cloud-tutorial-scale-manual/scale-up-out.png)

Dopo alcuni secondi, le modifiche apportate verranno visualizzate nella pagina **Panoramica**, con ulteriori dettagli disponibili nella scheda **Istanze dell'applicazione**. Il ridimensionamento non richiede alcuna modifica del codice o ridistribuzione.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato illustrato come ridimensionare manualmente le applicazioni Azure Spring Cloud. Per informazioni su come monitorare l'applicazione, proseguire con l'esercitazione successiva.

> [!div class="nextstepaction"]
> [Come monitorare un'applicazione](spring-cloud-tutorial-distributed-tracing.md)
