---
title: Modello di applicazione Web multi-tenant | Microsoft Docs
description: Sono disponibili informazioni generali sull'architettura e modelli di progettazione che descrivono come implementare un'applicazione Web multi-tenant in Azure.
services: ''
documentationcenter: .net
author: wadepickett
manager: wpickett
editor: ''
ms.assetid: 4f0281d2-1555-42b0-a99d-1222fade0b0f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 06/05/2015
ms.author: wpickett
ms.custom: devx-track-dotnet
ms.openlocfilehash: d36a2804519e5728dd068cc6c06ad005244e8c95
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/24/2020
ms.locfileid: "95524097"
---
# <a name="multitenant-applications-in-azure"></a>Applicazioni multi-tenant in Azure
Un'applicazione multi-tenant è una risorsa condivisa che consente agli utenti in tenant distinti di visualizzare l'applicazione come se fosse propria. Uno scenario tipico che si presta a un'applicazione multi-tenant è quello in cui tutti gli utenti dell'applicazione da tenant diversi possono desiderare di personalizzare l'esperienza utente, ma in caso contrario hanno gli stessi requisiti aziendali di base. Esempi di applicazioni multi-tenant di grandi dimensioni sono Microsoft 365, Outlook.com e visualstudio.com.

Dal punto di vista del fornitore di applicazioni, i vantaggi della multi-tenancy risiedono principalmente nell'efficienza operativa e dei costi. Una versione dell'applicazione può soddisfare le esigenze di molti tenant/client, consentendo il consolidamento di attività amministrative del sistema come il monitoraggio, l'ottimizzazione delle prestazioni, la manutenzione del software e i backup di dati.

Di seguito è riportato un elenco degli obiettivi e dei requisiti più significativi dal punto di vista di un fornitore.

* **Provisioning**: è necessario essere in grado di eseguire il provisioning di nuovi tenant per l'applicazione.  Per le applicazioni multi-tenant che contano un ingente numero di tenant, di solito è necessario automatizzare il processo abilitando il provisioning self-service.
* **Manutenibilità**: è necessario essere in grado di aggiornare l'applicazione ed eseguire altre attività di manutenzione quando è usata da più tenant.
* **Monitoraggio**: è necessario essere in grado di monitorare l'applicazione in ogni momento allo scopo di identificare eventuali problemi e risolverli. Ciò include il monitoraggio della modalità di uso dell'applicazione da parte di ogni tenant.

Un'applicazione multi-tenant correttamente implementata offre agli utenti i vantaggi seguenti.

* **Isolamento**: le attività dei singoli tenant non incidono sull'uso dell'applicazione da parte degli altri tenant. I tenant non possono accedere ai dati reciproci. Ogni tenant avrà apparentemente l'uso esclusivo dell'applicazione.
* **Disponibilità**: i singoli tenant vogliono che l'applicazione sia sempre disponibile, meglio se con garanzie definite in un contratto di servizio (SLA, Service Level Agreement). Come già accennato, le attività dei singoli tenant non dovrebbero incidere sulla disponibilità dell'applicazione.
* **Scalabilità**: è possibile scalare l'applicazione in modo da soddisfare la domanda di singoli tenant. La presenza e le azioni degli altri tenant non dovrebbero incidere sulle prestazioni dell'applicazione.
* **Costi**: i costi sono inferiori rispetto all'esecuzione di un'applicazione single-tenant dedicata, in quanto la multi-tenancy consente la condivisione delle risorse.
* **Personalizzazione**. la possibilità di personalizzare l'applicazione per un singolo tenant in vari modi, ad esempio con l'aggiunta o la rimozione di funzionalità, la modifica di colori e loghi o persino l'aggiunta di un proprio codice o script.

In breve, sebbene ci siano molte considerazioni che è necessario tenere in considerazione per fornire un servizio altamente scalabile, esistono anche diversi obiettivi e requisiti comuni a molte applicazioni multi-tenant. Alcuni potrebbero non essere pertinenti in scenari specifici e l'importanza dei singoli obiettivi e requisiti varierà in ogni scenario. In quanto provider dell'applicazione multi-tenant, si avranno anche obiettivi e requisiti, ad esempio, che soddisfano gli obiettivi e i requisiti del tenant, la redditività, la fatturazione, più livelli di servizio, il provisioning, il monitoraggio della gestibilità e l'automazione.

Per altre informazioni sulle considerazioni di progettazione di un'applicazione multi-tenant vedere [Hosting di un'applicazione multi-tenant in Azure][Hosting a Multi-Tenant Application on Azure]. Per informazioni sugli schemi di architettura dati comuni delle applicazioni di database multi-tenant software come un servizio (SaaS), vedere [Schemi progettuali per applicazioni SaaS multi-tenant con il database SQL di Azure](./azure-sql/database/saas-tenancy-app-design-patterns.md). 

Azure offre molte funzionalità che consentono di risolvere i principali problemi riscontrati durante la progettazione di un sistema multi-tenant.

**Isolamento**

* Segmentare i tenant del sito Web in base alle intestazioni host con o senza comunicazione TLS
* Segmentare i tenant del sito Web in base ai parametri della query
* Servizi Web nei ruoli di lavoro
  * Ruoli di lavoro che in genere elaborano i dati nel back-end di un'applicazione.
  * Ruoli Web che solitamente fungono da front-end per le applicazioni.

**Archiviazione**

Gestione dei dati, ad esempio il database SQL di Azure o i servizi di archiviazione di Azure, ad esempio il servizio tabelle, che fornisce servizi per l'archiviazione di grandi quantità di dati non strutturati e il servizio BLOB, che fornisce servizi per archiviare grandi quantità di dati di testo o binari non strutturati quali video, audio e immagini.

* Protezione dei dati multi-tenant nel database SQL per ogni tenant SQL Server account di accesso.
* Usando le tabelle di Azure per le risorse dell'applicazione specificando criteri di accesso a livello di contenitore, è possibile modificare le autorizzazioni senza dover emettere nuovi URL per le risorse protette con le firme di accesso condiviso.
* Code di Azure per le risorse dell'applicazione - Le code di Azure sono comunemente utilizzate per attivare l'elaborazione per conto dei tenant, ma possono essere utilizzate anche per distribuire il lavoro richiesto per il provisioning o la gestione.
* Code di bus di servizio - Per le risorse dell'applicazione che effettuano il push del lavoro a un servizio condiviso, è possibile usare una singola coda in cui ogni mittente del tenant dispone solo delle autorizzazioni (in base alle attestazioni generate da ACS) per effettuare il push a quella coda, mentre solo i ricevitori dal servizio dispongono dell'autorizzazione per effettuare il pull dei dati provenienti da più tenant dalla coda.

**Servizi di connessione e sicurezza**

* Il bus di servizio di Azure, un'infrastruttura di messaggistica che consente di scambiare messaggi tra le applicazioni con approccio "a regime di controllo libero" per migliorare la scalabilità e la resilienza.

**Servizi di rete**

Azure offre diversi servizi di rete che supportano l'autenticazione e migliorano la gestibilità delle applicazioni ospitate. Di seguito sono indicati alcuni servizi disponibili:

* La rete virtuale di Azure consente di effettuare il provisioning e la gestione delle reti private virtuali (VPN) in Azure e di collegare queste reti in modo sicuro con l'infrastruttura IT locale.
* Rete virtuale - Gestione traffico consente di bilanciare il carico del traffico in ingresso tra più servizi di Azure ospitati, in esecuzione sia nello stesso data center sia in data center diversi distribuiti in tutto il mondo.
* Azure Active Directory (Azure AD) è un servizio moderno basato su REST che fornisce funzionalità di gestione dell'identità e controllo di accesso per le applicazioni cloud. L'uso di Azure AD per le risorse dell'applicazione offre un modo semplice per autenticare e autorizzare gli utenti ad accedere alle applicazioni e ai servizi Web, consentendo al contempo le funzionalità di autenticazione e autorizzazione all'esterno del codice.
* Il bus di servizio di Azure fornisce una funzionalità protetta di messaggistica e flusso dei dati per le applicazioni distribuite e ibride, ad esempio la comunicazione tra le applicazioni ospitate su Azure e le applicazioni e i servizi locali, senza richiedere complesse infrastrutture di sicurezza e firewall. Usare l'inoltro del bus di servizio per le risorse dell'applicazione per accedere ai servizi esposti come endpoint possono appartenere al tenant (ad esempio, ospitati all'esterno del sistema, ad esempio in locale), oppure è possibile eseguire il provisioning dei servizi in modo specifico per il tenant (perché i dati sensibili e specifici del tenant passano tra di essi).

**Provisioning delle risorse**

Azure offre diversi modi per effettuare il provisioning di nuovi tenant per l'applicazione. Per le applicazioni multi-tenant che contano un ingente numero di tenant, di solito è necessario automatizzare il processo abilitando il provisioning self-service.

* I ruoli di lavoro consentono di eseguire il provisioning e il deprovisioning delle risorse per ogni tenant (ad esempio quando un nuovo tenant effettua o annulla l'iscrizione), raccogliendo metriche per misurare l'utilizzo e gestendo la scalabilità in base a una determinata pianificazione o in risposta al superamento di soglie degli indicatori di prestazioni chiave. Questo stesso ruolo può essere utilizzato anche per effettuare il push di aggiornamenti alla soluzione.
* È possibile utilizzare l'archivio BLOB di Azure per effettuare il provisioning delle risorse di calcolo o di archiviazione preinizializzate per i nuovi tenant, fornendo criteri di accesso a livello di contenitore per proteggere i pacchetti del servizio di calcolo, le immagini del disco rigido virtuale e altre risorse.
* Le opzioni per il provisioning delle risorse del database SQL per un tenant includono:
  
  * DDL negli script o incorporati come risorse all'interno degli assembly.
  * Distribuzione a livello di codice dei pacchetti SQL Server 2008 R2 DAC
  * Copia da un database di riferimento master.
  * Uso delle funzionalità di importazione ed esportazione del database per il provisioning di nuovi database da un file

<!--links-->

[Hosting a Multi-Tenant Application on Azure]: /previous-versions/msp-n-p/hh534480(v=pandp.10)
[Designing Multitenant Applications on Azure]: https://msdn.microsoft.com/library/windowsazure/hh689716