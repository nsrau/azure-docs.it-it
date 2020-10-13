---
title: Esperienze di gestione tra tenant
description: La gestione risorse delegate di Azure consente un'esperienza di gestione tra tenant.
ms.date: 10/12/2020
ms.topic: conceptual
ms.openlocfilehash: 7b2476d58cdfe057a94c52b40af7694abc7b263f
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/13/2020
ms.locfileid: "91970640"
---
# <a name="cross-tenant-management-experiences"></a>Esperienze di gestione tra tenant

Come provider di servizi, è possibile usare il [Faro di Azure](../overview.md) per gestire le risorse per più clienti dall'interno del proprio tenant di Azure Active Directory (Azure ad). Molte attività e servizi possono essere eseguiti in tenant gestiti usando la [gestione delle risorse delegata di Azure](../concepts/azure-delegated-resource-management.md).

> [!TIP]
> La gestione risorse delegate di Azure può essere usata anche [all'interno di un'azienda con più tenant Azure AD propri](enterprise.md) per semplificare l'amministrazione tra tenant.

## <a name="understanding-tenants-and-delegation"></a>Informazioni su tenant e delega

Un tenant Azure AD è una rappresentazione di un'organizzazione. Si tratta di un'istanza dedicata di Azure AD che un'organizzazione riceve quando crea una relazione con Microsoft iscrivendosi ad Azure, Microsoft 365 o ad altri servizi. Ogni tenant di Azure AD è distinto e separato dagli altri tenant di Azure AD e ha un proprio ID tenant (un GUID). Per altre informazioni, vedere [Informazioni su Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md).

In genere, per gestire le risorse di Azure per un cliente, i provider di servizi devono accedere al portale di Azure usando un account associato al tenant del cliente, richiedendo a un amministratore del tenant del cliente di creare e gestire gli account utente per il provider di servizi.

Con Azure Lighthouse, il processo di onboarding specifica gli utenti all'interno del tenant del provider di servizi che saranno in grado di lavorare su sottoscrizioni delegate e gruppi di risorse nel tenant del cliente. Questi utenti possono quindi accedere al portale di Azure usando le proprie credenziali. All'interno del portale di Azure possono gestire le risorse appartenenti a tutti i clienti a cui hanno accesso. Per eseguire questa operazione, visitare la pagina [Clienti personali](../how-to/view-manage-customers.md) nel portale di Azure o accedere direttamente al contesto della sottoscrizione di tale cliente nel portale di Azure o tramite API.

Azure Lighthouse consente una maggiore flessibilità nella gestione delle risorse per più clienti senza dover accedere a diversi account in tenant diversi. Un provider di servizi, ad esempio, può avere due clienti, con responsabilità e livelli di accesso diversi. Usando Azure Lighthouse, gli utenti autorizzati possono accedere al tenant del provider di servizi per accedere a tali risorse.

![Diagramma che mostra le risorse dei clienti gestite tramite un tenant del provider di servizi.](../media/azure-delegated-resource-management-service-provider-tenant.jpg)

## <a name="apis-and-management-tool-support"></a>Supporto delle API e degli strumenti di gestione

È possibile eseguire le attività di gestione nelle risorse delegate direttamente nel portale oppure usando le API e gli strumenti di gestione, ad esempio l'interfaccia della riga di comando di Azure e Azure PowerShell. Quando si usano le risorse delegate, è possibile utilizzare tutte le API esistenti, purché la funzionalità sia supportata per la gestione tra tenant e l'utente disponga delle autorizzazioni appropriate.

Il [cmdlet Azure PowerShell Get-AzSubscription](/powershell/module/Az.Accounts/Get-AzSubscription) Mostra gli `HomeTenantId` `ManagedByTenantIds` attributi e per ogni sottoscrizione, consentendo di identificare se una sottoscrizione restituita appartiene a un tenant gestito o al tenant di gestione.

Analogamente, i comandi dell'interfaccia della riga di comando di Azure come [AZ account list](/cli/azure/account#az-account-list) mostrano gli `homeTenantId` `managedByTenants` attributi e. Se, quando si usa l'interfaccia della riga di comando di Azure, questi valori non vengono visualizzati, provare a cancellare la cache eseguendo `az account clear` seguito da `az login --identity`.

Nell'API REST di Azure, i comandi [Subscriptions-Get](/rest/api/resources/subscriptions/get) e [Subscriptions-list](/rest/api/resources/subscriptions/list) includono `ManagedByTenant` .

> [!NOTE]
> Oltre alle informazioni sui tenant correlate al Faro di Azure, i tenant visualizzati da queste API possono anche riflettere i tenant partner per Azure Databricks o le applicazioni gestite di Azure.

Sono inoltre disponibili API specifiche per l'esecuzione delle attività del Faro di Azure. Per altre informazioni, vedere la sezione **Riferimento**.

## <a name="enhanced-services-and-scenarios"></a>Scenari e servizi avanzati

La maggior parte delle attività e dei servizi può essere eseguita nelle risorse delegate tra i tenant gestiti. Di seguito sono riportati alcuni scenari principali in cui la gestione tra tenant può essere particolarmente efficace.

[Azure Arc](../../azure-arc/index.yml):

- Gestire server ibridi su larga scala- [server abilitati per Azure Arc](../../azure-arc/servers/overview.md):
  - [Gestire computer Windows Server o Linux all'esterno di Azure connessi](../../azure-arc/servers/onboard-portal.md) a sottoscrizioni delegate e/o gruppi di risorse in Azure
  - Gestire computer connessi usando costrutti di Azure, ad esempio Criteri di Azure e assegnazione di tag
  - Assicurarsi che lo stesso set di criteri venga applicato tra gli ambienti ibridi dei clienti
  - Usare il Centro sicurezza di Azure per monitorare la conformità tra gli ambienti ibridi dei clienti
- Gestire cluster Kubernetes ibridi su larga scala: [Azure Arc abilitato Kubernetes (anteprima)](../../azure-arc/kubernetes/overview.md):
  - [Gestire i cluster Kubernetes connessi](../../azure-arc/kubernetes/connect-cluster.md) a sottoscrizioni delegate e/o gruppi di risorse in Azure
  - [Usare GitOps](../../azure-arc/kubernetes/use-gitops-connected-cluster.md) per i cluster connessi
  - Applicare i criteri tra i cluster connessi

[Automazione di Azure](../../automation/index.yml):

- Usare gli account di automazione per accedere e lavorare con le risorse delegate

[Backup di Azure](../../backup/index.yml):

- Eseguire il backup e il ripristino dei dati dei clienti [da carichi di lavoro locali, VM di Azure, condivisioni file di Azure e altro ancora](../..//backup/backup-overview.md#what-can-i-back-up)
- Usare l'[Explorer di Backup](../../backup/monitor-azure-backup-with-backup-explorer.md) per visualizzare le informazioni operative degli elementi di backup (incluse le risorse di Azure non ancora configurate per il backup) e le informazioni di monitoraggio (processi e avvisi) per le sottoscrizioni delegate. L'Explorer di Backup è attualmente disponibile solo per i dati delle macchine virtuali di Azure.
- Usare i [report di Backup](../../backup/configure-reports.md) tra sottoscrizioni delegate per tenere traccia delle tendenze cronologiche, analizzare l'utilizzo dello spazio di archiviazione dei backup e controllare i backup e i ripristini.

[Gestione dei costi di Azure e fatturazione](../../cost-management-billing/index.yml):

- Dal tenant di gestione, i partner CSP possono visualizzare, gestire e analizzare i costi di consumo pre-imposte (non inclusi gli acquisti) per i clienti che si trovano nel piano di Azure. Il costo sarà basato sulle tariffe al dettaglio e sull'accesso in base al ruolo di Azure (controllo degli accessi in base al ruolo di Azure) che il partner ha per la sottoscrizione del cliente.

[Servizio Azure Kubernetes](../../aks/index.yml):

- Gestire gli ambienti Kubernetes ospitati e distribuire e gestire le applicazioni in contenitori nei tenant dei clienti

[Monitoraggio di Azure](../../azure-monitor/index.yml):

- Visualizzare avvisi per le sottoscrizioni delegate, con la possibilità di visualizzare gli avvisi in tutte le sottoscrizioni
- Visualizzare i dettagli del log attività per le sottoscrizioni delegate
- Log Analytics: eseguire query sui dati dalle aree di lavoro remote in più tenant
- Creare avvisi nei tenant dei clienti che attivano l'automazione, ad esempio manuali operativi di automazione di Azure o funzioni di Azure, nel tenant di gestione tramite webhook
- Creare impostazioni di diagnostica nei tenant del cliente per inviare i log delle risorse alle aree di lavoro nel tenant di gestione
- Per i carichi di lavoro SAP, [monitora le metriche delle soluzioni SAP con una visualizzazione aggregata tra i tenant dei clienti](https://techcommunity.microsoft.com/t5/running-sap-applications-on-the/using-azure-lighthouse-and-azure-monitor-for-sap-solutions-to/ba-p/1537293)

[Rete di Azure](../../networking/networking-overview.md):

- Distribuire e gestire la [rete virtuale di Azure](../../virtual-network/index.yml) e le schede di interfaccia di rete virtuale (schede) all'interno di tenant gestiti
- Distribuire e configurare il [Firewall di Azure](../../firewall/overview.md) per proteggere le risorse della rete virtuale dei clienti
- Gestire i servizi di connettività, ad esempio [WAN virtuale di Azure](../../virtual-wan/virtual-wan-about.md), [ExpressRoute](../../expressroute/expressroute-introduction.md)e [gateway VPN](../../vpn-gateway/vpn-gateway-about-vpngateways.md)
- Usare Azure Lighthouse per supportare gli scenari principali per il programma [Rete di Azure MSP](../../networking/networking-partners-msp.md)

[Criteri di Azure](../../governance/policy/index.yml):

- Gli snapshot di conformità mostrano i dettagli per i criteri assegnati nelle sottoscrizioni delegate
- Creare e modificare le definizioni dei criteri nelle sottoscrizioni delegate
- Assegnare le definizioni dei criteri definiti dal cliente nelle sottoscrizioni delegate
- I clienti visualizzano i criteri creati dal provider di servizi insieme ai criteri che hanno creato personalmente
- Consente [di correggere deployIfNotExists o di modificare le assegnazioni all'interno del tenant gestito](../how-to/deploy-policy-remediation.md)

[Azure Resource Graph](../../governance/resource-graph/index.yml):

- Include ora l'ID tenant nei risultati della query restituiti, consentendo di identificare se una sottoscrizione appartiene a un tenant gestito

[Centro sicurezza di Azure](../../security-center/index.yml):

- Visibilità tra tenant
  - Monitorare la conformità ai criteri di sicurezza e garantire la copertura della sicurezza tra le risorse di tutti i tenant
  - Monitoraggio continuo della conformità alle normative in più tenant in una singola visualizzazione
  - Monitorare, valutare e classificare in ordine di priorità le raccomandazioni sulla sicurezza di utilità pratica con il calcolo del punteggio di sicurezza
- Gestione del comportamento di sicurezza tra tenant
  - Gestire i criteri di sicurezza
  - Intervenire sulle risorse non conformi alle raccomandazioni sulla sicurezza di utilità pratica
  - Raccogliere e archiviare i dati relativi alla sicurezza
- Rilevamento e protezione dalle minacce tra tenant
  - Rilevare le minacce nelle risorse dei tenant
  - Applicare controlli avanzati di protezione dalle minacce, ad esempio l'accesso JIT alle macchine virtuali
  - Rafforzare la configurazione dei gruppi di sicurezza di rete con Protezione avanzata adattiva per la rete
  - Assicurarsi che i server eseguano solo le applicazioni e i processi che devono eseguire con i controlli applicazione adattivi
  - Monitorare le modifiche apportate a file e voci del Registro di sistema importanti con Monitoraggio dell'integrità dei file
- Si noti che l'intera sottoscrizione deve essere delegata al tenant di gestione; Gli scenari del Centro sicurezza di Azure non sono supportati con i gruppi di risorse delegati

[Azure Sentinel](../../sentinel/multiple-tenants-service-providers.md):

- Gestire le risorse di Azure Sentinel [nei tenant dei clienti](../../sentinel/multiple-tenants-service-providers.md)
- [Tenere traccia degli attacchi e visualizzare gli avvisi di sicurezza in più tenant](https://techcommunity.microsoft.com/t5/azure-sentinel/using-azure-lighthouse-and-azure-sentinel-to-monitor-across/ba-p/1043899)
- [Visualizzare gli eventi imprevisti](../../sentinel/multiple-workspace-view.md) tra più aree di lavoro Sentinel distribuite tra i tenant

[Integrità dei servizi di Azure](../../service-health/index.yml):

- Monitorare l'integrità delle risorse dei clienti con Integrità risorse di Azure
- Tenere traccia dell'integrità dei servizi di Azure usati dai clienti

[Azure Site Recovery](../../site-recovery/index.yml):

- Gestire le opzioni di ripristino di emergenza per le macchine virtuali di Azure nei tenant dei clienti (si noti che non è possibile usare gli `RunAs` account per copiare le estensioni della macchina virtuale)

[Macchine virtuali di Azure](../../virtual-machines/index.yml):

- Usare le estensioni delle macchine virtuali per fornire attività di configurazione e automazione post-distribuzione nelle macchine virtuali di Azure
- Usare la diagnostica di avvio per risolvere i problemi delle macchine virtuali
- Accedere alle VM con la console seriale
- Integrare le VM con Azure Key Vault per password, segreti o chiavi crittografiche per la crittografia del disco usando l' [identità gestita tramite criteri](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/create-keyvault-secret), assicurando che i segreti vengano archiviati in un Key Vault nei tenant gestiti
- Si noti che non è possibile usare Azure Active Directory per l'accesso remoto alle macchine virtuali

Richieste di supporto:

- [Aprire richieste di supporto da **Guida e supporto** ](../../azure-portal/supportability/how-to-create-azure-support-request.md#getting-started) nel portale di Azure per le risorse delegate (selezione del piano di supporto disponibile per l'ambito delegato)

## <a name="current-limitations"></a>Limitazioni correnti

In tutti gli scenari tenere presenti le limitazioni correnti seguenti:

- Le richieste gestite da Azure Resource Manager possono essere eseguite usando Azure Lighthouse. Gli URI delle operazioni per queste richieste iniziano con `https://management.azure.com`. Tuttavia, le richieste gestite da un'istanza di un tipo di risorsa (ad esempio Key Vault accesso ai dati di archiviazione o accesso ai dati di archiviazione) non sono supportate con il faro di Azure. Gli URI delle operazioni per queste richieste iniziano in genere con un indirizzo univoco per l'istanza, ad esempio `https://myaccount.blob.core.windows.net` o `https://mykeyvault.vault.azure.net/`. Il secondo è in genere usato per le operazioni di dati invece che per le operazioni di gestione.
- Le assegnazione di ruolo devono usare i [ruoli predefiniti](../../role-based-access-control/built-in-roles.md) del controllo degli accessi in base al ruolo. Tutti i ruoli predefiniti sono attualmente supportati con la gestione delle risorse delegata di Azure, ad eccezione del proprietario o dei ruoli predefiniti con [`DataActions`](../../role-based-access-control/role-definitions.md#dataactions) autorizzazione. Il ruolo Amministratore Accesso utenti è supportato solo per uso limitato nell'[assegnazione di ruoli a identità gestite](../how-to/deploy-policy-remediation.md#create-a-user-who-can-assign-roles-to-a-managed-identity-in-the-customer-tenant).  I ruoli personalizzati e i [ruoli di amministratore della sottoscrizione classica](../../role-based-access-control/classic-administrators.md) non sono supportati.
- Sebbene sia possibile caricare sottoscrizioni che utilizzano Azure Databricks, gli utenti nel tenant di gestione non possono al momento avviare le aree di lavoro Azure Databricks in una sottoscrizione delegata.
- Sebbene sia possibile caricare sottoscrizioni e gruppi di risorse con blocchi di risorse, tali blocchi non impediranno l'esecuzione di azioni da parte degli utenti nel tenant di gestione. Le [assegnazioni di rifiuto](../../role-based-access-control/deny-assignments.md) che proteggono le risorse gestite dal sistema, ad esempio quelle create dalle applicazioni gestite di Azure o Azure Blueprints (assegnazioni di rifiuto assegnate dal sistema), impediscono agli utenti del tenant di gestione di agire su tali risorse; tuttavia, a questo punto gli utenti del tenant del cliente non possono creare le proprie assegnazioni di rifiuto (assegnazioni di rifiuto assegnate dall'utente).

## <a name="next-steps"></a>Passaggi successivi

- È possibile caricare i clienti in Azure Lighthouse, [usando Azure Resource Manager modelli](../how-to/onboard-customer.md) o pubblicando [un'offerta di servizi gestiti pubblici o privati in Azure Marketplace](../how-to/publish-managed-services-offers.md).
- [Visualizzare e gestire i clienti](../how-to/view-manage-customers.md) passando a **Clienti personali** nel portale di Azure.
