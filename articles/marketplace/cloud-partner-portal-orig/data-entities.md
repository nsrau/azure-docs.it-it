---
title: Entità di dati | Microsoft Docs
description: Una panoramica delle entità di dati.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: pbutlerm
manager: Ricardo.Villalobos
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: 579856ab87aaf8d051f2e3c161bb2d0e2f693ed5
ms.sourcegitcommit: 9b6492fdcac18aa872ed771192a420d1d9551a33
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/22/2019
ms.locfileid: "54446579"
---
# <a name="data-entities"></a>Entità di dati

Questo articolo contiene una definizione e una panoramica delle entità di dati. Include informazioni sulle funzionalità delle entità di dati, gli scenari che supportano, le categorie selezionate e i metodi usati per crearle.

## <a name="overview"></a>Panoramica

Un'entità di dati è un'astrazione dell'implementazione fisica delle tabelle di database. Ad esempio, nelle tabelle normalizzate, la maggior parte dei dati per ogni cliente può essere archiviata in una tabella clienti e i dati restanti possono essere distribuiti in un piccolo set di tabelle correlate. In questo caso, l'entità di dati per il concetto cliente viene presentata come vista denormalizzata, in cui ogni riga contiene tutti i dati della tabella clienti e delle tabelle correlate. Un'entità di dati incapsula un concetto di business in un formato che facilita lo sviluppo e l'integrazione. La natura astratta di un'entità di dati può semplificare lo sviluppo e la personalizzazione delle applicazioni. In una fase successiva, l'astrazione isola anche il codice dell'applicazione dalla varianza inevitabile delle tabelle fisiche tra le versioni.

Per riepilogare: l'entità di dati fornisce l'astrazione e l'incapsulamento concettuale (vista denormalizzata) degli schemi delle tabelle sottostanti per rappresentare i concetti e le funzionalità chiave dei dati.

## <a name="capabilities"></a>Capabilities

Un'entità di dati ha le seguenti funzionalità:

- Sostituisce i concetti di divergenti e frammentati di AXD, delle entità Data Import/Export Framework (DIXF) e delle query di aggregazione con un singolo concetto.
- Fornisce un singolo stack per acquisire la logica di business e abilitare scenari come importazione/esportazione, integrazione e programmabilità.
- Diventa il meccanismo principale per l'esportazione e l'importazione di pacchetti di dati per gli scenari di dati dimostrativi e Application Lifecycle Management (ALM).
- Può essere esposta come servizi OData e usata quindi in scenari di integrazione sincrona in stile tabellare e nelle integrazioni con Microsoft Office.

Per altre informazioni, vedere [Data Entities](https://docs.microsoft.com/dynamics365/operations/dev-itpro/data-entities/data-entities) (Entità di dati).
