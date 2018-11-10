---
title: Collegamenti aggiuntivi su Avere vFXT for Azure
description: Collegamenti a informazioni aggiuntive su Avere vFXT per Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: v-erkell
ms.openlocfilehash: afba39d1af700650cfbf7226dff36729a76a0bda
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/31/2018
ms.locfileid: "50669788"
---
# <a name="additional-documentation"></a>Documentazione aggiuntiva

Questo articolo contiene collegamenti a documentazione aggiuntiva sull'interfaccia di gestione del pannello di controllo di Avere e argomenti correlati. 

## <a name="avere-cluster-documentation"></a>Documentazione sul cluster Avere

Altri documenti sul cluster Avere sono disponibili nel sito Web all'indirizzo <http://library.averesystems.com/>. Questi documenti possono essere utili per comprendere le funzionalità del cluster e come configurare le relative impostazioni. 

* La [guida alla creazione di cluster FXT](<http://library.averesystems.com/#fxt_cluster>) è pensata per i cluster costituiti da nodi hardware fisici, ma alcune informazioni nel documento sono pertinenti anche per i cluster vFXT. In particolare, i nuovi amministratori di cluster vFXT possono trarre vantaggio dalla lettura di queste sezioni:
  * [Customizing Support and Monitoring Settings](<http://library.averesystems.com/create_cluster/4_8/html/config_support.html#config-support>) (Personalizzazione delle impostazioni di monitoraggio e supporto) illustrato come personalizzare le impostazioni di caricamento del supporto e abilitare il monitoraggio remoto. 
  * [Configuring VServers and Global Namespace](<http://library.averesystems.com/create_cluster/4_8/html/config_vserver.html#config-vserver>) (Configurazione di vserver e spazio dei nomi globale) contiene informazioni sulla creazione di uno spazio dei nomi lato client.
  * [Configuring DNS for the Avere cluster](<http://library.averesystems.com/create_cluster/4_8/html/config_network.html#dns-overview>) (Configurazione DNS per il cluster Avere) illustra come configurare il DNS round robin.
  * [Adding Backend Storage](<http://library.averesystems.com/create_cluster/4_8/html/config_core_filer.html#add-core-filer>) (Aggiunta di risorse di archiviazione back-end) illustra come aggiungere core filer.

* La [guida alla configurazione Cluster](<http://library.averesystems.com/#operations>) contiene informazioni di riferimento complete sulle impostazioni e le opzioni per un cluster Avere. Un cluster vFXT usa un subset di queste opzioni, ma la maggior parte delle pagine di configurazione è comunque applicabile.

* La [guida dal dashboard](<http://library.averesystems.com/#operations>) spiega come usare le funzionalità di monitoraggio cluster del pannello di controllo di Avere.

## <a name="vfxt-creation-and-management-documentation"></a>Documentazione sulla creazione e la gestione di vFXT

Una guida completa sull'uso di vfxt.py, la creazione di cluster cloud e l'utilità di gestione è disponibile all'indirizzo <https://github.com/AvereSystems/vFXT.py/blob/master/docs/README.md>.  
