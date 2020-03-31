---
title: Modello di fatturazione dei prezzi &
description: Panoramica sul funzionamento del modello di determinazione dei prezzi e fatturazione per le app per la logica di AzureOverview about how the pricing and billing model works for Azure Logic Apps
services: logic-apps
ms.suite: integration
author: kevinlam1
ms.author: klam
ms.reviewer: estfan, logicappspm
ms.topic: conceptual
ms.date: 07/19/2019
ms.openlocfilehash: 795acd67a8d4a9f8b8b7d78799a92134f249cf8d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79270459"
---
# <a name="pricing-model-for-azure-logic-apps"></a>Modello di determinazione dei prezzi per le App per la logica di Azure

[App per la logica](../logic-apps/logic-apps-overview.md) di Azure consente di creare ed eseguire flussi di lavoro di integrazione automatizzati scalabili nel cloud. Questo articolo descrive il funzionamento di fatturazione e determinazione dei prezzi per le app per la logica di Azure.This article describes how billing and pricing work for Azure Logic Apps. Per i prezzi, vedere [Prezzi delle app per la logica](https://azure.microsoft.com/pricing/details/logic-apps).

<a name="consumption-pricing"></a>

## <a name="consumption-pricing-model"></a>Modello di prezzi a consumo

Per le nuove app per la logica eseguite nel servizio App per la logica di Azure pubbliche o "globali", è necessario pagare solo per ciò che si usa. Queste app per la logica usano un piano e un modello di determinazione prezzi in base al consumo. Nell'app per la logica ogni passaggio è un'azione e le app per la logica di Azure misurano tutte le azioni eseguite nell'app per la logica.

Ad esempio, le azioni includono:For example, actions include:

* Trigger, che sono azioni speciali. Tutte le app per la logica richiedono un trigger come primo passaggio.
* ["Built-in" o azioni native](../connectors/apis-list.md#built-in) come HTTP, chiamate a Funzioni di Azure e Gestione API e così via
* Chiamate a [connettori gestiti](../connectors/apis-list.md#managed-connectors) come Outlook 365, Dropbox e così via
* Passaggi del flusso di controllo, ad esempio cicli, istruzioni condizionali e così viaControl flow steps, such as loops, conditional statements, and so on

[I connettori standard](../connectors/apis-list.md#managed-connectors) vengono addebitati al prezzo del [connettore Standard](https://azure.microsoft.com/pricing/details/logic-apps). I [connettori Enterprise](../connectors/apis-list.md#managed-connectors) generalmente disponibili vengono addebitati al prezzo del [connettore Enterprise](https://azure.microsoft.com/pricing/details/logic-apps), mentre i connettori Enterprise di anteprima pubblica vengono addebitati al prezzo del [connettore Standard](https://azure.microsoft.com/pricing/details/logic-apps).

Ulteriori informazioni sul funzionamento della fatturazione per [trigger](#triggers) e [azioni](#actions).

<a name="fixed-pricing"></a>

## <a name="fixed-pricing-model"></a>Modello di determinazione dei prezzi fissi

Un ambiente del servizio di [ *integrazione* (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) offre un modo isolato per creare ed eseguire app per la logica in grado di accedere alle risorse in una rete virtuale di Azure.An integration service environment (ISE) provides an isolated way for you to create and run logic apps that can access resources in an Azure virtual network. Per le nuove app per la logica eseguite all'interno di un ISE, paghi un [prezzo mensile fisso](https://azure.microsoft.com/pricing/details/logic-apps) per queste funzionalità:

* [Trigger](../connectors/apis-list.md#built-in) e azioni incorporati

  All'interno di un ISE, i trigger e le azioni incorporati visualizzano l'etichetta **Core** ed vengono eseguiti nella stessa ISE delle app per la logica.

* [Connettori standard](../connectors/apis-list.md#managed-connectors) e connettori [Enterprise](../connectors/apis-list.md#enterprise-connectors) (tutte le connessioni Enterprise desiderate)

   I connettori Standard ed Enterprise che visualizzano l'etichetta **ISE** vengono eseguiti nella stessa ISE delle app per la logica. I connettori che non visualizzano l'etichetta ISE vengono eseguiti nel servizio App per la logica globale. I prezzi mensili fissi si applicano anche ai connettori eseguiti nel servizio globale quando vengono utilizzati con app per la logica eseguite in isE.

* [Utilizzo dell'account](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) di integrazione senza costi aggiuntivi, in base allo [SKU ISE:](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level)

  * **SKU Premium:** un singolo account di integrazione [di livello StandardPremium](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits) SKU : A single Standard tier integration account

  * **SKU sviluppatore:** un singolo account di integrazione [di livello gratuito](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits)

  Ogni SKU ISE è limitato a 5 account di integrazione totali. Per un costo aggiuntivo, puoi avere più account di integrazione, in base al tuo SKU ISE:

  * **Premium SKU**: Fino a quattro altri account Standard. Nessun account gratuito o di base.

  * **SKU sviluppatore:** fino a 4 account Standard in più o fino a 5 account Standard totali. Nessun account Basic.

  Per ulteriori informazioni sui limiti degli account di integrazione, vedere [Limiti e configurazione](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits)delle app per la logica . Per altre informazioni sui livelli di account di integrazione e sul relativo modello di [determinazione dei prezzi,](#integration-accounts) vedere più avanti in questo argomento.

Se si sceglie lo SKU Premium ISE, l'unità di base ha capacità fissa. Se è necessaria una maggiore velocità effettiva, è possibile aggiungere altre unità di [scala,](../logic-apps/ise-manage-integration-service-environment.md#add-capacity)durante la creazione o in un secondo momento. Lo SKU ISE per sviluppatori non è in grado di aggiungere altre unità di scala. Le app per la logica eseguite in ISE non comportano costi di conservazione dei dati.

Per i prezzi, vedere [Prezzi di App per la logica.](https://azure.microsoft.com/pricing/details/logic-apps)

<a name="connectors"></a>

## <a name="connectors"></a>Connettori

I connettori delle app per la logica di Azure consentono alle app per la logica di accedere ad app, servizi e sistemi nel cloud o in locale fornendo [trigger,](#triggers) [azioni](#actions)o entrambi. I connettori sono classificati come Standard o Enterprise. Per una panoramica su questi connettori, vedere Connettori per app per la logica di Azure.For an overview about these [connectors, see Connectors for Azure Logic Apps.](../connectors/apis-list.md) Se non sono disponibili connettori predefiniti per le API REST che si desidera utilizzare nelle app per la logica, è possibile creare [connettori personalizzati,](https://docs.microsoft.com/connectors/custom-connectors)che sono solo wrapper per tali API REST. I connettori personalizzati vengono fatturati come connettori standard. Nelle sezioni seguenti vengono fornite ulteriori informazioni sul funzionamento della fatturazione per trigger e azioni.

<a name="triggers"></a>

## <a name="triggers"></a>Trigger

I trigger sono azioni speciali che creano un'istanza di un'app per la logica ogni volta che si verifica un evento specifico. I trigger agiscono in modi diversi, che influiscono sulla misurazione dell'app per la logica. Ecco i vari tipi di trigger presenti nelle app per la logica di Azure:Here are the various kinds of triggers that exist in Azure Logic Apps:

* **Trigger di polling:** questo trigger controlla continuamente un endpoint per i messaggi che soddisfano i criteri per la creazione di un'istanza dell'app per la logica e l'avvio del flusso di lavoro. Anche quando non viene creata alcuna istanza di app per la logica, il servizio App per la logica misura ogni richiesta di polling come un'esecuzione. Per specificare l'intervallo di polling, configurare il trigger tramite la finestra Progettazione app per la logica.

  [!INCLUDE [logic-apps-polling-trigger-non-standard-metering](../../includes/logic-apps-polling-trigger-non-standard-metering.md)]

* **Trigger Webhook:** questo trigger attende che un client invii una richiesta a un endpoint specifico. Ogni richiesta inviata all'endpoint del webhook viene conteggiata come esecuzione di azione. I trigger Richiesta e Webhook HTTP, ad esempio, sono entrambi trigger di webhook.

* Trigger di **ricorrenza:** questo trigger crea un'istanza dell'app per la logica in base all'intervallo di ricorrenza impostato nel trigger. Ad esempio, è possibile impostare un trigger Ricorrenza che viene eseguito ogni tre giorni o in base a una pianificazione più complessa.

<a name="actions"></a>

## <a name="actions"></a>Azioni

Le app per la logica di Azure misurano le azioni "predefinite", ad esempio HTTP, come azioni native. Ad esempio, le azioni predefinite includono chiamate HTTP, chiamate da Funzioni di Azure o Gestione API e passaggi del flusso di controllo, ad esempio condizioni, cicli e istruzioni switch. Ogni azione ha il proprio tipo di azione. Ad esempio, le azioni che chiamano [i connettori](https://docs.microsoft.com/connectors) hanno il tipo "ApiConnection". Questi connettori sono classificati come connettori Standard o Enterprise, che vengono misurati in base ai rispettivi [prezzi.](https://azure.microsoft.com/pricing/details/logic-apps) I connettori Enterprise in *Anteprima* vengono addebitati come Connettori Standard.

Le app per la logica di Azure misurano tutte le azioni riuscite e non riuscite come esecuzioni. Tuttavia, App per la logica non misura queste azioni:However, Logic Apps doesn't meter these actions:

* Azioni che vengono ignorate a causa di condizioni non soddisfatte
* Azioni che non vengono eseguite perché l'app per la logica le ha arrestate prima del termine

Per le azioni eseguite all'interno di cicli, le app per la logica di Azure contano ogni azione per ogni ciclo nel ciclo. Supponiamo ad esempio di avere un ciclo "for each" che elabora un elenco. App per la logica misura un'azione in tale ciclo moltiplicando il numero di voci di elenco per il numero di azioni nel ciclo e aggiungendo l'azione che avvia il ciclo. Pertanto, il calcolo per un elenco di 10 elementi è (10 x 1) - 1, che comporta 11 esecuzioni di azioni.

## <a name="disabled-logic-apps"></a>App per la logica disabilitate

Le app per la logica disabilitate non vengono addebitate perché non possono creare nuove istanze mentre sono disabilitate. Dopo che un'app per la logica è stata disabilitata, l'arresto di eventuali istanze in esecuzione può richiedere del tempo.

<a name="integration-accounts"></a>

## <a name="integration-accounts"></a>Account di integrazione

Un modello di [determinazione dei prezzi fisso](https://azure.microsoft.com/pricing/details/logic-apps) si applica agli account di [integrazione](logic-apps-enterprise-integration-create-integration-account.md) in cui è possibile esplorare, sviluppare e testare le funzionalità di [elaborazione B2B ed EDI](logic-apps-enterprise-integration-b2b.md) e [XML](logic-apps-enterprise-integration-xml.md) in Azure Logic Apps senza costi aggiuntivi. Ogni sottoscrizione di Azure può avere fino a un [limite specifico di account di integrazione.](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits) Ogni account di integrazione può archiviare fino a un limite specifico [di elementi,](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits)inclusi partner commerciali, accordi, mappe, schemi, assembly, certificati, configurazioni batch e così via.

Le app per la logica di Azure offrono account di integrazione gratuiti, di base e standard. I livelli Basic e Standard sono supportati dal contratto di servizio app per la logica, mentre il livello Gratuito non è supportato da un contratto di servizio e ha limiti di velocità effettiva e utilizzo. Ad eccezione degli account di integrazione di livello gratuito, è possibile avere più di un account di integrazione in ogni area di Azure.Ad eccezione di Account di integrazione di livello gratuito, è possibile avere più di un account di integrazione in ogni area di Azure.Except for Free tier integration accounts Per i prezzi, vedere [Prezzi di App per la logica.](https://azure.microsoft.com/pricing/details/logic-apps/)

Se si dispone di un ambiente del servizio di [ *integrazione* (ISE),](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) [Premium o Developer](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level), l'ISE può avere 5 account di integrazione totale. Per informazioni sul funzionamento del modello di determinazione dei prezzi fissi per un ISE, vedere la sezione Modello di [determinazione dei prezzi](#fixed-pricing) fissi precedente in questo argomento. Per i prezzi, vedere [Prezzi di App per la logica.](https://azure.microsoft.com/pricing/details/logic-apps)

Per scegliere tra un account di integrazione gratuito, di base o standard, consulta le seguenti descrizioni dei casi d'uso:

* **Gratuito**: per quando si desidera provare scenari esplorativi, non scenari di produzione

* **Basic**: Per quando si desidera solo la gestione dei messaggi o agire come un piccolo partner commerciale che ha una relazione di partner commerciale con un'entità aziendale più grande

* **Standard**: Per quando si dispone di relazioni B2B più complesse e un numero maggiore di entità che è necessario gestire

<a name="data-retention"></a>

## <a name="data-retention"></a>Conservazione dei dati

Ad eccezione delle app per la logica eseguite in un ambiente del servizio di integrazione (ISE), tutti gli input e gli output archiviati nella cronologia di esecuzione dell'app per la logica vengono fatturati in base al periodo di conservazione di [esecuzione](logic-apps-limits-and-config.md#run-duration-retention-limits)di un'app per la logica. Le app per la logica eseguite in ISE non comportano costi di conservazione dei dati. Per i prezzi, vedere [Prezzi di App per la logica.](https://azure.microsoft.com/pricing/details/logic-apps)

Per monitorare il consumo di spazio di archiviazione dell'app per la logica, è possibile:To help you monitor your logic app's storage consumption, you can:

* Visualizzare il numero di unità di archiviazione in GB utilizzate mensilmente dall'app per la logica.
* Visualizzare le dimensioni per gli input e gli output di un'azione specifica nella cronologia di esecuzione dell'app per la logica.

<a name="storage-consumption"></a>

### <a name="view-logic-app-storage-consumption"></a>Visualizzare il consumo di archiviazione dell'app per la logicaView logic app storage consumption

1. Nel portale di Azure trovare e aprire l'app per la logica.

1. Dal menu dell'app per la logica, in **Monitoraggio,** selezionare **Metriche**.

1. Nel riquadro destro, in **Titolo grafico**, nell'elenco **Metrica** selezionare **Utilizzo fatturazione per esecuzioni consumo di archiviazione**.

   Questa metrica fornisce il numero di unità di consumo di archiviazione in GB al mese che vengono fatturate.

<a name="input-output-sizes"></a>

### <a name="view-action-input-and-output-sizes"></a>Visualizzare le dimensioni di input e output delle azioni

1. Nel portale di Azure trovare e aprire l'app per la logica.

1. Nel menu dell'app per la logica selezionare **Panoramica**.

1. Nel riquadro di destra, in **Cronologia esecuzioni,** selezionare la sequenza con gli input e gli output che si desidera controllare.

1. In **Esecuzione app per la logica**scegliere Dettagli **esecuzione**.

1. Nella tabella delle azioni del riquadro dei **dettagli dell'esecuzione dell'app** per la logica selezionare l'azione che si desidera visualizzare.

1. Nel riquadro **azioni dell'app per** la logica individuare le dimensioni per gli input e gli output dell'azione visualizzati rispettivamente in **Collegamento Input** e Collegamento **Output**.

## <a name="next-steps"></a>Passaggi successivi

* [Altre informazioni su App per la logica di AzureLearn more about Azure Logic Apps](logic-apps-overview.md)
* [Creare la prima app per la logica](quickstart-create-first-logic-app-workflow.md)
