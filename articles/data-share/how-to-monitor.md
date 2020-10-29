---
title: Come monitorare la condivisione di dati di Azure
description: Informazioni su come monitorare lo stato degli inviti, condividere le sottoscrizioni e la cronologia snapshot nella condivisione dati di Azure
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: how-to
ms.date: 07/30/2020
ms.openlocfilehash: cc400922115f348090677661cd1b30434be19b72
ms.sourcegitcommit: d76108b476259fe3f5f20a91ed2c237c1577df14
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2020
ms.locfileid: "92910475"
---
# <a name="monitor-azure-data-share"></a>Monitorare Condivisione dati di Azure  

Questo articolo illustra come è possibile monitorare le condivisioni dati usando la condivisione di dati di Azure. Come provider di dati, è possibile monitorare diversi aspetti delle relazioni di condivisione dei dati. I dettagli, ad esempio se i consumer di dati hanno accettato l'invito alla condivisione dati, nonché se hanno creato una sottoscrizione di condivisione e iniziato a usare i dati sono tutti disponibili per il monitoraggio. 

In qualità di consumer di dati, è possibile monitorare gli snapshot attivati nella sottoscrizione di Azure. 

## <a name="monitor-invitation-status"></a>Monitorare lo stato degli inviti

Per visualizzare lo stato degli inviti alla condivisione dati, passare a condivisioni inviate-> inviti. 

![Stato invito](./media/invitation-status.png "Stato invito") 

È possibile che l'invito si trovi in tre stati:

* In sospeso: il destinatario della condivisione dati non ha ancora accettato l'invito.
* Accettato: il destinatario della condivisione dati ha accettato l'invito.
* Rifiutato: il destinatario della condivisione dati ha rifiutato l'invito.

> [!IMPORTANT]
> Se si elimina un invito dopo che è già stato accettato, non è equivalente alla revoca dell'accesso. Se si desidera arrestare la copia degli snapshot futuri nell'account di archiviazione dei consumer di dati, è necessario revocare l'accesso tramite la scheda *Condividi sottoscrizioni* . 

## <a name="monitor-share-subscriptions"></a>Monitorare le sottoscrizioni di condivisione

Per visualizzare lo stato delle sottoscrizioni di condivisione, passare a condivisioni inviate > sottoscrizioni di condivisione. In questo modo sarà possibile ottenere informazioni dettagliate sulle sottoscrizioni attive create dai consumer di dati dopo aver accettato l'invito. È possibile arrestare gli aggiornamenti futuri al consumer di dati selezionando la sottoscrizione di condivisione e selezionando *revoca* . 

## <a name="snapshot-history"></a>Cronologia snapshot 

Nella scheda **cronologia** di una condivisione è possibile visualizzare quando i dati vengono copiati dal provider di dati all'archivio dati del consumer di dati. È possibile monitorare la frequenza, la durata e lo stato di ogni snapshot. 

![Screenshot mostra le condivisioni inviate nel portale di Azure.](./media/sent-shares.png "Cronologia snapshot") 

È possibile visualizzare altri dettagli su ogni esecuzione di snapshot facendo clic sulla data di inizio dell'esecuzione. Fare quindi clic sullo stato di ogni set di dati per visualizzare la quantità di dati trasferiti, il numero di file/record copiati, la durata dello snapshot, il numero di Vcore usati e il messaggio di errore, se presenti. 

Viene visualizzato un massimo di 30 giorni di cronologia degli snapshot. Se è necessario salvare e visualizzare più di 30 giorni di cronologia, è possibile sfruttare le impostazioni di diagnostica.

## <a name="diagnostic-setting"></a>Impostazione di diagnostica

È possibile configurare l'impostazione di diagnostica per salvare i dati o gli eventi del log. Passare a monitoraggio-> impostazioni di diagnostica e selezionare Aggiungi impostazioni di **diagnostica** . Selezionare i dati o gli eventi di log a cui si è interessati e dove si desidera archiviarli o inviarli. 

![Screenshot mostra la pagina delle impostazioni di diagnostica nell'portale di Azure.](./media/diagnostic-settings.png "Impostazioni di diagnostica") 

## <a name="next-steps"></a>Passaggi successivi 

Scopri di più sulla [terminologia di condivisione dati di Azure](terminology.md)