---
title: Configurare le impostazioni della cache HPC di Azure
description: Viene illustrato come configurare impostazioni aggiuntive per la cache, ad esempio MTU e no-root-squash, e come accedere agli snapshot rapidi dalle destinazioni di archiviazione BLOB di Azure.
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 05/06/2020
ms.author: v-erkel
ms.openlocfilehash: 88aea7e58aacd9a630771948c6dbc6ed5712a674
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85505308"
---
# <a name="configure-additional-azure-hpc-cache-settings"></a>Configurare altre impostazioni della cache HPC di Azure

La pagina di **configurazione** nel portale di Azure dispone di opzioni per la personalizzazione di diverse impostazioni. La maggior parte degli utenti non è necessario modificare queste impostazioni dai valori predefiniti.

Questo articolo descrive anche come usare la funzionalità snapshot per le destinazioni di archiviazione BLOB di Azure. La funzionalità snapshot non contiene impostazioni configurabili.

Per visualizzare le impostazioni, aprire la pagina di **configurazione** della cache nel portale di Azure.

![screenshot della pagina di configurazione in portale di Azure](media/configuration.png)

> [!TIP]
> Il [video sulla gestione della cache HPC di Azure](https://azure.microsoft.com/resources/videos/managing-hpc-cache/) Mostra la pagina di configurazione e le relative impostazioni.

## <a name="adjust-mtu-value"></a>Regolazione del valore MTU
<!-- linked from troubleshoot-nas article -->

È possibile selezionare le dimensioni massime dell'unità di trasmissione per la cache usando il menu a discesa con etichetta **dimensioni MTU**.

Il valore predefinito è 1500 byte, ma è possibile modificarlo in 1400.

> [!NOTE]
> Se si riducono le dimensioni MTU della cache, assicurarsi che i client e i sistemi di archiviazione che comunicano con la cache abbiano la stessa impostazione MTU o un valore inferiore.

L'abbassamento del valore MTU della cache può essere utile per aggirare le restrizioni relative alle dimensioni dei pacchetti nel resto della rete della cache. Alcune VPN, ad esempio, non possono trasmettere correttamente i pacchetti a 1500 byte a dimensione intera. La riduzione delle dimensioni dei pacchetti inviati tramite la VPN potrebbe eliminare il problema. Si noti tuttavia che un'impostazione di MTU inferiore della cache significa che tutti gli altri componenti che comunicano con la cache, inclusi i client e i sistemi di archiviazione, devono avere anche un'impostazione MTU inferiore per evitare problemi di comunicazione.

Se non si desidera modificare le impostazioni MTU su altri componenti di sistema, è consigliabile non abbassare l'impostazione MTU della cache. Sono disponibili altre soluzioni per aggirare le restrizioni relative alle dimensioni del pacchetto VPN. Per altre informazioni sulla diagnosi e la risoluzione di questo problema, vedere [modificare le restrizioni sulle dimensioni del pacchetto VPN](troubleshoot-nas.md#adjust-vpn-packet-size-restrictions) nell'articolo sulla risoluzione dei problemi di NAS.

Per altre informazioni sulle impostazioni MTU nelle reti virtuali di Azure, vedere [ottimizzazione delle prestazioni TCP/IP per le macchine virtuali di Azure](../virtual-network/virtual-network-tcpip-performance-tuning.md).

## <a name="configure-root-squash"></a>Configura squash radice
<!-- linked from troubleshoot -->

L'impostazione **Abilita squash radice** controlla il modo in cui cache HPC di Azure considera le richieste provenienti dall'utente root nei computer client.

Quando è abilitata la funzionalità di squash radice, viene automaticamente eseguito il mapping degli utenti radice di un client all'utente "Nobody" quando inviano richieste tramite la cache HPC di Azure. Impedisce inoltre alle richieste client di utilizzare i bit di autorizzazione set-UID.

Se la zucca radice è disabilitata, una richiesta proveniente dall'utente radice del client (UID 0) viene passata a un sistema di archiviazione NFS back-end come radice. Questa configurazione potrebbe consentire l'accesso ai file non appropriato.

L'impostazione dello squash radice nella cache può compensare l'impostazione necessaria ``no_root_squash`` nei sistemi NAS usati come destinazioni di archiviazione. Per ulteriori informazioni sui [prerequisiti per l'archiviazione NFS](hpc-cache-prereqs.md#nfs-storage-requirements), vedere. Può anche migliorare la sicurezza quando viene usata con le destinazioni di archiviazione BLOB di Azure.

L'impostazione predefinita è **Sì**. (Le cache create prima del 2020 aprile potrebbero avere l'impostazione predefinita **No**).

## <a name="view-snapshots-for-blob-storage-targets"></a>Visualizzare gli snapshot per le destinazioni di archiviazione BLOB

Cache HPC di Azure Salva automaticamente gli snapshot di archiviazione per le destinazioni di archiviazione BLOB di Azure. Gli snapshot forniscono un punto di riferimento rapido per il contenuto del contenitore di archiviazione back-end.

Gli snapshot non sono sostitutivi dei backup dei dati e non includono informazioni sullo stato dei dati memorizzati nella cache.

> [!NOTE]
> Questa funzionalità snapshot è diversa dalla funzionalità di snapshot inclusa nel software di archiviazione NetApp o Isilon. Queste implementazioni di snapshot scaricano le modifiche dalla cache al sistema di archiviazione back-end prima di acquisire lo snapshot.
>
> Per maggiore efficienza, lo snapshot della cache HPC di Azure non Scarica prima le modifiche e registra solo i dati che sono stati scritti nel contenitore BLOB. Questo snapshot non rappresenta lo stato dei dati memorizzati nella cache, pertanto potrebbe non includere le modifiche recenti.

Questa funzionalità è disponibile solo per le destinazioni di archiviazione BLOB di Azure e la relativa configurazione non può essere modificata.

Gli snapshot vengono effettuati ogni otto ore, alle ore UTC 0:00, 08:00 e 16:00.

Cache HPC di Azure archivia snapshot giornalieri, settimanali e mensili fino a quando non vengono sostituiti con quelli nuovi. I limiti sono i seguenti:

* fino a 20 snapshot giornalieri
* fino a 8 snapshot settimanali
* fino a 3 snapshot mensili

Accedere agli snapshot dalla `.snapshot` Directory nello spazio dei nomi della destinazione di archiviazione BLOB.
