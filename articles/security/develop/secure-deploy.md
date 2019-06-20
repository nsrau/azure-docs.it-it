---
title: Distribuisci applicazioni sicure in Microsoft Azure
description: Questo articolo illustra le procedure consigliate da prendere in considerazione durante le fasi di rilascio e di risposta del progetto di applicazione web.
author: TerryLanfear
manager: barbkess
ms.author: terrylan
ms.date: 06/12/2019
ms.topic: article
ms.service: security
services: azure
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: e8249113ee65c28414c79f00c53d11596673434b
ms.sourcegitcommit: 22c97298aa0e8bd848ff949f2886c8ad538c1473
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/14/2019
ms.locfileid: "67144451"
---
# <a name="deploy-secure-applications-on-azure"></a>Distribuisci applicazioni sicure in Azure
In questo articolo ti presentiamo le attività di sicurezza e controlli da considerare quando si distribuiscono applicazioni per il cloud. Domande di sicurezza e i concetti da considerare durante le fasi di rilascio e di risposta di Microsoft [Security Development Lifecycle (SDL)](https://msdn.microsoft.com/library/windows/desktop/84aed186-1d75-4366-8e61-8d258746bopq.aspx) vengono analizzati. L'obiettivo è che consentono di definire le attività e i servizi di Azure che è possibile usare per distribuire un'applicazione più sicura.

Le seguenti fasi SDL sono illustrate in questo articolo:

- Versione
- Risposta

## <a name="release"></a>Versione
Lo stato attivo della fase di rilascio è preparazione di un progetto per la versione pubblica.
Ciò include la pianificazione di modi per eseguire attività di manutenzione post-rilascio in modo efficace e risolvono le vulnerabilità di sicurezza che potrebbero verificarsi in un secondo momento.

### <a name="check-your-applications-performance-before-you-launch"></a>Controllare le prestazioni dell'applicazione prima di avviare

Controllare le prestazioni dell'applicazione prima di avviare il programma o distribuire gli aggiornamenti nell'ambiente di produzione. Esecuzione basata sul cloud [test di carico](https://www.visualstudio.com/docs/test/performance-testing/getting-started/getting-started-with-performance-testing) usando Visual Studio per trovare i problemi di prestazioni nell'applicazione, migliorare la qualità della distribuzione, assicurarsi che l'applicazione sia sempre aggiornata o disponibile e che l'applicazione può gestire il traffico per il lancio.

### <a name="install-a-web-application-firewall"></a>Installare un web application firewall

Le applicazioni Web sono sempre più vittime di attacchi che sfruttano le più comuni vulnerabilità note. Sono comuni tra tali exploit hanno attacchi SQL injection e attacchi di scripting intersito. Impedire gli attacchi nel codice dell'applicazione può risultare complessa. Potrebbe richiedere una manutenzione rigorosa, l'applicazione di patch e il monitoraggio a molti livelli della topologia dell'applicazione. Un Web Application firewall centralizzato consente di semplificare la gestione della sicurezza. Una soluzione WAF è anche reagire a una minaccia alla sicurezza per l'applicazione delle patch di una vulnerabilità nota in una posizione centrale anziché proteggere ogni applicazione web singoli.

Il [WAF del Gateway applicazione Azure](https://docs.microsoft.com/azure/application-gateway/waf-overview) offre una protezione centralizzata delle applicazioni web da exploit e vulnerabilità comuni. Il WAF si basa sulle regole dal [OWASP core rule set](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project) 3.0 o 2.2.9.

### <a name="create-an-incident-response-plan"></a>Creare un piano di risposta agli eventi imprevisti

Preparazione di un piano di risposta agli eventi imprevisti è fondamentale per soddisfare le nuove minacce che potrebbero emergere nel corso del tempo. Preparazione di un piano di risposta agli eventi imprevisti prevede l'identificazione di contatti per le emergenze di sicurezza appropriati e definizione di una protezione per il codice che viene ereditato da altri gruppi dell'organizzazione e per il codice di terze parti concessi in licenza i piani di manutenzione.

### <a name="conduct-a-final-security-review"></a>Condurre una revisione finale della protezione

Deliberatamente esaminato tutte le attività di sicurezza che sono state eseguite consente di garantire conformità per l'applicazione o il rilascio del software. La revisione finale della protezione (FSR) include in genere esaminando i modelli di rischio, gli output di strumenti e prestazioni in relazione i controlli di qualità e le barre di bug che sono state definite in fase di requisiti.

### <a name="certify-release-and-archive"></a>Certificare rilascio e archivio

Prima di una versione consente di garantire che siano soddisfatti i requisiti di sicurezza e privacy per la certificazione software. Tutti i dati pertinenti di archiviazione è essenziale per l'esecuzione di attività di manutenzione post-rilascio. L'archiviazione inoltre consente di ridurre i costi a lungo termine associati sostenuta ingegneria del software.

## <a name="response"></a>Risposta
La fase di post-rilascio risposta Centra nel team di sviluppo è in grado di e disponibile per rispondere in modo appropriato per tutti i report di minacce software emergenti e vulnerabilità.

### <a name="execute-the-incident-response-plan"></a>Esecuzione del piano di risposta agli eventi imprevisti

La possibilità di implementare il piano di risposta agli eventi imprevisti istituito nella fase di rilascio è essenziale per aiutare a proteggere i clienti dalle vulnerabilità del software di sicurezza o privacy che emergono.

### <a name="monitor-application-performance"></a>Monitoraggio prestazioni applicazione

Monitoraggio continuo dell'applicazione dopo la distribuzione potenzialmente ti aiuta a rilevare problemi di prestazioni, nonché le vulnerabilità di sicurezza.
Servizi di Azure che agevolano il monitoraggio dell'applicazione sono:

  - Azure Application Insights
  - Centro sicurezza di Azure

#### <a name="application-insights"></a>Application Insights

[Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-overview) è un servizio estendibile di gestione delle prestazioni delle applicazioni per sviluppatori Web su più piattaforme, che consente di monitorare un'applicazione Web live. Application Insights rileva automaticamente le anomalie nelle prestazioni. Include strumenti di analitica potenti che consentono di diagnosticare i problemi e comprendere ciò che effettivamente eseguite dagli utenti all'app. Il servizio è progettato per supportare il miglioramento continuo delle prestazioni e dell'usabilità.

#### <a name="azure-security-center"></a>Centro sicurezza di Azure

[Centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/security-center-intro) consente di prevenire, rilevare e gestire le minacce tramite livelli superiori di visibilità (e il controllo di) la sicurezza delle risorse di Azure, incluse le applicazioni web. Centro sicurezza di Azure aiuta a rilevare le minacce che altrimenti passerebbero inosservate. Si integra con varie soluzioni di sicurezza.

Il livello gratuito del Centro sicurezza offre sicurezza limitata solo per le risorse di Azure. Il [livello Standard del Centro sicurezza](https://docs.microsoft.com/azure/security-center/security-center-onboarding) estende tali funzionalità per le risorse locali e in altri cloud.
Standard del Centro sicurezza consente di:

  - Individuare e correggere le vulnerabilità di sicurezza.
  - Applicare i controlli di accesso e delle applicazioni per bloccare attività dannose.
  - Rilevare le minacce con intelligence e analitica.
  - Rispondere rapidamente in caso di attacco.

## <a name="next-steps"></a>Passaggi successivi
Vedere gli articoli seguenti, si consiglia di controlli di sicurezza e le attività che consentono di progettare e sviluppano applicazioni sicure.

- [Progettazione di applicazioni sicure](secure-design.md)
- [Sviluppo di applicazioni sicure](secure-develop.md)
