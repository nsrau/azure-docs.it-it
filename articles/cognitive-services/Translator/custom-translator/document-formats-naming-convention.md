---
title: Formati dei documenti e convenzioni di denominazione - Custom Translator
titleSuffix: Azure Cognitive Services
description: Guida ai formati dei documenti e alle convenzioni di denominazione in Custom Translator. Questo concetto consente di gestire meglio i nomi dei documenti e di evitare conflitti di denominazione.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: 41b15cc998a7bacd033ef2fe083fc99f1bff0286
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/29/2019
ms.locfileid: "68595862"
---
# <a name="document-formats-and-naming-convention-guidance"></a>Guida ai formati dei documenti e alle convenzioni di denominazione

I file usati per la traduzione personalizzata devono includere **almeno** quattro caratteri.

Questa tabella include tutti i formati di file supportati che è possibile usare per creare il proprio sistema di traduzione:

| Formato            | Estensioni   | Descrizione                                                                                                                                                                                                                                                                    |
|-------------------|--------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| XLIFF             | XLF, XLIFF | Formato di documento parallelo, esportazione di sistemi di memoria di traduzione. Le lingue usate vengono definite all'interno del file.                                                                                                                                                              |
| TMX               | TMX         | Formato di documento parallelo, esportazione di sistemi di memoria di traduzione. Le lingue usate vengono definite all'interno del file.                                                                                                                                                              |
| ZIP               | ZIP         | ZIP è un formato di file di archivio.                                                                                                                                                                                                        |
| Locstudio         | LCL         | Formato Microsoft per i documenti paralleli                                                                                                                                                                                                                                      |
| Microsoft Word    | DOCX        | Documento di Microsoft Word                                                                                                                                                                                                                                                        |
| Adobe Acrobat     | PDF         | Documento portatile di Adobe Acrobat                                                                                                                                                                                                                                                |
| HTML              | HTML, HTM  | Documento HTML                                                                                                                                                                                                                                                                  |
| File di testo         | TXT         | File di testo con codifica UTF-16 o UTF-8. Il nome file non deve contenere caratteri giapponesi.                                                                                                                                                                                        |
| File di testo allineato | ALIGN       | `.ALIGN` è un'estensione speciale che è possibile usare se si è certi che le frasi nella coppia di documenti siano perfettamente allineate. Se si fornisce un file `.ALIGN`, Custom Translator non allinea le frasi automaticamente. |
| File di Excel        | XLSX        | File di Excel (2013 o versione successiva). La prima riga del foglio di calcolo deve essere il codice della lingua.                                                                                                                                                                                                                                                      |

## <a name="dictionary-formats"></a>Formati di dizionario

Per i dizionari, il convertitore personalizzato supporta tutti i formati di file supportati per i set di training. Se si utilizza un dizionario di Excel, la prima riga/riga del foglio di calcolo deve essere costituita da codici di lingua.

## <a name="zip-file-formats"></a>Formati di file ZIP

I documenti possono essere raggruppati in un unico file ZIP e caricati. Custom Translator supporta i formati di file ZIP (ZIP, GZ e TGZ).

Ogni documento nel file zip con estensione TXT, HTML, HTM, PDF, DOCX, ALIGN deve seguire questa convenzione di denominazione:

{nome documento}\_{codice lingua} dove {nome documento} è il nome del documento e {codice lingua} è l'ID lingua ISO (due caratteri), indicante che il documento contiene frasi in tale lingua. Deve essere presente un carattere di sottolineatura (_) prima del codice della lingua.

Ad esempio, per caricare due documenti paralleli all'interno di un file ZIP per un sistema di traduzione dall'inglese allo spagnolo, i file dovrebbero essere denominati "dati_en" e "dati_es".

I file di memoria di traduzione (con estensione TMX, XLF, XLIFF, LCL, XLSX) non devono seguire la convenzione di denominazione del linguaggio specifico.  

## <a name="next-steps"></a>Passaggi successivi

- Informazioni sul [progetto](workspace-and-project.md#what-is-a-custom-translator-project) per creare e gestire i documenti.
