---
title: Gestisci aree di lavoro di Sentinel di Azure su larga scala
description: Informazioni su come gestire efficacemente Azure Sentinel sulle risorse dei clienti Delegate.
ms.date: 08/27/2020
ms.topic: how-to
ms.openlocfilehash: 328c55afc141a7f2efd85104453342b62eae0bb2
ms.sourcegitcommit: 8a7b82de18d8cba5c2cec078bc921da783a4710e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/28/2020
ms.locfileid: "89050812"
---
# <a name="manage-azure-sentinel-workspaces-at-scale"></a>Gestisci aree di lavoro di Sentinel di Azure su larga scala

Come provider di servizi, è possibile che siano stati caricati più tenant del cliente nel [Faro di Azure](../overview.md). Azure Lighthouse consente ai provider di servizi di eseguire operazioni su larga scala tra più tenant Azure Active Directory (Azure AD) contemporaneamente, rendendo più efficienti le attività di gestione.

Azure Sentinel fornisce analisi della sicurezza e Intelligence per le minacce, offrendo un'unica soluzione per il rilevamento degli avvisi, la visibilità delle minacce, la ricerca proattiva e la risposta alle minacce. Con Azure Lighthouse è possibile gestire più aree di lavoro di Azure Sentinel tra i tenant su larga scala. Questo consente scenari come l'esecuzione di query su più aree di lavoro o la creazione di cartelle di lavoro per visualizzare e monitorare i dati dalle origini dati connesse per ottenere informazioni dettagliate. Gli IP, ad esempio query e PlayBook, rimangono nel tenant di gestione, ma possono essere usati per eseguire la gestione della sicurezza nei tenant dei clienti.

Questo argomento fornisce una panoramica dell'uso di [Sentinel di Azure](../../sentinel/overview.md) in modo scalabile per la visibilità tra tenant e i servizi di sicurezza gestiti.

> [!TIP]
> Per quanto riguarda i provider di servizi e i clienti in questo argomento, queste indicazioni si applicano anche alle [aziende che usano Azure Lighthouse per gestire più tenant](../concepts/enterprise.md).

## <a name="architectural-considerations"></a>Considerazioni sull'architettura

Per un provider di servizi di sicurezza gestito (MSSP) che vuole creare un'offerta di sicurezza distribuita come servizio con Sentinel di Azure, è possibile che sia necessario un solo Security Operations Center (SOC) per monitorare, gestire e configurare in modo centralizzato più aree di lavoro di Sentinel di Azure distribuite nei singoli tenant dei clienti. Analogamente, le aziende con più tenant Azure AD possono avere la facoltà di gestire centralmente più aree di lavoro di Azure Sentinel distribuite tra i tenant.

Questo modello di distribuzione centralizzato presenta i vantaggi seguenti:

- La proprietà dei dati rimane associata a ogni tenant gestito.
- Supporta i requisiti per archiviare i dati entro i confini geografici.
- Garantisce l'isolamento dei dati, poiché i dati per più clienti non vengono archiviati nella stessa area di lavoro.
- Impedisce i dati exfiltration dai tenant gestiti, contribuendo a garantire la conformità dei dati.
- I costi correlati vengono addebitati a ogni tenant gestito, anziché al tenant di gestione.
- I dati di tutte le origini dati e i connettori dati integrati con Sentinel di Azure, ad esempio Azure AD log attività, i log di Office 365 o gli avvisi di Microsoft Threat Protection, rimarranno all'interno di ogni tenant del cliente.
- Riduce la latenza di rete.
- È facile aggiungere o rimuovere nuove filiali o clienti.

## <a name="granular-role-based-access-control-rbac"></a>Controllo degli accessi in base al ruolo granulare

Ogni sottoscrizione cliente che verrà gestita da un MSSP deve essere caricata [in Azure Lighthouse](onboard-customer.md). In questo modo gli utenti designati nel tenant di gestione possono accedere ed eseguire operazioni di gestione nelle aree di lavoro di Azure Sentinel distribuite in tenant del cliente.

Quando si creano le autorizzazioni, è possibile assegnare i ruoli predefiniti di Sentinel di Azure a utenti, gruppi o entità servizio nel tenant di gestione:

- [Lettore sentinella di Azure](../../role-based-access-control/built-in-roles.md#azure-sentinel-reader)
- [Risponditore sentinella di Azure](../../role-based-access-control/built-in-roles.md#azure-sentinel-responder)
- [Collaboratore sentinella di Azure](../../role-based-access-control/built-in-roles.md#azure-sentinel-contributor)

È anche possibile assegnare ruoli predefiniti aggiuntivi per eseguire altre funzioni. Per informazioni sui ruoli specifici che possono essere usati con Azure Sentinel, vedere [autorizzazioni in Sentinel di Azure](../../sentinel/roles.md).

Dopo aver caricato i clienti, gli utenti designati possono accedere al tenant di gestione e [accedere direttamente all'area di lavoro di Azure Sentinel del cliente](../../sentinel/multiple-tenants-service-providers.md) con i ruoli assegnati.

## <a name="view-and-manage-incidents-across-workspaces"></a>Visualizzare e gestire gli eventi imprevisti tra le aree di lavoro

Se si gestiscono le risorse di Azure Sentinel per più clienti, è possibile visualizzare e gestire gli eventi imprevisti in più aree di lavoro in più tenant contemporaneamente. Per altre informazioni, vedere [gestire gli eventi imprevisti in molte aree](../../sentinel/multiple-workspace-view.md) di lavoro contemporaneamente ed [estendere Sentinel di Azure tra le aree](../../sentinel/extend-sentinel-across-workspaces-tenants.md)di lavoro e i tenant.

> [!NOTE]
> Assicurarsi che agli utenti del tenant di gestione siano state assegnate le autorizzazioni di lettura e scrittura per tutte le aree di lavoro gestite. Se un utente dispone solo delle autorizzazioni di lettura per alcune aree di lavoro, è possibile che vengano visualizzati messaggi di avviso quando si selezionano gli eventi imprevisti in tali aree di lavoro e l'utente non sarà in grado di modificare tali eventi o qualsiasi altro utente selezionato con questi (anche se si dispone delle autorizzazioni per gli altri).

## <a name="configure-playbooks-for-mitigation"></a>Configurare PlayBook per la mitigazione

I [PlayBook](../../sentinel/tutorial-respond-threats-playbook.md) possono essere usati per la mitigazione automatica quando viene attivato un avviso. Questi PlayBook possono essere eseguiti manualmente oppure possono essere eseguiti automaticamente quando vengono attivati avvisi specifici. I PlayBook possono essere distribuiti nel tenant di gestione o nel tenant del cliente, con le procedure di risposta configurate in base agli utenti del tenant che dovranno agire in risposta a una minaccia per la sicurezza.

## <a name="create-cross-tenant-workbooks"></a>Creare cartelle di lavoro tra tenant

Le [cartelle di lavoro di monitoraggio di Azure in Sentinel di Azure](../../sentinel/overview.md#workbooks) consentono di visualizzare e monitorare i dati dalle origini dati connesse per ottenere informazioni dettagliate. È possibile usare i modelli di cartella di lavoro predefiniti in Sentinel di Azure o creare cartelle di lavoro personalizzate per gli scenari.

È possibile distribuire cartelle di lavoro nel tenant di gestione e creare dashboard su larga scala per monitorare ed eseguire query sui dati tra i tenant del cliente. Per altre informazioni, vedere [monitoraggio tra aree di lavoro](../../sentinel/extend-sentinel-across-workspaces-tenants.md#using-cross-workspace-workbooks). Si noti che alcune funzionalità [non sono supportate in più aree di lavoro](../../sentinel/extend-sentinel-across-workspaces-tenants.md#whats-not-supported-across-workspaces).

È anche possibile distribuire le cartelle di lavoro direttamente in un singolo tenant gestito per gli scenari specifici del cliente.

## <a name="run-log-analytics-and-hunting-queries-across-azure-sentinel-workspaces"></a>Eseguire Log Analytics e cacciare query tra aree di lavoro di Azure Sentinel

Creare e salvare Log Analytics query per il rilevamento delle minacce in modo centralizzato nel tenant di gestione, incluse le [query di caccia](../../sentinel/extend-sentinel-across-workspaces-tenants.md#cross-workspace-hunting). Queste query possono quindi essere eseguite in tutte le aree di lavoro di Azure Sentinel dei clienti usando l'operatore Union e l'espressione Workspace (). Per altre informazioni, vedere [esecuzione di query tra aree di lavoro](../../sentinel/extend-sentinel-across-workspaces-tenants.md#cross-workspace-querying).

## <a name="use-automation-for-cross-workspace-management"></a>Usare l'automazione per la gestione tra aree di lavoro

È possibile usare l'automazione per gestire più aree di lavoro di Sentinel di Azure e configurare le [query di ricerca](../../sentinel/hunting.md), i PlayBook e le cartelle di lavoro. Per altre informazioni, vedere [gestione tra aree di lavoro tramite automazione](../../sentinel/extend-sentinel-across-workspaces-tenants.md#cross-workspace-management-using-automation).

Si noti che alcune funzionalità [non sono attualmente supportate in più aree di lavoro](../../sentinel/extend-sentinel-across-workspaces-tenants.md#whats-not-supported-across-workspaces).

## <a name="manage-security-of-office-365-environments"></a>Gestire la sicurezza degli ambienti Office 365

Usare Azure Lighthouse insieme a Sentinel di Azure per gestire la sicurezza degli ambienti di Office 365 tra i tenant. Per prima cosa, i [connettori dati di Office 365 predefiniti devono essere abilitati nel tenant gestito, in](../../sentinel/connect-office-365.md) modo che le informazioni sulle attività di utenti e amministratori in Exchange e SharePoint (incluso OneDrive) possano essere inserite in un'area di lavoro di Sentinel di Azure all'interno del tenant gestito. Sono inclusi i dettagli sulle azioni, ad esempio i download di file, le richieste di accesso inviate, le modifiche agli eventi del gruppo e le operazioni sulle cassette postali, oltre a informazioni sugli utenti che hanno eseguito le azioni. Gli [avvisi DLP di office 365](https://techcommunity.microsoft.com/t5/azure-sentinel/ingest-office-365-dlp-events-into-azure-sentinel/ba-p/1031820) sono supportati anche come parte del connettore Office 365 incorporato.

È possibile abilitare il [connettore Microsoft cloud app Security (MCAS)](../../sentinel/connect-cloud-app-security.md) per trasmettere avvisi e cloud Discovery log in Sentinel di Azure. Questo ti permette di ottenere visibilità sulle app Cloud, ottenere analisi sofisticate per identificare e combattere Cyberthreats e controllare il modo in cui i dati vengono trasmessi. I log attività per MCAS possono essere [utilizzati usando il formato CEF (Common Event Format)](https://techcommunity.microsoft.com/t5/azure-sentinel/ingest-box-com-activity-events-via-microsoft-cloud-app-security/ba-p/1072849).

Dopo aver configurato i connettori dati di Office 365, è possibile usare le funzionalità di Sentinel di Azure tra tenant, ad esempio la visualizzazione e l'analisi dei dati nelle cartelle di lavoro, l'uso di query per creare avvisi personalizzati e la configurazione di PlayBook per rispondere alle minacce.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su [Sentinel di Azure](../../sentinel/overview.md).
- Vedere la [pagina dei prezzi di Sentinel per Azure](https://azure.microsoft.com/pricing/details/azure-sentinel/).
- Informazioni sulle [esperienze di gestione tra tenant](../concepts/cross-tenant-management-experience.md).

