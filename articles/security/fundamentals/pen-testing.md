---
title: Test di penetrazione | Microsoft Docs
description: Questo articolo fornisce una panoramica del processo di test di penetrazione e illustra come eseguire un test di penna sull'app in esecuzione nell'infrastruttura di Azure.
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
ms.date: 08/24/2020
ms.author: terrylan
ms.openlocfilehash: af61b6ee1e69d175f47170df30f75832033d61d5
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/02/2020
ms.locfileid: "96489718"
---
# <a name="penetration-testing"></a>Test di penetrazione

Uno dei vantaggi che si ottengono usando Azure per il test e la distribuzione delle applicazioni consiste nella creazione rapida degli ambienti. Non è necessario richiedere, acquistare e installare il proprio hardware locale.

La creazione rapida di ambienti è un'ottima soluzione, ma è comunque necessario assicurarsi di eseguire la normale sicurezza dovuta alla diligenza. Una delle operazioni che probabilmente si desidera eseguire è il test di penetrazione delle applicazioni distribuite in Azure.

Forse si è già a conoscenza del fatto che Microsoft esegue [il test di penetrazione dell'ambiente di Azure](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e), Questo test aiuta a migliorare i miglioramenti di Azure.

I test di penetrazione dell'applicazione non vengono eseguiti per l'utente, ma è importante comprendere che si vuole ed è necessario eseguire il test delle applicazioni. Questo è un aspetto positivo, perché quando si migliora la sicurezza delle applicazioni, è possibile rendere più sicuro l'intero ecosistema di Azure.

A partire dal 15 giugno 2017, Microsoft non richiede più la pre-approvazione per condurre un test di penetrazione sulle risorse di Azure. Questo processo interessa esclusivamente Microsoft Azure e non è applicabile ad alcun altro servizio Microsoft Cloud.

>[!IMPORTANT]
>Anche se la notifica dei test di penetrazione Microsoft non è più necessaria, i clienti devono comunque conformarsi alle [Regole unificate di partecipazione ai test di penetrazione di Microsoft Cloud](https://technet.microsoft.com/mt784683).

I test standard che è possibile eseguire includono:

* Test sugli endpoint per scoprire le [prime 10 vulnerabilità OWASP (Open Web Application Security Project)](https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project)
* [Test con dati casuali](https://cloudblogs.microsoft.com/microsoftsecure/2007/09/20/fuzz-testing-at-microsoft-and-the-triage-process/) degli endpoint
* [Port scanning](https://en.wikipedia.org/wiki/Port_scanner) degli endpoint

Un tipo di test di penna che non è possibile eseguire è qualsiasi tipo di attacco [Denial of Service (DOS)](https://en.wikipedia.org/wiki/Denial-of-service_attack) . Questo test include l'avvio di un attacco DoS o l'esecuzione di test correlati che potrebbero determinare, dimostrare o simulare qualsiasi tipo di attacco DoS.

>[!Note]
>Microsoft ha collaborato con BreakingPoint Cloud per compilare un'interfaccia in cui è possibile generare il traffico per abilitare la protezione DDoS agli indirizzi IP pubblici per simulazioni. Per altre informazioni sulla simulazione cloud dei punti di interruzione, vedere [Validate DDoS Detection](../../ddos-protection/manage-ddos-protection.md#validate-and-test).

## <a name="next-steps"></a>Passaggi successivi

* Altre informazioni sulle [regole di test di penetrazione di engagement](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=2).