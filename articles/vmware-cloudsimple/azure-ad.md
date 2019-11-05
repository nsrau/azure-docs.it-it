---
title: 'Soluzione VMware di Azure di CloudSimple: usare Azure AD come origine delle identità nel cloud privato'
description: Viene descritto come aggiungere Azure AD come provider di identità nel cloud privato di CloudSimple per autenticare gli utenti che accedono a CloudSimple da Azure
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 1a5871a052998e9dd32d698c5a89f57064cc7d6b
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/28/2019
ms.locfileid: "72987573"
---
# <a name="use-azure-ad-as-an-identity-provider-for-vcenter-on-cloudsimple-private-cloud"></a>Usare Azure AD come provider di identità per vCenter nel cloud privato CloudSimple

È possibile configurare il cloud privato di CloudSimple per l'autenticazione con Azure Active Directory (Azure AD) per gli amministratori VMware per accedere a vCenter. Dopo aver configurato l'origine Single Sign-On identità, l'utente **cloudowner** può aggiungere utenti dall'origine identità a vCenter.  

È possibile configurare il dominio Active Directory e i controller di dominio in uno dei modi seguenti:

* Active Directory domini e controller di dominio in esecuzione in locale
* Active Directory controller di dominio e di dominio in esecuzione in Azure come macchine virtuali nella sottoscrizione di Azure
* Nuovo Active Directory di dominio e controller di dominio in esecuzione nel cloud privato CloudSimple
* Servizio Azure Active Directory

Questa guida illustra le attività necessarie per configurare Azure AD come origine di identità.  Per informazioni sull'uso di Active Directory locali o Active Directory in esecuzione in Azure, vedere [configurare le origini di identità vCenter da usare Active Directory](set-vcenter-identity.md) per istruzioni dettagliate per la configurazione dell'origine di identità.

## <a name="about-azure-ad"></a>Informazioni su Azure AD

Azure AD è il servizio Microsoft multi-tenant, Directory basato sul cloud e gestione delle identità.  Azure AD fornisce un meccanismo di autenticazione scalabile, coerente e affidabile per consentire agli utenti di autenticare e accedere a servizi diversi in Azure.  Fornisce anche servizi LDAP sicuri per i servizi di terze parti da usare Azure AD come origine di autenticazione/identità.  Azure AD combina servizi directory principali, governance avanzata delle identità e gestione dell'accesso alle applicazioni, che possono essere usati per concedere l'accesso al cloud privato per gli utenti che amministrano il cloud privato.

Per usare Azure AD come origine di identità con vCenter, è necessario configurare Azure AD e Azure AD Domain Services. Seguire queste istruzioni:

1. [Come configurare Azure AD e Azure AD servizi di dominio](#set-up-azure-ad-and-azure-ad-domain-services)
2. [Come configurare un'origine di identità nel cloud privato vCenter](#set-up-an-identity-source-on-your-private-cloud-vcenter)

## <a name="set-up-azure-ad-and-azure-ad-domain-services"></a>Configurare Azure AD e Azure AD servizi di dominio

Prima di iniziare, sarà necessario accedere alla sottoscrizione di Azure con i privilegi di amministratore globale.  Nei passaggi seguenti vengono fornite linee guida generali. I dettagli sono contenuti nella documentazione di Azure.

### <a name="azure-ad"></a>Azure AD

> [!NOTE]
> Se si dispone già di Azure AD, è possibile ignorare questa sezione.

1. Configurare Azure AD sulla sottoscrizione come descritto in [Azure ad documentazione](../active-directory/fundamentals/get-started-azure-ad.md).
2. Abilitare Azure Active Directory Premium sulla sottoscrizione come descritto in [iscriversi a Azure Active Directory Premium](../active-directory/fundamentals/active-directory-get-started-premium.md).
3. Configurare un nome di dominio personalizzato e verificare il nome di dominio personalizzato come descritto in [aggiungere un nome di dominio personalizzato a Azure Active Directory](../active-directory/fundamentals/add-custom-domain.md).
    1. Configurare un record DNS nel registrar con le informazioni fornite in Azure.
    2. Impostare il nome di dominio personalizzato come dominio primario.

Facoltativamente, è possibile configurare altre funzionalità di Azure AD.  Questi non sono necessari per l'abilitazione dell'autenticazione vCenter con Azure AD.

### <a name="azure-ad-domain-services"></a>Servizi di dominio Azure AD

> [!NOTE]
> Si tratta di un passaggio importante per abilitare Azure AD come origine di identità per vCenter.  Per evitare problemi, assicurarsi che tutti i passaggi vengano eseguiti correttamente.

1. Abilitare i servizi di dominio Azure AD come descritto in [abilitare servizi di dominio Azure Active Directory utilizzando il portale di Azure](../active-directory-domain-services/active-directory-ds-getting-started.md).
2. Configurare la rete che verrà usata dai servizi del dominio Azure AD come descritto in [abilitare Azure Active Directory Domain Services con il portale di Azure](../active-directory-domain-services/active-directory-ds-getting-started-network.md).
3. Configurare il gruppo di amministratori per la gestione di Azure AD Domain Services come descritto in [abilitare Azure Active Directory Domain Services con il portale di Azure](../active-directory-domain-services/active-directory-ds-getting-started-admingroup.md).
4. Aggiornare le impostazioni DNS per il Azure AD Domain Services come descritto in [abilitare Azure Active Directory Domain Services](../active-directory-domain-services/active-directory-ds-getting-started-dns.md).  Se si desidera connettersi ad Active Directory tramite Internet, impostare il record DNS per l'indirizzo IP pubblico del Azure AD servizi di dominio sul nome di dominio.
5. Abilitare la sincronizzazione dell'hash delle password per gli utenti.  Questo passaggio consente la sincronizzazione degli hash delle password necessari per l'autenticazione NTLM (NT LAN Manager) e Kerberos per Azure AD Domain Services. Al termine della configurazione della sincronizzazione dell'hash delle password, gli utenti potranno accedere al dominio gestito con le credenziali aziendali. Vedere [abilitare la sincronizzazione dell'hash delle password per Azure Active Directory Domain Services](../active-directory-domain-services/active-directory-ds-getting-started-password-sync.md).
    1. Se sono presenti utenti solo cloud, è necessario modificare la password usando <a href="http://myapps.microsoft.com/" target="_blank">Azure ad pannello di accesso</a> per assicurarsi che gli hash delle password siano archiviati nel formato richiesto da NTLM o Kerberos.  Seguire le istruzioni riportate in [abilitare la sincronizzazione dell'hash delle password nel dominio gestito per gli account utente solo cloud](../active-directory-domain-services/tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds).  Questo passaggio deve essere eseguito per singoli utenti e per qualsiasi nuovo utente creato nella directory Azure AD usando i cmdlet di portale di Azure o Azure AD PowerShell. Gli utenti che richiedono l'accesso a Azure AD Domain Services devono usare il <a href="http://myapps.microsoft.com/" target="_blank">Pannello di accesso Azure ad</a> e accedere al proprio profilo per modificare la password.

        > [!NOTE]
        > Se l'organizzazione dispone di account utente solo cloud, tutti gli utenti che devono usare Azure Active Directory Domain Services necessario modificare le password. Un account utente solo cloud è un account creato nella directory di Azure AD tramite il portale di Azure o i cmdlet di Azure AD PowerShell. Questi account utente non vengono sincronizzati da una directory locale.

    2. Se si sincronizzano le password da Active Directory locale, seguire la procedura descritta nella [documentazione Active Directory](../active-directory-domain-services/active-directory-ds-getting-started-password-sync-synced-tenant.md).

6.  Configurare LDAP sicuro nel Azure Active Directory Domain Services come descritto in [configurare LDAP sicuro (LDAPS) per un dominio gestito Azure ad Domain Services](../active-directory-domain-services/tutorial-configure-ldaps.md).
    1. Caricare un certificato per l'uso con LDAP sicuro, come descritto nell'argomento Azure [ottenere un certificato per l'LDAP sicuro](../active-directory-domain-services/tutorial-configure-ldaps.md#create-a-certificate-for-secure-ldap).  CloudSimple consiglia di usare un certificato firmato emesso da un'autorità di certificazione per assicurarsi che vCenter possa considerare attendibile il certificato.
    2. Abilitare LDAP sicuro come descritto in [abilitare LDAP sicuro (LDAPS) per un dominio gestito Azure ad Domain Services](../active-directory-domain-services/tutorial-configure-ldaps.md).
    3. Salvare la parte pubblica del certificato (senza la chiave privata) nel formato CER per l'uso con vCenter durante la configurazione dell'origine identità.
    4. Se è richiesto l'accesso a Internet ai servizi del dominio Azure AD, abilitare l'opzione "Consenti accesso sicuro a LDAP su Internet".
    5. Aggiungere la regola di sicurezza in ingresso per la NSG di servizi di dominio Azure AD per la porta TCP 636.

## <a name="set-up-an-identity-source-on-your-private-cloud-vcenter"></a>Configurare un'origine di identità nel cloud privato vCenter

1. [Escalation dei privilegi](escalate-private-cloud-privileges.md) per il cloud privato vCenter.
2. Raccogliere i parametri di configurazione necessari per la configurazione dell'origine identità.

    | **Opzione** | **Descrizione** |
    |------------|-----------------|
    | **Nome** | Nome dell'origine di identità. |
    | **DN di base per gli utenti** | Nome distinto di base per gli utenti.  Per Azure AD, usare: `OU=AADDC Users,DC=<domain>,DC=<domain suffix>` esempio: `OU=AADDC Users,DC=cloudsimplecustomer,DC=com`.|
    | **Nome di dominio** | FDQN del dominio, ad esempio example.com. Non specificare un indirizzo IP in questa casella di testo. |
    | **Alias di dominio** | *(facoltativo)* Nome NetBIOS del dominio. Se si usano le autenticazioni SSPI, aggiungere il nome NetBIOS del dominio Active Directory come alias dell'origine di identità. |
    | **DN di base per i gruppi** | Nome distinto di base per i gruppi. Per Azure AD, usare: `OU=AADDC Users,DC=<domain>,DC=<domain suffix>` esempio: `OU=AADDC Users,DC=cloudsimplecustomer,DC=com`|
    | **URL server primario** | Server LDAP del controller di dominio primario per il dominio.<br><br>Usare il formato `ldaps://hostname:port`. La porta è in genere 636 per le connessioni LDAPs. <br><br>Quando si usa `ldaps://` nell'URL LDAP primario o secondario, è necessario un certificato che stabilisce l'attendibilità per l'endpoint LDAPs del server Active Directory. |
    | **URL server secondario** | Indirizzo di un server LDAP del controller di dominio secondario usato per il failover. |
    | **Scegliere il certificato** | Se si vuole usare LDAPs con il server Active Directory LDAP o con l'origine identità del server OpenLDAP, viene visualizzato un pulsante Scegli certificato dopo aver digitato `ldaps://` nella casella di testo URL. Un URL secondario non è obbligatorio. |
    | **Nome utente** | ID di un utente nel dominio che dispone almeno dell'accesso in sola lettura al DN di base per utenti e gruppi. |
    | **Password** | Password dell'utente specificato dal nome utente. |

3. Accedere al cloud privato vCenter dopo l'escalation dei privilegi.
4. Seguire le istruzioni in [aggiungere un'origine di identità in vCenter](set-vcenter-identity.md#add-an-identity-source-on-vcenter) usando i valori del passaggio precedente per configurare Azure Active Directory come origine di identità.
5. Aggiungere utenti/gruppi da Azure AD ai gruppi vCenter come descritto nell'argomento relativo all' [aggiunta di membri a un gruppo vCenter Single Sign-on](https://docs.vmware.com/en/VMware-vSphere/5.5/com.vmware.vsphere.security.doc/GUID-CDEA6F32-7581-4615-8572-E0B44C11D80D.html)in VMware.

> [!CAUTION]
> I nuovi utenti devono essere aggiunti solo a *cloud-Owner-Group*, *cloud-Global-cluster-admin-* Group, *cloud-Global-Storage-admin-Group*, *cloud-Global-Network-Admin-Group* o, *cloud-Global-VM-admin-Group*.  Gli utenti aggiunti al gruppo *Administrators* verranno rimossi automaticamente.  Solo gli account di servizio devono essere aggiunti al gruppo *Administrators* .

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni sul modello di autorizzazione del cloud privato](learn-private-cloud-permissions.md)
