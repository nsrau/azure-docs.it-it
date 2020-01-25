---
title: Ricetta dell'istanza di contenitore di Azure
titleSuffix: Azure Cognitive Services
description: Informazioni su come distribuire i contenitori di servizi cognitivi nell'istanza di contenitore di Azure
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: dapine
ms.openlocfilehash: 78f35042678aa7c30cebf73796df3e5d564b4502
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/24/2020
ms.locfileid: "76717006"
---
# <a name="deploy-and-run-container-on-azure-container-instance"></a>Distribuire ed eseguire un contenitore in Istanze di Azure Container

Con i passaggi seguenti è possibile ridimensionare facilmente le applicazioni di servizi cognitivi di Azure nel cloud con [istanze di contenitore](https://docs.microsoft.com/azure/container-instances/)di Azure. Il contenitore consente di concentrarsi sulla creazione di applicazioni anziché sulla gestione dell'infrastruttura. Per altre informazioni sull'uso dei contenitori, vedere [funzionalità e vantaggi](../cognitive-services-container-support.md#features-and-benefits).

## <a name="prerequisites"></a>Prerequisiti

La ricetta funziona con qualsiasi contenitore di servizi cognitivi. Prima di usare la ricetta, è necessario creare la risorsa del servizio cognitivo nella portale di Azure. Ogni servizio cognitivo che supporta i contenitori ha un documento "How to install" specifico per l'installazione e la configurazione del servizio per un contenitore. Alcuni servizi richiedono un file o un set di file come input per il contenitore, è importante comprendere che il contenitore è stato usato correttamente prima di usare questa soluzione.

* Risorsa di servizio cognitiva, creata in portale di Azure.
* URL dell' **endpoint** di servizio cognitivo: esaminare il "come installare" del servizio specifico per il contenitore, trovare la posizione in cui si trova l'URL dell'endpoint all'interno del portale di Azure e l'aspetto di un esempio corretto dell'URL. Il formato esatto può variare da servizio a servizio.
* **Chiave** del servizio cognitivo: le chiavi si trovano nella pagina **chiavi** della risorsa di Azure. È necessaria solo una delle due chiavi. La chiave è una stringa di 32 caratteri alfanumerici.
* Un singolo contenitore di servizi cognitivi nell'host locale (computer). Verificare che sia possibile:
  * Estrarre l'immagine con un comando `docker pull`.
  * Eseguire correttamente il contenitore locale con tutte le impostazioni di configurazione necessarie con un comando `docker run`.
  * Chiamare l'endpoint del contenitore, ottenendo una risposta di HTTP 2xx e una risposta JSON.

Tutte le variabili racchiuse tra parentesi angolari `<>`devono essere sostituite con valori personalizzati. Questa sostituzione include le parentesi angolari.

[!INCLUDE [Create a Text Analytics Containers on Azure Container Instances](includes/create-container-instances-resource.md)]

## <a name="use-the-container-instance"></a>Usare l'istanza di contenitore

1. Selezionare la **Panoramica** e copiare l'indirizzo IP. Si tratta di un indirizzo IP numerico, ad esempio `55.55.55.55`.
1. Aprire una nuova scheda del browser e usare l'indirizzo IP, ad esempio `http://<IP-address>:5000 (http://55.55.55.55:5000`. Verrà visualizzato il home page del contenitore, che informa che il contenitore è in esecuzione.

1. Selezionare **Descrizione API servizio** per visualizzare la pagina di spavalderia per il contenitore.

1. Selezionare una delle API **post** e selezionare **prova**.  I parametri vengono visualizzati con l'input. Inserire i parametri.

1. Selezionare **Execute (Esegui** ) per inviare la richiesta all'istanza del contenitore.

    Sono stati creati e usati i contenitori di servizi cognitivi nell'istanza di contenitore di Azure.
