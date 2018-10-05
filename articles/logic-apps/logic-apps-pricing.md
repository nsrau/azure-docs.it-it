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
ms.date: 09/24/2018
ms.openlocfilehash: 5f9147035c07bbe4fb3f38b74025015e70dd87b3
ms.sourcegitcommit: 51a1476c85ca518a6d8b4cc35aed7a76b33e130f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2018
ms.locfileid: "47159564"
---
# <a name="pricing-model-for-azure-logic-apps"></a>Modello di determinazione dei prezzi per le App per la logica di Azure

Con App per la logica di Azure è possibile creare ed eseguire flussi di lavoro di integrazione automatizzati e scalabili nel cloud. Di seguito sono riportate informazioni dettagliate sul funzionamento dei prezzi e della fatturazione per App per la logica di Azure. 

<a name="consumption-pricing"></a>

## <a name="consumption-pricing-model"></a>Modello di prezzi a consumo

Per le nuove app per la logica create con il servizio di App per la logica "globale" o pubblico, si paga solo per le risorse usate. Queste app per la logica usano infatti un piano e un modello di determinazione dei prezzi basati sul consumo, pertanto vengono calcolate tutte le esecuzioni di azioni dell'app per la logica. Ogni passaggio della definizione di un'app per la logica è un'azione che include trigger, passaggi del flusso di controllo, chiamate ad azioni integrate e chiamate a connettori. Per altre informazioni, vedere [Prezzi di App per la logica](https://azure.microsoft.com/pricing/details/logic-apps).

<a name="fixed-pricing"></a>

## <a name="fixed-pricing-model"></a>Modello di determinazione dei prezzi fissi

> [!NOTE]
> L'ambiente del servizio di integrazione è in *anteprima privata*. Per richiedere l'accesso, [creare la richiesta di iscrizione qui](https://aka.ms/iseprivatepreview).

Per le nuove app per la logica create con un [*ambiente del servizio di integrazione* (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md), ovvero un'istanza privata isolata di App per la logica che usa risorse dedicate, si paga un prezzo mensile fisso per le azioni predefinite e i connettori standard ISE. ISE include un connettore aziendale a titolo gratuito, mentre ulteriori connettori aziendali prevedono costi in base ai prezzi aziendali al consumo. Per altre informazioni, vedere [Prezzi di App per la logica](https://azure.microsoft.com/pricing/details/logic-apps).

<a name="triggers"></a>

## <a name="triggers"></a>Trigger

I trigger sono azioni speciali che creano un'istanza di un'app per la logica ogni volta che si verifica un evento specifico. I trigger agiscono in modi diversi, che influiscono sulla misurazione dell'app per la logica.

* **Trigger di poll**: questo trigger controlla continuamente un endpoint per rilevare i messaggi che soddisfano i criteri per la creazione di un'istanza di app per la logica e avviare il flusso di lavoro. Ogni richiesta di polling viene considerata un'esecuzione e viene misurata, anche quando non viene creata alcuna istanza di app per la logica. Per specificare l'intervallo di polling, configurare il trigger tramite la finestra Progettazione app per la logica.

  [!INCLUDE [logic-apps-polling-trigger-non-standard-metering](../../includes/logic-apps-polling-trigger-non-standard-metering.md)]

* **Trigger di webhook**: questo trigger attende che un client invii una richiesta a un determinato endpoint. Ogni richiesta inviata all'endpoint del webhook viene conteggiata come esecuzione di azione. I trigger Richiesta e Webhook HTTP, ad esempio, sono entrambi trigger di webhook.

* **Trigger di ricorrenza**: questo trigger crea un'istanza dell'app per la logica in base all'intervallo di ricorrenza configurato nel trigger. Un trigger di ricorrenza, ad esempio, può essere configurato per essere eseguito ogni tre giorni o in base a una pianificazione più complessa.

Le esecuzioni dei trigger sono disponibili nella sezione Cronologia trigger del riquadro Panoramica delle app per la logica.

## <a name="actions"></a>Azioni

Le azioni predefinite, ad esempio le azioni che chiamano HTTP, Funzioni di Azure o Gestione API, e i passaggi del flusso di controllo, vengono misurati come azioni native, che hanno tipi specifici. Alle azioni che chiamano [connettori](https://docs.microsoft.com/connectors), ad esempio, è associato il tipo "ApiConnection". Questi connettori si distinguono tra connettori standard e aziendali e vengono misurati in base ai rispettivi [prezzi][pricing]. I connettori aziendali nel *Anteprima* vengono addebitati come connettori standard.

Tutte le azioni di esecuzione, indipendentemente dall'esito, vengono conteggiate e misurate come esecuzioni di azioni. Non vengono tuttavia conteggiate come esecuzioni di azioni sia le azioni ignorate a causa di condizioni non soddisfatte, sia le azioni non eseguite perché l'app per la logica è stata terminata prima del completamento. Per le app per la logica disabilitate non possono essere create nuove istanze. Di conseguenza, non viene effettuato alcun addebito mentre sono disabilitate.

> [!NOTE]
> Dopo che un'app per la logica è stata disabilitata, l'arresto di eventuali istanze in esecuzione può richiedere del tempo.

Le azioni eseguite all'interno di cicli vengono conteggiate per ogni iterazione del ciclo. Una singola azione in un ciclo ForEach che elabora un elenco di 10 elementi, ad esempio, viene conteggiata moltiplicando il numero di elementi dell'elenco (10) per il numero di azioni nel ciclo (1) più 1 per l'avvio del ciclo. Per questo esempio, quindi, il calcolo è (10 * 1) + 1, da cui risultano 11 esecuzioni di azioni.

## <a name="integration-account-usage"></a>Utilizzo dell'account di integrazione

L'utilizzo a consumo include un [account di integrazione](logic-apps-enterprise-integration-create-integration-account.md) in cui è possibile esplorare, sviluppare e testare gratuitamente le funzionalità [B2B/EDI](logic-apps-enterprise-integration-b2b.md) e di [elaborazione XML](logic-apps-enterprise-integration-xml.md) di App per la logica. È possibile avere un account di integrazione per area e archiviare un [numero specifico di artefatti](../logic-apps/logic-apps-limits-and-config.md), ad esempio partner commerciali e contratti EDI, mappe, schemi, assembly, certificati e configurazioni batch.

App per la logica offre anche account di integrazione di base e standard con il contratto di servizio di App per la logica standard. Gli account di integrazione di base vengono usati, in genere, quando si vuole usare solo la gestione dei messaggi o se un piccolo partner commerciale intrattiene una relazione commerciale con un'entità più grande. Gli account di integrazione standard supportano invece relazioni B2B più complesse e aumentano il numero di entità che è possibile gestire. Per altre informazioni, vedere la pagina relativa ai [prezzi di Azure](https://azure.microsoft.com/pricing/details/logic-apps).

## <a name="next-steps"></a>Passaggi successivi

* [Altre informazioni su App per la logica][whatis]
* [Creare la prima app per la logica][create]

[pricing]: https://azure.microsoft.com/pricing/details/logic-apps/
[whatis]: logic-apps-overview.md
[create]: quickstart-create-first-logic-app-workflow.md

