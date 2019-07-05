---
title: Abilitare i controlli di sicurezza di Azure AD Domain Services | Microsoft Docs
description: Abilitare i controlli di sicurezza di Azure AD Domain Services
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: 662362c3-1a5e-4e94-ae09-8e4254443697
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: iainfou
ms.openlocfilehash: 3105296b3c670d3d44789c93878fa1fc6076973b
ms.sourcegitcommit: d2785f020e134c3680ca1c8500aa2c0211aa1e24
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/04/2019
ms.locfileid: "67566495"
---
# <a name="enable-security-audits-for-azure-ad-domain-services-preview"></a>Abilitare i controlli di sicurezza di Azure AD Domain Services (anteprima)
Controlli di sicurezza di Azure Active Directory Domain Service consentono ai clienti di usare il portale di Azure AD Domain Services per eventi di controllo di flusso protezione alle risorse di destinazione. Le risorse che possono ricevere questi eventi includono l'archiviazione di Azure, le aree di lavoro di Analitica di Log di Azure o Hub eventi di Azure. Subito dopo l'abilitazione di eventi di controllo di sicurezza, Azure AD Domain Services invia tutti gli eventi controllati per la categoria selezionata la risorsa di destinazione. Gli eventi di controllo di sicurezza consentono ai clienti di archiviare controllati gli eventi nell'archiviazione di Azure. Inoltre, i clienti possono trasmettere gli eventi in sicurezza le informazioni ed evento software di gestione (SIEM) (o equivalente) con hub eventi o eseguire le proprie analisi e informazioni dettagliate usando Analitica di Log di Azure dal portale di Azure. 

> [!IMPORTANT]
> Sicurezza il controllo di Azure Active Directory Domain Services è disponibile solo su istanze basate su Azure Resource Manager per Azure AD Domain Services.
>
>

## <a name="auditing-event-categories"></a>Categorie di eventi di controllo
Sicurezza il controllo di Azure Active Directory Domain Services consente di allineare il controllo tradizionale è disponibile per i controller di dominio Active Directory Domain Services. Riutilizzo di pattern di controllo esistente garantisce che la stessa logica può essere utilizzata quando l'analisi di eventi. Sicurezza il controllo di Azure Active Directory Domain Services include le seguenti categorie di eventi.

| Nome di categoria di controllo | Descrizione |
|:---|:---|
| Accesso all'account|Consente di controllare i tentativi di autenticazione di dati dell'account in un controller di dominio o in un Security Account Manager (SAM) locale.</p>Accesso e le impostazioni dei criteri di chiusura di sessione ed eventi rilevare tentativi di accesso a un determinato computer. Le impostazioni e gli eventi in questa categoria concentrarsi sul database di account usato. Questa categoria include le seguenti sottocategorie:<ul><li>[Controlla convalida credenziali](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-credential-validation)</li><li>[Servizio di autenticazione Kerberos di controllo](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-kerberos-authentication-service)</li><li>[Controlla operazioni Ticket di servizio Kerberos](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-kerberos-service-ticket-operations)</li><li>[Controlla altri eventi di accesso/fine sessione](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-other-logonlogoff-events)</li></ul>|
| Gestione degli account|I controlli di modifica a gruppi e account utente e computer. Questa categoria include le seguenti sottocategorie:<ul><li>[Gestione gruppo di applicazioni di controllo](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-application-group-management)</li><li>[Controlla gestione degli Account Computer](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-computer-account-management)</li><li>[Gestione dei gruppi di distribuzione di controllo](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-distribution-group-management)</li><li>[Controlla altri gestione degli Account](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-other-account-management-events)</li><li>[Gestione dei gruppi di sicurezza di controllo](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-security-group-management)</li><li>[Controlla gestione degli Account utente](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-user-account-management)</li></ul>|
| Rilevamento dettagli|Consente di controllare le attività di singole applicazioni e utenti in tale computer e verificare l'utilizzo un computer. Questa categoria include le seguenti sottocategorie:<ul><li>[Controlla attività DPAPI](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-dpapi-activity)</li><li>[Controllare l'attività di plug and Play](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-pnp-activity)</li><li>[Creazione del processo di controllo](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-process-creation)</li><li>[Terminazione del processo di controllo](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-process-termination)</li><li>[Controlla eventi RPC](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-rpc-events)</li></ul>|
| Accesso al servizio directory|I controlli tenta di accedere e modificare oggetti in Active Directory Domain Services (AD DS). Questi eventi vengono registrati solo nei controller di dominio di controllo. Questa categoria include le seguenti sottocategorie:<ul><li>[Controlla replica dettagliata servizio Directory](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-detailed-directory-service-replication)</li><li>[Controlla accesso al servizio Directory](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-directory-service-access)</li><li>[Controlla modifiche servizio Directory](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-directory-service-changes)</li><li>[Controlla replica servizio Directory](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-directory-service-replication)</li></ul>|
| Disconnessione|Controlla i tentativi di accedere a un computer in modo interattivo o in una rete. Questi eventi sono utili per il rilevamento di attività dell'utente e identificazione di potenziali attacchi sulle risorse di rete. Questa categoria include le seguenti sottocategorie:<ul><li>[Controlla blocco Account](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-account-lockout)</li><li>[Controllo utente o dispositivo attestazioni](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-user-device-claims)</li><li>[Controlla modalità estesa IPsec](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-ipsec-extended-mode)</li><li>[Appartenenza al gruppo di controllo](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-group-membership)</li><li>[Controlla modalità principale IPsec](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-ipsec-main-mode)</li><li>[Controlla modalità rapida IPsec](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-ipsec-quick-mode)</li><li>[Controlla fine sessione](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-logoff)</li><li>[Controllo dell'accesso](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-logon)</li><li>[Audit Server dei criteri di rete](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-network-policy-server)</li><li>[Controlla altri eventi di accesso/fine sessione](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-other-logonlogoff-events)</li><li>[Controlla accesso speciale](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-special-logon)</li></ul>|
|Accesso agli oggetti| Controlla i tentativi di accesso specifici oggetti o tipi di oggetti in un computer o di rete. Questa categoria include le seguenti sottocategorie:<ul><li>[Controlla generato dall'applicazione](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-application-generated)</li><li>[Controlla servizi di certificazione](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-certification-services)</li><li>[Controlla condivisione File dettagliata](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-detailed-file-share)</li><li>[Controlla condivisione File](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-file-share)</li><li>[Controlla File System](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-file-system)</li><li>[Connessione a piattaforma filtro di controllo](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-filtering-platform-connection)</li><li>[Eliminazione di pacchetti Piattaforma filtro di controllo](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-filtering-platform-packet-drop)</li><li>[Controlla modifica Handle](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-handle-manipulation)</li><li>[Controlla oggetto Kernel](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-kernel-object)</li><li>[Controlla altri eventi di accesso di oggetto](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-other-object-access-events)</li><li>[Controlla Registro di sistema](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-registry)</li><li>[Controllo archivi rimovibili](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-removable-storage)</li><li>[Controlla SAM](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-sam)</li><li>[Controlla gestione temporanea criteri di accesso centrale](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-central-access-policy-staging)</li></ul>|
|Modifica dei criteri|I controlli di modifica ai criteri di sicurezza importanti in un sistema locale o in rete. I criteri vengono in genere definiti dagli amministratori per consentire alle risorse di rete protetta. Monitoraggio delle modifiche o i tentativi di modificare questi criteri può essere un aspetto importante della gestione della sicurezza per una rete. Questa categoria include le seguenti sottocategorie:<ul><li>[Controlla Modifica criteri di controllo](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-audit-policy-change)</li><li>[Controlla Modifica criteri di autenticazione](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-authentication-policy-change)</li><li>[Controlla Modifica criteri di autorizzazione](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-authorization-policy-change)</li><li>[Controlla Modifica criteri piattaforma filtro](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-filtering-platform-policy-change)</li><li>[Controlla Modifica criteri a livello di regola MPSSVC](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-mpssvc-rule-level-policy-change)</li><li>[Controlla Modifica criteri di altri](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-other-policy-change-events)</li></ul>|
|Uso dei privilegi| Consente di controllare l'uso di determinate autorizzazioni per uno o più sistemi. Questa categoria include le seguenti sottocategorie:<ul><li>[Controlla utilizzo privilegi Non sensibili](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-non-sensitive-privilege-use)</li><li>[Controlla utilizzo privilegi sensibili](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-sensitive-privilege-use)</li><li>[Controlla altri eventi di uso dei privilegi](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-other-privilege-use-events)</li></ul>|
|Sistema| Le modifiche a livello di sistema di controlli a un computer non inclusi nelle altre categorie e che hanno potenziali implicazioni di sicurezza. Questa categoria include le seguenti sottocategorie:<ul><li>[Controlla Driver IPsec](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-ipsec-driver)</li><li>[Controlla altri eventi di sistema](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-other-system-events)</li><li>[Controlla modifica stato sicurezza](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-security-state-change)</li><li>[Controlla estensione sistema di sicurezza](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-security-system-extension)</li><li>[Controlla integrità sistema](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-system-integrity)</li></ul>|

## <a name="event-ids-per-category"></a>ID evento per ogni categoria
 Il controllo di Azure Active Directory Domain Services security registra ai seguenti ID evento quando viene attivato l'azione specifica un evento controllabile.

| Nome categoria evento | ID evento |
|:---|:---|
|Protezione dell'accesso dell'account|4767, 4774, 4775, 4776, 4777|
|Sicurezza di gestione di account|4720, 4722, 4723, 4724, 4725, 4726, 4727, 4728, 4729, 4730, 4731, 4732, 4733, 4734, 4735, 4737, 4738, 4740, 4741, 4742, 4743, 4754, 4755, 4756, 4757, 4758, 4764, 4765, 4766, 4780, 4781, 4782, 4793, 4798, 4799, 5376, 5377|
|Sicurezza di rilevamento di dettaglio|Nessuna|
|Sicurezza dall'accesso di dominio Active Directory|5136, 5137, 5138, 5139, 5141|
|Security disconnessione|4624, 4625, 4634, 4647, 4648, 4672, 4675, 4964|
|Sicurezza dall'accesso di oggetto|Nessuna|
|Sicurezza di modifica dei criteri|4670, 4703, 4704, 4705, 4706, 4707, 4713, 4715, 4716, 4717, 4718, 4719, 4739, 4864, 4865, 4866, 4867, 4904, 4906, 4911, 4912|
|Usare la sicurezza con privilegi|4985|
|Sicurezza del sistema|4612, 4621|

## <a name="enable-security-audit-events"></a>Abilitare gli eventi di controllo di sicurezza
Il materiale sussidiario seguente aiuta a iscriverti per eventi di controllo di sicurezza di Azure AD Domain Services.

> [!IMPORTANT]
> I controlli di sicurezza di Azure Active Directory Domain Services non sono retroattivi. Non è possibile recuperare gli eventi degli ultimi o per riprodurre gli eventi degli ultimi. Il servizio può inviare solo gli eventi che si verificano dopo l'attivazione.
>

### <a name="choose-the-target-resource"></a>Scegliere la risorsa di destinazione
È possibile usare qualsiasi combinazione di archiviazione di Azure, hub eventi di Azure o le aree di lavoro di Azure Log Analitica come una risorsa di destinazione per i controlli di sicurezza. Si consideri la tabella seguente per la risorsa ottimale per il caso d'uso.

> [!IMPORTANT]
> È necessario creare la risorsa di destinazione prima di abilitare i controlli di sicurezza di Azure AD Domain Services.
>

| Risorsa di destinazione | Scenario |
|:---|:---|
|Archiviazione di Azure|È consigliabile usare questa destinazione quando l'esigenza primario consiste nell'archiviare gli eventi di controllo di sicurezza per scopi di archiviazione. Altre destinazioni possono essere usati per scopi di archiviazione; Tuttavia, le destinazioni forniscono funzionalità oltre la necessità di archiviazione primaria. Per creare un account di archiviazione di Azure, seguire [creare un account di archiviazione.](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal#create-a-storage-account-1)|
|Hub eventi di Azure|È consigliabile usare questa destinazione quando l'esigenza primario è quello di condividere gli eventi di controllo di sicurezza con software aggiuntivo, ad esempio software di analisi dei dati o sicurezza informazioni & eventi (SIEM) il software di gestione. Per creare un hub eventi, seguire [Guida introduttiva: Creare un hub eventi usando il portale di Azure.](https://docs.microsoft.com/azure/event-hubs/event-hubs-create)|
|Area di lavoro di Log di Azure Analitica|È consigliabile usare questa destinazione quando esigenze primarie per analizzare ed esaminare direttamente i controlli di protezione dal portale di Azure.  Per creare un'area di lavoro di Log Analitica, seguire [creare un'area di lavoro di Log Analitica nel portale di Azure.](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace)|

## <a name="using-the-azure-portal-to-enable-security-audit-events"></a>Nel portale di Azure per abilitare gli eventi di controllo di sicurezza 
1. Accedere al portale di Azure all'indirizzo https://portal.azure.com.  Nel portale di Azure, fare clic su tutti i servizi. Nell'elenco delle risorse, digitare **dominio**. Non appena si inizia a digitare, l'elenco viene filtrato in base all'input. Fare clic su **Azure AD Domain Services**.
2. Fare clic sull'istanza di Azure AD Domain Services dall'elenco.
3. Fare clic su **le impostazioni di diagnostica (anteprima)** dall'elenco di azioni a sinistra.</p>
![azione di impostazione di diagnostica](./media/security-audit-events/diagnostic-settings-action.png)
4. Digitare il nome della configurazione di diagnostica (**aadds-auditing** come esempio).</p>
![pagina Impostazioni di diagnostica](./media/security-audit-events/diagnostic-settings-page.png)
5. Selezionare la casella di controllo appropriata accanto le risorse di destinazione da utilizzare con gli eventi di controllo di sicurezza.
    > [!NOTE]
    > In questa pagina non è possibile creare le risorse di destinazione.
    >
    
    **archiviazione di Azure:**</p>
    Selezionare **archivia in un account di archiviazione**. Fare clic su **configurare**. Selezionare il **abbonamento** e il **account di archiviazione** da usare per archiviare gli eventi di controllo della sicurezza. Fare clic su **OK**.</p>
    
    ![impostazioni di archiviazione di diagnostica](./media/security-audit-events/diag-settings-storage.png)
    
    **Hub eventi di Azure:**</p>
    Selezionare **Stream a un hub eventi**. Fare clic su **configurare**. Nel **pagina dell'hub eventi selezionare**, selezionare la **sottoscrizione** utilizzato per creare l'hub eventi. Selezionare quindi il **spazio dei nomi dell'hub eventi**, **nome hub eventi**, e **nome criterio hub eventi**. Fare clic su **OK**.</p>
    ![impostazioni dell'hub eventi di diagnostica](./media/security-audit-events/diag-settings-eventhub.png)
    
    **Aree di lavoro di Log analitico Azure:**</p>
    Selezionare **Invia a Log Analytics**. Selezionare il **abbonamento** e **dell'area di lavoro di Log Analitica** usato per archiviare gli eventi di controllo di sicurezza.</p>
    ![impostazioni di diagnostica dell'area di lavoro](./media/security-audit-events/diag-settings-log-analytics.png)

6. Selezionare le categorie di log da includere per la risorsa di destinazione specifico. Se si usano account di archiviazione, è possibile configurare i criteri di conservazione.

    > [!NOTE]
    > È possibile selezionare le categorie di log diverso per ogni risorsa di destinazione all'interno di una singola configurazione. In questo modo è possibile scegliere quali log da mantenere per Log Analitica e quali categorie dei log di cui si desidera archiviare le categorie.
    >

7. Fare clic su **salvare** per eseguire il commit delle modifiche. Le risorse di destinazione riceveranno eventi di controllo di sicurezza di Azure AD Domain Services poco dopo aver salvato la configurazione.

## <a name="using-azure-powershell-to-enable-security-audit-events"></a>Usare Azure PowerShell per abilitare gli eventi di controllo di sicurezza
 
### <a name="prerequisites"></a>Prerequisiti

Seguire le istruzioni nell'articolo per [installare il modulo Azure PowerShell e connettersi alla sottoscrizione di Azure](https://docs.microsoft.com/powershell/azure/install-az-ps?toc=%2fazure%2factive-directory-domain-services%2ftoc.json).

### <a name="enable-security-audits"></a>Abilitare i controlli di sicurezza

1. Eseguire l'autenticazione ad Azure Resource Manager per il tenant appropriato e sottoscrizione usando il **Connect-AzAccount** cmdlet Azure PowerShell.
2. Creare la risorsa di destinazione per la sicurezza degli eventi di controllo.</p>
    **archiviazione di Azure:**</p>
    Seguire [creare un account di archiviazione](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-powershell) per creare l'account di archiviazione.</p>
    **Hub eventi di Azure:**</p>
    Seguire [Guida introduttiva: Creare un hub eventi usando Azure PowerShell](https://docs.microsoft.com/azure/event-hubs/event-hubs-quickstart-powershell) per creare l'hub eventi. È necessario anche usare il [New-AzEventHubAuthorizationRule](https://docs.microsoft.com/powershell/module/az.eventhub/new-azeventhubauthorizationrule?view=azps-2.3.2) cmdlet di Azure PowerShell per creare una regola di autorizzazione per consentire le autorizzazioni di Active Directory Active Directory Domain Services per l'hub eventi **dello spazio dei nomi**. La regola di autorizzazione deve includere il **Gestisci**, **ascolto**, e **inviare** diritti.
    > [!IMPORTANT]
    > Verificare di che aver impostato la regola di autorizzazione dello spazio dei nomi dell'hub eventi e non all'hub eventi.
       
    </p>
    
    **Aree di lavoro di Log analitico Azure:**</p>
    Seguire [creare un'area di lavoro di Log Analitica con Azure PowerShell](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace-posh) per creare l'area di lavoro.
3. Ottenere l'ID risorsa per l'istanza di Azure AD Domain Services. In una console di Windows PowerShell aperta, autenticata, digitare il comando seguente. Usare il **$aadds. ResourceId** variabile come parametro per l'ID di risorsa di Azure Active Directory Domain Services per i cmdlet future.
    ```powershell
    $aadds = Get-AzResource -name aaddsDomainName
    ``` 
4. Usare la **Set-AzDiagnosticSetting** cmdlet per configurare le impostazioni di diagnostica di Azure per usare la risorsa di destinazione per gli eventi di controllo di sicurezza di Azure AD Domain Services. Negli esempi riportati di seguito, la variabile $aadds. ResourceId rappresenta l'ID risorsa dell'istanza di Azure AD Domain Services (vedere il passaggio 3).</p>
    **archiviazione di Azure:**
    ```powershell
    Set-AzDiagnosticSetting `
    -ResourceId $aadds.ResourceId` 
    -StorageAccountId storageAccountId `
    -Enabled $true
    ```
    Sostituire *storageAccountId* ID dell'account di archiviazione</p>
    
    **Hub eventi di Azure:**
    ```powershell
    Set-AzDiagnosticSetting -ResourceId $aadds.ResourceId ` 
    -EventHubName eventHubName `
    -EventHubAuthorizationRuleId eventHubRuleId `
    -Enabled $true
    ```
    Sostituire *eventHubName* con il nome dell'hub eventi. Sostituire *eventHubRuleId* con l'ID regola di autorizzazione creato in precedenza.</p>
    
    **Aree di lavoro di Log analitico Azure:**
    ```powershell
    Set-AzureRmDiagnosticSetting -ResourceId $aadds.ResourceId ` 
    -WorkspaceID workspaceId `
    -Enabled $true
    ```
    Sostituire *workspaceId* con l'ID dell'area di lavoro di Log Analitica creato in precedenza. 

## <a name="view-security-audit-events-using-azure-monitor"></a>Visualizzare eventi di controllo di sicurezza tramite Monitoraggio di Azure
Le aree di lavoro analitici di log consentono di visualizzare e analizzare gli eventi di controllo di sicurezza usando monitoraggio di Azure e il linguaggio di query Kusto. Il linguaggio di query è progettato per l'uso di sola lettura che offre funzionalità analitiche di power con una sintassi facile da leggere.
Ecco alcune risorse che consentono di introduzione ai linguaggi di query Kusto.
* [Documentazione di Monitoraggio di Azure](https://docs.microsoft.com/azure/azure-monitor/)
* [Introduzione a Log Analitica in Monitoraggio di Azure](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal)
* [Introduzione alle query di log in Monitoraggio di Azure](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries)
* [Creare e condividere i dashboard dei dati di Log Analitica](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-logs-dashboards)

## <a name="sample-queries"></a>Query di esempio

### <a name="sample-query-1"></a>Esempio di query 1
Tutti gli account del blocco eventi negli ultimi sette giorni.
```Kusto
AADDomainServicesAccountManagement
| where TimeGenerated >= ago(7d)
| where OperationName has "4740"
```

### <a name="sample-query-2"></a>Esempio di query 2
Tutti gli account del blocco eventi (4740) tra il 26 giugno 2019 alle 9. e il 1 ° luglio 2019 mezzanotte, ordinati in ordine crescente in base alla data e ora.
```Kusto
AADDomainServicesAccountManagement
| where TimeGenerated >= datetime(2019-06-26 09:00) and TimeGenerated <= datetime(2019-07-01) 
| where OperationName has "4740"
| sort by TimeGenerated asc
```

### <a name="sample-query-3"></a>Esempio di query 3
Account di log sugli eventi di sette giorni (da adesso) per l'account utente denominato.
```Kusto
AADDomainServicesAccountLogon
| where TimeGenerated >= ago(7d)
| where "user" == tolower(extract("Logon Account:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
```

### <a name="sample-query-4"></a>Esempio di query 4
Eventi accesso account sette giorni dall'ora per l'account del nome utente che ha tentato di accedere usando una password errata (0xC0000006a).
```Kusto
AADDomainServicesAccountLogon
| where TimeGenerated >= ago(7d)
| where "user" == tolower(extract("Logon Account:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
| where "0xc000006a" == tolower(extract("Error Code:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
```

### <a name="sample-query-5"></a>Esempio di query 5
Eventi accesso account sette giorni dall'ora per l'account del nome utente che ha tentato di accedere anche se l'account è stato bloccato (0xC0000234).
```Kusto
AADDomainServicesAccountLogon
| where TimeGenerated >= ago(7d)
| where "user" == tolower(extract("Logon Account:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
| where "0xc0000234" == tolower(extract("Error Code:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
```

### <a name="sample-query-6"></a>Esempio di query 6
Il numero di eventi accesso account sette giorni da adesso per tutti i tentativi di accesso che si è verificato per tutte le bloccato gli utenti.
```Kusto
AADDomainServicesAccountLogon
| where TimeGenerated >= ago(7d)
| where "0xc0000234" == tolower(extract("Error Code:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
| summarize count()
```

## <a name="related-content"></a>Contenuti correlati
* [Panoramica](https://docs.microsoft.com/azure/kusto/query/) di Kusto il linguaggio di query.
* [Esercitazione di Kusto](https://docs.microsoft.com/azure/kusto/query/tutorial) per acquisire familiarità con nozioni fondamentali sulle query.
* [Query di esempio](https://docs.microsoft.com/azure/kusto/query/samples) che consentono imparare nuove modalità per visualizzare i dati.
* Kusto [procedure consigliate](https://docs.microsoft.com/azure/kusto/query/best-practices) : ottimizzare le query per l'esito positivo.














 
