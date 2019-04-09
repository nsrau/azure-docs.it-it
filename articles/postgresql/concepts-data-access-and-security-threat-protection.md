---
title: Advanced Threat Protection - Database di Azure per PostgreSQL
description: Advanced Threat Protection individua le attività di database anomale che indicano la presenza di potenziali minacce alla sicurezza nel database.
author: bolzmj
ms.author: mbolz
ms.service: postgresql
ms.topic: conceptual
ms.date: 04/05/2019
ms.openlocfilehash: 0521283f917d05a56d26503dc6f0af349f1ac1e5
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2019
ms.locfileid: "59260324"
---
# <a name="azure-database-for-postgresql-advanced-threat-protection"></a>Advanced Threat Protection per Database di Azure per PostgreSQL

Advanced Threat Protection per Database di Azure per PostgreSQL rileva attività anomale che indicano tentativi insoliti e potenzialmente dannosi di accedere o sfruttare i database.

> [!NOTE]
> Advanced Threat Protection è disponibile in anteprima pubblica.

Threat Protection fa parte dell'offerta Advanced Threat Protection (ATP), che è un pacchetto unificato per le funzionalità avanzate di sicurezza. Advanced Threat Protection siano accessibili e gestite tramite il [portale di Azure](https://portal.azure.com) o tramite [API REST](/rest/api/postgresql/serversecurityalertpolicies). La funzionalità è disponibile per i server per utilizzo generico e ottimizzate per la memoria.

> [!NOTE]
> La funzionalità Advanced Threat Protection **non** è disponibile nelle aree seguenti di cloud sovrano e Azure per enti pubblici: US Gov Texas, US Gov Arizona, US Gov Iowa, US Gov Virginia, US DoD (area orientale), US DoD (area centrale), Germania centrale, Germania settentrionale, Cina orientale, Cina orientale 2. Visitare [i prodotti disponibili in base all'area](https://azure.microsoft.com/global-infrastructure/services/) per verificare la disponibilità generale del prodotto.

## <a name="what-is-advanced-threat-protection"></a>Che cos'è Advanced Threat Protection?

Advanced Threat Protection per Database di Azure per PostgreSQL offre un nuovo livello di sicurezza che permette ai clienti di rilevare e gestire le possibili minacce non appena si manifestano attraverso avvisi di sicurezza sulle attività anomale. Gli utenti ricevono un avviso in caso di attività di database sospette, potenziali vulnerabilità e modelli di query e accesso ai database anomali. Advanced Threat Protection per Database di Azure per PostgreSQL integra gli avvisi con [Centro sicurezza di Azure](https://azure.microsoft.com/services/security-center/), includendo così i dettagli delle attività sospette e consiglia le azioni per individuare e ridurre la minaccia. Advanced Threat Protection per Database di Azure per PostgreSQL rende più semplice affrontare le minacce potenziali al database, senza dover essere esperti della sicurezza o gestire sistemi di monitoraggio della sicurezza avanzati. 

![Concetti di Advanced Threat Protection](media/concepts-data-access-and-security-threat-protection/advanced-threat-protection-concept.png)

## <a name="advanced-threat-protection-alerts"></a>Avvisi di Advanced Threat Protection 
Advanced Threat Protection per Database di Azure per PostgreSQL rileva le attività anomale che indicano tentativi insoliti e potenzialmente dannosi di accesso o exploit dei database e può attivare gli avvisi seguenti:
- **Accesso da una posizione insolita**: questo avviso viene attivato quando il modello di accesso al server Database di Azure per PostgreSQL cambia, situazione in cui un utente ha eseguito l'accesso al server Database di Azure per PostgreSQL da una località geografica insolita. In alcuni casi, l'avviso rileva un'azione legittima (una nuova applicazione o la manutenzione da parte dello sviluppatore). In altri casi, l'avviso rileva un'azione dannosa (da parte di un ex dipendente o un utente malintenzionato esterno).
- **Accesso da un data center di Azure insolito**: questo avviso viene attivato quando il modello di accesso al server Database di Azure per PostgreSQL cambia, situazione in cui un utente ha eseguito l'accesso al server da un data center di Azure insolito, rilevato nel server di recente. In alcuni casi, l'avviso rileva un'azione legittima (una nuova applicazione in Azure, Power BI, l'editor di query di Database di Azure per PostgreSQL). In altri casi, l'avviso rileva un'azione dannosa proveniente da una risorsa o un servizio di Azure (da parte di un ex dipendente o un utente malintenzionato esterno).
- **Accesso da un'entità di sicurezza non familiare**: questo avviso viene attivato quando il modello di accesso al server Database di Azure per PostgreSQL cambia, situazione in cui un utente ha eseguito l'accesso al server usando un'entità di sicurezza insolita (utente di Database di Azure per PostgreSQL). In alcuni casi, l'avviso rileva un'azione legittima (nuova applicazione o manutenzione da parte dello sviluppatore). In altri casi, l'avviso rileva un'azione dannosa (da parte di un ex dipendente o un utente malintenzionato esterno).
- **Accesso da un'applicazione potenzialmente dannosa**: questo avviso viene attivato quando un'applicazione potenzialmente dannosa viene usata per accedere al database. In alcuni casi, l'avviso rileva test di penetrazione in corso. In altri casi, l'avviso rileva un attacco mediante strumenti comuni di attacco.
- **Attacco di forza bruta alle credenziali del Database di Azure per PostgreSQL**: questo avviso viene attivato quando si verifica un numero elevato anomalo di accessi non riusciti con credenziali diverse. In alcuni casi, l'avviso rileva test di penetrazione in corso. In altri casi, l'avviso rileva un attacco di forza bruta.

## <a name="next-steps"></a>Passaggi successivi

* Altre informazioni sul [Centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/security-center-intro)
* Per altre informazioni sui prezzi, vedere la [pagina sui Prezzi di Database di Azure per PostgreSQL](https://azure.microsoft.com/pricing/details/postgresql/) 
* Configurare [Advanced Threat Protection per Database di Azure per PostgreSQL](howto-database-threat-protection-portal.md) usando il portale di Azure  
