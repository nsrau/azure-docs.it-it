---
title: Rilevamento minacce - Database SQL di Azure | Documentazione Microsoft
description: "La funzionalità di rilevamento delle minacce individua le attività di database che indicano la presenza di potenziali minacce alla sicurezza nel database."
services: sql-database
documentationcenter: 
author: ronitr
manager: jhubbard
editor: v-romcal
ms.assetid: b50d232a-4225-46ed-91e7-75288f55ee84
ms.service: sql-database
ms.custom: secure and protect
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 07/10/2016
ms.author: ronmat; ronitr
translationtype: Human Translation
ms.sourcegitcommit: 5d51a5ef3387b4c00079547b0f44ffe1f96bd77c
ms.openlocfilehash: eadaa3e7a279b6b92da1d0c026c3002297dfd298
ms.lasthandoff: 02/17/2017


---
# <a name="sql-database-threat-detection"></a>Rilevamento delle minacce nel database SQL

La funzionalità di rilevamento delle minacce individua le attività di database che indicano la presenza di potenziali minacce alla sicurezza nel database.  La funzionalità di rilevamento delle minacce è attualmente in anteprima.

## <a name="overview"></a>Panoramica

Il rilevamento delle minacce offre un nuovo livello di protezione, che consente ai clienti di rilevare e rispondere alle minacce potenziali non appena si verificano, fornendo avvisi di sicurezza sulle attività anomale.  Gli utenti possono esaminare gli eventi sospetti tramite il [servizio di controllo del database SQL](sql-database-auditing.md) per determinare se sono il risultato di un tentativo di accesso, una violazione o un exploit dei dati nel database.
Il rilevamento delle minacce rende più semplice affrontare le minacce potenziali al database, senza dover essere esperti della sicurezza o gestire sistemi di controllo di sicurezza avanzati.

Ad esempio, la funzionalità di rilevamento delle minacce individua determinate attività anomale nel database che indicano potenziali tentativi di attacco SQL injection. L'attacco SQL injection è uno dei problemi di sicurezza comuni delle applicazioni Web su Internet, che viene usato per attaccare le applicazioni guidate dai dati. Gli autori di attacchi sfruttano le vulnerabilità delle applicazioni per introdurre istruzioni SQL dannose nei campi di immissione dell'applicazione, per violare o modificare i dati nel database.

## <a name="next-steps"></a>Passaggi successivi

* Per configurare e gestire il rilevamento delle minacce, vedere [Configure and manage threat detection in the Azure portal](sql-database-threat-detection-portal.md) (Configurare e gestire il rilevamento delle minacce nel portale di Azure).

