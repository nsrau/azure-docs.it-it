---
title: Azure VMware Solution by CloudSimple - Use Azure AD as identity source on Private Cloud
description: Descrive come aggiungere Azure AD come provider di identità nel cloud privato CloudSimple per autenticare gli utenti che accedono a CloudSimple da AzureDescribes how to add Azure AD as an identity provider on your CloudSimple Private Cloud to authenticate users accessing CloudSimple from Azure
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 674ca8bea110d60557d1e50e7b68c9c3f7a92bf2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77564585"
---
# <a name="use-azure-ad-as-an-identity-provider-for-vcenter-on-cloudsimple-private-cloud"></a>Usare Azure AD come provider di identità per vCenter nel cloud CloudSimple Private CloudUse Azure AD as an identity provider for vCenter on CloudSimple Private Cloud

È possibile configurare il vCenter CloudSimple Private Cloud per l'autenticazione con Azure Active Directory (Azure AD) per gli amministratori di VMware per accedere a vCenter.You can set up your CloudSimple Private Cloud vCenter to authenticate with Azure Active Directory (Azure AD) for your VMware administrators to access vCenter. Dopo aver configurato l'origine dell'identità Single Sign-On, l'utente **del proprietario cloud** può aggiungere utenti dall'origine dell'identità a vCenter.  

È possibile configurare il dominio di Active Directory e i controller di dominio in uno dei modi seguenti:

* Controller di dominio e di dominio di Active Directory in esecuzione in locale
* Controller di dominio e dominio di Active Directory in esecuzione in Azure come macchine virtuali nella sottoscrizione di Azure
* Nuovi controller di dominio e di dominio Di Active Directory in esecuzione nel cloud Simple Private Cloud
* Servizio Azure Active Directory

Questa guida illustra le attività necessarie per configurare Azure AD come origine di identità.  Per informazioni sull'uso di Active Directory locale o di Active Directory in esecuzione in Azure, vedere Configurare le origini di [identità di vCenter per l'uso](set-vcenter-identity.md) di Active Directory per istruzioni dettagliate sulla configurazione dell'origine identità.

## <a name="about-azure-ad"></a>Informazioni su Azure AD

Azure AD è il servizio di gestione delle identità e delle directory multi-tenant basato su cloud Microsoft.Azure AD is the Microsoft multi-tenant, cloud based directory and identity management service.  Azure AD offre un meccanismo di autenticazione scalabile, coerente e affidabile per consentire agli utenti di autenticare e accedere a servizi diversi in Azure.Azure AD provides a scalable, consistent and reliable authentication mechanism for users to authenticate and access different services on Azure.  Fornisce inoltre servizi LDAP sicuri per tutti i servizi di terze parti per l'utilizzo di Azure AD come origine di autenticazione/identità.  Azure AD combina i servizi directory di base, la governance avanzata delle identità e la gestione degli accessi alle applicazioni, che possono essere usati per consentire l'accesso al cloud privato per gli utenti che amministrano il cloud privato.

Per usare Azure AD come origine di identità con vCenter, è necessario configurare i servizi di dominio di Azure AD e Azure AD. Seguire queste istruzioni:

1. [Come configurare i servizi di dominio di Azure AD e Azure ADHow to set up Azure AD and Azure AD domain services](#set-up-azure-ad-and-azure-ad-domain-services)
2. [Come configurare un'origine di identità nel vCenter del cloud privato](#set-up-an-identity-source-on-your-private-cloud-vcenter)

## <a name="set-up-azure-ad-and-azure-ad-domain-services"></a>Configurare i servizi di dominio di Azure AD e Azure ADSet up Azure AD and Azure AD domain services

Prima di iniziare, è necessario accedere alla sottoscrizione di Azure con privilegi di amministratore globale.  I passaggi seguenti forniscono linee guida generali. I dettagli sono contenuti nella documentazione di Azure.Details are contained in the Azure documentation.

### <a name="azure-ad"></a>Azure AD

> [!NOTE]
> Se si dispone già di Azure AD, è possibile ignorare questa sezione.

1. Configurare Azure AD nella sottoscrizione come descritto nella documentazione di [Azure AD.](../active-directory/fundamentals/get-started-azure-ad.md)
2. Abilitare Azure Active Directory Premium nella sottoscrizione come descritto in [Iscriversi ad Azure Active Directory Premium](../active-directory/fundamentals/active-directory-get-started-premium.md).
3. Configurare un nome di dominio personalizzato e verificare il nome di dominio personalizzato come descritto in Aggiungere un nome di [dominio personalizzato ad Azure Active Directory.](../active-directory/fundamentals/add-custom-domain.md)
    1. Configurare un record DNS nel registrar con le informazioni fornite in Azure.Set up a DNS record on your domain registrar with the information provided on Azure.
    2. Impostare il nome di dominio personalizzato come dominio primario.

Facoltativamente, è possibile configurare altre funzionalità di Azure AD.  Questi non sono necessari per abilitare l'autenticazione vCenter con Azure AD.

### <a name="azure-ad-domain-services"></a>Servizi di dominio Azure ADAzure AD domain services

> [!NOTE]
> Si tratta di un passaggio importante per l'abilitazione di Azure AD come origine di identità per vCenter.This is an important step for enabling Azure AD as an identity source for vCenter.  Per evitare problemi, assicurarsi che tutti i passaggi vengano eseguiti correttamente.

1. Abilitare i servizi di dominio di Azure AD come descritto in [Abilitare](../active-directory-domain-services/active-directory-ds-getting-started.md)i servizi di dominio di Azure Active Directory usando il portale di Azure.Enable Azure AD domain services as described in Enable Azure Active Directory domain services using the Azure portal .
2. Configurare la rete che verrà usata dai servizi di dominio di Azure AD come descritto in Abilitare Servizi di [dominio Azure Active Directory tramite il portale](../active-directory-domain-services/active-directory-ds-getting-started-network.md)di Azure.
3. Configurare il gruppo di amministratori per la gestione di Servizi di dominio Azure AD come descritto in Abilitare Servizi di [dominio Azure Active Directory tramite il portale](../active-directory-domain-services/active-directory-ds-getting-started-admingroup.md)di Azure.
4. Aggiornare le impostazioni DNS per Servizi di dominio Azure AD come descritto in [Abilitare Servizi di dominio Azure Active Directory.](../active-directory-domain-services/active-directory-ds-getting-started-dns.md)  Se si desidera connettersi ad Active Directory tramite Internet, configurare il record DNS per l'indirizzo IP pubblico dei servizi di dominio di Azure AD sul nome di dominio.
5. Abilitare la sincronizzazione dell'hash delle password per gli utenti.  Questo passaggio consente la sincronizzazione degli hash delle password necessari per l'autenticazione NT LAN Manager (NTLM) e Kerberos in Servizi di dominio Azure AD. Al termine della configurazione della sincronizzazione dell'hash delle password, gli utenti potranno accedere al dominio gestito con le credenziali aziendali. Vedere [Abilitare la sincronizzazione dell'hash delle password con Servizi](../active-directory-domain-services/active-directory-ds-getting-started-password-sync.md)di dominio Azure Active Directory .
    1. Se sono presenti utenti solo cloud, è necessario modificare la password usando il pannello di accesso di Azure AD per garantire che gli errori delle password vengano archiviati nel formato richiesto da NTLM o Kerberos.If cloud-only users are present, they must change their password using <a href="http://myapps.microsoft.com/" target="_blank">Azure AD access panel</a> to ensure password has hehes are stored in the format required by NTLM or Kerberos.  Seguire le istruzioni in Abilitare la [sincronizzazione dell'hash delle password con il dominio gestito per gli account utente solo cloud.](../active-directory-domain-services/tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds)  Questo passaggio deve essere eseguito per i singoli utenti e per qualsiasi nuovo utente creato nella directory di Azure AD usando il portale di Azure o i cmdlet PowerShell di Azure AD. Gli utenti che richiedono l'accesso ai servizi di dominio di Azure AD devono usare il pannello di accesso di <a href="http://myapps.microsoft.com/" target="_blank">Azure AD</a> e accedere al proprio profilo per modificare la password.

        > [!NOTE]
        > Se l'organizzazione include account utente solo cloud, tutti gli utenti che devono usare Azure Active Directory Domain Services devono cambiare le proprie password. Un account utente solo cloud è un account creato nella directory di Azure AD tramite il portale di Azure o i cmdlet di Azure AD PowerShell. Questi account utente non vengono sincronizzati da una directory locale.

    2. Se si sincronizzano le password da Active Directory locale, seguire i passaggi nella [documentazione](../active-directory-domain-services/active-directory-ds-getting-started-password-sync-synced-tenant.md)di Active Directory .

6.  Configurare LDAP sicuro in Servizi di dominio Azure Active Directory come descritto in [Configurare LDAP sicuro (LDAPS) per un dominio gestito](../active-directory-domain-services/tutorial-configure-ldaps.md)di Servizi di dominio Azure AD.
    1. Caricare un certificato per l'utilizzo tramite LDAP sicuro come descritto nell'argomento Azure [ottenere un certificato per LDAP sicuro.](../active-directory-domain-services/tutorial-configure-ldaps.md#create-a-certificate-for-secure-ldap)  CloudSimple consiglia di utilizzare un certificato firmato emesso da un'autorità di certificazione per garantire che vCenter possa considerare attendibile il certificato.
    2. Abilitare LDAP sicuro come descritto [Abilita LDAP sicuro (LDAPS) per un dominio gestito](../active-directory-domain-services/tutorial-configure-ldaps.md)di Servizi di dominio Azure AD.
    3. Salvare la parte pubblica del certificato (senza la chiave privata) in formato CER per l'utilizzo con vCenter durante la configurazione dell'origine dell'identità.
    4. Se è necessario l'accesso a Internet ai servizi di dominio di Azure AD, abilitare l'opzione 'Consenti accesso sicuro a LDAP su Internet'.
    5. Aggiungere la regola di sicurezza in ingresso per il gruppo di sicurezza di rete di Servizi di dominio Azure AD per la porta TCP 636.Add the inbound security rule for the Azure AD Domain services NSG for TCP port 636.

## <a name="set-up-an-identity-source-on-your-private-cloud-vcenter"></a>Configurare un'origine di identità nel vCenter del cloud privato

1. [Riassegnare i privilegi](escalate-private-cloud-privileges.md) per il tuo vCenter di Private Cloud.
2. Raccogliere i parametri di configurazione necessari per l'impostazione dell'origine identità.

    | **Opzione** | **Descrizione** |
    |------------|-----------------|
    | **Nome** | Nome dell'origine dell'identità. |
    | **DN di base per gli utenti** | Nome distinto di base per gli utenti.  Per Azure AD, `OU=AADDC Users,DC=<domain>,DC=<domain suffix>` usare: Esempio: `OU=AADDC Users,DC=cloudsimplecustomer,DC=com`.|
    | **Nome di dominio** | FQDN del dominio, ad esempio example.com. Non fornire un indirizzo IP in questa casella di testo. |
    | **Alias di dominio** | *(opzionale)* Nome NetBIOS del dominio. Aggiungere il nome NetBIOS del dominio di Active Directory come alias dell'origine identità se si utilizzano le autenticazioni SSPI. |
    | **DN di base per gruppi** | Nome distinto di base per i gruppi. Per Azure AD, `OU=AADDC Users,DC=<domain>,DC=<domain suffix>` usare: Esempio:For Azure AD, use: Example: Example:`OU=AADDC Users,DC=cloudsimplecustomer,DC=com`|
    | **URL server primario** | Server LDAP del controller di dominio primario per il dominio.<br><br>Utilizzare il `ldaps://hostname:port`formato . La porta è in genere 636 per le connessioni LDAPS. <br><br>Un certificato che stabilisce l'attendibilità per l'endpoint LDAPS `ldaps://` del server Active Directory è necessario quando si utilizza nell'URL LDAP primario o secondario. |
    | **URL server secondario** | Indirizzo di un server LDAP del controller di dominio secondario utilizzato per il failover. |
    | **Scegliere il certificato** | Se si desidera utilizzare LDAPS con il server LDAP di Active Directory o l'origine identità del server OpenLDAP, viene visualizzato un pulsante Scegli certificato dopo aver digitato `ldaps://` la casella di testo URL. Non è necessario un URL secondario. |
    | **Nome utente** | ID di un utente nel dominio che dispone di un accesso minimo in sola lettura al DN di base per utenti e gruppi. |
    | **Password** | Password dell'utente specificato da Nome utente. |

3. Accedere al vCenter del cloud privato dopo l'escalation dei privilegi.
4. Seguire le istruzioni in [Aggiungere un'origine di identità in vCenter](set-vcenter-identity.md#add-an-identity-source-on-vcenter) usando i valori del passaggio precedente per configurare Azure Active Directory come origine di identità.
5. Aggiungere utenti/gruppi da Azure AD ai gruppi vCenter come descritto nell'argomento VMware [Aggiungere membri a un gruppo Single Sign-On vCenter](https://docs.vmware.com/en/VMware-vSphere/5.5/com.vmware.vsphere.security.doc/GUID-CDEA6F32-7581-4615-8572-E0B44C11D80D.html).

> [!CAUTION]
> I nuovi utenti devono essere aggiunti solo a *Cloud-Owner-Group*, *Cloud-Global-Cluster-Admin-Group*, *Cloud-Global-Storage-Admin-Group*, *Cloud-Global-Network-Admin-Group* o *Cloud-Global-VM-Admin-Group*.  Gli utenti aggiunti al gruppo *Administrators* verranno rimossi automaticamente.  Solo gli account di servizio devono essere aggiunti al gruppo *Administrators.Only* service accounts must be added to Administrators group.

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni sul modello di autorizzazione del cloud privato](learn-private-cloud-permissions.md)
