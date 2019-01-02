---
title: SKU e piani privati | Microsoft Docs
description: Come usare gli SKU privati per gestire la disponibilità dell'offerta.
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
ms.openlocfilehash: 519f0354f2a19e106ca1072170721b27357d173e
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/04/2018
ms.locfileid: "52849208"
---
<a name="private-skus-and-plans"></a>SKU e piani privati
============

Gli SKU privati consentono di limitare la disponibilità degli SKU a clienti specifici. Quando uno SKU è contrassegnato come privato, non è disponibile in nessun catalogo pubblico, inclusi [Azure Marketplace](https://azuremarketplace.microsoft.com) e il [portale di Azure](http://portal.azure.com). Nel portale di Azure, solo i clienti con accesso allo SKU possono visualizzarlo. Verranno anche informati di avere accesso a offerte private.

>[!NOTE]
>Gli SKU privati devono avere nuovi ID piano/SKU univoci per evitare ogni eventuale conflitto con gli SKU pubblici.

È possibile usare gli SKU privati per gestire gli scenari seguenti:

1.  Pubblicare il software che si vuole rendere disponibile pubblicamente a clienti specifici e non a livello generale.
2.  Pubblicare le variazioni di software pubblico a un prezzo personalizzato per clienti specifici.
3.  Pubblicare le variazioni di software pubblico con una descrizione e termini personalizzati (tramite una nuova offerta).

Se si vuole solo modificare il prezzo, è possibile riutilizzare i dischi di un altro SKU nella stessa offerta. Con gli SKU privati non è necessario inviare nuovamente i dischi negli SKU.

<a name="mark-a-sku-private"></a>Contrassegnare uno SKU come privato
---------------------

Per contrassegnare uno SKU come privato, impostare l'opzione che chiede se lo SKU è privato:

![Contrassegnare uno SKU come privato](./media/cloud-partner-portal-publish-virtual-machine/markingskuprivate.png)

È possibile riutilizzare i dischi in un altro SKU e modificare il piano tariffario o la descrizione. Per riutilizzare i dischi, selezionare **Sì** quando viene chiesto se lo SKU riutilizza immagini di uno SKU pubblico.

Se lo SKU è contrassegnato come privato e l'offerta ha altri SKU con dischi riutilizzabili, è necessario indicare che lo SKU riutilizza i dischi di un altro SKU. Viene anche richiesto di specificare il pubblico target dello SKU privato.

>[!NOTE]
>Dopo la pubblicazione, uno SKU pubblico non può essere reso privato.

<a name="select-an-image"></a>Selezionare un'immagine
------------------

È possibile specificare nuovi dischi per lo SKU privato o riutilizzare gli stessi dischi già specificati in un altro SKU, modificando solo il piano tariffario o la descrizione. Per riutilizzare i dischi, selezionare **Sì** quando viene chiesto se lo SKU riutilizza l'immagine di uno SKU pubblico.

![Indicare l'immagine da riutilizzare](./media/cloud-partner-portal-publish-virtual-machine/selectimage1.png)

Dopo aver confermato che lo SKU riutilizza le immagini di un altro SKU, identificare lo SKU di origine delle immagini.

Le istruzioni nella schermata successiva spiegano come stabilire che lo SKU privato riutilizza le immagini dello SKU selezionato:

![Selezionare un'immagine](./media/cloud-partner-portal-publish-virtual-machine/selectimage2.png)

Quando si pubblica l'offerta, le immagini dello SKU selezionato vengono rese disponibili sotto l'ID SKU privato con le tariffe/termini personalizzati. Lo SKU privato è visibile solo al pubblico di destinazione.

Per gli aggiornamenti delle immagini, viene richiesto solo di aggiornare l'immagine dello SKU sottostante. Dietro le quinte, anche l'immagine dello SKU privato verrà aggiornata automaticamente. Allo stesso modo, se si elimina l'immagine dallo SKU sottostante, essa verrà rimossa anche dello SKU privato.

<a name="restricting-the-audience"></a>Limitazione del numero di destinatari
------------------------

Le offerte private possono essere trovate e distribuite solo dagli utenti di destinazione.
Al momento gli utenti di destinazione sono supportati tramite gli ID sottoscrizione.

Queste sottoscrizioni possono essere immesse tramite un modulo di immissione manuale **per un massimo di 10 sottoscrizioni**, oppure caricando un file CSV che consente **fino a 20.000 sottoscrizioni**.

Immissione manuale per un gruppo di destinatari limitato:

![Limitare manualmente destinatari](./media/cloud-partner-portal-publish-virtual-machine/restrictaudience1.png)

Caricamento CSV per un gruppo di destinatari limitato:

![Usare un file CSV per limitare i destinatari](./media/cloud-partner-portal-publish-virtual-machine/restrictaudience2.png)

Contenuto del file CSV di esempio:

            Type,Id,Description
            SubscriptionId,7738d703-3135-4e8d-8b81-1e70379abd9d,Private Customer

Quando si passa dall'immissione manuale alla vista di caricamento di un file CSV o dal file CSV all'immissione manuale, l'elenco precedente di ID sottoscrizione con accesso allo SKU non viene mantenuto. Viene visualizzato un avviso e l'elenco viene semplicemente sovrascritto al momento del salvataggio dell'offerta.

<a name="sync-private-subscriptions"></a>Sincronizzare le sottoscrizioni private
-------------------------

Quando si aggiungono sottoscrizioni per un'offerta pubblicata con uno SKU o un piano privato, non è necessario ripubblicare l'offerta per aggiungere informazioni sul gruppo di destinatari. È sufficiente usare un ID sottoscrizione di Azure (piani e SKU) o un ID tenant (solo piani) per aggiungere il gruppo di destinatari.

<a name="previewing-private-offers"></a>Visualizzazione in anteprima delle offerte private
-------------------------

Nella fase di anteprima/processo di gestione temporanea, solo le sottoscrizioni di anteprima a livello di offerta saranno in grado di accedere allo SKU. Questa fase di testing consente di confermare come l'offerta dovrà essere visualizzata dai clienti di destinazione ed è standard per tutti i tipi di pubblicazione.

Sottoscrizioni di anteprima a livello di offerta per accedere alle offerte in gestione temporanea:

![Preview Subscription Ids (ID sottoscrizione di anteprima)](./media/cloud-partner-portal-publish-virtual-machine/previewoffer1.png)

Dopo che l'offerta è attiva, solo le sottoscrizioni con gruppo di destinatari limitato (inserite tramite immissione manuale o file CSV) saranno in grado di visualizzare e distribuire lo SKU privato. È consigliabile **includere sempre le proprie sottoscrizioni nel gruppo di destinatari limitato** per lo SKU privato ai fini della convalida.

>[!NOTE]
>A scopo di debug, anche il supporto tecnico Microsoft e i team di progettazione avranno accesso a queste offerte private.
