---
title: Prezzi e fatturazione - App per la logica di Azure | Microsoft Docs
description: Informazioni sui prezzi e sulla fatturazione per App per la logica di Azure
services: logic-apps
ms.service: logic-apps
ms.suite: logic-apps
author: kevinlam1
ms.author: klam
ms.reviewer: estfan, LADocs
manager: carmonm
ms.assetid: f8f528f5-51c5-4006-b571-54ef74532f32
ms.topic: article
ms.date: 02/26/2019
ms.openlocfilehash: 9b5452f112c6325dafd5edbe693b90ec2a94abc0
ms.sourcegitcommit: f7f4b83996640d6fa35aea889dbf9073ba4422f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/28/2019
ms.locfileid: "56990238"
---
# <a name="pricing-model-for-azure-logic-apps"></a>Modello di determinazione dei prezzi per le App per la logica di Azure

Con App per la logica di Azure è possibile creare ed eseguire flussi di lavoro di integrazione automatizzati e scalabili nel cloud. Di seguito sono riportate informazioni dettagliate sul funzionamento dei prezzi e della fatturazione per App per la logica di Azure. 

<a name="consumption-pricing"></a>

## <a name="consumption-pricing-model"></a>Modello di prezzi a consumo

Per le nuove app per la logica eseguite nel servizio App per la logica pubblico o "globale" si paga solo per le risorse usate. Queste app per la logica usano un piano e un modello di determinazione prezzi in base al consumo. Nella definizione dell'app per la logica ogni passaggio è un'azione. Le azioni includono il trigger, i passaggi del flusso di controllo, le azioni predefinite e le chiamate al connettore. Il servizio App per la logica misura tutte le azioni eseguite nell'app per la logica.  
Per altre informazioni, vedere [Prezzi di App per la logica](https://azure.microsoft.com/pricing/details/logic-apps).

<a name="fixed-pricing"></a>

## <a name="fixed-pricing-model"></a>Modello di determinazione dei prezzi fissi

Nuova App per la logica eseguite all'interno di un' [ *ambiente del servizio di integrazione* (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md), si paga un prezzo mensile fisso per i connettori standard e le azioni predefinite. Un ISE consente di creare ed eseguire app per la logica isolate che possono accedere alle risorse in una rete virtuale di Azure. 

L'unità di base di ISE ha corretto la capacità, quindi se è necessaria una velocità effettiva maggiore, è possibile [aggiungere altre unità di scala](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#add-capacity), durante la creazione o in un secondo momento. L'ISE include un connettore aziendale gratuito, con un numero illimitato di connessioni. L'uso di connettori aziendali aggiuntivi viene addebitato in base al prezzo a consumo del contratto Enterprise. 

> [!NOTE]
> ISE è nel [ *versione di anteprima pubblica*](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Per altre informazioni, vedere [Prezzi di App per la logica](https://azure.microsoft.com/pricing/details/logic-apps).

<a name="triggers"></a>

## <a name="triggers"></a>Trigger

I trigger sono azioni speciali che creano un'istanza di un'app per la logica ogni volta che si verifica un evento specifico. I trigger agiscono in modi diversi, che influiscono sulla misurazione dell'app per la logica.

* **Trigger di poll**: questo trigger controlla continuamente un endpoint per rilevare i messaggi che soddisfano i criteri per la creazione di un'istanza di app per la logica e avviare il flusso di lavoro. Anche quando non viene creata alcuna istanza di app per la logica, il servizio App per la logica misura ogni richiesta di polling come un'esecuzione. Per specificare l'intervallo di polling, configurare il trigger tramite la finestra Progettazione app per la logica.

  [!INCLUDE [logic-apps-polling-trigger-non-standard-metering](../../includes/logic-apps-polling-trigger-non-standard-metering.md)]

* **Trigger di webhook**: questo trigger attende che un client invii una richiesta a un determinato endpoint. Ogni richiesta inviata all'endpoint del webhook viene conteggiata come esecuzione di azione. I trigger Richiesta e Webhook HTTP, ad esempio, sono entrambi trigger di webhook.

* **Trigger di ricorrenza**: questo trigger crea un'istanza dell'app per la logica in base all'intervallo di ricorrenza configurato nel trigger. Un trigger di ricorrenza, ad esempio, può essere configurato per essere eseguito ogni tre giorni o in base a una pianificazione più complessa.

## <a name="actions"></a>Azioni

Il servizio App per la logica misura le azioni predefinite come azioni native. Ad esempio, le azioni predefinite includono le chiamate su HTTP, le chiamate da Funzioni di Azure o Gestione API e i passaggi del flusso di controllo come cicli e condizioni, 
- ognuno con il proprio tipo di azione. Alle azioni che chiamano [connettori](https://docs.microsoft.com/connectors), ad esempio, è associato il tipo "ApiConnection". Questi connettori si distinguono tra connettori standard e aziendali e vengono misurati in base ai rispettivi [prezzi][pricing]. I connettori aziendali nel *Anteprima* vengono addebitati come connettori standard.

Tutte le azioni, eseguite correttamente o meno, vengono conteggiate e misurate dal servizio App per la logica come esecuzioni di azioni. App per la logica non misura le azioni seguenti: 

* Azioni che vengono ignorate a causa di condizioni non soddisfatte
* Azioni che non vengono eseguite perché l'app per la logica le ha arrestate prima del termine

Le app per la logica disabilitate non vengono addebitate finché sono in questo stato, in quanto non possono creare nuove istanze.

> [!NOTE]
> Dopo che un'app per la logica è stata disabilitata, l'arresto di eventuali istanze in esecuzione può richiedere del tempo.

Le azioni eseguite all'interno di cicli vengono conteggiate da App per la logica per ogni iterazione del ciclo. Supponiamo ad esempio di avere un ciclo "for each" che elabora un elenco. App per la logica misura un'azione in tale ciclo moltiplicando il numero di voci di elenco per il numero di azioni nel ciclo e aggiungendo l'azione che avvia il ciclo. Il calcolo per un elenco di 10 voci è (10 * 1) + 1, da cui risultano 11 esecuzioni di azioni.

## <a name="integration-account-usage"></a>Utilizzo dell'account di integrazione

L'utilizzo a consumo si applica agli [account di integrazione](logic-apps-enterprise-integration-create-integration-account.md) in cui è possibile esplorare, sviluppare e testare le funzionalità [B2B/EDI](logic-apps-enterprise-integration-b2b.md) e di [elaborazione XML](logic-apps-enterprise-integration-xml.md) di App per la logica senza costi aggiuntivi. È consentito un account di integrazione per area. Ogni account di integrazione può archiviare un [numero specifico di artefatti](../logic-apps/logic-apps-limits-and-config.md), ad esempio partner commerciali, contratti, mappe, schemi, assembly, certificati, configurazioni batch e così via.

App per la logica offre anche account di integrazione di base e standard con il contratto di servizio di App per la logica standard. Gli account di integrazione di base vengono usati, in genere, quando si vuole usare solo la gestione dei messaggi o se un piccolo partner commerciale intrattiene una relazione commerciale con un'entità più grande. Gli account di integrazione standard supportano invece relazioni B2B più complesse e consentono di gestire un numero di entità superiore. Per altre informazioni, vedere la pagina relativa ai [prezzi di Azure](https://azure.microsoft.com/pricing/details/logic-apps).

## <a name="next-steps"></a>Passaggi successivi

* [Altre informazioni su App per la logica][whatis]
* [Creare la prima app per la logica][create]

[pricing]: https://azure.microsoft.com/pricing/details/logic-apps/
[whatis]: logic-apps-overview.md
[create]: quickstart-create-first-logic-app-workflow.md

