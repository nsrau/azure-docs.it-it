---
title: Copertura per il traffico in Mappe di Azure | Microsoft Docs
description: Informazioni sulla copertura per il traffico in Mappe di Azure
author: kgremban
ms.author: kgremban
ms.date: 11/28/2017
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: a6fc1a8f050c3b1f546b4d0150f14b2cbebc31d0
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/01/2018
ms.locfileid: "34599707"
---
# <a name="azure-maps-traffic-coverage"></a>Copertura per il traffico in Mappe di Azure

Mappe di Azure offre informazioni avanzate sul traffico sotto forma di **flusso** del traffico e **incidenti** stradali. Questi dati possono essere visualizzati sulle mappe o usati per generare itinerari più intelligenti che tengano in considerazione le condizioni di guida reali. 

Mappe non ha tuttavia lo stesso livello di informazioni e accuratezza per tutte le aree. La tabella seguente indica la tipologia di informazioni sul traffico che è possibile richiedere per ogni area: 

|Region  |Eventi imprevisti  |Flusso  |
|---------|:---------:|:---------:|
|Argentina      |         |✓         |
|Australia     |✓         |✓        |
|Austria     |✓         |✓         |
|Bahrain     |         |✓         |
|Belgio     |✓         |✓         |
|Brasile     |✓         |✓         |
|Bulgaria     |✓         |✓         |
|Canada     |✓         |✓         |
|Cile     |✓         |✓         |
|Colombia      |         |✓         |
|Croazia     |         |✓         |
|Repubblica ceca     |✓         |✓         |
|Danimarca     |✓         |✓         |
|Egitto     |         |✓         |
|Estonia     |         | ✓        |
|Finlandia     |✓         |✓         |
|+Isole Åland      |✓         |✓         |
|Francia     |✓         |✓         |
|+Principato di Monaco     |✓         |✓         |
|Germania     |✓         |✓         |
|Grecia     |✓         |✓         |
|RAS di Hong Kong     |✓         |✓         |
|Ungheria     |✓         |✓         |
|Islanda     |         |✓         |
|Indonesia     |✓         |✓         |
|Irlanda, Repubblica     |✓         |✓         |
|Israele     |         |✓         |
|Italia     |✓         |✓        |
|+San Marino     |✓         |✓         |
|Kuwait     |✓         |✓         |
|Lettonia     |         |✓         |
|Lesotho     |✓         |✓         |
|Lituania     |         |✓         |
|Lussemburgo     |✓         |✓         |
|RAS di Macao     |         |✓         |
|Malaysia     |✓         |✓         |
|Malta     |✓         |✓         |
|Messico     |✓         |✓         |
|Marocco     |         |✓         |
|Paesi Bassi     |✓         |✓         |
|Nuova Zelanda     |✓         |✓         |
|Norvegia     |✓         |✓         |
|Oman     |         |✓         |
|Polonia     |✓         |✓         |
|Portogallo     |✓         |✓         |
|+Azzorre e Madeira     |         |✓         |
|Qatar     |         |✓         |
|Romania     |         |✓         |
|Federazione russa     |✓         |✓         |
|Arabia Saudita     |✓         |✓         |
|Singapore     |✓         |✓         |
|Slovacchia     |✓         |✓         |
|Slovenia     |✓         |✓         |
|Sud Africa     |✓         |✓         |
|Spagna     |✓         |✓         |
|+Andorra     |✓         |✓         |
|+Isole Baleari     |✓         |✓         |
|+Isole Canarie     |✓         |✓         |
|+Gibilterra     |✓         |✓         |
|Svezia     |✓         |✓         |
|Svizzera     |✓         |✓        |
|+Liechtenstein      |✓         |✓         |
|Taiwan     |✓         |✓        |
|Thailandia     |✓         |✓        |
|Turchia     |✓         |✓         |
|Ucraina     |✓         |✓         |
|Emirati Arabi Uniti     |✓         |✓         |
|Regno Unito     |✓         |✓         |
|(Guernsey & Jersey)     |✓         |✓         |
|Isola di Man     |✓         |✓         |
|Stati Uniti     |✓         |✓        |
|+Portorico     |✓         |✓         |

Per altre informazioni sui dati relativi al traffico di Mappe di Azure, vedere le pagine di riferimento sul [traffico](https://docs.microsoft.com/rest/api/maps/traffic).