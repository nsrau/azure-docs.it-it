---
title: 'Stack di archiviazione Azure: Considerazioni e le differenze'
description: Comprendere le differenze tra lo Stack di archiviazione di Azure e l'archiviazione di Azure, insieme a considerazioni sulla distribuzione di Azure Stack.
services: azure-stack
documentationcenter: 
author: xiaofmao
manager: 
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 9/25/2017
ms.author: xiaofmao
ms.openlocfilehash: 4d6fb44fd6fd2261059ca45093d8b49345adfa74
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2017
---
# <a name="azure-stack-storage-differences-and-considerations"></a>Stack di archiviazione Azure: Considerazioni e le differenze

*Si applica a: Azure Stack integrate di sistemi Azure Stack Development Kit*

Stack di archiviazione di Azure è il set di servizi di archiviazione cloud in Microsoft Azure Stack. Stack di archiviazione di Azure fornisce blob, tabelle, code e funzionalità di gestione di account con semantica coerente con Azure.

In questo articolo riepiloga le differenze di Stack di archiviazione di Azure note da archiviazione di Azure. Inoltre, riepiloga altre considerazioni da tenere presenti quando si distribuisce Azure Stack. Per ulteriori informazioni sulle differenze generali tra Stack di Azure e Azure, vedere il [considerazioni chiave](azure-stack-considerations.md) argomento.

## <a name="cheat-sheet-storage-differences"></a>Schede di riferimento rapido: differenze di archiviazione

| Funzionalità | Azure (globale) | Azure Stack |
| --- | --- | --- |
|Archiviazione file|Basato su cloud di condivisioni file SMB è supportate|Non è ancora supportato
|Dati a crittografia rest|crittografia AES a 256 bit|Non è ancora supportato
|Tipo di account di archiviazione|Account di archiviazione Blob di Azure e di utilizzo generale|Utilizzo generale solo
|Opzioni di replica|Archiviazione con ridondanza locale, l'archiviazione con ridondanza geografica, l'archiviazione con ridondanza geografica e accesso in lettura e archiviazione con ridondanza della zona|Archiviazione con ridondanza locale
|Archiviazione Premium|Supporto completo|Può essere eseguito il provisioning, ma nessun limite delle prestazioni o garanzia
|Dischi gestiti|Premium e standard supportati|Non è ancora supportato
|Nome del BLOB|1.024 caratteri (2.048 byte)|880 caratteri (1,760 byte)
|Dimensioni massime blob di blocco|4,75 TB (100 MB X 50.000 blocchi)|50.000 x 4 MB (circa 195 GB)
|Copia snapshot blob di pagina|Backup Azure non gestito macchina virtuale i dischi collegati a una macchina virtuale in esecuzione supportata|Non è ancora supportato
|Copia dello snapshot incrementale blob di pagina|Premium e BLOB di pagine di Azure standard supportati|Non è ancora supportato
|Dimensioni massime blob di pagina|8 TB|1 TB
|Dimensioni di pagina pagina blob|512 byte|4 KB
|Dimensione di chiave di riga e chiave di partizione di tabella|1.024 caratteri (2.048 byte)|400 caratteri (800 byte)

### <a name="metrics"></a>Metrica
Esistono inoltre alcune differenze con le metriche di archiviazione:
* I dati delle transazioni di metriche di archiviazione non viene applicata alcuna distinzione della larghezza di banda di rete interna o esterna.
* I dati delle transazioni di metriche di archiviazione non include macchina virtuale di accedere ai dischi installati.

## <a name="api-version"></a>Versione dell'API
Con Stack di archiviazione di Azure sono supportate le seguenti versioni:

* Servizi dati di archiviazione di Azure: [versione 2015-04-05 dell'API REST](https://docs.microsoft.com/rest/api/storageservices/Version-2015-04-05?redirectedfrom=MSDN)
* Servizi di archiviazione Azure: [2015-05-01-preview 2015-06-15 e 2016-01-01](https://docs.microsoft.com/rest/api/storagerp/?redirectedfrom=MSDN) 

## <a name="next-steps"></a>Passaggi successivi

* [Introduzione a strumenti di sviluppo dello Stack di archiviazione di Azure](azure-stack-storage-dev.md)
* [Introduzione all'archiviazione di Azure Stack](azure-stack-storage-overview.md)

