---
title: Collegamenti aggiuntivi su Avere vFXT for Azure
description: Collegamenti a informazioni aggiuntive su Avere vFXT per Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 01/13/2020
ms.author: rohogue
ms.openlocfilehash: 78e0500057baf407f1d26afe29d9df1e525cbf3d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "76153769"
---
# <a name="additional-documentation"></a>Documentazione aggiuntiva

Questo articolo contiene collegamenti a documentazione aggiuntiva sull'interfaccia di gestione del pannello di controllo di Avere e argomenti correlati.

## <a name="avere-cluster-documentation"></a>Documentazione sul cluster Avere

Altri documenti sul cluster Avere sono disponibili nel sito Web all'indirizzo <https://azure.github.io/Avere/>. Questi documenti possono essere utili per comprendere le funzionalità del cluster e come configurare le relative impostazioni.

* La [guida alla creazione di cluster FXT](<https://azure.github.io/Avere/#fxt_cluster>) è pensata per i cluster costituiti da nodi hardware fisici, ma alcune informazioni nel documento sono pertinenti anche per i cluster vFXT. In particolare, i nuovi amministratori di cluster vFXT possono trarre vantaggio dalla lettura di queste sezioni:
  * [Customizing Support and Monitoring Settings](<https://azure.github.io/Avere/legacy/create_cluster/4_8/html/config_support.html#config-support>) (Personalizzazione delle impostazioni di monitoraggio e supporto) illustrato come personalizzare le impostazioni di caricamento del supporto e abilitare il monitoraggio remoto.
  * [Configuring VServers and Global Namespace](<https://azure.github.io/Avere/legacy/create_cluster/4_8/html/config_vserver.html#config-vserver>) (Configurazione di vserver e spazio dei nomi globale) contiene informazioni sulla creazione di uno spazio dei nomi lato client.
  * [Configuring DNS for the Avere cluster](<https://azure.github.io/Avere/legacy/create_cluster/4_8/html/config_network.html#dns-overview>) (Configurazione DNS per il cluster Avere) illustra come configurare il DNS round robin.
  * [Aggiunta di documenti di archiviazione back-end](<https://azure.github.io/Avere/legacy/create_cluster/4_8/html/config_core_filer.html#add-core-filer>) come aggiungere i file di base.

* La [guida alla configurazione Cluster](<https://azure.github.io/Avere/#operations>) contiene informazioni di riferimento complete sulle impostazioni e le opzioni per un cluster Avere. Un cluster vFXT usa un subset di queste opzioni, ma la maggior parte delle pagine di configurazione è comunque applicabile.

* La [guida dal dashboard](<https://azure.github.io/Avere/#operations>) spiega come usare le funzionalità di monitoraggio cluster del pannello di controllo di Avere.

## <a name="vfxt-creation-and-management-documentation"></a>Documentazione sulla creazione e la gestione di vFXT

Una guida completa per l'uso di vfxt.py, un'utilità di creazione e gestione di cluster cloud basata su script, è disponibile in GitHub: [Gestione cluster cloud con vfxt.py](https://github.com/Azure/AvereSDK/blob/master/docs/README.md).
