<properties
   pageTitle="Terminologia del Catalogo dati di Azure"
   description="Terminologia del Catalogo dati di Azure"
   services="data-catalog"
   documentationCenter=""
   authors="dvana"
   manager="mblythe"
   editor=""
   tags=""/>
<tags
   ms.service="data-catalog"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-catalog"
   ms.date="07/13/2015"
   ms.author="derrickv"/>

# Terminologia del Catalogo dati di Azure

## Catalogo

Il Catalogo dati di Azure è un repository di metadati basato su cloud in cui è possibile registrare asset di dati e origini dati. Il catalogo viene utilizzato come posizione di archiviazione centrale per i metadati strutturali estratti dalle origini dati e i metadati descrittivi aggiunti dagli utenti.

## Origine dati

Un'origine dati è un sistema o un contenitore che gestisce gli asset di dati. Esempi sono i database di SQL Server, Oracle e SQL Server Analysis Services (tabulari o multidimensionali) nonché i server di SQL Server Reporting Services.

## Asset di dati

Gli asset di dati sono oggetti contenuti nelle origini dati che possono essere registrati nel catalogo. Ad esempio, tabelle e viste di SQL Server, tabelle e viste di Oracle, misure, dimensioni e indicatori KPI di SQL Server Analysis Services e report di SQL Server Reporting Services.

## Posizione degli asset di dati

Il catalogo include la posizione di un'origine dati o un asset di dati che può essere utilizzata per connettersi all'origine tramite un'applicazione client. Il formato e i dettagli della posizione variano in base al tipo di origine dati. Ad esempio, una tabella SQL Server può essere identificata tramite il nome composto da quattro parti (nome del server, nome del database, nome dello schema, nome dell'oggetto) mentre un report di SQL Server Reporting Services può essere identificato tramite il relativo URL.

## Metadati strutturali

I metadati strutturali sono metadati estratti da un'origine dati che descrive la struttura di un asset di dati. Ciò include il percorso dell’asset, il nome dell'oggetto, il tipo e ulteriori caratteristiche specifiche del tipo. Ad esempio, i metadati strutturali per tabelle e viste includono i nomi e i tipi di dati delle colonne dell'oggetto.

## Metadati descrittivi

I metadati descrittivi sono metadati che descrivono lo scopo o l'intenzione di un asset di dati. In genere i metadati descrittivi vengono aggiunti dagli utenti del catalogo tramite il portale del Catalogo dati di Azure, ma possono anche essere estratti durante la registrazione dell'origine dati. Lo strumento di registrazione del Catalogo dati di Azure estrae le descrizioni dalla proprietà Description in SQL Server Analysis Services e SQL Server Reporting Services e dalla [proprietà estesa ms_description](https://technet.microsoft.com/library/ms190243.aspx) nei database di SQL Server, se queste proprietà sono popolate con valori.

## Anteprime

L'anteprima nel Catalogo dati di Azure è uno snapshot di 20 record al massimo che possono essere estratti dall'origine dati durante la registrazione e archiviati nel catalogo con i metadati dell’asset di dati. L'anteprima può aiutare gli utenti a individuare un asset di dati per comprenderne meglio lo scopo e la funzione. In altre parole, i dati di esempio possono essere più importanti dei soli nomi di colonna e tipi di dati. Le anteprime sono supportate solo per tabelle e viste e devono essere selezionate in modo esplicito dall'utente durante la registrazione.

## Prospettiva dell'utente

Nel Catalogo dati di Azure, qualsiasi utente può fornire metadati descrittivi per un asset di dati registrato. Ogni utente dispone di una prospettiva diversa sui dati e sul relativo utilizzo. Ad esempio, l'amministratore responsabile di un server può fornire i dettagli delle finestre di backup o del contratto di servizio, un amministratore dei dati può fornire i collegamenti alla documentazione per i processi aziendali supportati dai dati e un analista può fornire una descrizione in termini rilevanti per altri analisti che può essere più significativa per gli utenti che necessitano di individuare e comprendere i dati.

Ognuna di queste prospettive sono intrinsecamente utili e con il Catalogo dati di Azure ogni utente può fornire informazioni significative, mentre tutti gli utenti possono utilizzare tali informazioni per comprendere dati e scopo.

## Esperto

Un esperto è un utente che è stato identificato come avente una prospettiva informata "esperta" per un asset di dati. Qualsiasi utente può aggiungere se stesso o un altro utente come esperto di un asset. Essere elencato come esperto non apporta ulteriori privilegi nel Catalogo dati di Azure. Consente agli utenti di individuare facilmente le prospettive che con maggiore probabilità sono utili durante la revisione dei metadati descrittivi di un asset.

## Proprietario

Un proprietario è un utente con privilegi aggiuntivi per la gestione di un asset di dati nel Catalogo dati di Azure. Gli utenti possono diventare proprietari di dati registrati e i proprietari possono aggiungere altri utenti come comproprietari.
> [AZURE.NOTE]La gestione e la proprietà sono disponibili solo nel Catalogo dati di Azure Standard Edition.
 
## Registrazione

La registrazione è l'atto di estrazione dei metadati di asset di dati da un'origine dati e di copia nel servizio Catalogo dati di Azure. Gli asset di dati registrati possono essere annotati e individuati.

## Vedere anche

- [Che cos'è il catalogo dei dati di Azure?](data-catalog-what-is-data-catalog.md). In questo articolo viene fornita una panoramica del servizio Catalogo dati di Azure, il valore restituito e gli scenari supportati.

- [Introduzione al Catalogo dati di Azure](data-catalog-get-started.md). In questo articolo viene fornita un'esercitazione end-to-end in che illustra come utilizzare il Catalogo dati di Azure per l'individuazione dell’origine dati.

<!---HONumber=July15_HO3-->