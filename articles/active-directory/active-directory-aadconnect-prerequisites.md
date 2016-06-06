<properties
   pageTitle="Azure AD Connect: Prerequisiti e hardware | Microsoft Azure"
   description="Questo argomento descrive i prerequisiti e i requisiti hardware per Azure AD Connect."
   services="active-directory"
   documentationCenter=""
   authors="andkjell"
   manager="stevenpo"
   editor="curtand"/>

<tags
   ms.service="active-directory"
   ms.workload="identity"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="article"
   ms.date="05/24/2016"
   ms.author="andkjell;billmath"/>

# Prerequisiti di Azure AD Connect
Questo argomento descrive i prerequisiti e i requisiti hardware per Azure AD Connect.

## Prima di installare Azure AD Connect
Prima di installare Azure AD Connect, sono necessari alcuni elementi.

### Azure AD
- Sottoscrizione di Azure o [sottoscrizione di una versione di valutazione di Azure](https://azure.microsoft.com/pricing/free-trial/). Questo è necessario solo per l'accesso al portale di Azure e non per l'uso di Azure AD Connect. Se si usa PowerShell o Office 365, non è necessaria una sottoscrizione di Azure per usare Azure AD Connect. Se si dispone di una licenza di Office 365, è inoltre possibile usare il portale di Office 365. Con una licenza di Office 365 a pagamento è inoltre possibile accedere al portale di Azure dal portale di Office 365.
- [Aggiungere e verificare il dominio](active-directory-add-domain.md) che si prevede di usare in Azure AD. Ad esempio, se si prevede di usare contoso.com per gli utenti, assicurarsi che il dominio sia stato verificato e che non si usi solo il dominio predefinito contoso.onmicrosoft.com.
- Una directory di Azure AD consentirà, per impostazione predefinita, 50.000 oggetti. Quando si verifica il dominio, il limite viene aumentato a 300.000 oggetti. Se sono necessari anche più oggetti in Azure AD, è necessario aprire un caso di supporto per aumentare ulteriormente il limite. Se sono necessari più di 500.000 oggetti, è necessaria una licenza, ad esempio Office 365, Azure AD Basic, Azure AD Premium o Enterprise Mobility Suite.

### Preparare i dati locali
- Esaminare le [funzionalità di sincronizzazione facoltative che è possibile abilitare in Azure AD](active-directory-aadconnectsyncservice-features.md) e valutare quali funzionalità abilitare.

### Ambiente e server locali
- Il livello funzionale della foresta e della versione dello schema di Active Directory deve essere Windows Server 2003 o versione successiva. I controller di dominio possono eseguire qualsiasi versione, purché siano soddisfatti i requisiti del livello dello schema e della foresta.
- Se si prevede di utilizzare la funzionalità **writeback delle password** i controller di dominio devono essere in Windows Server 2008 (con Service Pack più recenti) o versioni successive. Se i controller di dominio sono nella versione 2008 precedente a R2, è necessario applicare anche l'[hotfix KB2386717](http://support.microsoft.com/kb/2386717).
- Il controller di dominio usato da Azure AD deve essere scrivibile. Non è supportato l'uso di un controller di dominio di sola lettura e Azure AD Connect non seguirà alcun reindirizzamento di scrittura.
- Azure AD Connect non può essere installato su Small Business Server o Windows Server Essentials. Il server deve utilizzare Windows Server Standard o versione successiva.
- Azure AD Connect deve essere installato in Windows Server 2008 o versione successiva. Questo server può essere un controller di dominio o un server membro, se si usano le impostazioni rapide. Se si usano le impostazioni personalizzate, il server può anche essere autonomo e non deve essere aggiunto a un dominio.
- Se si installa Azure AD Connect in Windows Server 2008, assicurarsi di applicare gli aggiornamenti rapidi più recenti da Windows Update. L'installazione non inizierà con un server senza patch.
- Se si prevede di usare la funzionalità di **sincronizzazione delle password**, il server di Azure AD Connect deve essere in Windows Server 2008 R2 SP1 o versione successiva.
- Nel server Azure AD Connect devono essere installati [.NET Framework 4.5.1](#component-prerequisites) o versione successiva e [Microsoft PowerShell 3.0](#component-prerequisites) o versione successiva.
- Se viene distribuito Active Directory Federation Services, i server in cui verrà installato ADFS o il proxy dell'applicazione Web devono essere Windows Server 2012 R2 o versione successiva. In tali server per l'installazione remota è necessario che sia abilitata [Gestione remota Windows](#windows-remote-management).
- Se viene distribuito Active Directory Federation Services, sono necessari i [Certificati SSL](#ssl-certificate-requirements).
- Se viene distribuito Active Directory Federation Services, è necessario configurare la [risoluzione dei nomi](#name-resolution-for-federation-servers).
- Per archiviare i dati sull'identità, Azure AD Connect richiede un database SQL. Per impostazione predefinita, viene installato SQL Server 2012 Express LocalDB (una versione ridotta di SQL Server Express) e viene creato l'account del servizio nel computer locale. SQL Server Express ha un limite di dimensioni di 10 GB che consente di gestire circa 100.000 oggetti. Se è necessario gestire un numero di oggetti directory maggiore, si dovrà installare una versione di SQL Server diversa.
- Se si usa un SQL Server separato, si applicano questi requisiti:
    - Azure AD Connect supporta tutte le versioni di Microsoft SQL Server da SQL Server 2008 (con SP4) a SQL Server 2014. Il database SQL di Microsoft Azure **non è supportato** come database.
    - È necessario usare regole di confronto SQL senza distinzione tra maiuscole e minuscole. Queste sono identificate da \_CI\_ all'interno del nome. **Non è supportato** l'uso di regole di confronto con distinzione tra maiuscole e minuscole identificate da \_CI\_ all'interno del nome.
    - È possibile avere un'unica istanza del motore di sincronizzazione per database. **Non è supportata** la condivisione dell'istanza del database con la sincronizzazione FIM/MIM, DirSync o Azure AD Sync.

### Account
- Un account amministratore globale di Azure AD per la directory di Azure AD con cui si desidera eseguire l'integrazione. Deve essere un **account aziendale o dell'istituto di istruzione** e non un **account Microsoft**.
- Un account amministratore dell'organizzazione per Active Directory locale se si usano le impostazioni rapide o l’aggiornamento da DirSync.
- L’[Account è Active Directory](active-directory-aadconnect-accounts-permissions.md) se si usa il percorso di installazione delle impostazioni personalizzate.

### Configurazione del server Azure AD Connect
- Se gli amministratori globali hanno abilitato l'autenticazione MFA, l'URL ****https://secure.aadcdn.microsoftonline-p.com** deve essere incluso nell'elenco di siti attendibili. Se non è incluso, ne verrà richiesta l'aggiunta all'elenco di siti attendibili prima che venga effettuata una richiesta di verifica MFA. È possibile usare Internet Explorer per aggiungerlo ai siti attendibili.

### Connettività
- Il server Azure AD Connect necessita della risoluzione DNS per Intranet e Internet. Il server DNS deve essere in grado di risolvere i nomi per l'istanza locale di Active Directory e per gli endpoint di Azure AD.
- Se sono presenti firewall nella rete Intranet ed è necessario aprire alcune porte tra i server Azure AD Connect e i controller di dominio, vedere [Porte e protocolli necessari per la soluzione ibrida di gestione delle identità](active-directory-aadconnect-ports.md) per altre informazioni.
- Se il proxy limita gli URL a cui è possibile accedere, gli URL documentati in [URL e intervalli di indirizzi IP per Office 365](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2) devono essere aperti nel proxy.
    - Se si usa Microsoft Cloud Germany o il cloud di Microsoft Azure per enti pubblici, vedere [Azure AD Connect sync service instances considerations](active-directory-aadconnect-instances.md) (Azure AD Connect: Considerazioni speciali per le istanze) per i relativi URL.
- Per impostazione predefinita Azure AD Connect comunica con Azure AD tramite il protocollo TLS 1.0. È possibile sostituire quest'ultimo con TLS 1.2 seguendo i passaggi descritti in [Abilitare TLS 1.2 per Azure AD Connect](#enable-tls-12-for-azure-ad-connect).
- Se per la connessione a Internet si usa un proxy per traffico in uscita, è necessario aggiungere l'impostazione seguente al file **C:\\Windows\\Microsoft.NET\\Framework64\\v4.0.30319\\Config\\machine.config** per consentire all'Installazione guidata e al servizio di sincronizzazione di Azure AD Connect di connettersi a Internet e ad Azure AD. Questo testo deve essere immesso alla fine del file. In questo codice &lt;PROXYADRESS&gt; rappresenta l'indirizzo IP del proxy effettivo o nome host.

```
    <system.net>
        <defaultProxy>
            <proxy
            usesystemdefault="true"
            proxyaddress="http://<PROXYADDRESS>:<PROXYPORT>"
            bypassonlocal="true"
            />
        </defaultProxy>
    </system.net>
```

- Se il server proxy richiede l'autenticazione, l'[account del servizio](active-directory-aadconnect-accounts-permissions.md#azure-ad-connect-sync-service-accounts) deve trovarsi nel dominio ed è necessario specificare un [account del servizio personalizzato](active-directory-aadconnect-get-started-custom.md#install-required-components) tramite il percorso di installazione delle impostazioni personalizzate. Occorre inoltre modificare machine.config. Modificando machine.config, l'installazione guidata e il motore di sincronizzazione risponderanno alle richieste di autenticazione dal server proxy. In tutte le pagine dell’istallazione guidata, esclusa la pagina **Configura** vengono utilizzate le credenziali dell’utente che ha effettuato l’accesso. Nella pagina **Configura** alla fine dell'Installazione guidata il contesto passa all'[account del servizio](active-directory-aadconnect-accounts-permissions.md#azure-ad-connect-sync-service-accounts) creato. La sezione machine.config dovrebbe essere simile alla seguente sezione.

```
    <system.net>
        <defaultProxy enabled="true" useDefaultCredentials="true">
            <proxy
            usesystemdefault="true"
            proxyaddress="http://<PROXYADDRESS>:<PROXYPORT>"
            bypassonlocal="true"
            />
        </defaultProxy>
    </system.net>
```

Per altre informazioni sull’[Elemento proxy predefinito](https://msdn.microsoft.com/library/kd3cf2ex.aspx)vedere MSDN.

In caso di problemi di connettività, vedere [Risolvere i problemi di connettività con Azure AD Connect](active-directory-aadconnect-troubleshoot-connectivity.md).

### Altri
- Facoltativo: un account utente di prova per verificare la sincronizzazione.

## Prerequisiti dei componenti

### PowerShell e .Net Framework
Azure AD Connect si basa su Microsoft PowerShell e .NET Framework 4.5.1. Nel server deve essere installata questa versione o una versione successiva. In base alla versione di Windows Server, seguire questa procedura:

- Windows Server 2012 R2
  - Microsoft PowerShell viene installato per impostazione predefinita, non è richiesta alcuna azione.
  - .NET Framework 4.5.1 e versioni successive sono disponibili tramite Windows Update. Assicurarsi di aver installato gli aggiornamenti più recenti per Windows Server nel Pannello di controllo.
- Windows Server 2008 R2 e Windows Server 2012
  - La versione più recente di Microsoft PowerShell è disponibile in **Windows Management Framework 4.0** nell'[Area download Microsoft](http://www.microsoft.com/downloads).
  - .NET Framework 4.5.1 e versioni successive sono disponibili nell'[Area download Microsoft](http://www.microsoft.com/downloads).
- Windows Server 2008
  - La versione più recente supportata di PowerShell è disponibile in **Windows Management Framework 3.0**, disponibile nell'[Area download Microsoft](http://www.microsoft.com/downloads).
 - .NET Framework 4.5.1 e versioni successive sono disponibili nell'[Area download Microsoft](http://www.microsoft.com/downloads).

### Abilitare TLS 1.2 per Azure AD Connect
Per impostazione predefinita Azure AD Connect usa TLS 1.0 per crittografare le comunicazioni tra il server del motore di sincronizzazione e Azure AD. È possibile modificare questa impostazione configurando le applicazioni .Net in modo che usino TLS 1.2 per impostazione predefinita nel server. Altre informazioni su TLS 1.2 sono disponibili in [Advisory Microsoft sulla sicurezza 2960358](https://technet.microsoft.com/security/advisory/2960358).

1. Non è possibile abilitare TLS 1.2 in Windows Server 2008. È necessario Windows Server 2008 R2 o una versione successiva. Assicurarsi di aver installato l'hotfix di .Net 4.5.1 per il sistema operativo in uso. Vedere [Advisory Microsoft sulla sicurezza 2960358 ](https://technet.microsoft.com/security/advisory/2960358). Questa versione o una versione successiva potrebbe essere già installata nel server.
2. Se si usa Windows Server 2008 R2, verificare che TLS 1.2 sia abilitato. In Windows Server 2012 e nelle versioni successive dei sistemi operativi server, TLS 1.2 dovrebbe essere già abilitato.
```
[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2]
[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
```
3. Per tutti i sistemi operativi impostare questa chiave del Registro di sistema e riavviare il server.
```
HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319
"SchUseStrongCrypto"=dword:00000001
```
4. Se si vuole anche abilitare TLS 1.2 tra il server del motore di sincronizzazione e un SQL Server remoto, assicurarsi che siano installate le versioni necessarie installate per il [supporto di TLS 1.2 per Microsoft SQL Server](https://support.microsoft.com/kb/3135244).

## Prerequisiti per l'installazione e la configurazione dei servizi federativi

### Gestione remota di Windows
Quando si utilizza Azure AD Connect per distribuire Active Directory Federation Services o il Proxy applicazione Web, verificare i requisiti di seguito per garantire l’esito positivo della connettività e della configurazione:

- Se il server di destinazione fa parte del dominio, verificare che Windows Remote Managed sia abilitato
    - In una finestra di comando PSH con privilegi elevati, utilizzare il comando `Enable-PSRemoting –force`
- Se il server di destinazione è un computer WAP non appartenente al dominio, è necessario considerare alcuni requisiti aggiuntivi
 	- Nel computer di destinazione (computer WAP):
         - Verificare che il servizio winrm (Windows Remote Management / WS-Management) sia in esecuzione tramite lo snap-in Servizi
         - In una finestra di comando PSH con privilegi elevati, utilizzare il comando `Enable-PSRemoting –force`
    - Nel computer in cui viene eseguita la procedura guidata (se il computer di destinazione non appartiene a un dominio o appartiene a un dominio non attendibile):
        - In una finestra di comando PSH con privilegi elevati, utilizzare il comando `Set-Item WSMan:\localhost\Client\TrustedHosts –Value <DMZServerFQDN> -Force –Concatenate`
 	    - In Server Manager:
 		     - Aggiungere l'host DMZ WAP al pool di computer (scheda gestione server -> Gestisci -> Aggiungi server...usa DNS)
 		     - Scheda Gestione server Tutti i server: fare clic con il pulsante destro del mouse sul server WAP e scegliere Gestisci come, immettere le credenziali locali (non di dominio) per il computer WAP
 		     - Per convalidare la connettività PSH remota, nella scheda Gestione server Tutti i server: fare clic con il pulsante destro del mouse sul server WAP e scegliere Windows PowerShell. Si dovrebbe aprire una sessione remota di PSH per garantire sia possibile stabilire sessioni remote di PowerShell.

### Requisiti dei certificati SSL
**Importante:** è consigliabile utilizzare lo stesso certificato SSL in tutti i nodi della farm ADFS, e in tutti i server proxy applicazione Web.

- Il certificato deve essere un certificato X509.
- È possibile utilizzare un certificato autofirmato su server federativi in un ambiente di testing. Tuttavia, per un ambiente di produzione, si consiglia di ottenere il certificato da una CA pubblica.
    - Se si utilizza un certificato non attendibile pubblicamente, assicurarsi che il certificato installato su ciascun server Proxy applicazione Web sia attendibile nel server locale e in tutti i server federativi
- L'identità del certificato deve corrispondere al nome del servizio federativo (ad esempio, sts.contoso.com).
    - L'identità è un'estensione nome alternativo del soggetto (SAN) di tipo dNSName o, se non sono presenti voci SAN, il nome del soggetto specificato come nome comune.  
    - Nel certificato possono essere presenti più voci SAN, purché una di esse corrisponda al nome del servizio federativo.
    - Se si pianifica di utilizzare l’aggiunta alla rete aziendale, è necessario un SAN aggiuntivo con il valore **enterpriseregistration.** seguito dal suffisso nome dell’entità utente (UPN) dell'organizzazione, ad **esempio, enterpriseregistration.contoso.com**.
- I certificati basati su chiavi CNG (CryptoAPI next generation) chiavi e i provider di archiviazione chiavi non sono supportati. Ciò significa che è necessario utilizzare un certificato basato su un provider del servizio di crittografia e non su un provider di archiviazione chiavi.
- I certificati con caratteri jolly sono supportati.

### Risoluzione dei nomi per i server federativi
- Configurare i record DNS per il nome di servizio della federazione AD FS (ad esempio, sts.contoso.com) sia per la rete Intranet (il server DNS interno), sia per la rete Extranet (DNS pubblico attraverso il registrar di dominio). Per il record DNS Intranet, accertarsi di utilizzare record A e non record CNAME. Tale operazione è necessaria affinché l'autenticazione di Windows funzioni correttamente dal computer unito al dominio.
- Se si distribuiscono più server AD FS o server Proxy applicazione Web, assicurarsi di aver configurato il servizio di bilanciamento del carico e che i record DNS per il nome del servizio federativo AD FS (ad esempio, sts.contoso.com) puntino al bilanciamento del carico.
- Perché l'autenticazione integrata di Windows funzioni correttamente per le applicazioni browser con Internet Explorer nella rete Intranet, verificare che il nome del servizio federativo AD FS (ad esempio, adfs.contoso.com) venga aggiunto all'area Intranet in Internet Explorer. Tale operazione può essere controllata tramite criteri di gruppo e distribuita a tutti i computer appartenenti al dominio.

## Componenti di supporto di Azure AD Connect
Di seguito è riportato un elenco di componenti che verranno installati da Azure AD Connect nel server in cui è installato Azure AD Connect. L'elenco riguarda un'istallazione di SQL Express di base. Se si sceglie di usare una versione diversa di SQL Server nella pagina Installazione dei servizi di sincronizzazione, SQL Express LocalDB non viene installato in locale.

- Azure AD Connect Health
- Assistente per l'accesso a Microsoft Online Services per professionisti IT (installato ma senza dipendenze)
- Utilità della riga di comando di Microsoft SQL Server 2012
- Microsoft SQL Server 2012 Express LocalDB
- Client nativo di Microsoft SQL Server 2012
- Microsoft Visual C++ 2013 Redistribution Package

## Requisiti hardware per Azure AD Connect
La tabella seguente indica i requisiti minimi di sincronizzazione di Azure AD Connect per il computer.

| Numero di oggetti in Active Directory | CPU | Memoria | Dimensioni del disco rigido |
| ------------------------------------- | --- | ------ | --------------- |
| Meno di 10.000 | 1,6 GHz | 4 GB | 70 GB |
| 10\.000-50.000 | 1,6 GHz | 4 GB | 70 GB |
| 50\.000-100.000 | 1,6 GHz | 16 GB | 100 GB |
| Per 100.000 o più oggetti, è necessaria la versione completa di SQL Server| | | |
| 100\.000-300.000 | 1,6 GHz | 32 GB | 300 GB |
| 300\.000-600.000 | 1,6 GHz | 32 GB | 450 GB |
| Più di 600.000 | 1,6 GHz | 32 GB | 500 GB |

I requisiti minimi per i computer che eseguono ADFS o i server applicazioni Web sono i seguenti:

- CPU: dual core da 1,6 GHz o superiore
- MEMORIA: 2 GB o superiore
- Macchina virtuale di Azure: configurazione A2 o superiore

## Passaggi successivi
Altre informazioni su [Integrazione delle identità locali con Azure Active Directory](active-directory-aadconnect.md).

<!---HONumber=AcomDC_0525_2016-->