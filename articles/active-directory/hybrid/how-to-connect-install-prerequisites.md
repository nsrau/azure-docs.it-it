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
ms.date: 02/27/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6446b039d90e04c9fe7fca28b361f620183a0292
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2020
ms.locfileid: "80875742"
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
>Il server Azure AD Connect contiene dati di identità critici e deve essere considerato come un componente di livello 0 come documentato nel modello di [livello amministrativo](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material) di Active Directory

* Azure AD Connect non può essere installato su Small Business Server o Windows Server Essentials prima del 2019 (Windows Server Essentials 2019 è supportato). Il server deve utilizzare Windows Server Standard o versione successiva.
* L'installazione di Azure AD Connect in un controller di dominio non è consigliata a causa di procedure di sicurezza e impostazioni più restrittive che possono impedire la corretta installazione di Azure AD Connect.Installing Azure AD Connect on a Domain Controller is not recommended due to security practices and more restrictive settings that can prevent Azure AD Connect from installing correctly.
* Il server Azure AD Connect deve avere un'interfaccia utente grafica completa installata. **Non è supportata** l'installazione su Server Core.
>[!IMPORTANT]
>L'installazione di Azure AD Connect in un server aziendale di piccole dimensioni, in elementi essenziali del server o in un core del server non è supportata.

* Azure AD Connect deve essere installato in Windows Server 2012 o versione successiva. Questo server deve essere aggiunto a un dominio e può essere un controller di dominio o un server membro.
* Il server Azure AD Connect non deve avere Criteri di gruppo di trascrizione PowerShell abilitato se si usa la procedura guidata di Azure AD Connect per gestire la configurazione di ADFS. Se si usa la procedura guidata di Azure AD Connect per gestire la configurazione della sincronizzazione, è possibile abilitare la trascrizione di PowerShell.
* Se viene distribuito Active Directory Federation Services, i server in cui viene installato AD FS o il proxy dell'applicazione Web devono essere Windows Server 2012 R2 o versione successiva. [Gestione remota Windows](#windows-remote-management) .
* Se è in corso la distribuzione di Active Directory Federation Services, sono necessari [certificati TLS/SSL](#tlsssl-certificate-requirements).
* Se viene distribuito Active Directory Federation Services, è necessario configurare la [risoluzione dei nomi](#name-resolution-for-federation-servers).
* Se gli amministratori globali hanno abilitato **https://secure.aadcdn.microsoftonline-p.com** l'autenticazione a più fattori, l'URL deve essere presente nell'elenco dei siti attendibili. Viene richiesto di aggiungere il sito all'elenco dei siti attendibili quando viene richiesta la verifica MFA, se non è stata aggiunta prima. È possibile usare Internet Explorer per aggiungerlo ai siti attendibili.
* Microsoft consiglia di eseguire la protezione avanzata del server Azure AD Connect per ridurre la superficie di attacco alla sicurezza per questo componente critico dell'ambiente IT.  Seguire le indicazioni riportate di seguito ridurrà i rischi per la sicurezza dell'organizzazione.

* Distribuire Azure AD Connect in un server aggiunto a un dominio e limitare l'accesso amministrativo agli amministratori di dominio o ad altri gruppi di sicurezza strettamente controllati.

Per altre informazioni, vedere: 

* [Protezione dei gruppi di amministratori](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/appendix-g--securing-administrators-groups-in-active-directory)

* [Protezione degli account amministratore predefiniti](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/appendix-d--securing-built-in-administrator-accounts-in-active-directory)

* [Miglioramento e mantenimento della sicurezza riducendo le superfici di attacco](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access#2-reduce-attack-surfaces )

* [Riduzione della superficie di attacco di Active Directory](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/reducing-the-active-directory-attack-surface)

### <a name="sql-server-used-by-azure-ad-connect"></a>SQL Server usato da Azure AD Connect
* Per archiviare i dati sull'identità, Azure AD Connect richiede un database SQL. Per impostazione predefinita viene installato SQL Server 2012 Express LocalDB (una versione ridotta di SQL Server Express). SQL Server Express ha un limite di dimensioni di 10 GB che consente di gestire circa 100.000 oggetti. Se è necessario gestire un numero di oggetti directory maggiore, si dovrà installare una versione di SQL Server diversa. Il tipo di installazione di SQL Server può influire sulle [prestazioni di Azure AD Connect.](https://docs.microsoft.com/azure/active-directory/hybrid/plan-connect-performance-factors#sql-database-factors)
* Se si utilizza un'installazione diversa di SQL Server, si applicano i requisiti seguenti:If you use a different installation of SQL Server, then these requirements apply:
  * Azure AD Connect supporta tutte le versioni di Microsoft SQL Server da 2012 (con service Pack più recente) a SQL Server 2019. Il database SQL di Microsoft Azure **non è supportato** come database.
  * È necessario usare regole di confronto SQL senza distinzione tra maiuscole e minuscole. Queste regole di confronto sono identificate da \_CI_ all'interno del nome. **Non è supportato** l'uso di regole di confronto con distinzione tra maiuscole e minuscole identificate da _CS\_ all'interno del nome.
  * È possibile avere un unico motore di sincronizzazione per istanza SQL. **Non è supportata** la condivisione dell'istanza SQL con la sincronizzazione FIM/MIM, DirSync o Azure AD Sync.

### <a name="accounts"></a>Account
* Un account amministratore globale di Azure AD per il tenant di Azure AD con cui si vuole eseguire l'integrazione. Questo account deve essere un **account aziendale o dell'istituto di istruzione** e non un **account Microsoft**.
* Se si utilizzano [impostazioni rapide](reference-connect-accounts-permissions.md#express-settings-installation) o si esegue l'aggiornamento da DirSync, è necessario disporre di un account amministratore dell'organizzazione per Active Directory locale.
* Se si utilizza il percorso di installazione delle impostazioni personalizzate, sono disponibili più opzioni. Per ulteriori informazioni, consultate [Impostazioni di installazione personalizzate.](reference-connect-accounts-permissions.md#custom-installation-settings)

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

* Se il server proxy richiede l'autenticazione, l'[account del servizio](reference-connect-accounts-permissions.md#adsync-service-account) deve trovarsi nel dominio ed è necessario specificare un [account del servizio personalizzato](how-to-connect-install-custom.md#install-required-components) tramite il percorso di installazione delle impostazioni personalizzate. È inoltre necessaria una modifica diversa apportata a machine.config. Con questa modifica in machine.config, l'installazione guidata e il motore di sincronizzazione rispondono alle richieste di autenticazione dal server proxy. In tutte le pagine dell'istallazione guidata, esclusa la pagina **Configura**, vengono usate le credenziali dell'utente che ha effettuato l'accesso. Nella pagina **Configura** alla fine dell'installazione guidata il contesto passa all'[account del servizio](reference-connect-accounts-permissions.md#adsync-service-account) creato. La sezione machine.config dovrebbe essere simile alla seguente sezione.

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

### <a name="other"></a>Altri
* Facoltativo: un account utente di prova per verificare la sincronizzazione.

## <a name="component-prerequisites"></a>Prerequisiti dei componenti
### <a name="powershell-and-net-framework"></a>PowerShell e .NET Framework
Azure AD Connect si basa su Microsoft PowerShell e .NET Framework 4.5.1. Nel server deve essere installata questa versione o una versione successiva. In base alla versione di Windows Server, seguire questa procedura:

* Windows Server 2012 R2
  * Microsoft PowerShell viene installato per impostazione predefinita, Non è richiesta alcuna azione.
  * .NET Framework 4.5.1 e versioni successive sono disponibili tramite Windows Update. Assicurarsi di aver installato gli aggiornamenti più recenti per Windows Server nel Pannello di controllo.
* Windows Server 2012
  * La versione più recente di Microsoft PowerShell è disponibile in **Windows Management Framework 4.0**nell' [Area download Microsoft](https://www.microsoft.com/downloads).
  * .NET Framework 4.5.1 e versioni successive sono disponibili nell' [Area download Microsoft](https://www.microsoft.com/downloads).


### <a name="enable-tls-12-for-azure-ad-connect"></a>Abilitare TLS 1.2 per Azure AD Connect
Prima della versione 1.1.614.0, per impostazione predefinita Azure AD Connect usa TLS 1.0 per crittografare le comunicazioni tra il server del motore di sincronizzazione e Azure AD. È possibile modificare questa impostazione configurando le applicazioni .NET per l'utilizzo di TLS 1.2 per impostazione predefinita nel server. Altre informazioni su TLS 1.2 sono disponibili nell'articolo [Advisory Microsoft sulla sicurezza 2960358](https://technet.microsoft.com/security/advisory/2960358).

1.  Assicurarsi che sia installato l'hotfix .NET 4.5.1 per il sistema operativo, vedere [Microsoft Security Advisory 2960358](https://technet.microsoft.com/security/advisory/2960358). Questo hotfix o una versione successiva potrebbe essere già installata nel server.
    ```
2. For all operating systems, set this registry key and restart the server.
    ```
    HKEY_LOCAL_MACHINE SOFTWARE Microsoft\.NETFramework v4.0.30319 "SchUseStrongCrypto" , dword:00000001
    ```
4. If you also want to enable TLS 1.2 between the sync engine server and a remote SQL Server, then make sure you have the required versions installed for [TLS 1.2 support for Microsoft SQL Server](https://support.microsoft.com/kb/3135244).

## Prerequisites for federation installation and configuration
### Windows Remote Management
When using Azure AD Connect to deploy Active Directory Federation Services or the Web Application Proxy, check these requirements:

* If the target server is domain joined, then ensure that Windows Remote Managed is enabled
  * In an elevated PowerShell command window, use command `Enable-PSRemoting –force`
* If the target server is a non-domain joined WAP machine, then there are a couple of additional requirements
  * On the target machine (WAP machine):
    * Ensure the winrm (Windows Remote Management / WS-Management) service is running via the Services snap-in
    * In an elevated PowerShell command window, use command `Enable-PSRemoting –force`
  * On the machine on which the wizard is running (if the target machine is non-domain joined or untrusted domain):
    * In an elevated PowerShell command window, use the command `Set-Item WSMan:\localhost\Client\TrustedHosts –Value <DMZServerFQDN> -Force –Concatenate`
    * In Server Manager:
      * add DMZ WAP host to machine pool (server manager -> Manage -> Add Servers...use DNS tab)
      * Server Manager All Servers tab: right click WAP server and choose Manage As..., enter local (not domain) creds for the WAP machine
      * To validate remote PowerShell connectivity, in the Server Manager All Servers tab: right click WAP server and choose Windows PowerShell. A remote PowerShell session should open to ensure remote PowerShell sessions can be established.

### TLS/SSL Certificate Requirements
* It's strongly recommended to use the same TLS/SSL certificate across all nodes of your AD FS farm and all Web Application proxy servers.
* The certificate must be an X509 certificate.
* You can use a self-signed certificate on federation servers in a test lab environment. However, for a production environment, we recommend that you obtain the certificate from a public CA.
  * If using a certificate that is not publicly trusted, ensure that the certificate installed on each Web Application Proxy server is trusted on both the local server and on all federation servers
* The identity of the certificate must match the federation service name (for example, sts.contoso.com).
  * The identity is either a subject alternative name (SAN) extension of type dNSName or, if there are no SAN entries, the subject name specified as a common name.  
  * Multiple SAN entries can be present in the certificate, provided one of them matches the federation service name.
  * If you are planning to use Workplace Join, an additional SAN is required with the value **enterpriseregistration.** followed by the User Principal Name (UPN) suffix of your organization, for example, **enterpriseregistration.contoso.com**.
* Certificates based on CryptoAPI next generation (CNG) keys and key storage providers are not supported. This means you must use a certificate based on a CSP (cryptographic service provider) and not a KSP (key storage provider).
* Wild-card certificates are supported.

### Name resolution for federation servers
* Set up DNS records for the AD FS federation service name (for example sts.contoso.com) for both the intranet (your internal DNS server) and the extranet (public DNS through your domain registrar). For the intranet DNS record, ensure that you use A records and not CNAME records. This is required for windows authentication to work correctly from your domain joined machine.
* If you are deploying more than one AD FS server or Web Application Proxy server, then ensure that you have configured your load balancer and that the DNS records for the AD FS federation service name (for example sts.contoso.com) point to the load balancer.
* For windows integrated authentication to work for browser applications using Internet Explorer in your intranet, ensure that the AD FS federation service name (for example sts.contoso.com) is added to the intranet zone in IE. This can be controlled via group policy and deployed to all your domain joined computers.

## Azure AD Connect supporting components
The following is a list of components that Azure AD Connect installs on the server where Azure AD Connect is installed. This list is for a basic Express installation. If you choose to use a different SQL Server on the Install synchronization services page, then SQL Express LocalDB is not installed locally.

* Azure AD Connect Health
* Microsoft SQL Server 2012 Command Line Utilities
* Microsoft SQL Server 2012 Express LocalDB
* Microsoft SQL Server 2012 Native Client
* Microsoft Visual C++ 2013 Redistribution Package

## Hardware requirements for Azure AD Connect
The table below shows the minimum requirements for the Azure AD Connect sync computer.

| Number of objects in Active Directory | CPU | Memory | Hard drive size |
| --- | --- | --- | --- |
| Fewer than 10,000 |1.6 GHz |4 GB |70 GB |
| 10,000–50,000 |1.6 GHz |4 GB |70 GB |
| 50,000–100,000 |1.6 GHz |16 GB |100 GB |
| For 100,000 or more objects the full version of SQL Server is required | | | |
| 100,000–300,000 |1.6 GHz |32 GB |300 GB |
| 300,000–600,000 |1.6 GHz |32 GB |450 GB |
| More than 600,000 |1.6 GHz |32 GB |500 GB |

The minimum requirements for computers running AD FS or Web Application Proxy Servers is the following:

* CPU: Dual core 1.6 GHz or higher
* MEMORY: 2 GB or higher
* Azure VM: A2 configuration or higher

## Next steps
Learn more about [Integrating your on-premises identities with Azure Active Directory](whatis-hybrid-identity.md).
