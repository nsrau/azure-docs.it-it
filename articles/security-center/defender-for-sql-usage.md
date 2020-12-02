---
title: Come usare Azure Defender per SQL
description: Informazioni su come usare il piano facoltativo Azure Defender for SQL del Centro sicurezza di Azure
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: ba46c460-6ba7-48b2-a6a7-ec802dd4eec2
ms.service: security-center
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/22/2020
ms.author: memildin
ms.openlocfilehash: d37c362da484a2a7e400da8f87dd0a37f0f80d3a
ms.sourcegitcommit: 5e5a0abe60803704cf8afd407784a1c9469e545f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96436726"
---
# <a name="azure-defender-for-sql-servers-on-machines"></a>Azure Defender per SQL Server nei computer 

Questo piano di Azure Defender rileva attività anomale che indicano tentativi insoliti e potenzialmente dannosi di accesso o exploit dei database.

Si vedranno avvisi su attività di database sospette, potenziali vulnerabilità o attacchi SQL injection, oltre che anomalie negli accessi al database e nei modelli di query.

## <a name="availability"></a>Disponibilità

|Aspetto|Dettagli|
|----|:----|
|Stato della versione:|Anteprima<br>[!INCLUDE [Legalese](../../includes/security-center-preview-legal-text.md)] |
|Prezzi:|**Azure Defender per SQL Server nei computer** viene fatturato come indicato nella [pagina dei prezzi](security-center-pricing.md)|
|Versioni di SQL protette:|Azure SQL Server (tutte le versioni coperte dal supporto tecnico Microsoft)|
|Cloud:|![Sì](./media/icons/yes-icon.png) Cloud commerciali<br>![Sì](./media/icons/yes-icon.png) US Gov<br>![No](./media/icons/no-icon.png) Governo cinese, altri governi|
|||

## <a name="set-up-azure-defender-for-sql-servers-on-machines"></a>Configurare Azure Defender per SQL Server nei computer

Per abilitare il piano:

* Eseguire il provisioning dell'agente di Log Analytics nell'host di SQL Server. In questo modo viene fornita la connessione ad Azure.

* Abilitare il piano facoltativo nella pagina dei prezzi e delle impostazioni del Centro sicurezza.

Entrambi sono descritti di seguito.

### <a name="step-1-provision-the-log-analytics-agent-on-your-sql-servers-host"></a>Passaggio 1. Eseguire il provisioning dell'agente di Log Analytics nell'host di SQL Server:

- **SQL Server nella VM di Azure** : se il computer SQL è ospitato in una macchina virtuale di Azure, è possibile [abilitare il provisioning automatico <a name="auto-provision-mma"></a> dell'agente di log Analytics](security-center-enable-data-collection.md#auto-provision-mma). In alternativa, è possibile seguire la procedura manuale per l' [onboarding delle macchine virtuali Azure stack](quickstart-onboard-machines.md#onboard-your-azure-stack-vms).
- **SQL Server in Azure Arc** : se il SQL Server è gestito da server abilitati per [Azure Arc](../azure-arc/index.yml) , è possibile distribuire l'agente di log Analytics usando la raccomandazione del Centro sicurezza "log Analytics Agent deve essere installato nei computer Azure Arc basati su Windows (anteprima)". In alternativa, è possibile seguire i metodi di installazione descritti nella [documentazione di Azure Arc](../azure-arc/servers/manage-vm-extensions.md).

- **SQL Server** locale: se la SQL Server è ospitata in un computer Windows locale senza Azure Arc, sono disponibili due opzioni per la connessione ad Azure:
    
    - **Distribuire Azure Arc** : è possibile connettere qualsiasi computer Windows al centro sicurezza. Azure Arc fornisce tuttavia una maggiore integrazione in *tutti* gli ambienti di Azure. Se si configura Azure Arc, viene visualizzata la pagina **SQL Server-Azure Arc** nel portale e gli avvisi di sicurezza verranno visualizzati in una scheda **sicurezza** dedicata in tale pagina. Quindi, la prima opzione consigliata consiste nell' [impostare Azure Arc nell'host](../azure-arc/servers/onboard-portal.md#install-and-validate-the-agent-on-windows) e seguire le istruzioni per **SQL Server in Azure Arc**, sopra.
        
    - **Connettere il computer Windows senza Azure Arc** : se si sceglie di connettere un SQL Server in esecuzione in un computer Windows senza usare Azure Arc, seguire le istruzioni riportate in [connettere computer Windows a monitoraggio di Azure](../azure-monitor/platform/agent-windows.md).


### <a name="step-2-enable-the-optional-plan-in-security-centers-pricing-and-settings-page"></a>Passaggio 2: Abilitare il piano facoltativo nella pagina dei prezzi e delle impostazioni del Centro sicurezza:

1. Dal menu del Centro sicurezza aprire la pagina dei **prezzi & impostazioni** .

    - Se si usa l' **area di lavoro predefinita del Centro sicurezza di Azure** , denominata "defaultWorkspace-[ID sottoscrizione]-[area]", selezionare la **sottoscrizione** pertinente.

    - Se si usa **un'area di lavoro non predefinita**, selezionare l' **area di lavoro** pertinente (immettere il nome dell'area di lavoro nel filtro se necessario):

        ![Ricerca dell'area di lavoro non predefinita in base al titolo](./media/security-center-advanced-iaas-data/pricing-and-settings-workspaces.png)

1. Impostare l'opzione per **Azure Defender per SQL Server nei computer (anteprima)** piano **su on**. 

    ![Pagina dei prezzi del Centro sicurezza con piani facoltativi](media/security-center-advanced-iaas-data/sql-servers-on-vms-in-pricing-small.png)

    Il piano verrà abilitato in tutti i server SQL connessi all'area di lavoro selezionata. La protezione sarà completamente attiva dopo il primo riavvio dell'istanza di SQL Server.

    >[!TIP] 
    > Per creare una nuova area di lavoro, seguire le istruzioni riportate in [creare un'area di lavoro log Analytics](../azure-monitor/learn/quick-create-workspace.md).


1. Facoltativamente, configurare la notifica tramite posta elettronica per gli avvisi di sicurezza. 
    È possibile impostare un elenco di destinatari per ricevere una notifica tramite posta elettronica quando vengono generati gli avvisi del Centro sicurezza. Il messaggio di posta elettronica contiene un collegamento diretto all'avviso nel centro sicurezza di Azure con tutti i dettagli pertinenti. Per altre informazioni, vedere [configurare le notifiche di posta elettronica per gli avvisi di sicurezza](security-center-provide-security-contact-details.md).



## <a name="explore-vulnerability-assessment-reports"></a>Esplorare i report di valutazione della vulnerabilità

Il servizio di valutazione della vulnerabilità esegue l'analisi dei database una volta alla settimana. Le analisi vengono eseguite nello stesso giorno della settimana in cui è stato abilitato il servizio.

Il dashboard della valutazione della vulnerabilità fornisce una panoramica dei risultati della valutazione in tutti i database, insieme a un riepilogo di database integri e non integri, e un riepilogo generale dei controlli con esito negativo in base alla distribuzione dei rischi.

È possibile visualizzare i risultati della valutazione della vulnerabilità direttamente dal centro sicurezza.

1. Dall'intestazione laterale del Centro sicurezza, aprire la pagina **raccomandazioni** e selezionare le **vulnerabilità di raccomandazione nei server SQL sui computer da correggere (anteprima)**. Per altre informazioni, vedere [raccomandazioni del Centro sicurezza](security-center-recommendations.md). 

    :::image type="content" source="./media/security-center-advanced-iaas-data/data-and-storage-sqldb-vulns-on-vm.png" alt-text="I risultati della valutazione della vulnerabilità nei computer SQL Server devono essere corretti (anteprima)":::

    Viene visualizzata la visualizzazione dettagliata di questa raccomandazione.

    :::image type="content" source="./media/security-center-advanced-iaas-data/all-servers-view.png" alt-text="Visualizzazione dettagliata per la raccomandazione":::

1. Per altri dettagli, eseguire il drill-down:

    * Per una panoramica delle risorse analizzate (database) e l'elenco dei controlli di sicurezza testati, selezionare il server di interesse.

    * Per una panoramica delle vulnerabilità raggruppate in base a un database SQL specifico, selezionare il database di interesse.

    In ogni visualizzazione, i controlli di sicurezza sono ordinati in base alla **gravità**. Fare clic su un controllo di sicurezza specifico per visualizzare un riquadro dei dettagli con una **Descrizione**, **come risolverlo e altre** informazioni correlate, ad esempio **Impact** o **benchmark**.

## <a name="azure-defender-for-sql-alerts"></a>Avvisi di Azure Defender per SQL
Gli avvisi vengono generati da tentativi insoliti e potenzialmente dannosi di accedere o sfruttare le macchine virtuali SQL. Questi eventi possono attivare gli avvisi mostrati nella [sezione avvisi per database SQL e Azure sinapsi Analytics (in precedenza SQL Data Warehouse) della pagina di riferimento per gli avvisi](alerts-reference.md#alerts-sql-db-and-warehouse).

## <a name="explore-and-investigate-security-alerts"></a>Esplorare ed esaminare gli avvisi di sicurezza

Gli avvisi di Azure Defender per SQL sono disponibili nella pagina degli avvisi del Centro sicurezza, nella scheda sicurezza della risorsa, nel [dashboard di Azure Defender](azure-defender-dashboard.md)o tramite il collegamento diretto nei messaggi di posta elettronica di avviso.

1. Per visualizzare gli avvisi, selezionare **avvisi di sicurezza** dal menu del Centro sicurezza e selezionare un avviso.

1. Gli avvisi sono progettati per essere indipendenti, con procedure dettagliate per la correzione e informazioni di analisi in ognuna di esse. È possibile approfondire ulteriormente l'uso di altre funzionalità del Centro sicurezza di Azure e di Azure Sentinel per una visualizzazione più ampia:

    * Abilitare la funzionalità di controllo SQL Server per ulteriori indagini. Se si è un utente di Sentinel di Azure, è possibile caricare i log di controllo SQL dagli eventi del registro di sicurezza di Windows in Sentinel e usufruire di un'esperienza di analisi avanzata. [Altre informazioni sul controllo SQL Server](/sql/relational-databases/security/auditing/create-a-server-audit-and-server-audit-specification?preserve-view=true&view=sql-server-ver15).
    * Per migliorare il comportamento di sicurezza, usare le raccomandazioni del Centro sicurezza per il computer host indicato in ogni avviso. Questa operazione ridurrà i rischi di attacchi futuri. 

    [Altre informazioni sulla gestione e la risposta agli avvisi](security-center-managing-and-responding-alerts.md).


## <a name="next-steps"></a>Passaggi successivi

Per materiale correlato, vedere l'articolo seguente:

- [Avvisi di sicurezza per il database SQL e Azure sinapsi Analytics (in precedenza SQL Data Warehouse)](alerts-reference.md#alerts-sql-db-and-warehouse)
- [Configurare le notifiche tramite posta elettronica per gli avvisi di sicurezza](security-center-provide-security-contact-details.md)
- [Scopri di più su Azure Sentinel](../sentinel/index.yml)
- [Pacchetto di sicurezza dei dati del Centro sicurezza di Azure](../azure-sql/database/azure-defender-for-sql.md)