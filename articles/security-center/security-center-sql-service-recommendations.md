---
title: Protezione del servizio SQL di Azure e dei dati nel Centro sicurezza di Azure | Documentazione Microsoft
description: Questo documento illustra le raccomandazioni presenti nel Centro sicurezza di Azure che facilitano la protezione dei dati e del servizio SQL di Azure e garantiscano la conformità ai criteri di sicurezza.
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: ''
ms.assetid: bcae6987-05d0-4208-bca8-6a6ce7c9a1e3
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/27/2019
ms.author: monhaber
ms.openlocfilehash: bbba5f380fddb4fdec43a7414e59778135c4e0ef
ms.sourcegitcommit: ef06b169f96297396fc24d97ac4223cabcf9ac33
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/31/2019
ms.locfileid: "66428303"
---
# <a name="protecting-azure-sql-service-and-data-in-azure-security-center"></a>Protezione del servizio SQL di Azure e dei dati nel Centro sicurezza di Azure
Il Centro sicurezza di Azure analizza lo stato di sicurezza delle risorse di Azure. Quando il Centro sicurezza identifica potenziali vulnerabilità della sicurezza, crea raccomandazioni utili per definire il processo di configurazione dei controlli necessari.  Le raccomandazioni sono applicabili a diversi tipi di risorse di Azure, ovvero macchine virtuali, risorse di rete, SQL, dati e applicazioni.


### <a name="monitor-data-security"></a>Monitorare la sicurezza dei dati

Quando si fa clic su **Sicurezza dei dati** nella sezione **Prevenzione**, si apre il pannello **Risorse dati** con consigli relativi a SQL e all'archiviazione. Il pannello include anche [raccomandazioni](security-center-sql-service-recommendations.md) sullo stato di integrità generale del database. Per altre informazioni sulla crittografia per l'archiviazione, vedere [Enable encryption for Azure storage account in Azure Security Center](security-center-enable-encryption-for-storage-account.md) (Abilitare la crittografia per l'account di archiviazione di Azure nel Centro sicurezza di Azure).

![Risorse dati](./media/security-center-monitoring/security-center-monitoring-fig13-newUI-2017.png)

In **Raccomandazioni su SQL** è possibile fare clic su qualsiasi raccomandazione e ottenere maggiori dettagli sulle ulteriori azioni per risolvere un problema. L'esempio seguente illustra l'espansione della raccomandazione **Database Auditing & Threat detection on SQL databases** (Controllo database e rilevamento minacce nei database SQL).

![Informazioni dettagliate su una raccomandazione SQL](./media/security-center-monitoring/security-center-monitoring-fig14-ga-new.png)

Il pannello **Abilita il controllo e il rilevamento minacce nei database SQL** contiene le informazioni seguenti:

* Elenco di database SQL.
* Server in cui si trovano.
* Informazioni sull'impostazione, ovvero se è stata ereditata dal server o se è univoca in questo database.
* Stato corrente.
* Gravità del problema.

Quando si fa clic sul database per applicare la raccomandazione, viene aperto il pannello **Controllo e rilevamento minacce** come illustrato nella schermata seguente.

![Controllo e rilevamento minacce](./media/security-center-monitoring/security-center-monitoring-fig15-ga.png)

Per abilitare il controllo, selezionare semplicemente **SÌ** nell'opzione **Controllo**.

## <a name="data-and-storage-recommendations"></a>Raccomandazioni per i dati e l'archiviazione

|Tipo di risorsa|Punteggio di sicurezza|Recommendation|Descrizione|
|----|----|----|----|
|Account di archiviazione|20|Trasferimento sicuro per gli account di archiviazione deve essere abilitato|Il trasferimento sicuro è un'opzione che impone all'account di archiviazione di accettare richieste solo da connessioni sicure (HTTPS). L'uso di HTTPS garantisce l'autenticazione tra il server e il servizio e protegge i dati in transito dagli attacchi a livello rete, come attacchi man-in-the-middle, eavesdropping e hijack della sessione.|
|Redis|20|Devono essere abilitate solo connessioni protette per la Cache Redis|Consenti solo connessioni sicure alla Cache Redis di Azure tramite SSL. L'uso di connessioni sicure garantisce l'autenticazione tra il server e il servizio e protegge i dati in transito dagli attacchi a livello rete, come attacchi man-in-the-middle, eavesdropping e hijack della sessione.|
|SQL|15|Abilitare Transparent Data Encryption nei database SQL|Abilitare Transparent Data Encryption per proteggere i dati inattivi e rispettare i requisiti di conformità.|
|SQL|15|Controllo di SQL server deve essere abilitato.|Abilitare il controllo per i server SQL. (Solo servizio Azure SQL. Non include istanze di SQL in esecuzione nelle macchine virtuali.)|
|Data Lake Analytics|5|I log di diagnostica in Data Lake Analitica devono essere abilitati|Abilitare i log e conservarli fino a un anno. Ciò consente di ricreare la traccia delle attività per scopi di analisi quando si verifica un evento imprevisto della sicurezza o la rete viene compromessa. |
|Data Lake Store|5|I log di diagnostica in Azure Data Lake Store devono essere abilitati|Abilitare i log e conservarli fino a un anno. Questo consente di ricreare la traccia delle attività per scopi di analisi quando si verifica un evento imprevisto della sicurezza o la rete viene compromessa. |
|SQL|30|Le vulnerabilità nei database SQL devono essere corretti.|La funzionalità di valutazione della vulnerabilità di SQL analizza il database per individuare vulnerabilità a livello di sicurezza ed espone eventuali scostamenti dalle procedure consigliate, ad esempio configurazioni errate, autorizzazioni eccessive e dati sensibili non protetti. La risoluzione delle vulnerabilità rilevate può migliorare significativamente il livello di sicurezza del database.|
|SQL|20|Effettua il provisioning di un amministratore di Azure AD per SQL Server|Effettuare il provisioning di un amministratore di Azure AD per il server SQL per abilitare l'autenticazione di Azure AD. Questo tipo di autenticazione consente una gestione semplificata delle autorizzazioni e una gestione centralizzata delle identità degli utenti di database e di altri servizi Microsoft.|
|Account di archiviazione|15|Accesso agli account di archiviazione con firewall e le configurazioni di rete virtuale deve essere limitato|Controllare l'accesso alla rete senza restrizioni nelle impostazioni del firewall dell'account di archiviazione. Configurare invece regole di rete in modo che l'account di archiviazione sia accessibile solo alle applicazioni provenienti da reti consentite. Per consentire le connessioni da Internet specifici o ai client in locale, è possibile concedere l'accesso per il traffico proveniente da reti virtuali di Azure specifiche o a intervalli di indirizzi IP Internet pubblici.|
|Account di archiviazione|1|Gli account di archiviazione devono essere migrati a nuove risorse di Azure Resource Manager|Usare nuova v2 di Azure Resource Manager per gli account di archiviazione per fornire miglioramenti della sicurezza, ad esempio: controllo di accesso più avanzato (RBAC), il controllo migliore, distribuzione basata su Resource Manager e governance, accedere alle identità gestita, l'accesso a key vault per i segreti e l'autenticazione basata su Active Directory di Azure e il supporto per i tag e i gruppi di risorse per facilitare la gestione di sicurezza.|

## <a name="see-also"></a>Vedere anche
Per altre informazioni sulle raccomandazioni applicabili ad altri tipi di risorse di Azure, vedere gli argomenti seguenti:

* [Protezione delle macchine virtuali nel Centro sicurezza di Azure](security-center-virtual-machine-recommendations.md)
* [Protecting your applications in Azure Security Center](security-center-application-recommendations.md)
* [Protezione della rete nel Centro sicurezza di Azure](security-center-network-recommendations.md)

Per altre informazioni sul Centro sicurezza, vedere gli argomenti seguenti:

* [Impostazione dei criteri di sicurezza nel Centro sicurezza di Azure](tutorial-security-policy.md) : informazioni su come configurare i criteri di sicurezza per le sottoscrizioni e i gruppi di risorse di Azure.
* [Gestione e risposta agli avvisi di sicurezza nel Centro sicurezza di Azure](security-center-managing-and-responding-alerts.md) : informazioni su come gestire e rispondere agli avvisi di sicurezza.
* [Domande frequenti sul Centro sicurezza di Azure](security-center-faq.md) : domande frequenti sull'uso del servizio.
