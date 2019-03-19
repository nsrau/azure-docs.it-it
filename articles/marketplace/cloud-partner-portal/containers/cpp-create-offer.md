---
title: Creare un'offerta di contenitori di Azure | Microsoft Docs
description: Come pubblicare una nuova offerta di un contenitore per il Marketplace.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 11/01/2018
ms.author: pbutlerm
ms.openlocfilehash: 3b2a34a5ed2c6b57bfce0b9a4cf35cdf14cff9c9
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57900081"
---
# <a name="create-a-new-container-offer-with-the-cloud-partner-portal"></a>Creare una nuova offerta di un contenitore nel portale Cloud Partner

Questo articolo descrive come creare e pubblicare una voce di offerta di un contenitore per Azure Marketplace. Ogni offerta viene visualizzata come entità a sé stante in Azure Marketplace ed è associata a uno o più SKU.  L'offerta di un contenitore è composta dai gruppi di asset e servizi di supporto seguenti:

|  **Gruppo di risorse**   |  **Descrizione**  |
|  ---------------   |  ---------------  |
|    SKU            |  La più piccola unità distribuibile di un'offerta. Una singola offerta (classe di prodotti) può essere associata a più SKU. È possibile usare gli SKU per distinguere tra le funzionalità supportate e i modelli di fatturazione. |
|  Marketplace       | Contiene specifiche e risorse di marketing, legali e di gestione dei clienti potenziali.  <ul><li> Le risorse di marketing includono il nome dell'offerta, la descrizione e i loghi</li> <li> Gli asset legali includono l'informativa sulla privacy, le condizioni per l'utilizzo e altra documentazione legale</li>  <li> I criteri di gestione dei clienti potenziali consentono di specificare come gestire i clienti potenziali dal portale dell'utente finale Azure Marketplace.</li> </ul> |
| Supporto            | Contiene informazioni sui criteri e sui contatti di supporto |


## <a name="new-offer-form"></a>Modulo Nuova offerta 

Accedere al [portale Cloud Partner](https://cloudpartner.azure.com/) e quindi selezionare **+ Nuova offerta** nella barra dei menu a sinistra. Nel menu Nuova offerta selezionare **Contenitori** per visualizzare il modulo **Nuova offerta** e avviare il processo di definizione degli asset per la nuova offerta del contenitore.

![Selezionare l'opzione Contenitori per la nuova offerta](./media/azure-container-offer.png)

## <a name="next-steps"></a>Passaggi successivi

La pagina **Nuova offerta** per il tipo di offerta del contenitore include un set di schede e di campi modulo che è possibile usare per creare una nuova offerta. Ognuno degli articoli seguenti illustra come usare la scheda per definire i gruppi di asset e i servizi di supporto per la nuova offerta del contenitore.

- [Scheda Impostazioni dell'offerta](./cpp-offer-settings-tab.md)
- [Scheda SKU](./cpp-skus-tab.md)
- [Scheda Marketplace](./cpp-marketplace-tab.md)
- [Scheda Supporto](./cpp-support-tab.md)
