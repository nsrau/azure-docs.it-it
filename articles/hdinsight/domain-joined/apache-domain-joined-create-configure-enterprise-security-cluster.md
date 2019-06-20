---
title: Creare e configurare i cluster Enterprise Security Package in Azure HDInsight
description: Informazioni su come creare e configurare i cluster Enterprise Security Package in Azure HDInsight
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: howto
ms.date: 05/09/2019
ms.openlocfilehash: 7457c06f9f151cb310704a985c79572c7b770859
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/17/2019
ms.locfileid: "67166220"
---
# <a name="create-and-configure-enterprise-security-package-clusters-in-azure-hdinsight"></a>Creare e configurare i cluster Enterprise Security Package in Azure HDInsight

Enterprise Security Package per HDInsight di Azure consente di accedere per l'autenticazione basata su Active Directory, supporto multiutente e controllo degli accessi in base al ruolo per i cluster Apache Hadoop in Azure. I cluster HDInsight ESP consentono alle organizzazioni, che rispettano i criteri di sicurezza aziendali rigidi, per elaborare in modo sicuro dati sensibili.

L'obiettivo di questa guida è configurato correttamente le risorse necessarie in modo che in locale gli utenti possono accedere a una ESP abilitato cluster HDInsight. Questo articolo descrive i passaggi necessari per creare un Cluster HDInsight di Azure Enterprise Security Package abilitata. La procedura illustra la creazione di una macchina virtuale IaaS di Windows con Active Directory e i servizi DNS (Domain Name) abilitato. Questo server deve agire come una sostituzione per il **effettivi** nell'ambiente locale e ti permetterà di continuare la procedura di installazione e configurazione in modo che è possibile ripetere questa operazione in un secondo momento nel proprio ambiente. Questa Guida aiuterà anche a creare un ambiente di identità ibrida con sincronizzazione degli hash delle password con Azure Active Directory.

Questa guida è progettata per integrare [usare Enterprise Security Package in HDInsight](apache-domain-joined-architecture.md)

Prima di usare questo processo nel proprio ambiente, configurare Active Directory e i servizi DNS (Domain Name). Inoltre, abilitare Azure Active Directory e sincronizzazione locale degli account utente in Azure Active Directory.

![diagramma dell'architettura](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image002.png)

## <a name="create-on-premises-environment"></a>Creare l'ambiente locale

Panoramica In questa sezione si utilizzerà un modello di distribuzione rapida di Azure per creare nuove macchine virtuali, configurare i servizi DNS (Domain Name) e una nuova foresta di Active Directory.

1. Passare a [creare una VM di Azure con una nuova foresta AD](https://azure.microsoft.com/resources/templates/active-directory-new-domain/), visualizzare il modello di distribuzione rapida.

1. Fare clic su **Distribuisci in Azure**.
1. Accedere alla sottoscrizione di Azure.
1. Nel **creare una VM di Azure con una nuova foresta AD** schermata, seguire questa procedura:
    1. Selezionare la sottoscrizione in cui si desidera che le risorse distribuite dal **sottoscrizione** elenco a discesa.
    1. Selezionare **Crea nuovo** accanto a **gruppo di risorse** e immettere il nome **OnPremADVRG**
    1. Immettere i dettagli seguenti per il resto dei campi modello:

        * **Località**: Stati Uniti centrali
        * **Nome utente amministratore**: HDIFabrikamAdmin
        * **Password amministratore**: < YOUR_PASSWORD >
        * **Dominio**: HDIFabrikam.com
        * **Prefisso DNS**: hdifabrikam

        ![Modello di creare macchine Virtuali di Azure e di foresta di Active Directory](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-azure-vm-ad-forest.png)

    1. Fare clic su **acquisto**
    1. Monitorare la distribuzione e attendere il completamento.
    1. Verificare che le risorse vengono create nel gruppo di risorse corretto `OnPremADVRG`.

## <a name="configure-users-and-groups-for-cluster-access"></a>Configurare utenti e gruppi per l'accesso del cluster

Panoramica In questa sezione si creerà gli utenti che avranno accesso al cluster HDInsight al termine di questa Guida.

1. Connettersi al controller di dominio tramite Desktop remoto.
    1. Se si usa il modello indicato all'inizio, il controller di dominio è una macchina virtuale denominata **adVM** nel `OnPremADVRG` gruppo di risorse.
    1. Passare al portale di Azure > **gruppi di risorse** > **OnPremADVRG** > **adVM** > **connessione**.
    1. Scegliere il **RDP** scheda e quindi fare clic su **Scarica File RDP**.
    1. Salvare il file nel computer e aprirlo.
    1. Alla richiesta delle credenziali, usare `HDIFabrikam\HDIFabrikamAdmin` come nome utente e quindi immettere la password scelta per l'account amministratore.

1. Quando si apre la sessione Desktop remoto nel controller di dominio della macchina virtuale, avviare **Active Directory Users and Computers** dalle **Server Manager** dashboard. Fare clic su **degli strumenti** in alto a destra e quindi **Active Directory Users and Computers** dall'elenco a discesa.

    ![Gestione di Server Manager aprire Active Directory](./media/apache-domain-joined-create-configure-enterprise-security-cluster/server-manager-active-directory-screen.png)

1. Creare due nuovi utenti **HDIAdmin**, **HDIUser**. Questi due utenti useranno per accedere al cluster HDInsight.

    1. Nel **Active Directory Users and Computers** schermata, fare clic su **azione** > **nuovo** > **utente**.

        ![Crea nuovo utente di Active Directory](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-new-user.png)

    1. Nel **nuovo oggetto - utente** schermata, immettere `HDIUser` come il **nome accesso utente** e fare clic su **Avanti**.

        ![Crea primo utente amministratore](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image020.png)

    1. Nella finestra popup visualizzata, immettere la password desiderata per il nuovo account. Selezionare la casella con la dicitura **Nessuna scadenza Password**. HDIClick **OK**.
    1. Fare clic su **fine** per creare il nuovo account.
    1. Creare un altro utente `HDIAdmin`.

        ![Creare seconda utente amministratore](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image024.png)

1. Nel **Active Directory Users and Computers** schermata, fare clic su **azione** > **nuovo** > **gruppo**. Creare `HDIUserGroup` come un nuovo gruppo.

    ![Crea nuovo gruppo di Active Directory](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-new-group.png)

    ![Crea nuovo gruppo 2](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image028.png)

1. Aggiungere il **HDIUser** creato nel passaggio precedente per il **HDIUserGroup** come membro.

    1. Fare clic con il pulsante destro sul **HDIUserGroup** e fare clic su **proprietà**.
    1. Passare a **membri** scheda e fare clic su **Add**.
    1. Invio `HDIUser` nella casella **immettere i nomi degli oggetti da selezionare** e fare clic su **OK**.
    1. Ripetere i passaggi precedenti per l'altro account `HDIAdmin`

        ![aggiungere membri al gruppo](./media/apache-domain-joined-create-configure-enterprise-security-cluster/active-directory-add-users-to-group.png)

È stato creato l'ambiente Active Directory, insieme ai due utenti e un gruppo di utenti per l'accesso al cluster HDInsight.

Questi utenti verranno sincronizzati con Azure AD.

### <a name="create-a-new-azure-active-directory"></a>Creare un nuovo Azure Active Directory

1. Accedere al portale di Azure.
1. Fare clic su **crea una risorsa** e digitare **directory**. Selezionare **Azure Active Directory** > **creare**.
1. Immettere **HDIFabrikam** sotto **nome organizzazione**.
1. Immettere **HDIFabrikamoutlook** sotto **nome di dominio iniziale**.
1. Fare clic su **Create**(Crea).
1. A sinistra nel portale di Azure, fare clic su **Azure Active Directory**.
1. Se necessario, fare clic su **Cambia directory** per passare alla nuova directory creata **HDIFabrikamoutlook**.
1. Sotto **Manage** fare clic su **nomi di dominio personalizzati** > **Aggiungi dominio personalizzato**.
1. Immettere **HDIFabrikam.com** sotto **nome di dominio personalizzato** e fare clic su **Aggiungi dominio**.

![creare un nuovo azure active directory](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-new-directory.png)

![creare un nuovo dominio personalizzato](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-custom-domain.png)

## <a name="configure-your-azure-ad-tenant"></a>Configurare il tenant di Azure AD

Panoramica A questo punto si configurerà il tenant di Azure AD in modo che gli utenti e gruppi locali, è possibile sincronizzare Active Directory nel cloud.

1. Creare un amministratore di tenant di Active Directory.
    1. Accedere al portale di Azure e selezionare il tenant di Azure AD **HDIFabrikam**
    1. Selezionare **gli utenti** sotto **Gestisci** e quindi **nuovo utente**.
    1. Immettere i dettagli seguenti per il nuovo utente:

        * Nome: fabrikamazureadmin
        * Nome utente: fabrikamazureadmin@hdifabrikam.com
        * Password: una password sicura di propria scelta

    1. Fare clic sui **gruppi** , quindi cercare **AAD DC Administrators**e fare clic su **selezionare**.

        ![Gruppi](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image038.png)

    1. Fare clic sui **ruolo della Directory** della sezione e selezionare **amministratore globale** sul lato destro. Fare clic su **OK**.

        ![Ruolo directory](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image040.png)

    1. Immettere una password per l'utente. Fare clic su **Create**(Crea).

1. Se si desidera modificare la password per l'utente appena creato <fabrikamazureadmin@hdifabrikam.com>. Accedere al portale di Azure usando che l'identità e quindi verrà richiesto di cambiare la password.

## <a name="sync-on-premises-users-to-azure-ad"></a>Sincronizza gli utenti locali con Azure AD

### <a name="download-and-install-microsoft-azure-active-directory-connect"></a>Scaricare e installare Microsoft Azure Active Directory connect

1. [Scaricare Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594).

1. Installare Microsoft Azure Active Directory connect nel Controller di dominio.
    1. Aprire il file eseguibile scaricato nel passaggio precedente e accettare le condizioni di licenza. Scegliere **Continua**.

        ![Azure AD Connect](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image052.png)

    1. Fare clic su **Usa impostazioni rapide** e completare l'installazione.

        ![Usa impostazioni rapide](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image054.png)

### <a name="configure-sync-with-on-premises-domain-controller"></a>Configurare la sincronizzazione con il controller di dominio locale

1. Nel **ad Azure AD Connect** schermata, immettere il nome utente e la password dell'amministratore globale per Azure AD. Fare clic su **successivo**. Questo è il nome utente `fabrikamazureadmin@hdifabrikam.com` creato al momento della configurazione del tenant di Active Directory.
    ![Connessione ad Azure AD](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image058.png)
1. Nel **Connect in Active Directory Domain Services** schermata, immettere il nome utente e la password per un account amministratore dell'organizzazione. Fare clic su **successivo**. Questo è il nome utente `HDIFabrikam\HDIFabrikamAdmin` e la relativa password corrispondente creato in precedenza.

   ![Connettersi ai servizi di dominio Active Directory](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image060.png)
1. Nel **configurazione dell'accesso AD Azure** pagina, fare clic su **successivo**.
    ![Configurazione dell'accesso AD Azure](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image062.png)
1. Pronto per la configurazione dello schermo, scegliere **installare**.
    ![install](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image064.png)
1. Quando la **configurazione completata** viene visualizzata la schermata, fare clic su **uscita**.
    ![Configurazione completata](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image078.png)

1. Dopo aver completata la sincronizzazione, verificare se gli utenti che è stato creato il modello IAAS di Active Directory vengono sincronizzati con Azure Active Directory.
    1. Accedere al portale di Azure.
    1. Selezionare **Azure Active Directory** > **HDIFabrikam** > **utenti**.

### <a name="create-an-user-assigned-managed-identity"></a>Creare un'identità gestita assegnata dall'utente

Creare un'identità gestita assegnata dall'utente che verrà usata per configurare Azure Active Directory Domain Services (Azure AD-DS). Per altre informazioni sulla creazione di un'identità gestita assegnata dall'utente, vedere [Create, list, delete o assegnare un ruolo a un'identità gestito assegnata dall'utente tramite il portale di Azure](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md).

1. Accedere al portale di Azure.
1. Fare clic su **crea una risorsa** e digitare **identità gestita**. Selezionare **utente assegnato identità gestita** > **creare**.
1. Immettere **HDIFabrikamManagedIdentity** come la **nome risorsa**.
1. Selezionare la propria sottoscrizione.
1. Sotto **gruppo di risorse** fare clic su **Crea nuovo** e immettere **HDIFabrikam-CentralUS**.
1. Selezionare **Stati Uniti centrali** sotto **posizione**.
1. Fare clic su **Create**(Crea).

![creare una nuova identità gestita assegnata dall'utente](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image082.png)

### <a name="enable-azure-active-directory-domain-services"></a>Abilitare Azure Active Directory Domain Services

Per altre informazioni vedere [Abilitare Azure Active Directory Domain Services tramite il portale di Azure](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started).

1. Creare la rete virtuale all'host di Azure Active Directory Domain Services. Eseguire il codice di powershell seguente.

    ```powershell
    Connect-AzAccount
    Get-AzSubscription
    Set-AzContext -Subscription 'SUBSCRIPTION_ID'
    $virtualNetwork = New-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-CentralUS' -Location 'Central US' -Name 'HDIFabrikam-AADDSVNET' -AddressPrefix 10.1.0.0/16
    $subnetConfig = Add-AzVirtualNetworkSubnetConfig -Name 'AADDS-subnet' -AddressPrefix 10.1.0.0/24 -VirtualNetwork $virtualNetwork
    $virtualNetwork | Set-AzVirtualNetwork
    ```

1. Accedere al portale di Azure.
1. Fare clic su **Crea risorsa**, immettere **servizi di dominio** e selezionare **Azure AD Domain Services**.
1. Nel **nozioni di base** schermata completare i passaggi seguenti:
    1. Sotto **nome della Directory** selezionare Azure Active Directory creata per questa esercitazione **HDIFabrikam**.
    1. Immettere un **nome di dominio DNS** dei **HDIFabrikam.com**.
    1. Selezionare la propria sottoscrizione.
    1. Specificare il gruppo di risorse **HDIFabrikam-CentralUS** e il **posizione** dei **Stati Uniti centrali**.

        ![dettagli di base di Azure Active Directory Domain Services](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image084.png)

1. Nel **Network** schermata completo, selezionare la rete (**HDIFabrikam-VNET**) e la subnet (**AADDS-subnet**) che è stato creato con lo script di powershell precedente. In alternativa è possibile usare la **Crea nuovo** opzione per creare una rete virtuale a questo punto.

    ![Selezionare rete](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image086.png)

1. Nel **gruppo di amministratori** dello schermo, viene visualizzata una notifica che un gruppo denominato **AAD DC Administrators** è già stato creato per amministrare questo gruppo. È possibile modificare l'appartenenza di questo gruppo, ma non obbligatorio per i passaggi di questa esercitazione. Fare clic su **OK**.

    ![gruppo di amministratori di visualizzazione](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image088.png)

1. Nel **sincronizzazione** schermata, abilitare la sincronizzazione completa, selezionando **tutte** e quindi fare clic su **OK**.

    ![abilitare la sincronizzazione](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image090.png)

1. Nel **Summary** dello schermo, verificare i dettagli per Azure AD-DS e fare clic su **Ok**.

    ![Verificare i dettagli](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image092.png)

1. Dopo aver abilitato Azure Active Directory Domain Services, un server DNS (Domain Name Service) locale viene eseguito nelle macchine virtuali (VM) AD.

### <a name="configure-your-azure-ad-ds-virtual-network"></a>Configurare la rete virtuale di Azure Active Directory Domain Services

I passaggi descritti in questa sezione aiuterà a configurare la rete virtuale di Azure Active Directory Domain Services (**HDIFabrikam AADDSVNET**) usare i server DNS personalizzati.

1. Individuare gli indirizzi IP dei server DNS personalizzato. Fare clic sui **HDIFabrikam.com** risorsa di Active Directory Domain Services, fare clic su **delle proprietà** sotto **Gestisci**   ed esaminare gli indirizzi IP elencati sotto **IP Indirizzo su rete virtuale**.

    ![Individuare gli indirizzi IP DNS personalizzati per Azure AD-DS](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image096.png)

1. Configurare **HDIFabrikam AADDSVNET** verso gli indirizzi IP personalizzati `10.0.0.4` e `10.0.0.5`.

    1. Selezionare **server DNS** sotto il **impostazioni** categoria. quindi fare clic sul pulsante di opzione accanto a **Custom**, immettere il primo indirizzo IP (10.0.0.4) nella casella di testo seguente e fare clic su **salvare**.
    1. Aggiungere altri indirizzi IP (10.0.0.5) usando la stessa procedura.

1. Nello scenario di Azure Active Directory Domain Services è stato configurato per usare gli indirizzi IP 10.0.0.4 e 10.0.0.5, impostare lo stesso IP indirizzo su rete virtuale AADDS come illustrato nell'immagine seguente.

    ![visualizzare i server dns personalizzato](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image098.png)

## <a name="securing-ldap-traffic"></a>Protezione del traffico LDAP

Lightweight Directory Access Protocol (LDAP) viene usato per leggere e scrivere in Active Directory. È possibile rendere LDAP del traffico riservati e sicuro da utilizza Secure Sockets Layer (SSL) / tecnologia Transport Layer Security (TLS). È possibile abilitare LDAP su SSL (LDAPS) tramite l'installazione di un certificato formattato correttamente.

Per altre informazioni su accesso LDAP sicuro, vedere [configurare LDAP sicuro (LDAPS) per un Azure AD Domain Services managed domain](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-admin-guide-configure-secure-ldap).

In questa sezione si crea un certificato autofirmato, scaricare il certificato e configurare LDAP sicuro (LDAPS) per il **hdifabrikam** dominio gestito di Azure Active Directory Domain Services.

Lo script seguente crea un certificato per hdifabrikam. Il certificato viene salvato nel percorso "LocalMachine".

> [!Note] 
> Un'applicazione che crea un PKCS valido o utilità \#10 richieste possono essere usate per formare la richiesta di certificato SSL.

```powershell
$lifetime = Get-Date
New-SelfSignedCertificate -Subject hdifabrikam.com `
-NotAfter $lifetime.AddDays(365) -KeyUsage DigitalSignature, KeyEncipherment `
-Type SSLServerAuthentication -DnsName *.hdifabrikam.com, hdifabrikam.com
```

Verificare che il certificato sia installato nel computer\'archivio personale s. Completare questi passaggi:

1. Avviare Microsoft Management Console (MMC).
1. Aggiungere lo snap-in certificati che consente di gestire i certificati nel computer locale.
1. Espandere **certificati (Computer locale)** , espandere **personali**, quindi espandere **certificati**. Un nuovo certificato deve esistere nell'archivio personale. Questo certificato viene rilasciato al nome host completo.

    ![Verificare la creazione del certificato](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image102.png)

1. Nel riquadro di destra, fare doppio clic sul certificato che è stato creato nel passaggio precedente, scegliere **tutte le attività**, quindi fare clic su **esportare**.

1. Nel **Esporta chiave privata** pagina, fare clic su **Sì, Esporta la chiave privata,** . La chiave privata è necessaria per i messaggi da leggere dal computer in cui verrà importata la chiave crittografati.

    ![esporta la chiave privata](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image103.png)

1. Nel **formato File di esportazione** pagina, lasciare le impostazioni predefinite e quindi fare clic su **successivo**. 
1. Nel **Password** , digitare una password per la chiave privata, seleziona **TripleDES, SHA1** per **crittografia** e fare clic su **Next**.
1. Nel **File da esportare** pagina, digitare il percorso e il nome per il file di certificato esportato e quindi fare clic su **successivo**.
1. Il nome del file deve essere con estensione pfx, questo file è configurato nel portale di Azure per stabilire una connessione protetta.
1. Abilitare LDAP sicuro (LDAPS) per un dominio gestito di Azure AD Domain Services.
    1. Selezionare il dominio **HDIFabrikam.com** dal portale di Azure.
    1. Fare clic su **l'accesso LDAP sicuro** sotto **gestire**.
    1. Nel **LDAP sicuro** schermata, fare clic su **abilitare** sotto **Secure LDAP**.
    1. Cercare il file di certificato PFX esportato nel computer.
    1. Immettere la password del certificato.

    ![abilitare l'accesso ldap sicuro](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image113.png)

1. Ora che è abilitato LDAP sicuro, assicurarsi che sia raggiungibile abilitando la porta 636.
    1. Fare clic sul gruppo di sicurezza di rete **AADDS-HDIFabrikam.com-NSG** nel **HDIFabrikam-CentralUS** gruppo di risorse.
    1. Sotto **le impostazioni** fare clic su **regole di sicurezza in ingresso** > **Aggiungi**.
    1. Nel **Aggiungi regola di sicurezza in ingresso** schermata immettere le proprietà seguenti, quindi scegliere **Add**:

        | Proprietà | Value |
        |---|---|
        | `Source` | Qualsiasi |
        | Intervalli di porte di origine | * |
        | Destination | Qualsiasi |
        | Intervallo di porte di destinazione | 636 |
        | Protocol | Qualsiasi |
        | Azione | CONSENTI |
        | Priorità | <Desired Number> |
        | Name | Port_LDAP_636 |

    ![regola di sicurezza in ingresso](./media/apache-domain-joined-create-configure-enterprise-security-cluster/add-inbound-security-rule.png)

1. `HDIFabrikamManagedIdentity` è l'assegnata dall'utente identità gestita, il ruolo di collaboratore servizi di dominio HDInsight è abilitato per l'identità gestita che abiliterà questa identità leggere, creare, modificare ed eliminare le operazioni di servizi di dominio.

    ![Creare gestito identità assegnata all'utente](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image117.png)

## <a name="creating-enterprise-security-package-enabled-hdinsight-cluster"></a>Creazione di Enterprise Security Package abilitato per il cluster HDInsight

Questo passaggio richiede i prerequisiti seguenti:

1. Creare un nuovo gruppo di risorse `HDIFabrikam-WestUS` nella località `West US`.
1. Creare una macchina abilitata la rete che ospiterà ESP cluster HDInsight.

    ```powershell
    $virtualNetwork = New-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-WestUS' -Location 'West US' -Name 'HDIFabrikam-HDIVNet' -AddressPrefix 10.1.0.0/16
    $subnetConfig = Add-AzVirtualNetworkSubnetConfig -Name 'SparkSubnet' -AddressPrefix 10.1.0.0/24 -VirtualNetwork $virtualNetwork
    $virtualNetwork | Set-AzVirtualNetwork
    ```

1. Creare una relazione peer tra la rete virtuale che ospita AADDS (`HDIFabrikam-AADDSVNET`) e HDInsight cluster abilitato per la rete virtuale che ospiterà il ESP (`HDIFabrikam-HDIVNet`). Usare il codice di powershell seguente per eseguire il peering delle due reti virtuali.

    ```powershell
    Add-AzVirtualNetworkPeering -Name 'HDIVNet-AADDSVNet' -RemoteVirtualNetworkId (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-CentralUS').Id -VirtualNetwork (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-WestUS')

    Add-AzVirtualNetworkPeering -Name 'AADDSVNet-HDIVNet' -RemoteVirtualNetworkId (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-WestUS').Id -VirtualNetwork (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-CentralUS')
    ```

1. Creare un nuovo account di Azure Data Lake Storage Gen2 **Hdigen2store**, che viene configurato con l'identità dell'utente gestito **HDIFabrikamManagedIdentity**. Per altre informazioni sulla creazione di account Data Lake Storage Gen2 abilitato con utente identità gestita, vedere [usare Azure Data Lake Storage Gen2 con Azure HDInsight cluster](../hdinsight-hadoop-use-data-lake-storage-gen2.md).

1. Configurare DNS personalizzato nel **HDIFabrikam AADDSVNET** rete virtuale.
    1. Passare al portale di Azure > **gruppi di risorse** > **OnPremADVRG** > **HDIFabrikam AADDSVNET**  >   **I server DNS**.
    1. Selezionare **Custom** e immettere `10.0.0.4` e `10.0.0.5`.
    1. Fare clic su **Save**.

        ![salvare le impostazioni dns personalizzate](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image123.png)

1. Creare un nuovo cluster HDInsight Spark abilitati ESP.
    1. Fare clic su **Custom (size, le impostazioni, App)** .
    2. Immettere i dettagli desiderati per la sezione 1 **nozioni di base**. Verificare che il **tipo di Cluster** viene **Spark 2.3 (HDI 3.6)** e il **gruppo di risorse** è **HDIFabrikam-CentralUS**

    1. Nella sezione 2 **sicurezza e rete**, completare i passaggi seguenti:
        1. Fare clic su **abilitate** sotto **Enterprise Security Package**.
        1. Fare clic su **l'utente amministratore del Cluster** e selezionare il **HDIAdmin** account creato in precedenza come utente amministratore locale. Fare clic su **Seleziona**.

        1. Fare clic su **gruppo di accesso del Cluster** e quindi selezionare **HDIUserGroup**. Qualsiasi utente che si aggiunge a questo gruppo in futuro sarà in grado di accedere a cluster HDInsight.

            ![Selezionare il gruppo di accesso del cluster](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image129.jpg)

    1. Completare gli altri passaggi di configurazione del cluster e verificare i dettagli sul **riepilogo Cluster**. Fare clic su **Create**(Crea).

1. Accedi alla UI Ambari per il cluster appena creato nella `https://CLUSTERNAME.azurehdinsight.net` usando il proprio nome utente amministratore `hdiadmin@hdifabrikam.com` e la password.

    ![accedere ad Ambari](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image135.jpg)

1. Fare clic su **ruoli** dal dashboard del cluster.
1. Nel **ruoli** pagina, immettere il gruppo **hdiusergroup** assegnarlo al **amministrazione Cluster** ruolo sotto **assegnare ruoli a questi**.

    ![assegnare il ruolo di amministratore cluster hdiusergroup](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image137.jpg)

1. Aprire il client SSH e un account di accesso al cluster tramite il **hdiuser** creato in precedenza in Active Directory locale.

    ![accedere al cluster con SSH](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image139.jpg)

Se si è in grado di eseguire l'accesso con questo account, quindi è stato configurato il cluster ESP correttamente per eseguire la sincronizzazione con active directory locale.

## <a name="next-steps"></a>Passaggi successivi

* [Introduzione alla sicurezza Apache Hadoop con Enterprise Security Package](apache-domain-joined-introduction.md)
