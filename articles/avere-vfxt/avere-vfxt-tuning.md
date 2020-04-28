---
title: Ottimizzazione del cluster Avere vFXT - Azure
description: Panoramica delle prestazioni personalizzate per ottimizzare le prestazioni in Avere vFXT per Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 12/19/2019
ms.author: rohogue
ms.openlocfilehash: df20f050ff87fdb59a3e5cca373098240f8bfbb9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76152936"
---
# <a name="cluster-tuning"></a>Ottimizzazione del cluster

La maggior parte dei cluster vFXT può trarre vantaggio dalle impostazioni delle prestazioni personalizzate. Queste impostazioni consentono al cluster di ottenere la massima efficienza con il flusso di lavoro, il set di dati e gli strumenti specifici.

Questa personalizzazione dovrebbe essere eseguita con assistenza da un rappresentante del supporto, perché può comportare la configurazione di funzionalità non disponibili nel pannello di controllo.

Questa sezione descrive alcune delle operazioni di ottimizzazione personalizzate che è possibile eseguire.

## <a name="general-optimizations"></a>Ottimizzazioni generali

Queste modifiche possono essere consigliabili a seconda della qualità del set di dati o del tipo di flusso di lavoro.

* Se il carico di lavoro prevede un'intensa attività di scrittura, aumentare le dimensioni della cache di scrittura predefinite del 20%.
* Se il set di dati contiene molti file di piccole dimensioni, aumentare il limite del numero di file della cache del cluster.
* Se è necessario copiare o spostare dati tra due repository, modificare il numero di thread usati per spostare i dati:
  * Per aumentare la velocità, è possibile aumentare il numero di thread paralleli usati.
  * Se il volume di archiviazione back-end è in sovraccarico, potrebbe essere necessario ridurre il numero di thread paralleli usati.
* Se il cluster memorizza nella cache i dati per un core filer che usa elenchi di controllo di accesso NFSv4, abilitare la memorizzazione nella cache in modalità accesso per semplificare l'autorizzazione dei file per determinati client.

## <a name="cloud-nas-or-cloud-gateway-optimizations"></a>Ottimizzazioni del gateway cloud o del dispositivo NAS cloud

In uno scenario di gateway cloud o gateway, il cluster vFXT fornisce l'accesso in stile NAS a un contenitore cloud. Per sfruttare i vantaggi della velocità dei dati più elevata tra il cluster vFXT e l'archiviazione cloud, il rappresentante potrebbe consigliare di modificare le impostazioni per eseguire il push più aggressivo dei dati nel volume di archiviazione dalla cache. Ad esempio:

* Aumentare il numero di connessioni TCP tra il cluster e il contenitore di archiviazione

## <a name="cloud-bursting-or-hybrid-wan-optimizations"></a>Burst nel cloud o ottimizzazioni della rete WAN ibrida

In uno scenario di espansione nel cloud o in uno scenario di ottimizzazione WAN di archiviazione ibrida, il cluster vFXT fornisce l'integrazione tra il cloud e l'archiviazione hardware locale. Queste modifiche possono essere utili:

* Aumentare il numero di connessioni TCP consentite tra il cluster e il core filer
* Abilitare l'impostazione di ottimizzazione WAN per il core filer remoto. Questa impostazione può essere usata per un filer locale remoto o per un core filer cloud in un'altra area di Azure.
* Aumentare le dimensioni del buffer del socket TCP<sup>*</sup>
* Abilitare l'impostazione "sempre in diretta" per ridurre i file memorizzati nella cache ridondante<sup>*</sup>

<sup>*</sup>Queste rettifiche potrebbero non essere valide per tutti i sistemi, a seconda del carico di lavoro e delle esigenze di prestazioni.

## <a name="help-optimizing-your-avere-vfxt-for-azure"></a>Ottimizzazione di Avere vFXT per Azure

Per contattare il personale del supporto tecnico in merito a queste ottimizzazioni, attenersi alla procedura descritta in [ottenere assistenza con il sistema](avere-vfxt-open-ticket.md).
