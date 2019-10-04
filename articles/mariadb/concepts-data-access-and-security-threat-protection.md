---
title: Advanced Threat Protection-database di Azure per MariaDB | Microsoft Docs
description: Advanced Threat Protection individua le attività di database anomale che indicano la presenza di potenziali minacce alla sicurezza nel database.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 07/12/2019
ms.openlocfilehash: a96d8c3b9f9e3f9b1aedf269bce141da3e4ccf42
ms.sourcegitcommit: 10251d2a134c37c00f0ec10e0da4a3dffa436fb3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/13/2019
ms.locfileid: "67869690"
---
# <a name="azure-database-for-mariadb-advanced-threat-protection"></a>Database di Azure per MariaDB Advanced Threat Protection

Advanced Threat Protection per database di Azure per MariaDB rileva attività anomale che indicano tentativi insoliti e potenzialmente dannosi di accesso o exploit dei database.

> [!IMPORTANT]
> Advanced Threat Protection è in anteprima pubblica.

Advanced Threat Protection fa parte dell'offerta Advanced Data Security (Sicurezza dei dati avanzata), che è un pacchetto unificato per le funzionalità avanzate di sicurezza. È possibile accedere e gestire Advanced Threat Protection tramite il [portale di Azure](https://portal.azure.com). La funzionalità è disponibile per i server per utilizzo generico e con ottimizzazione per la memoria.

> [!NOTE]
> La funzionalità Advanced Threat Protection **non** è disponibile nelle aree seguenti di cloud sovrano e Azure per enti pubblici: US Gov Texas, US Gov Arizona, US Gov Iowa, US Gov Virginia, US DoD (area orientale), US DoD (area centrale), Germania centrale, Germania settentrionale, Cina orientale, Cina orientale 2. Visitare [i prodotti disponibili in base all'area](https://azure.microsoft.com/global-infrastructure/services/) per verificare la disponibilità generale del prodotto.


## <a name="what-is-advanced-threat-protection"></a>Che cos'è Advanced Threat Protection?

Advanced Threat Protection per database di Azure per MariaDB offre un nuovo livello di sicurezza, che consente ai clienti di rilevare e rispondere alle minacce potenziali non appena si verificano, fornendo avvisi di sicurezza sulle attività anomale. Gli utenti ricevono un avviso in caso di attività di database sospette, potenziali vulnerabilità e modelli di query e accesso ai database anomali. Advanced Threat Protection per database di Azure per MariaDB integra gli avvisi con il [Centro sicurezza di Azure](https://azure.microsoft.com/services/security-center/), che include i dettagli delle attività sospette e consiglia di agire su come analizzare e mitigare la minaccia. Advanced Threat Protection per database di Azure per MariaDB semplifica l'indirizzamento delle potenziali minacce al database senza dover essere esperti della sicurezza o gestire sistemi di monitoraggio della sicurezza avanzati. 

![Concetti di Advanced Threat Protection](media/concepts-data-access-and-security-threat-protection/advanced-threat-protection-concept.png)

## <a name="advanced-threat-protection-alerts"></a>Avvisi di Advanced Threat Protection 
Advanced Threat Protection per database di Azure per MariaDB rileva attività anomale che indicano tentativi insoliti e potenzialmente dannosi di accesso o exploit dei database e possono attivare gli avvisi seguenti:
- **Accesso da una posizione insolita**: Questo avviso viene generato quando viene apportata una modifica nel modello di accesso al database di Azure per il server MariaDB, in cui un utente ha effettuato l'accesso al database di Azure per il server MariaDB da una posizione geografica insolita. In alcuni casi, l'avviso rileva un'azione legittima (una nuova applicazione o la manutenzione da parte dello sviluppatore). In altri casi, l'avviso rileva un'azione dannosa (da parte di un ex dipendente o un utente malintenzionato esterno).
- **Accesso da un data center di Azure insolito**: Questo avviso viene generato quando viene apportata una modifica nel modello di accesso al database di Azure per il server MariaDB, in cui un utente ha effettuato l'accesso al server da un data center di Azure insolito visualizzato nel server durante il periodo recente. In alcuni casi, l'avviso rileva un'azione legittima (la nuova applicazione in Azure, Power BI). In altri casi, l'avviso rileva un'azione dannosa proveniente da una risorsa o un servizio di Azure (da parte di un ex dipendente o un utente malintenzionato esterno).
- **Accesso da un'entità di sicurezza non familiare**: Questo avviso viene generato quando viene apportata una modifica nel modello di accesso al database di Azure per il server MariaDB, in cui qualcuno ha eseguito l'accesso al server usando un'entità di protezione insolita (database di Azure per l'utente di MariaDB). In alcuni casi, l'avviso rileva un'azione legittima (nuova applicazione o manutenzione da parte dello sviluppatore). In altri casi, l'avviso rileva un'azione dannosa (da parte di un ex dipendente o un utente malintenzionato esterno).
- **Accesso da un'applicazione potenzialmente dannosa**: questo avviso viene attivato quando un'applicazione potenzialmente dannosa viene usata per accedere al database. In alcuni casi, l'avviso rileva test di penetrazione in corso. In altri casi, l'avviso rileva un attacco mediante strumenti comuni di attacco.
- **Forza bruta per le credenziali del database di Azure per MariaDB**: questo avviso viene attivato quando si verifica un numero elevato anomalo di accessi non riusciti con credenziali diverse. In alcuni casi, l'avviso rileva test di penetrazione in corso. In altri casi, l'avviso rileva un attacco di forza bruta.

## <a name="next-steps"></a>Passaggi successivi

* Altre informazioni sul [Centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/security-center-intro)
* Per altre informazioni sui prezzi, vedere la [pagina dei prezzi di database di Azure per MariaDB](https://azure.microsoft.com/pricing/details/mariadb/) 
* Configurare il [database di Azure per MariaDB Advanced Threat Protection](howto-database-threat-protection-portal.md) usando il portale di Azure  
