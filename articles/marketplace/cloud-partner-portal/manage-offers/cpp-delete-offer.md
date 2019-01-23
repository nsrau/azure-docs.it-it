---
title: Eliminare le offerte di Marketplace - Azure Marketplace | Microsoft Docs
description: Eliminare le offerte in Azure Marketplace e nel marketplace di AppSource usando il portale Cloud Partner
services: Azure, AppSource, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 01/09/2019
ms.author: pbutlerm
ms.openlocfilehash: 7b4ee33d3e231dc59ce1d7b4ae6337efb11e96bc
ms.sourcegitcommit: a1cf88246e230c1888b197fdb4514aec6f1a8de2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/16/2019
ms.locfileid: "54355344"
---
# <a name="delete-azure-marketplace-and-appsource-offers-or-skus"></a>Eliminare le offerte o gli SKU di Azure Marketplace e AppSource

Per vari motivi è possibile decidere di ritirare l'offerta dal relativo marketplace Microsoft. Tale operazione può assumere due forme:

- *Rimozione dell'offerta*: impedisce a nuovi clienti di acquistare o distribuire l'offerta, ma non ha alcun impatto sui clienti esistenti, ai quali è necessario fornire assistenza in base al contratto di licenza e alle normative pertinenti. 
- *Risoluzione dell'offerta*: è il processo di chiusura del servizio e/o di risoluzione del contratto di licenza tra l'editore e i clienti esistenti. Le linee guida e i criteri correlati alla rimozione e alla risoluzione di un'offerta sono forniti nel [Contratto Microsoft Marketplace per Editori](https://go.microsoft.com/fwlink/?LinkID=699560) (Sezione 7) e nei [Criteri di Partecipazione](https://azure.microsoft.com/support/legal/marketplace/participation-policies/) (Sezione 6.2). 

Questo articolo illustra i diversi scenari di eliminazione supportati e i passaggi necessari per eseguire ognuno.  

> [!NOTE]
> È possibile eliminare un'offerta non pubblicata semplicemente scegliendo il pulsante **Elimina** nella barra degli strumenti della scheda **Editor**.


## <a name="delete-a-published-sku-from-the-azure-marketplace"></a>Eliminare uno SKU pubblicato da Azure Marketplace

È possibile eliminare uno SKU pubblicato da Azure Marketplace seguendo questa procedura:

1.  Accedere al [portale Cloud Partner](https://cloudpartner.azure.com/).
2.  Nella pagina **Tutte le offerte** selezionare l'offerta.  L'offerta viene visualizzata nella scheda **Editor**.
3.  Nella barra degli strumenti a sinistra selezionare la scheda **SKU**. 
4.  Selezionare lo SKU da eliminare e fare clic sul pulsante **Elimina**.
5.  [Ripubblicare](./cpp-publish-offer.md) l'offerta su Azure Marketplace.

Dopo la pubblicazione dell'offerta modificata in Azure Marketplace, lo SKU selezionato non sarà più elencato in Azure Marketplace e nel portale di Azure.


## <a name="roll-back-to-a-previous-sku-version"></a>Eseguire il rollback di una versione precedente dello SKU

È possibile eliminare la versione corrente di uno SKU pubblicato da Azure Marketplace usando i passaggi descritti di seguito. Al termine del processo, viene eseguito il rollback dello SKU alla versione precedente.

1. Accedere al [portale Cloud Partner](https://cloudpartner.azure.com/).
2. Nella pagina **Tutte le offerte** selezionare l'offerta.  L'offerta viene visualizzata nella scheda **Editor**.
3. Nella barra degli strumenti a sinistra selezionare la scheda **SKU**. 
4. Eliminare la versione più recente dell'asset associato alla soluzione dall'elenco delle versioni del disco.  A seconda del tipo di offerta, questo campo può essere denominato **Disk Version** (Versione disco), **Versioni pacchetto** o simile. 
5. [Ripubblicare](./cpp-publish-offer.md) l'offerta su Azure Marketplace.

Dopo la pubblicazione dell'offerta modificata in Azure Marketplace, la versione corrente dello SKU elencato non verrà più visualizzata in Marketplace e nel portale di Azure.  Viene quindi eseguito il rollback dello SKU alla versione precedente.


## <a name="delete-a-live-offer"></a>Eliminare un'offerta attiva

Per la rimozione di un'offerta attiva è necessario prendere in considerazione vari aspetti di natura procedurale, legale e di business. Per ottenere informazioni dal team di supporto su come rimuovere un'offerta attiva da Azure Marketplace, seguire questa procedura:

1.  Generare un ticket di supporto tramite la pagina [Crea una richiesta di assistenza](https://go.microsoft.com/fwlink/?linkid=844975) o facendo clic su **Assistenza** in alto a destra del [portale Cloud Partner](https://cloudpartner.azure.com/).

2.  Selezionare il tipo di offerta specifico nell'elenco **Tipo di problema** e scegliere **Remove a published offer** (Rimuovi un'offerta pubblicata) dall'elenco **Categoria**.

3.  Inviare la richiesta.

Il team di supporto guida l'utente nel processo di eliminazione dell'offerta.

> [!NOTE]
> L'eliminazione di un'offerta (o di uno SKU) non avrà effetti sugli acquisti in corso dell'offerta (o dello SKU), che continueranno a funzionare come in precedenza. Le offerte o gli SKU eliminati non saranno tuttavia disponibili per eventuali acquisti futuri.


## <a name="next-steps"></a>Passaggi successivi

Dopo aver acquisito familiarità con le operazioni di base usate per gestire le offerte, è possibile procedere alla creazione di un'istanza di un'[offerta del marketplace](../cpp-marketplace-offers.md) Microsoft.
