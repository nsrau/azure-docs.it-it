---
title: Trasformazione Chiave surrogata per il flusso di dati di mapping di Azure Data Factory
description: Trasformazione Chiave surrogata per il flusso di dati di mapping di Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/12/2019
ms.openlocfilehash: 6243905857f0450168541f556636d90bb4d855f7
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/23/2019
ms.locfileid: "56734949"
---
# <a name="azure-data-factory-mapping-data-flow-surrogate-key-transformation"></a>Trasformazione Chiave surrogata per il flusso di dati di mapping di Azure Data Factory

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Usare la trasformazione Chiave surrogata per aggiungere un valore di chiave arbitrario non business incrementale al set di righe del flusso di dati. Ciò è utile quando si progettano le tabelle delle dimensioni in un modello di dati analitici con schema star in cui ogni membro nelle tabelle delle dimensioni deve avere una chiave univoca non business, parte della metodologia di data warehousing Kimball.

![Trasformazione Chiave surrogata](media/data-flow/surrogate.png "Trasformazione Chiave surrogata")

"Key Column" (Colonna chiave) è il nome che verrà assegnato alla nuova colonna chiave surrogata.

"Start Value" (Valore iniziale) è il punto di inizio del valore incrementale.
