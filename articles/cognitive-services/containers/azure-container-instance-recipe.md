---
title: Ricetta dell'istanza del contenitore di AzureAzure Container Instance recipe
titleSuffix: Azure Cognitive Services
description: Informazioni su come distribuire contenitori di Servizi cognitivi nell'istanza del contenitore di AzureLearn how to deploy Cognitive Services Containers on Azure Container Instance
services: cognitive-services
author: aahill
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: f247465c7e2c0a212df2821ebc7165d3ee5b15f3
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2020
ms.locfileid: "80876658"
---
# <a name="deploy-and-run-container-on-azure-container-instance"></a>Distribuire ed eseguire un contenitore in Istanze di Azure Container

Con i passaggi seguenti, ridimensionare facilmente le applicazioni di Servizi cognitivi di Azure nel cloud con [istanze del contenitore](https://docs.microsoft.com/azure/container-instances/)di Azure.With the following steps, scale Azure Cognitive Services applications in the cloud easily with Azure Container Instances . La containerizzazione consente di concentrarsi sulla creazione di applicazioni anziché sulla gestione dell'infrastruttura. Per ulteriori informazioni sull'utilizzo dei contenitori, vedere [funzionalità e vantaggi](../cognitive-services-container-support.md#features-and-benefits).

## <a name="prerequisites"></a>Prerequisiti

La ricetta funziona con qualsiasi contenitore di servizi cognitivi. La risorsa Servizio cognitivo deve essere creata nel portale di Azure prima di usare la ricetta. Ogni servizio cognitivo che supporta i contenitori dispone di un documento "Come installare" specifico per l'installazione e la configurazione del servizio per un contenitore. Alcuni servizi richiedono un file o un set di file come input per il contenitore, è importante comprendere e aver utilizzato correttamente il contenitore prima di utilizzare questa soluzione.

* Una risorsa del servizio cognitivo, creata nel portale di Azure.A Cognitive Service resource, created in Azure portal.
* URL **dell'endpoint** del servizio cognitivo: esaminare "Come installare" del servizio specifico per il contenitore, per individuare la posizione dell'URL dell'endpoint all'interno del portale di Azure e l'aspetto di un esempio corretto dell'URL. Il formato esatto può variare da servizio a servizio.
* Chiave servizio cognitivo: le chiavi si trovano nella pagina Chiavi per la risorsa di Azure.Cognitive Service **key** - the **keys are** on the Keys page for the Azure resource. È necessaria solo una delle due chiavi. La chiave è una stringa di 32 caratteri alfanumerici.
* Un singolo contenitore di servizi cognitivi nell'host locale (il computer). Assicurarsi di poter:
  * Tirare giù l'immagine con un `docker pull` comando.
  * Eseguire correttamente il contenitore locale `docker run` con tutte le impostazioni di configurazione necessarie con un comando.
  * Chiamare l'endpoint del contenitore, ottenendo una risposta di HTTP 2xx e una risposta JSON.

Tutte le variabili tra `<>`parentesi angolari, , devono essere sostituite con valori propri. Questa sostituzione include le parentesi angolari.

[!INCLUDE [Create a Text Analytics Containers on Azure Container Instances](includes/create-container-instances-resource.md)]

## <a name="use-the-container-instance"></a>Usare l'istanza del contenitoreUse the Container Instance

1. Selezionare **Panoramica** e copiare l'indirizzo IP. Sarà un indirizzo IP numerico come `55.55.55.55`.
1. Aprire una nuova scheda del browser e `http://<IP-address>:5000 (http://55.55.55.55:5000`utilizzare l'indirizzo IP, ad esempio ). Verrà visualizzata la home page del contenitore, che indica che il contenitore è in esecuzione.

1. Selezionare **Descrizione API servizio** per visualizzare la pagina swagger per il contenitore.

1. Selezionare una delle API **POST** e selezionare **Prova.**  Vengono visualizzati i parametri, incluso l'input. Compilare i parametri.

1. Selezionare **Esegui** per inviare la richiesta all'istanza del contenitore.

    I contenitori di Servizi cognitivi sono stati creati e usati correttamente nell'istanza del contenitore di Azure.You have successfully created and used Cognitive Services containers in Azure Container Instance.
