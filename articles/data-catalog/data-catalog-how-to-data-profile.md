---
title: 'Procedura: Eseguire il profiling dati delle origini dati'
description: Articolo sulle procedure che illustra come includere profili dati a livello di tabella e di colonna durante la registrazione delle origini dati in Azure Data Catalog e come usare i profili dati per comprendere le origini dati.
services: data-catalog
documentationcenter: ''
author: spelluru
manager: NA
editor: ''
tags: ''

ms.service: data-catalog
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-catalog
ms.date: 09/13/2016
ms.author: spelluru

---
# Eseguire il profiling dati delle origini dati
## Introduzione
**Catalogo dati di Microsoft Azure** è un servizio cloud completamente gestito che funge da sistema di registrazione e di individuazione per origini dati aziendali. In altre parole, il **Catalogo dati di Azure** consente agli utenti di individuare, comprendere e usare origini dati e aiuta le organizzazioni a ottenere maggior valore dai dati esistenti. Quando un'origine dati è stata registrata con **Azure Data Catalog**, i relativi metadati vengono copiati e indicizzati dal servizio, ma non è tutto.

La funzione di **profiling dati** di **Azure Data Catalog** esamina i dati dalle origini dati supportate nel catalogo e raccoglie statistiche e informazioni sui dati. È facile includere un profilo degli asset di dati. Quando si registra un asset di dati, scegliere **Includi profilo dati** nello strumento di registrazione delle origini dati.

## Informazioni sul profiling dati
Il profiling dati esamina i dati nell'origine dati di cui è in corso la registrazione e raccoglie statistiche e informazioni sui dati. Durante l'individuazione delle origini dati, le statistiche consentono di determinare l'idoneità dei dati per la risoluzione del problema aziendale.

<!-- In [How to discover data sources](data-catalog-how-to-discover.md), you learn about **Azure Data Catalog's** extensive search capabilities including searching for data assets that have a profile. See [How to include a data profile when registering a data source](#howto). -->

Le origini dati seguenti supportano il profiling dati:

* Viste e tabelle di SQL Server, inclusi database SQL di Azure e Azure SQL Data Warehouse.
* Viste e tabelle di Oracle
* Viste e tabelle di Teradata
* Tabelle Hive

Includendo i profili dati durante la registrazione degli asset di dati gli utenti possono rispondere a domande sulle origini dati, ad esempio:

* Può essere usata per risolvere il problema aziendale?
* I dati sono conformi a standard o modelli particolari?
* Quali sono alcune delle anomalie dell'origine dati?
* Quali sono i possibili problemi legati all'integrazione di questi dati nell'applicazione?

> [!NOTE]
> È anche possibile aggiungere della documentazione a un asset per descrivere come integrare i dati in un'applicazione. Vedere l'articolo relativo alla [documentazione delle origini dati](data-catalog-how-to-documentation.md).
> 
> 

<a name="howto"/>

## Come includere un profilo dati durante la registrazione di un'origine dati
È facile includere un profilo dell'origine dati. Quando si registra un'origine dati, nel pannello **Oggetti da registrare** dello strumento di registrazione delle origini dati scegliere **Includi profilo dati**.

![](media\\data-catalog-data-profile\\data-catalog-register-profile.png)

Per altre informazioni su come registrare le origini dati, vedere [Come registrare le origini dati](data-catalog-how-to-register.md) e [Introduzione ad Azure Data Catalog](data-catalog-get-started.md).

## Applicazione di filtri su asset di dati che includono profili dati
Per trovare asset di dati che includono un profilo dati, è possibile specificare `has:tableDataProfiles` o `has:columnsDataProfiles` come termini di ricerca.

> [!NOTE]
> Selezionando **Includi profilo dati** nello strumento di registrazione dell'origine dati, è possibile includere le informazioni del profilo a livello di tabella e a livello di colonna. Tuttavia, l'API del catalogo dati consente la registrazione degli asset di dati con un solo set di informazioni sul profilo.
> 
> 

## Visualizzazione delle informazioni sul profilo dati
Dopo aver individuato un'origine dati adatta con un profilo, è possibile visualizzare i dettagli relativi al profilo dati. Per visualizzare il profilo dati, selezionare un asset di dati e scegliere **Profilo dati** nella finestra del portale di Azure Data Catalog.

![](media\\data-catalog-data-profile\\data-catalog-view.png)

Un profilo dati in **Azure Data Catalog** include informazioni sul profilo a livello di tabella e di colonna, ad esempio:

### Profilo dati dell'oggetto
* Numero di righe
* Dimensioni della tabella
* Ultimo aggiornamento dell'oggetto

### Profilo dati della colonna
* Tipo di dati della colonna
* Numero di valori distinct
* Numero di righe con valori NULL
* Deviazione minima, massima, media e standard per i valori di colonna

## Riepilogo
Il profiling dati fornisce statistiche e informazioni sugli asset di dati registrati per consentire di determinare l'idoneità dei dati per la risoluzione di problemi aziendali. Oltre che annotare e documentare le origini dati, i profili dati permettono agli utenti di comprendere meglio i dati.

## Vedere anche
* [Come registrare le origini dati](data-catalog-how-to-register.md)
* [Introduzione ad Azure Data Catalog](data-catalog-get-started.md)

<!---HONumber=AcomDC_0914_2016-->