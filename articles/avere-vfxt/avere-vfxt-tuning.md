---
title: Ottimizzazione del cluster Avere vFXT - Azure
description: Panoramica delle prestazioni personalizzate per ottimizzare le prestazioni in Avere vFXT per Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: v-erkell
ms.openlocfilehash: f5e780dcab20befe19ca34020908eee93c290516
ms.sourcegitcommit: d4f728095cf52b109b3117be9059809c12b69e32
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/10/2019
ms.locfileid: "54197438"
---
# <a name="cluster-tuning"></a>Ottimizzazione del cluster


La maggior parte dei cluster vFXT può trarre vantaggio dalle impostazioni delle prestazioni personalizzate. Queste impostazioni consentono al cluster di ottenere la massima efficienza con il flusso di lavoro, il set di dati e gli strumenti specifici. 

Questa personalizzazione dovrebbe essere eseguita con un rappresentante del supporto tecnico, perché in genere è necessario configurare funzionalità non disponibili nel pannello di controllo di Avere.

Questa sezione illustra alcune delle ottimizzazioni personalizzate che è possibile eseguire.

<!-- 
[ xxx keep or not? \/ research this xxx ]

> [!TIP]
> The VDBench utility can be helpful in generating I/O workloads to test a vFXT cluster. Read [Measuring vFXT Performance](vdbench.md) to learn more.

-->

## <a name="general-optimizations"></a>Ottimizzazioni generali

Queste modifiche possono essere consigliabili a seconda della qualità del set di dati o del tipo di flusso di lavoro.

* Se il carico di lavoro prevede un'intensa attività di scrittura, aumentare le dimensioni della cache di scrittura predefinite del 20%. 
* Se il set di dati contiene molti file di piccole dimensioni, aumentare il limite del numero di file della cache del cluster. 
* Se è necessario copiare o spostare dati tra due repository, modificare il numero di thread usati per spostare i dati: 
  * Per aumentare la velocità, è possibile aumentare il numero di thread paralleli usati.
  * Se il volume di archiviazione back-end è in sovraccarico, potrebbe essere necessario ridurre il numero di thread paralleli usati.
* Se il cluster memorizza nella cache i dati per un core filer che usa elenchi di controllo di accesso NFSv4, abilitare la memorizzazione nella cache in modalità accesso per semplificare l'autorizzazione dei file per determinati client.

## <a name="cloud-nas-or-cloud-gateway-optimizations"></a>Ottimizzazioni del gateway cloud o del dispositivo NAS cloud

Per sfruttare le maggiori velocità dei dati tra il cluster vFXT e la risorsa di archiviazione cloud in uno scenario gateway o NAS cloud (dove il cluster vFXT fornisce un accesso di tipo NAS a un contenitore), il rappresentante potrebbe consigliare di modificare impostazioni come le seguenti per eseguire un push dei dati più aggressivo nel volume di archiviazione dalla cache:

* Aumentare il numero di connessioni TCP tra il cluster e il contenitore di archiviazione
* Diminuire il valore di timeout REST per la comunicazione tra il cluster e la risorsa di archiviazione per ripetere più rapidamente le operazioni di scrittura se non riescono immediatamente  
* Aumentare le dimensioni del segmento in modo che ogni segmento di scrittura back-end trasferisca un blocco di dati da 8 MB invece che da 1 MB

## <a name="cloud-bursting-or-hybrid-wan-optimizations"></a>Burst nel cloud o ottimizzazioni della rete WAN ibrida

In uno scenario di burst nel cloud o di ottimizzazione della rete WAN ibrida (dove l'integrazione del cluster vFXT fornisce l'integrazione tra il cloud e la risorsa di archiviazione hardware locale) possono essere utili queste modifiche:

* Aumentare il numero di connessioni TCP consentite tra il cluster e il core filer
* Abilitare l'impostazione di ottimizzazione WAN per il core filer remoto. Questa impostazione può essere usata per un filer locale remoto o per un core filer cloud in un'altra area di Azure.
* Aumentare le dimensioni del buffer socket TCP (a seconda del carico di lavoro e delle esigenze in termini di prestazioni)
* Abilitare l'impostazione "always forward" (Inoltra sempre) per ridurre i file memorizzati nella cache in modo ridondante (a seconda del carico di lavoro e delle esigenze in termini di prestazioni)

## <a name="help-optimizing-your-avere-vfxt-for-azure"></a>Ottimizzazione di Avere vFXT per Azure

Usare la procedura descritta in [Ottenere assistenza per il sistema](avere-vfxt-open-ticket.md) per contattare personale di supporto per informazioni su queste ottimizzazioni. 