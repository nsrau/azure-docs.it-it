---
title: File recipe istanza di contenitore di Azure
titleSuffix: Azure Cognitive Services
description: Informazioni su come distribuire i contenitori di servizi cognitivi sull'istanza di contenitore di Azure
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 06/26/2019
ms.author: dapine
ms.openlocfilehash: 45a03a0912681b4fc33ef8df88fa00fd5458f720
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67445829"
---
# <a name="deploy-and-run-container-on-azure-container-instance-aci"></a>Distribuire ed eseguire contenitori in Azure istanza di contenitore

Con la procedura seguente, la scalabilità delle applicazioni di servizi cognitivi di Azure nel cloud con facilità con Azure [istanza di contenitore](https://docs.microsoft.com/azure/container-instances/) (ACI). Ciò consente di concentrarsi sulla compilazione di applicazioni anziché dover gestire l'infrastruttura.

## <a name="prerequisites"></a>Prerequisiti

Questa soluzione funziona con qualsiasi contenitore di servizi cognitivi. La risorsa di servizi cognitivi deve essere creata nel portale di Azure prima di usare questa soluzione. Ogni servizio cognitivo che supporti i contenitori dispone di un documento "Come installare" in modo specifico per l'installazione e configurazione del servizio per un contenitore. Poiché alcuni servizi richiedono un file o set di file come input per il contenitore, è importante capire e hanno utilizzati correttamente il contenitore prima di usare questa soluzione.

* Una risorsa di servizi cognitivi, creata nel portale di Azure.
* Servizi cognitivi **URL dell'endpoint** -esaminare le specifiche del servizio "come installare" per il contenitore, per individuare dove l'URL dell'endpoint consiste nell'usare il portale di Azure e ciò che un esempio corretto dell'URL è simile. Il formato esatto può cambiare da servizio a servizio.
* Servizi cognitivi **key** -le chiavi sono nel **chiavi** pagina per la risorsa di Azure. È necessaria solo una delle due chiavi. La chiave è una stringa di 32 caratteri alfanumerici.
* Un singolo Cognitive Services contenitore nell'host locale (computer). Verificare che è possibile:
  * Acquisisce l'immagine con un `docker pull` comando.
  * Eseguire il contenitore locale correttamente con tutte le impostazioni di configurazione necessarie con un `docker run` comando.
  * Chiamare l'endpoint del contenitore, ricevendo una risposta di tipo 2xx e una risposta JSON.

Tutte le variabili di parentesi angolari, `<>`, devono essere sostituiti con i propri valori. Questa sostituzione include le parentesi acute.

[!INCLUDE [Create a Text Analytics Containers on Azure Container Instances (ACI)](./includes/create-aci-resource.md)]

## <a name="use-the-container-instance"></a>Usare l'istanza di contenitore

1. Selezionare il **Panoramica** e copiare l'indirizzo IP. Sarà un indirizzo IP numerico, ad esempio `55.55.55.55`.
1. Aprire una nuova scheda del browser e usare l'indirizzo IP, ad esempio, `http://<IP-address>:5000 (http://55.55.55.55:5000`). Noterai home page del contenitore per comunicarti che il contenitore è in esecuzione.

1. Selezionare **descrizione del servizio API** per visualizzare la pagina di swagger per il contenitore.

1. Selezionare una qualsiasi delle **POST** API e selezionare **provarlo**.  Vengono visualizzati i parametri, tra cui l'input. Specificare i parametri.

1. Selezionare **Execute** per inviare la richiesta all'istanza di contenitore.

    Aver creato e usato i contenitori di servizi cognitivi nell'istanza di contenitore di Azure.
