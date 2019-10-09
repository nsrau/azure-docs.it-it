---
title: Come monitorare l'anteprima della condivisione dati di Azure
description: Informazioni su come monitorare lo stato degli inviti, condividere le sottoscrizioni e la cronologia snapshot nell'anteprima della condivisione dati di Azure.
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: 22c95f1ac541e1288494ed85cc9654d42780ea60
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2019
ms.locfileid: "72169115"
---
# <a name="monitor-azure-data-share-preview"></a>Monitorare l'anteprima della condivisione dati di Azure 

Questo articolo illustra come è possibile monitorare le condivisioni dati usando l'anteprima di condivisione dati di Azure. Come provider di dati, è possibile monitorare diversi aspetti delle relazioni di condivisione dei dati. I dettagli, ad esempio se i consumer di dati hanno accettato l'invito alla condivisione dati, nonché se hanno creato una sottoscrizione di condivisione e iniziato a usare i dati sono tutti disponibili per il monitoraggio. 

In qualità di consumer di dati, è possibile monitorare gli snapshot attivati nella sottoscrizione di Azure. 

## <a name="monitor-invitation-status"></a>Monitorare lo stato degli inviti

Per visualizzare lo stato degli inviti alla condivisione dati, passare a condivisioni inviate-> inviti. 

![](./media/invitation-status.png "Stato invito") stato invito 

È possibile che l'invito si trovi in tre stati:

* In sospeso: il destinatario della condivisione dati non ha ancora accettato l'invito.
* Accettato: il destinatario della condivisione dati ha accettato l'invito.
* Rifiutato: il destinatario della condivisione dati ha rifiutato l'invito.

> [!IMPORTANT]
> Se si elimina un invito dopo che è già stato accettato, non è equivalente alla revoca dell'accesso. Se si desidera arrestare la copia degli snapshot futuri nell'account di archiviazione dei consumer di dati, è necessario revocare l'accesso tramite la scheda *Condividi sottoscrizioni* . 

## <a name="monitor-share-subscriptions"></a>Monitorare le sottoscrizioni di condivisione

Per visualizzare lo stato delle sottoscrizioni di condivisione, passare a condivisioni inviate > sottoscrizioni di condivisione. In questo modo sarà possibile ottenere informazioni dettagliate sulle sottoscrizioni attive create dai consumer di dati dopo aver accettato l'invito. È possibile arrestare gli aggiornamenti futuri al consumer di dati selezionando la sottoscrizione di condivisione e selezionando *revoca*. 

## <a name="snapshot-history"></a>Cronologia snapshot 

Nella scheda cronologia è possibile visualizzare gli snapshot che sono stati copiati nel tenant del consumer di dati. È possibile monitorare la frequenza e la durata di ogni intervallo di snapshot. 

![](./media/sent-shares.png "Cronologia snapshot") cronologia snapshot 

È possibile visualizzare altri dettagli su ogni esecuzione di snapshot facendo clic sulla data di inizio dell'esecuzione. 

Per impostazione predefinita, vengono visualizzati fino a 30 giorni di cronologia snapshot. Se è necessario visualizzare la cronologia di più di 30 giorni, passare a monitoraggio-> impostazioni di diagnostica e selezionare **Aggiungi**impostazioni di diagnostica. Verrà richiesto di selezionare un account di archiviazione in cui archiviare questi log. 

(./media/diagnostic-settings.png "Impostazioni di diagnostica") ![cronologia snapshot] 

## <a name="next-steps"></a>Passaggi successivi 

Scopri di più sulla [terminologia di condivisione dati di Azure](terminology.md)