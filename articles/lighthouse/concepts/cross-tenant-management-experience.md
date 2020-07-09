---
title: Esperienze di gestione tra tenant
description: La gestione risorse delegate di Azure consente un'esperienza di gestione tra tenant.
ms.date: 05/12/2020
ms.topic: conceptual
ms.openlocfilehash: 5e8a678530d9cf334d89091e7f23191ae8613737
ms.sourcegitcommit: e995f770a0182a93c4e664e60c025e5ba66d6a45
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/08/2020
ms.locfileid: "86135487"
---
# <a name="cross-tenant-management-experiences"></a>Esperienze di gestione tra tenant

Come provider di servizi, è possibile usare [Azure Lighthouse](../overview.md) per gestire le risorse per più clienti dall'interno del proprio tenant nel [portale di Azure](https://portal.azure.com). Molte attività e servizi possono essere eseguiti su risorse di Azure delegate tra tenant gestiti usando la [gestione delle risorse delegata di Azure](../concepts/azure-delegated-resource-management.md).

> [!NOTE]
> La gestione risorse delegate di Azure può essere usata anche [all'interno di un'azienda con più tenant Azure AD propri](enterprise.md) per semplificare l'amministrazione tra tenant.

## <a name="understanding-customer-tenants"></a>Informazioni sui tenant dei clienti

Un tenant di Azure Active Directory (Azure AD) è una rappresentazione di un'organizzazione. Si tratta di un'istanza dedicata di Azure AD che un'organizzazione riceve quando crea una relazione con Microsoft iscrivendosi ad Azure, Microsoft 365 o ad altri servizi. Ogni tenant di Azure AD è distinto e separato dagli altri tenant di Azure AD e ha un proprio ID tenant (un GUID). Per altre informazioni, vedere [Informazioni su Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md).

In genere, per gestire le risorse di Azure per un cliente, i provider di servizi devono accedere al portale di Azure usando un account associato al tenant del cliente, richiedendo a un amministratore del tenant del cliente di creare e gestire gli account utente per il provider di servizi.

Con Azure Lighthouse, il processo di onboarding specifica gli utenti all'interno del tenant del provider di servizi che saranno in grado di accedere e gestire le sottoscrizioni, i gruppi di risorse e le risorse nel tenant del cliente. Questi utenti possono quindi accedere al portale di Azure usando le proprie credenziali. All'interno del portale di Azure possono gestire le risorse appartenenti a tutti i clienti a cui hanno accesso. Per eseguire questa operazione, visitare la pagina [Clienti personali](../how-to/view-manage-customers.md) nel portale di Azure o accedere direttamente al contesto della sottoscrizione di tale cliente nel portale di Azure o tramite API.

Azure Lighthouse consente una maggiore flessibilità nella gestione delle risorse per più clienti senza dover accedere a diversi account in tenant diversi. Un provider di servizi, ad esempio, può avere due clienti, con responsabilità e livelli di accesso diversi. Usando Azure Lighthouse, gli utenti autorizzati possono accedere al tenant del provider di servizi per accedere a tali risorse.

![Risorse dei clienti gestite tramite un tenant del provider di servizi](../media/azure-delegated-resource-management-service-provider-tenant.jpg)

## <a name="apis-and-management-tool-support"></a>Supporto delle API e degli strumenti di gestione

È possibile eseguire le attività di gestione nelle risorse delegate direttamente nel portale oppure usando le API e gli strumenti di gestione, ad esempio l'interfaccia della riga di comando di Azure e Azure PowerShell. Quando si usano le risorse delegate, è possibile utilizzare tutte le API esistenti, purché la funzionalità sia supportata per la gestione tra tenant e l'utente disponga delle autorizzazioni appropriate.

Il [cmdlet Get-AzSubscription](/powershell/module/Az.Accounts/Get-AzSubscription?view=azps-3.5.0) di Azure PowerShell visualizza il **tenantID** di ogni sottoscrizione, consentendo di identificare se una sottoscrizione restituita appartiene al tenant del provider di servizi o a un tenant del cliente gestito.

Analogamente, i comandi dell'interfaccia della riga di comando di Azure, ad esempio [az account list](/cli/azure/account?view=azure-cli-latest#az-account-list), mostrano gli attributi **homeTenantId** e **managedByTenants**.

> [!TIP]
> Se, quando si usa l'interfaccia della riga di comando di Azure, questi valori non vengono visualizzati, provare a cancellare la cache eseguendo `az account clear` seguito da `az login --identity`.

Sono inoltre disponibili API specifiche per l'esecuzione delle attività del Faro di Azure. Per altre informazioni, vedere la sezione **Riferimento**.

## <a name="enhanced-services-and-scenarios"></a>Scenari e servizi avanzati

La maggior parte delle attività e dei servizi può essere eseguita nelle risorse delegate tra i tenant gestiti. Di seguito sono riportati alcuni scenari principali in cui la gestione tra tenant può essere particolarmente efficace.

[Azure Arc per server (anteprima)](../../azure-arc/servers/overview.md):

- [Connettere computer Windows Server o Linux esterni ad Azure](../../azure-arc/servers/onboard-portal.md) a sottoscrizioni e/o gruppi di risorse delegate in Azure
- Gestire computer connessi usando costrutti di Azure, ad esempio Criteri di Azure e assegnazione di tag

[Automazione di Azure](../../automation/index.yml):

- Usare gli account di automazione per accedere e lavorare con le risorse dei clienti delegate

[Backup di Azure](../../backup/index.yml):

- Eseguire il backup e il ripristino dei dati dei clienti nei tenant dei clienti
- Usare l'[Explorer di Backup](../../backup/monitor-azure-backup-with-backup-explorer.md) per visualizzare le informazioni operative degli elementi di backup (incluse le risorse di Azure non ancora configurate per il backup) e le informazioni di monitoraggio (processi e avvisi) per le sottoscrizioni delegate. L'Explorer di Backup è attualmente disponibile solo per i dati delle macchine virtuali di Azure.
- Usare i [report di Backup](../../backup/configure-reports.md) tra sottoscrizioni delegate per tenere traccia delle tendenze cronologiche, analizzare l'utilizzo dello spazio di archiviazione dei backup e controllare i backup e i ripristini.

[Gestione dei costi di Azure e fatturazione](../../cost-management-billing/index.yml):

- Dal tenant di gestione, i partner CSP possono visualizzare, gestire e analizzare i costi di consumo pre-imposte (non inclusi gli acquisti) per i clienti che si trovano nel piano di Azure. Il costo sarà basato sulle tariffe al dettaglio e sull'accesso RBAC di Azure che il partner ha per la sottoscrizione del cliente.

[Servizio Azure Kubernetes](../../aks/index.yml):

- Gestire gli ambienti Kubernetes ospitati e distribuire e gestire le applicazioni in contenitori nei tenant dei clienti

[Monitoraggio di Azure](../../azure-monitor/index.yml):

- Visualizzare avvisi per le sottoscrizioni delegate, con la possibilità di visualizzare gli avvisi in tutte le sottoscrizioni
- Visualizzare i dettagli del log attività per le sottoscrizioni delegate
- Log Analytics: Eseguire query sui dati dalle aree di lavoro remote dei clienti in più tenant
- Creare avvisi nei tenant dei clienti che attivano l'automazione, ad esempio runbook di Automazione di Azure o Funzioni di Azure, nel tenant del provider di servizi tramite webhook

[Rete di Azure](../../networking/networking-overview.md):

- Distribuire e gestire [Rete Virtuale di Azure (VNet)](../../virtual-network/index.yml) e schede di interfaccia di rete virtuale nei tenant dei clienti
- Distribuire e configurare il [Firewall di Azure](../../firewall/overview.md) per proteggere le risorse della rete virtuale dei clienti
- Gestire i servizi di connettività, ad esempio la [rete WAN virtuale di Azure](../../virtual-wan/virtual-wan-about.md), [ExpressRoute](../../expressroute/expressroute-introduction.md) e [Gateway VPN](../../vpn-gateway/vpn-gateway-about-vpngateways.md), per i clienti
- Usare Azure Lighthouse per supportare gli scenari principali per il programma [Rete di Azure MSP](../../networking/networking-partners-msp.md)


[Criteri di Azure](../../governance/policy/index.yml):

- Gli snapshot di conformità mostrano i dettagli per i criteri assegnati nelle sottoscrizioni delegate
- Creare e modificare le definizioni dei criteri in una sottoscrizione delegata
- Assegnare le definizioni dei criteri definite dal cliente nella sottoscrizione delegata
- I clienti visualizzano i criteri creati dal provider di servizi insieme ai criteri che hanno creato personalmente
- Può [correggere deployIfNotExists o modificare le assegnazioni nel tenant del cliente](../how-to/deploy-policy-remediation.md)

[Azure Resource Graph](../../governance/resource-graph/index.yml):

- Include ora l'ID tenant nei risultati delle query restituiti, consentendo di determinare se una sottoscrizione appartiene al tenant del cliente o al tenant del provider di servizi

[Centro sicurezza di Azure](../../security-center/index.yml):

- Visibilità tra tenant
  - Monitorare la conformità ai criteri di sicurezza e garantire la copertura della sicurezza tra le risorse di tutti i tenant
  - Monitoraggio continuo della conformità alle normative per più clienti in una singola visualizzazione
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

[Azure Sentinel](../../sentinel/multiple-tenants-service-providers.md):

- Gestire le risorse di Azure Sentinel [nei tenant dei clienti](../../sentinel/multiple-tenants-service-providers.md)
- [Tenere traccia degli attacchi e visualizzare gli avvisi di sicurezza in più tenant dei clienti](https://techcommunity.microsoft.com/t5/azure-sentinel/using-azure-lighthouse-and-azure-sentinel-to-monitor-across/ba-p/1043899)
- [Visualizzare gli eventi imprevisti](../../sentinel/multiple-workspace-view.md) tra più aree di lavoro Sentinel distribuite tra i tenant dei clienti

[Integrità dei servizi di Azure](../../service-health/index.yml):

- Monitorare l'integrità delle risorse dei clienti con Integrità risorse di Azure
- Tenere traccia dell'integrità dei servizi di Azure usati dai clienti

[Azure Site Recovery](../../site-recovery/index.yml):

- Gestire le opzioni di ripristino di emergenza per le macchine virtuali di Azure nei tenant dei clienti (tenere presente che non è possibile usare gli account RunAs per copiare le estensioni delle macchine virtuali)

[Macchine virtuali di Azure](../../virtual-machines/index.yml):

- Usare le estensioni macchina virtuale di Azure per fornire attività di configurazione e automazione post-distribuzione sulle macchine virtuali di Azure nei tenant dei clienti
- Usare la diagnostica di avvio per risolvere i problemi delle macchine virtuali di Azure nei tenant dei clienti
- Accedere alle macchine virtuali con la console seriale nei tenant dei clienti
- Integrare le macchine virtuali con Azure KeyVault per password, segreti o chiavi per la crittografia del disco usando l'[identità gestita tramite dei criteri](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/create-keyvault-secret), assicurando che i segreti vengano archiviati in un insieme di credenziali delle chiavi nei tenant del cliente
- Si noti che non è possibile usare Azure Active Directory per l'accesso remoto alle macchine virtuali nei tenant dei clienti

Richieste di supporto:

- Aprire le richieste di supporto per le risorse delegate dal pannello **Guida e supporto** nel portale di Azure (selezionando il piano di supporto disponibile per l'ambito delegato)

## <a name="current-limitations"></a>Limitazioni correnti
In tutti gli scenari tenere presenti le limitazioni correnti seguenti:

- Le richieste gestite da Azure Resource Manager possono essere eseguite usando la gestione risorse delegate di Azure. Gli URI delle operazioni per queste richieste iniziano con `https://management.azure.com`. Le richieste gestite da un'istanza di un tipo di risorsa (ad esempio l'accesso ai segreti di Key Vault o l'accesso ai dati di archiviazione) non sono tuttavia supportate con la gestione risorse delegate di Azure. Gli URI delle operazioni per queste richieste iniziano in genere con un indirizzo univoco per l'istanza, ad esempio `https://myaccount.blob.core.windows.net` o `https://mykeyvault.vault.azure.net/`. Il secondo è in genere usato per le operazioni di dati invece che per le operazioni di gestione. 
- Le assegnazione di ruolo devono usare i [ruoli predefiniti](../../role-based-access-control/built-in-roles.md) del controllo degli accessi in base al ruolo. Tutti i ruoli predefiniti sono attualmente supportati con la gestione risorse delegate di Azure, ad eccezione di Proprietario e dei ruoli predefiniti con l'autorizzazione [DataActions](../../role-based-access-control/role-definitions.md#dataactions). Il ruolo Amministratore Accesso utenti è supportato solo per uso limitato nell'[assegnazione di ruoli a identità gestite](../how-to/deploy-policy-remediation.md#create-a-user-who-can-assign-roles-to-a-managed-identity-in-the-customer-tenant).  I ruoli personalizzati e i [ruoli di amministratore della sottoscrizione classica](../../role-based-access-control/classic-administrators.md) non sono supportati.
- Sebbene sia possibile caricare sottoscrizioni che utilizzano Azure Databricks, gli utenti nel tenant di gestione non possono al momento avviare le aree di lavoro Azure Databricks in una sottoscrizione delegata.
- Sebbene sia possibile eseguire l'onboarding di sottoscrizioni e gruppi di risorse per la gestione risorse delegate di Azure con blocchi di risorse, tali blocchi non impediranno l'esecuzione di azioni da parte degli utenti nel tenant di gestione. Le [assegnazioni di rifiuto](../../role-based-access-control/deny-assignments.md) che proteggono le risorse gestite dal sistema, ad esempio quelle create dalle applicazioni gestite di Azure o Azure Blueprints (assegnazioni di rifiuto assegnate dal sistema), impediscono agli utenti del tenant di gestione di agire su tali risorse; tuttavia, a questo punto gli utenti del tenant del cliente non possono creare le proprie assegnazioni di rifiuto (assegnazioni di rifiuto assegnate dall'utente).

## <a name="next-steps"></a>Passaggi successivi

- Eseguire l'onboarding dei clienti nella gestione risorse delegate di Azure, [usando i modelli di Azure Resource Manager](../how-to/onboard-customer.md) o [pubblicando un'offerta di servizi gestiti privata o pubblica in Azure Marketplace](../how-to/publish-managed-services-offers.md).
- [Visualizzare e gestire i clienti](../how-to/view-manage-customers.md) passando a **Clienti personali** nel portale di Azure.
