---
title: Come documentare le origini dati in Azure Data Catalog
description: Articolo sulle procedure che illustra come documentare gli asset di dati in Azure Data Catalog.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: 5c0bc97e5a5b362ea78f82ea99bb967a9607093a
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/09/2019
ms.locfileid: "68882496"
---
# <a name="document-data-sources"></a>Documentare le origini dati
## <a name="introduction"></a>Introduzione
**Microsoft Azure Data Catalog** è un servizio cloud completamente gestito che funge da sistema di registrazione e di individuazione per origini dati aziendali. In altre parole, **Azure Data Catalog** permette agli utenti di trovare, *comprendere*e usare le origini dati e consente alle organizzazioni di ottenere maggior valore dai dati esistenti.

Quando un'origine dati viene registrata con **Azure Data Catalog**, i relativi metadati vengono copiati e indicizzati dal servizio, ma non è tutto. **Azure Data Catalog** consente agli utenti di fornire una documentazione completa per descrivere l'utilizzo e scenari comuni per l'origine dati.

L'articolo [Come annotare le origini dati](data-catalog-how-to-annotate.md)mostra come gli esperti che conoscono l'origine dati possono annotarla con tag e descrizioni. Il portale di **Azure Data Catalog** include un editor di testo RTF per permettere agli utenti di documentare in modo esauriente gli asset di dati e i contenitori. L'editor include la formattazione paragrafo, con intestazioni, formattazione del testo, elenchi puntati, elenchi numerati e tabelle.

Tag e descrizioni sono utili per inserire semplici annotazioni. Tuttavia, per avere una migliore comprensione di un'origine dati e dei relativi scenari di business gli utilizzatori di dati possono rivolgersi a un esperto per una documentazione completa e dettagliata. È facile documentare un'origine dati. Selezionare un contenitore o un asset di dati e scegliere **Documentazione**.

![Scheda documentazione in una Data Catalog](media/data-catalog-documentation/data-catalog-documentation.png)

## <a name="documenting-data-assets"></a>Documentare gli asset di dati
La documentazione permette di usare **Azure Data Catalog** come un repository per creare un resoconto completo degli asset di dati. È possibile esplorare contenuti dettagliati che descrivono i contenitori e le tabelle. Se sono già presenti contenuti in un altro repository, ad esempio SharePoint o una condivisione file, è possibile aggiungere alla documentazione degli asset collegamenti che fanno riferimento al contenuto esistente. Questa funzione rende i documenti esistenti più facilmente individuabili.

> [!NOTE]
> La documentazione non è inclusa nell'indice di ricerca.
>

![Scheda documentazione e collegamento ipertestuale al collegamento Web](media/data-catalog-documentation/data-catalog-documentation2.png)

Il livello di documentazione va dalla descrizione delle caratteristiche e del valore di un contenitore di asset di dati alla descrizione dettagliata dello schema di tabella all'interno di un contenitore. Il livello di documentazione fornito deve essere proporzionato alle esigenze aziendali. In linea generale, ecco alcuni vantaggi e svantaggi legati alla documentazione degli asset di dati:

* Documentare solo un contenitore: Tutto il contenuto è disponibile in un'unica posizione, ma potrebbe non avere le informazioni necessarie per consentire agli utenti di prendere decisioni informate.
* Documentare solo le tabelle: Il contenuto è specifico dell'oggetto, ma gli utenti hanno più posizioni per i documenti.
* Contenitori e tabelle di documenti: Approccio più completo, ma potrebbe introdurre una maggiore manutenzione dei documenti.

## <a name="summary"></a>Riepilogo
La documentazione delle origini dati con **Azure Data Catalog** permette di creare un resoconto sugli asset di dati con un livello di dettaglio basato sulle esigenze.  È possibile creare collegamenti a contenuti archiviati in un repository esistente che riunisce i documenti e gli asset di dati esistenti. Dopo aver trovato gli asset di dati appropriati, gli utenti hanno a disposizione un set di documentazione completo.
