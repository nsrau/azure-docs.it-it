---
title: Azure VMware Solution by CloudSimple - Configurare le origini di identità di vCenter nel cloud privato
description: Descrive come configurare il vCenter del cloud privato per l'autenticazione con Active Directory per gli amministratori di VMware per accedere a vCenter
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 5355e43ca6ac075e76a76ceb51be135cf4b62b0a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77564024"
---
# <a name="set-up-vcenter-identity-sources-to-use-active-directory"></a>Configurare le origini di identità del vCenter per l'utilizzo di Active Directory

## <a name="about-vmware-vcenter-identity-sources"></a>Informazioni sulle origini di identità VMware vCenter

VMware vCenter supporta diverse origini di identità per l'autenticazione degli utenti che accedono a vCenter.  Il vCenter CloudSimple Private Cloud può essere configurato per l'autenticazione con Active Directory per l'accesso degli amministratori VMware al vCenter. Al termine della configurazione, l'utente del proprietario di cloud può aggiungere utenti dall'origine identità a vCenter.When the setup is complete, the **cloudowner** user can add users from the identity source to vCenter.  

È possibile configurare il dominio di Active Directory e i controller di dominio in uno dei modi seguenti:

* Controller di dominio e di dominio di Active Directory in esecuzione in locale
* Controller di dominio e dominio di Active Directory in esecuzione in Azure come macchine virtuali nella sottoscrizione di Azure
* Nuovi controller di dominio e di dominio di Active Directory in esecuzione nel cloud privato
* Servizio Azure Active Directory

In questa guida vengono illustrate le attività per configurare i controller di dominio e di dominio di Active Directory in esecuzione in locale o come macchine virtuali nelle sottoscrizioni.  Se si vuole usare Azure AD come origine di identità, vedere Usare Azure AD come provider di [identità per vCenter nel cloudSimple Private Cloud](azure-ad.md) per istruzioni dettagliate sulla configurazione dell'origine di identità.

Prima di [aggiungere un'origine di identità](#add-an-identity-source-on-vcenter), riassegnare temporaneamente [i privilegi di vCenter.](escalate-private-cloud-privileges.md)

> [!CAUTION]
> I nuovi utenti devono essere aggiunti solo a *Cloud-Owner-Group*, *Cloud-Global-Cluster-Admin-Group*, *Cloud-Global-Storage-Admin-Group*, *Cloud-Global-Network-Admin-Group* o *Cloud-Global-VM-Admin-Group*.  Gli utenti aggiunti al gruppo *Administrators* verranno rimossi automaticamente.  Solo gli account di servizio devono essere aggiunti al gruppo *Administrators* e gli account di servizio non devono essere usati per accedere all'interfaccia utente Web di vSphere.   


## <a name="identity-source-options"></a>Opzioni di origine dell'identità

* [Aggiungere Active Directory locale come origine di identità Single Sign-On](#add-on-premises-active-directory-as-a-single-sign-on-identity-source)
* [Configurare un nuovo Active Directory in un cloud privato](#set-up-new-active-directory-on-a-private-cloud)
* [Configurare Active Directory in AzureSet Up Active Directory on Azure](#set-up-active-directory-on-azure)

## <a name="add-on-premises-active-directory-as-a-single-sign-on-identity-source"></a>Aggiungere Active Directory locale come origine di identità Single Sign-On

Per configurare Active Directory locale come origine di identità Single Sign-On, è necessario:To set up your on-premises Active Directory as a Single Sign-On identity source, you need:

* [Connessione VPN](vpn-gateway.md#set-up-a-site-to-site-vpn-gateway) da sito a sito dal data center locale al cloud privato.
* IP del server DNS locale aggiunto a vCenter e Platform Services Controller (PSC).

Utilizzare le informazioni nella tabella seguente quando si configura il dominio di Active Directory.

| **Opzione** | **Descrizione** |
|------------|-----------------|
| **Nome** | Nome dell'origine dell'identità. |
| **DN di base per gli utenti** | Nome distinto di base per gli utenti. |
| **Nome di dominio** | FQDN del dominio, ad esempio example.com. Non fornire un indirizzo IP in questa casella di testo. |
| **Alias di dominio** | Nome NetBIOS del dominio. Aggiungere il nome NetBIOS del dominio di Active Directory come alias dell'origine identità se si utilizzano le autenticazioni SSPI. |
| **DN di base per gruppi** | Nome distinto di base per i gruppi. |
| **URL server primario** | Server LDAP del controller di dominio primario per il dominio.<br><br>Utilizzare il `ldap://hostname:port`  `ldaps://hostname:port`formato o . La porta è in genere 389 per le connessioni LDAP e 636 per le connessioni LDAPS. Per le distribuzioni di controller multidominio di Active Directory, la porta è in genere 3268 per LDAP e 3269 per LDAPS.<br><br>Un certificato che stabilisce l'attendibilità per l'endpoint LDAPS `ldaps://` del server Active Directory è necessario quando si utilizza nell'URL LDAP primario o secondario. |
| **URL server secondario** | Indirizzo di un server LDAP del controller di dominio secondario utilizzato per il failover. |
| **Scegliere il certificato** | Se si desidera utilizzare LDAPS con il server LDAP di Active Directory o l'origine identità del server OpenLDAP, viene visualizzato un pulsante Scegli certificato dopo aver digitato `ldaps://` la casella di testo URL. Non è necessario un URL secondario. |
| **Nome utente** | ID di un utente nel dominio che dispone di un accesso minimo in sola lettura al DN di base per utenti e gruppi. |
| **Password** | Password dell'utente specificato da Nome utente. |

Quando si dispone delle informazioni nella tabella precedente, è possibile aggiungere Active Directory locale come origine di identità Single Sign-On in vCenter.

> [!TIP]
> Ulteriori informazioni sulle origini di identità Single Sign-On sono disponibili nella pagina della documentazione di [VMware](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.psc.doc/GUID-B23B1360-8838-4FF2-B074-71643C4CB040.html).

## <a name="set-up-new-active-directory-on-a-private-cloud"></a>Configurare un nuovo Active Directory in un cloud privato

È possibile configurare un nuovo dominio di Active Directory nel cloud privato e utilizzarlo come origine di identità per Single Sign-On.  Il dominio di Active Directory può far parte di una foresta di Active Directory esistente o può essere impostato come foresta indipendente.

### <a name="new-active-directory-forest-and-domain"></a>Nuova foresta e dominio di Active Directory

Per configurare una nuova foresta e un nuovo dominio di Active Directory, è necessario:

* Una o più macchine virtuali che eseguono Microsoft Windows Server da utilizzare come controller di dominio per la nuova foresta e dominio di Active Directory.
* Una o più macchine virtuali che eseguono il servizio DNS per la risoluzione dei nomi.

Per la procedura dettagliata, vedere Installare una nuova foresta di Active Directory di [Windows Server 2012.See Install a New Windows Server 2012 Active Directory Forest](https://docs.microsoft.com/windows-server/identity/ad-ds/deploy/install-a-new-windows-server-2012-active-directory-forest--level-200-) for detailed steps.

> [!TIP]
> Per la disponibilità elevata dei servizi, è consigliabile configurare più controller di dominio e server DNS.

Dopo aver impostato la foresta e il dominio di Active Directory, è possibile [aggiungere un'origine di identità in vCenter](#add-an-identity-source-on-vcenter) per il nuovo Active Directory.

### <a name="new-active-directory-domain-in-an-existing-active-directory-forest"></a>Nuovo dominio di Active Directory in una foresta di Active Directory esistente

Per configurare un nuovo dominio di Active Directory in una foresta di Active Directory esistente, è necessario:

* Connessione VPN da sito a sito alla posizione della foresta di Active Directory.
* Server DNS per risolvere il nome della foresta di Active Directory esistente.

Per la procedura dettagliata, vedere Installare un nuovo dominio figlio o albero di Active Directory di [Windows Server 2012.See Install a new Windows Server 2012 Active Directory child or tree domain](https://docs.microsoft.com/windows-server/identity/ad-ds/deploy/install-a-new-windows-server-2012-active-directory-child-or-tree-domain--level-200-) for detailed steps.

Dopo aver impostato il dominio di Active Directory, è possibile [aggiungere un'origine di identità in vCenter](#add-an-identity-source-on-vcenter) per il nuovo Active Directory.

## <a name="set-up-active-directory-on-azure"></a>Configurare Active Directory in AzureSet up Active Directory on Azure

Active Directory in esecuzione in Azure è simile ad Active Directory in esecuzione in locale.  Per configurare Active Directory in esecuzione in Azure come origine di identità Single Sign-On in vCenter, il server vCenter e PSC devono disporre della connettività di rete alla rete virtuale di Azure in cui sono in esecuzione i servizi Active Directory.  È possibile stabilire questa connettività usando Connessione di rete virtuale di Azure usando ExpressRoute dalla rete virtuale di Azure in cui i servizi Active Directory vengono eseguiti nel cloud privato CloudSimple.You can establish this connectivity using [Azure Virtual Network Connection using ExpressRoute](azure-expressroute-connection.md) from the Azure virtual network where Active Directory Services are running to CloudSimple Private Cloud.

Dopo aver stabilito la connessione di rete, seguire i passaggi descritti in [Aggiungere Active Directory locale come origine di identità Single Sign-On](#add-on-premises-active-directory-as-a-single-sign-on-identity-source) per aggiungerla come origine identità.  

## <a name="add-an-identity-source-on-vcenter"></a>Aggiungere un'origine identità in vCenterAdd an identity source on vCenter

1. [Aumenta i privilegi](escalate-private-cloud-privileges.md) sul tuo cloud privato.

2. Accedi al vCenter per il tuo cloud privato.

3. Selezionare **Home > Amministrazione**.

    ![Amministrazione](media/OnPremAD01.png)

4. Selezionare **Configurazione > Single Sign-On**.

    ![Single Sign On](media/OnPremAD02.png)

5. Aprire **Identity Sources** la scheda Origini **+** e fare clic per aggiungere una nuova origine di identità.

    ![Fonti di identità](media/OnPremAD03.png)

6. Selezionare **Active Directory come server LDAP** e fare clic su **Avanti**.

    ![Active Directory](media/OnPremAD04.png)

7. Specificare i parametri di origine dell'identità per l'ambiente e fare clic su **Avanti**.

    ![Active Directory](media/OnPremAD05.png)

8. Rivedere le impostazioni e fare clic su **Fine**.
