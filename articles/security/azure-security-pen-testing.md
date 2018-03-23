---
title: Test di penetrazione | Microsoft Docs
description: L'articolo offre una panoramica del processo del test di penetrazione e di come eseguire il test di penetrazione sulle app in esecuzione nell'infrastruttura di Azure.
services: security
documentationcenter: na
author: YuriDio
manager: swadhwa
editor: TomSh
ms.assetid: 695d918c-a9ac-4eba-8692-af4526734ccc
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/13/2018
ms.author: barclayn
ms.openlocfilehash: 5f2a72d133218e72d1db86b787c998cc50269d3a
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/16/2018
---
# <a name="pen-testing"></a>Test di penetrazione
Uno dei vantaggi che si ottengono usando Azure per il test e la distribuzione delle applicazioni consiste nella creazione rapida degli ambienti.  Non è necessario richiedere, acquistare e installare il proprio hardware locale.

È tuttavia necessario continuare ad applicare la normale due diligence in materia di sicurezza. Una delle operazioni da eseguire è il test di penetrazione delle applicazioni distribuite in Azure.

Forse si è già a conoscenza del fatto che Microsoft esegue [il test di penetrazione dell'ambiente di Azure](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e), utile per ottenere miglioramenti in Azure. 

Il test di penetrazione delle applicazioni non viene eseguito automaticamente, ma è ovvio che è auspicabile e necessario eseguirlo. Questo è positivo, perché quando si migliora la sicurezza delle applicazioni, l'intero ecosistema di Azure diventa più sicuro.

Cosa fare?

A partire dal 15 giugno 2017, Microsoft non richiede più alcuna approvazione preventiva per condurre un test di penetrazione su risorse di Azure. I clienti che desiderano documentare formalmente i test di penetrazione previsti su Microsoft Azure sono invitati a compilare il [modulo di notifica dei test di penetrazione sul servizio Azure](https://portal.msrc.microsoft.com/en-us/engage/pentest). Questo processo interessa esclusivamente Microsoft Azure e non è applicabile ad alcun altro servizio Microsoft Cloud. 

>[!IMPORTANT] 
>Anche se la notifica dei test di penetrazione Microsoft non è più necessaria, i clienti devono comunque conformarsi alle [Regole unificate di partecipazione ai test di penetrazione di Microsoft Cloud](https://technet.microsoft.com/en-us/mt784683). 

I test standard che è possibile eseguire includono:

* Test sugli endpoint per scoprire le [prime 10 vulnerabilità OWASP (Open Web Application Security Project)](https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project)
* [Test con dati casuali](https://blogs.microsoft.com/cybertrust/2007/09/20/fuzz-testing-at-microsoft-and-the-triage-process/) degli endpoint
* [Port scanning](https://en.wikipedia.org/wiki/Port_scanner) degli endpoint

Tra i tipi di test che non è possibile eseguire sono incluse tutte le tipologie di attacco [Denial of Service (DoS)](https://en.wikipedia.org/wiki/Denial-of-service_attack), tra cui l'avvio di un attacco DoS vero e proprio o l'esecuzione di test correlati che possono determinare, dimostrare o simulare tutti i tipi di attacco DoS.

## <a name="next-steps"></a>Passaggi successivi

- Si è pronti a iniziare il test di penetrazione delle applicazioni ospitate in Microsoft Azure? Se la risposta è sì, andare alla pagina [Panoramica del test di penetrazione](https://technet.microsoft.com/library/mt784683.aspx) e fare clic sul pulsante per la creazione di una richiesta di test alla fine della pagina. 