---
title: Prezzi e fatturazione - App per la logica di Azure | Microsoft Docs
description: Informazioni sui prezzi e sulla fatturazione per App per la logica di Azure
services: logic-apps
ms.service: logic-apps
ms.suite: logic-apps
author: kevinlam1
ms.author: klam
ms.reviewer: estfan, LADocs
ms.assetid: f8f528f5-51c5-4006-b571-54ef74532f32
ms.topic: article
ms.date: 03/25/2019
ms.openlocfilehash: 7e1868dd5ce62c28c9a8aac724862c58a5e0e1da
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2019
ms.locfileid: "58805177"
---
# <a name="pricing-model-for-azure-logic-apps"></a>Modello di determinazione dei prezzi per le App per la logica di Azure

[Le App per la logica di Azure](../logic-apps/logic-apps-overview.md) consente di creare ed eseguire i flussi di lavoro di integrazione automatizzata che è possono ridimensionare nel cloud. Questo articolo viene descritto il funzionamento di prezzi e fatturazione per le App per la logica di Azure. Per informazioni specifiche sui prezzi, vedere [prezzi di Azure per la logica app](https://azure.microsoft.com/pricing/details/logic-apps).

<a name="consumption-pricing"></a>

## <a name="consumption-pricing-model"></a>Modello di prezzi a consumo

Nuova App per la logica che eseguono nel servizio App per la logica di Azure pubblico o "global", si paga solo per ciò che usi. Queste app per la logica usano un piano e un modello di determinazione prezzi in base al consumo. Nella definizione dell'app per la logica ogni passaggio è un'azione. Ad esempio, le azioni includono: 

* Trigger, che sono azioni speciali. Tutte le App per la logica richiedono un trigger come primo passaggio.
* "Predefinite" o native le azioni, ad esempio HTTP, le chiamate a funzioni di Azure e gestione API e così via
* Chiamate ai connettori, ad esempio Outlook 365, Dropbox e così via
* Controllare i passaggi del flusso, ad esempio istruzioni condizionali, i cicli e così via

Le app di Azure per la logica conteggia tutte le azioni eseguite nell'app per la logica. Altre informazioni sul funzionamento di fatturazione per [trigger](#triggers) e [azioni](#actions).

<a name="fixed-pricing"></a>

## <a name="fixed-pricing-model"></a>Modello di determinazione dei prezzi fissi

Un' [ *ambiente del servizio di integrazione* (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) consente in modo privato isolato e dedicato per poter creare ed eseguire le App per la logica che possono accedere alle risorse in una rete virtuale di Azure. Nuova App per la logica eseguite all'interno di un'istanza di ISE, si paga un prezzo mensile fisso per i connettori Standard e le azioni predefinite. L'ISE include anche un connettore Enterprise gratuito, che include il numero di connessioni. Utilizzo per i connettori aziendali aggiuntivi viene addebitato in base al prezzo di consumo dell'organizzazione. 

L'unità di base di ISE ha corretto la capacità, quindi se è necessaria una velocità effettiva maggiore, è possibile [aggiungere altre unità di scala](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#add-capacity), durante la creazione o in un secondo momento.

> [!NOTE]
> ISE è nel [ *versione di anteprima pubblica*](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Per informazioni specifiche sui prezzi, vedere [prezzi di Azure per la logica app](https://azure.microsoft.com/pricing/details/logic-apps).

<a name="connectors"></a>

## <a name="connectors"></a>Connettori

Connettori di App per la logica di Azure consentono di accedere alle app di app per la logica, servizi e sistemi nel cloud o in locale, fornendo [trigger](#triggers), [azioni](#actions), o entrambi. I connettori sono classificati come Standard o Enterprise. Per una panoramica su questi connettori, vedere [connettori per App per la logica di Azure](../connectors/apis-list.md). Le sezioni seguenti forniscono altre informazioni su come attiva la fatturazione per azioni e di lavoro.

<a name="triggers"></a>

## <a name="triggers"></a>Trigger

I trigger sono azioni speciali che creano un'istanza di un'app per la logica ogni volta che si verifica un evento specifico. I trigger agiscono in modi diversi, che influiscono sulla misurazione dell'app per la logica. Ecco i diversi tipi di trigger esistenti nelle App per la logica di Azure:

* **Trigger di poll**: Questo trigger controlla continuamente un endpoint per i messaggi che soddisfano i criteri per creare un'istanza dell'app per la logica e avviare il flusso di lavoro. Anche quando non viene creata alcuna istanza di app per la logica, il servizio App per la logica misura ogni richiesta di polling come un'esecuzione. Per specificare l'intervallo di polling, configurare il trigger tramite la finestra Progettazione app per la logica.

  [!INCLUDE [logic-apps-polling-trigger-non-standard-metering](../../includes/logic-apps-polling-trigger-non-standard-metering.md)]

* **Trigger Webhook**: Questo trigger attende che un client di inviare una richiesta a un endpoint specifico. Ogni richiesta inviata all'endpoint del webhook viene conteggiata come esecuzione di azione. I trigger Richiesta e Webhook HTTP, ad esempio, sono entrambi trigger di webhook.

* **Trigger di ricorrenza**: Questo trigger crea un'istanza dell'app per la logica in base all'intervallo di ricorrenza impostato nel trigger. Ad esempio, è possibile impostare un trigger di ricorrenza che viene eseguito ogni tre giorni o a una pianificazione più complessa.

<a name="actions"></a>

## <a name="actions"></a>Azioni

Le App per la logica di Azure usare le azioni "predefinite", ad esempio HTTP, come azioni native. Ad esempio, le azioni predefinite includono le chiamate HTTP, le chiamate da funzioni di Azure o gestione API e controllano i passaggi del flusso, ad esempio le condizioni, cicli e le istruzioni switch. Ogni azione è proprio tipo di azione. Ad esempio, le azioni che chiamano [connettori](https://docs.microsoft.com/connectors) hanno il tipo "ApiConnection". Questi connettori si distinguono Standard o connettori aziendali, vengono misurati su nelle rispettive [prezzi](https://azure.microsoft.com/pricing/details/logic-apps). I connettori Enterprise nel *Preview* vengono addebitate come connettori Standard.

Le App per la logica di Azure usare tutte le azioni di esito positivo e negativo come esecuzioni. Tuttavia, queste azioni non misura l'App per la logica:

* Azioni che vengono ignorate a causa di condizioni non soddisfatte
* Azioni che non vengono eseguite perché l'app per la logica le ha arrestate prima del termine

Per le azioni eseguite all'interno di cicli, le App per la logica di Azure conta ogni azione per ogni ciclo nel ciclo. Supponiamo ad esempio di avere un ciclo "for each" che elabora un elenco. App per la logica misura un'azione in tale ciclo moltiplicando il numero di voci di elenco per il numero di azioni nel ciclo e aggiungendo l'azione che avvia il ciclo. Pertanto, il calcolo per un elenco di 10 elementi è (10 * 1) + 1, con conseguente 11 esecuzioni di azioni.

## <a name="disabled-logic-apps"></a>App per la logica disabilitate

Disabilitato per la logica App non vengono addebitate spese poiché non è possibile creare nuove istanze anche se rimangono disabilitati.
Dopo che un'app per la logica è stata disabilitata, l'arresto di eventuali istanze in esecuzione può richiedere del tempo.

## <a name="integration-accounts"></a>Account di integrazione

Prezzi a consumo si applica a [account di integrazione](logic-apps-enterprise-integration-create-integration-account.md) in cui è possibile esplorare, sviluppare e testare il [EDI e B2B](logic-apps-enterprise-integration-b2b.md) e [elaborazione XML](logic-apps-enterprise-integration-xml.md) funzionalità nelle App per la logica di Azure gratuitamente costi aggiuntivi.
È possibile avere un account di integrazione in ogni area di Azure. Ogni account di integrazione può archiviare un [numero specifico di artefatti](../logic-apps/logic-apps-limits-and-config.md), ad esempio partner commerciali, contratti, mappe, schemi, assembly, certificati, configurazioni batch e così via.

Le App per la logica di Azure offre anche gli account di integrazione Basic e Standard con contratto di servizio app di per la logica supportati. Ecco come è possibile scegliere se usare un account di integrazione Basic o Standard:

* Usare gli account di integrazione di base quando si semplicemente desidera che la gestione dei messaggi o agire come un piccolo partner che ha una relazione tra partner commerciali con un'entità di business più grande.

* Usare account di integrazione Standard quando si hanno relazioni B2B più complesse e si vuole aumentare il numero di entità che è possibile gestire.

Per informazioni specifiche sui prezzi, vedere [prezzi di App per la logica di Azure](https://azure.microsoft.com/pricing/details/logic-apps).

<a name="data-retention"></a>

## <a name="data-retention"></a>Conservazione dei dati

Tutti gli input e output che vengono archiviati nella cronologia di esecuzione dell'app per la logica ottenere fatturato in base a un'app per la logica [eseguire il periodo di conservazione](logic-apps-limits-and-config.md#run-duration-retention-limits). Per informazioni specifiche sui prezzi, vedere [prezzi di App per la logica di Azure](https://azure.microsoft.com/pricing/details/logic-apps).

Per monitorare il consumo di memoria dell'app per la logica, è possibile:

* Visualizzare il numero di unità di archiviazione in GB utilizzati ogni mese dall'app per la logica.
* Visualizzare le dimensioni per input e output di un'azione specifica nella cronologia di esecuzione dell'app per la logica.

<a name="storage-consumption"></a>

### <a name="view-logic-app-storage-consumption"></a>Visualizzare il consumo di archiviazione per la logica app

1. Nel portale di Azure trovare e aprire l'app per la logica.

1. Dal menu dell'app per la logica, sotto **Monitoring**, selezionare **metriche**.

1. Nel riquadro di destra, sotto **titolo del grafico**, dalle **metrica** elenco, selezionare **utilizzo della fatturazione per le esecuzioni di consumo di archiviazione**.

   Questa metrica offre il numero di utilizzo delle unità di archiviazione in GB al mese vengono fatturate.

<a name="input-output-sizes"></a>

### <a name="view-action-input-and-output-sizes"></a>Visualizzare l'input dell'azione e le dimensioni di output

1. Nel portale di Azure trovare e aprire l'app per la logica.

1. Nel menu dell'app per la logica, selezionare **Panoramica**.

1. Nel riquadro di destra, sotto **cronologia esecuzioni**, selezionare l'esecuzione che ha l'input e output da controllare.

1. Sotto **esecuzione dell'app per la logica**, scegliere **Dettagli esecuzione**.

1. Nel **Dettagli esecuzione dell'app per la logica** , nella tabella, le azioni che elenca lo stato e la durata di ogni azione, selezionare l'azione si desidera visualizzare.

1. Nel **azione di app per la logica** riquadro, trovare le dimensioni per input e output dell'azione vengono visualizzati rispettivamente nella sezione **collegamento agli input** e **collegamento agli output**.

## <a name="next-steps"></a>Passaggi successivi

* [Altre informazioni sulle App per la logica di Azure](logic-apps-overview.md)
* [Creare la prima app per la logica](quickstart-create-first-logic-app-workflow.md)