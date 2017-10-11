---
title: Scenari comuni di Azure Data Catalog | Documentazione Microsoft
description: "Panoramica degli scenari comuni per Azure Data Catalog, incluse la registrazione e l'individuazione delle origini dati ad alto valore, l'abilitazione della business intelligence in modalità self-service e l'acquisizione di conoscenze esistenti sulle origini dati e i processi."
services: data-catalog
documentationcenter: 
author: steelanddata
manager: NA
editor: 
tags: 
ms.assetid: 60930d78-d2d4-4d5d-9651-bdda50b0da0e
ms.service: data-catalog
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-catalog
ms.date: 08/15/2017
ms.author: maroche
ms.openlocfilehash: e01e6fa3b9871541bf9573963ced05848a3726e3
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/18/2017
---
# <a name="azure-data-catalog-common-scenarios"></a>Scenari comuni del Catalogo dati di Azure
In questo articolo vengono presentati scenari comuni in cui Azure Data Catalog può aiutare l'organizzazione a incrementare il valore delle origini dati esistenti.

## <a name="scenario-1-registration-of-central-data-sources"></a>Scenario 1: Registrazione delle origini dati centrali
Le organizzazioni hanno spesso molte origini dati di valore elevato. Queste origini dati includono sistemi di elaborazione di transazioni online (OLTP) line-of-business, data warehouse e database di business intelligence/analisi. Il numero dei sistemi e la sovrapposizione tra di essi in genere cresce nel tempo in base all'evoluzione dell'azienda e delle relative necessità, ad esempio dopo acquisizioni e fusioni.

Può essere difficile per i membri dell'organizzazione sapere dove si trovano i dati all'interno delle origini dati. Domande come le seguenti sono molto diffuse:

* Tra i tre sistemi HR usati nella società, quale è opportuno usare per creare questo tipo di report?
* Dove è necessario rivolgersi per ottenere i numeri delle vendite certificate per l'anno fiscale appena finito?
* A chi è necessario rivolgersi o qual è il processo per ottenere l'accesso al data warehouse?
* Se non si è certi che i numeri siano corretti, a chi è possibile rivolgersi per ottenere informazioni su come questi dati devono essere usati prima di condividere questo dashboard con il team?

Azure Data Catalog può offrire le risposte a queste e altre domande. Le origini dati centrali, di valore elevato e gestite da team IT, che vengono usate nelle organizzazioni sono spesso il punto di partenza logico per il popolamento del catalogo. Anche se qualsiasi utente può registrare un'origine dati, se il catalogo viene avviato con le origini di dati che hanno più probabilità di fornire valore a un maggior numero di utenti, si contribuisce all'adozione e all'uso del sistema. 

Se si sta iniziando a usare Azure Data Catalog, l'identificazione e la registrazione delle origini dati principali usate da molti team diversi di consumer di dati può essere il primo passo per il successo.

Questo scenario presenta anche un'opportunità per annotare le origini dati di valore elevato per facilitarne la comprensione e l'accesso. Un aspetto fondamentale di questa operazione consiste nell'includere informazioni su come gli utenti possono richiedere l'accesso all'origine dati. Con Azure Data Catalog è possibile fornire l'indirizzo di posta elettronica dell'utente o del team responsabile del controllo dell'accesso all'origine dati, collegamenti a strumenti o documenti esistenti oppure testo libero che descrive il processo di richiesta di accesso. Queste informazioni consentono ai membri che individuano origini dati registrate, ma che non hanno ancora le autorizzazioni per accedere ai dati, di richiedere facilmente l'accesso usando i processi definiti e controllati dai proprietari dell'origine dati.

## <a name="scenario-2-self-service-business-intelligence"></a>Scenario 2: Business intelligence in modalità self-service
Anche se le soluzioni di business intelligence aziendali tradizionali continuano a essere scenari di dati di molte organizzazioni, i vari cambiamenti di mercato hanno reso la soluzione BI in modalità self-service sempre più importante. Usando la BI in modalità self-service, gli information worker e gli analisti possono creare report, cartelle di lavoro e dashboard senza basarsi su un team IT centrale oppure senza limitazioni di pianificazione e disponibilità del team IT.

In scenari di business intelligence in modalità self-service gli utenti normalmente combinano dati da più origini, molte delle quali potrebbero non essere ancora state usate per analisi e business intelligence. Anche se alcune di queste origini dati potrebbero essere già note, può essere difficile capire che cosa fare per individuare e valutare le potenziali origini dati per una determinata attività.

In genere, questo processo di individuazione è manuale: gli analisti usano le connessioni di rete peer per identificare altre persone che lavorano con i dati cercati. Dopo che un'origine dati è stata trovata e usata, il processo viene ripetuto nuovamente per ogni successiva attività di BI in modalità self-service, con più utenti che eseguono un processo manuale ridondante di individuazione.

Con Azure Data Catalog, l'organizzazione può interrompere questo ciclo di attività. Dopo l'individuazione di un'origine dati tramite mezzi tradizionali, un analista può registrarla per renderla più facilmente individuabile da altri utenti in futuro. Anche se l'analista può aggiungere più valore annotando gli asset dei dati registrati, questa annotazione non è necessaria contestualmente alla registrazione. Gli utenti possono contribuire in futuro, come permesso dalle pianificazioni, aggiungendo gradualmente valore alle origini dati registrate nel catalogo.

Questa crescita strutturale del contenuto del catalogo è un complemento naturale alla registrazione iniziale delle origini dati centrali. La precompilazione del catalogo con i dati di molti utenti può essere una motivazione per l'uso e l'individuazione iniziali. Consentendo agli utenti di registrare e annotare altre origini, si offre un modo per mantenere l'impegno di utenti e altri membro dell'organizzazione.

È importante notare che, anche se questo scenario è incentrato specificamente sulla business intelligence in modalità self-service, i modelli e le richieste si applicano anche a progetti di BI aziendali su larga scala. Usando Data Catalog, l'organizzazione può migliorare qualsiasi attività comporti un processo manuale di individuazione delle origini dati.

## <a name="scenario-3-capturing-tribal-knowledge"></a>Scenario 3: Acquisizione di conoscenze specifiche
Di seguito sono riportate informazioni per sapere quali dati devono essere eseguiti dal processo e dove si trovano.

Se si ha una certa familiarità con il processo, probabilmente già si hanno le conoscenze necessarie. L'utente ha intrapreso gradualmente il processo di apprendimento e nel corso del tempo ha imparato a usare le origini dati che sono fondamentali quotidianamente.

Quando un nuovo dipendente viene aggiunto al team, probabilmente non sa quali dati sono necessari per il lavoro e dove si trovano,

anzi quasi certamente chiederà proprio queste informazioni.

Il trasferimento delle conoscenze specifiche in atto fa parte del processo di individuazione delle origini dati di piccole e grandi aziende. I membri del team più esperti hanno acquisito competenze nel corso degli anni e i membri del team più recenti hanno imparato a rivolgersi agli esperti per le domande. Spesso le informazioni più importanti sono presenti solo nelle teste di alcune persone chiave e quando tali utenti sono in vacanza o lasciano il team, l'organizzazione soffre.

Gli esperti di dati si impegnano in genere per documentare le proprie conoscenze e condividerle tramite posta elettronica o in documenti di Word in un sito di SharePoint del team. Anche se questo approccio può essere utile, è necessario considerare un nuovo problema di individuazione, ossia come fanno gli utenti a sapere quale documentazione è disponibile e dove reperirla.

Con Azure Data Catalog, l'organizzazione ha un'unica posizione centrale in cui archiviare e condividere queste informazioni specifiche, rendendole facilmente individuabili. In Data Catalog gli esperti di dati possono annotare direttamente gli asset di dati e possono fornire i collegamenti alla documentazione esistente. Quando i membri dell'organizzazione usano il catalogo per trovare un'origine dati, troveranno non solo l'origine stessa, ma anche le conoscenze che prima erano detenute esclusivamente dagli esperti dell'organizzazione.
