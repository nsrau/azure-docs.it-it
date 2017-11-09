---
title: Configurare il glossario aziendale per l'assegnazione di tag regolamentata in Azure Data Catalog | Microsoft Docs
description: Articolo sulle procedure che illustra il glossario aziendale in Azure Data Catalog per definire e usare un vocabolario aziendale comune in base al quale assegnare tag ad asset di dati registrati.
services: data-catalog
documentationcenter: 
author: steelanddata
manager: NA
editor: 
tags: 
ms.assetid: b3d63dbe-1ae7-499f-bc46-42124e950cd6
ms.service: data-catalog
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-catalog
ms.date: 08/15/2017
ms.author: maroche
ms.openlocfilehash: 83ca3b2d89a335a5fd6dddeaca7c11f6d0492234
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="set-up-the-business-glossary-for-governed-tagging"></a>Come configurare il glossario aziendale per l'assegnazione di tag regolamentata
## <a name="introduction"></a>Introduzione
Azure Data Catalog consente l'individuazione delle origini dati per poter comprendere e trovare facilmente le origini dati necessarie per eseguire analisi e prendere decisioni. Queste funzionalità fanno veramente la differenza quando si ha la possibilità di trovare e comprendere la più ampia gamma di origini dati disponibili.

L'assegnazione di tag è una delle funzionalità di Data Catalog che aiuta a capire i dati relativi agli asset. Usando l'assegnazione di tag, è possibile associare parole chiave a un asset o una colonna per facilitare l'individuazione dell'asset tramite ricerca o esplorazione. L'assegnazione di tag consente anche di comprendere più facilmente il contesto e la finalità dell'asset.

L'assegnazione di tag può, tuttavia, causare a volte problemi. Alcuni dei problemi causati dall'assegnazione di tag sono illustrati di seguito:

* Uso di abbreviazioni per alcuni asset e di testo espanso per altri. Questa incoerenza impedisce l'individuazione degli asset nonostante l'obiettivo sia quello di contrassegnare gli asset con lo stesso tag.
* Potenziali variazioni nel significato, a seconda del contesto. Ad esempio, un tag denominato *Ricavi* su un set di dati del cliente potrebbe significare ricavi del cliente, ma lo stesso tag su un set di dati di vendite trimestrali potrebbe invece significare ricavi trimestrali dell'azienda.  

Per affrontare queste e altre problematiche simili, Data Catalog include un glossario aziendale.

Usando il glossario aziendale di Data Catalog, un'organizzazione può spiegare i termini aziendali principali e includere le relative definizioni per creare un vocabolario aziendale comune. In questo modo si garantisce coerenza nell'uso dei dati all'interno dell'organizzazione. Un termine, dopo essere stato definito nel glossario aziendale, può essere assegnato a un asset di dati nel catalogo. Questo approccio, detto *assegnazione di tag regolamentata*, è uguale all'assegnazione di tag.

## <a name="glossary-availability-and-privileges"></a>Disponibilità e privilegi del glossario
Il glossario aziendale è disponibile nell'edizione Standard di Azure Data Catalog. L'edizione gratuita di data Catalog non include un glossario e non offre le funzionalità per l'assegnazione di tag regolamentata.

È possibile accedere al glossario aziendale tramite l'opzione **Glossario** nel menu di spostamento del portale di Data Catalog.  

![Accesso al glossario aziendale](./media/data-catalog-how-to-business-glossary/01-portal-menu.png)

Gli amministratori di Data Catalog e i membri del ruolo Amministratori del glossario possono creare, modificare ed eliminare i termini del glossario aziendale. Tutti gli utenti di Data Catalog possono visualizzare le definizioni dei termini e assegnare agli asset tag usando i termini del glossario.

![Aggiunta di un nuovo termine di glossario](./media/data-catalog-how-to-business-glossary/02-new-term.png)

## <a name="creating-glossary-terms"></a>Creazione dei termini di glossario
Facendo clic sul pulsante **Nuovo termine**, gli amministratori di Data Catalog e gli amministratori del glossario possono creare nuovi termini di glossario. Ogni termine del glossario contiene i campi seguenti:

* Una definizione aziendale del termine
* Una descrizione che illustra l'uso previsto o le regole business per l'asset o la colonna
* Un elenco delle parti interessate che conoscono meglio il termine
* Il termine padre, che definisce la gerarchia in cui è organizzato il termine

## <a name="glossary-term-hierarchies"></a>Gerarchie dei termini di glossario
Usando il glossario aziendale di Data Catalog, un'organizzazione può descrivere il proprio vocabolario aziendale come gerarchia di termini e creare una classificazione dei termini che rappresentano meglio la tassonomia aziendale.

Un termine deve essere univoco in un determinato livello della gerarchia. I nomi duplicati non sono consentiti. Non esiste alcun limite al numero di livelli in una gerarchia. Una gerarchia risulta tuttavia di più facile comprensione se è costituita da non oltre tre livelli.

L'uso delle gerarchie nel glossario aziendale è facoltativo. Se non viene specificato il campo del termine padre, viene creato un elenco semplice, vale a dire senza gerarchia, di termini di glossario.  

## <a name="tagging-assets-with-glossary-terms"></a>Assegnazione di tag agli asset con termini di glossario
Dopo aver definito i termini di glossario, è possibile assegnare tag agli asset per ottimizzare la ricerca nel glossario ogni volta che un utente digita un tag. Il portale di Data Catalog visualizza un elenco di termini di glossario che corrispondono alla ricerca. Se l'utente seleziona un termine di glossario dall'elenco, il termine viene aggiunto all'asset come tag (detto anche tag di glossario). L'utente può anche scegliere di creare un nuovo tag digitando un termine che non è incluso nel glossario (detto anche tag utente).

![Assegnazione di tag ad asset di dati con un tag utente o due tag di glossario](./media/data-catalog-how-to-business-glossary/03-tagged-asset.png)

> [!NOTE]
> I tag utente sono l'unico tipo di tag supportato nell'edizione gratuita di Data Catalog.
>
>

### <a name="hover-behavior-on-tags"></a>Comportamento al passaggio del mouse sui tag
Nel portale di Data Catalog i due tipi di tag sono visivamente distinti e presentano comportamenti diversi al passaggio del mouse. Quando il mouse viene spostato su un tag utente, è possibile visualizzare il testo del tag e l'utente o gli utenti che lo hanno aggiunto. Quando il mouse viene spostato su un tag di glossario, vengono visualizzati anche la definizione del termine di glossario e un collegamento per aprire il glossario aziendale e visualizzare la definizione completa del termine.

### <a name="search-filters-for-tags"></a>Filtri di ricerca per i tag
È possibile cercare sia che i tag di glossario che i tag utente e applicarli come filtri in una ricerca.

## <a name="summary"></a>Riepilogo
Usando il glossario aziendale di Azure Data Catalog e la relativa assegnazione di tag regolamentata, è possibile identificare, gestire e trovare asset di dati in modo coerente. Il glossario aziendale può facilitare l'apprendimento del vocabolario aziendale ai membri dell'organizzazione. Il glossario aiuta anche a trovare i metadati significativi, semplificando così l'individuazione e la comprensione degli asset.

## <a name="next-steps"></a>Passaggi successivi
* [Documentazione relativa all'API REST per operazioni che riguardano il glossario aziendale](https://msdn.microsoft.com/library/mt708855.aspx)
