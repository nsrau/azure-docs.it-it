---
title: Introduzione ad Azure Data Catalog | Documentazione Microsoft
description: "Questo articolo offre una panoramica di Microsoft Azure Data Catalog, incluse le funzionalità e i problemi per cui è stato progettato. Data Catalog offre funzionalità che consentono a tutti gli utenti, ad esempio analisti, data scientist e sviluppatori, di registrare le origini dati, di individuarle, di ottenere informazioni su di esse e di utilizzarle."
services: data-catalog
documentationcenter: 
author: steelanddata
manager: NA
editor: 
tags: 
ms.assetid: cc733907-17ec-4153-9f0c-5b3754b2db19
ms.service: data-catalog
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: data-catalog
ms.date: 05/15/2017
ms.author: maroche
ms.translationtype: Human Translation
ms.sourcegitcommit: 538f282b28e5f43f43bf6ef28af20a4d8daea369
ms.openlocfilehash: fb8f43f5bb5725da30e67cdf5d7b066fe40ed003
ms.contentlocale: it-it
ms.lasthandoff: 04/07/2017


---
# <a name="what-is-azure-data-catalog"></a>Che cos'è il Catalogo dei dati di Azure?
Azure Data Catalog è un servizio cloud completamente gestito che consente agli utenti di trovare le origini dati necessarie e ottenere informazioni su di esse e alle organizzazioni di ottenere il massimo dagli investimenti esistenti. Data Catalog offre funzionalità che consentono a tutti gli utenti, ad esempio analisti, data scientist e sviluppatori, di trovare le origini dati, ottenere informazioni su di esse e di utilizzarle. Data Catalog include un modello di crowdsourcing di metadati e annotazioni e consente a tutti gli utenti di contribuire con le proprie conoscenze per creare una community e una cultura di dati.

## <a name="discovery-challenges-for-data-consumers"></a>Difficoltà di individuazione per i consumer di dati
Tradizionalmente, individuazione di origini dati aziendali è stato un processo organico basato su conoscenze specifiche. Questo presenta numerose sfide per le aziende che desiderano ottenere il massimo dalle loro risorse di informazioni.

* Gli utenti non sono consapevoli che origini dati esistano, a meno che essi entrare in contatto con come parte di un altro processo. non esiste alcuna posizione centrale in cui origini dati vengono registrate.
* A meno che un utente conosca il percorso di un'origine dati, è impossibile connettersi ai dati usando un'applicazione client. Le esperienze di utilizzo dei dati richiedono agli utenti di individuare la stringa di connessione o il percorso.
* A meno che un utente conosca il percorso della documentazione dell'origine dati, egli non può comprendere gli usi previsti per i dati. La documentazione e le origini dati si trovano in posizioni diverse e vengono utilizzate tramite esperienze diverse.
* Se un utente ha delle domande su un asset di informazioni, deve individuare l'esperto o il team responsabile dei dati e coinvolgere tali esperti offline. Non è disponibile alcuna connessione esplicita tra i dati e gli esperti sul relativo uso.
* A meno che un utente sia in grado di comprendere il processo per richiedere l'accesso all'origine dati, l’individuazione dell'origine dati e della relativa documentazione non gli consente comunque di accedere ai dati necessari.

## <a name="discovery-challenges-for-data-producers"></a>Difficoltà di individuazione per i produttori di dati
Mentre i consumer di dati si trovano ad affrontare queste difficoltà, gli utenti responsabili della produzione e gestione delle risorse di informazioni affrontano difficoltà proprie.

* L'annotazione di origini dati con metadati descrittivi è spesso un lavoro perso; le applicazioni client in genere ignorano descrizioni archiviate nell'origine dati.
* La creazione di documentazione per le origini dati è spesso un lavoro perso; sincronizzazione documentazione con l'origine dati è una responsabilità in corso e gli utenti non dispongono di attendibilità nella documentazione come viene spesso considerata viene aggiornato.
* La limitazione dell'accesso all'origine dati e la sicurezza che i fruitori di dati siano in grado di richiedere l'accesso costituisce una sfida costante.

La procedura di creazione e gestione della documentazione relativa a un'origine dati è lunga e complessa. La sfida di rendere disponibile tale documentazione a tutti coloro che usano l'origine dati spesso lo è ancora di più.

Queste sfide presentano un ostacolo significativo per le aziende che desiderano incoraggiare e promuovere l'uso e la comprensione dei dati aziendali.

## <a name="azure-data-catalog-can-help"></a>Il contributo di Azure Data Catalog
Data Catalog è progettato per risolvere questi problemi e consentire alle aziende di ottenere il massimo dai propri asset di informazioni esistenti. Data Catalog rende le origini dati facilmente individuabili e comprensibili per gli utenti che necessitano dei dati gestiti.

Data Catalog fornisce un servizio basato sul cloud in cui le origini dati possono essere registrate. I dati rimangono nella posizione esistente, ma una copia dei metadati viene aggiunta a Data Catalog insieme a un riferimento al percorso dell'origine dati. Questi metadati sono indicizzati anche per rendere facilmente individuabile ogni singola origine dati tramite ricerca e per renderla comprensibile agli utenti che la trovano.

Una volta registrata un'origine dati, i metadati possono essere arricchiti, dall'utente che ha eseguito la registrazione o da altri utenti nell'organizzazione. Tutti gli utenti possono annotare un'origine dati, fornendo descrizioni, tag o altri metadati, come ad esempio la documentazione e i processi per richiedere l’accesso all’origine dati. Questi metadati descrittivi integrano i metadati strutturali, ad esempio i nomi delle colonne e tipi di dati, registrati dall'origine dati.

Individuazione e informazioni sulle origini dati e il relativo utilizzo è lo scopo principale di registrazione delle origini. Quando gli utenti aziendali devono usare dati per le proprie attività (business intelligence, sviluppo di applicazioni, analisi scientifica dei dati o qualsiasi altra attività in cui sono richiesti dati corretti) possono usare Data Catalog per trovare rapidamente dati che soddisfino le proprie esigenze, comprenderli e valutare la loro idoneità allo scopo e usarli aprendo l'origine dati in uno strumento di propria scelta. Allo stesso tempo, Data Catalog consente agli utenti di contribuire al catalogo, assegnando tag, documentando e annotando le origini dati che sono già state registrate e registrando nuove origini dati che possono essere individuate, comprese e utilizzate dalla community di utenti del catalogo.

![Funzionalità di Data Catalog](./media/data-catalog-what-is-data-catalog/data-catalog-capabilities.png)

## <a name="get-started-with-data-catalog"></a>Introduzione a Data Catalog
Per un'introduzione a Data Catalog, visitare il sito [www.azuredatacatalog.com](https://www.azuredatacatalog.com).

Una guida introduttiva è disponibile [qui](data-catalog-get-started.md).

## <a name="learn-more-about-data-catalog"></a>Altre informazioni su Data Catalog
Per altre informazioni sulle funzionalità di Data Catalog, vedere:

* [Come registrare le origini dati](data-catalog-how-to-register.md)
* [Come individuare le origini dati](data-catalog-how-to-discover.md)
* [Come annotare le origini dati](data-catalog-how-to-annotate.md)
* [Come documentare le origini dati](data-catalog-how-to-documentation.md)
* [Come connettersi a origini dati](data-catalog-how-to-connect.md)
* [Come usare Big Data](data-catalog-how-to-big-data.md)
* [Come gestire gli asset di dati](data-catalog-how-to-manage.md)
* [Come configurare il glossario aziendale](data-catalog-how-to-business-glossary.md)
* [Domande frequenti](data-catalog-frequently-asked-questions.md)

