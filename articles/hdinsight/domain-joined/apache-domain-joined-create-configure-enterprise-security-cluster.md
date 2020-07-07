---
title: Creare, configurare cluster Enterprise Security Package-Azure
description: Informazioni su come creare e configurare cluster Enterprise Security Package in Azure HDInsight
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: conceptual
ms.date: 12/10/2019
ms.openlocfilehash: fb3484d013314897ea2e9157b642d8f2b85dcd60
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "80437641"
---
# <a name="create-and-configure-enterprise-security-package-clusters-in-azure-hdinsight"></a>Creare e configurare cluster di Enterprise Security Package in Azure HDInsight

Enterprise Security Package (ESP) per Azure HDInsight consente di accedere all'autenticazione basata su Active Directory, al supporto multiutente e al controllo degli accessi in base al ruolo per i cluster Apache Hadoop in Azure. I cluster HDInsight ESP consentono alle organizzazioni che rispettano i rigorosi criteri di sicurezza aziendali di elaborare in modo sicuro i dati sensibili.

Questa guida illustra come creare un cluster Azure HDInsight abilitato per ESP. Viene inoltre illustrato come creare una macchina virtuale IaaS Windows in cui sono abilitati Active Directory e Domain Name System (DNS). Usare questa guida per configurare le risorse necessarie per consentire agli utenti locali di accedere a un cluster HDInsight abilitato per ESP.

Il server creato fungerà da sostituzione per l'ambiente locale *effettivo* . Verrà usato per i passaggi di installazione e configurazione. In un secondo momento si ripeteranno i passaggi nell'ambiente in uso.

Questa guida consente inoltre di creare un ambiente di identità ibrido usando la sincronizzazione dell'hash delle password con Azure Active Directory (Azure AD). La guida è complementare all' [uso di ESP in HDInsight](apache-domain-joined-architecture.md).

Prima di usare questo processo nel proprio ambiente:

* Configurare Active Directory e DNS.
* Abilitare Azure AD.
* Sincronizzare gli account utente locali con Azure AD.

![Diagramma dell'architettura Azure AD](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0002.png)

## <a name="create-an-on-premises-environment"></a>Creare un ambiente locale

In questa sezione si userà un modello di distribuzione di avvio rapido di Azure per creare nuove macchine virtuali, configurare DNS e aggiungere una nuova foresta Active Directory.

1. Passare al modello di distribuzione di avvio rapido per [creare una macchina virtuale di Azure con una nuova foresta Active Directory](https://azure.microsoft.com/resources/templates/active-directory-new-domain/).

1. Selezionare **Distribuisci in Azure**.
1. Accedere alla sottoscrizione di Azure.
1. Nella pagina **creare una macchina virtuale di Azure con una nuova foresta di Active Directory** specificare le informazioni seguenti:

    |Proprietà | valore |
    |---|---|
    |Subscription|Selezionare la sottoscrizione in cui si desidera distribuire le risorse.|
    |Resource group|Selezionare **Crea nuovo**e immettere il nome`OnPremADVRG`|
    |Posizione|Selezionare una località.|
    |Nome utente amministratore|`HDIFabrikamAdmin`|
    |Password amministratore|Immettere una password.|
    |Nome dominio|`HDIFabrikam.com`|
    |Prefisso DNS|`hdifabrikam`|

    Lasciare i valori predefiniti restanti.

    ![Modello per creare una macchina virtuale di Azure con una nuova foresta Azure AD](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-azure-vm-ad-forest.png)

1. Esaminare i **termini e le condizioni**e quindi selezionare Accetto **i termini e le condizioni indicati in precedenza**.
1. Selezionare **Acquista**e monitorare la distribuzione e attenderne il completamento. Per il completamento della distribuzione sono necessari circa 30 minuti.

## <a name="configure-users-and-groups-for-cluster-access"></a>Configurare utenti e gruppi per l'accesso al cluster

In questa sezione verranno creati gli utenti che avranno accesso al cluster HDInsight entro la fine di questa guida.

1. Connettersi al controller di dominio utilizzando Desktop remoto.
    1. Dal portale di Azure passare a gruppi di **risorse**  >  **OnPremADVRG**  >  **adVM**  >  **Connect**.
    1. Dall'elenco a discesa **indirizzo IP** selezionare l'indirizzo IP pubblico.
    1. Selezionare **Scarica file RDP**, quindi aprire il file.
    1. Utilizzare `HDIFabrikam\HDIFabrikamAdmin` come nome utente.
    1. Immettere la password scelta per l'account amministratore.
    1. Selezionare **OK**.

1. Dal dashboard del controller di dominio **Server Manager** passare a **strumenti**  >  **Active Directory utenti e computer**.

    ![Nel Dashboard Server Manager aprire Active Directory gestione](./media/apache-domain-joined-create-configure-enterprise-security-cluster/server-manager-active-directory-screen.png)

1. Creare due nuovi utenti: **HDIAdmin** e **HDIUser**. Questi due utenti accederanno ai cluster HDInsight.

    1. Nella pagina **Active Directory utenti e computer** , fare clic con il pulsante destro del mouse su `HDIFabrikam.com` , quindi passare a **nuovo**  >  **utente**.

        ![Creare un nuovo utente Active Directory](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-active-directory-user.png)

    1. Nella pagina **nuovo oggetto-utente** immettere nome `HDIUser` e nome **First name** di **accesso utente**. Gli altri campi vengono popolati automaticamente. Selezionare quindi **Avanti**.

        ![Creare il primo oggetto utente amministratore](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0020.png)

    1. Nella finestra popup visualizzata immettere una password per il nuovo account. Selezionare **Nessuna scadenza password**e quindi **OK** al messaggio popup.
    1. Fare clic su **Avanti**e quindi su **fine** per creare il nuovo account.
    1. Ripetere i passaggi precedenti per creare l'utente `HDIAdmin` .

        ![Creare un secondo oggetto utente amministratore](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0024.png)

1. Creare un gruppo di sicurezza globale.

    1. Da **Active Directory utenti e computer**, fare clic con il pulsante destro del mouse su `HDIFabrikam.com` , quindi passare a **nuovo**  >  **gruppo**.

    1. Immettere `HDIUserGroup` nella casella di testo **nome gruppo** .

    1. Selezionare **OK**.

    ![Creare un nuovo gruppo di Active Directory](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-active-directory-group.png)

    ![Creazione di un nuovo oggetto](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0028.png)

1. Aggiungere membri a **HDIUserGroup**.

    1. Fare clic con il pulsante destro del mouse su **HDIUser** e scegliere **Aggiungi a gruppo.**
    1. Nella casella **di testo immettere i nomi degli oggetti da selezionare** immettere `HDIUserGroup` . Fare quindi clic su **OK**e di nuovo su **OK** nella finestra popup.
    1. Ripetere i passaggi precedenti per l'account **HDIAdmin** .

        ![Aggiungere il membro HDIUser al gruppo HDIUserGroup](./media/apache-domain-joined-create-configure-enterprise-security-cluster/active-directory-add-users-to-group.png)

A questo punto è stato creato l'ambiente di Active Directory. Sono stati aggiunti due utenti e un gruppo di utenti che possono accedere al cluster HDInsight.

Gli utenti verranno sincronizzati con Azure AD.

### <a name="create-an-azure-ad-directory"></a>Creare una directory di Azure AD

1. Accedere al portale di Azure.
1. Selezionare **Crea una risorsa** e digitare `directory` . Selezionare **Azure Active Directory**  >  **Crea**.
1. In **nome organizzazione**immettere `HDIFabrikam` .
1. In **nome di dominio iniziale**immettere `HDIFabrikamoutlook` .
1. Selezionare **Crea**.

    ![Creare una directory di Azure AD](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-new-directory.png)

### <a name="create-a-custom-domain"></a>Creazione di un dominio personalizzato

1. Dalla nuova **Azure Active Directory**in **Gestisci**selezionare nomi di **dominio personalizzati**.
1. Selezionare **+ Aggiungi dominio personalizzato**.
1. In **nome dominio personalizzato**immettere `HDIFabrikam.com` e quindi selezionare **Aggiungi dominio**.
1. Quindi completare [aggiungere le informazioni DNS al registrar](../../active-directory/fundamentals/add-custom-domain.md#add-your-dns-information-to-the-domain-registrar).

![Creazione di un dominio personalizzato](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-custom-domain.png)

### <a name="create-a-group"></a>Creare un gruppo

1. Dalla nuova **Azure Active Directory**in **Gestisci**Selezionare **gruppi**.
1. Selezionare **+ nuovo gruppo**.
1. Nella casella di testo **nome gruppo** , immettere `AAD DC Administrators` .
1. Selezionare **Crea**.

## <a name="configure-your-azure-ad-tenant"></a>Configurare il tenant di Azure AD

A questo punto si configurerà il tenant di Azure AD in modo che sia possibile sincronizzare gli utenti e i gruppi dall'istanza di Active Directory locale al cloud.

Creare un Active Directory amministratore tenant.

1. Accedere al portale di Azure e selezionare il tenant di Azure AD, **HDIFabrikam**.

1. Passare a **Gestisci**  >  **utenti**  >  **nuovo utente**.

1. Immettere i dettagli seguenti per il nuovo utente:

    **Identità**

    |Proprietà |Descrizione |
    |---|---|
    |Nome utente|Immettere `fabrikamazureadmin` nella casella di testo. Dall'elenco a discesa nome dominio selezionare`hdifabrikam.com`|
    |Nome| Immettere `fabrikamazureadmin`.|

    **Password**
    1. Selezionare **Consenti la creazione della password**.
    1. Immettere una password sicura di propria scelta.

    **Gruppi e ruoli**
    1. Selezionare **0 gruppi selezionati**.
    1. Selezionare **AAD DC Administrators**, quindi **selezionare**.

    ![Finestra di dialogo gruppi di Azure AD](./media/apache-domain-joined-create-configure-enterprise-security-cluster/azure-ad-add-group-member.png)

    1. Selezionare **utente**.
    1. Selezionare **amministratore globale**e quindi **selezionare**.

    ![Finestra di dialogo Azure AD Role](./media/apache-domain-joined-create-configure-enterprise-security-cluster/azure-ad-add-role-member.png)

1. Selezionare **Crea**.

1. Quindi, fare in modo che il nuovo utente effettui l'accesso alla portale di Azure in cui verrà richiesto di modificare la password. È necessario eseguire questa operazione prima di configurare Microsoft Azure Active Directory Connect.

## <a name="sync-on-premises-users-to-azure-ad"></a>Sincronizzare gli utenti locali con Azure AD

### <a name="configure-microsoft-azure-active-directory-connect"></a>Configurare Microsoft Azure Active Directory Connect

1. Dal controller di dominio scaricare [Microsoft Azure Active Directory Connect](https://www.microsoft.com/download/details.aspx?id=47594).

1. Aprire il file eseguibile scaricato e accettare le condizioni di licenza. Selezionare **Continua**.

1. Selezionare **Usa impostazioni rapide**.

1. Nella pagina **Connetti a Azure ad** immettere il nome utente e la password dell'amministratore globale per Azure ad. Usare il nome utente `fabrikamazureadmin@hdifabrikam.com` creato al momento della configurazione del tenant di Active Directory. Selezionare quindi **Avanti**.

    ![Pagina "Connetti a Azure AD"](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0058.png)

1. Nella pagina **Connetti a Active Directory Domain Services** immettere il nome utente e la password per un account amministratore dell'organizzazione. Usare il nome utente `HDIFabrikam\HDIFabrikamAdmin` e la password creati in precedenza. Selezionare quindi **Avanti**.

   ![Pagina "Connetti a Azure AD"](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0060.png)
1. Nella pagina **configurazione dell'accesso Azure ad** fare clic su **Avanti**.
   ![Pagina di configurazione dell'accesso Azure AD](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0062.png)

1. Nella pagina **pronto per la configurazione** selezionare **Installa**.

   ![Pagina "pronto per la configurazione"](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0064.png)

1. Nella pagina **La configurazione è stata completata** selezionare **Esci**.
   ![Pagina "Configurazione completata"](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0078.png)

1. Al termine della sincronizzazione, verificare che gli utenti creati nella directory IaaS siano sincronizzati con Azure AD.
   1. Accedere al portale di Azure.
   1. Selezionare **Azure Active Directory**  >  **HDIFabrikam**  >  **utenti**HDIFabrikam.

### <a name="create-a-user-assigned-managed-identity"></a>Creare un'identità gestita assegnata dall'utente

Creare un'identità gestita assegnata dall'utente che è possibile usare per configurare Azure AD Domain Services (Azure AD DS). Per altre informazioni, vedere [creare, elencare, eliminare o assegnare un ruolo a un'identità gestita assegnata dall'utente usando il portale di Azure](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md).

1. Accedere al portale di Azure.
1. Selezionare **Crea una risorsa** e digitare `managed identity` . Selezionare **creazione identità gestita assegnata dall'utente**  >  **Create**.
1. Per il **nome della risorsa**, immettere `HDIFabrikamManagedIdentity` .
1. Selezionare la propria sottoscrizione.
1. In **gruppo di risorse**selezionare **Crea nuovo** e immettere `HDIFabrikam-CentralUS` .
1. In **località**selezionare **Stati Uniti centrali**.
1. Selezionare **Crea**.

![Creare una nuova identità gestita assegnata dall'utente](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0082.png)

### <a name="enable-azure-ad-ds"></a>Abilitare Azure AD DS

Per abilitare Azure AD DS, attenersi alla seguente procedura. Per ulteriori informazioni, vedere [Enable Azure AD DS using the portale di Azure](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started).

1. Creare una rete virtuale per ospitare Azure AD DS. Eseguire il codice PowerShell seguente.

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
1. Selezionare **Crea risorsa**, immettere `Domain services` e selezionare **Azure ad Domain Services**  >  **Crea**.
1. Nella pagina **nozioni di base** :
    1. In **nome directory**selezionare la directory di Azure ad creata: **HDIFabrikam**.
    1. Per **nome di dominio DNS**immettere *HDIFabrikam.com*.
    1. Selezionare la propria sottoscrizione.
    1. Specificare il gruppo di risorse **HDIFabrikam-centralus**. Per **località**selezionare **Stati Uniti centrali**.

        ![Dettagli di Azure AD DS Basic](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0084.png)

1. Nella pagina **rete** selezionare la rete (**HDIFabrikam-VNET**) e la subnet (**AADDS-subnet**) creata con lo script di PowerShell. In alternativa, scegliere **Crea nuovo** per creare una rete virtuale.

    ![Passaggio "Crea rete virtuale"](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0086.png)

1. Nella pagina **gruppo amministratore** dovrebbe essere visualizzata una notifica che segnala che è già stato creato un gruppo denominato **amministratori di AAD DC** per amministrare questo gruppo. Se lo si desidera, è possibile modificare l'appartenenza di questo gruppo, ma in questo caso non è necessario modificarlo. Selezionare **OK**.

    ![Visualizzare il gruppo amministratori Azure AD](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0088.png)

1. Nella pagina **sincronizzazione** abilitare la sincronizzazione completa selezionando **tutto**  >  **OK**.

    ![Abilitare la sincronizzazione di Azure AD DS](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0090.png)

1. Nella pagina **Riepilogo** verificare i dettagli per Azure AD DS e selezionare **OK**.

    ![Riepilogo di "Enable Azure AD Domain Services"](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0092.png)

Dopo aver abilitato Azure AD DS, un server DNS locale viene eseguito nelle VM Azure AD.

### <a name="configure-your-azure-ad-ds-virtual-network"></a>Configurare la rete virtuale Azure AD DS

Usare la procedura seguente per configurare la rete virtuale Azure AD DS (**HDIFabrikam-AADDSVNET**) per usare i server DNS personalizzati.

1. Individuare gli indirizzi IP dei server DNS personalizzati.
    1. Selezionare la `HDIFabrikam.com` risorsa Azure AD DS.
    1. In **Gestisci**selezionare **Proprietà**.
    1. Trovare gli indirizzi IP in **indirizzo IP nella rete virtuale**.

    ![Individuare indirizzi IP DNS personalizzati per Azure AD DS](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0096.png)

1. Configurare **HDIFabrikam-AADDSVNET** per l'uso di indirizzi IP personalizzati 10.0.0.4 e 10.0.0.5.

    1. In **Impostazioni**selezionare **server DNS**.
    1. Selezionare **personalizzata**.
    1. Nella casella di testo immettere il primo indirizzo IP (*10.0.0.4*).
    1. Selezionare **Salva**.
    1. Ripetere i passaggi per aggiungere l'altro indirizzo IP (*10.0.0.5*).

In questo scenario è stato configurato Azure AD DS per usare gli indirizzi IP 10.0.0.4 e 10.0.0.5, impostando lo stesso indirizzo IP nella rete virtuale Azure AD DS:

![Pagina server DNS personalizzati](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0098.png)

## <a name="securing-ldap-traffic"></a>Sicurezza del traffico LDAP

Lightweight Directory Access Protocol (LDAP) viene usato per leggere e scrivere in Azure Active Directory. È possibile rendere il traffico LDAP riservato e sicuro usando la tecnologia Secure Sockets Layer (SSL) o Transport Layer Security (TLS). È possibile abilitare LDAP su SSL (LDAPs) installando un certificato correttamente formattato.

Per altre informazioni su LDAP sicuro, vedere [configurare LDAPS per un dominio gestito Azure AD DS](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-admin-guide-configure-secure-ldap).

In questa sezione si crea un certificato autofirmato, si Scarica il certificato e si configurano i protocolli LDAP per il dominio gestito **HDIFabrikam** Azure AD DS.

Lo script seguente crea un certificato per **HDIFabrikam**. Il certificato viene salvato nel percorso *LocalMachine* .

```powershell
$lifetime = Get-Date
New-SelfSignedCertificate -Subject hdifabrikam.com `
-NotAfter $lifetime.AddDays(365) -KeyUsage DigitalSignature, KeyEncipherment `
-Type SSLServerAuthentication -DnsName *.hdifabrikam.com, hdifabrikam.com
```

> [!NOTE]  
> Per creare una richiesta \# di certificato TLS/SSL, è possibile usare qualsiasi applicazione o utilità che crea una richiesta valida di crittografia a chiave pubblica (PKCS) 10.

Verificare che il certificato sia installato nell'archivio **personale** del computer:

1. Avviare Microsoft Management Console (MMC).
1. Aggiungere lo snap-in **certificati** per la gestione dei certificati nel computer locale.
1. Espandere **Certificati (computer locale)**  > **Personale** > **Certificati**. Un nuovo certificato deve essere presente nell'archivio **personale** . Questo certificato viene emesso al nome host completo.

    ![Verificare la creazione del certificato locale](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0102.png)

1. Nel riquadro a destra fare clic con il pulsante destro del mouse sul certificato creato. Scegliere **tutte le attività**e quindi **Esporta**.

1. Nella pagina **Esporta chiave privata** selezionare **Sì, Esporta la chiave privata**. Il computer in cui verrà importata la chiave deve avere la chiave privata per leggere i messaggi crittografati.

    ![Pagina esportazione chiave privata dell'esportazione guidata certificati](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0103.png)

1. Nella pagina **formato file di esportazione** lasciare le impostazioni predefinite e quindi fare clic su **Avanti**.
1. Nella pagina **password** Digitare una password per la chiave privata. Per **crittografia**selezionare **TripleDES-SHA1**. Selezionare quindi **Avanti**.
1. Nella pagina **file da esportare** Digitare il percorso e il nome del file di certificato esportato, quindi fare clic su **Avanti**. Il nome del file deve avere un'estensione pfx. Questo file viene configurato nel portale di Azure per stabilire una connessione sicura.
1. Abilitare LDAPs per un dominio gestito Azure AD DS.
    1. Dal portale di Azure selezionare il dominio `HDIFabrikam.com` .
    1. In **Gestisci**selezionare **LDAP sicuro**.
    1. Nella pagina **LDAP sicuro** , in **LDAP sicuro**, selezionare **Abilita**.
    1. Individuare il file di certificato con estensione pfx esportato nel computer.
    1. Immettere la password del certificato.

    ![Abilitare LDAP sicuro](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0113.png)

1. Ora che è stato abilitato LDAPs, assicurarsi che sia raggiungibile abilitando la porta 636.
    1. Nel gruppo di risorse **HDIFabrikam-centralus** selezionare il gruppo di sicurezza di rete **AADDS-HDIFabrikam.com-NSG**.
    1. In **Impostazioni**selezionare **regole di sicurezza in ingresso**  >  **Aggiungi**.
    1. Nella pagina **Aggiungi regola di sicurezza in ingresso** immettere le proprietà seguenti e selezionare **Aggiungi**:

        | Proprietà | valore |
        |---|---|
        | Source (Sorgente) | Qualsiasi |
        | Intervalli di porte di origine | * |
        | Destination | Qualsiasi |
        | Intervallo di porte di destinazione | 636 |
        | Protocollo | Qualsiasi |
        | Azione | Allow |
        | Priorità | \<Desired number> |
        | Nome | Port_LDAP_636 |

    ![Finestra di dialogo "Aggiungi regola di sicurezza in ingresso"](./media/apache-domain-joined-create-configure-enterprise-security-cluster/add-inbound-security-rule.png)

**HDIFabrikamManagedIdentity** è l'identità gestita assegnata dall'utente. Il ruolo Collaboratore servizi di dominio HDInsight è abilitato per l'identità gestita che consente a questa identità di leggere, creare, modificare ed eliminare le operazioni di servizi di dominio.

![Creare un'identità gestita assegnata dall'utente](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0117.png)

## <a name="create-an-esp-enabled-hdinsight-cluster"></a>Creare un cluster HDInsight abilitato per ESP

Per questo passaggio sono necessari i prerequisiti seguenti:

1. Creare un nuovo gruppo di risorse *HDIFabrikam-westus* nella località **West US**.
1. Creare una rete virtuale che ospiterà il cluster HDInsight abilitato per ESP.

    ```powershell
    $virtualNetwork = New-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-WestUS' -Location 'West US' -Name 'HDIFabrikam-HDIVNet' -AddressPrefix 10.1.0.0/16
    $subnetConfig = Add-AzVirtualNetworkSubnetConfig -Name 'SparkSubnet' -AddressPrefix 10.1.0.0/24 -VirtualNetwork $virtualNetwork
    $virtualNetwork | Set-AzVirtualNetwork
    ```

1. Creare una relazione peer tra la rete virtuale che ospita Azure AD DS ( `HDIFabrikam-AADDSVNET` ) e la rete virtuale che ospiterà il cluster HDInsight abilitato per ESP ( `HDIFabrikam-HDIVNet` ). Usare il codice PowerShell seguente per eseguire il peering delle due reti virtuali.

    ```powershell
    Add-AzVirtualNetworkPeering -Name 'HDIVNet-AADDSVNet' -RemoteVirtualNetworkId (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-CentralUS').Id -VirtualNetwork (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-WestUS')

    Add-AzVirtualNetworkPeering -Name 'AADDSVNet-HDIVNet' -RemoteVirtualNetworkId (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-WestUS').Id -VirtualNetwork (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-CentralUS')
    ```

1. Creare un nuovo account Azure Data Lake Storage Gen2 denominato **Hdigen2store**. Configurare l'account con l'identità gestita dall'utente **HDIFabrikamManagedIdentity**. Per altre informazioni, vedere [usare Azure Data Lake storage Gen2 con i cluster HDInsight di Azure](../hdinsight-hadoop-use-data-lake-storage-gen2.md).

1. Configurare il DNS personalizzato nella rete virtuale **HDIFabrikam-AADDSVNET** .
    1. Passare alla portale di Azure > **gruppi di risorse**  >  **OnPremADVRG**  >  **HDIFabrikam-AADDSVNET**  >  **DNS Servers**.
    1. Selezionare **Custom (personalizzato** ) e immettere *10.0.0.4* e *10.0.0.5*.
    1. Selezionare **Salva**.

        ![Salvare le impostazioni DNS personalizzate per una rete virtuale](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0123.png)

1. Creare un nuovo cluster HDInsight Spark abilitato per ESP.
    1. Selezionare **personalizzata (dimensioni, impostazioni, app)**.
    1. Immettere i dettagli per le **nozioni di base** (sezione 1). Verificare che il **tipo di cluster** sia **Spark 2,3 (HDI 3,6)**. Verificare che il **gruppo di risorse** sia **HDIFabrikam-centralus**.

    1. Per **sicurezza e rete** (sezione 2), specificare i dettagli seguenti:
        * In **Enterprise Security Package**selezionare **abilitato**.
        * Selezionare **Amministrazione cluster utente** e selezionare l'account **HDIAdmin** creato come utente amministratore locale. Fare clic su **Seleziona**.
        * Selezionare il **gruppo di accesso al cluster**  >  **HDIUserGroup**. Tutti gli utenti aggiunti a questo gruppo in futuro saranno in grado di accedere ai cluster HDInsight.

            ![Selezionare il gruppo di accesso al cluster HDIUserGroup](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0129.jpg)

    1. Completare gli altri passaggi della configurazione del cluster e verificare i dettagli sul **Riepilogo del cluster**. Selezionare **Crea**.

1. Accedere all'interfaccia utente di Ambari per il cluster appena creato in `https://CLUSTERNAME.azurehdinsight.net` . Usare il nome utente amministratore `hdiadmin@hdifabrikam.com` e la relativa password.

    ![Finestra di accesso dell'interfaccia utente di Apache Ambari](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0135.jpg)

1. Dal dashboard del cluster selezionare **ruoli**.
1. Nella pagina **ruoli** , in **Assegna ruoli a questi**, accanto al ruolo **amministratore cluster** immettere il gruppo *hdiusergroup*. 

    ![Assegnare il ruolo di amministratore del cluster a hdiusergroup](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0137.jpg)

1. Aprire il client di Secure Shell (SSH) e accedere al cluster. Usare il **hdiuser** creato nell'istanza di Active Directory locale.

    ![Accedere al cluster usando il client SSH](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0139.jpg)

Se è possibile accedere con questo account, il cluster ESP è stato configurato correttamente per la sincronizzazione con l'istanza di Active Directory locale.

## <a name="next-steps"></a>Passaggi successivi

Leggi [un'introduzione a Apache Hadoop sicurezza con ESP](hdinsight-security-overview.md).
