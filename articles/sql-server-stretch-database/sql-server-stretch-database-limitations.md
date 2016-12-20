---
title: Limitazioni di Estensione database | Microsoft Docs
description: Informazioni sulle limitazioni di Estensione database.
services: sql-server-stretch-database
documentationcenter: 
author: douglaslMS
manager: jhubbard
editor: 
ms.assetid: 0a865872-7d42-4873-99b9-cbae45691e54
ms.service: sql-server-stretch-database
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/26/2016
ms.author: anvang
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 3c1599dfc6aee8adf7ec8714c392a7d8d4427d78


---
# <a name="limitations-for-stretch-database"></a>Limitazioni di Estensione database
Informazioni sulle limitazioni per le tabelle abilitate per Estensione e sulle limitazioni che attualmente impediscono l'abilitazione di Estensione su una tabella.

## <a name="a-namecaveatsa-limitations-for-stretch-enabled-tables"></a><a name="Caveats"></a> Limitazioni per le tabelle abilitate per Estensione
Le tabelle abilitate per Estensione presentano le limitazioni seguenti.

### <a name="constraints"></a>Vincoli
* L'univocità non viene applicata per i vincoli UNIQUE e i vincoli PRIMARY KEY in una tabella di Azure che contiene i dati migrati.

### <a name="dml-operations"></a>Operazioni DML
* Non è possibile eseguire UPDATE o DELETE sulle righe che sono state migrate o sulle righe che potrebbero essere migrate in una tabella abilitata per Estensione o in una vista che include tabelle di questo tipo.
* Non è possibile INSERIRE righe in una tabella abilitata per Estensione su un server collegato.

### <a name="indexes"></a>Indici
* Non è possibile creare un indice per una vista che include tabelle abilitate per Estensione.
* I filtri sugli indici di SQL Server non vengono propagati alla tabella remota.

## <a name="a-namelimitationsa-limitations-that-currently-prevent-you-from-enabling-stretch-for-a-table"></a><a name="Limitations"></a> Limitazioni che attualmente impediscono l'abilitazione dell'Estensione per una tabella
Attualmente, i seguenti elementi impediscono l'abilitazione dell'Estensione per una tabella.

### <a name="table-properties"></a>Proprietà tabella
* Tabelle che includono più di 1.023 colonne o più di 998 indici
* FileTable o tabelle contenenti dati FILESTREAM
* Tabelle replicate o che usano le funzionalità Rilevamento modifiche o Change Data Capture
* Tabelle ottimizzate per la memoria

### <a name="data-types"></a>Tipi di dati
* text, ntext e image
* timestamp
* sql\_variant
* XML
* Tipi di dati CLR tra cui i tipi geometry, geography, hierarchyid e tipi CLR definiti dall'utente

### <a name="column-types"></a>Tipi di colonna
* COLUMN\_SET
* Colonne calcolate

### <a name="constraints"></a>Vincoli
* Vincoli predefiniti e vincoli CHECK
* Vincoli di chiave esterna che fanno riferimento alla tabella. In una relazione padre\-figlio \(ad esempio Order e Order\_Detail\), è possibile abilitare Estensione per la tabella figlio \(Order\_Detail\) ma non per la tabella padre \(Order\).

### <a name="indexes"></a>Indici
* Indici full-text
* Indici XML
* Indici spaziali
* Viste indicizzate che fanno riferimento alla tabella

## <a name="see-also"></a>Vedere anche
[Identificare database e tabelle per Database Estensione eseguendo l'ottimizzazione guidata Database Estensione](sql-server-stretch-database-identify-databases.md)

[Abilitare Database Estensione per un database](sql-server-stretch-database-enable-database.md)

[Abilitare l'estensione database per una tabella](sql-server-stretch-database-enable-table.md)




<!--HONumber=Nov16_HO3-->


