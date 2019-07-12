---
title: Come monitorare l'anteprima di condivisione dei dati in Azure
description: Come monitorare l'anteprima di condivisione dei dati in Azure
author: joannapea
ms.service: data-share
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: joanpo
ms.openlocfilehash: 869c1ed41d7f78df184461bc1d8cab6c6eb8d426
ms.sourcegitcommit: 1572b615c8f863be4986c23ea2ff7642b02bc605
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/10/2019
ms.locfileid: "67789098"
---
# <a name="monitor-azure-data-share-preview"></a>Anteprima di condivisione dei dati di Azure monitor 

Questo articolo illustra come è possibile monitorare le condivisioni dati usando l'anteprima di condivisione dei dati in Azure. Come provider di dati, si è in grado di monitorare diversi aspetti di condivisione delle relazioni dei dati. I dettagli, ad esempio se il consumer di dati hanno accettato l'invito alla condivisione di dati, anche se non appena hanno creato una sottoscrizione di condivisione e iniziato a usare i dati sono tutti disponibili per il monitoraggio. 

Come un consumer di dati, è possibile monitorare gli snapshot che sono stati generati nella sottoscrizione di Azure. 

## <a name="monitor-invitation-status"></a>Monitorare lo stato di invito

Visualizza lo stato degli inviti di condivisione dei dati, passare alle condivisioni inviati -> gli inviti. 

![Lo stato dell'invito](./media/invitation-status.png "lo stato dell'invito") 

Esistono tre stati in cui l'invito può essere:

* In sospeso - destinatario di condivisione dei dati non ha ancora accettato l'invito.
* Accettato - destinatario di condivisione dei dati ha accettato l'invito.
* Rifiutato - destinatario di condivisione dei dati ha rifiutato l'invito.

> [!IMPORTANT]
> Se si elimina un invito dopo che è già stato accettato, non è equivalente alla revoca dell'accesso. Se si desidera arrestare futuri degli snapshot venga copiato nell'account di archiviazione consumer di dati, è necessario revocare l'accesso tramite il *condividere gli abbonamenti* scheda. 

## <a name="monitor-share-subscriptions"></a>Monitorare le sottoscrizioni

Visualizza lo stato delle sottoscrizioni di condivisione passando alle condivisioni inviati -> sottoscrizioni condividono. Questo fornirà informazioni dettagliate sulle sottoscrizioni attive create dai consumer di dati dopo aver accettato l'invito. È possibile interrompere gli aggiornamenti futuri per i consumer di dati, selezionando la sottoscrizione di condivisione e selezionando *revocare*. 

## <a name="snapshot-history"></a>Snapshot della cronologia 

Nella scheda Cronologia, si è in grado di visualizzare gli snapshot che sono stati copiati nel tenant del consumer di dati. Si è in grado di monitorare la frequenza e durata di ogni intervallo di snapshot. 

![Per gli snapshot della cronologia](./media/sent-shares.png "Snapshot della cronologia") 

È possibile visualizzare altri dettagli su ogni esecuzione, fare clic sulla data di inizio dell'esecuzione dello snapshot. 

## <a name="next-steps"></a>Fasi successive 

Altre informazioni su [terminologia di condivisione dei dati di Azure](terminology.md)