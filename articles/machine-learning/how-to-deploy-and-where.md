---
title: Come e dove distribuire i modelli
titleSuffix: Azure Machine Learning
description: Informazioni su come e dove distribuire i modelli di Azure Machine Learning, tra cui istanze di contenitore di Azure, servizio Azure Kubernetes, Azure IoT Edge e matrici di Gate programmabili da campo.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: gopalv
author: gvashishtha
ms.reviewer: larryfr
ms.date: 07/08/2020
ms.topic: conceptual
ms.custom: how-to, tracking-python
zone_pivot_groups: aml-control-methods
ms.openlocfilehash: 42205f87e2025fa7f4492cb76aeb44a1fbf46eb6
ms.sourcegitcommit: 8def3249f2c216d7b9d96b154eb096640221b6b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/03/2020
ms.locfileid: "87542788"
---
# <a name="deploy-models-with-azure-machine-learning"></a>Distribuire modelli con Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Informazioni su come distribuire un modello di machine learning come servizio Web nel cloud di Azure o per Azure IoT Edge dispositivi.

Il flusso di lavoro è simile indipendentemente dal punto in cui si distribuisce il modello:

1. Registrare il modello.
1. Preparare una configurazione di inferenza
1. Preparare uno script di immissione (a meno che non si usi la [distribuzione senza codice](how-to-deploy-no-code-deployment.md))
1. Distribuire il modello nella destinazione di calcolo.
1. Testare il modello distribuito, detto anche servizio Web.

Per ulteriori informazioni sui concetti relativi al flusso di lavoro di distribuzione, vedere [gestire, distribuire e monitorare i modelli con Azure Machine Learning](concept-model-management-and-deployment.md).


 
::: zone pivot="cli"
[!INCLUDE [CLI quickstart](../../includes/machine-learning-how-to-deploy-and-where-cli.md)]
::: zone-end

::: zone pivot="py-sdk"
[!INCLUDE [SDK quickstart](../../includes/machine-learning-how-to-deploy-and-where-sdk.md)]
::: zone-end


### <a name="understanding-service-state"></a>Informazioni sullo stato del servizio

Durante la distribuzione del modello, è possibile che venga visualizzata la modifica dello stato del servizio durante la distribuzione completa.

Nella tabella seguente vengono descritti i diversi Stati del servizio:

| Stato WebService | Description | Stato finale?
| ----- | ----- | ----- |
| Transizione | Il servizio è in fase di distribuzione. | No |
| Unhealthy | Il servizio è stato distribuito ma non è attualmente raggiungibile.  | No |
| Non pianificabile | Non è possibile distribuire il servizio in questo momento a causa di risorse insufficienti. | No |
| Non riuscito | La distribuzione del servizio non è riuscita a causa di un errore o di un arresto anomalo. | Sì |
| Healthy | Il servizio è integro e l'endpoint è disponibile. | Sì |


### <a name="batch-inference"></a><a id="azuremlcompute"></a>Inferenza batch
Azure Machine Learning le destinazioni di calcolo vengono create e gestite da Azure Machine Learning. Possono essere usati per la stima in batch da Azure Machine Learning pipeline.

Per una procedura dettagliata di inferenza batch con Azure Machine Learning calcolo, vedere [How to Run batch Predictions](tutorial-pipeline-batch-scoring-classification.md).

### <a name="iot-edge-inference"></a><a id="iotedge"></a>Inferenza IoT Edge
Il supporto per la distribuzione in Edge è in anteprima. Per altre informazioni, vedere [Deploy Azure Machine Learning As an IOT Edge Module](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-machine-learning).


## <a name="next-steps"></a>Passaggi successivi

* [Risolvere i problemi relativi a una distribuzione non riuscita](how-to-troubleshoot-deployment.md)
* [Distribuire nel servizio Azure Kubernetes](how-to-deploy-azure-kubernetes-service.md)
* [Creazione di applicazioni client per l'utilizzo di servizi Web](how-to-consume-web-service.md)
* [Aggiornare il servizio Web](how-to-deploy-update-web-service.md)
* [Come distribuire un modello usando un'immagine Docker personalizzata](how-to-deploy-custom-docker-image.md)
* [Usare TLS per proteggere un servizio Web tramite Azure Machine Learning](how-to-secure-web-service.md)
* [Monitorare i modelli di Azure Machine Learning con Application Insights](how-to-enable-app-insights.md)
* [Raccogliere i dati per i modelli nell'ambiente di produzione](how-to-enable-data-collection.md)
* [Creare avvisi e trigger per gli eventi per le distribuzioni di modelli](how-to-use-event-grid.md)

