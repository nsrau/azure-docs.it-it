---
title: Salvare le ricerche e aggiungere gli asset di dati in Azure Data Catalog | Microsoft Docs
description: "Articolo che include procedure che illustrano le funzionalità di Azure Data Catalog per il salvataggio delle origini dati e risorse dei dati per un uso successivo."
services: data-catalog
documentationcenter: 
author: steelanddata
manager: NA
editor: 
tags: 
ms.assetid: 6bd00a81-820d-4b7c-91fa-ab09e575474c
ms.service: data-catalog
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-catalog
ms.date: 08/15/2017
ms.author: maroche
ms.openlocfilehash: 8c319d0dcbe8b95af11b8be2368a9348b260446c
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/18/2017
---
# <a name="save-searches-and-pin-data-assets-in-azure-data-catalog"></a>Salvare le ricerche e aggiungere gli asset di dati in Azure Data Catalog
## <a name="introduction"></a>Introduzione
Azure Data Catalog fornisce le funzionalità per l'individuazione delle origini dati. È possibile eseguire ricerche e applicare filtri nel catalogo rapidamente per trovare le origini dati e comprenderne le finalità previste, semplificando l'individuazione dei dati corretti per il processo specifico.

Nelle situazioni in cui è necessario utilizzare regolarmente gli stessi dati o in cui tutti gli utenti contribuiscono regolarmente alle stesse origini dati del catalogo in base alle proprie competenze, dover eseguire ripetutamente le stesse ricerche può risultare inefficiente. In questi casi possono essere d'aiuto gli asset delle ricerche salvate e dei dati aggiunti.

## <a name="saved-searches"></a>Ricerche salvate
Una ricerca salvata di Data Catalog è una definizione di ricerca riutilizzabile per singolo utente. È possibile definire una ricerca, inclusi termini di ricerca, tag e altri filtri e quindi salvarli. È possibile eseguire nuovamente la definizione della ricerca salvata in un momento successivo per restituire eventuali asset di dati corrispondenti ai criteri di ricerca.

### <a name="create-a-saved-search"></a>Creare una ricerca salvata
Per creare una ricerca salvata, seguire questa procedura:
1. Nella finestra **Ricerca corrente** del portale di Azure Data Catalog fare clic su **Salva**. 

    ![Collegamento Salva nelle impostazioni di Ricerca corrente](./media/data-catalog-how-to-save-pin/01-save-option.png) 

2. Immettere i criteri di ricerca che si vuole usare di nuovo e quindi fare clic su **Salva**.

    ![Nome della ricerca salvato nelle impostazioni di Ricerca corrente](./media/data-catalog-how-to-save-pin/02-name.png)

3. Quando viene richiesto, immettere un nome per la ricerca salvata. Scegliere un nome significativo che descriva gli asset di dati che verranno restituiti dalla ricerca.

### <a name="manage-saved-searches"></a>Gestire le ricerche salvate
Dopo avere salvato una o più ricerche, viene visualizzata un'opzione **Ricerche salvate** sotto la casella **Ricerca corrente**. Quando l'elenco viene espanso, vengono visualizzate tutte le ricerche salvate.

 ![Elenco di ricerche salvate](./media/data-catalog-how-to-save-pin/03-list.png)

Eseguire una di queste operazioni:

* Per eseguire una ricerca, selezionare una ricerca salvata nell'elenco.

* Per visualizzare un elenco di opzioni di gestione per una ricerca salvata, fare clic sulla freccia verso il basso accanto al nome della ricerca.

    ![Opzioni per la gestione delle ricerche salvate](./media/data-catalog-how-to-save-pin/04-managing.png)

* Per immettere un nuovo nome per la ricerca salvata, selezionare **Rinomina**. La definizione della ricerca non viene modificata.

* Per rimuovere la ricerca salvata dall'elenco, selezionare **Elimina** e quindi confermare l'eliminazione.

* Per contrassegnare la ricerca salvata come ricerca predefinita, selezionare **Salva come predefinita**. Se si esegue una ricerca "vuota" dalla home page di Azure Data Catalog, viene eseguita la ricerca predefinita. La ricerca contrassegnata come predefinita viene anche visualizzata all'inizio dell'elenco **Ricerche salvate**.

### <a name="organizational-saved-searches"></a>Ricerche salvate dall'organizzazione
Tutti gli utenti dell'organizzazione possono salvare le ricerche per usarle a livello personale. Anche gli amministratori dei dati del catalogo possono salvare le ricerche per tutti gli utenti all'interno dell'organizzazione. Quando gli amministratori salvano una ricerca, viene visualizzata l'opzione **Condividere all'interno dell'azienda**. Se si seleziona questa opzione, la ricerca salvata viene condivisa con tutti gli utenti dell'organizzazione.

 ![Ricerche salvate dall'organizzazione](./media/data-catalog-how-to-save-pin/08-organizational-saved-search.png)

## <a name="pinned-data-assets"></a>Risorse di dati aggiunte
Con le ricerche salvate, è possibile salvare e usare di nuovo le definizioni di ricerca. Gli asset di dati restituiti dalle ricerche possono cambiare nel tempo perché cambiano i contenuti del catalogo. Quando si aggiungono asset di dati, è possibile identificare esplicitamente asset di dati specifici per semplificarne l'accesso senza dovere usare una ricerca.

L'aggiunta di un asset di dati è molto semplice. Per inserire l'asset di dati nell'elenco di elementi aggiunti, è sufficiente fare clic sull'icona **aggiungi**. L'icona viene visualizzata nell'angolo del riquadro dell'asset nella visualizzazione affiancata e nella colonna all'estrema sinistra della visualizzazione elenco nel portale di Azure Data Catalog.

![Icona aggiungi dell'asset di dati](./media/data-catalog-how-to-save-pin/05-pinning.png)

Anche la rimozione di un asset di dati è molto semplice. È sufficiente fare clic sull'icona **rimuovi** per disattivare l'impostazione per l'asset selezionato.

![Icona rimuovi dell'asset di dati](./media/data-catalog-how-to-save-pin/06-unpinning.png)

## <a name="the-my-assets-section"></a>Sezione Asset personali
La home page del portale di Data Catalog include una sezione **Asset personali** che visualizza le risorse di interesse dell'utente corrente. Questa sezione include sia le risorse aggiunte che le ricerche salvate.

![Sezione Asset personali nella home page](./media/data-catalog-how-to-save-pin/07-my-assets.png)

## <a name="summary"></a>Riepilogo
Azure Data Catalog offre funzionalità che semplificano l'individuazione delle origini dati necessarie, in modo che tutti i membri dell'organizzazione possano dedicare meno tempo alla ricerca dei dati e concentrarsi invece sull'uso dei dati. Le ricerche salvate e gli asset di dati aggiunti si basano su queste funzionalità di base, in modo che gli utenti possano identificare con facilità le origini dati con cui lavorano ripetutamente.
