---
title: DevOps per gestione API di Azure con i modelli ARM
description: Introduzione all'API DevOps con gestione API di Azure, uso di modelli Azure Resource Manager per gestire le distribuzioni di API in una pipeline CI/CD
services: api-management
author: miaojiang
ms.service: api-management
ms.topic: conceptual
ms.date: 10/09/2020
ms.author: apimpm
ms.openlocfilehash: 144baa0944451adaf08f0df84325b58079e19d65
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/19/2020
ms.locfileid: "92210060"
---
# <a name="cicd-for-api-management-using-azure-resource-manager-templates"></a>Integrazione continua/distribuzione continua per gestione API con modelli di Azure Resource Manager

Questo articolo introduce l'API DevOps con gestione API di Azure, usando modelli di Azure Resource Manager. Con il valore strategico delle API, una pipeline di integrazione continua (CI) e distribuzione continua (CD) è diventata un aspetto importante dello sviluppo di API. Consente alle organizzazioni di automatizzare la distribuzione delle modifiche apportate all'API senza passaggi manuali soggetti a errori, rilevare i problemi in precedenza e, infine, offrire valore agli utenti finali più velocemente. 

Per informazioni dettagliate, strumenti ed esempi di codice per implementare l'approccio DevOps descritto in questo articolo, vedere il [Resource Kit di gestione API di Azure](https://github.com/Azure/azure-api-management-devops-resource-kit) Open Source DevOps in GitHub. Poiché i clienti hanno un'ampia gamma di culture di progettazione e soluzioni di automazione esistenti, l'approccio non è una soluzione adatta a tutti.

## <a name="the-problem"></a>Problema

Attualmente, le organizzazioni hanno in genere più ambienti di distribuzione, ad esempio sviluppo, test e produzione, e usano istanze di gestione API separate per ogni ambiente. Alcune istanze sono condivise da più team di sviluppo, che sono responsabili di diverse API con diverse cadenze di rilascio.

Di conseguenza, i clienti affrontano i seguenti problemi:

* Come automatizzare la distribuzione di API in gestione API?
* Come eseguire la migrazione delle configurazioni da un ambiente a un altro
* Come evitare interferenze tra diversi team di sviluppo che condividono la stessa istanza di gestione API?

## <a name="manage-configurations-in-resource-manager-templates"></a>Gestire le configurazioni nei modelli di Gestione risorse

L'approccio proposto è illustrato nell'immagine seguente. 

:::image type="content" source="media/devops-api-development-templates/apim-devops.png" alt-text="DevOps con gestione API":::

In questo esempio sono disponibili due ambienti di distribuzione: *sviluppo* e *produzione*. Ognuno ha una propria istanza di gestione API. 

* Gli sviluppatori di API hanno accesso all'istanza di sviluppo e possono usarlo per lo sviluppo e il test delle API. 
* L'istanza di produzione viene gestita da un team designato denominato *Publisher di API*.

La chiave di questo approccio proposto consiste nel proteggere tutte le configurazioni di gestione API nei [modelli Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md). Questi modelli devono essere conservati in un sistema di controllo del codice sorgente come Git. Come illustrato nell'immagine, è disponibile un repository del server di pubblicazione che contiene tutte le configurazioni dell'istanza di gestione API di produzione in una raccolta di modelli:

|Modello  |Descrizione  |
|---------|---------|
|Modello di servizio     | Configurazioni a livello di servizio dell'istanza di gestione API, ad esempio il piano tariffario e i domini personalizzati         |
|Modelli condivisi     |  Risorse condivise in un'istanza di gestione API, ad esempio gruppi, prodotti e logger    |
|Modelli API     |  Configurazioni di API e relative sottorisorse: operazioni, criteri, impostazioni di diagnostica        |
|Modello master (Main)     |   Associa tutti gli elementi tramite il [collegamento](../azure-resource-manager/resource-group-linked-templates.md) a tutti i modelli e la loro distribuzione in ordine. Per distribuire tutte le configurazioni in un'istanza di gestione API, distribuire il modello principale. Ogni modello può essere distribuito anche singolarmente.       |

Gli sviluppatori di API potranno creare un fork del repository del server di pubblicazione in un repository per sviluppatori e usare le modifiche per le API. Nella maggior parte dei casi, si concentrano sui modelli di API per le API e non è necessario modificare i modelli di servizio o condivisi.

## <a name="migrate-configurations-to-templates"></a>Eseguire la migrazione delle configurazioni ai modelli
Si verificano problemi per gli sviluppatori di API quando si utilizzano modelli di Gestione risorse:

* Gli sviluppatori di API spesso utilizzano la [specifica openapi](https://github.com/OAI/OpenAPI-Specification) e potrebbero non avere familiarità con gestione risorse schemi. La creazione manuale di modelli potrebbe essere soggetta a errori. 

   Uno strumento di utilità denominato [**Creator**](https://github.com/Azure/azure-api-management-devops-resource-kit/blob/master/src/APIM_ARMTemplate/README.md#Creator) nel Resource Kit consente di automatizzare la creazione di modelli di API in base a un file di specifica API aperto. Inoltre, gli sviluppatori possono fornire i criteri di gestione API per un'API in formato XML. 

* Per i clienti che usano già gestione API, un'altra sfida consiste nell'estrarre le configurazioni esistenti in modelli Gestione risorse. Per i clienti, uno strumento chiamato [**Extractor**](https://github.com/Azure/azure-api-management-devops-resource-kit/blob/master/src/APIM_ARMTemplate/README.md#extractor) nel Resource Kit può aiutare a generare modelli estraendo le configurazioni dalle relative istanze di gestione API.  

## <a name="workflow"></a>Flusso di lavoro

* Una volta che gli sviluppatori di API hanno terminato di sviluppare e testare un'API e hanno generato i modelli API, possono inviare una richiesta pull per unire le modifiche nel repository del server di pubblicazione. 

* Gli editori di API possono convalidare la richiesta pull e verificare che le modifiche siano sicure e conformi. Ad esempio, è possibile verificare se solo HTTPS è autorizzato a comunicare con l'API. La maggior parte delle convalide può essere automatizzata come passaggio nella pipeline CI/CD.

* Una volta approvate e unite correttamente le modifiche, gli autori delle API possono scegliere di distribuirle nell'istanza di produzione in base a una pianificazione o su richiesta. La distribuzione dei modelli può essere automatizzata usando [azioni di GitHub](https://github.com/Azure/apimanagement-devops-samples), [Azure Pipelines](/devops/pipelines/), [Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md), l'interfaccia della riga di comando di [Azure](../azure-resource-manager/templates/deploy-cli.md)o altri strumenti.

Con questo approccio, la distribuzione delle modifiche all'API nelle istanze di gestione API può essere automatizzata ed è facile alzare di livello le modifiche da un ambiente a un altro. Poiché diversi team di sviluppo di API lavoreranno su diversi set di modelli e file API, impedisce l'interferenza tra diversi team.

## <a name="video"></a>Video

> [!VIDEO https://www.youtube.com/embed/4Sp2Qvmg6j8]

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni aggiuntive, strumenti e modelli di esempio, vedere il [Resource Kit di gestione API di Azure](https://github.com/Azure/azure-api-management-devops-resource-kit) Open Source DevOps.