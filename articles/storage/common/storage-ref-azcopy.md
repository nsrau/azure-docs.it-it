---
title: azcopy | Microsoft Docs
description: Questo articolo contiene informazioni di riferimento per il comando azcopy.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 984d0c570c6c0d5048d58377f113319157411244
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/17/2019
ms.locfileid: "72513391"
---
# <a name="azcopy"></a>azcopy

AzCopy è uno strumento da riga di comando che sposta i dati all'interno e all'esterno di archiviazione di Azure.

## <a name="synopsis"></a>Sinossi

Il formato generale dei comandi è: `azcopy [command] [arguments] --[flag-name]=[flag-value]`.

Per segnalare problemi o per ulteriori informazioni sullo strumento, vedere [https://github.com/Azure/azure-storage-azcopy](https://github.com/Azure/azure-storage-azcopy).

## <a name="options"></a>Opzioni

**--Cap-Mbps UInt32**   Viene riversata la velocità di trasferimento, in megabit al secondo. Una velocità effettiva momentanea potrebbe variare leggermente rispetto al limite. Se questa opzione è impostata su zero o viene omessa, la velocità effettiva non è limitata.

**-h,--Help** Guida per azcopy
      
**--output-tipo**  Formato dell'output del comando. Le scelte includono: text, JSON. Il valore predefinito è "Text". (impostazione predefinita "testo")

## <a name="see-also"></a>Vedi anche

- [Introduzione ad AzCopy](storage-use-azcopy-v10.md)
- [copia di azcopy](storage-ref-azcopy-copy.md)
- [documento azcopy](storage-ref-azcopy-doc.md)
- [azcopy ENV](storage-ref-azcopy-env.md)
- [processi azcopy](storage-ref-azcopy-jobs.md)
- [elenco azcopy](storage-ref-azcopy-list.md)
- [accesso azcopy](storage-ref-azcopy-login.md)
- [disconnessione azcopy](storage-ref-azcopy-logout.md)
- [azcopy make](storage-ref-azcopy-make.md)
- [azcopy rimuovere](storage-ref-azcopy-remove.md)
- [sincronizzazione azcopy](storage-ref-azcopy-sync.md)
