---
title: Creare, configurare cluster di pacchetti di sicurezza aziendale - AzureCreate, configure Enterprise Security Package clusters - Azure
description: Informazioni su come creare e configurare cluster di pacchetti di sicurezza aziendale in Azure HDInsightLearn how to create and configure Enterprise Security Package clusters in Azure HDInsight
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: conceptual
ms.date: 12/10/2019
ms.openlocfilehash: 4edafc0c07e967acfabf7fdc5b58c481b2cfccc3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75436057"
---
# <a name="create-and-configure-enterprise-security-package-clusters-in-azure-hdinsight"></a>Creare e configurare cluster di pacchetti di sicurezza aziendale in Azure HDInsightCreate and configure Enterprise Security Package clusters in Azure HDInsight

Enterprise Security Package (ESP) for Azure HDInsight gives you access to Active Directory-based authentication, multiuser support, and role-based access control for your Apache Hadoop clusters in Azure. I cluster ESP HDInsight consentono alle organizzazioni che aderiscono acriteri di sicurezza aziendale rigorosi di elaborare i dati sensibili in modo sicuro.

Questa guida illustra come creare un cluster Azure HDInsight abilitato per ESP. Viene inoltre illustrato come creare una macchina virtuale Windows IaaS in cui sono abilitati Active Directory e DNS (Domain Name System). Usare questa guida per configurare le risorse necessarie per consentire agli utenti locali di accedere a un cluster HDInsight abilitato per ESP.

Il server creato fungerà da sostituzione per l'ambiente locale *effettivo.* Verrà utilizzato per la procedura di installazione e configurazione. Successivamente si ripeteranno i passaggi nel proprio ambiente.

Questa guida consente inoltre di creare un ambiente di identità ibrido usando la sincronizzazione dell'hash delle password con Azure Active Directory (Azure AD). La guida integra [L'utilizzo di ESP in HDInsight](apache-domain-joined-architecture.md).

Prima di utilizzare questo processo nel proprio ambiente:Before you use this process in your own environment:

* Configurare Active Directory e DNS.
* Abilitare Azure AD.
* Sincronizzare gli account utente locali con Azure AD.

![Diagramma dell'architettura di Azure ADAzure AD architecture diagram](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0002.png)

## <a name="create-an-on-premises-environment"></a>Creare un ambiente localeCreate an on-premises environment

In questa sezione si userà un modello di distribuzione di Azure Quickstart per creare nuove macchine virtuali, configurare DNS e aggiungere una nuova foresta di Active Directory.In this section, you'll use an Azure Quickstart deployment template to create new VMs, configure DNS, and add a new Active Directory forest.

1. Passare al modello di distribuzione Quickstart per creare una macchina virtuale di Azure con una nuova foresta di [Active Directory.](https://azure.microsoft.com/resources/templates/active-directory-new-domain/)

1. Selezionare **Distribuisci in Azure**.
1. Accedere alla sottoscrizione di Azure.
1. Nella pagina Crea una macchina virtuale di Azure con una nuova foresta di Active Directory fornire le informazioni seguenti:On the **Create an Azure VM with a new AD Forest** page, provide the following information:

    |Proprietà | valore |
    |---|---|
    |Subscription|Selezionare la sottoscrizione in cui si vogliono distribuire le risorse.|
    |Resource group|Selezionare **Crea nuovo**e immettere il nome`OnPremADVRG`|
    |Location|Selezionare una località.|
    |Nome utente amministratore|`HDIFabrikamAdmin`|
    |Password amministratore|Immettere una password.|
    |Nome dominio|`HDIFabrikam.com`|
    |Prefisso Dns|`hdifabrikam`|

    Lasciare i valori predefiniti restanti.

    ![Modello per creare una macchina virtuale di Azure con una nuova foresta di Azure ADTemplate for Create an Azure VM with a new Azure AD Forest](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-azure-vm-ad-forest.png)

1. Esaminare i **Termini e condizioni**, quindi selezionare **Accetto i termini e le condizioni sopra indicati**.
1. Selezionare **Purchase**e monitorare la distribuzione e attenderne il completamento. Il completamento della distribuzione richiede circa 30 minuti.

## <a name="configure-users-and-groups-for-cluster-access"></a>Configurare utenti e gruppi per l'accesso al clusterConfigure users and groups for cluster access

In questa sezione verranno creati gli utenti che avranno accesso al cluster HDInsight entro la fine di questa guida.

1. Connettersi al controller di dominio utilizzando Desktop remoto.
    1. Dal portale di Azure passare a **Gruppi** > di risorse**OnPremADVRG** > **adVM** > **Connect**.
    1. Nell'elenco a discesa **Indirizzo IP** selezionare l'indirizzo IP pubblico.
    1. Selezionare **Scarica file RDP**, quindi aprire il file.
    1. Utilizzare `HDIFabrikam\HDIFabrikamAdmin` come nome utente.
    1. Immettere la password scelta per l'account amministratore.
    1. Selezionare **OK**.

1. Dal dashboard di **Server Manager** del controller di dominio passare a **Strumenti** > **utenti e computer**di Active Directory .

    ![Nel dashboard di Server Manager aprire Gestione Active Directory](./media/apache-domain-joined-create-configure-enterprise-security-cluster/server-manager-active-directory-screen.png)

1. Creare due nuovi utenti: **HDIAdmin** e **HDIUser**. Questi due utenti accederanno ai cluster HDInsight.These two users will sign in to HDInsight clusters.

    1. Nella pagina **Utenti e computer** di `HDIFabrikam.com`Active Directory fare clic con il pulsante destro del mouse su , quindi scegliere **Nuovo** > **utente**.

        ![Creare un nuovo utente di Active Directory](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-active-directory-user.png)

    1. Nella pagina Nuovo oggetto - `HDIUser` **Utente** immettere **Nome** e Nome **accesso utente**. Gli altri campi verranno compilati automaticamente. Quindi selezionare **Avanti**.

        ![Creare il primo oggetto utente admin](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0020.png)

    1. Nella finestra popup visualizzata, immettere una password per il nuovo account. Selezionare **Nessuna scadenza password**, quindi **OK** nel messaggio popup.
    1. Selezionare **Avanti**e quindi **Fine** per creare il nuovo account.
    1. Ripetere i passaggi precedenti `HDIAdmin`per creare l'utente .

        ![Creare un secondo oggetto utente amministratoreCreate a second admin user object](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0024.png)

1. Creare un gruppo di sicurezza globale.

    1. Da **Utenti e computer di Active Directory**fare clic con il pulsante destro del mouse su `HDIFabrikam.com`, quindi scegliere **Nuovo** > **gruppo**.

    1. Immettere `HDIUserGroup` nella casella di testo **Nome gruppo.**

    1. Selezionare **OK**.

    ![Creare un nuovo gruppo di Active Directory](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-active-directory-group.png)

    ![Creazione di un nuovo oggetto](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0028.png)

1. Aggiungere membri a **HDIUserGroup**.

    1. Fare clic con il pulsante destro del mouse su **HDIUser** e selezionare **Aggiungi a un gruppo...**.
    1. Nella casella di testo **Immettere i nomi degli oggetti da selezionare** immettere `HDIUserGroup`. Quindi selezionare **OK**e di nuovo **OK** nel popup.
    1. Ripetere i passaggi precedenti per l'account **HDIAdmin.**

        ![Aggiungere il membro HDIUser al gruppo HDIUserGroup](./media/apache-domain-joined-create-configure-enterprise-security-cluster/active-directory-add-users-to-group.png)

A questo punto è stato creato l'ambiente Active Directory. Sono stati aggiunti due utenti e un gruppo di utenti che possono accedere al cluster HDInsight.You've added two users and a user group that can access the HDInsight cluster.

Gli utenti verranno sincronizzati con Azure AD.

### <a name="create-an-azure-ad-directory"></a>Creare una directory di Azure AD

1. Accedere al portale di Azure.
1. Selezionare Crea una `directory` **risorsa** e digitare . Selezionare **Azure Active Directory** > **Create**.
1. In **Nome**organizzazione `HDIFabrikam`immettere .
1. In Nome dominio `HDIFabrikamoutlook` **iniziale**immettere .
1. Selezionare **Crea**.

    ![Creare una directory di Azure AD](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-new-directory.png)

### <a name="create-a-custom-domain"></a>Creare un dominio personalizzatoCreate a custom domain

1. Dal nuovo **Azure Active Directory,** in **Gestisci,** selezionare Nomi di **dominio personalizzati**.
1. Selezionare **: Aggiungi dominio personalizzato**.
1. In **Nome dominio** `HDIFabrikam.com`personalizzato immettere e quindi selezionare **Aggiungi dominio**.
1. Completare quindi [Aggiungere le informazioni DNS al registrar](../../active-directory/fundamentals/add-custom-domain.md#add-your-dns-information-to-the-domain-registrar).

![Creare un dominio personalizzatoCreate a custom domain](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-custom-domain.png)

### <a name="create-a-group"></a>Creare un gruppo

1. Dal nuovo **Azure Active Directory,** in **Gestisci,** selezionare **Gruppi**.
1. Selezionare **: Nuovo gruppo**.
1. Nella casella di testo `AAD DC Administrators`Nome **gruppo** immettere .
1. Selezionare **Crea**.

## <a name="configure-your-azure-ad-tenant"></a>Configurare il tenant di Azure ADConfigure your Azure AD tenant

A questo punto si configurerà il tenant di Azure AD in modo da poter sincronizzare utenti e gruppi dall'istanza di Active Directory locale al cloud.

Creare un amministratore tenant di Active Directory.Create an Active Directory tenant administrator.

1. Accedere al portale di Azure e selezionare il tenant di Azure AD, **HDIFabrikam**.

1. Passare a **Gestisci** > **utenti** > **Nuovo utente**.

1. Immettere i seguenti dettagli per il nuovo utente:

    **Identità**

    |Proprietà |Descrizione |
    |---|---|
    |Nome utente|Immettere `fabrikamazureadmin` nella casella di testo. Dall'elenco a discesa del nome di dominio, selezionare`hdifabrikam.com`|
    |Nome| Immettere `fabrikamazureadmin`.|

    **Password**
    1. Selezionare **Consenti creazione della password**.
    1. Immettere una password sicura di propria scelta.

    **Gruppi e ruoli**
    1. Selezionare **0 gruppi selezionati**.
    1. Selezionare **AAD DC Administrators**, quindi **selezionare**.

    ![Finestra di dialogo Gruppi di Azure AD](./media/apache-domain-joined-create-configure-enterprise-security-cluster/azure-ad-add-group-member.png)

    1. Selezionare **Utente**.
    1. Selezionare **Amministratore globale**, quindi **selezionare**.

    ![Finestra di dialogo Ruolo di Azure AD](./media/apache-domain-joined-create-configure-enterprise-security-cluster/azure-ad-add-role-member.png)

1. Selezionare **Crea**.

1. Chiedere quindi al nuovo utente di accedere al portale di Azure in cui verrà richiesto di modificare la password. È necessario eseguire questa operazione prima di configurare Microsoft Azure Active Directory Connect.You'll need to do this before configuring Microsoft Azure Active Directory Connect.

## <a name="sync-on-premises-users-to-azure-ad"></a>Sincronizzare gli utenti locali con Azure ADSync on-premises users to Azure AD

### <a name="configure-microsoft-azure-active-directory-connect"></a>Configurare Microsoft Azure Active Directory Connect

1. Dal controller di dominio scaricare [Microsoft Azure Active Directory Connect](https://www.microsoft.com/download/details.aspx?id=47594).

1. Aprire il file eseguibile scaricato e accettare le condizioni di licenza. Selezionare **Continua**.

1. Selezionare **Usa impostazioni rapide**.

1. Nella pagina **Connetti ad Azure AD** immettere il nome utente e la password dell'amministratore globale per Azure AD. Usare il `fabrikamazureadmin@hdifabrikam.com` nome utente creato durante la configurazione del tenant di Active Directory. Quindi selezionare **Avanti**.

    ![La pagina "Connetti ad Azure AD"](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0058.png)

1. Nella pagina Connessione a Servizi di **dominio Active Directory** immettere il nome utente e la password di un account amministratore aziendale. Utilizzare il `HDIFabrikam\HDIFabrikamAdmin` nome utente e la password creati in precedenza. Quindi selezionare **Avanti**.

   ![La pagina "Connetti ad Azure AD"](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0060.png)
1. Nella pagina **Configurazione dell'accesso** ad Azure AD selezionare **Avanti**.
   ![Pagina "Configurazione dell'accesso ad Azure AD"](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0062.png)

1. Nella pagina **Pronto per** la configurazione selezionare **Installa**.

   ![La pagina "Pronto per la configurazione"](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0064.png)

1. Nella pagina **La configurazione è stata completata** selezionare **Esci**.
   ![La pagina "Configurazione completata"](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0078.png)

1. Al termine della sincronizzazione, verificare che gli utenti creati nella directory IaaS siano sincronizzati con Azure AD.
   1. Accedere al portale di Azure.
   1. Selezionare**Utenti****HDIFabrikam** >  **di Azure Active Directory** > .

### <a name="create-a-user-assigned-managed-identity"></a>Creare un'identità gestita assegnata dall'utente

Creare un'identità gestita assegnata dall'utente che è possibile usare per configurare Servizi di dominio Azure AD (Azure AD DS). Per altre informazioni, vedere [Creare, elencare, eliminare o assegnare un ruolo a un'identità gestita assegnata dall'utente tramite il portale di Azure.For more information, see Create, list, delete, or assign a role to a user-assigned managed identity by using the Azure portal](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md).

1. Accedere al portale di Azure.
1. Selezionare Crea una `managed identity` **risorsa** e digitare . Selezionare Creazione >  **identità gestita assegnata dall'utente****.**
1. Per **Nome risorsa** `HDIFabrikamManagedIdentity`immettere .
1. Selezionare la propria sottoscrizione.
1. In **Gruppo di**risorse selezionare Crea **nuovo** e immettere `HDIFabrikam-CentralUS`.
1. In **Posizione**selezionare **Stati Uniti centrali**.
1. Selezionare **Crea**.

![Creare una nuova identità gestita assegnata dall'utenteCreate a new user-assigned managed identity](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0082.png)

### <a name="enable-azure-ad-ds"></a>Abilitare Azure AD DS

Seguire questi passaggi per abilitare Servizi di dominio Active Directory di Azure.Follow these steps to enable Azure AD DS. Per altre informazioni, vedere Abilitare Servizi di dominio Active Directory di Azure tramite il portale di Azure.For more information, see [Enable Azure AD DS by using the Azure portal.](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started)

1. Creare una rete virtuale per ospitare Azure AD DS.Create a virtual network to host Azure AD DS. Eseguire il codice di PowerShell seguente.

    ```powershell
    # Sign in to your Azure subscription
    $sub = Get-AzSubscription -ErrorAction SilentlyContinue
    if(-not($sub))
    {
        Connect-AzAccount
    }

    # If you have multiple subscriptions, set the one to use
    # Select-AzSubscription -SubscriptionId "<SUBSCRIPTIONID>"
    
    $virtualNetwork = New-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-CentralUS' -Location 'Central US' -Name 'HDIFabrikam-AADDSVNET' -AddressPrefix 10.1.0.0/16
    $subnetConfig = Add-AzVirtualNetworkSubnetConfig -Name 'AADDS-subnet' -AddressPrefix 10.1.0.0/24 -VirtualNetwork $virtualNetwork
    $virtualNetwork | Set-AzVirtualNetwork
    ```

1. Accedere al portale di Azure.
1. Selezionare **Crea** `Domain services`risorsa , immettere e selezionare **Azure AD Domain Services** > **Create**.
1. Nella pagina **Nozioni di base:**
    1. In **Nome directory**selezionare la directory di Azure AD creata: **HDIFabrikam**.
    1. Per **Nome dominio DNS**, immettere *HDIFabrikam.com*.
    1. Selezionare la propria sottoscrizione.
    1. Specificare il gruppo di risorse **HDIFabrikam-CentralUS**. Per **Posizione**, selezionare **Stati Uniti centrali**.

        ![Dettagli di base di Azure AD DSAzure AD DS basic details](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0084.png)

1. Nella pagina **Rete** selezionare la rete (**HDIFabrikam-VNET**) e la subnet (**AADDS-subnet**) creata utilizzando lo script di PowerShell. In alternativa, scegliere **Crea nuova** per creare subito una rete virtuale.

    ![Il passaggio "Crea rete virtuale"](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0086.png)

1. Nella pagina **Gruppo** amministratori dovrebbe essere visualizzata una notifica che indica che è già stato creato un gruppo denominato **AAD DC Administrators** per amministrarlo. Se lo si desidera, è possibile modificare l'appartenenza a questo gruppo, ma in questo caso non è necessario modificarla. Selezionare **OK**.

    ![Visualizzare il gruppo di amministratori di Azure ADView the Azure AD administrator group](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0088.png)

1. Nella pagina **Sincronizzazione** abilitare la sincronizzazione completa selezionando **Tutto** > **OK**.

    ![Abilitare la sincronizzazione di Servizi di dominio Active Directory di AzureEnable Azure AD DS synchronization](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0090.png)

1. Nella pagina **Riepilogo** verificare i dettagli per Servizi di dominio Active Directory di Azure e selezionare **OK**.

    ![Riepilogo di "Abilitare Servizi di dominio Azure AD"The summary of "Enable Azure AD Domain Services"](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0092.png)

Dopo aver abilitato Servizi di dominio Active Directory di Azure, viene eseguito un server DNS locale nelle macchine virtuali di Azure AD.

### <a name="configure-your-azure-ad-ds-virtual-network"></a>Configurare la rete virtuale di Azure AD DSConfigure your Azure AD DS virtual network

Utilizzare la procedura seguente per configurare la rete virtuale di Servizi di dominio Active Directory di Azure (**HDIFabrikam-AADDSVNET**) per l'utilizzo dei server DNS personalizzati.

1. Individuare gli indirizzi IP dei server DNS personalizzati.
    1. Selezionare `HDIFabrikam.com` la risorsa di Dominio active Directory di Azure.Select the Azure AD DS resource.
    1. In **Gestisci**selezionare **Proprietà**.
    1. Individuare gli indirizzi IP in **Indirizzo IP nella rete virtuale**.

    ![Individuare indirizzi IP DNS personalizzati per Azure AD DS](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0096.png)

1. Configurare **HDIFabrikam-AADDSVNET** per l'utilizzo di indirizzi IP personalizzati 10.0.0.4 e 10.0.0.5.

    1. In **Impostazioni**selezionare **Server DNS**.
    1. Selezionare **Personalizzato**.
    1. Nella casella di testo immettere il primo indirizzo IP (*10.0.0.4*).
    1. Selezionare **Salva**.
    1. Ripetere i passaggi per aggiungere l'altro indirizzo IP (*10.0.0.5*).

In questo scenario è stato configurato Azure AD DS per l'utilizzo di indirizzi IP 10.0.0.4 e 10.0.0.5, impostando lo stesso indirizzo IP nella rete virtuale di Azure AD DS:In our scenario, we configured Azure AD DS to use IP addresses 10.0.0.4 and 10.0.0.5, setting the same IP address on the Azure AD DS virtual network:

![Pagina dei server DNS personalizzati](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0098.png)

## <a name="securing-ldap-traffic"></a>Protezione del traffico LDAP

Il protocollo LDAP (Lightweight Directory Access Protocol) viene usato per leggere e scrivere in Azure Active Directory. È possibile rendere il traffico LDAP riservato e protetto utilizzando la tecnologia Secure Sockets Layer (SSL) o Transport Layer Security (TLS). È possibile abilitare LDAP su SSL (LDAPS) installando un certificato formattato correttamente.

Per altre informazioni su LDAP sicuro, vedere [Configurare LDAPS per un dominio gestito di Azure AD DS.](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-admin-guide-configure-secure-ldap)

In questa sezione viene creato un certificato autofirmato, si scarica il certificato e si configura LDAPS per il dominio gestito **HDIFabrikam** Azure DS.

Lo script seguente crea un certificato per **HDIFabrikam**. Il certificato viene salvato nel percorso *LocalMachine.The* certificate is saved in the LocalMachine path.

```powershell
$lifetime = Get-Date
New-SelfSignedCertificate -Subject hdifabrikam.com `
-NotAfter $lifetime.AddDays(365) -KeyUsage DigitalSignature, KeyEncipherment `
-Type SSLServerAuthentication -DnsName *.hdifabrikam.com, hdifabrikam.com
```

> [!NOTE]  
> Qualsiasi utilità o applicazione che crea una richiesta valida \#di Public Key Cryptography Standards (PKCS) 10 può essere utilizzata per formare la richiesta di certificato SSL.

Verificare che il certificato sia installato nell'archivio **personale** del computer:

1. Avviare Microsoft Management Console (MMC).
1. Aggiungere lo snap-in **Certificati** che gestisce i certificati nel computer locale.
1. Espandere **Certificati (computer locale)** > **Certificati****personali** > . Nell'archivio **personale** deve essere presente un nuovo certificato. Questo certificato viene rilasciato al nome host completo.

    ![Verificare la creazione di certificati locali](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0102.png)

1. Nel riquadro a destra fare clic con il pulsante destro del mouse sul certificato creato. Scegliere **Tutte le attività**, quindi selezionare **Esporta**.

1. Nella pagina **Esporta chiave privata** selezionare **Sì, esporta la chiave privata**. Il computer in cui verrà importata la chiave necessita della chiave privata per leggere i messaggi crittografati.

    ![Pagina Esporta chiave privata dell'Esportazione guidata certificati](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0103.png)

1. Nella pagina **Formato file** di esportazione lasciare le impostazioni predefinite e quindi selezionare **Avanti**.
1. Nella pagina **Password** digitare una password per la chiave privata. Per **Crittografia**, selezionare **TripleDES-SHA1**. Quindi selezionare **Avanti**.
1. Nella pagina **File da esportare** digitare il percorso e il nome del file del certificato esportato e quindi scegliere **Avanti**. Il nome del file deve avere estensione pfx. Questo file è configurato nel portale di Azure per stabilire una connessione sicura.
1. Abilitare LDAPS per un dominio gestito di Azure AD DS.Enable LDAPS for an Azure AD DS managed domain.
    1. Nel portale di Azure `HDIFabrikam.com`selezionare il dominio .
    1. In **Gestisci**selezionare **LDAP sicuro**.
    1. Nella pagina **LDAP sicuro,** in **LDAP sicuro,** selezionare **Abilita**.
    1. Cercare il file di certificato con estensione pfx esportato nel computer.
    1. Immettere la password del certificato.

    ![Abilitare LDAP sicuro](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0113.png)

1. Dopo aver abilitato LDAPS, assicurati che sia raggiungibile abilitando la porta 636.
    1. Nel gruppo di risorse **HDIFabrikam-CentralUS** selezionare il gruppo di sicurezza di rete **AADDS-HDIFabrikam.com-NSG**.
    1. In **Impostazioni**selezionare Regole > di **sicurezza in ingresso****Aggiungi**.
    1. Nella pagina Aggiungi regola di **sicurezza in ingresso** immettere le proprietà seguenti e selezionare **Aggiungi:**

        | Proprietà | valore |
        |---|---|
        | Source (Sorgente) | Qualsiasi |
        | Intervalli di porte di origine | * |
        | Destination | Qualsiasi |
        | Intervallo di porte di destinazione | 636 |
        | Protocollo | Qualsiasi |
        | Azione | Allow |
        | Priorità | \<Numero desiderato> |
        | Nome | Port_LDAP_636 |

    ![Finestra di dialogo "Aggiungi regola di sicurezza in ingresso"](./media/apache-domain-joined-create-configure-enterprise-security-cluster/add-inbound-security-rule.png)

**HDIFabrikamManagedIdentity** è l'identità gestita assegnata dall'utente. Il ruolo Collaboratore servizi di dominio HDInsight è abilitato per l'identità gestita che consentirà a questa identità di leggere, creare, modificare ed eliminare le operazioni dei servizi di dominio.

![Creare un'identità gestita assegnata dall'utente](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0117.png)

## <a name="create-an-esp-enabled-hdinsight-cluster"></a>Creare un cluster HDInsight abilitato per ESPCreate an ESP-enabled HDInsight cluster

Questo passaggio richiede i prerequisiti seguenti:This step requires the following prerequisites:

1. Creare un nuovo gruppo di risorse *HDIFabrikam-WestUS* nella posizione **West US**.
1. Creare una rete virtuale che ospiterà il cluster HDInsight abilitato per ESP.

    ```powershell
    $virtualNetwork = New-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-WestUS' -Location 'West US' -Name 'HDIFabrikam-HDIVNet' -AddressPrefix 10.1.0.0/16
    $subnetConfig = Add-AzVirtualNetworkSubnetConfig -Name 'SparkSubnet' -AddressPrefix 10.1.0.0/24 -VirtualNetwork $virtualNetwork
    $virtualNetwork | Set-AzVirtualNetwork
    ```

1. Creare una relazione peer tra la rete virtuale`HDIFabrikam-AADDSVNET`che ospita Servizi di dominio Active Directory di`HDIFabrikam-HDIVNet`Azure ( ) e la rete virtuale che ospiterà il cluster HDInsight abilitato per ESP ( ). Usare il codice PowerShell seguente per eseguire il peerare le due reti virtuali.

    ```powershell
    Add-AzVirtualNetworkPeering -Name 'HDIVNet-AADDSVNet' -RemoteVirtualNetworkId (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-CentralUS').Id -VirtualNetwork (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-WestUS')

    Add-AzVirtualNetworkPeering -Name 'AADDSVNet-HDIVNet' -RemoteVirtualNetworkId (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-WestUS').Id -VirtualNetwork (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-CentralUS')
    ```

1. Creare un nuovo account Azure Data Lake Storage Gen2 denominato **Hdigen2store**. Configurare l'account con l'identità gestita dall'utente **HDIFabrikamManagedIdentity**. Per altre informazioni, vedere Usare Azure Data Lake Storage Gen2 con cluster di Azure HDInsight.For more information, see [Use Azure Data Lake Storage Gen2 with Azure HDInsight clusters.](../hdinsight-hadoop-use-data-lake-storage-gen2.md)

1. Configurare DNS personalizzato nella rete virtuale **HDIFabrikam-AADDSVNET.**
    1. Passare al portale di Azure > **gruppi** > di risorse**OnPremADVRG** > **HDIFabrikam-AADDSVNET** > **.**
    1. Selezionare **Personalizzato** e immettere *10.0.0.4* e *10.0.0.5*.
    1. Selezionare **Salva**.

        ![Salvare le impostazioni DNS personalizzate per una rete virtualeSave custom DNS settings for a virtual network](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0123.png)

1. Creare un nuovo cluster HDInsight Spark abilitato per ESP.
    1. Selezionare **Personalizzato (dimensioni, impostazioni, app)**.
    1. Immettere i dettagli per **Nozioni di base** (sezione 1). Verificare che il **tipo di cluster** sia **Spark 2.3 (HDI 3.6)**. Assicurarsi che il **gruppo di risorse** sia **HDIFabrikam-CentralUS**.

    1. Per **Sicurezza e rete** (sezione 2), inserire i dettagli seguenti:
        * In **Pacchetto di sicurezza aziendale**selezionare **Abilitato**.
        * Selezionare **Utente amministratore cluster** e selezionare l'account **HDIAdmin** creato come utente amministratore locale. Fare clic su **Seleziona**.
        * Selezionare **Gruppo** > di accesso cluster**HDIUserGroup**. Qualsiasi utente aggiunto a questo gruppo in futuro sarà in grado di accedere ai cluster HDInsight.

            ![Selezionare il gruppo di accesso al cluster HDIUserGroupSelect the cluster access group HDIUserGroup](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0129.jpg)

    1. Completare gli altri passaggi della configurazione del cluster e verificare i dettagli nel riepilogo del **cluster**. Selezionare **Crea**.

1. Accedere all'interfaccia utente di Ambari `https://CLUSTERNAME.azurehdinsight.net`per il cluster appena creato in . Usa il `hdiadmin@hdifabrikam.com` tuo nome utente amministratore e la sua password.

    ![Finestra di accesso aPache Ambari UI](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0135.jpg)

1. Nel dashboard del cluster selezionare **Ruoli**.
1. Nella pagina **Ruoli,** in **Assegna ruoli a questi**, accanto al ruolo Amministratore **cluster,** immettere il gruppo *hdiusergroup*. 

    ![Assegnare il ruolo di amministratore del cluster a hdiusergroupAssign the cluster admin role to hdiusergroup](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0137.jpg)

1. Aprire il client Secure Shell (SSH) e accedere al cluster. Utilizzare **l'hdiuser** creato nell'istanza di Active Directory locale.

    ![Accedere al cluster utilizzando il client SSH](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0139.jpg)

Se è possibile accedere con questo account, il cluster ESP è stato configurato correttamente per la sincronizzazione con l'istanza di Active Directory locale.

## <a name="next-steps"></a>Passaggi successivi

Leggi [un'introduzione alla sicurezza Apache Hadoop con ESP](hdinsight-security-overview.md).
