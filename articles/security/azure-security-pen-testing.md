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
ms.date: 08/24/2017
ms.author: yurid
ms.translationtype: HT
ms.sourcegitcommit: 646886ad82d47162a62835e343fcaa7dadfaa311
ms.openlocfilehash: 070e848f753452953b9e5dfe94799e7c0a314530
ms.contentlocale: it-it
ms.lasthandoff: 08/24/2017

---
# <a name="pen-testing"></a>Test di penetrazione
Uno dei maggiori vantaggi dell'uso di Microsoft Azure per il test e la distribuzione di applicazioni è che non è necessaria un'infrastruttura locale per sviluppare, testare e distribuire le applicazioni. I servizi della piattaforma Microsoft Azure gestiscono l'intera infrastruttura. Non è necessario richiedere, acquistare e installare il proprio hardware locale.

È tuttavia necessario continuare ad applicare la normale due diligence in materia di sicurezza. Una delle operazioni da eseguire è il test di penetrazione delle applicazioni distribuite in Azure.

Forse si è già a conoscenza del fatto che Microsoft esegue [il test di penetrazione dell'ambiente di Azure](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e), che consente di ottimizzare la piattaforma e di determinare le azioni mirate a migliorare i controlli di sicurezza, introdurre nuovi controlli di sicurezza e perfezionare i processi di sicurezza.

Il test di penetrazione delle applicazioni non viene eseguito automaticamente, ma è ovvio che è auspicabile e necessario eseguirlo. Questo è positivo, perché quando si migliora la sicurezza delle applicazioni, l'intero ecosistema di Azure diventa più sicuro.

Quando si esegue il test di penetrazione delle applicazioni, potrebbe sembrare un attacco a Microsoft. I modelli di attacco vengono [continuamente monitorati](http://blogs.msdn.com/b/azuresecurity/archive/2015/07/05/best-practices-to-protect-your-azure-deployment-against-cloud-drive-by-attacks.aspx) e, se necessario, verrà avviato un processo di risposta agli eventi imprevisti, ma l'attivazione di una risposta agli eventi imprevisti in seguito a un test di penetrazione basato sulla due diligence dell'utente non è utile a nessuno.

Cosa fare?

Quando si è pronti per eseguire il test di penetrazione delle applicazioni ospitate in Azure, [comunicarlo a Microsoft](https://portal.msrc.microsoft.com/en-us/engage/pentest). Sapendo che stanno per essere eseguiti test specifici, Microsoft eviterà di bloccare inavvertitamente gli utenti (ad esempio, bloccando l'indirizzo IP da cui si esegue il test), purché i test siano conformi ai termini e alle condizioni per i test di penetrazione di Azure descritti in [Microsoft Cloud Unified Penetration Testing Rules of Engagement](https://technet.microsoft.com/en-us/mt784683) (Regole d'ingaggio del test di penetrazione unificato di Microsoft Cloud).
I test standard che è possibile eseguire includono:

* Test sugli endpoint per scoprire le [prime 10 vulnerabilità OWASP (Open Web Application Security Project)](https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project)
* [Test con dati casuali](https://blogs.microsoft.com/cybertrust/2007/09/20/fuzz-testing-at-microsoft-and-the-triage-process/) degli endpoint
* [Port scanning](https://en.wikipedia.org/wiki/Port_scanner) degli endpoint

Tra i tipi di test che non è possibile eseguire sono incluse tutte le tipologie di attacco [Denial of Service (DoS)](https://en.wikipedia.org/wiki/Denial-of-service_attack), tra cui l'avvio di un attacco DoS vero e proprio o l'esecuzione di test correlati che possono determinare, dimostrare o simulare tutti i tipi di attacco DoS.

Si è pronti a iniziare il test di penetrazione delle applicazioni ospitate in Microsoft Azure? Se la risposta è sì, andare alla pagina [Panoramica del test di penetrazione](https://technet.microsoft.com/library/mt784683.aspx) e fare clic sul pulsante per la creazione di una richiesta di test alla fine della pagina. Sono disponibili anche altre informazioni sui termini e sulle condizioni del test di penetrazione e utili collegamenti per poter segnalare i problemi di sicurezza correlati ad Azure o ad altri servizi Microsoft.

