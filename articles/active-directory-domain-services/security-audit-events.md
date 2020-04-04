---
title: Abilitare i controlli di sicurezza per Servizi di dominio Azure AD Documenti Microsoft
description: Informazioni su come abilitare i controlli di sicurezza per centralizzare la registrazione degli eventi per l'analisi e gli avvisi in Servizi di dominio Azure AD
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 662362c3-1a5e-4e94-ae09-8e4254443697
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 02/10/2020
ms.author: iainfou
ms.openlocfilehash: ce910b553e14d09eefa35efc5f2973337dfa1309
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/03/2020
ms.locfileid: "80654669"
---
# <a name="enable-security-audits-for-azure-active-directory-domain-services"></a>Abilitare i controlli di sicurezza per Servizi di dominio Azure Active DirectoryEnable security audits for Azure Active Directory Domain Services

I controlli di sicurezza di Servizi di dominio Azure Active Directory (Azure AD DS) consentono di eseguire gli eventi di sicurezza del flusso di Azure alle risorse di destinazione. Queste risorse includono Archiviazione di Azure, aree di lavoro di Azure Log Analytics o Hub eventi di Azure.These resources include Azure Storage, Azure Log Analytics workspaces, or Azure Event Hub. Dopo aver abilitato gli eventi di controllo di sicurezza, Azure AD DS invia tutti gli eventi controllati per la categoria selezionata alla risorsa di destinazione.

È possibile archiviare gli eventi in eventi di archiviazione e flusso di Azure nel software SIEM (Security Information and Event Management) usando Hub eventi di Azure oppure eseguire analisi personalizzate e usare le aree di lavoro di Azure Log Analytics dal portale di Azure.You can archive events into Azure storage and stream events into security information and event management (SIEM) software (or equivalent) using Azure Event Hubs, oppure eseguire analisi personalizzate e usare le aree di lavoro di Azure Log Analytics dal portale di Azure.You can archive events into Azure storage and stream events into security information and event management (SIEM) software (or equivalent) using Azure Event Hubs, or do your own analysis and using Azure Log Analytics workspaces

> [!IMPORTANT]
> I controlli di sicurezza di Servizi di dominio Active Directory di Azure sono disponibili solo per le istanze basate su Azure Resource Manager.Azure AD DS security audits are only available for Azure Resource Manager-based instances. Per informazioni su come eseguire la migrazione, vedere Eseguire la migrazione di Azure AD DS dal modello di [rete virtuale classica a Resource Manager.][migrate-azure-adds]

## <a name="security-audit-destinations"></a>Destinazioni di controllo di sicurezza

È possibile usare Archiviazione di Azure, Hub eventi di Azure o aree di lavoro di Azure Log Analytics come risorsa di destinazione per i controlli di sicurezza di Azure AD DS.You can use Azure Storage, Azure Event Hubs, or Azure Log Analytics workspaces as a target resource for Azure AD DS security audits. Queste destinazioni possono essere combinate. Ad esempio, è possibile usare Archiviazione di Azure per archiviare gli eventi di controllo della sicurezza, ma un'area di lavoro di Analisi dei log di Azure per analizzare e creare report sulle informazioni a breve termine.

Nella tabella seguente vengono descritti gli scenari per ogni tipo di risorsa di destinazione.

> [!IMPORTANT]
> È necessario creare la risorsa di destinazione prima di abilitare i controlli di sicurezza di Servizi di dominio Active Directory di Azure.You need to create the target resource before you enable Azure AD DS security audits. È possibile creare queste risorse usando il portale di Azure, Azure PowerShell o l'interfaccia della riga di comando di Azure.You can create these resources using the Azure portal, Azure PowerShell, or the Azure CLI.

| Risorsa di destinazione | Scenario |
|:---|:---|
|Archiviazione di Azure| Questa destinazione deve essere utilizzata quando è necessario archiviare gli eventi di controllo di sicurezza per scopi di archiviazione. Altre destinazioni possono essere utilizzate per scopi di archiviazione, tuttavia tali destinazioni forniscono funzionalità oltre la necessità primaria di archiviazione. <br /><br />Prima di abilitare gli eventi di controllo di sicurezza di Servizi di dominio Active Directory di Azure, creare un account di Archiviazione di Azure.Before you enable Azure AD DS security audit [events,](../storage/common/storage-account-create.md)first Create an Azure Storage account .|
|Hub eventi di Azure| Questa destinazione deve essere utilizzata quando la necessità principale è quella di condividere gli eventi di controllo di sicurezza con software aggiuntivi, ad esempio software di analisi dei dati o software SIEM (Security Information & Event Management).<br /><br />Prima di abilitare gli eventi di controllo di sicurezza di Servizi di dominio Active Directory di Azure, [creare un hub eventi tramite il portale](https://docs.microsoft.com/azure/event-hubs/event-hubs-create) di Azure|
|Azure Log Analytics Workspace| Questa destinazione deve essere usata quando è necessario analizzare ed esaminare direttamente i controlli sicuri dal portale di Azure.This target should be used when your primary need is to analyze and review secure audits from the Azure portal directly.<br /><br />Prima di abilitare gli eventi di controllo di sicurezza di Servizi di dominio Active Directory di Azure, [creare un'area](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace) di lavoro di Log Analytics nel portale di Azure.Before you enable Azure AD DS security audit events, Create a Log Analytics workspace in the Azure portal.|

## <a name="enable-security-audit-events-using-the-azure-portal"></a>Abilitare gli eventi di controllo di sicurezza tramite il portale di AzureEnable security audit events using the Azure portal

Per abilitare gli eventi di controllo di sicurezza di Servizi di dominio Active Directory di Azure tramite il portale di Azure, completare i passaggi seguenti.

> [!IMPORTANT]
> I controlli di sicurezza di Servizi di dominio Active Directory di Azure non sono retroattivi. Non puoi recuperare o riprodurre eventi dal passato. Servizi di dominio Active Directory di Azure può inviare eventi che si verificano solo dopo l'abilitazione dei controlli di sicurezza.

1. Accedere al portale di Azure all'indirizzo https://portal.azure.com.
1. Nella parte superiore del portale di Azure cercare e selezionare Servizi di **dominio Azure AD.** Scegliere il dominio gestito, ad esempio *aaddscontoso.com*.
1. Nella finestra Servizi di dominio Active Directory di Azure selezionare **Impostazioni di diagnostica** sul lato sinistro.
1. Nessuna diagnostica configurata per impostazione predefinita. Per iniziare, selezionare **Aggiungi impostazione diagnostica**.

    ![Aggiungere un'impostazione di diagnostica per Servizi di dominio Azure ADAdd a diagnostic setting for Azure AD Domain Services](./media/security-audit-events/add-diagnostic-settings.png)

1. Immettere un nome per la configurazione diagnostica, ad esempio *aadds-auditing*.

    Selezionare la casella relativa alla destinazione del controllo di sicurezza desiderata. È possibile scegliere da un account di archiviazione di Azure, un hub eventi di Azure o un'area di lavoro di Log Analytics.You can choose from an Azure Storage account, an Azure event hub, or a Log Analytics workspace. Queste risorse di destinazione devono già esistere nella sottoscrizione di Azure.These destination resources must already exist in your Azure subscription. Non è possibile creare le risorse di destinazione in questa procedura guidata.

    ![Abilitare la destinazione e il tipo di eventi di controllo necessari per l'acquisizione](./media/security-audit-events/diagnostic-settings-page.png)

    * **Archiviazione di AzureAzure storage**
        * Selezionare **Archivia in un account di archiviazione**, quindi scegliere **Configura**.
        * Selezionare la **sottoscrizione** e l'account **di archiviazione** che si desidera utilizzare per archiviare gli eventi di controllo della sicurezza.
        * Al termine, scegliere **OK**.
    * **Hub eventi di AzureAzure event hubs**
        * Selezionare **Flusso a un hub eventi**, quindi scegliere **Configura**.
        * Selezionare **sottoscrizione** e **lo spazio dei nomi dell'hub eventi**. Se necessario, scegliere anche un **nome dell'hub Evento** e quindi **Nome criterio hub eventi**.
        * Al termine, scegliere **OK**.
    * **Aree di lavoro di Analisi dei log di AzureAzure Log Analytic workspaces**
        * Selezionare **Invia a Log Analytics**, quindi scegliere l'area di lavoro **Sottoscrizione** e **Log Analytics** che si vuole usare per archiviare gli eventi di controllo di sicurezza.

1. Selezionare le categorie di log che si desidera includere per la risorsa di destinazione specifica. Se si inviano gli eventi di controllo a un account di archiviazione di Azure, è anche possibile configurare criteri di conservazione che definiscono il numero di giorni per la conservazione dei dati. Un'impostazione predefinita *pari a 0* mantiene tutti i dati e non ruota gli eventi dopo un periodo di tempo.

    È possibile selezionare categorie di log diverse per ogni risorsa di destinazione all'interno di una singola configurazione. Questa funzionalità consente di scegliere le categorie di log da mantenere per Log Analytics e le categorie di log che si desidera archiviare, ad esempio.

1. Al termine, selezionare **Salva** per eseguire il commit delle modifiche. Le risorse di destinazione iniziano a ricevere gli eventi di controllo di sicurezza di Azure AD DS subito dopo il salvataggio della configurazione.

## <a name="enable-security-audit-events-using-azure-powershell"></a>Abilitare gli eventi di controllo di sicurezza con Azure PowerShellEnable security audit events using Azure PowerShell

Per abilitare gli eventi di controllo di sicurezza di Servizi di dominio Active Directory di Azure tramite Azure PowerShell, completare i passaggi seguenti. Se necessario, installare prima [il modulo di Azure PowerShell e connettersi alla sottoscrizione](/powershell/azure/install-az-ps)di Azure.

> [!IMPORTANT]
> I controlli di sicurezza di Servizi di dominio Active Directory di Azure non sono retroattivi. Non puoi recuperare o riprodurre eventi dal passato. Servizi di dominio Active Directory di Azure può inviare eventi che si verificano solo dopo l'abilitazione dei controlli di sicurezza.

1. Eseguire l'autenticazione alla sottoscrizione di Azure usando il cmdlet [Connect-AzAccount.Authenticate](/powershell/module/Az.Accounts/Connect-AzAccount) to your Azure subscription using the Connect-AzAccount cmdlet. Quando richiesto, immettere le credenziali dell'account.

    ```azurepowershell
    Connect-AzAccount
    ```

1. Creare la risorsa di destinazione per gli eventi di controllo di sicurezza.

    * **Archiviazione di Azure** - Creare un account di archiviazione con Azure PowerShellAzure storage[Create a storage account using Azure PowerShell](../storage/common/storage-account-create.md?tabs=azure-powershell)
    * **Hub eventi di Azure** - Creare un hub[eventi tramite Azure PowerShell.Azure](../event-hubs/event-hubs-quickstart-powershell.md)event hubs Create an event hub using Azure PowerShell . Potrebbe anche essere necessario usare il cmdlet [New-AzEventHubAuthorizationRule](/powershell/module/az.eventhub/new-azeventhubauthorizationrule) per creare una regola di autorizzazione che conceda le autorizzazioni di Azure AD DS allo *spazio dei nomi*dell'hub eventi. La regola di autorizzazione deve includere i diritti **Manage**, **Listen**e **Send.**

        > [!IMPORTANT]
        > Assicurarsi di impostare la regola di autorizzazione nello spazio dei nomi dell'hub eventi e non nell'hub eventi stesso.

    * **Aree** - di lavoro di Azure Log Analytic[Creare un'area di lavoro di Log Analytics con Azure PowerShell.](../azure-monitor/learn/quick-create-workspace-posh.md)

1. Ottenere l'ID risorsa per il dominio gestito di Azure AD DS usando il cmdlet [Get-AzResource.Get](/powershell/module/Az.Resources/Get-AzResource) the resource ID for your Azure AD DS managed domain using the Get-AzResource cmdlet. Creare una variabile denominata *$aadds. ResourceId* per contenere il valore:ResourceId to hold the value:

    ```azurepowershell
    $aadds = Get-AzResource -name aaddsDomainName
    ```

1. Configurare le impostazioni di Diagnostica di Azure usando il cmdlet [Set-AzDiagnosticSetting](/powershell/module/Az.Monitor/Set-AzDiagnosticSetting) per usare la risorsa di destinazione per gli eventi di controllo di sicurezza di Servizi di dominio Azure AD. Negli esempi seguenti la variabile *$aadds. ResourceId* viene usato dal passaggio precedente.

    * **Archiviazione di Azure:** sostituire storageAccountId con il nome dell'account di archiviazione:Azure storage - Replace *storageAccountId* with your storage account name:

        ```powershell
        Set-AzDiagnosticSetting `
            -ResourceId $aadds.ResourceId `
            -StorageAccountId storageAccountId `
            -Enabled $true
        ```

    * Hub eventi di Azure: sostituire eventHubName con il nome dell'hub eventi e eventHubRuleId con l'ID della regola di autorizzazione:Azure event **hubhub hubs** - Replace *eventHubName* with the name of your event hub and *eventHubRuleId* with your authorization rule ID:

        ```powershell
        Set-AzDiagnosticSetting -ResourceId $aadds.ResourceId `
            -EventHubName eventHubName `
            -EventHubAuthorizationRuleId eventHubRuleId `
            -Enabled $true
        ```

    * **Aree** di lavoro di Analisi log di Azure - Sostituire *workspaceId* con l'ID dell'area di lavoro di Log Analytics:

        ```powershell
        Set-AzureRmDiagnosticSetting -ResourceId $aadds.ResourceId `
            -WorkspaceID workspaceId `
            -Enabled $true
        ```

## <a name="query-and-view-security-audit-events-using-azure-monitor"></a>Query and view security audit events using Azure Monitor

Le aree di lavoro Analisi log consentono di visualizzare e analizzare gli eventi di controllo di sicurezza usando Monitoraggio di Azure e il linguaggio di query Kusto.Log Analytic workspaces let you view and analyze the security audit events using Azure Monitor and the Kusto query language. Questo linguaggio di query è progettato per l'uso di sola lettura che offre funzionalità di analisi dell'alimentazione con una sintassi di facile lettura. Per altre informazioni per iniziare a usare i linguaggi di query Di Kusto, vedere gli articoli seguenti:For more information to get started with Kusto query languages, see the following articles:

* [Documentazione di Monitoraggio di Azure](https://docs.microsoft.com/azure/azure-monitor/)
* [Introduzione a Log Analytics in Monitoraggio di Azure](../azure-monitor/log-query/get-started-portal.md)
* [Introduzione alle query su log in Monitoraggio di Azure](../azure-monitor/log-query/get-started-queries.md)
* [Creare e condividere i dashboard dei dati di Log Analytics](../azure-monitor/learn/tutorial-logs-dashboards.md)

Le query di esempio seguenti possono essere usate per avviare l'analisi degli eventi di controllo di sicurezza da Servizi di dominio Active Directory di Azure.The following sample queries can be used to start analyzing security audit events from Azure AD DS.

### <a name="sample-query-1"></a>Query di esempio 1

Visualizzare tutti gli eventi di blocco dell'account degli ultimi sette giorni:

```Kusto
AADDomainServicesAccountManagement
| where TimeGenerated >= ago(7d)
| where OperationName has "4740"
```

### <a name="sample-query-2"></a>Query di esempio 2

Visualizza tutti gli eventi di blocco dell'account (*4740*) tra il 3 febbraio 2020 e le ore 9.00. e 10 febbraio 2020 mezzanotte, ordinati in ordine crescente per la data e l'ora:

```Kusto
AADDomainServicesAccountManagement
| where TimeGenerated >= datetime(2020-02-03 09:00) and TimeGenerated <= datetime(2020-02-10)
| where OperationName has "4740"
| sort by TimeGenerated asc
```

### <a name="sample-query-3"></a>Query di esempio 3

Visualizzare gli eventi di accesso all'account sette giorni fa (da ora) per l'account denominato utente:

```Kusto
AADDomainServicesAccountLogon
| where TimeGenerated >= ago(7d)
| where "user" == tolower(extract("Logon Account:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
```

### <a name="sample-query-4"></a>Query di esempio 4

Visualizzare gli eventi di accesso all'account sette giorni fa per l'account denominato utente che ha tentato di accedere utilizzando una password errata (*0xC00000006a*):

```Kusto
AADDomainServicesAccountLogon
| where TimeGenerated >= ago(7d)
| where "user" == tolower(extract("Logon Account:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
| where "0xc000006a" == tolower(extract("Error Code:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
```

### <a name="sample-query-5"></a>Query di esempio 5

Visualizzare gli eventi di accesso all'account sette giorni fa per l'account denominato utente che ha tentato di accedere mentre l'account è stato bloccato (*0xC0000234*):

```Kusto
AADDomainServicesAccountLogon
| where TimeGenerated >= ago(7d)
| where "user" == tolower(extract("Logon Account:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
| where "0xc0000234" == tolower(extract("Error Code:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
```

### <a name="sample-query-6"></a>Query di esempio 6

Visualizzare il numero di eventi di accesso dell'account sette giorni fa per tutti i tentativi di accesso che si sono verificati per tutti gli utenti bloccati:

```Kusto
AADDomainServicesAccountLogon
| where TimeGenerated >= ago(7d)
| where "0xc0000234" == tolower(extract("Error Code:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
| summarize count()
```

## <a name="audit-event-categories"></a>Controllare le categorie di eventi

I controlli di sicurezza di Servizi di dominio Active Directory di Azure sono in linea con il controllo tradizionale per i controller di dominio di Servizi di dominio Active Directory tradizionali. Negli ambienti ibridi, è possibile riutilizzare i modelli di controllo esistenti in modo che la stessa logica possa essere utilizzata durante l'analisi degli eventi. A seconda dello scenario che è necessario risolvere o analizzare, è necessario utilizzare le diverse categorie di eventi di controllo.

Sono disponibili le seguenti categorie di eventi di audit:

| Nome categoria di controllo | Descrizione |
|:---|:---|
| Accesso all'account|Controlla i tentativi di autenticazione dei dati dell'account in un controller di dominio o in un gestore di account di protezione (SAM) locale.</p>Le impostazioni dei criteri di accesso e disconnessione e gli eventi tengono traccia dei tentativi di accesso a un determinato computer. Le impostazioni e gli eventi di questa categoria si concentrano sul database degli account utilizzato. Questa categoria include le seguenti sottocategorie:<ul><li>[Controlla Convalida credenziali](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-credential-validation)</li><li>[Controlla Servizio di autenticazione Kerberos](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-kerberos-authentication-service)</li><li>[Controlla Operazioni ticket di servizio Kerberos](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-kerberos-service-ticket-operations)</li><li>[Controlla Altri eventi di accesso/fine sessione](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-other-logonlogoff-events)</li></ul>|
| Gestione degli account|Controlla le modifiche apportate agli account utente e computer e ai gruppi. Questa categoria include le seguenti sottocategorie:<ul><li>[Controlla Gestione gruppi di applicazioni](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-application-group-management)</li><li>[Controlla Gestione account computer](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-computer-account-management)</li><li>[Controlla Gestione gruppi di distribuzione](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-distribution-group-management)</li><li>[Controllo della gestione degli altri account](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-other-account-management-events)</li><li>[Controlla Gestione gruppi di sicurezza](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-security-group-management)</li><li>[Controlla Gestione account utente](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-user-account-management)</li></ul>|
| Tracciamento dei dettagli|Controlla le attività di singole applicazioni e utenti su tale computer e per comprendere come viene utilizzato un computer. Questa categoria include le seguenti sottocategorie:<ul><li>[Controlla Attività DPAPI](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-dpapi-activity)</li><li>[Controllare l'attività PNP](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-pnp-activity)</li><li>[Controlla Creazione di processi](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-process-creation)</li><li>[Controlla Chiusura di processi](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-process-termination)</li><li>[Controlla Eventi RPC](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-rpc-events)</li></ul>|
| Accesso ai servizi directory|Controlla i tentativi di accesso e modifica degli oggetti in Servizi di dominio Active Directory. Questi eventi di controllo vengono registrati solo nei controller di dominio. Questa categoria include le seguenti sottocategorie:<ul><li>[Controlla Replica dettagliata servizio directory](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-detailed-directory-service-replication)</li><li>[Controlla Accesso al servizio directory](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-directory-service-access)</li><li>[Controlla Modifiche servizio directory](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-directory-service-changes)</li><li>[Controlla Replica servizio directory](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-directory-service-replication)</li></ul>|
| Access-Logoff|Controlla i tentativi di accesso a un computer in modo interattivo o in rete. Questi eventi sono utili per tenere traccia dell'attività degli utenti e identificare potenziali attacchi alle risorse di rete. Questa categoria include le seguenti sottocategorie:<ul><li>[Controlla Blocco account](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-account-lockout)</li><li>[Controlla attestazioni utente/dispositivo](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-user-device-claims)</li><li>[Controlla Modalità estesa IPsec](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-ipsec-extended-mode)</li><li>[Controlla appartenenza a gruppo](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-group-membership)</li><li>[Controlla Modalità principale IPsec](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-ipsec-main-mode)</li><li>[Controlla Modalità rapida IPsec](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-ipsec-quick-mode)</li><li>[Controlla Fine sessione](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-logoff)</li><li>[Controlla Accesso](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-logon)</li><li>[Controlla Server dei criteri di rete](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-network-policy-server)</li><li>[Controlla Altri eventi di accesso/fine sessione](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-other-logonlogoff-events)</li><li>[Controlla Accesso speciale](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-special-logon)</li></ul>|
|Accesso a oggetti| Controlla i tentativi di accesso a oggetti o tipi di oggetti specifici in una rete o in un computer. Questa categoria include le seguenti sottocategorie:<ul><li>[Controlla Generato dall'applicazione](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-application-generated)</li><li>[Controlla Servizi di certificazione](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-certification-services)</li><li>[Controlla condivisione file dettagliata](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-detailed-file-share)</li><li>[Controlla Condivisione file](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-file-share)</li><li>[File system di controllo](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-file-system)</li><li>[Controlla Connessione a Piattaforma filtro Windows](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-filtering-platform-connection)</li><li>[Controlla Mancata elaborazione pacchetti Piattaforma filtro Windows](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-filtering-platform-packet-drop)</li><li>[Manipolazione dell'handle di controlloAudit Handle Manipulation](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-handle-manipulation)</li><li>[Oggetto kernel di controllo](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-kernel-object)</li><li>[Controlla Altri eventi di accesso agli oggetti](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-other-object-access-events)</li><li>[Registro di controllo](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-registry)</li><li>[Controllo di Archiviazione rimovibile](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-removable-storage)</li><li>[Controlla SAM](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-sam)</li><li>[Controlla Gestione temporanea Criteri di accesso centrale](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-central-access-policy-staging)</li></ul>|
|Modifica dei criteri|Controlla le modifiche a criteri di sicurezza importanti in un sistema locale o in una rete. I criteri vengono in genere stabiliti dagli amministratori per proteggere le risorse di rete. Il monitoraggio delle modifiche o i tentativi di modifica di questi criteri può essere un aspetto importante della gestione della sicurezza per una rete. Questa categoria include le seguenti sottocategorie:<ul><li>[Controlla Controlla modifica ai criteri](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-audit-policy-change)</li><li>[Controlla Modifica criteri di autenticazione](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-authentication-policy-change)</li><li>[Controlla Modifica criteri di autorizzazione](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-authorization-policy-change)</li><li>[Controlla Modifica criteri Piattaforma filtro Windows](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-filtering-platform-policy-change)</li><li>[Controlla Modifica criteri a livello di regola MPSSVC](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-mpssvc-rule-level-policy-change)</li><li>[Audit altre modifiche ai criteri](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-other-policy-change-events)</li></ul>|
|Uso dei privilegi| Controlla l'utilizzo di determinate autorizzazioni su uno o più sistemi. Questa categoria include le seguenti sottocategorie:<ul><li>[Controlla Utilizzo privilegi non sensibili](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-non-sensitive-privilege-use)</li><li>[Controlla Utilizzo privilegi sensibili](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-sensitive-privilege-use)</li><li>[Controlla Altri eventi di utilizzo dei privilegi](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-other-privilege-use-events)</li></ul>|
|System| Controlla le modifiche a livello di sistema a un computer non incluso in altre categorie e che hanno potenziali implicazioni sulla sicurezza. Questa categoria include le seguenti sottocategorie:<ul><li>[Controlla Driver IPSec](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-ipsec-driver)</li><li>[Controlla Altri eventi di sistema](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-other-system-events)</li><li>[Controlla Modifica stato sicurezza](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-security-state-change)</li><li>[Controlla Estensione sistema di sicurezza](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-security-system-extension)</li><li>[Controlla Integrità sistema](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-system-integrity)</li></ul>|

## <a name="event-ids-per-category"></a>ID evento per categoria

 I controlli di sicurezza di Azure AD ServicesS registrano gli ID evento seguenti quando l'azione specifica attiva un evento controllabile:Azure AD DS security audits record the following event IDs when the specific action triggers an auditable event:

| Nome categoria evento | ID evento |
|:---|:---|
|Protezione dell'accesso all'account|4767, 4774, 4775, 4776, 4777|
|Sicurezza di Gestione account|4720, 4722, 4723, 4724, 4725, 4726, 4727, 4728, 4729, 4730, 4731, 4732, 4733, 4734, 4735, 4737, 4738, 4740, 4741, 4742, 4743, 4754, 4755, 4756, 4757, 4758, 4764, 4765, 4766, 4780, 4781, 4782, 4793, 4798, 4799, 5376, 5377|
|Sicurezza del monitoraggio dei dettagli|nessuno|
|Sicurezza dell'accesso DS|5136, 5137, 5138, 5139, 5141|
|Protezione accessi-logoff|4624, 4625, 4634, 4647, 4648, 4672, 4675, 4964|
|Sicurezza dell'accesso agli oggetti|nessuno|
|Sicurezza delle modifiche dei criteri|4670, 4703, 4704, 4705, 4706, 4707, 4713, 4715, 4716, 4717, 4718, 4719, 4739, 4864, 4865, 4866, 4867, 4904, 4906, 4911, 4912|
|PrivilegiO Utilizzare la sicurezza|4985|
|Protezione del sistema|4612, 4621|

## <a name="next-steps"></a>Passaggi successivi

Per informazioni specifiche su Kusto, vedere i seguenti articoli:

* [Panoramica](/azure/kusto/query/) del linguaggio di query Kusto.
* [Kusto esercitazione](/azure/kusto/query/tutorial) per acquisire familiarità con le nozioni di base sulle query.
* [Query di esempio](/azure/kusto/query/samples) che consentono di apprendere nuovi modi per visualizzare i dati.
* Kusto [best practice](/azure/kusto/query/best-practices) per ottimizzare le query per il successo.

<!-- LINKS - Internal -->
[migrate-azure-adds]: migrate-from-classic-vnet.md
