---
title: Note sulla versione di Azure Stack Edge & Azure Data Box Gateway 2007 | Microsoft Docs
description: Descrive i problemi e le risoluzioni critici per il Azure Stack Edge e Data Box Gateway l'esecuzione del rilascio 2007.
services: databox
author: twooley
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 07/14/2020
ms.author: twooley
ms.openlocfilehash: f9a08cda1466dbe284a0ac3c0e34b109be236883
ms.sourcegitcommit: 9c3cfbe2bee467d0e6966c2bfdeddbe039cad029
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/24/2020
ms.locfileid: "88783927"
---
# <a name="azure-stack-edge-and-azure-data-box-gateway-2007-release-notes"></a>Note sulla versione di Azure Stack Edge e Azure Data Box Gateway 2007

Le seguenti note sulla versione identificano i problemi critici aperti e i problemi risolti per la versione 2007 per Azure Stack Edge e Data Box Gateway.

Le note sulla versione vengono aggiornate continuamente e i problemi che richiedono una soluzione alternativa vengono aggiunti man mano che vengono individuati. Prima di distribuire il Azure Stack Edge/Data Box Gateway, esaminare attentamente le informazioni contenute nelle note sulla versione.

Questa versione corrisponde alle versioni del software:

- **Azure stack Edge 2007 (1.6.1280.1667)** -KB 4566549
- **Data Box Gateway 2007 (1.6.1280.1667)** -KB 4566550

> [!NOTE]
> L'aggiornamento 2007 può essere applicato solo a tutti i dispositivi che eseguono versioni di disponibilità generale (GA) del software o versione successiva.

## <a name="whats-new"></a>Novità

Questa versione include la correzione di bug seguente:

- **Problema di caricamento** : questa versione corregge un problema di caricamento in cui i riavvii del caricamento a causa di errori possono rallentare la frequenza di completamento del caricamento. Questo problema può verificarsi quando si carica un set di dati costituito principalmente da file di dimensioni elevate rispetto alla larghezza di banda disponibile, in particolare, ma non limitato, quando la limitazione della larghezza di banda è attiva. Questa modifica garantisce che venga fornita un'opportunità sufficiente per il completamento del caricamento prima di riavviare il caricamento per un determinato file.

Questa versione contiene anche gli aggiornamenti seguenti:

- L'immagine di base per il disco rigido virtuale Windows è stata aggiornata.
- Sono inclusi tutti gli aggiornamenti cumulativi di Windows e gli aggiornamenti di .NET Framework rilasciati fino al 2020 maggio.
- Questa versione supporta IoT Edge 1.0.9.3 nei dispositivi Azure Stack Edge.

## <a name="known-issues-in-this-release"></a>Problemi noti in questa versione

Non sono stati rilasciati nuovi problemi per questa versione. Tutti i problemi rilasciati sono stati rilasciati dalle versioni precedenti. Per visualizzare un elenco di problemi noti, vedere [problemi noti nella versione GA](data-box-gateway-release-notes.md#known-issues-in-ga-release).

## <a name="next-steps"></a>Passaggi successivi

- [Preparare la distribuzione di Azure Stack Edge](data-box-edge-deploy-prep.md)
- [Preparare la distribuzione di Azure Data Box Gateway](data-box-gateway-deploy-prep.md)
