---
title: 'Azure AD Connect: Prerequisiti e hardware | Microsoft Docs'
description: Questo argomento descrive i prerequisiti e i requisiti hardware per Azure AD Connect
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 91b88fda-bca6-49a8-898f-8d906a661f07
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/08/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: b0392a40ef948d96e613da9127629f52b02deb97
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/08/2019
ms.locfileid: "67655810"
---
# <a name="prerequisites-for-azure-ad-connect"></a>Prerequisiti di Azure AD Connect
Questo argomento descrive i prerequisiti e i requisiti hardware per Azure AD Connect.

## <a name="before-you-install-azure-ad-connect"></a>Prima di installare Azure AD Connect
Prima di installare Azure AD Connect, sono necessari alcuni elementi.

### <a name="azure-ad"></a>Azure AD
* Un tenant di Azure AD. Uno è disponibile con una [versione di valutazione gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/). È possibile usare uno dei portali seguenti per gestire Azure AD Connect:
  * Il [portale di Azure](https://portal.azure.com).
  * Il [portale di Office](https://portal.office.com).  
* [Aggiungere e verificare il dominio](../active-directory-domains-add-azure-portal.md) che si prevede di usare in Azure AD. Ad esempio, se si prevede di usare contoso.com per gli utenti, assicurarsi che il dominio sia stato verificato e che non si usi solo il dominio predefinito contoso.onmicrosoft.com.
* Per impostazione predefinita, in un tenant di Azure AD sono consentiti 50.000 oggetti. Quando si verifica il dominio, questo limite aumenta a 300.000 oggetti. Se sono necessari anche più oggetti in Azure AD, è necessario aprire un caso di supporto per aumentare ulteriormente il limite. Se sono necessari più di 500.000 oggetti, allora occorre una licenza, ad esempio Office 365, Azure AD Basic, Azure AD Premium o Enterprise Mobility and Security.

### <a name="prepare-your-on-premises-data"></a>Preparare i dati locali
* Usare [IdFix](https://support.office.com/article/Install-and-run-the-Office-365-IdFix-tool-f4bd2439-3e41-4169-99f6-3fabdfa326ac) per identificare gli errori, ad esempio i duplicati e problemi di formattazione nella directory prima di eseguire la sincronizzazione con Azure AD e Office 365.
* Esaminare le [funzionalità di sincronizzazione facoltative che è possibile abilitare in Azure AD](how-to-connect-syncservice-features.md) e valutare quali funzionalità abilitare.

### <a name="on-premises-active-directory"></a>Active Directory locale
* Il livello funzionale della foresta e della versione dello schema di Active Directory deve essere Windows Server 2003 o versione successiva. I controller di dominio possono eseguire qualsiasi versione, purché siano soddisfatti i requisiti del livello dello schema e della foresta.
* Se si prevede di utilizzare la funzionalità **writeback delle password**, i controller di dominio devono essere in Windows Server 2008 R2 o versioni successive.
* Il controller di dominio usato da Azure AD deve essere scrivibile. **Non è supportato** l'uso di un controller di dominio di sola lettura e Azure AD Connect non segue alcun reindirizzamento di scrittura.
* **Non è supportato** l'uso di foreste o domini locali con nomi con "punti" (nome contenenti un punto ".") NetBios.
* È consigliabile [abilitare il Cestino di Active Directory](how-to-connect-sync-recycle-bin.md).

### <a name="azure-ad-connect-server"></a>Server di Azure AD Connect
>[!IMPORTANT]
>Il server Azure AD Connect contiene dati relativi alle identità critici e devono essere considerato come un componente di livello 0, come documentato in [il modello di livello amministrativo di Active Directory](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material)

* Azure AD Connect non può essere installato su Small Business Server o Windows Server Essentials prima del 2019 (Windows Server Essentials 2019 è supportato). Il server deve utilizzare Windows Server Standard o versione successiva.
* Installare Azure AD Connect in un Controller di dominio non è consigliata a causa di procedure consigliate e le impostazioni più restrittive che possono impedire l'installazione correttamente Azure AD Connect.
* Il server Azure AD Connect deve avere un'interfaccia utente grafica completa installata. **Non è supportata** l'installazione su Server Core.
>[!IMPORTANT]
>Installa Azure AD Connect nel server di piccole imprese, essentials server o server core non è supportato.

* Azure AD Connect deve essere installato in Windows Server 2008 R2 o versione successiva. Questo server deve essere aggiunto e può essere un controller di dominio o un server membro di dominio.
* Se si installa Azure AD Connect in Windows Server 2008 R2, assicurarsi di applicare gli hotfix più recenti da Windows Update. Se il server non dispone delle patch, l'installazione non può iniziare.
* Se si prevede di usare la funzionalità di **sincronizzazione delle password**, il server di Azure AD Connect deve essere in Windows Server 2008 R2 SP1 o versione successiva.
* Se si prevede di usare un **account del servizio gestito del gruppo**, il server Azure AD Connect deve essere installato su Windows Server 2012 o versioni successive.
* Nel server Azure AD Connect devono essere installati[.NET Framework 4.5.1](#component-prerequisites) o versione successiva e [Microsoft PowerShell 3.0](#component-prerequisites) o versione successiva.
* Nel server Azure AD Connect non deve essere abilitato il criterio di gruppo per la trascrizione di PowerShell.
* Se viene distribuito Active Directory Federation Services, i server in cui viene installato AD FS o il proxy dell'applicazione Web devono essere Windows Server 2012 R2 o versione successiva. [Gestione remota Windows](#windows-remote-management) .
* Se viene distribuito Active Directory Federation Services, sono necessari i [Certificati SSL](#ssl-certificate-requirements).
* Se viene distribuito Active Directory Federation Services, è necessario configurare la [risoluzione dei nomi](#name-resolution-for-federation-servers).
* Se gli amministratori globali hanno attivato MFA, l’URL quindi **https://secure.aadcdn.microsoftonline-p.com** deve essere nella lista dei siti attendibili. Viene richiesto di aggiungere il sito all'elenco dei siti attendibili quando viene richiesta la verifica MFA, se non è stata aggiunta prima. È possibile usare Internet Explorer per aggiungerlo ai siti attendibili.
* Microsoft consiglia di protezione avanzata dei server di Azure AD Connect per ridurre la superficie di attacco di sicurezza per questo componente fondamentale dell'ambiente IT.  Seguire le indicazioni riportate di seguito consentirà di ridurre i rischi di sicurezza per l'organizzazione.

* Distribuire Azure AD Connect in un server di dominio e limitare l'accesso amministrativo per gli amministratori di dominio o altri gruppi di sicurezza strettamente controllato.

Per altre informazioni, vedere: 

* [Protezione dei gruppi di amministratori](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/appendix-g--securing-administrators-groups-in-active-directory)

* [Protezione degli account amministratore predefinito](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/appendix-d--securing-built-in-administrator-accounts-in-active-directory)

* [Miglioramento della protezione e sustainment riducendo le superfici di attacco](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access#2-reduce-attack-surfaces )

* [La riduzione della superficie di attacco di Active Directory](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/reducing-the-active-directory-attack-surface)

### <a name="sql-server-used-by-azure-ad-connect"></a>SQL Server usato da Azure AD Connect
* Per archiviare i dati sull'identità, Azure AD Connect richiede un database SQL. Per impostazione predefinita viene installato SQL Server 2012 Express LocalDB (una versione ridotta di SQL Server Express). SQL Server Express ha un limite di dimensioni di 10 GB che consente di gestire circa 100.000 oggetti. Se è necessario gestire un numero di oggetti directory maggiore, si dovrà installare una versione di SQL Server diversa. Il tipo di installazione di SQL Server può influire sulle [sulle prestazioni di Azure AD Connect](https://docs.microsoft.com/azure/active-directory/hybrid/plan-connect-performance-factors#sql-database-factors).
* Se si usa un'installazione diversa di SQL Server, quindi applicano questi requisiti:
  * Azure AD Connect supporta tutte le versioni di Microsoft SQL Server da 2008 R2 (con Service Pack più recente) a SQL Server 2019. Il database SQL di Microsoft Azure **non è supportato** come database.
  * È necessario usare regole di confronto SQL senza distinzione tra maiuscole e minuscole. Queste regole di confronto sono identificate da \_CI_ all'interno del nome. **Non è supportato** l'uso di regole di confronto con distinzione tra maiuscole e minuscole identificate da _CS\_ all'interno del nome.
  * È possibile avere un unico motore di sincronizzazione per istanza SQL. **Non è supportata** la condivisione dell'istanza SQL con la sincronizzazione FIM/MIM, DirSync o Azure AD Sync.

### <a name="accounts"></a>Account
* Un account amministratore globale di Azure AD per il tenant di Azure AD con cui si vuole eseguire l'integrazione. Questo account deve essere un **account aziendale o dell'istituto di istruzione** e non un **account Microsoft**.
* Se si usano le impostazioni rapide o si esegue l'aggiornamento da DirSync, è necessario disporre di un account di amministratore dell'organizzazione per il servizio Active Directory locale.
* [Account in Active Directory](reference-connect-accounts-permissions.md) se si usa il percorso di installazione delle impostazioni personalizzate o un account di amministratore dell'organizzazione per il servizio Active Directory locale.

### <a name="connectivity"></a>Connettività
* Il server Azure AD Connect necessita della risoluzione DNS per Intranet e Internet. Il server DNS deve essere in grado di risolvere i nomi per gli endpoint locali di Active Directory e per gli endpoint di Azure AD.
* Se sono presenti firewall nella rete Intranet ed è necessario aprire alcune porte tra i server Azure AD Connect e i controller di dominio, vedere [Azure AD Connect: porte](reference-connect-ports.md) per altre informazioni.
* Se il proxy o il firewall limita gli URL a cui è possibile accedere, gli URL documentati in [URL e intervalli di indirizzi IP per Office 365 ](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2) devono essere aperti.
  * Se si usa Microsoft Cloud Germany o il cloud di Microsoft Azure per enti pubblici, vedere [Azure AD Connect sync service instances considerations](reference-connect-instances.md) (Azure AD Connect: Considerazioni speciali per le istanze) per i relativi URL.
* Per impostazione predefinita Azure AD Connect (versione 1.1.614.0 e successive) usa TLS 1.2 per crittografare le comunicazioni tra il motore di sincronizzazione e Azure AD. Se TLS 1.2 non è disponibile nel sistema operativo sottostante, Azure AD Connect esegue il fallback in modo incrementale sui protocolli meno recenti (TLS 1.1 e TLS 1.0).
* Prima della versione 1.1.614.0, per impostazione predefinita, Azure AD Connect usa TLS 1.0 per crittografare le comunicazioni tra il motore di sincronizzazione e Azure AD. Per passare a TLS 1.2 seguire i passaggi descritti in [Abilitare TLS 1.2 per Azure AD Connect](#enable-tls-12-for-azure-ad-connect).
* Se per la connessione a Internet si usa un proxy per il traffico in uscita, è necessario aggiungere l'impostazione seguente al file **C:\Windows\Microsoft.NET\Framework64\v4.0.30319\Config\machine.config** per consentire all'installazione guidata e al servizio di sincronizzazione Azure AD Connect di connettersi a Internet e ad Azure AD. Questo testo deve essere immesso alla fine del file. In questo codice &lt;PROXYADDRESS&gt; rappresenta l'effettivo indirizzo IP o nome host del proxy.

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

* Se il server proxy richiede l'autenticazione, l'[account del servizio](reference-connect-accounts-permissions.md#adsync-service-account) deve trovarsi nel dominio ed è necessario specificare un [account del servizio personalizzato](how-to-connect-install-custom.md#install-required-components) tramite il percorso di installazione delle impostazioni personalizzate. È inoltre necessario modificare il file machine.config, in modo tale che l'installazione guidata e il motore di sincronizzazione rispondano alle richieste di autenticazione provenienti dal server proxy. In tutte le pagine dell'istallazione guidata, esclusa la pagina **Configura**, vengono usate le credenziali dell'utente che ha effettuato l'accesso. Nella pagina **Configura** alla fine dell'installazione guidata il contesto passa all'[account del servizio](reference-connect-accounts-permissions.md#adsync-service-account) creato. La sezione machine.config dovrebbe essere simile alla seguente sezione.

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

* Quando Azure AD Connect invia una richiesta Web ad Azure AD come parte della sincronizzazione della directory, possono essere necessari fino a 5 minuti per ottenere la risposta da Azure AD. Accade spesso che i server proxy abbiano una configurazione per il timeout di inattività della connessione. Verificare che la configurazione sia impostata almeno su 6 minuti o più.

Per altre informazioni vedere MSDN sull'[elemento proxy predefinito](https://msdn.microsoft.com/library/kd3cf2ex.aspx).  
Per altre informazioni in caso di problemi di connettività, vedere [Risolvere i problemi di connettività](tshoot-connect-connectivity.md).

### <a name="other"></a>Altro
* Facoltativo: Un account utente di prova per verificare la sincronizzazione.

## <a name="component-prerequisites"></a>Prerequisiti dei componenti
### <a name="powershell-and-net-framework"></a>PowerShell e .NET Framework
Azure AD Connect si basa su Microsoft PowerShell e .NET Framework 4.5.1. Nel server deve essere installata questa versione o una versione successiva. In base alla versione di Windows Server, seguire questa procedura:

* Windows Server 2012 R2
  * Microsoft PowerShell viene installato per impostazione predefinita, non è necessaria alcuna azione.
  * .NET Framework 4.5.1 e versioni successive sono disponibili tramite Windows Update. Assicurarsi di aver installato gli aggiornamenti più recenti per Windows Server nel Pannello di controllo.
* Windows Server 2008 R2 e Windows Server 2012
  * La versione più recente di Microsoft PowerShell è disponibile in **Windows Management Framework 4.0**nell' [Area download Microsoft](https://www.microsoft.com/downloads).
  * .NET Framework 4.5.1 e versioni successive sono disponibili nell' [Area download Microsoft](https://www.microsoft.com/downloads).


### <a name="enable-tls-12-for-azure-ad-connect"></a>Abilitare TLS 1.2 per Azure AD Connect
Prima della versione 1.1.614.0, per impostazione predefinita Azure AD Connect usa TLS 1.0 per crittografare le comunicazioni tra il server del motore di sincronizzazione e Azure AD. È possibile modificare questa impostazione configurando le applicazioni .NET per usare TLS 1.2 per impostazione predefinita nel server. Altre informazioni su TLS 1.2 sono disponibili nell'articolo [Advisory Microsoft sulla sicurezza 2960358](https://technet.microsoft.com/security/advisory/2960358).

1. Prima di Windows Server 2008 R2 o versione successiva, non è possibile abilitare TLS 1.2. Assicurarsi di disporre di .NET 4.5.1 hotfix installato per il sistema operativo, vedere [Advisory Microsoft sulla sicurezza 2960358](https://technet.microsoft.com/security/advisory/2960358). Questo hotfix o una versione successiva potrebbe essere già installata nel server.
2. Se si usa Windows Server 2008 R2, verificare che TLS 1.2 sia abilitato. In Windows Server 2012 e versioni successive, TLS 1.2 dovrebbe essere già abilitato.
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
4. Se si vuole anche abilitare TLS 1.2 tra il server del motore di sincronizzazione e un SQL Server remoto, assicurarsi che siano installate le versioni necessarie per il [supporto di TLS 1.2 per Microsoft SQL Server](https://support.microsoft.com/kb/3135244).

## <a name="prerequisites-for-federation-installation-and-configuration"></a>Prerequisiti per l'installazione e la configurazione dei servizi federativi
### <a name="windows-remote-management"></a>Gestione remota Windows
Quando si usa Azure AD Connect per distribuire Active Directory Federation Services o il Proxy applicazione Web, verificare i requisiti di seguito:

* Se il server di destinazione fa parte del dominio, verificare che Windows Remote Managed sia abilitato
  * In una finestra di comando PSH con privilegi elevati, utilizzare il comando `Enable-PSRemoting –force`
* Se il server di destinazione è un computer WAP non appartenente al dominio, è necessario considerare alcuni requisiti aggiuntivi
  * Nel computer di destinazione (computer WAP):
    * Verificare che il servizio winrm (Windows Remote Management / WS-Management) sia in esecuzione tramite lo snap-in Servizi
    * In una finestra di comando PSH con privilegi elevati, utilizzare il comando `Enable-PSRemoting –force`
  * Nel computer in cui viene eseguita la procedura guidata (se il computer di destinazione non appartiene a un dominio o appartiene a un dominio non attendibile):
    * In una finestra di comando PSH con privilegi elevati, utilizzare il comando `Set-Item WSMan:\localhost\Client\TrustedHosts –Value <DMZServerFQDN> -Force –Concatenate`
    * In Server Manager:
      * Aggiungere l'host DMZ WAP al pool di computer (scheda gestione server -> Gestisci -> Aggiungi server...usa DNS)
      * Scheda Gestione server Tutti i server: fare clic con il pulsante destro del mouse sul server WAP e scegliere Gestisci come, immettere le credenziali locali (non di dominio) per il computer WAP
      * Per convalidare la connettività PSH remota, nella scheda Gestione server Tutti i server: fare clic con il pulsante destro del mouse sul server WAP e scegliere Windows PowerShell. Si dovrebbe aprire una sessione remota di PSH per garantire sia possibile stabilire sessioni remote di PowerShell.

### <a name="ssl-certificate-requirements"></a>Requisiti dei certificati SSL
* È consigliabile usare lo stesso certificato SSL in tutti i nodi della farm AD FS e in tutti i server proxy dell'Applicazione Web.
* Il certificato deve essere un certificato X509.
* È possibile utilizzare un certificato autofirmato su server federativi in un ambiente di testing. Tuttavia, per un ambiente di produzione, si consiglia di ottenere il certificato da una CA pubblica.
  * Se si utilizza un certificato non attendibile pubblicamente, assicurarsi che il certificato installato su ciascun server Proxy applicazione Web sia attendibile nel server locale e in tutti i server federativi
* L'identità del certificato deve corrispondere al nome del servizio federativo (ad esempio, sts.contoso.com).
  * L'identità è un'estensione nome alternativo del soggetto (SAN) di tipo dNSName o, se non sono presenti voci SAN, il nome del soggetto specificato come nome comune.  
  * Nel certificato possono essere presenti più voci SAN, purché una di esse corrisponda al nome del servizio federativo.
  * Se si prevede di usare Aggiunta all'area di lavoro, è richiesto un SAN aggiuntivo con il valore **enterpriseregistration.** seguito dal suffisso del nome dell'entità utente (UPN) dell'organizzazione, ad esempio **enterpriseregistration.contoso.com**.
* I certificati basati su chiavi CNG (CryptoAPI next generation) chiavi e i provider di archiviazione chiavi non sono supportati. Ciò significa che è necessario utilizzare un certificato basato su un provider del servizio di crittografia e non su un provider di archiviazione chiavi.
* I certificati con caratteri jolly sono supportati.

### <a name="name-resolution-for-federation-servers"></a>Risoluzione dei nomi per i server federativi
* Configurare i record DNS per il nome di servizio della federazione AD FS (ad esempio, sts.contoso.com) sia per la rete Intranet (il server DNS interno), sia per la rete Extranet (DNS pubblico attraverso il registrar di dominio). Per il record DNS Intranet, accertarsi di usare record A e non record CNAME. Tale operazione è necessaria affinché l'autenticazione di Windows funzioni correttamente dal computer unito al dominio.
* Se si distribuiscono più server AD FS o server Proxy applicazione Web, assicurarsi di aver configurato il servizio di bilanciamento del carico e che i record DNS per il nome del servizio federativo AD FS (ad esempio, sts.contoso.com) puntino al bilanciamento del carico.
* Perché l'autenticazione integrata di Windows funzioni correttamente per le applicazioni browser con Internet Explorer nella rete Intranet, verificare che il nome del servizio federativo AD FS (ad esempio, adfs.contoso.com) venga aggiunto all'area Intranet in Internet Explorer. Tale operazione può essere controllata tramite criteri di gruppo e distribuita a tutti i computer appartenenti al dominio.

## <a name="azure-ad-connect-supporting-components"></a>Componenti di supporto di Azure AD Connect
Di seguito è riportato un elenco di componenti che vengono installati da Azure AD Connect nel server in cui è installato Azure AD Connect. L'elenco riguarda un'istallazione di SQL Express di base. Se si sceglie di usare una versione diversa di SQL Server nella pagina Installazione dei servizi di sincronizzazione, SQL Express LocalDB non viene installato in locale.

* Azure AD Connect Health
* Utilità della riga di comando di Microsoft SQL Server 2012
* Microsoft SQL Server 2012 Express LocalDB
* Client nativo di Microsoft SQL Server 2012
* Microsoft Visual C++ 2013 Redistribution Package

## <a name="hardware-requirements-for-azure-ad-connect"></a>Requisiti hardware per Azure AD Connect
La tabella seguente indica i requisiti minimi di sincronizzazione di Azure AD Connect per il computer.

| Numero di oggetti in Active Directory | CPU | Memoria | Dimensioni del disco rigido |
| --- | --- | --- | --- |
| Meno di 10.000 |1,6 GHz |4 GB |70 GB |
| 10.000-50.000 |1,6 GHz |4 GB |70 GB |
| 50.000-100.000 |1,6 GHz |16 GB |100 GB |
| Per 100.000 o più oggetti, è necessaria la versione completa di SQL Server | | | |
| 100.000-300.000 |1,6 GHz |32 GB |300 GB |
| 300.000-600.000 |1,6 GHz |32 GB |450 GB |
| Più di 600.000 |1,6 GHz |32 GB |500 GB |

I requisiti minimi per i computer che eseguono ADFS o i server applicazioni Web sono i seguenti:

* CPU: Dual core da 1,6 GHz o superiore
* MEMORIA: 2 GB o superiore
* Macchina virtuale di Azure: Configurazione A2 o superiore

## <a name="next-steps"></a>Passaggi successivi
Altre informazioni su [Integrazione delle identità locali con Azure Active Directory](whatis-hybrid-identity.md).
