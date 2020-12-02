---
title: Guida alla pubblicazione per tipo di offerta-Marketplace commerciale Microsoft
description: Questo articolo descrive i tipi di offerta disponibili in Microsoft Commercial Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: trkeya
ms.author: trkeya
ms.date: 10/06/2020
ms.openlocfilehash: 2191a4df5b319ec16a4a6116aa99cfac50c87d9b
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96462914"
---
# <a name="publishing-guide-by-offer-type"></a>Guida alla pubblicazione per tipo di offerta

Questo articolo descrive i tipi di offerta disponibili nel Marketplace commerciale. Il *tipo di offerta* definisce la struttura dell'offerta, che include i metadati, gli artefatti e altri contenuti presentati nel Marketplace commerciale.

Dopo aver scelto [un'opzione di pubblicazione](determine-your-listing-type.md), è necessario scegliere un tipo di offerta prima di iniziare a creare l'offerta nel centro per i partner. Il tipo di offerta corrisponde al tipo di soluzione, app o servizio da pubblicare, nonché all'allineamento a prodotti e servizi Microsoft.

È possibile configurare un singolo tipo di offerta in modi diversi per abilitare diverse opzioni di pubblicazione, l'opzione di elenco, il provisioning o i prezzi. L'opzione di pubblicazione e la configurazione del tipo di offerta si allineano anche ai requisiti tecnici e di idoneità.

Prima di creare l'offerta, assicurarsi di esaminare i requisiti di idoneità per il tipo e il negozio online e i requisiti per la pubblicazione tecnica.

## <a name="list-of-offer-types"></a>Elenco di tipi di offerte

La tabella seguente illustra i tipi di offerta del Marketplace commerciale nel centro per i partner.

| **Tipo di offerta**    | **Descrizione**  |
| :------------------- | :-------------------|
| [**applicazione Azure**](plan-azure-application-offer.md) | Esistono due tipi di piani per le applicazioni di Azure: il _modello di soluzione_ e l' _applicazione gestita_. Entrambi i tipi di piano supportano l'automazione della distribuzione e della configurazione di una soluzione oltre a una singola macchina virtuale (VM). È possibile automatizzare il processo di fornitura di più risorse, tra cui macchine virtuali, rete e risorse di archiviazione, per offrire soluzioni complesse, ad esempio soluzioni IaaS. Entrambi i tipi di piano possono impiegare molti tipi diversi di risorse di Azure, tra cui, ma non limitati alle macchine virtuali.<ul><li>I piani di **modelli di soluzione** sono uno dei modi principali per pubblicare una soluzione nel Marketplace commerciale. I piani di modelli di soluzione non sono transazionali nel Marketplace commerciale, ma possono essere usati per distribuire le offerte di macchine virtuali a pagamento fatturate tramite il Marketplace commerciale. Utilizzare il tipo di piano modello soluzione quando il cliente gestirà la soluzione e le transazioni vengono fatturate in base a un altro piano.</li><br><li>I piani di **applicazioni gestite** consentono di creare e distribuire in modo semplice applicazioni chiavi in mano completamente gestite per i clienti. Hanno le stesse funzionalità dei piani di modelli di soluzione, con alcune differenze principali:</li><ul><li> Le risorse vengono distribuite in un gruppo di risorse e sono gestite dall'autore dell'app. Il gruppo di risorse è presente nella sottoscrizione del consumer, ma un'identità nel tenant dell'entità di pubblicazione dell'app ha accesso al gruppo di risorse.</li><li>In qualità di editore, è possibile specificare il costo per il supporto continuo della soluzione e le transazioni sono supportate tramite il Marketplace commerciale.</li></ul>Usare il tipo di piano di applicazione gestita quando l'utente o il cliente richiede che la soluzione sia gestita da un partner o si distribuirà una soluzione basata sulla sottoscrizione.</ul> |
| [**Contenitore di Azure**](marketplace-containers.md) | Usare il tipo di offerta contenitore di Azure quando la soluzione è un'immagine del contenitore Docker di cui è stato effettuato il provisioning come servizio contenitore di Azure basato su Kubernetes. |
| [**Macchina virtuale di Azure**](marketplace-virtual-machines.md) | Usare il tipo di offerta per le macchine virtuali quando si distribuisce un'appliance virtuale all'abbonamento associato al cliente. |
| [**Servizio di consulenza**](./plan-consulting-service-offer.md) | I servizi di consulenza consentono di connettere i clienti con servizi per supportare ed estendere l'uso dei servizi di Azure, Dynamics 365 o Power Suite.|
| [**Dynamics 365**](appsource-offer-publishing-guide.md) | Puoi pubblicare offerte AppSource che sviluppano o estendono le app Dynamics 365 business Central, Dynamics 365 Customer Engagement, Power Apps e Finance e Operations.|
| [**Modulo IoT Edge**](iot-edge-module.md) | I moduli di Azure IoT Edge sono le unità di calcolo più piccole gestite da IoT Edge e possono contenere i servizi di Microsoft, ad esempio analisi di flusso, servizi di terze parti o il codice specifico per la soluzione. |
| [**Servizi gestiti**](partner-center-portal/create-new-managed-service-offer.md) | È possibile creare offerte di servizi gestiti e gestire sottoscrizioni o gruppi di risorse delegati dai clienti tramite [Azure Lighthouse](../lighthouse/overview.md).|
| [**App Power bi** <br/> **Microsoft 365**](appsource-offer-publishing-guide.md) | È possibile pubblicare offerte AppSource che compilano o estendono Power BI e Microsoft 365.|
| [**Software come servizio**](plan-saas-offer.md) | Usare il tipo di offerta Software as a Service (SaaS) per consentire al cliente di acquistare una soluzione tecnica basata su SaaS come sottoscrizione. Per informazioni sui requisiti Single Sign-On per le offerte SaaS, vedere la pagina relativa alle [offerte saas Azure ad e transazionali nel Marketplace commerciale](azure-ad-saas.md). |


## <a name="next-steps"></a>Passaggi successivi

- Esaminare i requisiti di idoneità nell'articolo corrispondente per il tipo di offerta per finalizzare la selezione e la configurazione dell'offerta.
- Esaminare i modelli di pubblicazione per ogni negozio online per esempi sul modo in cui la soluzione è mappata a un tipo di offerta e a una configurazione.