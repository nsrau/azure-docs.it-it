---
title: Sicurezza avanzata dei dati del Centro sicurezza di Azure per computer SQL (anteprima)
description: Informazioni su come abilitare la sicurezza dei dati avanzata per i computer SQL nel centro sicurezza di Azure
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: ba46c460-6ba7-48b2-a6a7-ec802dd4eec2
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/28/2020
ms.author: memildin
ms.openlocfilehash: f159d2cdc48b144d0c75c62cd8a7ba6667424243
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/07/2020
ms.locfileid: "86043870"
---
# <a name="advanced-data-security-for-sql-machines-preview"></a>Sicurezza avanzata dei dati per i computer SQL (anteprima)

La protezione avanzata dei dati del Centro sicurezza di Azure per i computer SQL protegge i server SQL ospitati in Azure, in altri ambienti cloud e anche in computer locali. In questo modo si estendono le protezioni per i server SQL nativi di Azure per supportare completamente gli ambienti ibridi.

Questa funzionalità di anteprima include funzionalità per l'identificazione e la mitigazione di potenziali vulnerabilità del database e per il rilevamento di attività anomale che potrebbero indicare minacce per il database: 

* **Valutazione della vulnerabilità** : il servizio di analisi per individuare, monitorare e aiutare a correggere le potenziali vulnerabilità del database. Le analisi di valutazione forniscono una panoramica dello stato di sicurezza dei computer SQL e i dettagli dei risultati di sicurezza.

* [Advanced Threat Protection](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview) : servizio di rilevamento che monitora costantemente i server SQL per individuare minacce come attacchi SQL injection, attacchi di forza bruta e abuso dei privilegi. Questo servizio fornisce avvisi di sicurezza orientati alle azioni nel centro sicurezza di Azure con i dettagli dell'attività sospetta, le linee guida su come attenuare le minacce e le opzioni per proseguire le indagini con Azure Sentinel.

>[!TIP]
> Sicurezza avanzata dei dati per i computer SQL è un'estensione del pacchetto di [sicurezza avanzata dei dati](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security)del Centro sicurezza di Azure, disponibile per database SQL di Azure, sinapsi di Azure e SQL istanza gestita.


## <a name="set-up-advanced-data-security-for-sql-machines"></a>Configurare la sicurezza dei dati avanzata per i computer SQL 

La configurazione della sicurezza avanzata dei dati del Centro sicurezza di Azure per i computer SQL prevede due passaggi:

* Eseguire il provisioning dell'agente di Log Analytics nell'host di SQL Server. In questo modo viene fornita la connessione ad Azure.

* Abilitare il bundle facoltativo nella pagina dei prezzi e delle impostazioni del Centro sicurezza.

Entrambi sono descritti di seguito.

### <a name="step-1-provision-the-log-analytics-agent-on-your-sql-servers-host"></a>Passaggio 1. Eseguire il provisioning dell'agente di Log Analytics nell'host di SQL Server:

- **SQL Server nella VM di Azure** : se il computer SQL è ospitato in una macchina virtuale di Azure, è possibile eseguire [il provisioning automatico dell'agente di log Analytics](security-center-enable-data-collection.md#workspace-configuration). In alternativa, è possibile seguire la procedura manuale per l' [aggiunta di una macchina virtuale di Azure](quick-onboard-azure-stack.md#add-the-virtual-machine-extension-to-your-existing-azure-stack-virtual-machines).

- **SQL Server in Azure Arc** : se la SQL Server è ospitata in una macchina di [Azure Arc](https://docs.microsoft.com/azure/azure-arc/) , è possibile distribuire l'agente di log Analytics usando la raccomandazione "log Analytics Agent deve essere installato nei computer Azure Arc basati su Windows (anteprima)". In alternativa, è possibile seguire la procedura manuale nella [documentazione di Azure Arc](https://docs.microsoft.com/azure/azure-arc/servers/manage-vm-extensions#enable-extensions-from-the-portal).

- **SQL Server** locale: se la SQL Server è ospitata in un computer Windows locale senza Azure Arc, sono disponibili due opzioni per la connessione ad Azure:
    
    - **Distribuire Azure Arc** : è possibile connettere qualsiasi computer Windows al centro sicurezza. Azure Arc fornisce tuttavia una maggiore integrazione in *tutti* gli ambienti di Azure. Se si configura Azure Arc, viene visualizzata la pagina **SQL Server-Azure Arc** nel portale e gli avvisi di sicurezza verranno visualizzati in una scheda **sicurezza** dedicata in tale pagina. Quindi, la prima opzione consigliata consiste nell' [impostare Azure Arc nell'host](https://docs.microsoft.com/azure/azure-arc/servers/onboard-portal#install-and-validate-the-agent-on-windows) e seguire le istruzioni per **SQL Server in Azure Arc**, sopra.
        
    - **Connettere il computer Windows senza Azure Arc** : se si sceglie di connettere un SQL Server in esecuzione in un computer Windows senza usare Azure Arc, seguire le istruzioni riportate in [connettere computer Windows a monitoraggio di Azure](https://docs.microsoft.com/azure/azure-monitor/platform/agent-windows).


### <a name="step-2-enable-the-optional-bundle-in-security-centers-pricing-and-settings-page"></a>Passaggio 2: Abilitare il bundle facoltativo nella pagina dei prezzi e delle impostazioni del Centro sicurezza:

1. Dalla barra laterale del Centro sicurezza, aprire la pagina **impostazioni & prezzi** .

    - Se si usa l' **area di lavoro predefinita del Centro sicurezza di Azure** , denominata "defaultWorkspace-[ID sottoscrizione]-[area]", selezionare la **sottoscrizione**pertinente.

    - Se si usa **un'area di lavoro non predefinita**, selezionare l' **area di lavoro** pertinente (immettere il nome dell'area di lavoro nel filtro se necessario):

        ![title](./media/security-center-advanced-iaas-data/pricing-and-settings-workspaces.png)


1. Attivare o disabilitare l'opzione per **SQL Server nei computer (anteprima)** . 

    [![Pagina dei prezzi del Centro sicurezza con Bundle facoltativi](media/security-center-advanced-iaas-data/sql-servers-on-vms-in-pricing-small.png)](media/security-center-advanced-iaas-data/sql-servers-on-vms-in-pricing-large.png#lightbox)

    La protezione avanzata dei dati per SQL Server nei computer verrà abilitata in tutti i server SQL connessi all'area di lavoro selezionata. La protezione sarà completamente attiva dopo il primo riavvio del SQL Server. 

    >[!TIP] 
    > Per creare una nuova area di lavoro, seguire le istruzioni riportate in [creare un'area di lavoro log Analytics](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace).


1. Facoltativamente, configurare la notifica tramite posta elettronica per gli avvisi di sicurezza. 
    È possibile impostare un elenco di destinatari per ricevere una notifica tramite posta elettronica quando vengono generati gli avvisi del Centro sicurezza. Il messaggio di posta elettronica contiene un collegamento diretto all'avviso nel centro sicurezza di Azure con tutti i dettagli pertinenti. Per altre informazioni, vedere [configurare le notifiche di posta elettronica per gli avvisi di sicurezza](https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details).



## <a name="explore-vulnerability-assessment-reports"></a>Esplorare i report di valutazione della vulnerabilità

Il servizio di valutazione della vulnerabilità esegue l'analisi dei database una volta alla settimana. Le analisi vengono eseguite nello stesso giorno della settimana in cui è stato abilitato il servizio.

Il dashboard della valutazione della vulnerabilità fornisce una panoramica dei risultati della valutazione in tutti i database, insieme a un riepilogo di database integri e non integri, e un riepilogo generale dei controlli con esito negativo in base alla distribuzione dei rischi.

È possibile visualizzare i risultati della valutazione della vulnerabilità direttamente dal centro sicurezza.

1. Dall'intestazione laterale del Centro sicurezza, aprire la pagina **raccomandazioni** e selezionare le **vulnerabilità di raccomandazione nei server SQL sui computer da correggere (anteprima)**. Per altre informazioni, vedere [raccomandazioni del Centro sicurezza](security-center-recommendations.md). 


    [![* * Le vulnerabilità nei computer SQL Server devono essere risolte (anteprima) * * Raccomandazione](media/security-center-advanced-iaas-data/data-and-storage-sqldb-vulns-on-vm.png)](media/security-center-advanced-iaas-data/data-and-storage-sqldb-vulns-on-vm.png#lightbox)

    Viene visualizzata la visualizzazione dettagliata di questa raccomandazione.

    [![Visualizzazione dettagliata delle * * vulnerabilità nei server SQL sui computer da correggere (anteprima) * * Raccomandazione](media/security-center-advanced-iaas-data/all-servers-view.png)](media/security-center-advanced-iaas-data/all-servers-view.png#lightbox)

1. Per altri dettagli, eseguire il drill-down:

    * Per una panoramica delle risorse analizzate (database) e l'elenco dei controlli di sicurezza testati, selezionare il server di interesse.

    * Per una panoramica delle vulnerabilità raggruppate in base a un database SQL specifico, selezionare il database di interesse.

    In ogni visualizzazione, i controlli di sicurezza sono ordinati in base alla **gravità**. Fare clic su un controllo di sicurezza specifico per visualizzare un riquadro dei dettagli con una **Descrizione**, **come risolverlo e altre** informazioni correlate, ad esempio **Impact** o **benchmark**.

## <a name="advanced-threat-protection-for-sql-servers-on-machines-alerts"></a>Avvisi di Advanced Threat Protection per SQL Server in computer
Gli avvisi vengono generati da tentativi insoliti e potenzialmente dannosi di accedere o sfruttare le macchine virtuali SQL. Questi eventi possono attivare gli avvisi mostrati nella [sezione avvisi per database SQL e SQL data warehouse della pagina di riferimento per gli avvisi](alerts-reference.md#alerts-sql-db-and-warehouse).



## <a name="explore-and-investigate-security-alerts"></a>Esplorare ed esaminare gli avvisi di sicurezza

Gli avvisi di sicurezza sono disponibili nella pagina degli avvisi del Centro sicurezza, nella scheda sicurezza della risorsa o tramite il collegamento diretto nei messaggi di posta elettronica di avviso.

1. Per visualizzare gli avvisi, selezionare **avvisi di sicurezza** dall'intestazione laterale del Centro sicurezza e selezionare un avviso.

1. Gli avvisi sono progettati per essere indipendenti, con procedure dettagliate per la correzione e informazioni di analisi in ognuna di esse. È possibile approfondire ulteriormente l'uso di altre funzionalità del Centro sicurezza di Azure e di Azure Sentinel per una visualizzazione più ampia:

    * Abilitare la funzionalità di controllo SQL Server per ulteriori indagini. Se si è un utente di Sentinel di Azure, è possibile caricare i log di controllo SQL dagli eventi del registro di sicurezza di Windows in Sentinel e usufruire di un'esperienza di analisi avanzata. [Altre informazioni sul controllo SQL Server](https://docs.microsoft.com/sql/relational-databases/security/auditing/create-a-server-audit-and-server-audit-specification?view=sql-server-ver15).
    * Per migliorare il comportamento di sicurezza, usare le raccomandazioni del Centro sicurezza per il computer host indicato in ogni avviso. Questa operazione ridurrà i rischi di attacchi futuri. 

    [Altre informazioni sulla gestione e la risposta agli avvisi](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts).


## <a name="next-steps"></a>Passaggi successivi

Per materiale correlato, vedere l'articolo seguente:

- [Avvisi di sicurezza per il database SQL e SQL Data Warehouse](alerts-reference.md#alerts-sql-db-and-warehouse)
- [Configurare le notifiche di posta elettronica per gli avvisi di sicurezza](security-center-provide-security-contact-details.md)
- [Scopri di più su Azure Sentinel](https://docs.microsoft.com/azure/sentinel/)
- [Pacchetto di sicurezza avanzata dei dati del Centro sicurezza di Azure](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security)