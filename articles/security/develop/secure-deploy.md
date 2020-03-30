---
title: Distribuire applicazioni sicure in Microsoft AzureDeploy secure applications on Microsoft Azure
description: In questo articolo vengono illustrate le procedure consigliate da considerare durante le fasi di rilascio e risposta del progetto di applicazione Web.
author: TerryLanfear
manager: barbkess
ms.author: terrylan
ms.date: 06/12/2019
ms.topic: article
ms.service: security
ms.subservice: security-develop
services: azure
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: dfe4f09d00a5629249a3041946190f56e83c3480
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "68934878"
---
# <a name="deploy-secure-applications-on-azure"></a>Distribuire applicazioni sicure in AzureDeploy secure applications on Azure
In questo articolo vengono presentate le attività di sicurezza e i controlli da considerare quando si distribuiscono applicazioni per il cloud. Domande sulla sicurezza e concetti da considerare durante le fasi di rilascio e risposta del Microsoft [Security Development Lifecycle (SDL)](https://msdn.microsoft.com/library/windows/desktop/84aed186-1d75-4366-8e61-8d258746bopq.aspx) sono trattati. L'obiettivo è definire le attività e i servizi di Azure che è possibile usare per distribuire un'applicazione più sicura.

In questo articolo vengono illustrate le fasi SDL seguenti:

- Versione
- Risposta

## <a name="release"></a>Versione
L'obiettivo della fase di rilascio è la preparazione di un progetto per il rilascio pubblico.
Ciò include la pianificazione di modi per eseguire in modo efficace le attività di manutenzione post-rilascio e risolvere le vulnerabilità di sicurezza che potrebbero verificarsi in un secondo momento.

### <a name="check-your-applications-performance-before-you-launch"></a>Controllare le prestazioni dell'applicazione prima di avviarla

Controllare le prestazioni dell'applicazione prima di avviarla o distribuire gli aggiornamenti nell'ambiente di produzione. Eseguire test di [carico](https://www.visualstudio.com/docs/test/performance-testing/getting-started/getting-started-with-performance-testing) basati su cloud utilizzando Visual Studio per individuare problemi di prestazioni nell'applicazione, migliorare la qualità della distribuzione, assicurarsi che l'applicazione sia sempre aggiornata o disponibile e che l'applicazione sia in grado di gestire il traffico per l'avvio.

### <a name="install-a-web-application-firewall"></a>Installare un web application firewall

Le applicazioni Web sono sempre più vittime di attacchi che sfruttano le più comuni vulnerabilità note. Comune tra questi exploit sono attacchi SQL injection e attacchi cross-site scripting. Prevenire questi attacchi nel codice dell'applicazione può essere difficile. Potrebbe richiedere una manutenzione rigorosa, l'applicazione di patch e il monitoraggio a molti livelli della topologia dell'applicazione. Un WAF centralizzato semplifica la gestione della sicurezza. Una soluzione WAF può anche reagire a una minaccia alla sicurezza applicando patch a una vulnerabilità nota in una posizione centrale rispetto alla protezione di ogni singola applicazione Web.

[Il WAF](../../application-gateway/waf-overview.md) di Gateway applicazione di Azure offre una protezione centralizzata delle applicazioni Web da exploit e vulnerabilità comuni. Il WAF si basa sulle regole dei set di [regole di base OWASP](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project) 3.0 o 2.2.9.

### <a name="create-an-incident-response-plan"></a>Creare un piano di risposta agli eventi imprevisti

La preparazione di un piano di risposta agli incidenti è fondamentale per affrontare le nuove minacce che potrebbero emergere nel tempo. La preparazione di un piano di risposta agli incidenti include l'identificazione dei contatti di emergenza di sicurezza appropriati e la definizione di piani di manutenzione della sicurezza per il codice ereditato da altri gruppi dell'organizzazione e per il codice di terze parti con licenza.

### <a name="conduct-a-final-security-review"></a>Condurre una revisione finale della sicurezza

La revisione deliberata di tutte le attività di sicurezza eseguite consente di garantire la disponibilità per la versione o l'applicazione del software. La revisione finale della sicurezza (FSR) include in genere l'esame dei modelli di minaccia, degli output degli strumenti e delle prestazioni rispetto ai gate di qualità e alle barre dei bug definiti nella fase dei requisiti.

### <a name="certify-release-and-archive"></a>Certificare il rilascio e l'archiviazione

La certificazione del software prima di una liberatoria garantisce che i requisiti di sicurezza e privacy siano soddisfatti. L'archiviazione di tutti i dati pertinenti è essenziale per l'esecuzione di attività di manutenzione post-rilascio. L'archiviazione consente inoltre di ridurre i costi a lungo termine associati alla progettazione di software prolungato.

## <a name="response"></a>Risposta
La fase post-release di risposta è in grado e disponibile per rispondere in modo appropriato a qualsiasi segnalazione di minacce e vulnerabilità software emergenti.

### <a name="execute-the-incident-response-plan"></a>Eseguire il piano di risposta agli eventi imprevisti

Essere in grado di implementare il piano di risposta agli incidenti istituito nella fase di rilascio è essenziale per proteggere i clienti dalle vulnerabilità di sicurezza software o privacy che emergono.

### <a name="monitor-application-performance"></a>Monitoraggio prestazioni applicazione

Il monitoraggio continuo dell'applicazione dopo la distribuzione consente di rilevare potenziali problemi di prestazioni e vulnerabilità della sicurezza.
I servizi di Azure che facilitano il monitoraggio delle applicazioni sono:Azure services that assist with application monitoring are:

  - Azure Application Insights
  - Centro sicurezza di Azure

#### <a name="application-insights"></a>Application Insights

[Application Insights](../../azure-monitor/app/app-insights-overview.md) è un servizio APM (Application Performance Management) estensibile per sviluppatori Web su più piattaforme. che consente di monitorare un'applicazione Web live. Application Insights rileva automaticamente le anomalie delle prestazioni. Include potenti strumenti di analisi che consentono di diagnosticare i problemi e comprendere cosa fanno effettivamente gli utenti con l'app. Il servizio è progettato per supportare il miglioramento continuo delle prestazioni e dell'usabilità.

#### <a name="azure-security-center"></a>Centro sicurezza di Azure

[Centro sicurezza di Azure](../../security-center/security-center-intro.md) consente di prevenire, rilevare e rispondere alle minacce con una maggiore visibilità e controllo sulla sicurezza delle risorse di Azure, incluse le applicazioni Web. Centro sicurezza di Azure consente di rilevare le minacce che potrebbero altrimenti passare inosservate. Funziona con varie soluzioni di sicurezza.

Il livello gratuito del Centro sicurezza offre sicurezza limitata solo per le risorse di Azure. Il [livello Security Center Standard](../../security-center/security-center-onboarding.md) estende queste funzionalità alle risorse locali e ad altri cloud.
Security Center Standard consente di:

  - Individuare e risolvere le vulnerabilità della sicurezza.
  - Applicare l'accesso e i controlli delle applicazioni per bloccare le attività dannose.
  - Rileva le minacce utilizzando l'analisi e l'intelligenza.
  - Rispondere rapidamente quando sotto attacco.

## <a name="next-steps"></a>Passaggi successivi
Negli articoli seguenti vengono consigliati i controlli di sicurezza e le attività che consentono di progettare e sviluppare applicazioni protette.

- [Progettare applicazioni sicure](secure-design.md)
- [Sviluppare applicazioni sicure](secure-develop.md)
