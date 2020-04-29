---
title: Regole per la denominazione di entità Azure Data Factory
description: Descrive le regole di denominazione per le entità di Data factory.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/16/2018
ms.openlocfilehash: f922ada663391cf65a61f4e18bba53668f9c4a1a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81419409"
---
# <a name="azure-data-factory---naming-rules"></a>Azure Data Factory - Regole di denominazione

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

La tabella seguente specifica le regole di denominazione per gli elementi di Data factory.

| Name | Univocità del nome | Controlli di convalida |
|:--- |:--- |:--- |
| Data Factory |Univoco in Microsoft Azure. Per i nomi non viene fatta distinzione tra maiuscole e minuscole: `MyDF` e `mydf`, ad esempio, fanno riferimento alla stessa data factory. |<ul><li>Ogni data factory è collegata a una sola sottoscrizione di Azure.</li><li>I nomi degli oggetti devono iniziare con una lettera o un numero e possono contenere solo lettere, numeri e il carattere trattino (-).</li><li>Ogni carattere trattino (-) deve essere immediatamente preceduto e seguito da una lettera o un numero. Nei nomi di contenitori non sono consentiti trattini consecutivi.</li><li>Il nome può contenere da 3 a 63 caratteri.</li></ul> |
| Servizi collegati/set di dati/pipeline |Univoco in una data factory. Per i nomi viene fatta distinzione tra maiuscole e minuscole. |<ul><li>I nomi degli oggetti devono iniziare con una lettera, un numero o un carattere di sottolineatura (_).</li><li>Non sono ammessi i caratteri seguenti: ".", "+", "?", "/", "<", ">", "*", "%", "&", ":", "\\".</li><li>I trattini ("-") non sono ammessi solo nei nomi di servizi collegati e di set di dati.</li></ul>  |
| Gruppo di risorse |Univoco in Microsoft Azure. Per i nomi viene fatta distinzione tra maiuscole e minuscole. | Per altre informazioni, vedere [Regole di denominazione e restrizioni](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging#resource-naming). |

## <a name="next-steps"></a>Passaggi successivi
Informazioni su come creare data factory seguendo le istruzioni dettagliate riportate nell'articolo [Guida introduttiva: creazione di una data factory](quickstart-create-data-factory-powershell.md). 
