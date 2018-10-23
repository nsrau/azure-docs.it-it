---
title: Eliminare un'offerta o una SKU da Azure Marketplace | Microsoft Docs
description: Passaggi per l'eliminazione di un'offerta o di una SKU.
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
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: cc172e35e8964fad3b1a1410d1f1f3240c423ab3
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/05/2018
ms.locfileid: "48807607"
---
<a name="delete-an-offer-or-sku-from-azure-marketplace"></a>Eliminare un'offerta o una SKU da Azure Marketplace
==========================================

Per vari motivi, si potrebbe decidere di rimuovere l'offerta dal Marketplace. Rimozione offerta assicura che i nuovi clienti non possano più acquistare o distribuire l'offerta, ma non ha alcun effetto sui clienti esistenti.
Risoluzione offerta è il processo di risoluzione del servizio e/o del contratto di licenza tra l'utente e i clienti esistenti. Il materiale sussidiario e i criteri relativi alla rimozione e al termine dell'offerta sono regolati dal [Contratto per la pubblicazione in Microsoft Marketplace](http://go.microsoft.com/fwlink/?LinkID=699560) (vedere la Sezione
7) e i [Criteri di partecipazione](https://azure.microsoft.com/support/legal/marketplace/participation-policies/) (vedere la Sezione 6.2). Questo articolo tratta i diversi scenari di eliminazione supportati e le operazioni necessarie da intraprendere.

<a name="delete-a-live-sku-from-azure-marketplace"></a>Eliminare una SKU attiva da Azure Marketplace
----------------------------------------

È possibile eliminare una SKU attiva da Azure Marketplace seguendo questa procedura:

1.  Accedere al [Portale per Cloud Partner](https://cloudpartner.azure.com/).

2.  Selezionare l'offerta dalla scheda **Tutte le offerte**.

3.  Nel riquadro sul lato sinistro della schermata selezionare la scheda **SKU**.

4.  Selezionare la SKU che si desidera eliminare e fare clic sul pulsante Elimina per tale SKU.

5.  [Ripubblicare](./cloud-partner-portal-make-offer-live-on-Azure-Marketplace.md) l'offerta su Azure Marketplace.

Dopo che l'offerta è attiva in Azure Marketplace, la SKU verrà eliminata da Azure Marketplace e dal portale di Azure.

<a name="roll-back-to-a-previous-sku-version"></a>Eseguire il rollback di una versione precedente della SKU
----------------------------------

È possibile eliminare la versione corrente di una SKU attiva da Azure Marketplace seguendo questa procedura. Al termine del processo verrà eseguito il rollback della SKU alla versione precedente.

1.  Accedere al [Portale per Cloud Partner](https://cloudpartner.azure.com/).

2.  Selezionare l'offerta dalla scheda **Tutte le offerte**.

3.  Nel riquadro sul lato sinistro della schermata selezionare la scheda **SKU**.

4.  Eliminare la **Versione del disco** più recente dall'elenco delle versioni del disco pubblicate.

5.  [Ripubblicare](./cloud-partner-portal-make-offer-live-on-Azure-Marketplace.md) l'offerta su Azure Marketplace.

Dopo che l'offerta è attiva in Azure Marketplace, la versione corrente della SKU elencata verrà eliminata da Azure Marketplace e dal portale di Azure.
Verrà eseguito il rollback dello SKU alla versione precedente.

<a name="delete-a-live-offer"></a>Eliminare un'offerta attiva
-------------------

Esistono diversi aspetti che devono essere presi in considerazione nel caso di una richiesta di rimozione di un'offerta attiva. Per ottenere informazioni dal team di supporto su come rimuovere un'offerta attiva da Azure Marketplace, seguire questa procedura:

1.  Generare un ticket di supporto usando questo [collegamento](https://go.microsoft.com/fwlink/?linkid=844975), oppure facendo clic su Assistenza in alto a destra del Portale per CLoud Partner.

2.  Selezionare il tipo di offerta specifico nell'elenco **Tipo di problema** e **Rimuovere un'offerta pubblicata** nell'elenco **Categoria**.

3.  Inviare la richiesta.

Il team di supporto guiderà l'utente nel processo di eliminazione dell'offerta.

>[!NOTE]
>L'eliminazione di una SKU/offerta non avrà effetto sugli acquisti correnti dell'offerta/SKU. Le SKU/offerte continueranno a funzionare come prima. Tuttavia, non saranno disponibili per eventuali nuovi acquisti in futuro.
