---
title: Prerequisiti per la configurazione del peering con Microsoft
titleSuffix: Azure
description: Prerequisiti per la configurazione del peering con Microsoft
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: conceptual
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 3c820a7be561aeef9b7e50fd0ac0cf4dee721af8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75775407"
---
# <a name="prerequisites-to-set-up-peering-with-microsoft"></a>Prerequisiti per la configurazione del peering con Microsoft

Verificare che i prerequisiti seguenti siano soddisfatti prima di richiedere un nuovo peering o convertire un peering legacy in una risorsa di Azure.Ensure the prerequisites below are met before you request for a new peering or convert a legacy peering to Azure resource.

## <a name="azure-related-prerequisites"></a>Prerequisiti correlati ad AzureAzure related prerequisites
* **Account di Microsoft Azure:** Se non si dispone di un account Microsoft Azure, creare un [account di Microsoft Azure.](https://azure.microsoft.com/free) Per configurare il peering, è necessaria una sottoscrizione valida e attiva di Microsoft Azure, poiché i peering vengono modellati come risorse all'interno delle sottoscrizioni di Azure.A valid and active Microsoft Azure subscription is required to set up peering, as the peerings are modeled as resources within Azure subscriptions. È importante tenere presente quanto segue:
    * I tipi di risorse di Azure usati per configurare il peering sono sempre gratuiti prodotti Di Azure, ovvero non ti viene addebitato alcun costo per la creazione di un account Azure o la creazione di una sottoscrizione o l'accesso alle risorse di Azure **PeerAsn** e **peering** per configurare il peering. Questo non deve essere confuso con il contratto di peering per il peering diretto tra te e Microsoft, i cui termini sono discussi in modo esplicito con il nostro team di peering. In caso di domande a questo proposito, contattare [il peering Microsoft.](mailto:peering@microsoft.com)
    * È possibile usare la stessa sottoscrizione di Azure per accedere ad altri prodotti o servizi cloud di Azure che possono essere gratuiti o a pagamento. Quando accedi a un prodotto a pagamento, incorrerai a costi aggiuntivi.
    * Se si crea un nuovo account e/o sottoscrizione di Azure, è possibile che si sia idonei per il credito gratuito di Azure durante un periodo di valutazione che è possibile usare per provare i servizi cloud di Azure.If you are creating a new Azure account and/or subscription, you may be eligible for free Azure credit during a trial period which you may use to try Azure Cloud services. Se interessato, visita [l'account Microsoft Azure](https://azure.microsoft.com/free) per altre informazioni.

* **ASN peer associato:** Prima di richiedere il peering, associare l'ASN e le informazioni di contatto all'abbonamento. Seguire le istruzioni in [Associa ASN peer alla sottoscrizione](howto-subscription-association-powershell.md)di Azure .

## <a name="other-prerequisites"></a>Altri prerequisiti
* **Profilo PeeringDB:** I peer devono avere un profilo completo e aggiornato in [PeeringDB](https://www.peeringdb.com). Utilizziamo queste informazioni nel nostro sistema di registrazione per convalidare i dettagli del peer, come le informazioni NOC, le informazioni di contatto tecniche e la loro presenza presso le strutture di peering, ecc.

## <a name="next-steps"></a>Passaggi successivi

* [Creare o modificare un peering diretto utilizzando il portale](howto-direct-portal.md).
* [Convertire un peering diretto legacy in una risorsa di Azure con il portale](howto-legacy-direct-portal.md)
* [Creare o modificare il peering di Exchange tramite il portale](howto-exchange-portal.md)
* [Convertire un peering di Exchange legacy in una risorsa di Azure con il portale](howto-legacy-exchange-portal.md)