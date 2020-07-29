---
title: Mostra i processi di azcopy | Microsoft Docs
description: Questo articolo contiene informazioni di riferimento per il comando azcopy Jobs show.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 07/24/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 7ffb16936ec96f30ad7d8e05babbadb85e01a71e
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87281940"
---
# <a name="azcopy-jobs-show"></a>azcopy jobs show

Mostra informazioni dettagliate per l'ID del processo specificato.

## <a name="synopsis"></a>Riepilogo

Se viene fornito solo l'ID processo senza flag, viene restituito il riepilogo dello stato di avanzamento del processo.

I conteggi dei byte e la percentuale di completamento visualizzati quando si esegue questo comando riflettono solo i file completati nel processo. Non riflettono i file parzialmente completati.

Se il `with-status` flag è impostato, verrà visualizzato l'elenco di trasferimenti nel processo con il valore specificato.

```azcopy
azcopy jobs show [jobID] [flags]
```

## <a name="related-conceptual-articles"></a>Articoli concettuali correlati

- [Introduzione ad AzCopy](storage-use-azcopy-v10.md)
- [Trasferire i dati con AzCopy e l'archiviazione BLOB](storage-use-azcopy-blobs.md)
- [Trasferire dati con AzCopy e l'archivio file](storage-use-azcopy-files.md)
- [Configurare, ottimizzare e risolvere i problemi di AzCopy](storage-use-azcopy-configure.md)

## <a name="options"></a>Opzioni

|Opzione|Descrizione|
|--|--|
|-h, --help|Visualizza il contenuto della Guida per il comando Mostra.|
|--with-status stringa|Elencare solo i trasferimenti del processo con questo stato, i valori disponibili: Started, Success, failed|

## <a name="options-inherited-from-parent-commands"></a>Opzioni ereditate dai comandi padre

|Opzione|Descrizione|
|---|---|
|--Cap-Mbps float|Viene riversata la velocità di trasferimento, in megabit al secondo. Una velocità effettiva momentanea potrebbe variare leggermente rispetto al limite. Se questa opzione è impostata su zero o viene omessa, la velocità effettiva non è limitata.|
|--output-tipo stringa|Formato dell'output del comando. Le scelte includono: text, JSON. Il valore predefinito è "Text".|
|--trusted-Microsoft-suffissi stringa   |Specifica i suffissi di dominio aggiuntivi in cui è possibile inviare i token di accesso Azure Active Directory.  Il valore predefinito è'*. Core.Windows.NET;*. core.chinacloudapi.cn; *. Core.cloudapi.de;*. core.usgovcloudapi.net ". Tutti gli elencati qui vengono aggiunti al valore predefinito. Per la sicurezza, è consigliabile inserire qui solo Microsoft Azure domini. Separare più voci con un punto e virgola.|

## <a name="see-also"></a>Vedere anche

- [azcopy jobs](storage-ref-azcopy-jobs.md)
