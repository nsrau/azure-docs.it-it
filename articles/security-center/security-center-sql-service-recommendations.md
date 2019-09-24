---
title: Proteggere i servizi di archiviazione e dati di Azure nel centro sicurezza di Azure | Microsoft Docs
description: Questo documento illustra le raccomandazioni presenti nel Centro sicurezza di Azure che facilitano la protezione dei dati e del servizio SQL di Azure e garantiscano la conformità ai criteri di sicurezza.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: bcae6987-05d0-4208-bca8-6a6ce7c9a1e3
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/19/2019
ms.author: memildin
ms.openlocfilehash: 80611fe0c37af7dfd27b561186f3e967ad3159b4
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/23/2019
ms.locfileid: "71201028"
---
# <a name="protect-azure-data-and-storage-services-in-azure-security-center"></a>Proteggere i servizi di archiviazione e dati di Azure nel centro sicurezza di Azure
Questo argomento illustra come visualizzare e implementare le raccomandazioni sulla sicurezza per i dati e le risorse di archiviazione. Il Centro sicurezza di Azure ha rilevato questi consigli durante l'analisi dello stato di sicurezza delle risorse di Azure.

## <a name="view-your-data-security-information"></a>Visualizzare le informazioni sulla sicurezza dei dati

1. Nella sezione **igiene della sicurezza delle risorse** fare clic su **risorse di archiviazione e dati**.

   ![Risorse di archiviazione & dati](./media/security-center-monitoring/click-data.png)

    Verrà visualizzata la pagina **sicurezza dati** con le raccomandazioni per le risorse di dati.

     ![Risorse dati](./media/security-center-monitoring/sql-overview.png)

Da questa pagina è possibile:

* Fare clic sulla scheda **Panoramica** per elencare tutte le raccomandazioni sulle risorse dati da correggere. 
* Fare clic su ogni scheda e visualizzare le raccomandazioni per tipo di risorsa.

    > [!NOTE]
    > Per altre informazioni sulla crittografia di archiviazione, vedere [crittografia di archiviazione di Azure per dati](../storage/common/storage-service-encryption.md)inattivi.


## <a name="remediate-a-recommendation-on-a-data-resource"></a>Correggere una raccomandazione su una risorsa di dati

1. In una delle schede delle risorse fare clic su una risorsa. Verrà visualizzata la pagina informazioni che elenca le raccomandazioni da correggere.

    ![Informazioni sulle risorse](./media/security-center-monitoring/sql-recommendations.png)

2. Fare clic su una raccomandazione. Verrà visualizzata la pagina raccomandazione con i **passaggi correttivi** per implementare la raccomandazione.

   ![Procedura di correzione](./media/security-center-monitoring/remediate1.png)

3. Fare clic su **intraprendere un'azione**. Verrà visualizzata la pagina Impostazioni risorsa.

    ![Abilita raccomandazione](./media/security-center-monitoring/remediate2.png)

4. Attenersi alla **procedura di correzione** e fare clic su **Salva**.

## <a name="data-and-storage-recommendations"></a>Raccomandazioni per i dati e l'archiviazione

|Tipo di risorsa|Punteggio di sicurezza|Indicazione|Descrizione|
|----|----|----|----|
|Account di archiviazione|20|Il trasferimento sicuro negli account di archiviazione deve essere abilitato|Il trasferimento sicuro è un'opzione che impone all'account di archiviazione di accettare richieste solo da connessioni sicure (HTTPS). HTTPS assicura l'autenticazione tra il server e il servizio e protegge i dati in transito da attacchi a livello di rete, ad esempio Man-in-the-Middle, intercettazione e Hijack della sessione.|
|Redis|20|Devono essere abilitate solo connessioni sicure alla Cache Redis|Consenti solo connessioni sicure alla Cache Redis di Azure tramite SSL. L'uso di connessioni sicure garantisce l'autenticazione tra il server e il servizio e protegge i dati in transito dagli attacchi a livello rete, come attacchi man-in-the-middle, eavesdropping e hijack della sessione.|
|SQL|15|Transparent Data Encryption deve essere abilitata nei database SQL|Abilitare Transparent Data Encryption per proteggere i dati inattivi e rispettare i requisiti di conformità.|
|SQL|15|Il controllo di SQL Server deve essere abilitato|Abilitare il controllo per i server SQL. (Solo servizio Azure SQL. Non include istanze di SQL in esecuzione nelle macchine virtuali.)|
|Data Lake Analytics|5|I log di diagnostica in Data Lake Analytics devono essere abilitati|Abilitare i log e conservarli per un periodo massimo di un anno. Ciò consente di ricreare la traccia delle attività per scopi di analisi quando si verifica un evento imprevisto della sicurezza o la rete viene compromessa. |
|Data Lake Store|5|I log di diagnostica in Azure Data Lake Store devono essere abilitati|Abilitare i log e conservarli per un periodo massimo di un anno. Ciò consente di ricreare la traccia delle attività per scopi di analisi quando si verifica un evento imprevisto della sicurezza o la rete viene compromessa. |
|SQL|30|Le vulnerabilità dei database SQL devono essere risolte|La valutazione della vulnerabilità di SQL analizza il database per individuare le vulnerabilità di sicurezza ed espone eventuali deviazioni dalle procedure consigliate, ad esempio configurazioni errate, autorizzazioni eccessive e dati sensibili non protetti. La risoluzione delle vulnerabilità rilevate può migliorare significativamente il livello di sicurezza del database.|
|SQL|20|Effettua il provisioning di un amministratore di Azure AD per SQL Server|Effettuare il provisioning di un amministratore di Azure AD per il server SQL per abilitare l'autenticazione di Azure AD. Questo tipo di autenticazione consente una gestione semplificata delle autorizzazioni e una gestione centralizzata delle identità degli utenti di database e di altri servizi Microsoft.|
|Account di archiviazione|15|L'accesso agli account di archiviazione con configurazioni del firewall e della rete virtuale deve essere limitato|Controllare l'accesso alla rete senza restrizioni nelle impostazioni del firewall dell'account di archiviazione. Configurare invece regole di rete in modo che l'account di archiviazione sia accessibile solo alle applicazioni provenienti da reti consentite. Per consentire le connessioni da client Internet o locali specifici, è possibile concedere l'accesso al traffico da reti virtuali di Azure specifiche o a intervalli di indirizzi IP Internet pubblici.|
|Account di archiviazione|1|È necessario eseguire la migrazione degli account di archiviazione alle nuove risorse Azure Resource Manager|Usa il nuovo Azure Resource Manager V2 per gli account di archiviazione per offrire miglioramenti alla sicurezza, ad esempio il controllo degli accessi più sicuro (RBAC), il controllo migliore, la distribuzione e la governance basati su Gestione risorse, l'accesso alle identità gestite, l'accesso a Key Vault per segreti e autenticazione basata su Azure AD e supporto per i tag e i gruppi di risorse per semplificare la gestione della sicurezza.|

## <a name="see-also"></a>Vedere anche
Per ulteriori informazioni sulle raccomandazioni applicabili ad altri tipi di risorse di Azure, vedere gli argomenti seguenti:

* [Protezione delle macchine virtuali nel Centro sicurezza di Azure](security-center-virtual-machine-protection.md)
* [Protezione della rete nel Centro sicurezza di Azure](security-center-network-recommendations.md)

Per ulteriori informazioni sul centro sicurezza, vedere gli argomenti seguenti:

* [Impostazione dei criteri di sicurezza nel Centro sicurezza di Azure](tutorial-security-policy.md) : informazioni su come configurare i criteri di sicurezza per le sottoscrizioni e i gruppi di risorse di Azure.
* [Gestione e risposta agli avvisi di sicurezza nel Centro sicurezza di Azure](security-center-managing-and-responding-alerts.md) : informazioni su come gestire e rispondere agli avvisi di sicurezza.
* [Domande frequenti sul Centro sicurezza di Azure](security-center-faq.md) : domande frequenti sull'uso del servizio.
