---
title: Eliminare un'offerta/SKU da Azure Marketplace
description: Eliminare un'offerta/SKU da Azure Marketplace
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: qianw211
manager: pbutlerm
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: 5ad106d45c8bae2d41e0bde74b27f80f4d8ab79b
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/07/2018
ms.locfileid: "51241277"
---
<a name="delete-an-offersku-from-azure-marketplace"></a>Eliminare un'offerta/SKU da Azure Marketplace 
==========================================

Per vari motivi, si potrebbe decidere di rimuovere l'offerta dal Marketplace. I nuovi clienti non possono più acquistare o distribuire l'offerta, ma i clienti esistenti non saranno interessati.
Risoluzione offerta è il processo di risoluzione del servizio e/o del contratto di licenza tra l'utente e i clienti esistenti. Il materiale sussidiario e i criteri relativi alla rimozione e al termine dell'offerta sono regolati dal [Contratto per la pubblicazione in Microsoft Marketplace](https://go.microsoft.com/fwlink/?LinkID=699560) (vedere la Sezione
7) e i [Criteri di partecipazione](https://azure.microsoft.com/support/legal/marketplace/participation-policies/) (vedere la Sezione 6.2). Vengono trattati i diversi scenari di eliminazione supportati e i passaggi necessari.

<a name="delete-a-live-sku-from-azure-marketplace"></a>Eliminare una SKU attiva da Azure Marketplace 
----------------------------------------

È possibile eliminare una SKU attiva da Azure Marketplace seguendo questa procedura:

1.  Accedere al [Portale per Cloud Partner](http://cloudpartner.azure.com).
2.  Selezionare l'offerta dalla scheda All offers (Tutte le offerte).
3.  Nel riquadro sul lato sinistro della schermata selezionare la scheda SKUs (SKU).
4.  Selezionare lo SKU che si vuole eliminare e fare clic sul pulsante per eliminare tale SKU.
5.  Ripubblicare l'offerta in Azure Marketplace.

Dopo che l'offerta è attiva in Azure Marketplace, la SKU verrà eliminata da Azure Marketplace e dal portale di Azure.

<a name="roll-back-to-a-previous-sku-version"></a>Eseguire il rollback di una versione precedente dello SKU 
----------------------------------

È possibile eliminare la versione corrente di una SKU attiva da Azure Marketplace seguendo questa procedura. Al termine del processo verrà eseguito il rollback della SKU alla versione precedente.

1.  Accedere al [Portale per Cloud Partner](http://cloudpartner.azure.com).
2.  Selezionare l'offerta dalla scheda All offers (Tutte le offerte).
3.  Nel riquadro sul lato sinistro della schermata selezionare la scheda SKUs (SKU).
4.  Eliminare la versione del pacchetto più recente dall'elenco dei pacchetti pubblicati.
5.  Ripubblicare l'offerta in Azure Marketplace.

Dopo che l'offerta è attiva in Azure Marketplace, la versione corrente della SKU elencata verrà eliminata da Azure Marketplace e dal portale di Azure.
Verrà eseguito il rollback dello SKU alla versione precedente.

<a name="delete-a-live-offer"></a>Eliminare un'offerta attiva 
-------------------

Per ottenere informazioni dal team di supporto su come rimuovere un'offerta attiva da Azure Marketplace, seguire questa procedura:

1.  Generare un ticket di supporto usando questo [collegamento](https://go.microsoft.com/fwlink/?linkid=844975)
2.  Selezionare Managing offers (Gestione offerte) nell'elenco Problem type (Tipo di problema), Modifying an offer (Modifica di un'offerta) e/o SKU already in production (SKU già in produzione) nell'elenco Category (Categoria).
3.  Inviare la richiesta.

Il team di supporto guiderà l'utente nel processo di eliminazione dell'offerta.

L'eliminazione di uno SKU/offerta non avrà effetto sugli acquisti correnti dell'offerta/SKU già realizzati. Non subiranno modifiche in termini di funzionamento, ma non saranno disponibili per eventuali nuovi acquisti in futuro.
