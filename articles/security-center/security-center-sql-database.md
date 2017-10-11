---
title: Centro sicurezza di Azure e servizio Database SQL di Azure | Microsoft Docs
description: Questo articolo illustra come il Centro sicurezza consente di proteggere i database nel servizio Database SQL di Azure.
services: sql-database
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: f109adfd-daed-4257-9692-2042a1399480
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/02/2017
ms.author: terrylan
ms.openlocfilehash: 46dd298a5664d914e55d45c5b7599d5983287476
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2017
---
# <a name="azure-security-center-and-azure-sql-database-service"></a>Centro sicurezza di Azure e servizio Database SQL di Azure
Il [Centro sicurezza di Azure](https://azure.microsoft.com/documentation/services/security-center/) consente di impedire, rilevare e gestire le minacce. Offre funzionalità integrate di monitoraggio della sicurezza e gestione dei criteri tra le sottoscrizioni di Azure, facilita il rilevamento delle minacce che altrimenti passerebbero inosservate e funziona con un ampio ecosistema di soluzioni di sicurezza.

Questo articolo illustra come il Centro sicurezza consente di proteggere i database nel servizio Database SQL di Azure.

## <a name="why-use-security-center"></a>Perché usare il Centro sicurezza?
Il Centro sicurezza consente di proteggere i dati nel database SQL fornendo visibilità per la sicurezza di tutti i server e database. Con il Centro sicurezza è possibile:

* Definire i criteri per la crittografia e il controllo del database SQL.
* Monitorare la sicurezza delle risorse del database SQL per tutte le sottoscrizioni.
* Identificare e correggere rapidamente i problemi di sicurezza.
* Integrare gli avvisi generati dal [rilevamento delle minacce del database SQL di Azure](../sql-database/sql-database-threat-detection.md).

Oltre ad aiutare a proteggere le risorse del database SQL, il Centro sicurezza offre funzionalità per il monitoraggio e la gestione della sicurezza per le macchine virtuali di Azure, i servizi cloud, i servizi app, le reti virtuali e altro ancora. Per altre informazioni sul Centro sicurezza, fare clic [qui](security-center-intro.md).

## <a name="prerequisites"></a>Prerequisiti
Per iniziare a usare Centro sicurezza, è necessario avere una sottoscrizione di Microsoft Azure. Il livello gratuito del Centro sicurezza viene abilitato con la sottoscrizione. Per altre informazioni sui livelli gratuito e standard del Centro sicurezza, vedere [Centro sicurezza Prezzi](https://azure.microsoft.com/pricing/details/security-center/).

Il Centro sicurezza supporta l'accesso in base al ruolo. Per ulteriori informazioni sul controllo di accesso in base al ruolo (RBAC) in Azure, vedere [Controllo di accesso in base al ruolo di Azure Active Directory](../active-directory/role-based-access-control-configure.md). Le domande frequenti relative al Centro sicurezza offrono informazioni sulla [modalità di gestione delle autorizzazioni nel Centro sicurezza](security-center-faq.md#permissions).

## <a name="access-security-center"></a>Accedere al Centro sicurezza
È possibile accedere al Centro sicurezza tramite il [portale di Azure](https://azure.microsoft.com/features/azure-portal/). [Accedere al portale](https://portal.azure.com/) e selezionare l'opzione **Centro sicurezza**.

![Opzione Centro sicurezza][1]

Verrà visualizzato il pannello **Centro sicurezza**.
![Pannello Centro sicurezza][2]

## <a name="set-security-policy"></a>Impostare i criteri di sicurezza
I criteri di sicurezza definiscono il set di controlli consigliati per le risorse nell'ambito della sottoscrizione o del gruppo di risorse specificato. Nel Centro sicurezza è possibile definire i criteri per le sottoscrizioni o i gruppi di risorse in base alle esigenze di sicurezza della propria società e al tipo di applicazioni o al livello di riservatezza dei dati di ogni sottoscrizione.

È possibile impostare criteri per visualizzare le raccomandazioni per il controllo SQL e la crittografia SQL Transparent Data Encryption (TDE).

* Quando si abilita la funzionalità di **controllo e rilevamento delle minacce SQL**, il Centro sicurezza suggerisce l'abilitazione del controllo dell'accesso al database di Azure per scopi di conformità e di rilevamento e analisi avanzati.
* Quando si abilita la crittografia **SQL Transparent Data Encryption**, il Centro sicurezza suggerisce l'abilitazione della crittografia dati inattivi per il database SQL di Azure, i backup associati e file di log delle transazioni.

Per impostare criteri di sicurezza, selezionare il riquadro **Criteri** nel pannello Centro sicurezza. Nel pannello **Criteri di sicurezza** selezionare la sottoscrizione in cui abilitare i criteri di sicurezza. Selezionare **Criteri di prevenzione** e impostare su **On** le raccomandazioni di sicurezza da usare per la sottoscrizione.
![Criteri di sicurezza][3]

Per altre informazioni, vedere [Impostare i criteri di sicurezza](security-center-policies.md).

## <a name="manage-security-recommendation"></a>Gestire una raccomandazione di sicurezza
Il Centro sicurezza analizza periodicamente lo stato di sicurezza delle risorse di Azure. Quando identifica potenziali vulnerabilità della sicurezza, crea raccomandazioni. Queste raccomandazioni illustrano in dettaglio il processo di configurazione dei controlli necessari.

Dopo l'impostazione dei criteri di sicurezza, il Centro sicurezza analizza lo stato di sicurezza delle risorse per identificare le potenziali vulnerabilità. Le raccomandazioni vengono visualizzate sotto forma di tabella, dove ogni riga rappresenta una particolare raccomandazione. Usare la tabella seguente come riferimento per comprendere le raccomandazioni disponibili per il database SQL di Azure e l'effetto prodotto da ogni raccomandazione che viene applicata. Se si seleziona una raccomandazione, viene visualizzato un articolo che illustra come implementare la raccomandazione nel Centro sicurezza.

| Raccomandazione | Descrizione |
| --- | --- |
| [Enable Auditing & Threat detection on SQL servers](security-center-enable-auditing-on-sql-servers.md) (Abilita il controllo e il rilevamento delle minacce nei server SQL) |Raccomanda di abilitare il controllo e il rilevamento delle minacce per i server di database SQL (solo per il servizio Database SQL; non prevede l'esecuzione di Microsoft SQL Server sulle macchine virtuali). |
| [Enable Auditing & Threat detection on SQL databases](security-center-enable-auditing-on-sql-databases.md) (Abilita il controllo e il rilevamento delle minacce nei database SQL) |Raccomanda di abilitare il controllo e il rilevamento delle minacce per i database del servizio database SQL (solo per il servizio Database SQL; non prevede l'esecuzione di Microsoft SQL Server sulle macchine virtuali). |
| [Abilita Transparent Data Encryption](security-center-enable-transparent-data-encryption.md) |Raccomanda di abilitare la crittografia per i database SQL (solo per il servizio Database SQL). |

Per visualizzare le raccomandazioni per le risorse di Azure, selezionare il riquadro **Raccomandazioni** nel pannello Centro sicurezza. Nel pannello **Raccomandazioni** selezionare una raccomandazione per visualizzare i dettagli. In questo esempio si seleziona **Enable Auditing & Threat detection on SQL servers** (Abilita il controllo e il rilevamento delle minacce nei server SQL).

![Raccomandazioni][4]

Come illustrato di seguito, il Centro sicurezza mostra i server SQL in cui il controllo e il rilevamento delle minacce non sono abilitati. Dopo aver abilitato il controllo, è possibile configurare le impostazioni di rilevamento delle minacce e di posta elettronica per ricevere gli avvisi di sicurezza. La funzionalità di rilevamento delle minacce visualizza un avviso quando rileva attività di database che indicano la presenza di potenziali minacce alla sicurezza nel database. Gli avvisi vengono visualizzati nel dashboard del Centro sicurezza.
![Controllo e rilevamento delle minacce][5]

Seguire i passaggi in [SQL Database Threat Detection in the Azure portal](../sql-database/sql-database-threat-detection-portal.md) (Rilevamento di minacce del database SQL nel portale di Azure) per attivare e configurare il rilevamento di minacce e per configurare l'elenco dei messaggi di posta elettronica che riceveranno gli avvisi di sicurezza al rilevamento di anomalie dell'attività.

Per altre informazioni sulle raccomandazioni, vedere [Gestione delle raccomandazioni di sicurezza](security-center-recommendations.md).

## <a name="monitor-security-health"></a>Monitorare l'integrità della sicurezza
Una volta abilitati i [criteri di sicurezza](security-center-policies.md) per le risorse di una sottoscrizione, il Centro sicurezza analizza la sicurezza delle risorse per identificare le potenziali vulnerabilità.  Nel riquadro **Integrità sicurezza delle risorse** è possibile visualizzare lo stato di sicurezza delle risorse. Quando si fa clic su **Dati** nel riquadro **Integrità sicurezza delle risorse**, viene aperto il pannello **Data Resources** (Risorse dati) con le raccomandazioni SQL relative a problemi come la mancata abilitazione del controllo e di Transparent Data Encryption. Include anche raccomandazioni relative allo stato di integrità generale del database.
![Integrità della sicurezza delle risorse][6]

Per altre informazioni, vedere [Monitoraggio dell'integrità della sicurezza](security-center-monitoring.md).

## <a name="manage-and-respond-to-security-alerts"></a>Gestire e rispondere agli avvisi di sicurezza
Il Centro sicurezza raccoglie, analizza e integra automaticamente i dati di log generati dalla funzionalità di [rilevamento delle minacce del database SQL di Azure](../sql-database/sql-database-threat-detection.md) e da altre risorse di Azure per rilevare le minacce reali e ridurre i falsi positivi. Il Centro sicurezza visualizza un elenco degli avvisi di sicurezza in ordine di priorità, nonché le informazioni necessarie per analizzare rapidamente il problema e indicazioni per risolvere un attacco.

Per visualizzare gli avvisi, selezionare il riquadro **Avvisi di sicurezza** nel pannello Centro sicurezza. Nel pannello **Avvisi di sicurezza** selezionare un avviso per visualizzare altre informazioni sugli eventi che hanno attivato l'avviso ed eventualmente i passaggi da seguire per risolvere i problemi associati a un attacco. In questo esempio si seleziona **Potential SQL Injection** (Potenziale attacco SQL injection).
![Avvisi di sicurezza][7]

Come illustrato di seguito, il Centro sicurezza visualizza altre informazioni di approfondimento sull'evento che ha attivato l'avviso, la risorsa di destinazione, l'indirizzo IP di origine, quando applicabile, ed eventuali raccomandazioni per risolvere il problema.
![Potenziale attacco SQL injection][8]

Per altre informazioni, vedere [Gestione e risposta agli avvisi di sicurezza](security-center-managing-and-responding-alerts.md).

## <a name="next-steps"></a>Passaggi successivi
* [Domande frequenti sul Centro sicurezza](security-center-faq.md): leggere le domande frequenti sull'uso del servizio.
* [Guida alla pianificazione e alla gestione del Centro sicurezza](security-center-planning-and-operations-guide.md): eseguire una serie di passaggi e attività per ottimizzare l'uso del Centro sicurezza in base ai requisiti di sicurezza e al modello di gestione cloud dell'organizzazione.
* [Sicurezza dei dati nel Centro sicurezza](security-center-data-security.md): apprendere come il Centro sicurezza raccoglie ed elabora i dati sulle risorse di Azure, tra cui informazioni di configurazione, metadati, registri eventi, file di dump di arresto anomalo del sistema e altro.
* [Gestione degli eventi imprevisti della sicurezza](security-center-incident.md): imparare a usare la funzionalità degli avvisi del Centro sicurezza per gestire gli eventi imprevisti della sicurezza.

<!--Image references-->
[1]: ./media/security-center-sql-database/security-center.png
[2]: ./media/security-center-sql-database/security-center-blade.png
[3]: ./media/security-center-sql-database/security-policy.png
[4]: ./media/security-center-sql-database/recommendation.png
[5]: ./media/security-center-sql-database/turn-on-auditing.png
[6]: ./media/security-center-sql-database/monitor-health.png
[7]: ./media/security-center-sql-database/alert.png
[8]: ./media/security-center-sql-database/sql-injection.png
