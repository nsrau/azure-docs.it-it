---
title: Configurare il peering con Microsoft
titleSuffix: Azure
description: Panoramica del peering
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: overview
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 34d340881e4f612544f4b3d68d3c1f3da598cab3
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/21/2020
ms.locfileid: "95023429"
---
# <a name="internet-peering-overview"></a>Panoramica del peering Internet

Il peering è l'interconnessione tra la rete globale di Microsoft (AS8075) e la rete del cliente, allo scopo di scambiare traffico Internet da/verso i servizi online Microsoft e i servizi di Microsoft Azure. Gli operatori o i provider di servizi possono richiedere una connessione con Microsoft in una delle posizioni perimetrali disponibili. Ogni richiesta viene esaminata da Microsoft per verificare che sia conforme ai suoi criteri di peering. È possibile configurare un peering con la rete Microsoft in due modi:

* **Peering diretto:**

    Il peering viene stabilito tramite connessioni fisiche dirette tra la rete perimetrale Microsoft e la rete del cliente. Le sessioni BGP sono configurate tra queste connessioni in base ai criteri di routing Microsoft e tramite un contratto prenegoziato. Questo scenario si identifica anche con l'acronimo PNI.

* **Peering di interscambio:**

    Si riferisce alle connessioni di peering pubblico standard presso i punti Internet Exchange (IX). Le connessioni fisiche tra la rete Microsoft e la rete del cliente passano attraverso l'infrastruttura di switch gestita da IX. Le sessioni BGP vengono configurate usando lo spazio IP fornito da IX.

## <a name="benefits-of-peering-with-microsoft"></a>Vantaggi del peering con Microsoft
* Riduzione dei costi di transito distribuendo il traffico Microsoft tramite peering con Microsoft.
* Aumento delle prestazioni per i clienti tramite riduzione dei hop di rete e della latenza con la rete perimetrale Microsoft.
* Protezione del traffico dei clienti da errori della loro rete o di quella del provider di transito, tramite peering con Microsoft in posizioni ridondanti.
* Acquisizione di informazioni sulle metriche riguardanti le connessioni di peering allo scopo di risolvere i problemi di rete.

## <a name="benefits-of-using-azure-to-set-up-peering"></a>Vantaggi dell'uso di Azure per configurare il peering

È possibile richiedere il peering con Microsoft usando Azure PowerShell o il portale. Il peering configurato in questo modo viene gestito come risorsa di Azure e offre i vantaggi seguenti:
* Procedura semplificata e automatizzata per la configurazione e la gestione del peering con Microsoft.
* Modo rapido e semplice per visualizzare e gestire tutti i peering in un'unica posizione.
* Monitoraggio dello stato e dei dati sulla larghezza di banda per tutte le connessioni.
* Possibilità di usare la stessa sottoscrizione per accedere ai servizi cloud di Azure.

Se sono già stati stabiliti peering con Microsoft, questi vengono definiti come **peering legacy**. È possibile scegliere di gestire tali peering come risorsa di Azure per sfruttare i vantaggi descritti sopra. Per inviare una nuova richiesta di peering o convertire il peering legacy in una risorsa di Azure, seguire i collegamenti nella sezione **Passaggi successivi** di seguito.

## <a name="peering-policy"></a>Criteri di peering
Microsoft adotta criteri di peering selettivi ma generalmente aperti. I peer vengono selezionati in base alle prestazioni, alla funzionalità e alla presenza di vantaggi reciproci e sono soggetti a determinati requisiti tecnici, commerciali e legali. Per informazioni dettagliate, vedere i [criteri di peering](policy.md).

## <a name="faq"></a>Domande frequenti
Per domande frequenti sul peering, vedere [Domande frequenti sul peering Internet](faqs.md).

## <a name="next-steps"></a>Passaggi successivi

* Per informazioni sulla procedura di configurazione del peering diretto con Microsoft, seguire la [procedura dettagliata sul peering diretto](walkthrough-direct-all.md)
* Per informazioni sulla procedura di configurazione del peering di interscambio con Microsoft, seguire la [procedura dettagliata sul peering di interscambio](walkthrough-exchange-all.md)
* Informazioni su alcune delle altre [funzionalità di rete](../networking/networking-overview.md) chiave di Azure.