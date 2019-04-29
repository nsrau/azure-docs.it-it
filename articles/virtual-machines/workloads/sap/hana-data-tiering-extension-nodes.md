---
title: Nodi di estensione e suddivisione in livelli dei dati per SAP HANA in Azure (istanze Large) | Microsoft Docs
description: Nodi di estensione e suddivisione in livelli dei dati per SAP HANA in Azure (istanze Large).
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/04/2018
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: b5868aaf29d6b57ea608763840a08f1991a982e8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60628592"
---
# <a name="use-sap-hana-data-tiering-and-extension-nodes"></a>Usare nodi di estensione e suddivisione in livelli dei dati di SAP HANA

SAP supporta un modello di suddivisione in livelli dei dati per SAP BW di diverse versioni di SAP NetWeaver e SAP BW/4HANA. Per altre informazioni sul modello di suddivisione in livelli dei dati, vedere il documento di SAP [SAP BW/4HANA and SAP BW on HANA with SAP HANA extension nodes](https://www.sap.com/documents/2017/05/ac051285-bc7c-0010-82c7-eda71af511fa.html#) (SAP BW/4HANA e SAP BW su HANA con nodi di estensione di SAP HANA).
Con le istanze Large di HANA è possibile usare la configurazione option-1 dei nodi di estensione di SAP HANA, come descritto in dettaglio nei documenti delle domande frequenti e del blog di SAP. Le configurazioni Option-2 possono essere configurate con le SKU delle istanze Large HANA seguenti: S72m, S192, S192m, S384 e S384m. 

Quando si esamina la documentazione, il vantaggio può non essere immediatamente visibile. Se tuttavia si leggono le linee guida del dimensionamento di SAP, il vantaggio offerto dai nodi di estensione option-1 e option-2 di SAP HANA è evidente. Ecco alcuni esempi:

- Le linee guida del dimensionamento di SAP HANA richiedono in genere il doppio del volume di dati per la memoria. Quando si esegue l'istanza di SAP HANA con hot data, solo il 50% o una percentuale inferiore della memoria è occupata dai dati. Idealmente, il resto della memoria viene mantenuto per il funzionamento di SAP HANA.
- Questo significa che in un'unità S192 di istanza Large di HANA con 2 TB di memoria che esegue un database SAP BW, il volume di dati è di 1 TB.
- Se si usa un nodo di estensione di SAP HANA aggiuntivo di tipo option-1, anche uno SKU S192 di istanze Large di HANA, si ottiene una capacità aggiuntiva di 2 TB per il volume di dati. Nella configurazione option-2 si ottengono 4 TB aggiuntivi per il volume di warm data. Rispetto al nodo di tipo hot, nel nodo di estensione di tipo warm può essere usata la capacità di memoria completa per l'archiviazione dei dati per la configurazione option-1. Una capacità doppia di memoria può essere usata per il volume di dati nella configurazione del nodo di estensione option-2 di SAP HANA.
- Si ottengono quindi una capacità di 3 TB per i dati, con un rapporto hot/warm di 1:2, nella configurazione del nodo di estensione option-1 e una capacità di 5 TB per i dati, con un rapporto hot/warm di 1:4, nella configurazione del nodo di estensione option-2.

Maggiore sarà il volume di dati rispetto alla memoria, più alte saranno le probabilità che i warm data desiderati si trovino in una risorsa di archiviazione su disco.

**Passaggi successivi**
- Fare riferimento all'[architettura di SAP HANA (istanze Large) in Azure](hana-architecture.md)