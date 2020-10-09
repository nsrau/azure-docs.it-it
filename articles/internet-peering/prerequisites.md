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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "75775407"
---
# <a name="prerequisites-to-set-up-peering-with-microsoft"></a>Prerequisiti per la configurazione del peering con Microsoft

Verificare che i prerequisiti seguenti siano soddisfatti prima di richiedere un nuovo peering o convertire un peering legacy in una risorsa di Azure.

## <a name="azure-related-prerequisites"></a>Prerequisiti correlati ad Azure
* **Account Microsoft Azure:** Se non si dispone di un account Microsoft Azure, creare un [account di Microsoft Azure](https://azure.microsoft.com/free). Per configurare il peering è necessaria una sottoscrizione di Microsoft Azure valida e attiva perché i peering vengono modellati come risorse all'interno delle sottoscrizioni di Azure. È importante tenere presente quanto segue:
    * I tipi di risorse di Azure usati per configurare il peering sono prodotti di Azure sempre gratuiti, ovvero non viene addebitato alcun costo per la creazione di un account di Azure o la creazione di una sottoscrizione o l'accesso alle risorse di Azure **peerasn sugli** e il **peering** per configurare il peering. Questo non deve essere confuso con il contratto di peering per il peering diretto tra l'utente e Microsoft, i termini per i quali vengono esplicitamente discussi con il team di peering. Contattare il [peering Microsoft](mailto:peering@microsoft.com) per eventuali domande in proposito.
    * È possibile usare la stessa sottoscrizione di Azure per accedere ad altri prodotti o servizi cloud di Azure che possono essere gratuiti o a pagamento. Quando si accede a un prodotto a pagamento, vengono addebitati i costi.
    * Se si sta creando un nuovo account Azure e/o una sottoscrizione, potrebbe essere possibile usufruire del credito Azure gratuito durante un periodo di valutazione, che può essere usato per provare i servizi cloud di Azure. Se interessato, visitare [Microsoft Azure account](https://azure.microsoft.com/free) per altre informazioni.

* **Associa ASN peer:** Prima di richiedere il peering, associare prima di tutto l'ASN e le informazioni di contatto alla sottoscrizione. Seguire le istruzioni riportate in [associare l'ASN peer alla sottoscrizione di Azure](howto-subscription-association-powershell.md).

## <a name="other-prerequisites"></a>Altri prerequisiti
* **Profilo PeeringDB:** I peer dovrebbero avere un profilo completo e aggiornato in [PeeringDB](https://www.peeringdb.com). Queste informazioni vengono usate nel nostro sistema di registrazione per convalidare i dettagli del peer, ad esempio le informazioni del NOC, le informazioni di contatto tecnico e la loro presenza presso le strutture di peering e così via.

## <a name="next-steps"></a>Passaggi successivi

* [Creare o modificare un peering diretto usando il portale](howto-direct-portal.md).
* [Convertire un peering diretto legacy in una risorsa di Azure con il portale](howto-legacy-direct-portal.md)
* [Creare o modificare un peering di Exchange con il portale](howto-exchange-portal.md)
* [Convertire un peering di Exchange legacy in una risorsa di Azure con il portale](howto-legacy-exchange-portal.md)