---
title: Eliminare un&quot;offerta da Azure Marketplace | Microsoft Docs
description: Questo articolo offre dettagli sull&quot;eliminazione di un&quot;offerta da Azure Marketplace
services: cloud-partner-portal
documentationcenter: 
author: anuragdalmia
manager: hamidm
ms.service: cloud-partner-portal
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/22/2017
ms.author: andalmia
translationtype: Human Translation
ms.sourcegitcommit: 233d62b176303ca384aae9453c2e125c0c70fad2
ms.openlocfilehash: f09be39b75bd0f39094a2ec08609c0c7ff03bc8e
ms.lasthandoff: 04/12/2017


---
# <a name="delete-an-offersku-from-azure-marketplace"></a>Eliminare un'offerta/SKU da Azure Marketplace

Per vari motivi, si potrebbe decidere di rimuovere l'offerta dal Marketplace.  Rimozione offerta assicura che i nuovi clienti non possano più acquistare o distribuire l'offerta, ma non ha alcun effetto sui clienti esistenti. Risoluzione offerta è il processo di risoluzione del servizio e/o del contratto di licenza tra l'utente e i clienti esistenti.  Le indicazioni e i criteri relativi alla rimozione e alla risoluzione dell'offerta sono regolati dal [Contratto per la pubblicazione in Microsoft Marketplace](http://go.microsoft.com/fwlink/?LinkID=699560) (vedere la sezione 7) e dai [Criteri di partecipazione](https://azure.microsoft.com/support/legal/marketplace/participation-policies/) (vedere la sezione 6.2).   Questo articolo tratta i diversi scenari di eliminazione supportati e le operazioni necessarie da intraprendere.

## <a name="delete-a-live-sku-from-azure-marketplace"></a>Eliminare una SKU attiva da Azure Marketplace

È possibile eliminare una SKU attiva da Azure Marketplace seguendo questa procedura:

1.  Accedere al [Portale per Cloud Partner](https://cloudpartner.azure.com/).

2.  Selezionare l'offerta dalla scheda **Tutte le offerte**.

3.  Nel riquadro sul lato sinistro della schermata selezionare la scheda **SKU**.

4.  Selezionare la SKU che si desidera eliminare e fare clic sul pulsante Elimina per tale SKU.

5.  [Ripubblicare](./Cloud-partner-portal-make-offer-live-on-Azure-Marketplace.md) l'offerta su Azure Marketplace.

Dopo che l'offerta è attiva in Azure Marketplace, la SKU verrà eliminata da Azure Marketplace e dal portale di Azure.

## <a name="rollback-to-a-previous-sku-version"></a>Eseguire il rollback di una versione precedente della SKU

È possibile eliminare la versione corrente di una SKU attiva da Azure Marketplace seguendo questa procedura. Al termine del processo verrà eseguito il rollback della SKU alla versione precedente.

1.  Accedere al [Portale per Cloud Partner](https://cloudpartner.azure.com/).

2.  Selezionare l'offerta dalla scheda **Tutte le offerte**.

3.  Nel riquadro sul lato sinistro della schermata selezionare la scheda **SKU**.

4.  Eliminare la **Versione del disco** più recente dall'elenco delle versioni del disco pubblicate.

5.  [Ripubblicare](./Cloud-partner-portal-make-offer-live-on-Azure-Marketplace.md) l'offerta su Azure Marketplace.

Dopo che l'offerta è attiva in Azure Marketplace, la versione corrente della SKU elencata verrà eliminata da Azure Marketplace e dal portale di Azure. Verrà eseguito il rollback dello SKU alla versione precedente.

## <a name="delete-a-live-offer"></a>Eliminare un'offerta attiva

Esistono diversi aspetti che devono essere presi in considerazione nel caso di una richiesta di rimozione di un'offerta attiva. Per ottenere informazioni dal team di supporto su come rimuovere un'offerta attiva da Azure Marketplace, seguire questa procedura:

1.  Generare un ticket di supporto usando questo [collegamento](https://go.microsoft.com/fwlink/?linkid=844975)

2.  Selezionare **Gestione delle offerte** nell'elenco **Tipo di problema** e **Modifica un'offerta e/o SKU già in produzione** nell'elenco **Categoria**.

3.  Inviare la richiesta

Il team di supporto guiderà l'utente nel processo di eliminazione dell'offerta.

Si noti che l'eliminazione di una SKU/offerta non avrà effetto sugli acquisti correnti dell'offerta/SKU già realizzata. Continueranno a funzionare come prima. Non saranno disponibili per eventuali nuovi acquisti in futuro.  
