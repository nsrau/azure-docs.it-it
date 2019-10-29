---
title: Creare, configurare cluster Enterprise Security Package-Azure
description: Informazioni su come creare e configurare cluster Enterprise Security Package in Azure HDInsight
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: conceptual
ms.date: 05/09/2019
ms.openlocfilehash: 6247a6b2eeeb421773400cc60d05696f973a1dff
ms.sourcegitcommit: 38251963cf3b8c9373929e071b50fd9049942b37
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73044679"
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
1. Nella pagina **creare una macchina virtuale di Azure con una nuova foresta di Active Directory** :
    1. Dall'elenco a discesa **sottoscrizione** selezionare la sottoscrizione in cui si desidera distribuire le risorse.
    1. Accanto a **gruppo di risorse**selezionare **Crea nuovo**e immettere il nome *OnPremADVRG*.
    1. Per il resto dei campi del modello, immettere i dettagli seguenti:

        * **Località**: Stati Uniti centrali
        * **Nome utente amministratore**: HDIFabrikamAdmin
        * **Password amministratore**: \<password >
        * **Nome di dominio**: HDIFabrikam.com
        * **Prefisso DNS**: hdifabrikam

        ![Modello per creare una macchina virtuale di Azure con una nuova foresta Azure AD](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-azure-vm-ad-forest.png)

    1. Selezionare **Acquisto**.
    1. Monitorare la distribuzione e attenderne il completamento.
    1. Assicurarsi che le risorse vengano create nel gruppo di risorse corretto, **OnPremADVRG**.

## <a name="configure-users-and-groups-for-cluster-access"></a>Configurare utenti e gruppi per l'accesso al cluster

In questa sezione verranno creati gli utenti che avranno accesso al cluster HDInsight entro la fine di questa guida.

1. Connettersi al controller di dominio utilizzando Desktop remoto.
    1. Se è stato usato il modello indicato all'inizio, il controller di dominio è una macchina virtuale denominata **adVM** nel gruppo di risorse **OnPremADVRG** .
    1. Nella portale di Azure selezionare gruppi di **risorse** > **OnPremADVRG** > **adVM** > **Connect**.
    1. Selezionare la scheda **rdp** > **scaricare il file RDP**.
    1. Salvare il file nel computer e aprirlo.
    1. Quando vengono richieste le credenziali, usare *HDIFabrikam\HDIFabrikamAdmin* come nome utente. Immettere quindi la password scelta per l'account amministratore.

1. Quando si apre la sessione di Desktop remoto nella macchina virtuale del controller di dominio, nel dashboard **Server Manager** aprire **Active Directory utenti e computer**. Nell'angolo in alto a destra selezionare **strumenti** > **Active Directory utenti e computer**.

    ![Nel Dashboard Server Manager aprire Active Directory gestione](./media/apache-domain-joined-create-configure-enterprise-security-cluster/server-manager-active-directory-screen.png)

1. Creare due nuovi utenti: **HDIAdmin** e **HDIUser**. Questi due utenti accederanno ai cluster HDInsight.

    1. Nella pagina **Active Directory utenti e computer** selezionare **azione** > **nuovo** **utente** > .

        ![Creare un nuovo utente Active Directory](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-active-directory-user.png)

    1. Nella pagina **nuovo oggetto-utente** , per il **nome di accesso utente**, immettere *HDIUser*. Quindi selezionare **Avanti**.

        ![Creare il primo oggetto utente amministratore](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0020.png)

    1. Nella finestra popup visualizzata immettere una password per il nuovo account. Selezionare **Nessuna scadenza Password** > **OK**.
    1. Selezionare **Finish (fine** ) per creare il nuovo account.
    1. Creare un altro utente denominato *HDIAdmin*.

        ![Creare un secondo oggetto utente amministratore](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0024.png)

1. Nella pagina **Active Directory utenti e computer** selezionare **azione** > **nuovo** **gruppo**di > . Aggiungere un gruppo denominato *HDIUserGroup*.

    ![Creare un nuovo gruppo di Active Directory](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-active-directory-group.png)

    ![Creazione di un nuovo oggetto](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0028.png)

1. Aggiungere **HDIUser** a **HDIUserGroup** come membro del gruppo.

    1. Fare clic con il pulsante destro del mouse su **HDIUserGroup** e scegliere **Proprietà**.
    1. Nella scheda **membri** selezionare **Aggiungi**.
    1. Nella casella **immettere i nomi degli oggetti da selezionare** immettere *HDIUser*. Selezionare **OK**.
    1. Ripetere i passaggi precedenti per l'account **HDIAdmin** .

        ![Aggiungere il membro HDIUser al gruppo HDIUserGroup](./media/apache-domain-joined-create-configure-enterprise-security-cluster/active-directory-add-users-to-group.png)

A questo punto è stato creato l'ambiente di Active Directory. Sono stati aggiunti due utenti e un gruppo di utenti che possono accedere al cluster HDInsight.

Gli utenti verranno sincronizzati con Azure AD.

### <a name="create-an-azure-ad-directory"></a>Creare una directory di Azure AD

1. Accedere al portale di Azure.
1. Selezionare **Crea una risorsa** e digitare la *directory*. Selezionare **Azure Active Directory** > **Crea**.
1. In **nome organizzazione**immettere *HDIFabrikam*.
1. In **nome di dominio iniziale**immettere *HDIFabrikamoutlook*.
1. Selezionare **Create** (Crea).

    ![Creare una directory di Azure AD](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-new-directory.png)

1. Sul lato sinistro del portale di Azure selezionare **Azure Active Directory**.
1. Se necessario, selezionare Cambia **directory** per passare alla nuova directory **HDIFabrikamoutlook** .
1. In **Gestisci**selezionare **nomi di dominio personalizzati** > **Aggiungi dominio personalizzato**.
1. In **nome dominio personalizzato**immettere *HDIFabrikam.com* e selezionare **Aggiungi dominio**.

![Creazione di un dominio personalizzato](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-custom-domain.png)

## <a name="configure-your-azure-ad-tenant"></a>Configurare il tenant di Azure AD

A questo punto si configurerà il tenant di Azure AD in modo che sia possibile sincronizzare gli utenti e i gruppi dall'istanza di Active Directory locale al cloud.

1. Creare un Active Directory amministratore tenant.
    1. Accedere al portale di Azure e selezionare il tenant di Azure AD, **HDIFabrikam**.
    1. In **Gestisci**selezionare **utenti** > **nuovo utente**.
    1. Immettere i dettagli seguenti per il nuovo utente:
        * **Nome**: fabrikamazureadmin
        * **Nome utente**: fabrikamazureadmin@hdifabrikam.com
        * **Password**: password sicura di propria scelta

    1. Nella sezione **gruppi** cercare gli amministratori di **AAD DC** e fare clic su **Seleziona**.

        ![Finestra di dialogo gruppi di Azure AD](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0038.png)

    1. Aprire la sezione **ruolo della directory** e a destra selezionare **amministratore globale** > **OK**.

        ![Finestra di dialogo Azure AD Role](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0040.png)

    1. Immettere una password per l'utente. Selezionare quindi **Crea**.

1. Se si vuole modificare la password per l'utente appena creato \<fabrikamazureadmin@hdifabrikam.com>, usare l'identità per accedere al portale di Azure. Verrà quindi richiesto di modificare la password.

## <a name="sync-on-premises-users-to-azure-ad"></a>Sincronizzare gli utenti locali con Azure AD

### <a name="download-and-install-azure-ad-connect"></a>Scaricare e installare Azure AD Connect

1. [Scaricare Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594).

1. Installare Azure AD Connect nel controller di dominio.

    1. Aprire il file eseguibile scaricato e accettare le condizioni di licenza. Selezionare **Continua**.

        ![Pagina "Welcome to Azure AD Connect"](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0052.png)

    1. Selezionare **Usa impostazioni rapide** e completare l'installazione.

        ![Impostazioni di Azure AD Connect Express](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0054.png)

### <a name="configure-a-sync-with-the-on-premises-domain-controller"></a>Configurare una sincronizzazione con il controller di dominio locale

1. Nella pagina **Connetti a Azure ad** immettere il nome utente e la password dell'amministratore globale per Azure ad. Usare il nome utente `fabrikamazureadmin@hdifabrikam.com` creato al momento della configurazione del tenant di Active Directory. Quindi selezionare **Avanti**. 

    ![Pagina "Connetti a Azure AD"](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0058.png)

1. Nella pagina **Connetti a Active Directory Domain Services** immettere il nome utente e la password per un account amministratore dell'organizzazione. Usare il nome utente `HDIFabrikam\HDIFabrikamAdmin` e la relativa password creati in precedenza. Quindi selezionare **Avanti**. 

   ![Pagina "Connetti a Azure AD"](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0060.png)
1. Nella pagina **configurazione dell'accesso Azure ad** fare clic su **Avanti**.
   ![pagina "configurazione Azure AD accesso"](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0062.png)

1. Nella pagina **pronto per la configurazione** selezionare **Installa**.

   ![Pagina "pronto per la configurazione"](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0064.png)

1. Nella pagina **La configurazione è stata completata** selezionare **Esci**.
   ![pagina "Configurazione completata"](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0078.png)

1. Al termine della sincronizzazione, verificare che gli utenti creati nella directory IaaS siano sincronizzati con Azure AD.
   1. Accedere al portale di Azure.
   1. Selezionare **Azure Active Directory** > **HDIFabrikam** > **utenti**.

### <a name="create-a-user-assigned-managed-identity"></a>Creare un'identità gestita assegnata dall'utente

Creare un'identità gestita assegnata dall'utente che è possibile usare per configurare Azure AD Domain Services (Azure AD DS). Per altre informazioni, vedere [creare, elencare, eliminare o assegnare un ruolo a un'identità gestita assegnata dall'utente usando il portale di Azure](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md).

1. Accedere al portale di Azure.
1. Selezionare **Crea una risorsa** e digitare *identità gestita*. Selezionare **identità gestita assegnata dall'utente** > **Crea**.
1. Per **nome risorsa**immettere *HDIFabrikamManagedIdentity*.
1. Selezionare la propria sottoscrizione.
1. In **gruppo di risorse**selezionare **Crea nuovo** e immettere *HDIFabrikam-centralus*.
1. In **località**selezionare **Stati Uniti centrali**.
1. Selezionare **Create** (Crea).

![Creare una nuova identità gestita assegnata dall'utente](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0082.png)

### <a name="enable-azure-ad-ds"></a>Abilitare Azure AD DS

Per abilitare Azure AD DS, attenersi alla seguente procedura. Per ulteriori informazioni, vedere [Enable Azure AD DS using the portale di Azure](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started).

1. Creare una rete virtuale per ospitare Azure AD DS. Eseguire il codice PowerShell seguente.

    ```powershell
    Connect-AzAccount
    Get-AzSubscription
    Set-AzContext -Subscription 'SUBSCRIPTION_ID'
    $virtualNetwork = New-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-CentralUS' -Location 'Central US' -Name 'HDIFabrikam-AADDSVNET' -AddressPrefix 10.1.0.0/16
    $subnetConfig = Add-AzVirtualNetworkSubnetConfig -Name 'AADDS-subnet' -AddressPrefix 10.1.0.0/24 -VirtualNetwork $virtualNetwork
    $virtualNetwork | Set-AzVirtualNetwork
    ```

1. Accedere al portale di Azure.
1. Selezionare **Crea risorsa**, immettere *servizi di dominio*e selezionare **Azure ad Domain Services**.
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

1. Nella pagina **sincronizzazione** abilitare la sincronizzazione completa selezionando **tutti** > **OK**.

    ![Abilitare la sincronizzazione di Azure AD DS](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0090.png)

1. Nella pagina **Riepilogo** verificare i dettagli per Azure AD DS e selezionare **OK**.

    ![Riepilogo di "Enable Azure AD Domain Services"](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0092.png)

Dopo aver abilitato Azure AD DS, un server DNS locale viene eseguito nelle VM Azure AD.

### <a name="configure-your-azure-ad-ds-virtual-network"></a>Configurare la rete virtuale Azure AD DS

Usare la procedura seguente per configurare la rete virtuale Azure AD DS (**HDIFabrikam-AADDSVNET**) per usare i server DNS personalizzati.

1. Individuare gli indirizzi IP dei server DNS personalizzati. 
    1. Selezionare la risorsa **HDIFabrikam.com** Azure AD DS. 
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
> Per formare la richiesta di certificato SSL, è possibile usare qualsiasi applicazione o utilità che crea una richiesta di crittografia a chiave pubblica (PKCS) \#10 valida.

Verificare che il certificato sia installato nell'archivio **personale** del computer:

1. Avviare Microsoft Management Console (MMC).
1. Aggiungere lo snap-in **certificati** per la gestione dei certificati nel computer locale.
1. Espandere **certificati (computer locale)**  > **certificati** > **personali** . Un nuovo certificato deve essere presente nell'archivio **personale** . Questo certificato viene emesso al nome host completo.

    ![Verificare la creazione del certificato locale](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0102.png)

1. Nel riquadro a destra fare clic con il pulsante destro del mouse sul certificato creato. Scegliere **tutte le attività**e quindi **Esporta**.

1. Nella pagina **Esporta chiave privata** selezionare **Sì, Esporta la chiave privata**. Il computer in cui verrà importata la chiave deve avere la chiave privata per leggere i messaggi crittografati.

    ![Pagina esportazione chiave privata dell'esportazione guidata certificati](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0103.png)

1. Nella pagina **formato file di esportazione** lasciare le impostazioni predefinite e quindi fare clic su **Avanti**. 
1. Nella pagina **password** Digitare una password per la chiave privata. Per **crittografia**selezionare **TripleDES-SHA1**. Quindi selezionare **Avanti**.
1. Nella pagina **file da esportare** Digitare il percorso e il nome del file di certificato esportato, quindi fare clic su **Avanti**. Il nome del file deve avere un'estensione pfx. Questo file viene configurato nel portale di Azure per stabilire una connessione sicura.
1. Abilitare LDAPs per un dominio gestito Azure AD DS.
    1. Dal portale di Azure selezionare il dominio **HDIFabrikam.com**.
    1. In **Gestisci**selezionare **LDAP sicuro**.
    1. Nella pagina **LDAP sicuro** , in **LDAP sicuro**, selezionare **Abilita**.
    1. Individuare il file di certificato con estensione pfx esportato nel computer.
    1. Immettere la password del certificato.

    ![Abilitare LDAP sicuro](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0113.png)

1. Ora che è stato abilitato LDAPs, assicurarsi che sia raggiungibile abilitando la porta 636.
    1. Nel gruppo di risorse **HDIFabrikam-centralus** selezionare il gruppo di sicurezza di rete **AADDS-HDIFabrikam.com-NSG**.
    1. In **Impostazioni**selezionare **regole di sicurezza in ingresso** > **Aggiungi**.
    1. Nella pagina **Aggiungi regola di sicurezza in ingresso** immettere le proprietà seguenti e selezionare **Aggiungi**:

        | Proprietà | Value |
        |---|---|
        | Source (Sorgente) | Qualsiasi |
        | Intervalli di porte di origine | * |
        | Destinazione | Qualsiasi |
        | Intervallo di porte di destinazione | 636 |
        | Protocol | Qualsiasi |
        | Azione | Allow |
        | Priorità | \<numero desiderato > |
        | name | Port_LDAP_636 |

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

1. Creare una relazione peer tra la rete virtuale che ospita Azure AD DS (`HDIFabrikam-AADDSVNET`) e la rete virtuale che ospiterà il cluster HDInsight abilitato per ESP (`HDIFabrikam-HDIVNet`). Usare il codice PowerShell seguente per eseguire il peering delle due reti virtuali.

    ```powershell
    Add-AzVirtualNetworkPeering -Name 'HDIVNet-AADDSVNet' -RemoteVirtualNetworkId (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-CentralUS').Id -VirtualNetwork (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-WestUS')

    Add-AzVirtualNetworkPeering -Name 'AADDSVNet-HDIVNet' -RemoteVirtualNetworkId (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-WestUS').Id -VirtualNetwork (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-CentralUS')
    ```

1. Creare un nuovo account Azure Data Lake Storage Gen2 denominato **Hdigen2store**. Configurare l'account con l'identità gestita dall'utente **HDIFabrikamManagedIdentity**. Per altre informazioni, vedere [usare Azure Data Lake storage Gen2 con i cluster HDInsight di Azure](../hdinsight-hadoop-use-data-lake-storage-gen2.md).

1. Configurare il DNS personalizzato nella rete virtuale **HDIFabrikam-AADDSVNET** .
    1. Passare al portale di Azure > **gruppi di risorse** > **OnPremADVRG** > **HDIFabrikam-AADDSVNET** > **server DNS**.
    1. Selezionare **Custom (personalizzato** ) e immettere *10.0.0.4* e *10.0.0.5*.
    1. Selezionare **Salva**.

        ![Salvare le impostazioni DNS personalizzate per una rete virtuale](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0123.png)

1. Creare un nuovo cluster HDInsight Spark abilitato per ESP.
    1. Selezionare **personalizzata (dimensioni, impostazioni, app)** .
    2. Immettere i dettagli per le **nozioni di base** (sezione 1). Verificare che il **tipo di cluster** sia **Spark 2,3 (HDI 3,6)** . Verificare che il **gruppo di risorse** sia **HDIFabrikam-centralus**.

    1. Per **sicurezza e rete** (sezione 2), specificare i dettagli seguenti:
        * In **Enterprise Security Package**selezionare **abilitato**.
        * Selezionare **Amministrazione cluster utente** e selezionare l'account **HDIAdmin** creato come utente amministratore locale. Fare clic su **Seleziona**.
        * Selezionare il **gruppo di accesso al Cluster** > **HDIUserGroup**. Tutti gli utenti aggiunti a questo gruppo in futuro saranno in grado di accedere ai cluster HDInsight.

            ![Selezionare il gruppo di accesso al cluster HDIUserGroup](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0129.jpg)

    1. Completare gli altri passaggi della configurazione del cluster e verificare i dettagli sul **Riepilogo del cluster**. Selezionare **Create** (Crea).

1. Accedere all'interfaccia utente di Ambari per il cluster appena creato in `https://CLUSTERNAME.azurehdinsight.net`. Usare il nome utente amministratore `hdiadmin@hdifabrikam.com` e la relativa password.

    ![Finestra di accesso dell'interfaccia utente di Apache Ambari](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0135.jpg)

1. Dal dashboard del cluster selezionare **ruoli**.
1. Nella pagina **ruoli** , in **Assegna ruoli a questi**, accanto al ruolo **amministratore cluster** immettere il gruppo *hdiusergroup*. 

    ![Assegnare il ruolo di amministratore del cluster a hdiusergroup](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0137.jpg)

1. Aprire il client di Secure Shell (SSH) e accedere al cluster. Usare il **hdiuser** creato nell'istanza di Active Directory locale.

    ![Accedere al cluster usando il client SSH](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0139.jpg)

Se è possibile accedere con questo account, il cluster ESP è stato configurato correttamente per la sincronizzazione con l'istanza di Active Directory locale.

## <a name="next-steps"></a>Passaggi successivi

Leggi [un'introduzione a Apache Hadoop sicurezza con ESP](hdinsight-security-overview.md).
