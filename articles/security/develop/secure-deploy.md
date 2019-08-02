---
title: Distribuire applicazioni sicure in Microsoft Azure
description: Questo articolo illustra le procedure consigliate da prendere in considerazione durante le fasi di rilascio e risposta del progetto di applicazione Web.
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
ms.openlocfilehash: dd86e6bf571dd67da00aee63eadff031a1040ff7
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/01/2019
ms.locfileid: "68728728"
---
# <a name="deploy-secure-applications-on-azure"></a>Distribuire applicazioni sicure in Azure
In questo articolo vengono presentate le attività e i controlli di sicurezza da prendere in considerazione quando si distribuiscono applicazioni per il cloud. Vengono analizzate le domande e i concetti di sicurezza da considerare durante le fasi di rilascio e risposta di Microsoft [Security Development Lifecycle (SDL)](https://msdn.microsoft.com/library/windows/desktop/84aed186-1d75-4366-8e61-8d258746bopq.aspx) . L'obiettivo è consentire di definire le attività e i servizi di Azure che è possibile usare per distribuire un'applicazione più protetta.

Le fasi SDL seguenti sono descritte in questo articolo:

- Release
- Risposta

## <a name="release"></a>Release
Lo stato attivo della fase di rilascio è la preparazione di un progetto per la versione pubblica.
Sono incluse le modalità di pianificazione per eseguire efficacemente le attività di manutenzione post-rilascio e risolvere le vulnerabilità della sicurezza che potrebbero verificarsi in un secondo momento.

### <a name="check-your-applications-performance-before-you-launch"></a>Controllare le prestazioni dell'applicazione prima di avviare

Controllare le prestazioni dell'applicazione prima di avviarla o distribuire gli aggiornamenti nell'ambiente di produzione. Eseguire [test di carico](https://www.visualstudio.com/docs/test/performance-testing/getting-started/getting-started-with-performance-testing) basati sul cloud usando Visual Studio per individuare problemi di prestazioni nell'applicazione, migliorare la qualità della distribuzione, assicurarsi che l'applicazione sia sempre attiva o disponibile e che l'applicazione sia in grado di gestire il traffico per l'avvio.

### <a name="install-a-web-application-firewall"></a>Installare un web application firewall

Le applicazioni Web sono sempre più vittime di attacchi che sfruttano le più comuni vulnerabilità note. Comuni tra questi exploit sono gli attacchi SQL injection e gli attacchi di scripting tra siti. La prevenzione di questi attacchi nel codice dell'applicazione può essere complessa. Potrebbe essere necessaria una manutenzione rigorosa, l'applicazione di patch e il monitoraggio a molti livelli della topologia dell'applicazione. Un WAF centralizzato semplifica la gestione della sicurezza. Una soluzione WAF può anche rispondere a una minaccia per la sicurezza applicando patch a una vulnerabilità nota in una posizione centrale rispetto alla sicurezza di ogni singola applicazione Web.

Il [Gateway applicazione Azure WAF](https://docs.microsoft.com/azure/application-gateway/waf-overview) offre una protezione centralizzata delle applicazioni Web da exploit e vulnerabilità comuni. WAF si basa sulle regole della [OWASP Core Rule sets](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project) 3,0 o 2.2.9.

### <a name="create-an-incident-response-plan"></a>Creazione di un piano di risposta agli eventi imprevisti

La preparazione di un piano di risposta agli eventi imprevisti è fondamentale per aiutare a risolvere nuove minacce che potrebbero emergere nel tempo. La preparazione di un piano di risposta agli eventi imprevisti include l'identificazione di contatti di emergenza di sicurezza appropriati e la definizione di piani di manutenzione di sicurezza per il codice ereditato da altri gruppi nell'organizzazione e per il codice di terze parti concesso in licenza.

### <a name="conduct-a-final-security-review"></a>Eseguire una verifica finale della sicurezza

La revisione deliberata di tutte le attività di sicurezza eseguite contribuisce a garantire la preparazione per la versione del software o l'applicazione. La revisione finale della sicurezza (FSR) include in genere l'analisi dei modelli di rischio, gli output degli strumenti e le prestazioni in base ai controlli di qualità e alle barre dei bug definiti nella fase dei requisiti.

### <a name="certify-release-and-archive"></a>Certificare la versione e l'archivio

La certificazione del software prima di una versione consente di garantire che siano soddisfatti i requisiti di sicurezza e privacy. L'archiviazione di tutti i dati pertinenti è essenziale per l'esecuzione di attività di manutenzione post-rilascio. L'archiviazione consente inoltre di ridurre i costi a lungo termine associati alla progettazione software prolungata.

## <a name="response"></a>Risposta
La fase di post-rilascio della risposta si concentra sul team di sviluppo che è in grado di rispondere in modo appropriato a tutti i report relativi a minacce e vulnerabilità software emergenti.

### <a name="execute-the-incident-response-plan"></a>Eseguire il piano di risposta agli eventi imprevisti

La possibilità di implementare il piano di risposta agli eventi imprevisti istituito nella fase di rilascio è essenziale per aiutare a proteggere i clienti dalla sicurezza del software o dalle vulnerabilità della privacy emerse.

### <a name="monitor-application-performance"></a>Monitoraggio prestazioni applicazione

Il monitoraggio continuo dell'applicazione dopo che è stato distribuito potenzialmente consente di rilevare i problemi di prestazioni e le vulnerabilità della sicurezza.
I servizi di Azure che assistono al monitoraggio delle applicazioni sono:

  - Azure Application Insights
  - Centro sicurezza di Azure

#### <a name="application-insights"></a>Application Insights

[Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-overview) è un servizio estendibile di gestione delle prestazioni delle applicazioni per sviluppatori Web su più piattaforme, che consente di monitorare un'applicazione Web live. Application Insights rileva automaticamente le anomalie delle prestazioni. Sono inclusi strumenti di analisi avanzati che consentono di diagnosticare i problemi e comprendere il funzionamento effettivo dell'app da parte degli utenti. Il servizio è progettato per supportare il miglioramento continuo delle prestazioni e dell'usabilità.

#### <a name="azure-security-center"></a>Centro sicurezza di Azure

Il [Centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/security-center-intro) consente di prevenire, rilevare e rispondere alle minacce con una maggiore visibilità e controllo della sicurezza delle risorse di Azure, incluse le applicazioni Web. Il Centro sicurezza di Azure consente di rilevare le minacce che potrebbero altrimenti andare inosservate. Funziona con diverse soluzioni di sicurezza.

Il livello gratuito del Centro sicurezza offre sicurezza limitata solo per le risorse di Azure. Il [livello standard del Centro sicurezza](https://docs.microsoft.com/azure/security-center/security-center-onboarding) estende queste funzionalità alle risorse locali e ad altri cloud.
Il Centro sicurezza standard consente di:

  - Individuare e correggere le vulnerabilità della sicurezza.
  - Applicare i controlli di accesso e dell'applicazione per bloccare le attività dannose.
  - Rilevare le minacce usando le funzionalità di analisi e intelligenza.
  - Rispondere rapidamente in caso di attacco.

## <a name="next-steps"></a>Passaggi successivi
Negli articoli seguenti si consigliano i controlli di sicurezza e le attività che consentono di progettare e sviluppare applicazioni sicure.

- [Progettare applicazioni protette](secure-design.md)
- [Sviluppare applicazioni sicure](secure-develop.md)
