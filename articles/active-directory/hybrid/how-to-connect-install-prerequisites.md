---
title: 'Azure AD Connect: Prerequisiti e hardware | Microsoft Docs'
description: Questo articolo descrive i prerequisiti e i requisiti hardware per Azure AD Connect.
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
ms.topic: how-to
ms.date: 06/25/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: d1f77d6189e5b32ca771d17ae9902341bcaa1871
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/20/2020
ms.locfileid: "88688131"
---
# <a name="prerequisites-for-azure-ad-connect"></a>Prerequisiti di Azure AD Connect
Questo articolo descrive i prerequisiti e i requisiti hardware per la connessione Azure Active Directory (Azure AD).

## <a name="before-you-install-azure-ad-connect"></a>Prima di installare Azure AD Connect
Prima di installare Azure AD Connect, sono necessari alcuni elementi.

### <a name="azure-ad"></a>Azure AD
* È necessario un tenant Azure AD. Uno è disponibile con una [versione di valutazione gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/). È possibile usare uno dei portali seguenti per gestire Azure AD Connect:
  * [Portale di Azure](https://portal.azure.com).
  * Il [portale di Office](https://portal.office.com).
* [Aggiungere e verificare il dominio](../active-directory-domains-add-azure-portal.md) che si prevede di usare in Azure AD. Se ad esempio si prevede di usare contoso.com per gli utenti, verificare che il dominio sia stato verificato e che non si stia usando solo il dominio predefinito contoso.onmicrosoft.com.
* Un tenant di Azure AD consente, per impostazione predefinita, 50.000 oggetti. Quando si verifica il dominio, il limite aumenta a 300.000 oggetti. Se sono necessari ancora più oggetti in Azure AD, aprire un caso di supporto per aumentare ulteriormente il limite. Se sono necessari più di 500.000 oggetti, è necessaria una licenza, ad esempio Office 365, Azure AD Premium o Enterprise Mobility + Security.

### <a name="prepare-your-on-premises-data"></a>Preparare i dati locali
* Usare [IdFix](https://support.office.com/article/Install-and-run-the-Office-365-IdFix-tool-f4bd2439-3e41-4169-99f6-3fabdfa326ac) per identificare gli errori, ad esempio i duplicati e problemi di formattazione nella directory prima di eseguire la sincronizzazione con Azure AD e Office 365.
* Esaminare le [funzionalità di sincronizzazione facoltative che è possibile abilitare in Azure ad](how-to-connect-syncservice-features.md)e valutare le funzionalità da abilitare.

### <a name="on-premises-active-directory"></a>Active Directory locale
* La versione dello schema Active Directory e il livello di funzionalità della foresta devono essere Windows Server 2003 o versioni successive. I controller di dominio possono eseguire qualsiasi versione, purché siano soddisfatti i requisiti di versione dello schema e a livello di foresta.
* Se si prevede di usare il *writeback delle password*della funzionalità, i controller di dominio devono essere in Windows Server 2008 R2 o versioni successive.
* Il controller di dominio usato da Azure AD deve essere scrivibile. L'uso di un controller di dominio di sola lettura (RODC) *non è supportato*e Azure ad Connect non segue alcun reindirizzamento di scrittura.
* Uso di foreste o domini locali con "punteggiato" (il nome contiene un punto ".") I nomi NetBIOS *non sono supportati*.
* Si consiglia di [abilitare il cestino Active Directory](how-to-connect-sync-recycle-bin.md).

### <a name="azure-ad-connect-server"></a>Server di Azure AD Connect
Il server di Azure AD Connect contiene dati di identità critici. È importante che l'accesso amministrativo a questo server sia protetto correttamente. Seguire le linee guida per la [protezione dell'accesso con privilegi](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access). 

Il server di Azure AD Connect deve essere considerato come un componente di livello 0 come documentato nel [modello di livello amministrativo Active Directory](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material) 

Per altre informazioni sulla protezione dell'ambiente di Active Directory, vedere [procedure consigliate per la protezione di Active Directory](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/best-practices-for-securing-active-directory).

#### <a name="installation-prerequisites"></a>Prerequisiti di installazione

- Azure AD Connect deve essere installato in un dominio Windows Server 2012 o versione successiva. 
- Non è possibile installare Azure AD Connect su Small Business Server o Windows Server Essentials prima del 2019 (è supportato Windows Server Essentials 2019). Il server deve utilizzare Windows Server Standard o versione successiva. 
- Il server Azure AD Connect deve avere un'interfaccia utente grafica completa installata. L'installazione di Azure AD Connect in Windows Server Core non è supportata. 
- Il server di Azure AD Connect non deve avere la trascrizione di PowerShell Criteri di gruppo abilitata se si usa la procedura guidata Azure AD Connect per gestire la configurazione Active Directory Federation Services (AD FS). È possibile abilitare la trascrizione di PowerShell se si utilizza la procedura guidata Azure AD Connect per gestire la configurazione della sincronizzazione. 
- Se è in corso la distribuzione di AD FS: 
    - I server in cui è installato AD FS o il proxy dell'applicazione Web devono essere Windows Server 2012 R2 o versione successiva. Gestione remota Windows . 
    - È necessario configurare i certificati TLS/SSL. Per ulteriori informazioni, vedere [gestione dei protocolli SSL/TLS e dei pacchetti di crittografia per ad FS](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/manage-ssl-protocols-in-ad-fs) e [gestione dei certificati SSL in ad FS](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/manage-ssl-certificates-ad-fs-wap).
    - È necessario configurare la risoluzione dei nomi. 
- Se l'autenticazione a più fattori è abilitata per gli amministratori globali, l'URL https://secure.aadcdn.microsoftonline-p.com *deve* trovarsi nell'elenco dei siti attendibili. Viene richiesto di aggiungere il sito all'elenco siti attendibili quando viene richiesta una richiesta di autenticazione a più fattori e non è stato aggiunto in precedenza. È possibile usare Internet Explorer per aggiungerlo ai siti attendibili.

#### <a name="harden-your-azure-ad-connect-server"></a>Protezione avanzata del server di Azure AD Connect 
È consigliabile finalizzare il server Azure AD Connect per ridurre la superficie di attacco alla sicurezza per questo componente critico dell'ambiente IT. Seguendo questi consigli, è possibile ridurre i rischi di sicurezza per l'organizzazione.

- Considerare Azure AD Connect come un controller di dominio e altre risorse di livello 0. Per ulteriori informazioni, vedere [Active Directory modello di livello amministrativo](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material).
- Limitare l'accesso amministrativo al server Azure AD Connect solo agli amministratori di dominio o ad altri gruppi di sicurezza strettamente controllati.
- Creare un [account dedicato per tutto il personale con accesso con privilegi](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access). Gli amministratori non dovrebbero esplorare il Web, controllare la posta elettronica e svolgere attività quotidiane per la produttività con account con privilegi elevati.
- Seguire le istruzioni fornite nella pagina relativa alla [protezione dell'accesso con privilegi](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access). 
- Negare l'utilizzo dell'autenticazione NTLM con il server AADConnect. Di seguito sono riportati alcuni modi per eseguire questa operazione: [limitazione di NTLM nel server AADConnect](https://docs.microsoft.com/windows/security/threat-protection/security-policy-settings/network-security-restrict-ntlm-outgoing-ntlm-traffic-to-remote-servers) e [restrizione di NTLM in un dominio](https://docs.microsoft.com/windows/security/threat-protection/security-policy-settings/network-security-restrict-ntlm-ntlm-authentication-in-this-domain)
- Verificare che ogni computer disponga di una password di amministratore locale univoca. Per ulteriori informazioni, vedere la pagina relativa alla [soluzione di password di amministratore locale (giri)](https://support.microsoft.com/help/3062591/microsoft-security-advisory-local-administrator-password-solution-laps) può configurare password casuali univoche in ogni workstation e server archiviarle in Active Directory protette da un ACL. Solo gli utenti autorizzati idonei possono leggere o richiedere la reimpostazione di queste password di account amministratore locale. È possibile ottenere i giri da usare nelle workstation e nei server dall' [area download Microsoft](https://www.microsoft.com/download/details.aspx?id=46899#:~:text=The%20%22Local%20Administrator%20Password%20Solution,it%20or%20request%20its%20reset.). Indicazioni aggiuntive per la gestione di un ambiente con i giri e le workstation con accesso con privilegi (Paw) sono disponibili negli [standard operativi basati sul principio di origine pulita](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material#operational-standards-based-on-clean-source-principle). 
- Implementare [workstation con accesso con privilegi](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations) dedicati per tutto il personale con accesso con privilegi ai sistemi informativi dell'organizzazione. 
- Seguire queste [linee guida aggiuntive](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/reducing-the-active-directory-attack-surface) per ridurre la superficie di attacco dell'ambiente Active Directory.


### <a name="sql-server-used-by-azure-ad-connect"></a>SQL Server usato da Azure AD Connect
* Per archiviare i dati sull'identità, Azure AD Connect richiede un database SQL. Per impostazione predefinita, viene installato un SQL Server 2012 Express local DB (una versione ridotta di SQL Server Express). SQL Server Express ha un limite di dimensioni di 10 GB che consente di gestire circa 100.000 oggetti. Se è necessario gestire un volume superiore di oggetti directory, puntare l'installazione guidata a una diversa installazione di SQL Server. Il tipo di installazione di SQL Server può influisca sulle [prestazioni di Azure ad Connect](https://docs.microsoft.com/azure/active-directory/hybrid/plan-connect-performance-factors#sql-database-factors).
* Se si usa un'installazione diversa di SQL Server, si applicano i requisiti seguenti:
  * Azure AD Connect supporta tutte le versioni di SQL Server da 2012 (con le Service Pack più recenti) a SQL Server 2019. Il database SQL di Azure *non è supportato* come database.
  * È necessario usare regole di confronto SQL senza distinzione tra maiuscole e minuscole. Queste regole di confronto sono identificate da \_CI_ all'interno del nome. L'utilizzo di regole di confronto con distinzione tra maiuscole e minuscole identificate dal \_ cs_ nel nome *non è supportato*.
  * È possibile avere un solo motore di sincronizzazione per ogni istanza di SQL. La condivisione di un'istanza SQL con la sincronizzazione FIM/MIM, DirSync o Azure AD Sync *non è supportata*.

### <a name="accounts"></a>Account
* È necessario disporre di un account Azure AD amministratore globale per il tenant Azure AD con cui si vuole eseguire l'integrazione. Questo account deve essere un *account aziendale o dell'Istituto di istruzione* e non può essere un *account Microsoft*.
* Se si usano [le impostazioni rapide](reference-connect-accounts-permissions.md#express-settings-installation) o si esegue l'aggiornamento da dirsync, è necessario disporre di un account amministratore dell'organizzazione per la Active Directory locale.
* Se si usa il percorso di installazione delle impostazioni personalizzate, sono disponibili più opzioni. Per ulteriori informazioni, vedere [impostazioni di installazione personalizzate](reference-connect-accounts-permissions.md#custom-installation-settings).

### <a name="connectivity"></a>Connettività
* Il server Azure AD Connect necessita della risoluzione DNS per Intranet e Internet. Il server DNS deve essere in grado di risolvere i nomi per gli endpoint locali di Active Directory e per gli endpoint di Azure AD.
* Se si dispone di firewall sulla Intranet ed è necessario aprire le porte tra i server Azure AD Connect e i controller di dominio, vedere [porte Azure ad Connect](reference-connect-ports.md) per ulteriori informazioni.
* Se il proxy o il firewall limita gli URL a cui è possibile accedere, gli URL documentati in [URL e intervalli di indirizzi IP di Office 365](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2) devono essere aperti.
  * Se si usa Microsoft Cloud in Germania o nel cloud Microsoft Azure per enti pubblici, vedere Considerazioni sulle [istanze del servizio di sincronizzazione Azure ad Connect](reference-connect-instances.md) per gli URL.
* Per impostazione predefinita Azure AD Connect (versione 1.1.614.0 e successive) usa TLS 1.2 per crittografare le comunicazioni tra il motore di sincronizzazione e Azure AD. Se TLS 1.2 non è disponibile nel sistema operativo sottostante, Azure AD Connect esegue il fallback in modo incrementale sui protocolli meno recenti (TLS 1.1 e TLS 1.0).
* Prima della versione 1.1.614.0, per impostazione predefinita, Azure AD Connect usa TLS 1.0 per crittografare le comunicazioni tra il motore di sincronizzazione e Azure AD. Per passare a TLS 1.2 seguire i passaggi descritti in [Abilitare TLS 1.2 per Azure AD Connect](#enable-tls-12-for-azure-ad-connect).
* Se si usa un proxy in uscita per la connessione a Internet, è necessario aggiungere l'impostazione seguente nel file di **C:\Windows\Microsoft.NET\Framework64\v4.0.30319\Config\machine.config** per l'installazione guidata e Azure ad Connect sincronizzazione per potersi connettere a internet e Azure ad. Questo testo deve essere immesso alla fine del file. In questo codice * &lt; PROXYADDRESS &gt; * rappresenta l'indirizzo IP o il nome host effettivo del proxy.

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

* Se il server proxy richiede l'autenticazione, l' [account del servizio](reference-connect-accounts-permissions.md#adsync-service-account) deve trovarsi nel dominio. Usare il percorso di installazione delle impostazioni personalizzate per specificare un [account del servizio personalizzato](how-to-connect-install-custom.md#install-required-components). È anche necessario apportare una modifica diversa a machine.config. Con questa modifica in machine.config, l'installazione guidata e il motore di sincronizzazione rispondono alle richieste di autenticazione dal server proxy. In tutte le pagine dell'installazione guidata, esclusa la pagina **Configura** , vengono utilizzate le credenziali dell'utente che ha eseguito l'accesso. Nella pagina **Configura** alla fine dell'installazione guidata, il contesto viene impostato sull' [account del servizio](reference-connect-accounts-permissions.md#adsync-service-account) creato. La sezione machine.config dovrebbe essere simile alla seguente:

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

* Se la configurazione del proxy viene eseguita in un programma di installazione esistente, è necessario riavviare il **servizio Microsoft Azure ad Sync** per la Azure ad Connect per leggere la configurazione del proxy e aggiornare il behviour. 
* Quando Azure AD Connect invia una richiesta Web ad Azure AD come parte della sincronizzazione della directory, possono essere necessari fino a 5 minuti per ottenere la risposta da Azure AD. Per i server proxy è frequente la configurazione del timeout di inattività della connessione. Verificare che la configurazione sia impostata su almeno 6 minuti o più.

Per ulteriori informazioni, vedere MSDN sull' [elemento proxy predefinito](https://msdn.microsoft.com/library/kd3cf2ex.aspx).
Per altre informazioni in caso di problemi di connettività, vedere [Risolvere i problemi di connettività](tshoot-connect-connectivity.md).

### <a name="other"></a>Altro
Facoltativo: usare un account utente di test per verificare la sincronizzazione.

## <a name="component-prerequisites"></a>Prerequisiti dei componenti
### <a name="powershell-and-net-framework"></a>PowerShell e .NET Framework
Azure AD Connect si basa su Microsoft PowerShell e .NET Framework 4.5.1. Nel server deve essere installata questa versione o una versione successiva. A seconda della versione di Windows Server, eseguire le operazioni seguenti:

* Windows Server 2012 R2
  * Microsoft PowerShell viene installato per impostazione predefinita, Non è richiesta alcuna azione.
  * .NET Framework 4.5.1 e versioni successive sono disponibili tramite Windows Update. Assicurarsi di aver installato gli ultimi aggiornamenti di Windows Server nel pannello di controllo.
* Windows Server 2012
  * La versione più recente di Microsoft PowerShell è disponibile in Windows Management Framework 4,0, disponibile nell' [area download Microsoft](https://www.microsoft.com/downloads).
  * .NET Framework 4.5.1 e versioni successive sono disponibili nell' [area download Microsoft](https://www.microsoft.com/downloads).


### <a name="enable-tls-12-for-azure-ad-connect"></a>Abilitare TLS 1.2 per Azure AD Connect
Prima della versione 1.1.614.0, per impostazione predefinita Azure AD Connect usa TLS 1.0 per crittografare le comunicazioni tra il server del motore di sincronizzazione e Azure AD. Per impostazione predefinita, è possibile configurare le applicazioni .NET per l'uso di TLS 1,2 nel server. Per ulteriori informazioni su TLS 1,2, vedere [Microsoft Security advisor 2960358](https://technet.microsoft.com/security/advisory/2960358).

1. Assicurarsi che sia installato l'hotfix .NET 4.5.1 per il sistema operativo in uso. Per ulteriori informazioni, vedere [Microsoft Security advisory 2960358](https://technet.microsoft.com/security/advisory/2960358). Questo hotfix o una versione successiva potrebbe essere già installata nel server.

1. Per tutti i sistemi operativi impostare questa chiave del Registro di sistema e riavviare il server.
    ```
    HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319
    "SchUseStrongCrypto"=dword:00000001
    ```
1. Se si vuole anche abilitare TLS 1,2 tra il server del motore di sincronizzazione e una SQL Server remota, verificare che siano installate le versioni necessarie per il [supporto di tls 1,2 per Microsoft SQL Server](https://support.microsoft.com/kb/3135244).

## <a name="prerequisites-for-federation-installation-and-configuration"></a>Prerequisiti per l'installazione e la configurazione dei servizi federativi
### <a name="windows-remote-management"></a>Gestione remota Windows
Quando si usa Azure AD Connect per distribuire AD FS o il proxy dell'applicazione Web (WAP), verificare i requisiti seguenti:

* Se il server di destinazione è aggiunto a un dominio, verificare che Windows Remote Managed sia abilitato.
  * In una finestra di comando di PowerShell con privilegi elevati usare il comando `Enable-PSRemoting –force` .
* Se il server di destinazione è un computer WAP non appartenente al dominio, esistono un paio di requisiti aggiuntivi:
  * Nel computer di destinazione (computer WAP):
    * Verificare che il servizio Gestione remota Windows/WS-Management (WinRM) sia in esecuzione tramite lo snap-in servizi.
    * In una finestra di comando di PowerShell con privilegi elevati usare il comando `Enable-PSRemoting –force` .
  * Nel computer in cui è in esecuzione la procedura guidata (se il computer di destinazione non è aggiunto a un dominio o è un dominio non trusted):
    * In una finestra di comando di PowerShell con privilegi elevati usare il comando `Set-Item.WSMan:\localhost\Client\TrustedHosts –Value <DMZServerFQDN> -Force –Concatenate` .
    * In Server Manager:
      * Aggiungere un host WAP DMZ a un pool di computer. In Server Manager selezionare **Gestisci**  >  **Aggiungi server**e quindi utilizzare la scheda **DNS** .
      * Nella scheda **Server Manager tutti i server** fare clic con il pulsante destro del mouse sul server WAP e scegliere **Gestisci come**. Immettere le credenziali locali (non di dominio) per il computer WAP.
      * Per convalidare la connettività remota di PowerShell, nella scheda **Server Manager tutti i server** fare clic con il pulsante destro del mouse sul server WAP e scegliere **Windows PowerShell**. È necessario aprire una sessione di PowerShell remota per assicurarsi che sia possibile stabilire sessioni remote di PowerShell.

### <a name="tlsssl-certificate-requirements"></a>Requisiti del certificato TLS/SSL
* Si consiglia di utilizzare lo stesso certificato TLS/SSL in tutti i nodi della farm AD FS e in tutti i server proxy applicazione Web.
* Il certificato deve essere un certificato X509.
* È possibile utilizzare un certificato autofirmato su server federativi in un ambiente di testing. Per un ambiente di produzione, è consigliabile ottenere il certificato da un'autorità di certificazione pubblica.
  * Se si utilizza un certificato non attendibile pubblicamente, verificare che il certificato installato in ogni server proxy applicazione Web sia considerato attendibile sia nel server locale che in tutti i server federativi.
* L'identità del certificato deve corrispondere al nome del servizio federativo (ad esempio, sts.contoso.com).
  * L'identità è un'estensione del nome alternativo del soggetto (SAN) di tipo dNSName o, se non sono presenti voci SAN, il nome del soggetto viene specificato come nome comune.
  * Nel certificato possono essere presenti più voci SAN, purché una di esse corrisponda al nome del servizio federativo.
  * Se si prevede di usare Workplace Join, è necessaria una SAN aggiuntiva con il valore **enterpriseregistration.** seguito dal suffisso del nome dell'entità utente (UPN) dell'organizzazione, ad esempio enterpriseregistration.contoso.com.
* I certificati basati sulle chiavi CNG (CryptoAPI Next-Generation) e i provider di archiviazione chiavi (KSP) non sono supportati. Di conseguenza, è necessario utilizzare un certificato basato su un provider del servizio di crittografia (CSP) e non su un KSP.
* I certificati con caratteri jolly sono supportati.

### <a name="name-resolution-for-federation-servers"></a>Risoluzione dei nomi per i server federativi
* Configurare i record DNS per il nome del AD FS (ad esempio, sts.contoso.com) sia per la rete Intranet (il server DNS interno) sia per la rete Extranet (DNS pubblico attraverso il registrar di dominio). Per il record DNS Intranet, accertarsi di usare record A e non record CNAME. Per il corretto funzionamento dell'autenticazione di Windows dal computer aggiunto al dominio, è necessario utilizzare record A.
* Se si distribuiscono più server AD FS server o proxy applicazione Web, assicurarsi di aver configurato il servizio di bilanciamento del carico e che i record DNS per il nome del AD FS (ad esempio, sts.contoso.com) puntino al servizio di bilanciamento del carico.
* Per il funzionamento dell'autenticazione integrata di Windows per le applicazioni browser con Internet Explorer nella rete Intranet, verificare che il nome del AD FS (ad esempio, sts.contoso.com) venga aggiunto all'area Intranet in Internet Explorer. Questo requisito può essere controllato tramite Criteri di gruppo e distribuito in tutti i computer aggiunti al dominio.

## <a name="azure-ad-connect-supporting-components"></a>Componenti di supporto di Azure AD Connect
Azure AD Connect installa i componenti seguenti nel server in cui è installato Azure AD Connect. L'elenco riguarda un'istallazione di SQL Express di base. Se si sceglie di utilizzare un SQL Server diverso nella pagina **installazione dei servizi di sincronizzazione** , il database locale di SQL Express non viene installato localmente.

* Azure AD Connect Health
* Utilità della riga di comando di Microsoft SQL Server 2012
* Microsoft SQL Server 2012 Express LocalDB
* Client nativo di Microsoft SQL Server 2012
* Microsoft Visual C++ 2013 Redistribution Package

## <a name="hardware-requirements-for-azure-ad-connect"></a>Requisiti hardware per Azure AD Connect
La tabella seguente illustra i requisiti minimi per il computer di sincronizzazione Azure AD Connect.

| Numero di oggetti in Active Directory | CPU | Memoria | Dimensioni del disco rigido |
| --- | --- | --- | --- |
| Meno di 10.000 |1,6 GHz |4 GB |70 GB |
| 10.000-50.000 |1,6 GHz |4 GB |70 GB |
| 50.000-100.000 |1,6 GHz |16 GB |100 GB |
| Per 100.000 o più oggetti, è richiesta la versione completa di SQL Server | | | |
| 100.000-300.000 |1,6 GHz |32 GB |300 GB |
| 300.000-600.000 |1,6 GHz |32 GB |450 GB |
| Più di 600.000 |1,6 GHz |32 GB |500 GB |

I requisiti minimi per i computer che eseguono AD FS o i server proxy applicazione Web sono:

* CPU: dual core da 1,6 GHz o superiore
* Memoria: 2 GB o superiore
* Macchina virtuale di Azure: configurazione A2 o superiore

## <a name="next-steps"></a>Passaggi successivi
Altre informazioni su [Integrazione delle identità locali con Azure Active Directory](whatis-hybrid-identity.md).
