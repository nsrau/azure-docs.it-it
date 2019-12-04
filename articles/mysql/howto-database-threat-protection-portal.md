---
title: Advanced Threat Protection-portale di Azure-database di Azure per MySQL
description: Informazioni su come configurare Advanced Threat Protection per rilevare attività di database anomale che indicano potenziali minacce alla sicurezza per il database.
author: bolzmj
ms.author: mbolz
ms.service: mysql
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: 11ac05c710f7daf709ffbc29aebff706aea704d5
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74765047"
---
# <a name="advanced-threat-protection-for-azure-database-for-mysql"></a>Advanced Threat Protection per Database di Azure per MySQL

Advanced Threat Protection per Database di Azure per MySQL rileva attività anomale che indicano tentativi insoliti e potenzialmente dannosi di accedere o sfruttare i database.

Advanced Threat Protection fa parte dell'offerta Advanced Data Security (Sicurezza dei dati avanzata), che è un pacchetto unificato per le funzionalità avanzate di sicurezza. È possibile accedere e gestire Advanced Threat Protection tramite il [portale di Azure](https://portal.azure.com) ed è attualmente in anteprima.

> [!NOTE]
> La funzionalità Advanced Threat Protection **non** è disponibile nelle aree geografiche cloud sovrani di Azure: US Gov Texas, US Gov Arizona, US Gov Iowa, Stati Uniti, Gov Virginia, US DoD (area orientale), US DoD (area centrale), Germania centrale, Germania settentrionale, Cina orientale, Cina orientale 2. Visitare [i prodotti disponibili in base all'area](https://azure.microsoft.com/global-infrastructure/services/) per verificare la disponibilità generale del prodotto.
>

> [!NOTE]
> Questa funzionalità è disponibile in tutte le aree di Azure in cui Database di Azure per MySQL viene distribuito nei server per utilizzo generico e ottimizzati per la memoria.

## <a name="set-up-threat-detection"></a>Impostare il rilevamento delle minacce
1. Avviare il portale di Azure all'indirizzo [https://portal.azure.com](https://portal.azure.com).
2. Passare alla pagina di configurazione del Database di Azure per MySQL che si vuole proteggere. Nelle impostazioni di sicurezza selezionare **Advanced Threat Protection (anteprima)** .
3. Nella pagina di configurazione **Advanced Threat Protection (anteprima)** :

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

Rilevamento minacce del database SQL integra i suoi avvisi con il [Centro sicurezza di Azure](https://azure.microsoft.com/services/security-center/). Un riquadro Live SQL Threat detection tiene traccia dello stato delle minacce attive nel database e nelle pagine di SQL ATP nel portale di Azure.

Fare clic su **Avviso rilevamento minacce**per avviare la pagina degli avvisi del Centro sicurezza di Azure e ottenere una panoramica delle minacce SQL attive rilevate nel database.

   ![Avviso di rilevamento minacce](./media/howto-database-threat-protection-portal/threat-detection-alert-asc.png)
   

## <a name="next-steps"></a>Passaggi successivi

* Altre informazioni sul [Centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/security-center-intro)
* Per altre informazioni sui prezzi, vedere la [pagina sui Prezzi di Database di Azure per MySQL](https://azure.microsoft.com/pricing/details/mysql/)  
