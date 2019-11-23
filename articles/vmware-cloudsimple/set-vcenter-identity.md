---
title: Soluzione VMware di Azure di CloudSimple-configurare le origini delle identità vCenter nel cloud privato
description: Viene descritto come configurare il cloud privato vCenter per l'autenticazione con Active Directory per gli amministratori VMware per accedere a vCenter
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 9d2986acc47087c267193eee43136e030abcc422
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/28/2019
ms.locfileid: "72990310"
---
# <a name="set-up-vcenter-identity-sources-to-use-active-directory"></a>Configurare le origini di identità vCenter da usare Active Directory

## <a name="about-vmware-vcenter-identity-sources"></a>Informazioni sulle origini di identità VMware vCenter

VMware vCenter supporta diverse origini di identità per l'autenticazione degli utenti che accedono a vCenter.  Il cloud privato di CloudSimple può essere configurato per l'autenticazione con Active Directory per consentire agli amministratori VMware di accedere a vCenter. Al termine dell'installazione, l'utente **cloudowner** può aggiungere utenti dall'origine identità a vCenter.  

È possibile configurare il dominio Active Directory e i controller di dominio in uno dei modi seguenti:

* Active Directory domini e controller di dominio in esecuzione in locale
* Active Directory controller di dominio e di dominio in esecuzione in Azure come macchine virtuali nella sottoscrizione di Azure
* Nuovi controller di dominio e dominio Active Directory in esecuzione nel cloud privato
* Servizio Azure Active Directory

Questa guida illustra le attività per la configurazione di Active Directory controller di dominio e di dominio in esecuzione in locale o come macchine virtuali nelle sottoscrizioni.  Se si vuole usare Azure AD come origine di identità, vedere [usare Azure ad come provider di identità per vCenter in CloudSimple cloud privato](azure-ad.md) per istruzioni dettagliate sulla configurazione dell'origine di identità.

Prima [di aggiungere un'origine di identità](#add-an-identity-source-on-vcenter), [inoltrare temporaneamente i privilegi di vCenter](escalate-private-cloud-privileges.md).

> [!CAUTION]
> I nuovi utenti devono essere aggiunti solo a *cloud-Owner-Group*, *cloud-Global-cluster-admin-* Group, *cloud-Global-Storage-admin-Group*, *cloud-Global-Network-Admin-Group* o, *cloud-Global-VM-admin-Group*.  Gli utenti aggiunti al gruppo *Administrators* verranno rimossi automaticamente.  Solo gli account di servizio devono essere aggiunti al gruppo *Administrators* .  


## <a name="identity-source-options"></a>Opzioni origine identità

* [Aggiungere Active Directory locali come origine Single Sign-On identità](#add-on-premises-active-directory-as-a-single-sign-on-identity-source)
* [Configurare nuovi Active Directory in un cloud privato](#set-up-new-active-directory-on-a-private-cloud)
* [Configurare Active Directory in Azure](#set-up-active-directory-on-azure)

## <a name="add-on-premises-active-directory-as-a-single-sign-on-identity-source"></a>Aggiungere Active Directory locali come origine dell'identità Single Sign-on

Per configurare la Active Directory locale come origine di identità Single Sign-on, è necessario:

* [Connessione VPN da sito a sito](vpn-gateway.md#set-up-a-site-to-site-vpn-gateway) dal Data Center locale al cloud privato.
* IP del server DNS locale aggiunto a vCenter e controller dei servizi di piattaforma (PSC).

Usare le informazioni nella tabella seguente durante la configurazione del dominio Active Directory.

| **Opzione** | **Descrizione** |
|------------|-----------------|
| **Nome** | Nome dell'origine di identità. |
| **DN di base per gli utenti** | Nome distinto di base per gli utenti. |
| **Nome di dominio** | FDQN del dominio, ad esempio example.com. Non specificare un indirizzo IP in questa casella di testo. |
| **Alias di dominio** | Nome NetBIOS del dominio. Se si usano le autenticazioni SSPI, aggiungere il nome NetBIOS del dominio Active Directory come alias dell'origine di identità. |
| **DN di base per i gruppi** | Nome distinto di base per i gruppi. |
| **URL server primario** | Server LDAP del controller di dominio primario per il dominio.<br><br>Usare il formato `ldap://hostname:port` o `ldaps://hostname:port`. La porta è in genere 389 per le connessioni LDAP e 636 per le connessioni LDAPs. Per Active Directory distribuzioni di controller multidominio, la porta è in genere 3268 per LDAP e 3269 per LDAPs.<br><br>Quando si usa `ldaps://` nell'URL LDAP primario o secondario, è necessario un certificato che stabilisce l'attendibilità per l'endpoint LDAPs del server Active Directory. |
| **URL server secondario** | Indirizzo di un server LDAP del controller di dominio secondario usato per il failover. |
| **Scegliere il certificato** | Se si vuole usare LDAPs con il server Active Directory LDAP o con l'origine identità del server OpenLDAP, viene visualizzato un pulsante Scegli certificato dopo aver digitato `ldaps://` nella casella di testo URL. Un URL secondario non è obbligatorio. |
| **Nome utente** | ID di un utente nel dominio che dispone almeno dell'accesso in sola lettura al DN di base per utenti e gruppi. |
| **Password** | Password dell'utente specificato dal nome utente. |

Quando si hanno le informazioni nella tabella precedente, è possibile aggiungere la Active Directory locale come origine di identità Single Sign-on in vCenter.

> [!TIP]
> Altre informazioni sulle origini di identità Single Sign-on sono disponibili nella [pagina della documentazione di VMware](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.psc.doc/GUID-B23B1360-8838-4FF2-B074-71643C4CB040.html).

## <a name="set-up-new-active-directory-on-a-private-cloud"></a>Configurare nuovi Active Directory in un cloud privato

È possibile configurare un nuovo dominio di Active Directory nel cloud privato e usarlo come origine di identità per l'accesso Single Sign-on.  Il Active Directory dominio può essere parte di una foresta Active Directory esistente oppure può essere configurato come una foresta indipendente.

### <a name="new-active-directory-forest-and-domain"></a>Nuova foresta e dominio Active Directory

Per configurare una nuova foresta Active Directory e un nuovo dominio, è necessario:

* Una o più macchine virtuali che eseguono Microsoft Windows Server da utilizzare come controller di dominio per la nuova foresta e dominio Active Directory.
* Una o più macchine virtuali che eseguono il servizio DNS per la risoluzione dei nomi.

Per la procedura dettagliata, vedere [installare una nuova foresta di Windows Server 2012 Active Directory](https://docs.microsoft.com/windows-server/identity/ad-ds/deploy/install-a-new-windows-server-2012-active-directory-forest--level-200-) .

> [!TIP]
> Per la disponibilità elevata dei servizi, è consigliabile configurare più controller di dominio e server DNS.

Dopo aver configurato la foresta e il dominio Active Directory, è possibile [aggiungere un'origine di identità in vCenter](#add-an-identity-source-on-vcenter) per la nuova Active Directory.

### <a name="new-active-directory-domain-in-an-existing-active-directory-forest"></a>Nuovo Active Directory dominio in una foresta Active Directory esistente

Per configurare un nuovo dominio di Active Directory in una foresta Active Directory esistente, è necessario quanto segue:

* Connessione VPN da sito a sito al percorso della foresta Active Directory.
* Server DNS per risolvere il nome della foresta Active Directory esistente.

Per i passaggi dettagliati, vedere [installare un nuovo Windows Server 2012 Active Directory dominio figlio o albero](https://docs.microsoft.com/windows-server/identity/ad-ds/deploy/install-a-new-windows-server-2012-active-directory-child-or-tree-domain--level-200-) .

Dopo aver configurato il dominio di Active Directory, è possibile [aggiungere un'origine di identità in vCenter](#add-an-identity-source-on-vcenter) per la nuova Active Directory.

## <a name="set-up-active-directory-on-azure"></a>Configurare Active Directory in Azure

Active Directory in esecuzione in Azure è simile a Active Directory in esecuzione in locale.  Per configurare Active Directory in esecuzione in Azure come origine di identità Single Sign-on in vCenter, il server vCenter e PSC devono disporre di connettività di rete alla rete virtuale di Azure in cui sono in esecuzione Active Directory servizi.  È possibile stabilire questa connettività usando la [connessione di rete virtuale di Azure usando ExpressRoute](azure-expressroute-connection.md) dalla rete virtuale di Azure in cui Active Directory servizi sono in esecuzione nel cloud privato CloudSimple.

Dopo aver stabilito la connessione di rete, seguire la procedura descritta in [aggiungere Active Directory locali come origine di identità Single Sign-on](#add-on-premises-active-directory-as-a-single-sign-on-identity-source) per aggiungerla come origine di identità.  

## <a name="add-an-identity-source-on-vcenter"></a>Aggiungere un'origine di identità in vCenter

1. [Escalation dei privilegi](escalate-private-cloud-privileges.md) sul cloud privato.

2. Accedere a vCenter per il cloud privato.

3. Selezionare **Home > amministrazione**.

    ![amministrazione](media/OnPremAD01.png)

4. Selezionare **Single Sign-On > Configuration**.

    ![Single Sign On](media/OnPremAD02.png)

5. Aprire la scheda **origini identità** e fare clic su **+** per aggiungere una nuova origine identità.

    ![Origini identità](media/OnPremAD03.png)

6. Selezionare **Active Directory come server LDAP** e fare clic su **Avanti**.

    ![Active Directory](media/OnPremAD04.png)

7. Specificare i parametri di origine dell'identità per l'ambiente e fare clic su **Avanti**.

    ![Active Directory](media/OnPremAD05.png)

8. Verificare le impostazioni e fare clic su **fine**.
