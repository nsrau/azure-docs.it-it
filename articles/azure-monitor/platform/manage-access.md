---
title: Gestire le aree di lavoro di Log Analytics in Monitoraggio di Azure | Microsoft Docs
description: È possibile gestire le aree di lavoro di Log Analytics in Monitoraggio di Azure usando svariate attività amministrative per utenti, account, aree di lavoro e account di Azure.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: d0e5162d-584b-428c-8e8b-4dcaa746e783
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: magoedte
ms.openlocfilehash: 4a777c2bd57d40b4bb6c8d36c996b655cb019e5f
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/11/2019
ms.locfileid: "56005370"
---
# <a name="manage-log-analytics-workspaces-in-azure-monitor"></a>Gestire le aree di lavoro di Log Analytics in Monitoraggio di Azure
Monitoraggio di Azure archivia i dati di log in un'area di lavoro di Log Analytics, che in pratica è un contenitore che include dati e informazioni di configurazione. Per gestire l'accesso ai dati di log, vengono eseguite diverse attività amministrative relative alle aree di lavoro. Nell'organizzazione è possibile usare più aree di lavoro per gestire diversi set di dati raccolti dall'intera infrastruttura IT o da una parte di essa.

Per creare un'area di lavoro, è necessario:

1. Disporre di una sottoscrizione di Azure
2. Scegliere un nome per l'area di lavoro
3. Associare l'area di lavoro con uno degli abbonamenti e dei gruppi di risorse.
4. Scegliere una località geografica

## <a name="determine-the-number-of-workspaces-you-need"></a>Determinare il numero di aree di lavoro necessarie
Un'area di lavoro di Log Analytics è una risorsa di Azure e rappresenta un contenitore in cui i dati vengono raccolti, analizzati e presentati in Monitoraggio di Azure.

È possibile disporre di più aree di lavoro per ogni sottoscrizione di Azure e avere accesso a più di un'area di lavoro, con la possibilità di eseguire facilmente query su di esse. Questa sezione descrive quando può essere utile creare più aree di lavoro.

Un'area di lavoro di Log Analytics offre:

* Una posizione geografica per archiviare i dati
* Isolamento dei dati per definire diritti di accesso utente diversi
* Ambito per la configurazione di impostazioni come [piano tariffario](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#changing-pricing-tier), [conservazione](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period) e [limitazione dei dati](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#daily-cap) 

Dal punto di vista del consumo, è consigliabile creare meno aree di lavoro possibile. In questo modo le operazioni di amministrazione e di esecuzione di query sono più semplici e più rapide. Tuttavia, in base alle caratteristiche precedenti, si possono creare più aree di lavoro se:

* Si opera in un'azienda globale ed è necessario che i dati di log siano archiviati in aree specifiche per motivi di sovranità o conformità dei dati.
* Si usa Azure e si intendono evitare costi di trasferimento dei dati in uscita tramite un'area di lavoro nella stessa area delle risorse di Azure da essa gestite.
* Si intende allocare le spese a reparti o gruppi aziendali diversi in base all'uso tramite la creazione di un'area di lavoro per ogni reparto o gruppo aziendale nella relativa sottoscrizione di Azure.
* Si opera come provider di servizi gestiti e per ogni cliente gestito è necessario mantenere i dati di Log Analytics isolati da altri dati del cliente.
* Si gestiscono più clienti e si vuole che ogni cliente, reparto o gruppo aziendale visualizzi i propri dati, ma non quelli di altri.

Quando si usano agenti Windows per la raccolta dei dati, è possibile [configurare ogni agente in modo che faccia riferimento a una o più aree di lavoro](../../azure-monitor/platform/agent-windows.md).

Se si usa System Center Operations Manager, ogni gruppo di gestione di Operations Manager può essere connesso con una sola area di lavoro. È possibile installare Microsoft Monitoring Agent nei computer gestiti da Operations Manager e fare sì che l’agente faccia riferimento sia a Operations Manager che a un'altra area di lavoro di Log Analytics.

## <a name="workspace-information"></a>Informazioni sull'area di lavoro
Quando si analizzano i dati nell'area di lavoro di Log Analytics nel menu **Monitoraggio di Azure** nel portale di Azure, si creano e gestiscono aree di lavoro nel menu **Aree di lavoro di Log Analytics**.
 

1. Accedere al [portale di Azure](https://portal.azure.com) e fare clic su **Tutti i servizi**. Nell'elenco delle risorse digitare **Log Analytics**. Non appena si inizia a digitare, l'elenco viene filtrato in base all'input. Selezionare **Aree di lavoro di Log Analytics**.  

    ![Portale di Azure](media/manage-access/azure-portal-01.png)  

3. Selezionare l'area di lavoro dall'elenco.

4. In tale pagina vengono visualizzati i dettagli su come iniziare, sulla configurazione e sui collegamenti per ottenere altre informazioni.  

    ![Dettagli dell'area di lavoro](./media/manage-access/workspace-overview-page.png)  

## <a name="manage-accounts-and-users"></a>Gestire utenti e account
A ogni area di lavoro possono essere associati più account, ognuno dei quali può avere accesso a diverse aree di lavoro. L'accesso viene gestito tramite [accesso basato sui ruoli Azure](../../role-based-access-control/role-assignments-portal.md). Questi diritti di accesso si applicano sia nel portale di Azure sia sull'accesso all'API.


Le attività seguenti richiedono anche le autorizzazioni di Azure:

| Azione                                                          | Autorizzazioni di Azure necessarie | Note |
|-----------------------------------------------------------------|--------------------------|-------|
| Aggiunta e rimozione di soluzioni di gestione                        | `Microsoft.Resources/deployments/*` <br> `Microsoft.OperationalInsights/*` <br> `Microsoft.OperationsManagement/*` <br> `Microsoft.Automation/*` <br> `Microsoft.Resources/deployments/*/write` | Queste autorizzazioni devono essere concesse a livello di gruppo di risorse o di sottoscrizione. |
| Modifica del piano tariffario                                       | `Microsoft.OperationalInsights/workspaces/*/write` | |
| Visualizzazione dei dati nei riquadri delle soluzioni *Backup* e *Site Recovery* | Amministratore/Coamministratore | Risorse di accesso distribuite usando il modello di distribuzione classica |
| Creare un'area di lavoro nel portale di Azure                        | `Microsoft.Resources/deployments/*` <br> `Microsoft.OperationalInsights/workspaces/*` ||


### <a name="managing-access-to-log-analytics-workspace-using-azure-permissions"></a>Gestione dell'accesso all'area di lavoro di Log Analytics con le autorizzazioni di Azure
Per concedere l'accesso all'area di lavoro di Log Analytics usando le autorizzazioni di Azure, seguire i passaggi indicati in [Usare le assegnazioni di ruolo per gestire l'accesso alle risorse della sottoscrizione di Azure](../../role-based-access-control/role-assignments-portal.md).

Azure offre due ruoli utente predefiniti per le aree di lavoro di Log Analytics:
- Lettore di Log Analytics
- Collaboratore di Log Analytics

I membri del ruolo *Lettore di Log Analytics* possono eseguire queste operazioni:
- Visualizzare e cercare tutti i dati di monitoraggio 
- Visualizzare le impostazioni di monitoraggio, inclusa la configurazione della diagnostica di Azure in tutte le risorse di Azure.

Il ruolo di lettore di Log Analytics include le azioni di Azure seguenti:

| Type    | Autorizzazione | DESCRIZIONE |
| ------- | ---------- | ----------- |
| Azione | `*/read`   | Consente di visualizzare tutte le risorse di Azure e la configurazione delle risorse. Include la visualizzazione di: <br> Stato dell'estensione macchina virtuale <br> Configurazione della diagnostica di Azure nelle risorse <br> Tutte le proprietà e le impostazioni di tutte le risorse |
| Azione | `Microsoft.OperationalInsights/workspaces/analytics/query/action` | Consente di eseguire query di ricerca log versione 2 |
| Azione | `Microsoft.OperationalInsights/workspaces/search/action` | Consente di eseguire query di ricerca log versione 1 |
| Azione | `Microsoft.Support/*` | Consente di aprire casi di supporto |
|Non azione | `Microsoft.OperationalInsights/workspaces/sharedKeys/read` | Impedisce la lettura della chiave dell'area di lavoro, necessaria per l'uso dell'API di raccolta dati e per l'installazione degli agenti. Ciò impedisce all'utente di aggiungere nuove risorse all'area di lavoro |


I membri del ruolo *Collaboratore di Log Analytics* possono eseguire queste operazioni:
- Leggere tutti i dati di monitoraggio come Lettore di Log Analytics può  
- Creare e configurare account di automazione  
- Aggiunta e rimozione di soluzioni di gestione    
    > [!NOTE] 
    > Per eseguire correttamente le ultime due azioni, questa autorizzazione deve essere concessa a livello di gruppo di risorse o di abbonamento.  

- Leggere le chiavi degli account di archiviazione   
- Configurare la raccolta di log da Archiviazione di Azure  
- Modificare le impostazioni di monitoraggio per le risorse di Azure, tra cui
  - Aggiunta dell'estensione macchina virtuale alle VM
  - Configurazione della diagnostica di Azure in tutte le risorse di Azure

> [!NOTE] 
> È possibile usare la possibilità di aggiungere un'estensione macchina virtuale a una VM per ottenere il controllo completo su di essa.

Il ruolo di collaboratore di Log Analytics include le azioni di Azure seguenti:

| Autorizzazione | DESCRIZIONE |
| ---------- | ----------- |
| `*/read`     | Consente di visualizzare tutte le risorse e le configurazioni delle risorse. Include la visualizzazione di: <br> Stato dell'estensione macchina virtuale <br> Configurazione della diagnostica di Azure nelle risorse <br> Tutte le proprietà e le impostazioni di tutte le risorse |
| `Microsoft.Automation/automationAccounts/*` | Consente di creare e configurare account di automazione di Azure, inclusa l'aggiunta e modifica di runbook |
| `Microsoft.ClassicCompute/virtualMachines/extensions/*` <br> `Microsoft.Compute/virtualMachines/extensions/*` | Consente di aggiungere, aggiornare e rimuovere estensioni macchina virtuale, inclusa l'estensione Microsoft Monitoring Agent e l'estensione agente OMS per Linux |
| `Microsoft.ClassicStorage/storageAccounts/listKeys/action` <br> `Microsoft.Storage/storageAccounts/listKeys/action` | Consente di visualizzare la chiave dell'account di archiviazione, è necessaria per configurare Log Analytics per la lettura dei log dagli account di archiviazione di Azure |
| `Microsoft.Insights/alertRules/*` | Consente di aggiungere, aggiornare e rimuovere regole di avviso |
| `Microsoft.Insights/diagnosticSettings/*` | Consente di aggiungere, aggiornare e rimuovere impostazioni di diagnostica dalle risorse di Azure |
| `Microsoft.OperationalInsights/*` | Consente di aggiungere, aggiornare e rimuovere configurazioni per le aree di lavoro di Log Analytics |
| `Microsoft.OperationsManagement/*` | Consente di aggiungere e rimuovere soluzioni di gestione |
| `Microsoft.Resources/deployments/*` | Consente di creare ed eliminare distribuzioni, è necessaria per aggiungere e rimuovere soluzioni, aree di lavoro e account di automazione |
| `Microsoft.Resources/subscriptions/resourcegroups/deployments/*` | Consente di creare ed eliminare distribuzioni, è necessaria per aggiungere e rimuovere soluzioni, aree di lavoro e account di automazione |

Per aggiungere e rimuovere utenti da un ruolo utente, è necessario avere le autorizzazioni `Microsoft.Authorization/*/Delete` e `Microsoft.Authorization/*/Write`.

Usare questi ruoli per concedere agli utenti l'accesso ad ambiti diversi:
- Sottoscrizione: accesso a tutte le aree di lavoro nella sottoscrizione
- Gruppo di risorse: accesso a tutte le aree di lavoro nel gruppo di risorse
- Risorsa: accesso alla sola area di lavoro specificata

È consigliabile eseguire le assegnazioni solo a livello di risorsa (area di lavoro) per assicurare un controllo di accesso accurato.  Usare i [ruoli personalizzati](../../role-based-access-control/custom-roles.md) per creare ruoli con le autorizzazioni specifiche necessarie.

## <a name="next-steps"></a>Passaggi successivi
* Vedere [Log Analytics agent overview](../../azure-monitor/platform/log-analytics-agent.md) (Panoramica dell'agente di Log Analytics) per raccogliere dati dai computer nel data center o in un altro ambiente cloud.
* Vedere [Raccogliere dati sulle macchine virtuali di Azure](../../azure-monitor/learn/quick-collect-azurevm.md) per configurare la raccolta di dati dalle macchine virtuali di Azure.  

