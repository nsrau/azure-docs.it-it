---
title: 'Esercitazione: Creare e gestire dati esportati da Gestione costi di Azure | Microsoft Docs'
description: Questo articolo descrive come creare e gestire dati esportati di Gestione costi di Azure in modo da poterli usare in sistemi esterni.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 09/21/2018
ms.topic: tutorial
ms.service: cost-management
manager: dougeby
ms.custom: ''
ms.openlocfilehash: 0765e79b90eed49742f5eead33063907eb1db1f4
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/24/2018
ms.locfileid: "47030847"
---
# <a name="tutorial-create-and-manage-exported-data"></a>Esercitazione: Creare e gestire dati esportati

Se è già stata seguita l'esercitazione sull'analisi dei costi, si ha familiarità con il download manuale di dati di Gestione costi. Tuttavia, è possibile creare un'attività periodica giornaliera che esporta automaticamente i dati di Gestione costi nell'archiviazione di Azure. I dati esportati sono in formato CSV e contengono tutte le informazioni raccolte da Gestione costi. È quindi possibile usare i dati esportati nell'archiviazione di Azure con sistemi esterni e combinarli con dati personalizzati. È possibile usare i dati esportati anche in un sistema esterno come un dashboard o un altro sistema finanziario.

Gli esempi contenuti in questa esercitazione descrivono in modo dettagliato come esportare dati di gestione dei costi e come verificare che i dati siano stati esportati correttamente.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Creare un'esportazione giornaliera
> * Verificare che i dati vengano raccolti

## <a name="prerequisites"></a>Prerequisiti

L'esportazione dati è disponibile per tutti i clienti con [contratto Enterprise](https://azure.microsoft.com/pricing/enterprise-agreement/). Sono supportate le autorizzazioni di Azure seguenti per ogni sottoscrizione per l'esportazione dati per utente e gruppo:

- Proprietario: può creare, modificare o eliminare esportazioni pianificate per una sottoscrizione.
- Collaboratore: può creare, modificare o eliminare le proprie esportazioni pianificate. Può modificare il nome delle esportazioni pianificate create da altri.
- Lettore: può pianificare le esportazioni per cui ha le autorizzazioni.

Per gli account di archiviazione di Azure:
- Sono necessarie autorizzazioni in scrittura per modificare l'account di archiviazione configurato, indipendentemente dalle autorizzazioni per l'esportazione.
- L'account di archiviazione di Azure deve essere configurato per l'archiviazione BLOB o file.

## <a name="sign-in-to-azure"></a>Accedere ad Azure
Accedere al portale di Azure all'indirizzo [https://portal.azure.com](https://portal.azure.com/).

## <a name="create-a-daily-export"></a>Creare un'esportazione giornaliera

Gestione dei costi e fatturazione &gt; Selezionare un sottoscrizione o gruppo di risorse in una sottoscrizione &gt; Esporta &gt; **Aggiungi**.

Digitare un nome per l'esportazione e specificare la sottoscrizione, l'account di archiviazione di Azure, il contenitore e la directory di archiviazione file o il contenitore BLOB, quindi fare clic su **Crea**.

![Nuova esportazione](./media/tutorial-export-acm-data/new-export01.png)

La nuova esportazione viene visualizzata nell'elenco delle esportazioni. Per impostazione predefinita, le nuove esportazioni sono abilitate e vengono eseguite ogni giorno. Se si vuole disabilitare o eliminare un'esportazione pianificata, fare clic su qualsiasi voce nell'elenco e quindi fare clic su **Disabilita** o **Elimina**.

Inizialmente, possono essere necessarie da una a due ore prima che l'esportazione venga eseguita. Tuttavia, possono essere necessarie fino a quattro ore prima che i dati vengano visualizzati nei file esportati.

## <a name="verify-that-data-is-collected"></a>Verificare che i dati vengano raccolti

È possibile verificare che i dati di Gestione costi vengano raccolti e visualizzare il file CSV esportato in tutta semplicità usando Azure Storage Explorer.

Nell'elenco delle esportazioni fare clic sul nome dell'account di archiviazione. Nella pagina dell'account di archiviazione fare clic su Apri in Explorer. Se viene visualizzata una finestra di conferma, fare clic su **Sì** per aprire il file in Azure Storage Explorer.

![Pagina dell'account di archiviazione](./media/tutorial-export-acm-data/storage-account-page.png)

In Storage Explorer passare al contenitore che si vuole aprire e selezionare la cartella corrispondente al mese corrente. Viene visualizzato un elenco di file CSV. Selezionarne uno e quindi fare clic su **Apri**.

![Storage Explorer](./media/tutorial-export-acm-data/storage-explorer.png)

Il file viene aperto con l'applicazione o il programma impostato per aprire le estensioni di file CSV. Ecco un esempio in Excel.

![Dati di esportazione di esempio](./media/tutorial-export-acm-data/example-export-data.png)

## <a name="access-exported-data-from-other-systems"></a>Accedere a dati esportati da altri sistemi

Uno degli scopi dell'esportazione dei dati di Gestione costi è potervi accedere da sistemi esterni. È possibile usare un dashboard o un altro sistema finanziario. Poiché questi sistemi sono molto diversi tra loro, la visualizzazione di un solo esempio non è sufficiente né utile.  Tuttavia, per iniziare ad accedere ai dati dalle proprie applicazioni, fare riferimento a [Introduzione ad Archiviazione di Azure](../storage/common/storage-introduction.md).

## <a name="next-steps"></a>Passaggi successivi

Questa esercitazione illustra come:

> [!div class="checklist"]
> * Creare un'esportazione giornaliera
> * Verificare che i dati vengano raccolti

Passare alla prossima esercitazione per ottimizzare e migliorare l'efficienza identificando le risorse inattive e sottoutilizzate.

> [!div class="nextstepaction"]
> [Esaminare e implementare i consigli per l'ottimizzazione](tutorial-acm-opt-recommendations.md)
