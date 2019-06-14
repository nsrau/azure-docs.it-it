---
title: Test di penetrazione | Microsoft Docs
description: L'articolo offre una panoramica del processo del test di penetrazione e di come eseguire il test di penetrazione sulle app in esecuzione nell'infrastruttura di Azure.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: TomSh
ms.assetid: 695d918c-a9ac-4eba-8692-af4526734ccc
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/13/2018
ms.author: barclayn
ms.openlocfilehash: 8835c4534b6dab1e8dbfb3546257ae4bc3b9d7af
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60610934"
---
# <a name="penetration-testing"></a>Test di penetrazione
Uno dei vantaggi che si ottengono usando Azure per il test e la distribuzione delle applicazioni consiste nella creazione rapida degli ambienti. Non è necessario richiedere, acquistare e installare il proprio hardware locale.

È tuttavia necessario continuare ad applicare la normale due diligence in materia di sicurezza. Uno degli aspetti probabile che si voglia eseguire è penetrazione testare le applicazioni si distribuiscono in Azure.

Forse si è già a conoscenza del fatto che Microsoft esegue [il test di penetrazione dell'ambiente di Azure](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e), utile per ottenere miglioramenti in Azure.

Non è il test di penetrazione dell'applicazione, ma è ovvio che si sarà necessario ed è necessario eseguire il test delle applicazioni. Che è positivo, perché quando si migliora la sicurezza delle applicazioni contribuire a rendere più sicuro l'intero ecosistema di Azure.

A partire dal 15 giugno 2017, Microsoft non richiede approvazione preventiva per condurre un test di penetrazione su risorse di Azure. I clienti che desiderano documentare formalmente i test di penetrazione previsti su Microsoft Azure sono invitati a compilare il [modulo di notifica dei test di penetrazione sul servizio Azure](https://portal.msrc.microsoft.com/en-us/engage/pentest). Questo processo interessa esclusivamente Microsoft Azure e non è applicabile ad alcun altro servizio Microsoft Cloud.

>[!IMPORTANT]
>Anche se la notifica dei test di penetrazione Microsoft non è più necessaria, i clienti devono comunque conformarsi alle [Regole unificate di partecipazione ai test di penetrazione di Microsoft Cloud](https://technet.microsoft.com/mt784683).

I test standard che è possibile eseguire includono:

* Test sugli endpoint per scoprire le [prime 10 vulnerabilità OWASP (Open Web Application Security Project)](https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project)
* [Test con dati casuali](https://cloudblogs.microsoft.com/microsoftsecure/2007/09/20/fuzz-testing-at-microsoft-and-the-triage-process/) degli endpoint
* [Port scanning](https://en.wikipedia.org/wiki/Port_scanner) degli endpoint

Tra i tipi di test che non è possibile eseguire sono incluse tutte le tipologie di attacco [Denial of Service (DoS)](https://en.wikipedia.org/wiki/Denial-of-service_attack), tra cui l'avvio di un attacco DoS vero e proprio o l'esecuzione di test correlati che possono determinare, dimostrare o simulare tutti i tipi di attacco DoS.

## <a name="next-steps"></a>Passaggi successivi

- Se si desidera documentare formalmente un' test di penetrazione delle applicazioni ospitate in Microsoft Azure, andare al [penetrazione test regole di accordo](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=2) e compilare il modulo di notifica di test.
