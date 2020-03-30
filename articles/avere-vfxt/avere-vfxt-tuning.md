---
title: Ottimizzazione del cluster Avere vFXT - Azure
description: Panoramica delle prestazioni personalizzate per ottimizzare le prestazioni in Avere vFXT per Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 12/19/2019
ms.author: rohogue
ms.openlocfilehash: df20f050ff87fdb59a3e5cca373098240f8bfbb9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76152936"
---
# <a name="cluster-tuning"></a>Ottimizzazione del cluster

La maggior parte dei cluster vFXT può trarre vantaggio dalle impostazioni delle prestazioni personalizzate. Queste impostazioni consentono al cluster di ottenere la massima efficienza con il flusso di lavoro, il set di dati e gli strumenti specifici.

Questa personalizzazione deve essere eseguita con l'aiuto di un rappresentante del supporto, perché può comportare la configurazione di funzionalità che non sono disponibili dal Pannello di controllo Di Avere.

In questa sezione vengono descritte alcune delle ottimizzazioni personalizzate che è possibile eseguire.

## <a name="general-optimizations"></a>Ottimizzazioni generali

Queste modifiche possono essere consigliabili a seconda della qualità del set di dati o del tipo di flusso di lavoro.

* Se il carico di lavoro prevede un'intensa attività di scrittura, aumentare le dimensioni della cache di scrittura predefinite del 20%.
* Se il set di dati contiene molti file di piccole dimensioni, aumentare il limite del numero di file della cache del cluster.
* Se è necessario copiare o spostare dati tra due repository, modificare il numero di thread usati per spostare i dati:
  * Per aumentare la velocità, è possibile aumentare il numero di thread paralleli usati.
  * Se il volume di archiviazione back-end è in sovraccarico, potrebbe essere necessario ridurre il numero di thread paralleli usati.
* Se il cluster memorizza nella cache i dati per un core filer che usa elenchi di controllo di accesso NFSv4, abilitare la memorizzazione nella cache in modalità accesso per semplificare l'autorizzazione dei file per determinati client.

## <a name="cloud-nas-or-cloud-gateway-optimizations"></a>Ottimizzazioni del gateway cloud o del dispositivo NAS cloud

In uno scenario NAS o gateway cloud, il cluster vFXT fornisce l'accesso in stile NAS a un contenitore cloud. Per sfruttare i vantaggi di velocità dei dati più elevate tra il cluster vFXT e l'archiviazione cloud, il rappresentante potrebbe consigliare di modificare le impostazioni per eseguire il push più aggressivo dei dati nel volume di archiviazione dalla cache. Ad esempio:

* Aumentare il numero di connessioni TCP tra il cluster e il contenitore di archiviazione

## <a name="cloud-bursting-or-hybrid-wan-optimizations"></a>Burst nel cloud o ottimizzazioni della rete WAN ibrida

In uno scenario di cloud bursting o di ottimizzazione WAN di archiviazione ibrida, il cluster vFXT fornisce l'integrazione tra il cloud e l'archiviazione hardware locale. Queste modifiche possono essere utili:

* Aumentare il numero di connessioni TCP consentite tra il cluster e il core filer
* Abilitare l'impostazione di ottimizzazione WAN per il core filer remoto. Questa impostazione può essere usata per un filer locale remoto o per un core filer cloud in un'altra area di Azure.
* Aumentare la dimensione del buffer del socket TCP<sup>*</sup>
* Attivare l'impostazione "sempre avanti" per ridurre i file memorizzati in modalità ridondante<sup>*</sup>

<sup>*</sup>Queste regolazioni potrebbero non essere applicabili a tutti i sistemi, a seconda del carico di lavoro e delle esigenze di prestazioni.

## <a name="help-optimizing-your-avere-vfxt-for-azure"></a>Ottimizzazione di Avere vFXT per Azure

Per contattare il personale di supporto per queste ottimizzazioni, utilizzare la procedura descritta in [Ottenere assistenza per il sistema.](avere-vfxt-open-ticket.md)
