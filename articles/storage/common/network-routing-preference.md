---
title: Configurare la preferenza di routing di rete (anteprima)
titleSuffix: Azure Storage
description: Configurare la preferenza di routing di rete (anteprima) per l'account di archiviazione di Azure per specificare come deve essere instradato il traffico di rete dai client all'account tramite Internet.
services: storage
author: santoshc
ms.service: storage
ms.topic: conceptual
ms.date: 05/12/2020
ms.author: santoshc
ms.reviewer: tamram
ms.subservice: common
ms.openlocfilehash: 5b4a1b1f27dff059090d78e24a6a0eca0bbbf01f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85514192"
---
# <a name="configure-network-routing-preference-for-azure-storage-preview"></a>Configurare la preferenza di routing di rete per Archiviazione di Azure (anteprima)

È possibile configurare la [preferenza di routing](../../virtual-network/routing-preference-overview.md) di rete (anteprima) per l'account di archiviazione di Azure per specificare come deve essere instradato il traffico di rete dai client all'account tramite Internet. Per impostazione predefinita, il traffico proveniente da Internet viene instradato all'endpoint pubblico dell'account di archiviazione tramite la [rete globale Microsoft](../../networking/microsoft-global-network.md). Archiviazione di Azure prevede altre opzioni per configurare il routing del traffico all'account di archiviazione.

La configurazione della preferenza di routing offre la flessibilità necessaria per ottimizzare il traffico per prestazioni di rete elevate o per i costi. Quando si configura una preferenza di routing, si specifica come verrà indirizzato il traffico all'endpoint pubblico per l'account di archiviazione per impostazione predefinita. È anche possibile pubblicare endpoint specifici della route per l'account di archiviazione.

## <a name="microsoft-global-network-versus-internet-routing"></a>Rete globale Microsoft rispetto a routing Internet

Per impostazione predefinita, i client esterni all'ambiente di Azure accedono all'account di archiviazione tramite la rete globale Microsoft. La rete globale Microsoft è ottimizzata per la selezione di percorsi a bassa latenza per offrire prestazioni di rete elevate con ampia affidabilità. Il traffico in ingresso e in uscita viene instradato attraverso il POP (Point of Presence) più vicino al client. Questa configurazione di routing predefinita garantisce che il traffico in ingresso e in uscita dall'account di archiviazione attraversi la rete globale Microsoft per la maggior parte del percorso, ottimizzando le prestazioni di rete.

È possibile cambiare la configurazione di routing per l'account di archiviazione in modo che il traffico in ingresso e in uscita venga instradato verso e dai client esterni all'ambiente di Azure tramite il POP più vicino all'account di archiviazione. Questa route riduce al minimo l'attraversamento del traffico attraverso la rete globale Microsoft, passandolo all'ISP di transito alla prima opportunità. L'utilizzo di questa configurazione di routing consente di ridurre i costi di rete.

Il diagramma seguente illustra il flusso del traffico tra il client e l'account di archiviazione per ogni preferenza di routing:

![Panoramica delle opzioni di routing per Archiviazione di Azure](media/network-routing-preference/routing-options-diagram.png)

Per altre informazioni sulla preferenza di routing in Azure, vedere [Che cos'è la preferenza di routing (anteprima)?](../../virtual-network/routing-preference-overview.md)

## <a name="routing-configuration"></a>Configurazione di routing

Come preferenza di routing predefinita per l'endpoint pubblico dell'account di archiviazione, è possibile scegliere tra la rete globale Microsoft e il routing Internet. La preferenza di routing predefinita si applica a tutto il traffico proveniente dai client esterni ad Azure e influisce sugli endpoint per Azure Data Lake Storage Gen2, archiviazione BLOB, File di Azure e siti web statici. La configurazione della preferenza di routing non è supportata per le code di Azure o le tabelle di Azure.

È anche possibile pubblicare endpoint specifici della route per l'account di archiviazione. Quando si pubblicano endpoint specifici della route, Archiviazione di Azure crea nuovi endpoint pubblici per l'account di archiviazione che instradano il traffico sul percorso scelto. Questa flessibilità consente di indirizzare il traffico all'account di archiviazione tramite una route specifica senza cambiare la preferenza di routing predefinita.

Ad esempio, la pubblicazione di un endpoint specifico della route Internet per 'StorageAccountA' influirà sugli endpoint seguenti per l'account di archiviazione:

| Servizio di archiviazione        | Endpoint specifico della route                                  |
| :--------------------- | :------------------------------------------------------- |
| Servizio BLOB           | `StorageAccountA-internetrouting.blob.core.windows.net`  |
| Data Lake Storage Gen2 | `StorageAccountA-internetrouting.dfs.core.windows.net`   |
| Servizio file           | `StorageAccountA-internetrouting.file.core.windows.net`  |
| Siti Web statici        | `StorageAccountA-internetrouting.web.core.windows.net`   |

Nel caso dell'archiviazione con ridondanza geografica e accesso in lettura (RA-GRS) o dell'archiviazione con ridondanza geografica della zona e accesso in lettura (RA-GZRS), la pubblicazione di endpoint specifici della route consente inoltre di creare automaticamente gli endpoint corrispondenti nell'area secondaria per l'accesso in lettura.

| Servizio di archiviazione        | Endpoint secondario di sola lettura specifico della route                        |
| :--------------------- | :----------------------------------------------------------------- |
| Servizio BLOB           | `StorageAccountA-internetrouting-secondary.blob.core.windows.net`  |
| Data Lake Storage Gen2 | `StorageAccountA-internetrouting-secondary.dfs.core.windows.net`   |
| Servizio file           | `StorageAccountA-internetrouting-secondary.file.core.windows.net`  |
| Siti Web statici        | `StorageAccountA-internetrouting-secondary.web.core.windows.net`   |

Le stringhe di connessione per gli endpoint specifici della route pubblicati possono essere copiate tramite il [portale di Azure](https://portal.azure.com). Queste stringhe di connessione possono essere usate per l'autorizzazione con chiave condivisa con tutti gli SDK e le API di Archiviazione di Azure esistenti.

## <a name="about-the-preview"></a>Informazioni sulla versione di anteprima

La preferenza di routing per Archiviazione di Azure è disponibile nelle aree geografiche seguenti:

- Francia meridionale
- Stati Uniti centro-settentrionali
- Stati Uniti centro-occidentali

I problemi noti seguenti influiscono sull'anteprima della preferenza di routing per Archiviazione di Azure:

- Le richieste di accesso per l'endpoint specifico della route per la rete globale Microsoft non riescono con errore HTTP 404 o equivalente. Il routing sulla rete globale Microsoft funziona come previsto quando viene impostato come preferenza di routing predefinita per l'endpoint pubblico.

## <a name="pricing-and-billing"></a>Prezzi e fatturazione

Per informazioni su prezzi e fatturazione, vedere la sezione **Prezzi** in [Che cos'è la preferenza di routing (anteprima)?](../../virtual-network/routing-preference-overview.md#pricing)

## <a name="next-steps"></a>Passaggi successivi

- [Che cos'è la preferenza di routing (anteprima)?](../../virtual-network/routing-preference-overview.md)
- [Configurare i firewall e le reti virtuali di Archiviazione di Azure](storage-network-security.md)
- [Raccomandazioni sulla sicurezza per archiviazione BLOB](../blobs/security-recommendations.md)
