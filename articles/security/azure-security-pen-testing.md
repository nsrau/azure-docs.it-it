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
ms.date: 12/08/2017
ms.author: yurid
ms.openlocfilehash: dd5c874406ec184a2526a9eb0843cd6f3b6b3aa1
ms.sourcegitcommit: 094061b19b0a707eace42ae47f39d7a666364d58
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/08/2017
---
# <a name="pen-testing"></a>Test di penetrazione
Uno dei vantaggi che si ottengono usando Azure per il test e la distribuzione delle applicazioni consiste nella creazione rapida degli ambienti.  Non è necessario richiedere, acquistare e installare il proprio hardware locale.

È tuttavia necessario continuare ad applicare la normale due diligence in materia di sicurezza. Una delle operazioni da eseguire è il test di penetrazione delle applicazioni distribuite in Azure.

Forse si è già a conoscenza del fatto che Microsoft esegue [il test di penetrazione dell'ambiente di Azure](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e), utile per ottenere miglioramenti in Azure. 

Il test di penetrazione delle applicazioni non viene eseguito automaticamente, ma è ovvio che è auspicabile e necessario eseguirlo. Questo è positivo, perché quando si migliora la sicurezza delle applicazioni, l'intero ecosistema di Azure diventa più sicuro.

Cosa fare?

A partire da 15 giugno 2017, Microsoft non richiede l'approvazione per condurre un penetrazione test su risorse di Azure. I clienti che desiderano formalmente documento future del test di penetrazione appuntamenti in Microsoft Azure sono invitati a compilare il [modulo di notifica di test di penetrazione del servizio Azure](https://portal.msrc.microsoft.com/engage/pentest). Questo processo è correlato solo in Microsoft Azure e non è applicabile a qualsiasi altro servizio Cloud di Microsoft. 

>[!IMPORTANT] 
>Durante la notifica di Microsoft della penna le attività di test non è più necessario comunque devono conformarsi i clienti di [Microsoft Cloud unificata penetrazione test regole di comportamento](https://technet.microsoft.com/en-us/mt784683). 

I test standard che è possibile eseguire includono:

* Test sugli endpoint per scoprire le [prime 10 vulnerabilità OWASP (Open Web Application Security Project)](https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project)
* [Test con dati casuali](https://blogs.microsoft.com/cybertrust/2007/09/20/fuzz-testing-at-microsoft-and-the-triage-process/) degli endpoint
* [Port scanning](https://en.wikipedia.org/wiki/Port_scanner) degli endpoint

Tra i tipi di test che non è possibile eseguire sono incluse tutte le tipologie di attacco [Denial of Service (DoS)](https://en.wikipedia.org/wiki/Denial-of-service_attack), tra cui l'avvio di un attacco DoS vero e proprio o l'esecuzione di test correlati che possono determinare, dimostrare o simulare tutti i tipi di attacco DoS.

## <a name="next-steps"></a>Passaggi successivi

- Si è pronti a iniziare il test di penetrazione delle applicazioni ospitate in Microsoft Azure? Se la risposta è sì, andare alla pagina [Panoramica del test di penetrazione](https://technet.microsoft.com/library/mt784683.aspx) e fare clic sul pulsante per la creazione di una richiesta di test alla fine della pagina. 