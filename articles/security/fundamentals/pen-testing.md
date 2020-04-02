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
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/13/2018
ms.author: terrylan
ms.openlocfilehash: 413ec8b121838a4ffac4119421ec3266e141618b
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/02/2020
ms.locfileid: "80549259"
---
# <a name="penetration-testing"></a>Test di penetrazione
Uno dei vantaggi che si ottengono usando Azure per il test e la distribuzione delle applicazioni consiste nella creazione rapida degli ambienti. Non è necessario richiedere, acquistare e installare il proprio hardware locale.

È tuttavia necessario continuare ad applicare la normale due diligence in materia di sicurezza. Una delle cose che probabilmente si vuole fare è test di penetrazione le applicazioni distribuite in Azure.One of the likely want to do is penetration test the applications you deploy in Azure.

Forse si è già a conoscenza del fatto che Microsoft esegue [il test di penetrazione dell'ambiente di Azure](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e), utile per ottenere miglioramenti in Azure.

Non forniamo test di penetrazione l'applicazione per voi, ma sappiamo che si desidera e necessario eseguire test sulle proprie applicazioni. Questa è una buona cosa, perché quando si migliora la sicurezza delle applicazioni si contribuisce a rendere l'intero ecosistema di Azure più sicuro.

A partire dal 15 giugno 2017, Microsoft non richiede più l'approvazione preliminare per eseguire un test di penetrazione sulle risorse di Azure.At of June 15, 2017, Microsoft no longer requires pre-approval to conduct a penetration test against Azure resources. I clienti che desiderano documentare formalmente i test di penetrazione previsti su Microsoft Azure sono invitati a compilare il [modulo di notifica dei test di penetrazione sul servizio Azure](https://portal.msrc.microsoft.com/en-us/engage/pentest). Questo processo interessa esclusivamente Microsoft Azure e non è applicabile ad alcun altro servizio Microsoft Cloud.

>[!IMPORTANT]
>Anche se la notifica dei test di penetrazione Microsoft non è più necessaria, i clienti devono comunque conformarsi alle [Regole unificate di partecipazione ai test di penetrazione di Microsoft Cloud](https://technet.microsoft.com/mt784683).

I test standard che è possibile eseguire includono:

* Test sugli endpoint per scoprire le [prime 10 vulnerabilità OWASP (Open Web Application Security Project)](https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project)
* [Test con dati casuali](https://cloudblogs.microsoft.com/microsoftsecure/2007/09/20/fuzz-testing-at-microsoft-and-the-triage-process/) degli endpoint
* [Port scanning](https://en.wikipedia.org/wiki/Port_scanner) degli endpoint

Tra i tipi di test che non è possibile eseguire sono incluse tutte le tipologie di attacco [Denial of Service (DoS)](https://en.wikipedia.org/wiki/Denial-of-service_attack), tra cui l'avvio di un attacco DoS vero e proprio o l'esecuzione di test correlati che possono determinare, dimostrare o simulare tutti i tipi di attacco DoS.

## <a name="next-steps"></a>Passaggi successivi

- Se si desidera documentare formalmente un test di penetrazione imminente rispetto alle applicazioni ospitate in Microsoft Azure, passare alle regole di engagement dei test di [penetrazione](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=2) e compilare il modulo di notifica di test.
