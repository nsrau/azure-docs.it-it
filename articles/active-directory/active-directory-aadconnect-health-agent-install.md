<properties
	pageTitle="Installazione dell'agente di Azure AD Connect Health per | Microsoft Azure"
	description="Si tratta della pagina di Azure AD Connect Health che descrive l'installazione dell'agente per ADFS e la sincronizzazione."
	services="active-directory"
	documentationCenter=""
	authors="karavar"
	manager="femila"
	editor="karavar"/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="08/05/2016"
	ms.author="vakarand"/>


# Installazione dell'agente di Azure AD Connect Health

Questo documento illustra le procedure per installare e configurare l'agente di Azure AD Connect Health. Gli agenti sono disponibili per il download in [questa pagina](active-directory-aadconnect-health.md#download-and-install-azure-ad-connect-health-agent).

## 	Requisiti
La tabella seguente è un elenco di requisiti per l'uso di Azure AD Connect Health.

| Requisito | Descrizione|
| ----------- | ---------- |
|Azure AD Premium| Azure AD Connect Health è una funzionalità di Azure AD Premium e richiede una licenza di Azure AD Premium. </br></br>Per altre informazioni, vedere [Introduzione ad Azure Active Directory Premium](active-directory-get-started-premium.md). </br>Per ottenere una versione di valutazione gratuita valida 30 giorni, vedere [Avviare una versione di valutazione](https://azure.microsoft.com/trial/get-started-active-directory/).|
|Per iniziare a usare Azure AD Connect Health, è necessario essere un amministratore globale dell'istanza di Azure AD.|Per impostazione predefinita, solo gli amministratori globali possono installare e configurare gli agenti per l'integrità per eseguire le operazioni iniziali, accedere al portale ed eseguire qualsiasi operazione in Azure AD Connect Health. Per altre informazioni, vedere [Amministrare la directory di Azure AD](active-directory-administer.md). <br><br> Con il controllo degli accessi in base al ruolo è possibile consentire l'accesso ad Azure AD Connect Health ad altri utenti dell'organizzazione. Per altre informazioni, vedere [Controllo degli accessi in base al ruolo per Azure AD Connect Health](active-directory-aadconnect-health-operations.md#manage-access-with-role-based-access-control). </br></br>**Importante:** l'account usato per l'installazione degli agenti deve essere un account aziendale o dell'istituto di istruzione. Non può essere un account Microsoft. Per altre informazioni, vedere [Iscrizione ad Azure come organizzazione](sign-up-organization.md)
|L'agente di Azure AD Connect Health è installato in ogni server di destinazione| Per Azure AD Connect Health è necessario che nei server di destinazione sia installato un agente, che fornirà i dati visualizzati nel portale. </br></br>Ad esempio, per ottenere i dati sull'infrastruttura locale di AD FS, l'agente deve essere installato nei server AD FS, nei server proxy AD FS e nei server proxy applicazione Web. Analogamente, per poter ottenere i dati sull'infrastruttura locale di Servizi di dominio Active Directory, l'agente deve essere installato nei controller di dominio. </br></br>**Importante:** l'account usato per l'installazione degli agenti deve essere un account aziendale o dell'istituto di istruzione. Non può essere un account Microsoft. Per altre informazioni, vedere [Iscrizione ad Azure come organizzazione](sign-up-organization.md)|
|Connettività in uscita agli endpoint di servizio di Azure|Durante l'installazione e la fase di esecuzione, l'agente richiede la connettività agli endpoint di servizio di Azure AD Connect Health. Se la connettività in uscita è bloccata, assicurarsi che gli endpoint seguenti siano aggiunti all'elenco degli indirizzi consentiti: </br></br><li>&#42;.blob.core.windows.net </li><li>&#42;.queue.core.windows.net</li><li>adhsprodwus.servicebus.windows.net - Port: 5671 </li><li>https://management.azure.com </li><li>https://s1.adhybridhealth.azure.com/</li><li>https://policykeyservice.dc.ad.msft.net/</li><li>https://login.windows.net</li><li>https://login.microsoftonline.com</li><li>https://secure.aadcdn.microsoftonline-p.com</li> |
|Porte del firewall nel server che esegue l'agente.| Per consentire la comunicazione dell'agente con gli endpoint di servizio Azure AD Connect Health è necessario che le porte del firewall seguenti siano aperte.</br></br><li>Porta TCP/UDP 443</li><li>Porta TCP/UDP 5671</li>
|Consentire i siti Web seguenti se è abilitata la funzionalità Protezione avanzata di IE| Se è abilitata la sicurezza avanzata IE, è necessario consentire i siti Web seguenti nel server in cui verrà installato l'agente.</br></br><li>https://login.microsoftonline.com</li><li>https://secure.aadcdn.microsoftonline-p.com</li><li>https://login.windows.net</li><li>Server federativo dell'organizzazione considerato attendibile da Azure Active Directory. Ad esempio: https://sts.contoso.com</li>




## Installare l'agente di Azure AD Connect Health per AD FS
Per avviare l'installazione dell'agente, fare doppio clic sul file EXE scaricato. Nella prima schermata fare clic su Installa.

![Verificare Azure AD Connect Health](./media/active-directory-aadconnect-health-requirements/install1.png)

Una volta completata l'installazione, fare clic su Configura ora.

![Verificare Azure AD Connect Health](./media/active-directory-aadconnect-health-requirements/install2.png)

Viene avviato un prompt dei comandi seguito da codice PowerShell che esegue Register-AzureADConnectHealthADFSAgent. Quando viene richiesto l'accesso ad Azure, effettuare l'accesso.

![Verificare Azure AD Connect Health](./media/active-directory-aadconnect-health-requirements/install3.png)


Dopo l'accesso, PowerShell continuerà. Al termine, è possibile chiudere PowerShell e la configurazione sarà completa.

A questo punto, i servizi dovrebbero venire avviati automaticamente, consentendo all'agente di monitorare e raccogliere i dati. Se non sono stati soddisfatti tutti i prerequisiti descritti nelle sezioni precedenti, vengono visualizzati avvisi nella finestra di PowerShell. Assicurarsi di completare i requisiti elencati [qui](active-directory-aadconnect-health-agent-install.md#requirements) prima di installare l'agente. La schermata seguente mostra un esempio di tali errori.

![Verificare Azure AD Connect Health](./media/active-directory-aadconnect-health-requirements/install4.png)

Per verificare che l'agente sia stato installato, cercare i servizi seguenti nel server. Questi servizi dovrebbero essere già in esecuzione se la configurazione è stata completata. In caso contrario, vengono arrestati fino al completamento della configurazione.

- Azure AD Connect Health AD FS Diagnostics Service
- Azure AD Connect Health AD FS Insights Service
- Azure AD Connect Health AD FS Monitoring Service

![Verificare Azure AD Connect Health](./media/active-directory-aadconnect-health-requirements/install5.png)


### Installazione dell'agente in server Windows Server 2008 R2

Procedura per server Windows Server 2008 R2:

1. Assicurarsi che il server venga eseguito con il Service Pack 1 o versione successiva.
1. Disattivare la funzionalità Protezione avanzata di IE per l'installazione dell'agente:
1. Installare Windows PowerShell 4.0 in ogni server prima di installare l'agente per l'integrità AD. Per installare Windows PowerShell 4.0:
 - Installare [Microsoft .NET Framework 4.5](https://www.microsoft.com/download/details.aspx?id=40779) usando il collegamento seguente per scaricare il programma di installazione offline.
 - Installare PowerShell ISE (da Funzionalità Windows)
 - Installare [Windows Management Framework 4.0.](https://www.microsoft.com/download/details.aspx?id=40855)
 - Installare Internet Explorer versione 10 o successiva nel server. Questa operazione è necessaria perché il Servizio integrità possa eseguire l'autenticazione usando le credenziali di amministratore di Azure.
1. Per altre informazioni sull'installazione di Windows PowerShell 4.0 in Windows Server 2008 R2, vedere l'articolo wiki [qui](http://social.technet.microsoft.com/wiki/contents/articles/20623.step-by-step-upgrading-the-powershell-version-4-on-2008-r2.aspx).

### Abilitare il controllo per AD FS

> [AZURE.NOTE] Questa sezione si applica solo ai server federativi AD FS.

Per poter usare questa funzionalità per raccogliere dati e analizzarli, l'agente di Azure AD Connect Health deve poter accedere alle informazioni presenti nei log di controllo di AD FS. Questi log non sono abilitati per impostazione predefinita. Usare le procedure seguenti per abilitare il controllo di AD FS e individuare i log di controllo di AD FS nei server AD FS.

#### Per abilitare il controllo per ADFS 2.0

1. Fare clic su **Avvia**, scegliere **Programmi**, scegliere **Strumenti di amministrazione** e quindi fare clic su **Criteri di sicurezza locali**.
2. Passare alla cartella **Impostazioni sicurezza\\Criteri locali\\Gestione diritti utente** e quindi fare doppio clic su Generazione di controlli di sicurezza.
3. Nella scheda **Impostazioni di sicurezza locali** verificare che sia elencato l'account del servizio AD FS 2.0. Se l'account non è presente, fare clic su **Aggiungi utente o gruppo** e aggiungerlo all'elenco, quindi fare clic su **OK**.
4. Per abilitare il controllo, aprire un prompt dei comandi con privilegi elevati ed eseguire il comando seguente: <code>auditpol.exe /set /subcategory:"Application Generated" /failure:enable /success:enable</code>
5. Chiudere Criteri di sicurezza locali e quindi aprire lo snap-in Gestione. Per aprire lo snap-in di gestione, fare clic su **Start**, scegliere **Programmi**, scegliere **Strumenti di amministrazione** e quindi fare clic sullo snap-in di gestione di AD FS 2.0.
6. Nel riquadro Azioni fare clic su Modifica proprietà servizio federativo.
7. Nella finestra di dialogo **Proprietà servizio federativo** fare clic sulla scheda **Eventi**.
8. Selezionare le caselle di controllo **Operazioni riuscite** e **Operazioni non riuscite**.
9. Fare clic su **OK**.

#### Per abilitare il controllo per ADFS in Windows Server 2012 R2

1. Per passare a **Criteri di sicurezza locali**, aprire **Server Manager** nella schermata Start oppure Server Manager nella barra delle applicazioni sul desktop, quindi fare clic su **Strumenti/Criteri di sicurezza locali**.
2. Passare alla cartella **Impostazioni sicurezza\\Criteri locali\\Assegnazione diritti utente** e quindi fare doppio clic su **Generazione di controlli di sicurezza**.
3. Nella scheda **Impostazioni sicurezza locale** verificare che sia elencato l'account del servizio AD FS. Se l'account non è presente, fare clic su **Aggiungi utente o gruppo** e aggiungerlo all'elenco, quindi fare clic su **OK**.
4. Per abilitare il controllo, aprire un prompt dei comandi con privilegi elevati ed eseguire il comando seguente: <code>auditpol.exe /set /subcategory:"Application Generated" /failure:enable /success:enable.</code>
5. Chiudere **Criteri di sicurezza locali** e quindi aprire lo snap-in di **Gestione AD FS**. In Server Manager fare clic su Strumenti e quindi selezionare Gestione AD FS.
6. Nel riquadro Azioni fare clic su **Modifica proprietà servizio federativo**.
7. Nella finestra di dialogo Proprietà servizio federativo fare clic sulla scheda **Eventi**.
8. Selezionare le caselle di controllo **Operazioni riuscite e Operazioni non riuscite** e quindi fare clic su **OK**.






#### Per individuare i log di controllo di ADFS


1. Aprire il **Visualizzatore eventi**.
2. Passare a Registri di Windows e selezionare **Sicurezza**.
3. A destra fare clic su **Filtra registri correnti**.
4. In Origine evento selezionare **Controllo di ADFS**.

![Log di controllo di ADFS](./media/active-directory-aadconnect-health-requirements/adfsaudit.png)

> [AZURE.WARNING] Se esistono criteri di gruppo che disabilitano il controllo AD FS, l'agente di Azure AD Connect Health non è in grado di raccogliere informazioni. Assicurarsi che non siano presenti criteri di gruppo che potrebbero disabilitare il controllo.

[//]: # "Inizio della sezione relativa alla configurazione del proxy per l'agente"

## Installare l'agente di Azure AD Connect Health per la sincronizzazione
L'Agente di integrità di Azure AD Connect per la sincronizzazione è installato automaticamente nella build più recente di Azure AD Connect. Per usare Azure AD Connect Health per la sincronizzazione, è necessario scaricare la versione più recente di Azure AD Connect e installarla. È possibile scaricare la versione più recente [qui](http://www.microsoft.com/download/details.aspx?id=47594).

Per verificare che l'agente sia stato installato, cercare i servizi seguenti nel server. Questi servizi dovrebbero essere già in esecuzione se la configurazione è stata completata. In caso contrario, vengono arrestati fino al completamento della configurazione.

- Azure AD Connect Health Sync Insights Service
- Azure AD Connect Health Sync Monitoring Service

![Verificare Azure AD Connect Health per la sincronizzazione](./media/active-directory-aadconnect-health-sync/services.png)

> [AZURE.NOTE] Si tenga presente che l'utilizzo di integrità di Azure AD Connect richiede Azure AD Premium. Se non si ha Azure AD Premium, non è possibile completare la configurazione nel portale di Azure. Per altre informazioni, vedere la [pagina dei requisiti](active-directory-aadconnect-health-agent-install.md#requirements).


## Registrazione manuale di Azure AD Connect Health per la sincronizzazione
Se la registrazione dell'agente Azure AD Connect Health per la sincronizzazione non riesce pur dopo avere installato correttamente Azure AD Connect, è possibile usare il comando di PowerShell seguente per registrare manualmente l'agente.

>[AZURE.IMPORTANT] È necessario usare questo comando di PowerShell solo se la registrazione dell'agente non riesce dopo avere installato Azure AD Connect.

Il comando di PowerShell seguente è necessario SOLO quando la registrazione dell'agente per l'integrità non riesce anche se l'installazione e la configurazione di Azure AD Connect sono state eseguite correttamente. I servizi di Azure AD Connect Health verranno avviati dopo la corretta registrazione dell'agente.

È possibile registrare manualmente l'agente Azure AD Connect Health per la sincronizzazione usando il comando di PowerShell seguente:

`Register-AzureADConnectHealthSyncAgent -AttributeFiltering $false -StagingMode $false`

Il comando accetta i parametri seguenti:

- AttributeFiltering: $true (impostazione predefinita) se Azure AD Connect non sincronizza il set di attributi predefiniti ed è stato personalizzato per usare un set di attributi filtrati; in caso contrario, $false.
- StagingMode: $false (impostazione predefinita) se il server Azure AD Connect NON è in modalità di staging, $true se il server è configurato per la modalità di staging.

Quando viene chiesta l'autenticazione, è consigliabile usare lo stesso account amministratore globale, ad esempio, admin@domain.onmicrosoft.com, usato per configurare Azure AD Connect.

## Installazione dell'agente di Azure AD Connect Health per Servizi di dominio Active Directory
Per avviare l'installazione dell'agente, fare doppio clic sul file EXE scaricato. Nella prima schermata fare clic su Installa.

![Verificare Azure AD Connect Health](./media/active-directory-aadconnect-health/aadconnect-health-adds-agent-install1.png)

Una volta completata l'installazione, fare clic su Configura ora.

![Verificare Azure AD Connect Health](./media/active-directory-aadconnect-health/aadconnect-health-adds-agent-install2.png)

Viene avviato un prompt dei comandi seguito da codice PowerShell che esegue Register-AzureADConnectHealthADDSAgent. Quando viene richiesto l'accesso ad Azure, effettuare l'accesso.

![Verificare Azure AD Connect Health](./media/active-directory-aadconnect-health/aadconnect-health-adds-agent-install3.png)

Dopo l'accesso, PowerShell continuerà. Al termine, è possibile chiudere PowerShell e la configurazione sarà completa.

A questo punto, i servizi dovrebbero venire avviati automaticamente, consentendo all'agente di monitorare e raccogliere i dati. Se non sono stati soddisfatti tutti i prerequisiti descritti nelle sezioni precedenti, vengono visualizzati avvisi nella finestra di PowerShell. Assicurarsi di completare i requisiti elencati [qui](active-directory-aadconnect-health-agent-install.md#requirements) prima di installare l'agente. La schermata seguente mostra un esempio di tali errori.

![Verificare Azure AD Connect Health per Servizi di dominio Active Directory](./media/active-directory-aadconnect-health/aadconnect-health-adds-agent-install4.png)

Per verificare che l'agente sia stato installato, cercare i servizi seguenti nel controller di dominio.

- Azure AD Connect Health AD DS Insights Service
- Azure AD Connect Health AD DS Monitoring Service

Se la configurazione è stata completata, questi servizi dovrebbero già essere in esecuzione. In caso contrario, vengono arrestati fino al completamento della configurazione.

![Verificare Azure AD Connect Health](./media/active-directory-aadconnect-health/aadconnect-health-adds-agent-install5.png)

## Installazione dell'agente di Azure AD Connect Health per Servizi di dominio Active Directory in Server Core 
Dopo aver installato il file EXE, è possibile completare il processo di registrazione con il comando di PowerShell seguente:

`Register-AzureADConnectHealthADDSAgent -Credential $cred`

## Configurare gli agenti di Azure AD Connect Health per l'uso del proxy HTTP
È possibile configurare gli agenti di Azure AD Connect Health per l'interazione con un proxy HTTP.

>[AZURE.NOTE]
- L'uso di "Netsh WinHttp set ProxyServerAddress" non è supportato, perché l'agente usa System.Net, invece dei servizi HTTP Microsoft Windows, per creare richieste Web.
- L'indirizzo del proxy HTTP viene usato per il pass-through di messaggi HTTPS crittografati.
- I proxy autenticati, che usano HTTPBasic, non sono supportati.

### Modificare la configurazione del proxy per l'agente di Health
Sono disponibili le opzioni seguenti per configurare l'agente di Azure AD Connect Health per l'uso di un proxy HTTP.

>[AZURE.NOTE] È necessario riavviare tutti i servizi dell'agente Azure AD Connect Health per consentire l'aggiornamento delle impostazioni del proxy. Eseguire il comando seguente:<br> Restart-Service AdHealth*

#### Importare le impostazioni del proxy esistenti

##### Importare da Internet Explorer
È possibile importare le impostazioni del proxy HTTP di Internet Explorer, in modo che possano essere usate dagli agenti di Azure AD Connect Health. Eseguire il comando di PowerShell seguente in ogni server che esegue l'agente per l'integrità:

	Set-AzureAdConnectHealthProxySettings -ImportFromInternetSettings

##### Importare da WinHTTP
È possibile importare le impostazioni del proxy WinHTTP, in modo che possano essere usate dagli agenti di Azure AD Connect Health. Eseguire il comando di PowerShell seguente in ogni server che esegue l'agente per l'integrità:

	Set-AzureAdConnectHealthProxySettings -ImportFromWinHttp

#### Specificare gli indirizzi proxy manualmente
È possibile specificare manualmente un server proxy in ogni server che esegue l'agente per l'integrità, eseguendo il comando di PowerShell seguente:

	Set-AzureAdConnectHealthProxySettings -HttpsProxyAddress address:port

Esempio: *Set-AzureAdConnectHealthProxySettings -HttpsProxyAddress myproxyserver: 443*

- "address" può essere un nome di server risolvibile con DNS o un indirizzo IPv4
- "port" può essere omesso. Se omesso, viene scelta la porta 443 come porta predefinita.

#### Cancellare la configurazione del proxy esistente
È possibile eseguire il comando seguente per cancellare la configurazione del proxy esistente:

	Set-AzureAdConnectHealthProxySettings -NoProxy


### Leggere le impostazioni del proxy correnti
È possibile leggere le impostazioni del proxy attualmente configurate eseguendo il comando seguente:

	Get-AzureAdConnectHealthProxySettings


## Testare la connettività per il servizio Azure AD Connect Health
È possibile che si verifichino problemi che provocano la perdita di connettività dell'agente di Azure AD Connect Health con il servizio Azure AD Connect Health, ad esempio problemi di rete, problemi di autorizzazioni o di altro tipo.

Se l'agente non riesce a inviare dati al servizio Azure AD Connect Health per più di due ore, il problema viene segnalato con l'avviso seguente nel portale: "I dati del Servizio integrità non sono aggiornati". È possibile verificare se l'agente di Azure AD Connect Health interessato è in grado di caricare dati nel servizio Azure AD Connect Health eseguendo il comando di PowerShell seguente:

    Test-AzureADConnectHealthConnectivity -Role ADFS

Il parametro role accetta attualmente i valori seguenti:

- AD FS
- Sincronizzazione
- ADDS

È possibile usare il flag -ShowResults nel comando per visualizzare log dettagliati. Usare l'esempio seguente:

    Test-AzureADConnectHealthConnectivity -Role Sync -ShowResult

>[AZURE.NOTE]Per usare lo strumento di connettività, è prima di tutto necessario completare la registrazione dell'agente. Se non è possibile completare la registrazione dell'agente, verificare che siano stati soddisfatti tutti i [requisiti](active-directory-aadconnect-health-agent-install.md#requirements) per Azure AD Connect Health. Questo test di connettività viene eseguito per impostazione predefinita durante la registrazione dell'agente.



## Collegamenti correlati

* [Azure AD Connect Health](active-directory-aadconnect-health.md)
* [Operazioni di Azure AD Connect Health](active-directory-aadconnect-health-operations.md)
* [Uso di Azure AD Connect Health con AD FS](active-directory-aadconnect-health-adfs.md)
* [Uso di Azure AD Connect Health per la sincronizzazione](active-directory-aadconnect-health-sync.md)
* [Uso di Azure AD Connect Health con Servizi di dominio Active Directory](active-directory-aadconnect-health-adds.md)
* [Domande frequenti su Azure AD Connect Health](active-directory-aadconnect-health-faq.md)
* [Cronologia delle versioni di Azure AD Connect Health](active-directory-aadconnect-health-version-history.md)

<!---HONumber=AcomDC_1005_2016-->