---
title: Advanced Threat Protection - Azure Database for MariaDB
description: Advanced Threat Protection individua le attività di database anomale che indicano la presenza di potenziali minacce alla sicurezza nel database.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 8485907eba75f80c8f0ed4fd0cc7368c6147b9fd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79532179"
---
# <a name="azure-database-for-mariadb-advanced-threat-protection"></a>Database di Azure per MariaDB Advanced Threat Protection

Advanced Threat Protection for Azure Database for MariaDB rileva attività anomale che indicano tentativi insoliti e potenzialmente dannosi di accedere o sfruttare i database.

> [!IMPORTANT]
> Advanced Threat Protection è in anteprima pubblica.

Advanced Threat Protection fa parte dell'offerta Advanced Data Security (Sicurezza dei dati avanzata), che è un pacchetto unificato per le funzionalità avanzate di sicurezza. È possibile accedere e gestire Advanced Threat Protection tramite il portale di [Azure.](https://portal.azure.com) La funzione è disponibile per i server General Purpose e Memory Optimized.

> [!NOTE]
> La funzionalità Advanced Threat Protection **non** è disponibile nelle aree geografiche cloud sovrani di Azure: US Gov Texas, US Gov Arizona, US Gov Iowa, Stati Uniti, Gov Virginia, US DoD (area orientale), US DoD (area centrale), Germania centrale, Germania settentrionale, Cina orientale, Cina orientale 2. Visitare [i prodotti disponibili in base all'area](https://azure.microsoft.com/global-infrastructure/services/) per verificare la disponibilità generale del prodotto.


## <a name="what-is-advanced-threat-protection"></a>Che cos'è Advanced Threat Protection?

Advanced Threat Protection for Azure Database for MariaDB offre un nuovo livello di sicurezza, che consente ai clienti di rilevare e rispondere alle potenziali minacce man mano che si verificano fornendo avvisi di sicurezza per attività anomale. Gli utenti ricevono un avviso in caso di attività di database sospette, potenziali vulnerabilità e modelli di query e accesso ai database anomali. Advanced Threat Protection for Azure Database for MariaDB integra gli avvisi con il Centro sicurezza di [Azure,](https://azure.microsoft.com/services/security-center/)che include dettagli sulle attività sospette e consiglia azioni su come analizzare e ridurre la minaccia. Advanced Threat Protection for Azure Database for MariaDB semplifica la gestione di potenziali minacce al database senza la necessità di essere esperti di sicurezza o di gestire sistemi di monitoraggio della sicurezza avanzati. 

![Concetti di Advanced Threat Protection](media/concepts-data-access-and-security-threat-protection/advanced-threat-protection-concept.png)

## <a name="advanced-threat-protection-alerts"></a>Avvisi di Advanced Threat Protection 
Advanced Threat Protection for Azure Database for MariaDB rileva attività anomale che indicano tentativi insoliti e potenzialmente dannosi di accedere o sfruttare i database e può generare gli avvisi seguenti:Advanced Threat Protection for Azure Database for MariaDB detects anomalous activities indicating unusual and potentially harmful attempts to access or exploit databases and it can trigger the following alerts:
- **Accesso da una posizione insolita:** questo avviso viene attivato quando viene apportata una modifica al modello di accesso al database di Azure per il server MariaDB, in cui un utente ha eseguito l'accesso al database di Azure per il server MariaDB da una posizione geografica insolita. In alcuni casi, l'avviso rileva un'azione legittima (una nuova applicazione o la manutenzione da parte dello sviluppatore). In altri casi, l'avviso rileva un'azione dannosa (da parte di un ex dipendente o un utente malintenzionato esterno).
- Accesso da un data center di **Azure insolito:** questo avviso viene attivato quando si verifica una modifica nel modello di accesso al database di Azure per il server MariaDB, in cui un utente ha eseguito l'accesso al server da un data center di Azure insolito visualizzato in questo server durante il periodo recente. In alcuni casi, l'avviso rileva un'azione legittima (la nuova applicazione in Azure, Power BI). In altri casi, l'avviso rileva un'azione dannosa proveniente da una risorsa o un servizio di Azure (da parte di un ex dipendente o un utente malintenzionato esterno).
- **Accesso da un'entità sconosciuta:** questo avviso viene attivato quando viene apportata una modifica al modello di accesso al database di Azure per il server MariaDB, in cui un utente ha eseguito l'accesso al server usando un'entità insolita (database di Azure per l'utente MariaDB). In alcuni casi, l'avviso rileva un'azione legittima (nuova applicazione o manutenzione da parte dello sviluppatore). In altri casi, l'avviso rileva un'azione dannosa (da parte di un ex dipendente o un utente malintenzionato esterno).
- **Accesso da un'applicazione potenzialmente dannosa**: questo avviso viene attivato quando un'applicazione potenzialmente dannosa viene usata per accedere al database. In alcuni casi, l'avviso rileva test di penetrazione in corso. In altri casi, l'avviso rileva un attacco mediante strumenti comuni di attacco.
- **Forza bruta Database di Azure per le credenziali MariaDB:** questo avviso viene attivato quando si verifica un numero elevato anomalo di accessi non riusciti con credenziali diverse. In alcuni casi, l'avviso rileva test di penetrazione in corso. In altri casi, l'avviso rileva un attacco di forza bruta.

## <a name="next-steps"></a>Passaggi successivi

* Altre informazioni sul Centro sicurezza di [AzureLearn](https://docs.microsoft.com/azure/security-center/security-center-intro) more about Azure Security Center
* Per altre informazioni sui prezzi, vedere la pagina Prezzi del [database di Azure per MariaDBFor](https://azure.microsoft.com/pricing/details/mariadb/) more information on pricing, see the Azure Database for MariaDB Pricing page 
* Configurare il [database di Azure per MariaDB Advanced Threat Protection](howto-database-threat-protection-portal.md) tramite il portale di AzureConfigure Azure Database for MariaDB Advanced Threat Protection using the Azure portal  
