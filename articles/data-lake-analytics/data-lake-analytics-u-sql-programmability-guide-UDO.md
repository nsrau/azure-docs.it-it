---
title: Guida alla programmabilità di U-SQL UDO per Azure Data Lake
description: Informazioni sulle Azure Data Lake Analytics di programmabilità di U-SQL UDO per consentire la creazione di uno script USQL valido.
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 06/30/2017
ms.openlocfilehash: 02360c68e5e830ceee69075fd5532b126d85bec2
ms.sourcegitcommit: df66dff4e34a0b7780cba503bb141d6b72335a96
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/02/2020
ms.locfileid: "96512578"
---
# <a name="u-sql-user-defined-objects-overview"></a>Panoramica degli oggetti definiti dall'utente U-SQL


## <a name="u-sql-user-defined-objects-udo"></a>U-SQL: oggetti definiti dall'utente: UDO
U-SQL consente di definire oggetti di programmabilità personalizzati, denominati oggetti definiti dall'utente (UDO).

Di seguito è riportato un elenco degli oggetti definiti dall'utente in U-SQL:

* Estrattori definiti dall'utente
    * Estrazione riga per riga
    * Vengono usati per implementare l'estrazione di dati da file strutturati personalizzati

* Outputter definiti dall'utente
    * Output riga per riga
    * Vengono usati per tipi di dati di output o formati di file personalizzati

* Elaboratori definiti dall'utente
    * Viene usato per richiedere una riga e produrre una riga
    * Vengono usati per ridurre il numero di colonne o produrre nuove colonne con valori derivati da un set di colonne esistente

* Oggetti di applicazione definiti dall'utente
    * Serve a richiedere una riga e produrre da 0 a n righe
    * Viene usato con OUTER/CROSS APPLY

* Combinatori definiti dall'utente
    * Combinano set di righe (JOIN definiti dall'utente)

* Riduttori definiti dall'utente
    * Serve a richiedere n righe e produrre una riga
    * Vengono usati per ridurre il numero di righe

Un oggetto definito dall'utente viene in genere chiamato in modo esplicito negli script U-SQL come parte delle istruzioni U-SQL seguenti:

* EXTRACT
* OUTPUT
* PROCESS
* COMBINE
* REDUCE

> [!NOTE]  
> Gli oggetti definiti dall'utente sono limitati per occupare 0,5 GB di memoria.  Questa limitazione di memoria non è applicabile alle esecuzioni locali.

## <a name="next-steps"></a>Passaggi successivi
* [Guida alla programmabilità di U-SQL-Panoramica](data-lake-analytics-u-sql-programmability-guide.md)
* [Guida alla programmabilità di U-SQL-UDT e UDAGG](data-lake-analytics-u-sql-programmability-guide-UDT-AGG.md)