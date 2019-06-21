---
title: Proteggere i servizi di dati e archiviazione di Azure nel Centro sicurezza di Azure | Microsoft Docs
description: Questo documento illustra le raccomandazioni presenti nel Centro sicurezza di Azure che facilitano la protezione dei dati e del servizio SQL di Azure e garantiscano la conformità ai criteri di sicurezza.
services: security-center
documentationcenter: na
author: monhaber
manager: rkarlin
editor: ''
ms.assetid: bcae6987-05d0-4208-bca8-6a6ce7c9a1e3
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/19/2019
ms.author: monhaber
ms.openlocfilehash: 2ac0e4ebaafb8b0c9c79e885cecbefc5a65c1823
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/20/2019
ms.locfileid: "67275279"
---
# <a name="protect-azure-data-and-storage-services-in-azure-security-center"></a>Proteggere i servizi di dati e archiviazione di Azure nel Centro sicurezza di Azure
In questo argomento viene illustrato come visualizzare e implementare le raccomandazioni sulla sicurezza per le risorse di archiviazione e dati. Centro sicurezza di Azure trovare queste indicazioni durante l'analisi lo stato di sicurezza delle risorse di Azure.

## <a name="view-your-data-security-information"></a>Visualizzare le informazioni di sicurezza dei dati

1. Nel **igiene security Resource** fare clic su **risorse dati e archiviazione**.

   ![Risorse di archiviazione & dati](./media/security-center-monitoring/click-data.png)

    Il **la protezione dei dati** verrà visualizzata la pagina con raccomandazioni per le risorse di dati.

     ![Risorse dati](./media/security-center-monitoring/sql-overview.png)

In questa pagina è possibile:

* Fare clic sui **Panoramica** scheda sono elencate tutte le indicazioni di risorse di dati per essere risolti. 
* Fare clic su ogni scheda e visualizzare i suggerimenti in base al tipo di risorsa.

    > [!NOTE]
    > Per altre informazioni sulla crittografia per l'archiviazione, vedere [Enable encryption for Azure storage account in Azure Security Center](security-center-enable-encryption-for-storage-account.md) (Abilitare la crittografia per l'account di archiviazione di Azure nel Centro sicurezza di Azure).


## <a name="remediate-a-recommendation-on-a-data-resource"></a>Monitora e aggiorna una raccomandazione su una risorsa di dati

1. Una delle schede della risorsa, fare clic su una risorsa. Verrà visualizzata la pagina delle informazioni le raccomandazioni per essere risolti.

    ![Informazioni sulle risorse](./media/security-center-monitoring/sql-recommendations.png)

2. Fare clic su una raccomandazione. Pagina di raccomandazione si apre e visualizza i **i passaggi correttivi** implementare la raccomandazione.

   ![Procedura di correzione](./media/security-center-monitoring/remediate1.png)

3. Fare clic su **intervenire**. Viene visualizzata la pagina di impostazioni di risorsa.

    ![Abilitare la raccomandazione](./media/security-center-monitoring/remediate2.png)

4. Seguire le **i passaggi correttivi** e fare clic su **salvare**.

## <a name="data-and-storage-recommendations"></a>Raccomandazioni per i dati e l'archiviazione

|Tipo di risorsa|Punteggio di sicurezza|Recommendation|Descrizione|
|----|----|----|----|
|Account di archiviazione|20|Trasferimento sicuro per gli account di archiviazione deve essere abilitato|Il trasferimento sicuro è un'opzione che impone all'account di archiviazione di accettare richieste solo da connessioni sicure (HTTPS). HTTPS assicura l'autenticazione tra il server e il servizio e protegge i dati in transito da attacchi a livello di rete, ad esempio man-in-the-middle, intercettazione e Hijack della sessione.|
|Redis|20|Devono essere abilitate solo connessioni protette per la Cache Redis|Consenti solo connessioni sicure alla Cache Redis di Azure tramite SSL. L'uso di connessioni sicure garantisce l'autenticazione tra il server e il servizio e protegge i dati in transito dagli attacchi a livello rete, come attacchi man-in-the-middle, eavesdropping e hijack della sessione.|
|SQL|15|Abilitare Transparent Data Encryption nei database SQL|Abilitare Transparent Data Encryption per proteggere i dati inattivi e rispettare i requisiti di conformità.|
|SQL|15|Controllo di SQL server deve essere abilitato.|Abilitare il controllo per i server SQL. (Solo servizio Azure SQL. Non include istanze di SQL in esecuzione nelle macchine virtuali.)|
|Data Lake Analytics|5|I log di diagnostica in Data Lake Analitica devono essere abilitati|Abilitare i log e conservarli fino a un anno. Ciò consente di ricreare la traccia delle attività per scopi di analisi quando si verifica un evento imprevisto della sicurezza o la rete viene compromessa. |
|Data Lake Store|5|I log di diagnostica in Azure Data Lake Store devono essere abilitati|Abilitare i log e conservarli fino a un anno. Questo consente di ricreare la traccia delle attività per scopi di analisi quando si verifica un evento imprevisto della sicurezza o la rete viene compromessa. |
|SQL|30|Le vulnerabilità nei database SQL devono essere corretti.|Valutazione della vulnerabilità SQL esegue l'analisi delle vulnerabilità di sicurezza nel database ed espone tutte le deviazioni dalle procedure consigliate, ad esempio configurazioni errate, autorizzazioni eccessive e dati sensibili non protetti. La risoluzione delle vulnerabilità rilevate può migliorare significativamente il livello di sicurezza del database.|
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
