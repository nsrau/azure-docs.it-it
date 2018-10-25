---
title: Advanced Threat Protection - Database di Azure per MySQL | Microsoft Docs
description: Advanced Threat Protection individua le attività di database anomale che indicano la presenza di potenziali minacce alla sicurezza nel database.
services: mysql
author: bolzmj
manager: kfile
ms.service: mysql
ms.topic: article
ms.date: 09/20/2018
ms.author: mbolz
ms.openlocfilehash: e123acd09e4cbfdbaf0531db72598dbd478fa1c9
ms.sourcegitcommit: 7b0778a1488e8fd70ee57e55bde783a69521c912
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/10/2018
ms.locfileid: "49069387"
---
# <a name="azure-database-for-mysql-advanced-threat-protection"></a>Advanced Threat Protection per Database di Azure per MySQL

Advanced Threat Protection per Database di Azure per MySQL rileva attività anomale che indicano tentativi insoliti e potenzialmente dannosi di accedere o sfruttare i database.

Advanced Threat Protection fa parte dell'offerta Advanced Data Security (Sicurezza dei dati avanzata), che è un pacchetto unificato per le funzionalità avanzate di sicurezza. È possibile accedere e gestire Advanced Threat Protection tramite il [portale di Azure](https://portal.azure.com) ed è attualmente in anteprima.

> [!NOTE]
> La funzionalità Advanced Threat Protection **non** è disponibile nelle aree geografiche cloud sovrani di Azure: US Gov Texas, US Gov Arizona, US Gov Iowa, Stati Uniti, Gov Virginia, US DoD (area orientale), US DoD (area centrale), Germania centrale, Germania settentrionale, Cina orientale, Cina orientale 2. Visitare [i prodotti disponibili in base all'area](https://azure.microsoft.com/global-infrastructure/services/) per verificare la disponibilità generale del prodotto.
> 

## <a name="what-is-advanced-threat-protection"></a>Che cos'è Advanced Threat Protection?

Advanced Threat Protection per Database di Azure per MySQL offre un nuovo livello di sicurezza che permette ai clienti di rilevare e gestire le possibili minacce non appena si manifestano attraverso avvisi di sicurezza sulle attività anomale. Gli utenti ricevono un avviso in caso di attività di database sospette, potenziali vulnerabilità e modelli di query e accesso ai database anomali. Advanced Threat Protection per Database di Azure per MySQL integra gli avvisi con [Centro sicurezza di Azure](https://azure.microsoft.com/services/security-center/), includendo così i dettagli delle attività sospette e consiglia le azioni per individuare e ridurre la minaccia. Advanced Threat Protection per Database di Azure per MySQL rende più semplice affrontare le minacce potenziali al database, senza dover essere esperti della sicurezza o gestire sistemi di controllo di sicurezza avanzati. 

![Concetti di Advanced Threat Protection](media/concepts-data-access-and-security-threat-protection/advanced-threat-protection-concept.png)

## <a name="advanced-threat-protection-alerts"></a>Avvisi di Advanced Threat Protection 
Advanced Threat Protection per Database di Azure per MySQL rileva le attività anomale che indicano tentativi insoliti e potenzialmente dannosi di accesso o exploit dei database e può attivare gli avvisi seguenti:
- **Accesso da una posizione insolita**: questo avviso viene attivato quando il modello di accesso al server Database di Azure per MySQL cambia, quando un utente ha effettuato l'accesso a Database di Azure per MySQL da una posizione geografica insolita. In alcuni casi, l'avviso rileva un'azione legittima (una nuova applicazione o la manutenzione da parte dello sviluppatore). In altri casi, l'avviso rileva un'azione dannosa (da parte di un ex dipendente o un utente malintenzionato esterno).
- **Accesso da un data center di Azure insolito**: questo avviso viene attivato quando il modello di accesso al server Database di Azure per MySQL cambia, quando un utente ha effettuato l'accesso al server da un data center di Azure insolito, rilevato nel server di recente. In alcuni casi, l'avviso rileva un'azione legittima (una nuova applicazione in Azure, Power BI, l'editor di query di Database di Azure per MySQL). In altri casi, l'avviso rileva un'azione dannosa proveniente da una risorsa o un servizio di Azure (da parte di un ex dipendente o un utente malintenzionato esterno).
- **Accesso da un'entità di sicurezza non familiare**: questo avviso viene attivato quando il modello di accesso a Database di Azure per MySQL cambia, quando un utente ha effettuato l'accesso al server usando un'entità di sicurezza insolita (utente di Database di Azure per MySQL). In alcuni casi, l'avviso rileva un'azione legittima (nuova applicazione o manutenzione da parte dello sviluppatore). In altri casi, l'avviso rileva un'azione dannosa (da parte di un ex dipendente o un utente malintenzionato esterno).
- **Accesso da un'applicazione potenzialmente dannosa**: questo avviso viene attivato quando un'applicazione potenzialmente dannosa viene usata per accedere al database. In alcuni casi, l'avviso rileva test di penetrazione in corso. In altri casi, l'avviso rileva un attacco mediante strumenti comuni di attacco.
- **Attacco di forza bruta a credenziali Database di Azure per MySQL**: questo avviso viene attivato quando si verifica un numero insolitamente elevato di accessi non riusciti con credenziali diverse. In alcuni casi, l'avviso rileva test di penetrazione in corso. In altri casi, l'avviso rileva un attacco di forza bruta.

## <a name="next-steps"></a>Passaggi successivi

* Altre informazioni sul [Centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/security-center-intro)
* Per altre informazioni sui prezzi, vedere la [pagina sui Prezzi di Database di Azure per MySQL](https://azure.microsoft.com/pricing/details/mysql/) 
* Configurare [Advanced Threat Protection per Database di Azure per MySQL](howto-database-threat-protection-portal.md) usando il portale di Azure  
