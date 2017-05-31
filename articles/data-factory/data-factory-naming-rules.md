---
title: "Regole per la denominazione delle entità di Azure Data Factory | Microsoft Docs"
description: "Descrive le regole di denominazione per le entità di Data factory."
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: monicar
ms.assetid: bc5e801d-0b3b-48ec-9501-bb4146ea17f1
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 2/24/2017
ms.author: shlo
ms.translationtype: Human Translation
ms.sourcegitcommit: 7c4d5e161c9f7af33609be53e7b82f156bb0e33f
ms.openlocfilehash: f6538666e0124f5d9d0ce3043304612efb526b17
ms.contentlocale: it-it
ms.lasthandoff: 05/04/2017


---
# <a name="azure-data-factory---naming-rules"></a>Azure Data Factory - Regole di denominazione
La tabella seguente specifica le regole di denominazione per gli elementi di Data factory.

| Nome | Univocità del nome | Controlli di convalida |
|:--- |:--- |:--- |
| Data factory |Univoco in Microsoft Azure. Per i nomi non viene fatta distinzione tra maiuscole e minuscole; MyDF e mydf, ad esempio, fanno riferimento alla stessa data factory. |<ul><li>Ogni data factory è collegata a una sola sottoscrizione di Azure.</li><li>I nomi degli oggetti devono iniziare con una lettera o un numero e possono contenere solo lettere, numeri e il carattere trattino (-).</li><li>Ogni carattere trattino (-) deve essere immediatamente preceduto e seguito da una lettera o un numero. Nei nomi di contenitori non sono consentiti trattini consecutivi.</li><li>Il nome può contenere da 3 a 63 caratteri.</li></ul> |
| Servizi collegati/Tabelle/Pipeline |Univoco in una data factory. Per i nomi viene fatta distinzione tra maiuscole e minuscole. |<ul><li>Numero massimo di caratteri nel nome di una tabella: 260.</li><li>I nomi degli oggetti devono iniziare con una lettera, un numero o un carattere di sottolineatura (_).</li><li>Non sono ammessi i caratteri seguenti: ".", "+", "?", "/", "<", ">", "*", "%", "&", ":", "\\".</li></ul> |
| Gruppo di risorse |Univoco in Microsoft Azure. Per i nomi viene fatta distinzione tra maiuscole e minuscole. |<ul><li>Numero massimo di caratteri: 1000.</li><li>Il nome può contenere lettere, cifre e i caratteri seguenti: "-", "_", "," e ".".</li></ul> |


