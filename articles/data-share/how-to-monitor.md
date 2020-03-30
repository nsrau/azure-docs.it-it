---
title: Come monitorare la condivisione dati di AzureHow to monitor Azure Data Share
description: Informazioni su come monitorare lo stato degli inviti, condividere sottoscrizioni e cronologia snapshot nella condivisione dati di AzureLearn how to monitor invitation status, share subscriptions, and snapshot history in Azure Data Share
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: 15089dd99de0471c244a6c0d93931438442599a7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "73490507"
---
# <a name="monitor-azure-data-share"></a>Monitorare Condivisione dati di Azure  

Questo articolo illustra come monitorare le condivisioni dati usando Condivisione dati di Azure.This article explains how you can monitor your data shares using Azure Data Share. In qualità di provider di dati, è possibile monitorare vari aspetti delle relazioni di condivisione dei dati. Dettagli, ad esempio se i data consumer hanno accettato l'invito alla condivisione dati, nonché se hanno creato una sottoscrizione di condivisione e hanno iniziato a utilizzare i dati sono tutti disponibili per il monitoraggio. 

In qualità di consumer di dati, è possibile monitorare gli snapshot che sono stati attivati nella sottoscrizione di Azure.As a data consumer, you can monitor the snapshots that have been triggered into your Azure subscription. 

## <a name="monitor-invitation-status"></a>Monitorare lo stato dell'invito

Visualizzare lo stato degli inviti alla condivisione dati passando a Inviti > condivisioni inviate. 

![Stato invito](./media/invitation-status.png "Stato invito") 

Ci sono tre stati che il vostro invito può essere in:

* In sospeso: il destinatario della condivisione dati non ha ancora accettato l'invito.
* Accettato - Destinatario condivisione dati ha accettato l'invito.
* Rifiutato: il destinatario della condivisione dati ha rifiutato l'invito.

> [!IMPORTANT]
> Se si elimina un invito dopo che è già stato accettato, non è equivalente a revocare l'accesso. Se si desidera impedire che gli snapshot futuri vengano copiati nell'account di archiviazione dei consumer di dati, è necessario revocare l'accesso tramite la scheda *Condividi sottoscrizioni.* 

## <a name="monitor-share-subscriptions"></a>Monitorare le sottoscrizioni di condivisioneMonitor share subscriptions

Visualizzare lo stato delle sottoscrizioni di condivisione passando a Condivisioni inviate > Sottoscrizioni condivisione. In questo modo verranno forniti dettagli sulle sottoscrizioni attive create dai consumer di dati dopo aver accettato l'invito. È possibile interrompere gli aggiornamenti futuri per il consumer di dati selezionando la sottoscrizione di condivisione e selezionando *Revoca*. 

## <a name="snapshot-history"></a>Cronologia snapshot 

Nella scheda Cronologia è possibile visualizzare gli snapshot copiati nel tenant del consumer di dati. È possibile monitorare la frequenza e la durata di ogni intervallo di snapshot. 

![Cronologia snapshot](./media/sent-shares.png "Cronologia snapshot") 

È possibile visualizzare ulteriori dettagli su ogni esecuzione dello snapshot facendo clic sulla data di inizio dell'esecuzione. 

Per impostazione predefinita, per impostazione predefinita vengono visualizzati fino a 30 giorni di cronologia delle istantanee. Se è necessario visualizzare più di 30 giorni di cronologia, passare a Monitoraggio -> Impostazioni di diagnostica e selezionare **Aggiungi impostazione diagnostica**. Sarà necessario selezionare un account di archiviazione in cui archiviare questi log. 

![Cronologia snapshot](./media/diagnostic-settings.png "Impostazioni di diagnostica") 

## <a name="next-steps"></a>Passaggi successivi 

Altre informazioni sulla [terminologia](terminology.md) di Azure Data ShareLearn more about Azure Data Share terminology