---
title: Configurare le impostazioni della cache HPC di AzureConfigure Azure HPC Cache settings
description: Viene illustrato come configurare impostazioni aggiuntive per la cache come MTU e no-root-squash e come accedere agli snapshot express dalle destinazioni di archiviazione BLOB di Azure.Explains how to configure additional settings for the cache like MTU and no-root-squash, and how to access the express snapshots from Azure Blob storage targets.
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: v-erkel
ms.openlocfilehash: 137e41a3f7d346bb612605660d7798ac2fc297d1
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81538817"
---
# <a name="configure-additional-azure-hpc-cache-settings"></a>Configurare altre impostazioni della cache HPC di AzureConfigure additional Azure HPC Cache settings

Nella pagina **Configurazione** del portale di Azure sono disponibili opzioni per la personalizzazione di diverse impostazioni. La maggior parte degli utenti non è necessario modificarli rispetto ai valori predefiniti.

Questo articolo descrive anche come usare la funzionalità snapshot per le destinazioni di archiviazione BLOB di Azure.This article also describes how to use the snapshot feature for Azure Blob storage targets. La funzionalità snapshot non ha impostazioni configurabili.

Per visualizzare le impostazioni, aprire la pagina Configurazione della cache nel portale di Azure.To see the settings, open the cache's **Configuration** page in the Azure portal.

![Schermata della pagina di configurazione nel portale di Azure](media/configuration.png)

## <a name="adjust-mtu-value"></a>Regolare il valore MTU
<!-- linked from troubleshoot-nas article -->

È possibile selezionare la dimensione massima dell'unità di trasmissione per la cache utilizzando il menu a discesa con etichetta **MTU size**.

Il valore predefinito è 1500 byte, ma è possibile modificarlo in 1400.

> [!NOTE]
> Se si abbassano le dimensioni MTU della cache, assicurarsi che i client e i sistemi di archiviazione che comunicano con la cache abbiano la stessa impostazione MTU o un valore inferiore.

La riduzione del valore MTU della cache consente di aggirare le restrizioni relative alle dimensioni dei pacchetti nel resto della rete della cache. Ad esempio, alcune VPN non possono trasmettere correttamente pacchetti full-size da 1500 byte. La riduzione delle dimensioni dei pacchetti inviati tramite la VPN potrebbe eliminare tale problema. Si noti tuttavia che un'impostazione MTU cache inferiore indica che qualsiasi altro componente che comunica con la cache, inclusi client e sistemi di archiviazione, deve avere anche un'impostazione inferiore per evitare problemi di comunicazione con la cache.

Se non si desidera modificare le impostazioni MTU in altri componenti di sistema, non abbassare l'impostazione MTU della cache. Esistono altre soluzioni per aggirare le restrizioni relative alle dimensioni dei pacchetti VPN. Leggere [Regolare le restrizioni](troubleshoot-nas.md#adjust-vpn-packet-size-restrictions) relative alle dimensioni dei pacchetti VPN nell'articolo sulla risoluzione dei problemi NAS per ulteriori informazioni sulla diagnosi e la risoluzione di questo problema.

Per altre informazioni sulle impostazioni MTU nelle reti virtuali di Azure, vedere [L'ottimizzazione delle prestazioni TCP/IP per le macchine virtuali](../virtual-network/virtual-network-tcpip-performance-tuning.md)di Azure.Learn more about MTU settings in Azure virtual networks by reading TCP/IP performance tuning for Azure VMs .

## <a name="configure-root-squash"></a>Configurare lo squash radice
<!-- linked from troubleshoot -->

L'impostazione **Abilita squash radice** controlla il modo in cui la cache HPC di Azure consente l'accesso root. Lo squash radice consente di impedire l'accesso a livello di radice da client non autorizzati.

Questa impostazione consente agli utenti di controllare l'accesso ``no_root_squash`` root a livello di cache, che consente di compensare l'impostazione richiesta per i sistemi NAS utilizzati come destinazioni di archiviazione. (Ulteriori informazioni sui prerequisiti di [destinazione di archiviazione NFS](hpc-cache-prereqs.md#nfs-storage-requirements).) Può anche migliorare la sicurezza quando viene usato con le destinazioni di archiviazione BLOB di Azure.It also can improve security when used with Azure Blob storage targets.

L'impostazione predefinita è **Sì**. (Le cache create prima di aprile 2020 potrebbero avere l'impostazione predefinita **No**.) Se abilitata, questa funzionalità impedisce anche l'utilizzo di bit di autorizzazione set-UID nelle richieste client alla cache.

## <a name="view-snapshots-for-blob-storage-targets"></a>Visualizzare gli snapshot per le destinazioni di archiviazione BLOBView snapshots for blob storage targets

La cache HPC di Azure salva automaticamente gli snapshot di archiviazione per le destinazioni di archiviazione BLOB di Azure.Azure HPC Cache automatically saves storage snapshots for Azure Blob storage targets. Gli snapshot forniscono un punto di riferimento rapido per il contenuto del contenitore di archiviazione back-end. Gli snapshot non sostituiscono i backup dei dati e non includono informazioni sullo stato dei dati memorizzati nella cache.

> [!NOTE]
> Questa funzionalità snapshot è diversa dalla funzionalità snapshot inclusa nel software di archiviazione NetApp, Isilon o .FS. Tali implementazioni dello snapshot scaricano le modifiche dalla cache al sistema di archiviazione back-end prima di creare lo snapshot.
>
> Per migliorare l'efficienza, lo snapshot della cache HPC di Azure non scarica prima le modifiche e registra solo i dati scritti nel contenitore BLOB. Questo snapshot non rappresenta lo stato dei dati memorizzati nella cache, pertanto le modifiche recenti potrebbero essere escluse.

Questa funzionalità è disponibile solo per le destinazioni di archiviazione BLOB di Azure e la relativa configurazione non può essere modificata.

Gli snapshot vengono eseguiti ogni otto ore, alle 0:00 UTC 0:00, 08:00 e 16:00.

La cache HPC di Azure archivia gli snapshot giornalieri, settimanali e mensili fino a quando non vengono sostituiti da nuovi. I limiti sono i seguenti:

* fino a 20 istantanee giornaliere
* fino a 8 istantanee settimanali
* fino a 3 istantanee mensili

Accedere agli snapshot `.snapshot` dalla directory nello spazio dei nomi della destinazione di archiviazione BLOB.
