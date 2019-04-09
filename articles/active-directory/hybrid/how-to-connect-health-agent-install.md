---
title: Installazione dell'agente di Azure AD Connect Health | Documentazione Microsoft
description: Si tratta della pagina di Azure AD Connect Health che descrive l'installazione dell'agente per ADFS e la sincronizzazione.
services: active-directory
documentationcenter: ''
author: zhiweiwangmsft
manager: daveba
editor: curtand
ms.assetid: 1cc8ae90-607d-4925-9c30-6770a4bd1b4e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/18/2017
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6648aec8741a748dd4150406831035a68b97af7c
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2019
ms.locfileid: "59268466"
---
# <a name="azure-ad-connect-health-agent-installation"></a>Installazione dell'agente di Azure AD Connect Health

Questo documento illustra le procedure per installare e configurare l'agente di Azure AD Connect Health. Gli agenti sono disponibili per il download in [questa pagina](how-to-connect-install-roadmap.md#download-and-install-azure-ad-connect-health-agent).

## <a name="requirements"></a>Requisiti

La tabella seguente è un elenco di requisiti per l'uso di Azure AD Connect Health.

| Requisito | DESCRIZIONE |
| --- | --- |
| Azure AD Premium |Azure AD Connect Health è una funzionalità di Azure AD Premium e richiede una licenza di Azure AD Premium. <br /><br />Per altre informazioni, vedere [Introduzione ad Azure Active Directory Premium](../fundamentals/active-directory-get-started-premium.md). <br />Per ottenere una versione di valutazione gratuita valida 30 giorni, vedere la pagina relativa all'[avvio di una versione di valutazione.](https://azure.microsoft.com/trial/get-started-active-directory/) |
| Per iniziare a usare Azure AD Connect Health, è necessario essere un amministratore globale dell'istanza di Azure AD. |Per impostazione predefinita, solo gli amministratori globali possono installare e configurare gli agenti per l'integrità per eseguire le operazioni iniziali, accedere al portale ed eseguire qualsiasi operazione in Azure AD Connect Health. Per altre informazioni, vedere [Amministrare la directory di Azure AD](../fundamentals/active-directory-administer.md). <br /><br /> Con il controllo degli accessi in base al ruolo è possibile consentire l'accesso ad Azure AD Connect Health ad altri utenti dell'organizzazione. Per altre informazioni, vedere l'articolo relativo al [controllo degli accessi in base al ruolo per Azure AD Connect Health.](how-to-connect-health-operations.md#manage-access-with-role-based-access-control) <br /><br />**Importante:** l'account usato per l'installazione degli agenti deve essere un account aziendale o dell'istituto di istruzione. Non può essere un account Microsoft. Per altre informazioni, vedere [Iscriversi ad Azure come organizzazione](../fundamentals/sign-up-organization.md). |
| L'agente di Azure AD Connect Health è installato in ogni server di destinazione | Azure AD Connect Health richiede l'installazione e la configurazione degli agenti per l'integrità nei server di destinazione per ricevere i dati e fornire le funzionalità di monitoraggio e di analisi. <br /><br />Per ottenere i dati sull'infrastruttura di AD FS, ad esempio, l'agente deve essere installato nei server AD FS e nei server proxy applicazione Web. Analogamente, per poter ottenere i dati sull'infrastruttura locale di Servizi di dominio Active Directory, l'agente deve essere installato nei controller di dominio. <br /><br /> |
| Connettività in uscita agli endpoint di servizio di Azure | Durante l'installazione e la fase di esecuzione, l'agente richiede la connettività agli endpoint di servizio di Azure AD Connect Health. Se la connettività in uscita è bloccata tramite firewall, verificare che gli endpoint seguenti siano aggiunti all'elenco elementi consentiti. Vedere [Endpoint di connettività in uscita](how-to-connect-health-agent-install.md#outbound-connectivity-to-the-azure-service-endpoints) |
|Connettività in uscita in base agli indirizzi IP | Per informazioni sui filtri basati su indirizzo IP nei firewall, vedere [Intervalli di indirizzi IP di Azure](https://www.microsoft.com/download/details.aspx?id=41653).|
| L'analisi SSL per il traffico in uscita è filtrata o disabilitata | Il passaggio di registrazione dell'agente o le operazioni di caricamento dei dati possono avere esito negativo in caso di analisi SSL o di interruzione per il traffico in uscita a livello di rete. Vedere altre informazioni sulla [configurazione dell'ispezione SSL](https://technet.microsoft.com/library/ee796230.aspx) |
| Porte del firewall nel server che esegue l'agente |Per consentire la comunicazione dell'agente con gli endpoint di servizio Azure AD Connect Health è necessario che le porte del firewall seguenti siano aperte.<br /><br /><li>Porta TCP 443</li><li>Porta TCP 5671</li> <br />Si noti che la porta 5671 non è più necessaria per la versione più recente dell'agente. Eseguire l'aggiornamento alla versione più recente, in modo che sia necessaria solo la porta 443. Vedere altre informazioni sull'[abilitazione delle porte del firewall](https://technet.microsoft.com/library/ms345310(v=sql.100).aspx) |
| Consentire i siti Web seguenti se è abilitata la funzionalità Protezione avanzata di IE |Se la funzionalità Sicurezza avanzata di Internet Explorer è abilitata, nel server in cui verrà installato l'agente devono essere consentiti i siti Web seguenti.<br /><br /><li>https:\//login.microsoftonline.com</li><li>https:\//secure.aadcdn.microsoftonline-p.com</li><li>https:\//login.windows.net</li><li>Server federativo dell'organizzazione considerato attendibile da Azure Active Directory. Ad esempio: https:\//sts.contoso.com</li> Vedere altre informazioni sulla [configurazione di IE](https://support.microsoft.com/help/815141/internet-explorer-enhanced-security-configuration-changes-the-browsing) |
| Assicurarsi che sia installato PowerShell v4.0 o versione successiva | <li>Windows Server 2008 R2 include PowerShell v2.0, che non è sufficiente per l'agente. Aggiornare PowerShell come illustrato di seguito in [Installazione dell'agente in server Windows Server 2008 R2](#agent-installation-on-windows-server-2008-r2-servers).</li><li>Windows Server 2012 include PowerShell v3.0, che non è sufficiente per l'agente.  [Aggiornare](https://www.microsoft.com/download/details.aspx?id=40855) Windows Management Framework.</li><li>Windows Server 2012 R2 e versioni successive includono una versione sufficientemente recente di PowerShell.</li>|
|Disabilitare FIPS|La funzionalità FIPS non è supportata dagli agenti di Azure AD Connect Health.|

### <a name="outbound-connectivity-to-the-azure-service-endpoints"></a>Connettività in uscita agli endpoint di servizio di Azure

 Durante l'installazione e la fase di esecuzione, l'agente richiede la connettività agli endpoint di servizio di Azure AD Connect Health. Se la connettività in uscita è bloccata tramite firewall, verificare che gli URL seguenti non siano bloccati per impostazione predefinita. Non disabilitare il monitoraggio della sicurezza o l'ispezione degli URL, ma consentire tali URL come si farebbe con il resto del traffico Internet. Essi consentono la comunicazione con gli endpoint di servizio di Azure AD Connect Health. Vedere altre informazioni sulla [verifica della connettività in uscita](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections)

| Ambiente di dominio | Endpoint del servizio di Azure richiesti |
| --- | --- |
| Pubblico generale | <li>&#42;.blob.core.windows.net </li><li>&#42;.aadconnecthealth.azure.com </li><li>&#42;.servicebus.windows.net - Porta: 5671 </li><li>&#42;.adhybridhealth.azure.com/</li><li>https:\//management.azure.com </li><li>https:\//policykeyservice.dc.ad.msft.net/</li><li>https:\//login.windows.net</li><li>https:\//login.microsoftonline.com</li><li>https:\//secure.aadcdn.microsoftonline-p.com </li><li>https:\//www.office.com (questo endpoint viene usato solo per scopi di individuazione durante la registrazione).</li> |
| Azure Germania | <li>&#42;.blob.core.cloudapi.de </li><li>&#42;.servicebus.cloudapi.de </li> <li>&#42;.aadconnecthealth.microsoftazure.de </li><li>https:\//management.microsoftazure.de </li><li>https:\//policykeyservice.aadcdi.microsoftazure.de </li><li>https:\//login.microsoftonline.de </li><li>https:\//secure.aadcdn.microsoftonline-p.de </li><li>https:\//www.office.de (questo endpoint viene usato solo per scopi di individuazione durante la registrazione).</li> |
| Azure Government | <li>&#42;.blob.core.usgovcloudapi.net </li> <li>&#42;.servicebus.usgovcloudapi.net </li> <li>&#42;.aadconnecthealth.microsoftazure.us </li> <li>https:\//management.usgovcloudapi.net </li><li>https:\//policykeyservice.aadcdi.azure.us </li><li>https:\//login.microsoftonline.us </li><li>https:\//secure.aadcdn.microsoftonline-p.com </li><li>https:\//www.office.com (questo endpoint viene usato solo per scopi di individuazione durante la registrazione).</li> |


## <a name="download-and-install-the-azure-ad-connect-health-agent"></a>Scaricare e installare l'agente di Azure AD Connect Health

* Verificare che siano [soddisfatti i requisiti](how-to-connect-health-agent-install.md#requirements) per Azure AD Connect Health.
* Introduzione ad Azure AD Connect Health per AD FS
    * [Scaricare Azure AD agente di Connect Health per AD FS.](https://go.microsoft.com/fwlink/?LinkID=518973)
    * [Vedere le istruzioni per l'installazione](#installing-the-azure-ad-connect-health-agent-for-ad-fs).
* Introduzione ad Azure AD Connect Health per la sincronizzazione
    * [Scaricare e installare la versione più recente di Azure AD Connect](https://go.microsoft.com/fwlink/?linkid=615771). L'agente di integrità per la sincronizzazione verrà installato come parte dell'installazione di Azure AD Connect (versione 1.0.9125.0 o versione successiva).
* Introduzione ad Azure AD Connect Health per Active Directory Domain Services
    * [Scaricare l'agente di Azure AD Connect Health per Active Directory Domain Services](https://go.microsoft.com/fwlink/?LinkID=820540).
    * [Vedere le istruzioni per l'installazione](#installing-the-azure-ad-connect-health-agent-for-ad-ds).

## <a name="installing-the-azure-ad-connect-health-agent-for-ad-fs"></a>Installare l'agente di Azure AD Connect Health per AD FS

> [!NOTE]
> Il server AD FS deve essere diverso dal server di sincronizzazione. Non installare l'agente AD FS nel server di sincronizzazione.
>

Prima dell'installazione, assicurarsi che il nome host del server AD FS sia univoco e che non sia presente nel servizio AD FS.
Per avviare l'installazione dell'agente, fare doppio clic sul file EXE scaricato. Nella prima schermata fare clic su Installa.

![Verificare Azure AD Connect Health](./media/how-to-connect-health-agent-install/install1.png)

Una volta completata l'installazione, fare clic su Configura ora.

![Verificare Azure AD Connect Health](./media/how-to-connect-health-agent-install/install2.png)

Viene visualizzata una finestra di PowerShell per l'avvio del processo di registrazione dell'agente. Quando richiesto, accedere con un account Azure AD autorizzato ad eseguire la registrazione dell'agente. Per impostazione predefinita, l'account Amministratore globale ha le autorizzazioni necessarie.

![Verificare Azure AD Connect Health](./media/how-to-connect-health-agent-install/install3.png)

Dopo l'accesso, PowerShell continuerà. Al termine, è possibile chiudere PowerShell e la configurazione sarà completa.

A questo punto, i servizi dell'agente vengono avviati automaticamente, consentendo all'agente di caricare i dati necessari nel servizio cloud in modo sicuro.

Se non sono stati soddisfatti tutti i prerequisiti descritti nelle sezioni precedenti, vengono visualizzati avvisi nella finestra di PowerShell. Assicurarsi di completare i [requisiti](how-to-connect-health-agent-install.md#requirements) prima di installare l'agente. La schermata seguente mostra un esempio di tali errori.

![Verificare Azure AD Connect Health](./media/how-to-connect-health-agent-install/install4.png)

Per verificare che l'agente sia stato installato, cercare i servizi seguenti nel server. Questi servizi dovrebbero essere già in esecuzione se la configurazione è stata completata. In caso contrario, vengono arrestati fino al completamento della configurazione.

* Azure AD Connect Health AD FS Diagnostics Service
* Azure AD Connect Health AD FS Insights Service
* Azure AD Connect Health AD FS Monitoring Service

![Verificare Azure AD Connect Health](./media/how-to-connect-health-agent-install/install5.png)

### <a name="agent-installation-on-windows-server-2008-r2-servers"></a>Installazione dell'agente in server Windows Server 2008 R2

Procedura per server Windows Server 2008 R2:

1. Assicurarsi che il server venga eseguito con il Service Pack 1 o versione successiva.
2. Disattivare la funzionalità Protezione avanzata di IE per l'installazione dell'agente:
3. Installare Windows PowerShell 4.0 in ogni server prima di installare l'agente per l'integrità AD. Per installare Windows PowerShell 4.0:
   * Installare [Microsoft .NET Framework 4.5](https://www.microsoft.com/download/details.aspx?id=40779) usando il collegamento seguente per scaricare il programma di installazione offline.
   * Installare PowerShell ISE (da Funzionalità Windows)
   * Installare [Windows Management Framework 4.0.](https://www.microsoft.com/download/details.aspx?id=40855)
   * Installare Internet Explorer versione 10 o successiva nel server. Questa operazione è necessaria perché il Servizio integrità possa eseguire l'autenticazione usando le credenziali di amministratore di Azure.
4. Per altre informazioni sull'installazione di Windows PowerShell 4.0 in Windows Server 2008 R2, vedere l'articolo wiki [qui](https://social.technet.microsoft.com/wiki/contents/articles/20623.step-by-step-upgrading-the-powershell-version-4-on-2008-r2.aspx).

### <a name="enable-auditing-for-ad-fs"></a>Abilitare il controllo per AD FS

> [!NOTE]
> Questa sezione si applica solo ai server AD FS. Non è necessario eseguire questa procedura nei server del proxy dell'applicazione Web.
>

Per poter usare questa funzionalità per raccogliere dati e analizzarli, l'agente di Azure AD Connect Health deve poter accedere alle informazioni presenti nei log di controllo di AD FS. Questi log non sono abilitati per impostazione predefinita. Usare le procedure seguenti per abilitare il controllo di AD FS e individuare i log di controllo di AD FS nei server AD FS.

#### <a name="to-enable-auditing-for-ad-fs-on-windows-server-2008-r2"></a>Per abilitare il controllo per AD FS in Windows Server 2008 R2

1. Fare clic su **Start**, scegliere **Programmi** e quindi **Strumenti di amministrazione** e infine fare clic su **Criteri di sicurezza locali**.
2. Passare alla cartella **Impostazioni sicurezza\Criteri locali\Assegnazione diritti utente** e quindi fare doppio clic su **Generazione di controlli di sicurezza**.
3. Nella scheda **Impostazioni di sicurezza locali** verificare che sia elencato l'account del servizio AD FS 2.0. Se l'account non è presente, fare clic su **Aggiungi utente o gruppo** e aggiungerlo all'elenco, quindi fare clic su **OK**.
4. Per abilitare il controllo, aprire un prompt dei comandi con privilegi elevati ed eseguire il comando seguente: <code>auditpol.exe /set /subcategory:{0CCE9222-69AE-11D9-BED3-505054503030} /failure:enable /success:enable</code>
5. Chiudere **Criteri di sicurezza locali**.
<br />   -- **La procedura seguente è necessaria solo per i server AD FS primari.** -- <br />
6. Aprire lo snap-in **Gestione AD FS**. Per aprire lo snap-in Gestione AD FS, fare clic su **Start**, scegliere **Programmi**, **Strumenti di amministrazione** e quindi fare clic su **Gestione AD FS 2.0**.
7. Nel riquadro **Azioni** fare clic su **Modifica proprietà servizio federativo**.
8. Nella finestra di dialogo **Proprietà servizio federativo** fare clic sulla scheda **Eventi**.
9. Selezionare le caselle di controllo **Operazioni riuscite** e **Operazioni non riuscite**.
10. Fare clic su **OK**.

#### <a name="to-enable-auditing-for-ad-fs-on-windows-server-2012-r2"></a>Per abilitare il controllo per ADFS in Windows Server 2012 R2

1. Per passare a **Criteri di sicurezza locali**, aprire **Server Manager** nella schermata Start oppure Server Manager nella barra delle applicazioni sul desktop e quindi fare clic su **Strumenti/Criteri di sicurezza locali**.
2. Passare alla cartella **Impostazioni sicurezza\Criteri locali\Assegnazione diritti utente** e quindi fare doppio clic su **Generazione di controlli di sicurezza**.
3. Nella scheda **Impostazioni sicurezza locale** verificare che sia elencato l'account del servizio AD FS. Se l'account non è presente, fare clic su **Aggiungi utente o gruppo** e aggiungerlo all'elenco, quindi fare clic su **OK**.
4. Per abilitare il controllo, aprire un prompt dei comandi con privilegi elevati ed eseguire il comando seguente: ```auditpol.exe /set /subcategory:{0CCE9222-69AE-11D9-BED3-505054503030} /failure:enable /success:enable```
5. Chiudere **Criteri di sicurezza locali**.
<br />   -- **La procedura seguente è necessaria solo per i server AD FS primari.** -- <br />
6. Aprire lo snap-in **Gestione AD FS** facendo clic su Strumenti in Server Manager e quindi selezionando Gestione AD FS.
7. Nel riquadro **Azioni** fare clic su **Modifica proprietà servizio federativo**.
8. Nella finestra di dialogo **Proprietà servizio federativo** fare clic sulla scheda **Eventi**.
9. Selezionare le caselle di controllo **Operazioni riuscite** e Operazioni non riuscite e quindi fare clic su **OK**.

#### <a name="to-enable-auditing-for-ad-fs-on-windows-server-2016"></a>Per abilitare il controllo per AD FS in Windows Server 2016

1. Per passare a **Criteri di sicurezza locali**, aprire **Server Manager** nella schermata Start oppure Server Manager nella barra delle applicazioni sul desktop e quindi fare clic su **Strumenti/Criteri di sicurezza locali**.
2. Passare alla cartella **Impostazioni sicurezza\Criteri locali\Assegnazione diritti utente** e quindi fare doppio clic su **Generazione di controlli di sicurezza**.
3. Nella scheda **Impostazioni sicurezza locale** verificare che sia elencato l'account del servizio AD FS. Se l'account non è presente, fare clic su **Aggiungi utente o gruppo**, quindi aggiungere l'account del servizio AD FS all'elenco e fare clic su **OK**.
4. Per abilitare il controllo, aprire un prompt dei comandi con privilegi elevati ed eseguire il comando seguente: <code>auditpol.exe /set /subcategory:{0CCE9222-69AE-11D9-BED3-505054503030} /failure:enable /success:enable</code>
5. Chiudere **Criteri di sicurezza locali**.
<br />   -- **La procedura seguente è necessaria solo per i server AD FS primari.** -- <br />
6. Aprire lo snap-in **Gestione AD FS** facendo clic su Strumenti in Server Manager e quindi selezionando Gestione AD FS.
7. Nel riquadro **Azioni** fare clic su **Modifica proprietà servizio federativo**.
8. Nella finestra di dialogo **Proprietà servizio federativo** fare clic sulla scheda **Eventi**.
9. Selezionare le caselle di controllo **Operazioni riuscite** e Operazioni non riuscite e quindi fare clic su **OK**. Questa opzione dovrebbe essere abilitata per impostazione predefinita.
10. Aprire una finestra di PowerShell ed eseguire il comando seguente: ```Set-AdfsProperties -AuditLevel Verbose```.

Si noti che il livello di controllo "di base" è abilitato per impostazione predefinita. Per altre informazioni, vedere [Miglioramenti del controllo AD FS in Windows Server 2016](https://docs.microsoft.com/windows-server/identity/ad-fs/technical-reference/auditing-enhancements-to-ad-fs-in-windows-server)


#### <a name="to-locate-the-ad-fs-audit-logs"></a>Per individuare i log di controllo di ADFS

1. Aprire il **Visualizzatore eventi**.
2. Passare a Registri di Windows e selezionare **Sicurezza**.
3. A destra fare clic su **Filtra registri correnti**.
4. In Origine evento selezionare **Controllo di ADFS**.

    Vedere la [nota nelle domande frequenti](reference-connect-health-faq.md#operations-questions) sui log di controllo.

![Log di controllo di ADFS](./media/how-to-connect-health-agent-install/adfsaudit.png)

> [!WARNING]
> Un oggetto Criteri di gruppo può disabilitare il controllo di AD FS. Se il controllo di AD FS è disabilitato, non sono disponibili i dati di analisi di utilizzo relativi alle attività di accesso. Assicurarsi che non siano presenti criteri di gruppo che disabilitano il controllo di AD FS.
>


## <a name="installing-the-azure-ad-connect-health-agent-for-sync"></a>Installare l'agente di Azure AD Connect Health per la sincronizzazione

L'Agente di integrità di Azure AD Connect per la sincronizzazione è installato automaticamente nella build più recente di Azure AD Connect. Per usare Azure AD Connect Health per la sincronizzazione, è necessario scaricare la versione più recente di Azure AD Connect e installarla. È possibile scaricare la versione più recente [qui](https://www.microsoft.com/download/details.aspx?id=47594).

Per verificare che l'agente sia stato installato, cercare i servizi seguenti nel server. Questi servizi dovrebbero essere già in esecuzione se la configurazione è stata completata. In caso contrario, vengono arrestati fino al completamento della configurazione.

* Azure AD Connect Health Sync Insights Service
* Azure AD Connect Health Sync Monitoring Service

![Verificare Azure AD Connect Health per la sincronizzazione](./media/how-to-connect-health-agent-install/services.png)

> [!NOTE]
> Si tenga presente che l'utilizzo di integrità di Azure AD Connect richiede Azure AD Premium. Se non si ha Azure AD Premium, non è possibile completare la configurazione nel portale di Azure. Per altre informazioni, vedere la sezione relativa ai [requisiti](how-to-connect-health-agent-install.md#requirements).
>
>

## <a name="manual-azure-ad-connect-health-for-sync-registration"></a>Registrazione manuale di Azure AD Connect Health per la sincronizzazione

Se la registrazione dell'agente Azure AD Connect Health per la sincronizzazione non riesce pur dopo avere installato correttamente Azure AD Connect, è possibile usare il comando di PowerShell seguente per registrare manualmente l'agente.

> [!IMPORTANT]
> È necessario usare questo comando di PowerShell solo se la registrazione dell'agente non riesce dopo avere installato Azure AD Connect.
>
>

Il comando di PowerShell seguente è necessario SOLO quando la registrazione dell'agente per l'integrità non riesce anche se l'installazione e la configurazione di Azure AD Connect sono state eseguite correttamente. I servizi di Azure AD Connect Health verranno avviati dopo la corretta registrazione dell'agente.

È possibile registrare manualmente l'agente Azure AD Connect Health per la sincronizzazione usando il comando di PowerShell seguente:

`Register-AzureADConnectHealthSyncAgent -AttributeFiltering $false -StagingMode $false`

Il comando accetta i parametri seguenti:

* AttributeFiltering: $true (impostazione predefinita) se Azure AD Connect non sincronizza il set di attributi predefiniti ed è stato personalizzato per usare un set di attributi filtrati, $false in caso contrario.
* StagingMode: $false (impostazione predefinita) se il server Azure AD Connect NON è in modalità di staging, $true se il server è configurato per la modalità di staging.

Quando viene richiesta l'autenticazione, è consigliabile usare lo stesso account amministratore globale, ad esempio admin@domain.onmicrosoft.com, usato per configurare Azure AD Connect.

## <a name="installing-the-azure-ad-connect-health-agent-for-ad-ds"></a>Installazione dell'agente di Azure AD Connect Health per Servizi di dominio Active Directory

Per avviare l'installazione dell'agente, fare doppio clic sul file EXE scaricato. Nella prima schermata fare clic su Installa.

![Verificare Azure AD Connect Health](./media/how-to-connect-health-agent-install/aadconnect-health-adds-agent-install1.png)

Una volta completata l'installazione, fare clic su Configura ora.

![Verificare Azure AD Connect Health](./media/how-to-connect-health-agent-install/aadconnect-health-adds-agent-install2.png)

Viene avviato un prompt dei comandi seguito da codice PowerShell che esegue Register-AzureADConnectHealthADDSAgent. Quando viene richiesto l'accesso ad Azure, effettuare l'accesso.

![Verificare Azure AD Connect Health](./media/how-to-connect-health-agent-install/aadconnect-health-adds-agent-install3.png)

Dopo l'accesso, PowerShell continuerà. Al termine, è possibile chiudere PowerShell e la configurazione sarà completa.

A questo punto, i servizi dovrebbero venire avviati automaticamente, consentendo all'agente di monitorare e raccogliere i dati. Se non sono stati soddisfatti tutti i prerequisiti descritti nelle sezioni precedenti, vengono visualizzati avvisi nella finestra di PowerShell. Assicurarsi di completare i [requisiti](how-to-connect-health-agent-install.md#requirements) prima di installare l'agente. La schermata seguente mostra un esempio di tali errori.

![Verificare Azure AD Connect Health per Servizi di dominio Active Directory](./media/how-to-connect-health-agent-install/aadconnect-health-adds-agent-install4.png)

Per verificare che l'agente sia stato installato, cercare i servizi seguenti nel controller di dominio.

* Azure AD Connect Health AD DS Insights Service
* Azure AD Connect Health AD DS Monitoring Service

Se la configurazione è stata completata, questi servizi dovrebbero già essere in esecuzione. In caso contrario, vengono arrestati fino al completamento della configurazione.

![Verificare Azure AD Connect Health](./media/how-to-connect-health-agent-install/aadconnect-health-adds-agent-install5.png)

### <a name="quick-agent-installation-in-multiple-servers"></a>Installazione rapida dell'agente in più server

1. Creare un account utente in Azure AD con una password.
2. Assegnare il **proprietario** ruolo per l'account AAD locale in Azure AD Connect Health nel portale. Seguire la procedura [qui](how-to-connect-health-operations.md#manage-access-with-role-based-access-control). Assegnare il ruolo per tutte le istanze del servizio. 
3. Scaricare il file MSI .exe nel controller di dominio locale per l'installazione.
4. Eseguire lo script seguente per la registrazione. Sostituire i parametri con il nuovo account utente creato e la relativa password. 

```powershell
AdHealthAddsAgentSetup.exe /quiet
Start-Sleep 30
$userName = "NEWUSER@DOMAIN"
$secpasswd = ConvertTo-SecureString "PASSWORD" -AsPlainText -Force
$myCreds = New-Object System.Management.Automation.PSCredential ($userName, $secpasswd)
import-module "C:\Program Files\Azure Ad Connect Health Adds Agent\PowerShell\AdHealthAdds"
 
Register-AzureADConnectHealthADDSAgent -UserPrincipalName $USERNAME -Credential $password

```

1. Al termine, è possibile rimuovere l'accesso per l'account locale eseguendo una o più dei seguenti: 
    * Rimuovere l'assegnazione di ruolo per l'account locale per AAD Connect Health
    * Ruotare la password per l'account locale. 
    * Disabilitare l'account locale di AAD
    * Eliminare l'account locale di AAD  

## <a name="agent-registration-using-powershell"></a>Registrazione dell'agente tramite PowerShell

Dopo l'installazione del file setup.exe appropriato per l'agente, è possibile eseguire il passaggio di registrazione dell'agente usando i comandi di PowerShell seguenti, in base al ruolo. Aprire una finestra di PowerShell ed eseguire il comando appropriato:

```powershell
    Register-AzureADConnectHealthADFSAgent
    Register-AzureADConnectHealthADDSAgent
    Register-AzureADConnectHealthSyncAgent

```

Questi comandi accettano "Credential" come parametro per completare la registrazione in modo non interattivo in un computer Server-Core.
* Il parametro Credential può essere acquisito in una variabile di PowerShell passata come parametro.
* È possibile specificare qualsiasi identità di Azure AD autorizzata all'accesso per la registrazione degli agenti e in cui NON sia abilitata la funzionalità Multi-Factor Authentication.
* Per impostazione predefinita, gli amministratori globali sono autorizzati ad accedere per eseguire la registrazione dell'agente. È anche possibile consentire ad altre identità con privilegi inferiori di eseguire questo passaggio. Per altre informazioni, vedere [Controllo degli accessi in base al ruolo](how-to-connect-health-operations.md#manage-access-with-role-based-access-control).

```powershell
    $cred = Get-Credential
    Register-AzureADConnectHealthADFSAgent -Credential $cred

```

## <a name="configure-azure-ad-connect-health-agents-to-use-http-proxy"></a>Configurare gli agenti di Azure AD Connect Health per l'uso del proxy HTTP

È possibile configurare gli agenti di Azure AD Connect Health per l'interazione con un proxy HTTP.

> [!NOTE]
> * L'uso di "Netsh WinHttp set ProxyServerAddress" non è supportato, perché l'agente usa System.Net, invece dei servizi HTTP Microsoft Windows, per creare richieste Web.
> * L'indirizzo del proxy HTTP viene usato per il pass-through di messaggi HTTPS crittografati.
> * I proxy autenticati, che usano HTTPBasic, non sono supportati.
>
>

### <a name="change-health-agent-proxy-configuration"></a>Modificare la configurazione del proxy per l'agente di Health

Sono disponibili le opzioni seguenti per configurare l'agente di Azure AD Connect Health per l'uso di un proxy HTTP.

> [!NOTE]
> È necessario riavviare tutti i servizi dell'agente Azure AD Connect Health per consentire l'aggiornamento delle impostazioni del proxy. Eseguire il comando seguente:<br />
>  Restart-Service AdHealth*
>
>

#### <a name="import-existing-proxy-settings"></a>Importare le impostazioni del proxy esistenti

##### <a name="import-from-internet-explorer"></a>Importare da Internet Explorer

È possibile importare le impostazioni del proxy HTTP di Internet Explorer, in modo che possano essere usate dagli agenti di Azure AD Connect Health. Eseguire il comando di PowerShell seguente in ogni server che esegue l'agente per l'integrità:

    Set-AzureAdConnectHealthProxySettings -ImportFromInternetSettings

##### <a name="import-from-winhttp"></a>Importare da WinHTTP

È possibile importare le impostazioni del proxy WinHTTP, in modo che possano essere usate dagli agenti di Azure AD Connect Health. Eseguire il comando di PowerShell seguente in ogni server che esegue l'agente per l'integrità:

    Set-AzureAdConnectHealthProxySettings -ImportFromWinHttp

#### <a name="specify-proxy-addresses-manually"></a>Specificare gli indirizzi proxy manualmente

È possibile specificare manualmente un server proxy in ogni server che esegue l'agente per l'integrità, eseguendo il comando di PowerShell seguente:

    Set-AzureAdConnectHealthProxySettings -HttpsProxyAddress address:port

Esempio: *Set-AzureAdConnectHealthProxySettings -HttpsProxyAddress myproxyserver: 443*

* "address" può essere un nome di server risolvibile con DNS o un indirizzo IPv4
* "port" può essere omesso. Se omesso, viene scelta la porta 443 come porta predefinita.

#### <a name="clear-existing-proxy-configuration"></a>Cancellare la configurazione del proxy esistente

È possibile eseguire il comando seguente per cancellare la configurazione del proxy esistente:

    Set-AzureAdConnectHealthProxySettings -NoProxy


### <a name="read-current-proxy-settings"></a>Leggere le impostazioni del proxy correnti

È possibile leggere le impostazioni del proxy attualmente configurate eseguendo il comando seguente:

    Get-AzureAdConnectHealthProxySettings


## <a name="test-connectivity-to-azure-ad-connect-health-service"></a>Testare la connettività per il servizio Azure AD Connect Health

È possibile che si verifichino problemi che provocano la perdita di connettività dell'agente di Azure AD Connect Health con il servizio Azure AD Connect Health, ad esempio problemi di rete, problemi di autorizzazioni o di altro tipo.

Se l'agente non riesce a inviare dati al servizio Azure AD Connect Health per più di due ore, il problema viene segnalato con l'avviso seguente nel portale: "I dati del Servizio integrità non sono aggiornati". È possibile verificare se l'agente di Azure AD Connect Health interessato è in grado di caricare dati nel servizio Azure AD Connect Health eseguendo il comando di PowerShell seguente:

    Test-AzureADConnectHealthConnectivity -Role ADFS

Il parametro role accetta attualmente i valori seguenti:

* AD FS
* Sincronizzazione
* ADDS

> [!NOTE]
> Per usare lo strumento di connettività, è prima di tutto necessario completare la registrazione dell'agente. Se non è possibile completare la registrazione dell'agente, verificare che siano stati soddisfatti tutti i [requisiti](how-to-connect-health-agent-install.md#requirements) per Azure AD Connect Health. Questo test di connettività viene eseguito per impostazione predefinita durante la registrazione dell'agente.
>
>

## <a name="related-links"></a>Collegamenti correlati

* [Azure AD Connect Health](whatis-hybrid-identity-health.md)
* [Operazioni di Azure AD Connect Health](how-to-connect-health-operations.md)
* [Uso di Azure AD Connect Health con AD FS](how-to-connect-health-adfs.md)
* [Uso di Azure AD Connect Health per la sincronizzazione](how-to-connect-health-sync.md)
* [Uso di Azure AD Connect Health con Servizi di dominio Active Directory](how-to-connect-health-adds.md)
* [Domande frequenti su Azure AD Connect Health](reference-connect-health-faq.md)
* [Cronologia delle versioni di Azure AD Connect Health](reference-connect-health-version-history.md)
