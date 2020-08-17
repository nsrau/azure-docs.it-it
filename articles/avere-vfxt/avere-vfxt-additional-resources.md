---
title: Collegamenti aggiuntivi su Avere vFXT for Azure
description: Usare queste risorse per ottenere altre informazioni su vFXT per Azure, inclusa la documentazione sui cluster e la documentazione sulla gestione di vFXT.
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 01/13/2020
ms.author: rohogue
ms.openlocfilehash: 6f9a9f8e6d2a279871ded3dff614be99dd2901df
ms.sourcegitcommit: 2bab7c1cd1792ec389a488c6190e4d90f8ca503b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/17/2020
ms.locfileid: "88271091"
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
