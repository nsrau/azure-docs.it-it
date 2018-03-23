---
title: Proteggere i dati personali in Microsoft Azure | Microsoft Docs
description: Questo articolo illustra come usare Azure per proteggere i dati personali e assicurare la conformità al Regolamento generale sulla protezione dei dati (RGPD).
services: security
documentationcenter: na
author: Barclayn
manager: MBaldwin
editor: TomSh
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/06/2018
ms.author: barclayn
ms.custom: ''
ms.openlocfilehash: 741fb17be315faacef6483cbaaa565136622cb45
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/08/2018
---
# <a name="protect-personal-data-in-microsoft-azure"></a>Proteggere i dati personali in Microsoft Azure

Questo articolo è il primo di una serie di articoli che illustrano come usare le tecnologie e i servizi di sicurezza di Azure per proteggere i dati personali. Si tratta di un requisito fondamentale per molte aziende e iniziative di governance e conformità. È ad esempio possibile usare le informazioni disponibili in questa serie di articoli per assicurare la conformità al Regolamento generale sulla protezione dei dati (RGPD). L'articolo descrive anche lo scenario, la presentazione del problema e gli obiettivi aziendali.

## <a name="scenario-and-problem-statement"></a>Scenario e presentazione del problema

Un'importante compagnia di viaggi in crociera, con sede negli Stati Uniti, sta espandendo le proprie operazioni per offrire itinerari nel Mar Mediterraneo e nel Mar Baltico, nonché nelle isole britanniche. Per supportare tale iniziativa, ha acquistato diverse linee minori con sede in Italia, Germania, Danimarca e Regno Unito.

La società usa Microsoft Azure per archiviare i dati aziendali nel cloud. I dati possono includere informazioni su clienti e/o dipendenti, tra cui:

- Indirizzi
- Numeri di telefono
- Codici fiscali
- Dati delle carte di credito

La società deve proteggere la privacy dei dati dei propri clienti e dipendenti rendendoli accessibili ai reparti che ne hanno bisogno, ad esempio quelli che si occupano di retribuzioni e prenotazioni.

## <a name="company-goals"></a>Obiettivi dell'azienda 

- Le origini dati che contengono dati personali vengono crittografate quando che risiedono nell'archiviazione cloud.

- I dati personali che vengono trasferiti da una posizione all'altra vengono crittografati durante il transito. Ciò accade se i dati vengono trasferiti attraverso la rete virtuale o in Internet tra i data center aziendali e il cloud di Azure.

- La riservatezza e l'integrità dei dati personali vengono protette da accessi non autorizzati grazie a tecnologie avanzate di gestione delle identità e controllo di accesso.

- I dati personali vengono protetti dall'esposizione dovuta alla violazione dei dati grazie al monitoraggio di minacce e vulnerabilità.

- Lo stato di sicurezza dei servizi di Azure che archiviano o trasmettono dati personali viene valutato per identificare le opportunità di ottimizzazione della protezione di tali dati.

## <a name="data-protection-guidance"></a>Materiale sussidiario sulla protezione dei dati

Gli articoli seguenti contengono indicazioni sulle procedure tecniche che consentono di raggiungere gli obiettivi di protezione dei dati personali elencati in precedenza:

- [Tecnologie di crittografia di Azure](protect-personal-data-at-rest.md)

- [Tecnologie di crittografia di Azure](protect-personal-data-in-transit-encryption.md)

- [Tecnologie di gestione delle identità e degli accessi di Azure](protect-personal-data-identity-access-controls.md)

- [Tecnologie di sicurezza di rete di Azure](protect-personal-data-network-security.md)

- [Centro sicurezza di Azure](protect-personal-data-azure-security-center.md)



## <a name="next-steps"></a>Passaggi successivi

- [Sito di informazioni sulla sicurezza di Azure](https://aka.ms/AzureSecInfo)

- [Centro protezione Microsoft](https://www.microsoft.com/TrustCenter/default.aspx)

- [Centro sicurezza di Azure](https://azure.microsoft.com/services/security-center/)

- [Blog del team di sicurezza di Azure](https://www.azuresecurityorg)

- [Blog di Azure.com - Sicurezza](https://azure.microsoft.com/blog/topics/security/)
