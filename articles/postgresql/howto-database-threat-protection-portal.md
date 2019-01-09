---
title: Advanced Threat Protection - Database di Azure per PostgreSQL
description: La Protezione dalle minacce individua le attività di database che indicano la presenza di potenziali minacce alla sicurezza nel database.
author: bolzmj
ms.author: mbolz
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/20/2018
ms.openlocfilehash: b7c1f873d47be0f2ec0125f769a47a399e492662
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/17/2018
ms.locfileid: "53536152"
---
# <a name="advanced-threat-protection-for-azure-database-for-postgresql"></a>Advanced Threat Protection per Database di Azure per PostgreSQL

Advanced Threat Protection per Database di Azure per PostgreSQL rileva attività anomale che indicano tentativi insoliti e potenzialmente dannosi di accedere o sfruttare i database.

Advanced Threat Protection fa parte dell'offerta Advanced Data Security (Sicurezza dei dati avanzata), che è un pacchetto unificato per le funzionalità avanzate di sicurezza. È possibile accedere e gestire Advanced Threat Protection tramite il [portale di Azure](https://portal.azure.com) ed è attualmente in anteprima.

> [!NOTE]
> La funzionalità Advanced Threat Protection **non** è disponibile nelle aree seguenti di cloud sovrano e Azure per enti pubblici: US Gov Texas, US Gov Arizona, US Gov Iowa, US Gov Virginia, US DoD (area orientale), US DoD (area centrale), Germania centrale, Germania settentrionale, Cina orientale, Cina orientale 2. Visitare [i prodotti disponibili in base all'area](https://azure.microsoft.com/global-infrastructure/services/) per verificare la disponibilità generale del prodotto.
>

## <a name="set-up-threat-detection"></a>Impostare il rilevamento delle minacce
1. Avviare il portale di Azure all'indirizzo [https://portal.azure.com](https://portal.azure.com).
2. Passare alla pagina di configurazione del Database di Azure per PostgreSQL che si vuole proteggere. Nelle impostazioni di sicurezza selezionare **Advanced Threat Protection (anteprima)**.
3. Nella pagina di configurazione **Advanced Threat Protection (anteprima)**:

   - Abilita Advanced Threat Protection nel server.
   - In **Impostazioni di Advanced Threat Protection**, nella casella di testo **Invia avvisi a**, viene fornito l'elenco dei messaggi di posta elettronica per ricevere avvisi di sicurezza al rilevamento delle attività anomale del database.
  
   ![Impostare il rilevamento delle minacce](./media/howto-database-threat-protection-portal/set-up-threat-protection.png)

## <a name="explore-anomalous-database-activities"></a>Esplorare le attività anomale del database

Si riceverà una notifica tramite posta elettronica al rilevamento di attività di database anomale. Il messaggio di posta elettronica fornirà informazioni sull'evento di sicurezza sospetto, inclusi la natura delle attività anomale, il nome del database, il nome del server, il nome dell'applicazione e l'ora dell'evento. Il messaggio di posta elettronica fornirà anche informazioni sulle possibili cause e le azioni consigliate per analizzare e ridurre il rischio di una potenziale minaccia al database.
    
1. Fare clic sul collegamento **View recent alerts** (Visualizza gli avvisi recenti) nel messaggio di posta elettronica per avviare il portale di Azure e visualizzare la pagina degli avvisi del Centro sicurezza di Azure, con una panoramica delle minacce attive rilevate nel database di SQL.
    
    ![Report Anomalie dell'attività](./media/howto-database-threat-protection-portal/anomalous-activity-report.png)

    Visualizzare le minacce attive:

    ![Minacce attive](./media/howto-database-threat-protection-portal/active-threats.png)

2. Fare clic su uno specifico avviso per visualizzare altri dettagli e azioni per analizzare la minaccia e risolvere eventuali minacce future.
    
    ![Avviso specifico](./media/howto-database-threat-protection-portal/specific-alert.png)

## <a name="explore-threat-detection-alerts"></a>Esplorare gli avvisi di rilevamento minacce

Advanced Threat Protection integra i suoi avvisi con il [Centro sicurezza di Azure](https://azure.microsoft.com/services/security-center/). 

Fare clic su **Avvisi di sicurezza** sotto **PROTEZIONE DALLE MINACCE** per avviare la pagina degli avvisi del Centro sicurezza di Azure e ottenere una panoramica delle minacce SQL attive rilevate nel database.

  ![Threat protectoin asc](./media/howto-database-threat-protection-portal/threat-detection-alert-asc.png)

## <a name="next-steps"></a>Passaggi successivi

* Altre informazioni sul [Centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/security-center-intro)
* Per altre informazioni sui prezzi, vedere la [pagina sui Prezzi di Database di Azure per PostgreSQL](https://azure.microsoft.com/pricing/details/postgresql/)  
