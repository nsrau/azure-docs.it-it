---
title: Installare gli agenti di Connect Health in Azure Active Directory
description: Questo articolo Azure AD Connect Health descrive l'installazione dell'agente per Active Directory Federation Services (AD FS) e per la sincronizzazione.
services: active-directory
documentationcenter: ''
author: zhiweiwangmsft
manager: daveba
editor: curtand
ms.assetid: 1cc8ae90-607d-4925-9c30-6770a4bd1b4e
ms.service: active-directory
ms.subservice: hybrid
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 10/20/2020
ms.topic: how-to
ms.author: billmath
ms.collection: M365-identity-device-management
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: b680c275b92340cc7efba187769cb17602b08b45
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/25/2020
ms.locfileid: "95973287"
---
# <a name="azure-ad-connect-health-agent-installation"></a>Installazione agente Azure AD Connect Health

Questo articolo illustra come installare e configurare gli agenti di Azure Active Directory (Azure AD) Connect Health. Per scaricare gli agenti, vedere [queste istruzioni](how-to-connect-install-roadmap.md#download-and-install-azure-ad-connect-health-agent).

## <a name="requirements"></a>Requisiti

Nella tabella seguente sono elencati i requisiti per l'utilizzo di Azure AD Connect Health.

| Requisito | Descrizione |
| --- | --- |
| Azure AD Premium è installato. |Azure AD Connect Health è una funzionalità di Azure AD Premium. Per ulteriori informazioni, vedere la pagina [relativa all'iscrizione per Azure ad Premium](../fundamentals/active-directory-get-started-premium.md). <br /><br />Per avviare una versione di valutazione gratuita di 30 giorni, vedere [avviare una versione di valutazione](https://azure.microsoft.com/trial/get-started-active-directory/). |
| Si è un amministratore globale in Azure AD. |Per impostazione predefinita, solo gli amministratori globali possono installare e configurare gli agenti di integrità, accedere al portale ed eseguire qualsiasi operazione all'interno Azure AD Connect Health. Per altre informazioni, vedere [Amministrare la directory di Azure AD](../fundamentals/active-directory-whatis.md). <br /><br /> Usando il controllo degli accessi in base al ruolo di Azure (RBAC di Azure), è possibile consentire ad altri utenti dell'organizzazione di accedere a Azure AD Connect Health. Per ulteriori informazioni, vedere controllo [Azure ad Connect Health degli accessi in base al ruolo di Azure](how-to-connect-health-operations.md#manage-access-with-azure-rbac). <br /><br />**Importante**: usare un account aziendale o dell'Istituto di istruzione per installare gli agenti. Non è possibile usare un account Microsoft. Per altre informazioni, vedere [iscriversi ad Azure come organizzazione](../fundamentals/sign-up-organization.md). |
| L'agente di Azure AD Connect Health viene installato in ogni server di destinazione. | Gli agenti di integrità devono essere installati e configurati nei server di destinazione in modo che possano ricevere dati e fornire funzionalità di monitoraggio e analisi. <br /><br />Ad esempio, per ottenere i dati dall'infrastruttura di Active Directory Federation Services (AD FS), è necessario installare l'agente nel server AD FS e nel server proxy applicazione Web. Analogamente, per ottenere i dati dall'infrastruttura Azure AD Domain Services locale (Azure AD DS), è necessario installare l'agente sui controller di dominio.  |
| Gli endpoint del servizio di Azure hanno connettività in uscita. | Durante l'installazione e la fase di esecuzione, l'agente richiede la connettività agli endpoint di servizio di Azure AD Connect Health. Se i firewall bloccano la connettività in uscita, aggiungere gli [endpoint di connettività in uscita](how-to-connect-health-agent-install.md#outbound-connectivity-to-the-azure-service-endpoints) all'elenco Consenti. |
|La connettività in uscita si basa sugli indirizzi IP. | Per informazioni sul filtro del firewall basato sugli indirizzi IP, vedere [intervalli IP di Azure](https://www.microsoft.com/download/details.aspx?id=41653).|
| L'ispezione TLS per il traffico in uscita è filtrata o disabilitata. | Il passaggio di registrazione dell'agente o le operazioni di caricamento dei dati potrebbero non riuscire se è presente l'ispezione o la terminazione di TLS per il traffico in uscita a livello di rete. Per altre informazioni, vedere [set up TLS Inspection](/previous-versions/tn-archive/ee796230(v=technet.10)). |
| Le porte del firewall nel server eseguono l'agente. |L'agente richiede che le porte del firewall seguenti siano aperte, in modo che sia in grado di comunicare con gli endpoint del servizio Azure AD Connect Health: <br /><li>Porta TCP 443</li><li>Porta TCP 5671</li> <br />La versione più recente dell'agente non richiede la porta 5671. Eseguire l'aggiornamento alla versione più recente in modo che sia necessaria solo la porta 443. Per altre informazioni, vedere [porte e protocolli di identità ibrida](./reference-connect-ports.md). |
| Se è abilitata la sicurezza avanzata di Internet Explorer, consentire i siti Web specificati.  |Se è abilitata la sicurezza avanzata di Internet Explorer, consentire i siti Web seguenti nel server in cui si installa l'agente:<br /><li>https:\//login.microsoftonline.com</li><li>https:\//secure.aadcdn.microsoftonline-p.com</li><li>https:\//login.windows.net</li><li>https: \/ /aadcdn.msftauth.NET</li><li>Server federativo per l'organizzazione considerato attendibile da Azure AD (ad esempio, https: \/ /STS.contoso.com)</li> <br />Per ulteriori informazioni, vedere [come configurare Internet Explorer](https://support.microsoft.com/help/815141/internet-explorer-enhanced-security-configuration-changes-the-browsing). Se si dispone di un proxy nella rete, vedere la nota che viene visualizzata alla fine della tabella.|
| È installato PowerShell versione 4,0 o successiva. | Windows Server 2012 include PowerShell versione 3,0. Questa versione *non* è sufficiente per l'agente.</br></br> Windows Server 2012 R2 e versioni successive includono una versione abbastanza recente di PowerShell.|
|FIPS (Federal Information Processing Standard) è disabilitato.|Gli agenti Azure AD Connect Health non supportano FIPS.|

> [!IMPORTANT]
> Windows Server Core non supporta l'installazione dell'agente di Azure AD Connect Health.


> [!NOTE]
> Se si dispone di un ambiente con blocco elevato e con restrizioni, è necessario aggiungere più URL rispetto a quelli elencati nella tabella per la sicurezza avanzata di Internet Explorer. Aggiungere inoltre URL elencati nella tabella nella sezione successiva.  


### <a name="outbound-connectivity-to-the-azure-service-endpoints"></a>Connettività in uscita agli endpoint di servizio di Azure

Durante l'installazione e il runtime, l'agente richiede la connettività agli endpoint del servizio Azure AD Connect Health. Se i firewall bloccano la connettività in uscita, assicurarsi che gli URL nella tabella seguente non siano bloccati per impostazione predefinita. 

Non disabilitare il monitoraggio della sicurezza o l'ispezione di questi URL. In alternativa, è possibile consentirne l'accesso al traffico Internet. 

Questi URL consentono la comunicazione con gli endpoint del servizio Azure AD Connect Health. Più avanti in questo articolo si apprenderà come [controllare la connettività in uscita](#test-connectivity-to-azure-ad-connect-health-service) usando `Test-AzureADConnectHealthConnectivity` .

| Ambiente di dominio | Endpoint del servizio di Azure richiesti |
| --- | --- |
| Pubblico generale | <li>&#42;.blob.core.windows.net </li><li>&#42;.aadconnecthealth.azure.com </li><li>&#42;. servicebus.windows.net-porta: 5671 (questo endpoint non è necessario nella versione più recente dell'agente).</li><li>&#42;.adhybridhealth.azure.com/</li><li>https:\//management.azure.com </li><li>https:\//policykeyservice.dc.ad.msft.net/</li><li>https:\//login.windows.net</li><li>https:\//login.microsoftonline.com</li><li>https:\//secure.aadcdn.microsoftonline-p.com </li><li>https: \/ /www.Office.com (questo endpoint viene usato solo per scopi di individuazione durante la registrazione).</li> |
| Azure Germania | <li>&#42;.blob.core.cloudapi.de </li><li>&#42;.servicebus.cloudapi.de </li> <li>&#42;.aadconnecthealth.microsoftazure.de </li><li>https:\//management.microsoftazure.de </li><li>https:\//policykeyservice.aadcdi.microsoftazure.de </li><li>https:\//login.microsoftonline.de </li><li>https:\//secure.aadcdn.microsoftonline-p.de </li><li>https: \/ /www.Office.de (questo endpoint viene usato solo per scopi di individuazione durante la registrazione).</li> |
| Azure Government | <li>&#42;.blob.core.usgovcloudapi.net </li> <li>&#42;.servicebus.usgovcloudapi.net </li> <li>&#42;.aadconnecthealth.microsoftazure.us </li> <li>https:\//management.usgovcloudapi.net </li><li>https:\//policykeyservice.aadcdi.azure.us </li><li>https:\//login.microsoftonline.us </li><li>https:\//secure.aadcdn.microsoftonline-p.com </li><li>https: \/ /www.Office.com (questo endpoint viene usato solo per scopi di individuazione durante la registrazione).</li> |


## <a name="install-the-agent"></a>Installare l'agente

Per scaricare e installare l'agente di Azure AD Connect Health: 

* Assicurarsi di soddisfare i [requisiti](how-to-connect-health-agent-install.md#requirements) per Azure ad Connect Health.
* Introduzione all'uso di Azure AD Connect Health per AD FS:
    * [Scaricare l'agente di Azure ad Connect Health per ad FS](https://go.microsoft.com/fwlink/?LinkID=518973).
    * Vedere le [istruzioni di installazione](#install-the-agent-for-ad-fs).
* Introduzione all'uso di Azure AD Connect Health per la sincronizzazione:
    * [Scaricare e installare la versione più recente di Azure AD Connect](https://go.microsoft.com/fwlink/?linkid=615771). L'agente integrità per la sincronizzazione viene installato come parte dell'installazione di Azure AD Connect (versione 1.0.9125.0 o successiva).
* Introduzione all'uso di Azure AD Connect Health per Azure AD DS:
    * [Scaricare l'agente di Azure ad Connect Health per Azure AD DS](https://go.microsoft.com/fwlink/?LinkID=820540).
    * Vedere le [istruzioni di installazione](#install-the-agent-for-azure-ad-ds).

## <a name="install-the-agent-for-ad-fs"></a>Installare l'agente per AD FS

> [!NOTE]
> Il server di AD FS deve essere diverso da quello del server di sincronizzazione. Non installare l'agente di AD FS nel server di sincronizzazione.
>

Prima di installare l'agente, verificare che il nome host del server AD FS sia univoco e non sia presente nel servizio AD FS.
Per avviare l'installazione dell'agente, fare doppio clic sul file *exe* scaricato. Nella prima finestra selezionare **Installa**.

![Screenshot che mostra la finestra di installazione per l'agente di Azure AD Connect Health AD FS.](./media/how-to-connect-health-agent-install/install1.png)

Al termine dell'installazione, selezionare **Configura ora**.

![Screenshot che mostra il messaggio di conferma per l'installazione dell'agente di Azure AD Connect Health AD FS.](./media/how-to-connect-health-agent-install/install2.png)

Viene aperta una finestra di PowerShell per avviare il processo di registrazione dell'agente. Quando viene richiesto, eseguire l'accesso usando un account Azure AD con le autorizzazioni per registrare l'agente. Per impostazione predefinita, l'account amministratore globale dispone delle autorizzazioni.

![Screenshot che illustra la finestra di accesso per Azure AD Connect Health AD FS.](./media/how-to-connect-health-agent-install/install3.png)

Dopo l'accesso, PowerShell continua. Al termine, è possibile chiudere PowerShell. La configurazione è stata completata.

A questo punto, i servizi dell'agente dovrebbero avviarsi automaticamente per consentire all'agente di caricare in modo sicuro i dati necessari nel servizio cloud.

Se non sono stati soddisfatti tutti i prerequisiti, gli avvisi vengono visualizzati nella finestra di PowerShell. Assicurarsi di completare i [requisiti](how-to-connect-health-agent-install.md#requirements) prima di installare l'agente. Lo screenshot seguente mostra un esempio di questi avvisi.

![Screenshot che mostra lo script Azure AD Connect Health AD FS configure.](./media/how-to-connect-health-agent-install/install4.png)

Per verificare che l'agente sia stato installato, cercare i servizi seguenti nel server. Questi servizi dovrebbero essere già in esecuzione se la configurazione è stata completata. In caso contrario, verranno interrotti fino al completamento della configurazione.

* Azure AD Connect Health AD FS Diagnostics Service
* Azure AD Connect Health AD FS Insights Service
* Azure AD Connect Health AD FS Monitoring Service

![Screenshot che illustra Azure AD Connect Health AD FS Services.](./media/how-to-connect-health-agent-install/install5.png)


### <a name="enable-auditing-for-ad-fs"></a>Abilitare il controllo per AD FS

> [!NOTE]
> Questa sezione si applica solo ai server AD FS. Non è necessario attenersi alla procedura seguente nei server proxy applicazione Web.
>

La funzionalità di analisi dell'utilizzo deve raccogliere e analizzare i dati. Quindi, l'agente di Azure AD Connect Health richiede le informazioni nei log di controllo AD FS. Questi log non sono abilitati per impostazione predefinita. Utilizzare le procedure seguenti per abilitare il controllo AD FS e individuare i log di controllo AD FS sui server AD FS.

#### <a name="to-enable-auditing-for-ad-fs-on-windows-server-2012-r2"></a>Per abilitare il controllo per ADFS in Windows Server 2012 R2

1. Nella schermata Start aprire **Server Manager**, quindi aprire **criteri di sicurezza locali**. In alternativa, nella barra delle applicazioni aprire **Server Manager**, quindi selezionare **strumenti/Criteri di sicurezza locali**.
2. Passare alla cartella *Security protezione\Criteri locali\Assegnazione Rights Assignment* . Fare quindi doppio clic su **genera controlli di sicurezza**.
3. Nella scheda **Impostazioni di protezione locali** verificare che sia elencato l'account del servizio ADFS. Se non è elencato, selezionare **Aggiungi utente o gruppo** e aggiungerlo all'elenco. Selezionare **OK**.
4. Per abilitare il controllo, aprire una finestra del prompt dei comandi con privilegi elevati. Quindi, eseguire il comando seguente: 
    
    `auditpol.exe /set /subcategory:{0CCE9222-69AE-11D9-BED3-505054503030} /failure:enable /success:enable`
1. Chiudere **Criteri di sicurezza locali**.
    >[!Important]
    >I passaggi seguenti sono necessari solo per i server di AD FS primario. 
1. Aprire lo snap-in **Gestione AD FS**. (In **Server Manager** selezionare **strumenti**  >  **Gestione ad FS**.)
1. Nel riquadro **azioni** selezionare **modifica proprietà servizio federativo**.
1. Nella finestra di dialogo **proprietà servizio federativo** selezionare la scheda **eventi** .
1. Selezionare le caselle di controllo **Success audits e Failure Audits** , quindi fare clic su **OK**.
1. Per abilitare la registrazione dettagliata tramite PowerShell, usare il comando seguente: 

    `Set-AdfsProperties -LOGLevel Verbose`

#### <a name="to-enable-auditing-for-ad-fs-on-windows-server-2016"></a>Per abilitare il controllo per AD FS in Windows Server 2016

1. Nella schermata Start aprire **Server Manager**, quindi aprire **criteri di sicurezza locali**. In alternativa, nella barra delle applicazioni aprire **Server Manager**, quindi selezionare **strumenti/Criteri di sicurezza locali**.
2. Passare alla cartella *Security protezione\Criteri locali\Assegnazione Rights Assignment* , quindi fare doppio clic su **genera controlli di sicurezza**.
3. Nella scheda **Impostazioni di protezione locali** verificare che sia elencato l'account del servizio ADFS. Se non è elencato, selezionare **Aggiungi utente o gruppo** e aggiungere l'account del servizio ad FS all'elenco. Selezionare **OK**.
4. Per abilitare il controllo, aprire una finestra del prompt dei comandi con privilegi elevati. Quindi, eseguire il comando seguente: 

    `auditpol.exe /set /subcategory:{0CCE9222-69AE-11D9-BED3-505054503030} /failure:enable /success:enable`
1. Chiudere **Criteri di sicurezza locali**.
    >[!Important]
    >I passaggi seguenti sono necessari solo per i server di AD FS primario.
1. Aprire lo snap-in **Gestione AD FS**. (In **Server Manager** selezionare **strumenti**  >  **Gestione ad FS**.)
1. Nel riquadro **azioni** selezionare **modifica proprietà servizio federativo**.
1. Nella finestra di dialogo **proprietà servizio federativo** selezionare la scheda **eventi** .
1. Selezionare le caselle di controllo **Success audits e Failure Audits** , quindi fare clic su **OK**. I controlli di esito positivo e negativo devono essere abilitati per impostazione predefinita.
1. Aprire una finestra di PowerShell ed eseguire il comando seguente: 

    `Set-AdfsProperties -AuditLevel Verbose`

Il livello di controllo "Basic" è abilitato per impostazione predefinita. Per ulteriori informazioni, vedere [ad FS miglioramento del controllo in Windows Server 2016](/windows-server/identity/ad-fs/technical-reference/auditing-enhancements-to-ad-fs-in-windows-server).


#### <a name="to-locate-the-ad-fs-audit-logs"></a>Per individuare i log di controllo di ADFS

1. Aprire **Visualizzatore eventi**.
2. Passare a **registri di Windows** e quindi selezionare **sicurezza**.
3. A destra selezionare **filtro registri correnti**.
4. Per **origini evento** selezionare **ad FS controllo**.

    Per ulteriori informazioni sui log di controllo, vedere [domande sulle operazioni](reference-connect-health-faq.md#operations-questions).

    ![Screenshot che mostra la finestra del log corrente del filtro. Nel campo "origini eventi" è selezionato "controllo AD FS".](./media/how-to-connect-health-agent-install/adfsaudit.png)

> [!WARNING]
> Un oggetto Criteri di gruppo può disabilitare il controllo di AD FS. Se AD FS controllo è disabilitato, le analisi di utilizzo relative alle attività di accesso non sono disponibili. Assicurarsi che non siano presenti criteri di gruppo che disabilitano il controllo AD FS.
>


## <a name="install-the-agent-for-sync"></a>Installare l'agente per la sincronizzazione

L'agente di Azure AD Connect Health per la sincronizzazione viene installato automaticamente nella versione più recente di Azure AD Connect. Per usare Azure AD Connect per la sincronizzazione, [scaricare la versione più recente di Azure ad Connect](https://www.microsoft.com/download/details.aspx?id=47594) e installarla.

Per verificare che l'agente sia stato installato, cercare i servizi seguenti nel server. Se è stata completata la configurazione, i servizi devono essere già in esecuzione. In caso contrario, i servizi vengono arrestati fino al completamento della configurazione.

* Azure AD Connect Health Sync Insights Service
* Azure AD Connect Health Sync Monitoring Service

![Screenshot che mostra la Azure AD Connect Health in esecuzione per i servizi di sincronizzazione nel server.](./media/how-to-connect-health-agent-install/services.png)

> [!NOTE]
> Tenere presente che è necessario avere Azure AD Premium per usare Azure AD Connect Health. Se non si dispone di Azure AD Premium, non è possibile completare la configurazione nella portale di Azure. Per ulteriori informazioni, vedere i [requisiti](how-to-connect-health-agent-install.md#requirements).
>
>

## <a name="manually-register-azure-ad-connect-health-for-sync"></a>Registrare manualmente Azure AD Connect Health per la sincronizzazione

Se la Azure AD Connect Health per la registrazione dell'agente di sincronizzazione non riesce dopo aver installato correttamente Azure AD Connect, è possibile usare un comando di PowerShell per registrare manualmente l'agente.

> [!IMPORTANT]
> Usare questo comando di PowerShell solo se la registrazione dell'agente non riesce dopo l'installazione di Azure AD Connect.
>
>

Registrare manualmente l'agente di Azure AD Connect Health per la sincronizzazione usando il comando di PowerShell seguente. I servizi di Azure AD Connect Health verranno avviati dopo la corretta registrazione dell'agente.

`Register-AzureADConnectHealthSyncAgent -AttributeFiltering $false -StagingMode $false`

Il comando accetta i parametri seguenti:

* **AttributeFiltering**: `$true` (impostazione predefinita) se Azure ad Connect non esegue la sincronizzazione del set di attributi predefinito ed è stato personalizzato per l'utilizzo di un set di attributi filtrati. In caso contrario, usare `$false`.
* **StagingMode**: `$false` (impostazione predefinita) se il server Azure ad Connect *non* è in modalità di gestione temporanea. Se il server è configurato per essere in modalità di staging, utilizzare `$true` .

Quando viene richiesta l'autenticazione, usare lo stesso account amministratore globale (ad esempio admin@domain.onmicrosoft.com ) usato per configurare Azure ad Connect.

## <a name="install-the-agent-for-azure-ad-ds"></a>Installare l'agente per Azure AD DS

Per avviare l'installazione dell'agente, fare doppio clic sul file *exe* scaricato. Nella prima finestra selezionare **Installa**.

![Screenshot che mostra la finestra di installazione di Azure AD Connect Health Agent per servizi di dominio Active Directory.](./media/how-to-connect-health-agent-install/aadconnect-health-adds-agent-install1.png)

Al termine dell'installazione, selezionare **Configura ora**.

![Screenshot che mostra la finestra che completa l'installazione dell'agente di Azure AD Connect Health per Azure AD DS.](./media/how-to-connect-health-agent-install/aadconnect-health-adds-agent-install2.png)

Verrà visualizzata una finestra del prompt dei comandi. PowerShell viene eseguito `Register-AzureADConnectHealthADDSAgent` . Quando viene richiesto, accedere ad Azure.

![Screenshot che illustra la finestra di accesso per l'agente di Azure AD Connect Health per Azure AD DS.](./media/how-to-connect-health-agent-install/aadconnect-health-adds-agent-install3.png)

Dopo l'accesso, PowerShell continua. Al termine, è possibile chiudere PowerShell. La configurazione è stata completata.

A questo punto, i servizi devono essere avviati automaticamente, consentendo all'agente di monitorare e raccogliere i dati. Se non sono stati soddisfatti tutti i prerequisiti descritti nelle sezioni precedenti, gli avvisi vengono visualizzati nella finestra di PowerShell. Assicurarsi di completare i [requisiti](how-to-connect-health-agent-install.md#requirements) prima di installare l'agente. Lo screenshot seguente mostra un esempio di questi avvisi.

![Screenshot che mostra un avviso per l'agente di Azure AD Connect Health per la configurazione di Azure AD DS.](./media/how-to-connect-health-agent-install/aadconnect-health-adds-agent-install4.png)

Per verificare che l'agente sia installato, cercare i servizi seguenti nel controller di dominio:

* Azure AD Connect Health AD DS Insights Service
* Azure AD Connect Health AD DS Monitoring Service

Se la configurazione è stata completata, questi servizi dovrebbero già essere in esecuzione. In caso contrario, verranno interrotti fino al termine della configurazione.

![Screenshot che mostra i servizi in esecuzione sul controller di dominio.](./media/how-to-connect-health-agent-install/aadconnect-health-adds-agent-install5.png)

### <a name="quickly-install-the-agent-on-multiple-servers"></a>Installare rapidamente l'agente su più server

1. Creare un account utente in Azure AD. Proteggerlo tramite una password.
2. Assegnare il ruolo **proprietario** per l'account di Azure ad locale in Azure ad Connect Health usando il portale. Attenersi alla [seguente procedura](how-to-connect-health-operations.md#manage-access-with-azure-rbac). Assegnare il ruolo a tutte le istanze del servizio. 
3. Scaricare il file MSI *exe* nel controller di dominio locale per l'installazione.
4. Eseguire lo script seguente. Sostituire i parametri con il nuovo account utente e la relativa password. 

    ```powershell
    AdHealthAddsAgentSetup.exe /quiet
    Start-Sleep 30
    $userName = "NEWUSER@DOMAIN"
    $secpasswd = ConvertTo-SecureString "PASSWORD" -AsPlainText -Force
    $myCreds = New-Object System.Management.Automation.PSCredential ($userName, $secpasswd)
    import-module "C:\Program Files\Azure Ad Connect Health Adds Agent\PowerShell\AdHealthAdds"
     
    Register-AzureADConnectHealthADDSAgent -Credential $myCreds
    
    ```

Al termine, è possibile rimuovere l'accesso per l'account locale eseguendo una o più delle attività seguenti: 
* Rimuovere l'assegnazione di ruolo per l'account locale per Azure AD Connect Health.
* Ruotare la password per l'account locale. 
* Disabilitare l'account Azure AD locale.
* Eliminare il Azure AD account locale.  

## <a name="register-the-agent-by-using-powershell"></a>Registrare l'agente usando PowerShell

Dopo aver installato il file di *setup.exe* agente appropriato, è possibile registrare l'agente usando i comandi di PowerShell seguenti, a seconda del ruolo. Aprire una finestra di PowerShell ed eseguire il comando appropriato:

```powershell
    Register-AzureADConnectHealthADFSAgent
    Register-AzureADConnectHealthADDSAgent
    Register-AzureADConnectHealthSyncAgent

```

Questi comandi accettano `Credential` come parametro per completare la registrazione in modo non interattivo o per completare la registrazione in un computer che esegue Server Core. Occorre ricordare che:
* È possibile acquisire `Credential` in una variabile di PowerShell passata come parametro.
* È possibile specificare qualsiasi identità di Azure AD con le autorizzazioni per registrare gli agenti e per cui *non* è abilitata l'autenticazione a più fattori.
* Per impostazione predefinita, gli amministratori globali dispongono delle autorizzazioni per registrare gli agenti. È anche possibile consentire alle identità con privilegi di minore entità di eseguire questo passaggio. Per altre informazioni [, vedere controllo](how-to-connect-health-operations.md#manage-access-with-azure-rbac)degli accessi in base al ruolo.

```powershell
    $cred = Get-Credential
    Register-AzureADConnectHealthADFSAgent -Credential $cred

```

## <a name="configure-azure-ad-connect-health-agents-to-use-http-proxy"></a>Configurare gli agenti Azure AD Connect Health per l'uso del proxy HTTP

È possibile configurare Azure AD Connect Health agenti in modo che funzionino con un proxy HTTP.

> [!NOTE]
> * `Netsh WinHttp set ProxyServerAddress` non è supportato. L'agente usa System.Net anziché i servizi HTTP di Windows per eseguire le richieste Web.
> * L'indirizzo proxy HTTP configurato viene usato per passare i messaggi HTTPS crittografati.
> * I proxy autenticati, che usano HTTPBasic, non sono supportati.
>
>

### <a name="change-the-agent-proxy-configuration"></a>Modificare la configurazione del proxy dell'agente

Per configurare l'agente di Azure AD Connect Health per l'utilizzo di un proxy HTTP, è possibile:
* Importa le impostazioni proxy esistenti.
* Specificare manualmente gli indirizzi proxy.
* Cancellare la configurazione del proxy esistente.

> [!NOTE]
> Per aggiornare le impostazioni proxy, è necessario riavviare tutti i servizi di Azure AD Connect Health Agent. Eseguire il comando seguente:
>
> `Restart-Service AzureADConnectHealth*`

#### <a name="import-existing-proxy-settings"></a>Importa impostazioni proxy esistenti

È possibile importare le impostazioni del proxy HTTP di Internet Explorer in modo che gli agenti Azure AD Connect Health possano utilizzare le impostazioni. In ogni server che esegue l'agente per l'integrità, eseguire il comando PowerShell seguente:

```powershell
Set-AzureAdConnectHealthProxySettings -ImportFromInternetSettings
```

È possibile importare le impostazioni proxy WinHTTP in modo che possano essere usate dagli agenti Azure AD Connect Health. In ogni server che esegue l'agente per l'integrità, eseguire il comando PowerShell seguente:

```powershell
Set-AzureAdConnectHealthProxySettings -ImportFromWinHttp
```

#### <a name="specify-proxy-addresses-manually"></a>Specificare manualmente gli indirizzi proxy

È possibile specificare manualmente un server proxy. In ogni server che esegue l'agente per l'integrità, eseguire il comando PowerShell seguente:

```powershell
Set-AzureAdConnectHealthProxySettings -HttpsProxyAddress address:port
```

Ecco un esempio: 

`Set-AzureAdConnectHealthProxySettings -HttpsProxyAddress myproxyserver: 443`

Esempio:
* L' `address` impostazione può essere un nome di server risolvibile da DNS o un indirizzo IPv4.
* È possibile omettere `port` . In tal caso, 443 è la porta predefinita.

#### <a name="clear-the-existing-proxy-configuration"></a>Cancellare la configurazione del proxy esistente

È possibile eseguire il comando seguente per cancellare la configurazione del proxy esistente:

```powershell
Set-AzureAdConnectHealthProxySettings -NoProxy
```

### <a name="read-current-proxy-settings"></a>Leggere le impostazioni del proxy correnti

È possibile leggere le impostazioni del proxy corrente eseguendo il comando seguente:

```powershell
Get-AzureAdConnectHealthProxySettings
```

## <a name="test-connectivity-to-azure-ad-connect-health-service"></a>Testare la connettività al servizio Azure AD Connect Health

Occasionalmente, l'agente di Azure AD Connect Health può perdere la connettività con il servizio Azure AD Connect Health. Le cause di questa perdita di connettività possono includere problemi di rete, problemi di autorizzazione e diversi altri problemi.

Se l'agente non è in grado di inviare dati al servizio Azure AD Connect Health per più di due ore, nel portale viene visualizzato l'avviso seguente: "i dati Servizio integrità non sono aggiornati". 

È possibile verificare se l'agente Azure AD Connect Health interessato può caricare i dati nel servizio Azure AD Connect Health eseguendo il comando PowerShell seguente:

```powershell
Test-AzureADConnectHealthConnectivity -Role ADFS
```

Il parametro role accetta attualmente i valori seguenti:

* AD FS
* Sincronizza
* ADDS

> [!NOTE]
> Per utilizzare lo strumento di connettività, è necessario innanzitutto registrare l'agente. Se non è possibile completare la registrazione dell'agente, assicurarsi di aver soddisfatto tutti i [requisiti](how-to-connect-health-agent-install.md#requirements) per Azure ad Connect Health. La connettività viene verificata per impostazione predefinita durante la registrazione dell'agente.
>
>

## <a name="next-steps"></a>Passaggi successivi

Vedere gli articoli correlati seguenti:

* [Azure AD Connect Health](./whatis-azure-ad-connect.md)
* [Operazioni di Azure AD Connect Health](how-to-connect-health-operations.md)
* [Uso di Azure AD Connect Health con AD FS](how-to-connect-health-adfs.md)
* [Uso di Azure AD Connect Health per la sincronizzazione](how-to-connect-health-sync.md)
* [Uso di Azure AD Connect Health con Azure AD DS](how-to-connect-health-adds.md)
* [Domande frequenti su Azure AD Connect Health](reference-connect-health-faq.md)
* [Cronologia delle versioni di Azure AD Connect Health](reference-connect-health-version-history.md)
