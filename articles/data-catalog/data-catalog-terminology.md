---
title: Terminologia di Azure Data Catalog | Documentazione Microsoft
description: Questo articolo fornisce un'introduzione ai concetti e ai termini usati nella documentazione di Azure Data Catalog.
services: data-catalog
documentationcenter: 
author: steelanddata
manager: NA
editor: 
tags: 
ms.assetid: 6fec74d9-4a3c-4b4b-88ba-cad5ad143331
ms.service: data-catalog
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-catalog
ms.date: 08/15/2017
ms.author: maroche
ms.translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: e9e1009bc20679a907e4bd2387865a6000b4a898
ms.contentlocale: it-it
ms.lasthandoff: 11/17/2016

---
# <a name="azure-data-catalog-terminology"></a>Terminologia del Catalogo dati di Azure
## <a name="catalog"></a>Catalogo
Il Catalogo dati di Azure è un repository di metadati basato su cloud in cui è possibile registrare asset di dati e origini dati. Il catalogo viene usato come posizione di archiviazione centrale per i metadati strutturali estratti dalle origini dati e i metadati descrittivi aggiunti dagli utenti.

## <a name="data-source"></a>Origine dati
Un'origine dati è un sistema o un contenitore che gestisce gli asset di dati. Esempi sono i database di SQL Server, Oracle e SQL Server Analysis Services (tabulari o multidimensionali) nonché i server di SQL Server Reporting Services.

## <a name="data-asset"></a>Asset di dati
Gli asset di dati sono oggetti contenuti nelle origini dati che possono essere registrati nel catalogo. Ad esempio, tabelle e viste di SQL Server, tabelle e viste di Oracle, misure, dimensioni e indicatori KPI di SQL Server Analysis Services e report di SQL Server Reporting Services.

## <a name="data-asset-location"></a>Posizione degli asset di dati
Il catalogo include la posizione di un'origine dati o un asset di dati che può essere usata per connettersi all'origine tramite un'applicazione client. Il formato e i dettagli della posizione variano in base al tipo di origine dati. Ad esempio, una tabella SQL Server può essere identificata tramite il nome composto da quattro parti (nome del server, nome del database, nome dello schema, nome dell'oggetto) mentre un report di SQL Server Reporting Services può essere identificato tramite il relativo URL.

## <a name="structural-metadata"></a>Metadati strutturali
I metadati strutturali sono metadati estratti da un'origine dati che descrive la struttura di un asset di dati. Ciò include il percorso dell’asset, il nome dell'oggetto, il tipo e ulteriori caratteristiche specifiche del tipo. Ad esempio, i metadati strutturali per tabelle e viste includono i nomi e i tipi di dati delle colonne dell'oggetto.

## <a name="descriptive-metadata"></a>Metadati descrittivi
I metadati descrittivi sono metadati che descrivono lo scopo o l'intenzione di un asset di dati. In genere i metadati descrittivi vengono aggiunti dagli utenti del catalogo tramite il portale del Catalogo dati di Azure, ma possono anche essere estratti durante la registrazione dell'origine dati. Il tool di registrazione di Azure Data Catalog, ad esempio, estrae le descrizioni dalla proprietà Description in SQL Server Analysis Services e SQL Server Reporting Services e dalla [proprietà estesa ms_description](https://technet.microsoft.com/library/ms190243.aspx) nei database di SQL Server, se queste proprietà sono popolate con valori.

## <a name="request-access"></a>Richiedere l'accesso
I metadati descrittivi dell'asset di dati possono includere informazioni su come richiedere l'accesso a asset di dati o origine dati. Queste informazioni vengono visualizzate con la posizione degli asset di dati e possono includere una o più delle seguenti opzioni:

* Indirizzo di posta elettronica dell'utente o del team responsabile della concessione dell'accesso all'origine dati.
* L'URL della procedura documentata che gli utenti devono seguire per ottenere l'accesso all'origine dati.
* L'URL di un'identità e lo strumento di gestione dell'accesso (ad esempio Microsoft Identity Manager) che può essere usato per accedere all'origine dati.
* Una voce di testo libero che descrive come gli utenti possono accedere all'origine dati.

## <a name="preview"></a>Preview
L'anteprima nel Catalogo dati di Azure è uno snapshot di 20 record al massimo che possono essere estratti dall'origine dati durante la registrazione e archiviati nel catalogo con i metadati dell’asset di dati. L'anteprima può aiutare gli utenti a individuare un asset di dati per comprenderne meglio lo scopo e la funzione. In altre parole, i dati di esempio possono essere più importanti dei soli nomi di colonna e tipi di dati.
Le anteprime sono supportate solo per tabelle e viste e devono essere selezionate in modo esplicito dall'utente durante la registrazione.

## <a name="data-profile"></a>Profilo dei dati
Un profilo dati nel Catalogo dati di Azure è uno snapshot di metadati a livello di tabella e a livello di colonna su un asset di dati registrati che può essere estratto dall'origine dati durante la registrazione e archiviato nel catalogo con i metadati dell’asset di dati. Il profilo dati può aiutare gli utenti a individuare un asset di dati per comprenderne meglio lo scopo e la funzione. Analogamente alle anteprime, i profili dati devono essere selezionati esplicitamente dall'utente durante la registrazione.

> [!NOTE]
> L’estrazione di un profilo dati può essere un'operazione costosa per tabelle e viste di grandi dimensioni e potrebbe aumentare significativamente il tempo necessario per registrare un'origine dati.
>
>

## <a name="user-perspective"></a>Prospettiva dell'utente
Nel Catalogo dati di Azure, qualsiasi utente può fornire metadati descrittivi per un asset di dati registrato. Ogni utente dispone di una prospettiva diversa sui dati e sul relativo uso. Ad esempio, l'amministratore responsabile di un server può fornire i dettagli delle finestre di backup o del contratto di servizio, un amministratore dei dati può fornire i collegamenti alla documentazione per i processi aziendali supportati dai dati e un analista può fornire una descrizione in termini rilevanti per altri analisti che può essere più significativa per gli utenti che necessitano di individuare e comprendere i dati.

Ognuna di queste prospettive sono intrinsecamente utili e con il Catalogo dati di Azure ogni utente può fornire informazioni significative, mentre tutti gli utenti possono usare tali informazioni per comprendere dati e scopo.

## <a name="expert"></a>Esperto
Un esperto è un utente che è stato identificato come avente una prospettiva informata "esperta" per un asset di dati. Qualsiasi utente può aggiungere se stesso o un altro utente come esperto di un asset. Essere elencato come esperto non apporta ulteriori privilegi nel Catalogo dati di Azure. Consente agli utenti di individuare facilmente le prospettive che con maggiore probabilità sono utili durante la revisione dei metadati descrittivi di un asset.

## <a name="owner"></a>Proprietario
Un proprietario è un utente con privilegi aggiuntivi per la gestione di un asset di dati nel Catalogo dati di Azure. Gli utenti possono diventare proprietari di dati registrati e i proprietari possono aggiungere altri utenti come comproprietari. Per altre informazioni, vedere [Come gestire gli asset di dati](data-catalog-how-to-manage.md)  

> [!NOTE]
> La gestione e la proprietà sono disponibili solo nell'edizione Standard del Catalogo dati di Azure.
>
>

## <a name="registration"></a>Registrazione
La registrazione è l'atto di estrazione dei metadati di asset di dati da un'origine dati e di copia nel servizio Catalogo dati di Azure. Gli asset di dati registrati possono essere annotati e individuati.

## <a name="see-also"></a>Vedere anche
* [Che cos'è il Catalogo dei dati di Azure?](data-catalog-what-is-data-catalog.md) Questo articolo fornisce una panoramica del servizio Azure Data Catalog, del valore offerto dal servizio e degli scenari supportati.
* [Introduzione ad Azure Data Catalog](data-catalog-get-started.md), che contiene un'esercitazione end-to-end in cui viene spiegato come utilizzare Azure Data Catalog per individuare le origini dati.  

