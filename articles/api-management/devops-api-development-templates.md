---
title: CI/CD per gestione API di Azure con i modelli ARM
description: Introduzione all'API DevOps con gestione API di Azure, uso di modelli Azure Resource Manager per gestire le distribuzioni di API in una pipeline CI/CD
services: api-management
author: miaojiang
ms.service: api-management
ms.topic: conceptual
ms.date: 10/09/2020
ms.author: apimpm
ms.openlocfilehash: 92d108304f788279a636b1dc5e1c4e6c103ede3d
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93088880"
---
# <a name="cicd-for-api-management-using-azure-resource-manager-templates"></a>Integrazione continua/distribuzione continua per gestione API con modelli di Azure Resource Manager

Questo articolo illustra come usare l'API DevOps con gestione API di Azure tramite modelli di Azure Resource Manager. Con il valore strategico delle API, una pipeline di integrazione continua e distribuzione continua (CI/CD) è diventata un aspetto importante dello sviluppo di API. Consente alle organizzazioni di automatizzare la distribuzione delle modifiche apportate all'API senza passaggi manuali soggette a errori, rilevare i problemi in precedenza e infine offrire un valore più veloce agli utenti. 

Per informazioni dettagliate, strumenti ed esempi di codice per implementare l'approccio DevOps descritto in questo articolo, vedere il [Resource Kit di gestione API di Azure](https://github.com/Azure/azure-api-management-devops-resource-kit) Open Source DevOps in GitHub. Poiché i clienti hanno un'ampia gamma di culture di progettazione e soluzioni di automazione esistenti, l'approccio non è una soluzione adatta a tutti.

## <a name="the-problem"></a>Problema

Attualmente, le organizzazioni hanno in genere più ambienti di distribuzione, ad esempio sviluppo, test e produzione, e usano istanze di gestione API separate per ogni ambiente. Alcune istanze sono condivise da più team di sviluppo, che sono responsabili di diverse API con diverse cadenze di rilascio.

Di conseguenza, i clienti affrontano i seguenti problemi:

* Come automatizzare la distribuzione di API in gestione API
* Come eseguire la migrazione delle configurazioni da un ambiente a un altro
* Come evitare interferenze tra diversi team di sviluppo che condividono la stessa istanza di gestione API

## <a name="manage-configurations-in-resource-manager-templates"></a>Gestire le configurazioni nei modelli di Gestione risorse

Nell'immagine seguente viene illustrato l'approccio proposto. 

:::image type="content" source="media/devops-api-development-templates/apim-devops.png" alt-text="Diagramma che illustra DevOps con gestione API.":::

In questo esempio sono disponibili due ambienti di distribuzione: *sviluppo* e *produzione* . Ognuno ha una propria istanza di gestione API. 

* Gli sviluppatori di API hanno accesso all'istanza di sviluppo e possono usarlo per lo sviluppo e il test delle API. 
* Un team designato denominato *editori API* gestisce l'istanza di produzione.

La chiave di questo approccio proposto consiste nel proteggere tutte le configurazioni di gestione API nei [modelli Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md). L'organizzazione deve tenere questi modelli in un sistema di controllo del codice sorgente come Git. Come illustrato nell'immagine, un repository del server di pubblicazione contiene tutte le configurazioni dell'istanza di gestione API di produzione in una raccolta di modelli:

|Modello  |Descrizione  |
|---------|---------|
|Modello di servizio     | Configurazioni a livello di servizio dell'istanza di gestione API, ad esempio il piano tariffario e i domini personalizzati.         |
|Modelli condivisi     |  Risorse condivise in un'istanza di gestione API, ad esempio gruppi, prodotti e logger.    |
|Modelli API     |  Configurazioni di API e relative sottorisorse: operazioni, criteri, impostazioni di diagnostica.        |
|Modello master (Main)     |   Associa tutti gli elementi tramite il [collegamento](../azure-resource-manager/resource-group-linked-templates.md) a tutti i modelli e la loro distribuzione in ordine. Per distribuire tutte le configurazioni in un'istanza di gestione API, distribuire il modello principale. È anche possibile distribuire ogni modello singolarmente.       |

Gli sviluppatori di API potranno creare un fork del repository del server di pubblicazione in un repository per sviluppatori e usare le modifiche per le API. Nella maggior parte dei casi, si concentrano sui modelli di API per le API e non è necessario modificare i modelli di servizio o condivisi.

## <a name="migrate-configurations-to-templates"></a>Eseguire la migrazione delle configurazioni ai modelli
Gli sviluppatori di API affrontano problemi durante l'uso di modelli di Gestione risorse:

* Gli sviluppatori di API spesso utilizzano la [specifica openapi](https://github.com/OAI/OpenAPI-Specification) e potrebbero non avere familiarità con gestione risorse schemi. La creazione manuale di modelli potrebbe essere soggetta a errori. 

   Uno strumento denominato [Creator](https://github.com/Azure/azure-api-management-devops-resource-kit/blob/master/src/APIM_ARMTemplate/README.md#Creator) nel Resource Kit consente di automatizzare la creazione di modelli di API in base a un file di specifica API aperto. Inoltre, gli sviluppatori possono fornire i criteri di gestione API per un'API in formato XML. 

* Per i clienti che usano già gestione API, un'altra sfida consiste nell'estrarre le configurazioni esistenti in modelli Gestione risorse. Per i clienti, uno strumento chiamato [Extractor](https://github.com/Azure/azure-api-management-devops-resource-kit/blob/master/src/APIM_ARMTemplate/README.md#extractor) nel Resource Kit può aiutare a generare modelli estraendo le configurazioni dalle relative istanze di gestione API.  

## <a name="workflow"></a>Flusso di lavoro

* Dopo che gli sviluppatori di API hanno terminato di sviluppare e testare un'API e hanno generato i modelli API, possono inviare una richiesta pull per unire le modifiche nel repository del server di pubblicazione. 

* Gli editori di API possono convalidare la richiesta pull e verificare che le modifiche siano sicure e conformi. Ad esempio, è possibile verificare se solo HTTPS è autorizzato a comunicare con l'API. La maggior parte delle convalide può essere automatizzata come passaggio nella pipeline CI/CD.

* Una volta approvate e unite correttamente le modifiche, gli autori delle API possono scegliere di distribuirle nell'istanza di produzione in base a una pianificazione o su richiesta. La distribuzione dei modelli può essere automatizzata usando [azioni di GitHub](https://github.com/Azure/apimanagement-devops-samples), [Azure Pipelines](/azure/devops/pipelines), [Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md), l'interfaccia della riga di comando di [Azure](../azure-resource-manager/templates/deploy-cli.md)o altri strumenti.


Con questo approccio, un'organizzazione può automatizzare la distribuzione delle modifiche API nelle istanze di gestione API ed è facile alzare di livello le modifiche da un ambiente a un altro. Poiché i diversi team di sviluppo API lavoreranno su diversi set di modelli e file API, impedisce l'interferenza tra diversi team.

## <a name="video"></a>Video

> [!VIDEO https://www.youtube.com/embed/4Sp2Qvmg6j8]

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni aggiuntive, strumenti e modelli di esempio, vedere il [Resource Kit di gestione API di Azure](https://github.com/Azure/azure-api-management-devops-resource-kit) Open Source DevOps.