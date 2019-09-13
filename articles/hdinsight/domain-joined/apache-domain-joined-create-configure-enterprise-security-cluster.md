---
title: Creare e configurare cluster di Enterprise Security Package in Azure HDInsight
description: Informazioni su come creare e configurare cluster Enterprise Security Package in Azure HDInsight
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: conceptual
ms.date: 05/09/2019
ms.openlocfilehash: 24c00d8d5db7c36746d68ad10edc4db4f76e0aac
ms.sourcegitcommit: 083aa7cc8fc958fc75365462aed542f1b5409623
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/11/2019
ms.locfileid: "70918743"
---
# <a name="create-and-configure-enterprise-security-package-clusters-in-azure-hdinsight"></a>Creare e configurare cluster di Enterprise Security Package in Azure HDInsight

Il Enterprise Security Package per Azure HDInsight consente di accedere all'autenticazione basata su Active Directory, al supporto multiutente e al controllo degli accessi in base al ruolo per i cluster Apache Hadoop in Azure. I cluster HDInsight ESP consentono alle organizzazioni, che rispettano i rigorosi criteri di sicurezza aziendali, di elaborare in modo sicuro i dati sensibili.

L'obiettivo di questa guida è configurare correttamente le risorse necessarie in modo che gli utenti locali possano accedere a un cluster HDInsight abilitato per ESP. Questo articolo illustra i passaggi necessari per creare un cluster Azure HDInsight abilitato Enterprise Security Package. I passaggi illustrano la creazione di una macchina virtuale IaaS Windows con Active Directory & DNS (Domain Name Services) abilitato. Questo server fungerà da sostituzione per l'ambiente locale **effettivo** e consentirà di procedere con i passaggi di installazione e configurazione in modo da poterli ripetere in un secondo momento nel proprio ambiente. Questa guida consente inoltre di creare un ambiente di identità ibrido con la sincronizzazione dell'hash delle password con Azure Active Directory.

Questa guida è destinata a completare l' [uso di Enterprise Security Package in HDInsight](apache-domain-joined-architecture.md)

Prima di usare questo processo nel proprio ambiente, configurare Active Directory e Domain Name Services (DNS). Inoltre, abilitare Azure Active Directory e sincronizzare gli account utente locali per Azure Active Directory.

![Diagramma dell'architettura](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0002.png)

## <a name="create-on-premises-environment"></a>Creare un ambiente locale

Panoramica In questa sezione si userà un modello di distribuzione rapida di Azure per creare nuove macchine virtuali, configurare Domain Name Services (DNS) e una nuova foresta AD.

1. Passare a [creare una macchina virtuale di Azure con una nuova foresta di Active Directory](https://azure.microsoft.com/resources/templates/active-directory-new-domain/)per visualizzare il modello di distribuzione rapida.

1. Fare clic su **Distribuisci in Azure**.
1. Accedere alla sottoscrizione di Azure.
1. Nella schermata **creare una macchina virtuale di Azure con una nuova foresta di Active Directory** completare i passaggi seguenti:
    1. Selezionare la sottoscrizione in cui si desidera distribuire le risorse dall'elenco a discesa **sottoscrizione** .
    1. Selezionare **Crea nuovo** accanto a **gruppo di risorse** e immettere il nome **OnPremADVRG**
    1. Immettere i dettagli seguenti per il resto dei campi del modello:

        * **Località**: Stati Uniti centrali
        * **Nome utente amministratore**: HDIFabrikamAdmin
        * **Password amministratore**: < password >
        * **Dominio**: HDIFabrikam.com
        * **Prefisso DNS**: hdifabrikam

        ![Modello creare una macchina virtuale di Azure e una foresta Active Directory](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-azure-vm-ad-forest.png)

    1. Fare clic su **Acquista**
    1. Monitorare la distribuzione e attenderne il completamento.
    1. Verificare che le risorse vengano create nel gruppo `OnPremADVRG`di risorse corretto.

## <a name="configure-users-and-groups-for-cluster-access"></a>Configurare utenti e gruppi per l'accesso al cluster

Panoramica In questa sezione si creeranno gli utenti che avranno accesso al cluster HDInsight entro la fine di questa guida.

1. Connettersi al controller di dominio utilizzando Desktop remoto.
    1. Se è stato usato il modello indicato all'inizio, il controller di dominio è una macchina virtuale denominata adVM `OnPremADVRG` nel gruppo di risorse.
    1. Passare alla portale di Azure > **gruppi** > di risorse**OnPremADVRG** > **adVM** > **Connect**.
    1. Fare clic sulla scheda **RDP** e quindi su **Scarica file RDP**.
    1. Salvare il file nel computer e aprirlo.
    1. Quando vengono richieste le credenziali, `HDIFabrikam\HDIFabrikamAdmin` usare come nome utente e quindi immettere la password scelta per l'account amministratore.

1. Una volta aperta la sessione di Desktop remoto nella macchina virtuale del controller di dominio, avviare **Active Directory utenti e computer** dal dashboard di **Server Manager** . Fare clic su **strumenti** in alto a destra e quindi **Active Directory utenti e computer** nell'elenco a discesa.

    ![Gestione Server Manager aperta Active Directory](./media/apache-domain-joined-create-configure-enterprise-security-cluster/server-manager-active-directory-screen.png)

1. Creare due nuovi utenti, **HDIAdmin**, **HDIUser**. Questi due utenti verranno usati per accedere ai cluster HDInsight.

    1. Nella schermata **Active Directory utenti e computer** fare clic su **azione** > **nuovo** > **utente**.

        ![Crea nuovo utente Active Directory](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-active-directory-user.png)

    1. Nella schermata **nuovo oggetto-utente** immettere `HDIUser` come **nome di accesso utente** e fare clic su **Avanti**.

        ![Crea primo utente amministratore](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0020.png)

    1. Nella finestra popup visualizzata immettere la password desiderata per il nuovo account. Selezionare la casella che indica che la **password non scade mai**. HDIClick **OK**.
    1. Fare clic su **fine** per creare il nuovo account.
    1. Creare un altro `HDIAdmin`utente.

        ![Crea secondo utente amministratore](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0024.png)

1. Nella schermata **Active Directory utenti e computer** fare clic su **azione** > **nuovo** > **gruppo**. Crea `HDIUserGroup` come nuovo gruppo.

    ![Crea nuovo gruppo di Active Directory](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-active-directory-group.png)

    ![Crea nuovo group2](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0028.png)

1. Aggiungere il **HDIUser** creato nel passaggio precedente a **HDIUserGroup** come membro.

    1. Fare clic con il pulsante destro del mouse su **HDIUserGroup** e scegliere **Proprietà**.
    1. Passare alla scheda **membri** e fare clic su **Aggiungi**.
    1. Immettere `HDIUser` nella casella **immettere i nomi degli oggetti da selezionare** e fare clic su **OK**.
    1. Ripetere i passaggi precedenti per l'altro account`HDIAdmin`

        ![Aggiungi membri al gruppo](./media/apache-domain-joined-create-configure-enterprise-security-cluster/active-directory-add-users-to-group.png)

A questo punto è stato creato l'ambiente di Active Directory, insieme a due utenti e a un gruppo di utenti per accedere al cluster HDInsight.

Questi utenti verranno sincronizzati con Azure AD.

### <a name="create-a-new-azure-active-directory"></a>Crea una nuova Azure Active Directory

1. Accedere al portale di Azure.
1. Fare clic su **Crea una risorsa** e digitare la **directory**. Selezionare **Azure Active Directory** > **Crea**.
1. Immettere **HDIFabrikam** in **nome organizzazione**.
1. Immettere **HDIFabrikamoutlook** in **nome di dominio iniziale**.
1. Fare clic su **Create**(Crea).
1. A sinistra nella portale di Azure fare clic su **Azure Active Directory**.
1. Se necessario, fare clic su Cambia **directory** per passare alla nuova directory creata **HDIFabrikamoutlook**.
1. In **Gestisci** fare clic su **nomi** > di dominio personalizzati**Aggiungi dominio personalizzato**.
1. Immettere **HDIFabrikam.com** in **nome dominio personalizzato** e fare clic su **Aggiungi dominio**.

![creare una nuova Azure Active Directory](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-new-directory.png)

![creazione di un nuovo dominio personalizzato](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-custom-domain.png)

## <a name="configure-your-azure-ad-tenant"></a>Configurare il tenant di Azure AD

Panoramica A questo punto si configurerà il tenant di Azure AD in modo che sia possibile sincronizzare gli utenti e i gruppi da AD locale al cloud.

1. Creare un amministratore tenant di AD.
    1. Accedere al portale di Azure e selezionare il tenant di Azure AD **HDIFabrikam**
    1. Selezionare **utenti** in **Gestisci** e quindi **nuovo utente**.
    1. Immettere i dettagli seguenti per il nuovo utente:

        * Nome: fabrikamazureadmin
        * Nome utente:fabrikamazureadmin@hdifabrikam.com
        * Password: password sicura di propria scelta

    1. Fare clic sulla sezione **gruppi** , cercare **amministratori di AAD DC**e fare clic su **Seleziona**.

        ![Gruppi](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0038.png)

    1. Fare clic sulla sezione **ruolo della directory** e selezionare **amministratore globale** sul lato destro. Fare clic su **OK**.

        ![Ruolo directory](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0040.png)

    1. Immettere una password per l'utente. Fare clic su **Create**(Crea).

1. Se si desidera modificare la password per l'utente <fabrikamazureadmin@hdifabrikam.com>appena creato. Accedere al portale di Azure usando l'identità e quindi verrà richiesto di modificare la password.

## <a name="sync-on-premises-users-to-azure-ad"></a>Sincronizzare gli utenti locali con Azure AD

### <a name="download-and-install-microsoft-azure-active-directory-connect"></a>Scaricare e installare Microsoft Azure Active Directory Connect

1. [Scaricare Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594).

1. Installare Microsoft Azure Active Directory connettersi al controller di dominio.
    1. Aprire il file eseguibile scaricato nel passaggio precedente e accettare le condizioni di licenza. Scegliere **Continua**.

        ![Azure AD Connect](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0052.png)

    1. Fare clic su **Usa impostazioni rapide** e completare l'installazione.

        ![Usa impostazioni rapide](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0054.png)

### <a name="configure-sync-with-on-premises-domain-controller"></a>Configurare la sincronizzazione con il controller di dominio locale

1. Nella schermata **Connetti a Azure ad** immettere il nome utente e la password dell'amministratore globale per Azure ad. Fare clic su **Avanti**. Si tratta del nome `fabrikamazureadmin@hdifabrikam.com` utente creato durante la configurazione del tenant di ad.
    ![Connessione ad Azure AD](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0058.png)
1. Nella schermata **Connetti a Active Directory Domain Services** immettere il nome utente e la password per un account amministratore dell'organizzazione. Fare clic su **Avanti**. Si tratta del nome `HDIFabrikam\HDIFabrikamAdmin` utente e della password corrispondente creati in precedenza.

   ![Connetti a Active Directory Domain Services](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0060.png)
1. Nella pagina **configurazione dell'accesso Azure ad** fare clic su **Avanti**.
    ![Configurazione dell'accesso Azure AD](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0062.png)
1. Nella schermata pronto per la configurazione fare clic su **Installa**.
    ![Installa](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0064.png)
1. Quando viene visualizzata la schermata **Configurazione completata** , fare clic su **Esci**.
    ![Configurazione completata](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0078.png)

1. Al termine della sincronizzazione, verificare se gli utenti creati in IAAS Active Directory vengono sincronizzati con Azure Active Directory.
    1. Accedere al portale di Azure.
    1. Selezionare **Azure Active Directory** > **utenti** **HDIFabrikam** > .

### <a name="create-an-user-assigned-managed-identity"></a>Creare un'identità gestita assegnata dall'utente

Creare un'identità gestita assegnata dall'utente che verrà usata per configurare Azure Active Directory Domain Services (Azure AD-DS). Per altre informazioni sulla creazione di un'identità gestita assegnata dall'utente, vedere [creare, elencare, eliminare o assegnare un ruolo a un'identità gestita assegnata dall'utente usando il portale di Azure](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md).

1. Accedere al portale di Azure.
1. Fare clic su **Crea una risorsa** e digitare **identità gestita**. Selezionare > **creazione** **identità gestita assegnata dall'utente**.
1. Immettere **HDIFabrikamManagedIdentity** come **nome della risorsa**.
1. Selezionare la propria sottoscrizione.
1. In **gruppo di risorse** fare clic su **Crea nuovo** e immettere **HDIFabrikam-centralus**.
1. Selezionare **Stati Uniti centrali** in **località**.
1. Fare clic su **Create**(Crea).

![creare una nuova identità gestita assegnata dall'utente](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0082.png)

### <a name="enable-azure-active-directory-domain-services"></a>Abilitare Azure Active Directory Domain Services

Per altre informazioni vedere [Abilitare Azure Active Directory Domain Services tramite il portale di Azure](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started).

1. Creare la rete virtuale per ospitare Azure AD-DS. Eseguire il codice PowerShell seguente.

    ```powershell
    Connect-AzAccount
    Get-AzSubscription
    Set-AzContext -Subscription 'SUBSCRIPTION_ID'
    $virtualNetwork = New-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-CentralUS' -Location 'Central US' -Name 'HDIFabrikam-AADDSVNET' -AddressPrefix 10.1.0.0/16
    $subnetConfig = Add-AzVirtualNetworkSubnetConfig -Name 'AADDS-subnet' -AddressPrefix 10.1.0.0/24 -VirtualNetwork $virtualNetwork
    $virtualNetwork | Set-AzVirtualNetwork
    ```

1. Accedere al portale di Azure.
1. Fare clic su **Crea risorsa**, immettere **servizi di dominio** e selezionare **Azure ad Domain Services**.
1. Nella schermata **nozioni di base** completare i passaggi seguenti:
    1. In **nome directory** selezionare il Azure Active Directory creato per questo articolo, **HDIFabrikam**.
    1. Immettere un **nome di dominio DNS** di **HDIFabrikam.com**.
    1. Selezionare la propria sottoscrizione.
    1. Specificare il gruppo di risorse **HDIFabrikam-centralus** e la **posizione** degli **Stati Uniti centrali**.

        ![Dettagli di base Azure AD-DS](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0084.png)

1. Nella schermata **rete** completare selezionare la rete (**HDIFabrikam-VNET**) e la subnet (**AADDS-subnet**) creata con lo script di PowerShell precedente. In alternativa, è possibile usare l'opzione **Crea nuovo** per creare una rete virtuale.

    ![Selezionare la rete](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0086.png)

1. Nella schermata **gruppo amministratore** dovrebbe essere visualizzata una notifica che segnala che è già stato creato un gruppo denominato **amministratori di AAD DC** per amministrare questo gruppo. Facoltativamente, è possibile modificare l'appartenenza al gruppo, ma non è necessario per i passaggi di questo articolo. Fare clic su **OK**.

    ![Visualizza gruppo amministratori](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0088.png)

1. Nella schermata **sincronizzazione** abilitare completa sincronizzazione selezionando **tutto** e quindi fare clic su **OK**.

    ![Abilita sincronizzazione](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0090.png)

1. Nella schermata **Riepilogo** verificare i dettagli per Azure AD-DS e fare clic su **OK**.

    ![Verifica dettagli](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0092.png)

1. Dopo aver abilitato Azure Active Directory Domain Services, un server DNS (Domain Name Service) locale viene eseguito nelle macchine virtuali (VM) AD.

### <a name="configure-your-azure-ad-ds-virtual-network"></a>Configurare la rete virtuale Azure AD-DS

I passaggi descritti in questa sezione consentiranno di configurare la rete virtuale Azure AD DS (**HDIFabrikam-AADDSVNET**) per l'uso dei server DNS personalizzati.

1. Individuare gli indirizzi IP dei server DNS personalizzati. Fare clic sulla **risorsa HDIFabrikam.com** ad DS, fare clic su **Proprietà** in **Gestisci**   ed esaminare gli indirizzi IP elencati in **indirizzo IP nella rete virtuale**.

    ![Individuare indirizzi IP DNS personalizzati per Azure AD-DS](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0096.png)

1. Configurare **HDIFabrikam-AADDSVNET** in indirizzi IP `10.0.0.4` personalizzati `10.0.0.5`e.

    1. Selezionare **Server** DNS nella categoria **Impostazioni** . Fare quindi clic sul pulsante di opzione accanto a **personalizzata**, immettere il primo indirizzo IP (10.0.0.4) nella casella di testo seguente e fare clic su **Salva**.
    1. Aggiungere altri indirizzi IP (10.0.0.5) usando la stessa procedura.

1. In questo scenario Azure AD-DS è stato configurato per usare gli indirizzi IP 10.0.0.4 e 10.0.0.5, impostando lo stesso indirizzo IP in AADDS VNet come illustrato nell'immagine seguente.

    ![Visualizzare i server DNS personalizzati](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0098.png)

## <a name="securing-ldap-traffic"></a>Sicurezza del traffico LDAP

Lightweight Directory Access Protocol (LDAP) viene usato per leggere e scrivere in Active Directory. È possibile rendere il traffico LDAP riservato e sicuro usando la tecnologia Secure Sockets Layer (SSL)/Transport Layer Security (TLS). È possibile abilitare LDAP su SSL (LDAPs) installando un certificato correttamente formattato.

Per altre informazioni su LDAP sicuro, vedere [configurare LDAP sicuro (LDAPS) per un Azure ad Domain Services dominio gestito](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-admin-guide-configure-secure-ldap).

In questa sezione si crea un certificato autofirmato, si Scarica il certificato e si configura LDAP sicuro (LDAPs) per il dominio gestito **hdifabrikam** Azure AD-DS.

Lo script seguente crea un certificato per hdifabrikam. Il certificato viene salvato nel percorso "LocalMachine".

> [!Note] 
> Per formare la richiesta di certificato SSL, è \#possibile usare qualsiasi utilità o applicazione che crea una richiesta PKCS 10 valida.

```powershell
$lifetime = Get-Date
New-SelfSignedCertificate -Subject hdifabrikam.com `
-NotAfter $lifetime.AddDays(365) -KeyUsage DigitalSignature, KeyEncipherment `
-Type SSLServerAuthentication -DnsName *.hdifabrikam.com, hdifabrikam.com
```

Verificare che il certificato sia installato nell'archivio personale\'del computer. Completare questi passaggi:

1. Avviare Microsoft Management Console (MMC).
1. Aggiungere lo snap-in certificati per la gestione dei certificati nel computer locale.
1. Espandere **certificati (computer locale)** , espandere **personale**, quindi **certificati**. Un nuovo certificato deve essere presente nell'archivio personale. Questo certificato viene emesso al nome host completo.

    ![verificare la creazione del certificato](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0102.png)

1. Nel riquadro destro fare clic con il pulsante destro del mouse sul certificato creato nel passaggio precedente, scegliere **tutte le attività**, quindi fare clic su **Esporta**.

1. Nella pagina **esportazione della chiave** privata, fare clic su **Sì, Esporta la chiave privata**. La chiave privata è necessaria per la lettura dei messaggi crittografati dal computer in cui viene importata la chiave.

    ![Esporta chiave privata](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0103.png)

1. Nella pagina **formato** file di esportazione lasciare le impostazioni predefinite e quindi fare clic su **Avanti**. 
1. Nella pagina **password** digitare una password per la chiave privata, selezionare **TripleDES-SHA1** per la **crittografia** e fare clic su **Avanti**.
1. Nella pagina **file da esportare** digitare il percorso e il nome del file di certificato esportato, quindi fare clic su **Avanti**.
1. Il nome file deve essere estensione pfx, questo file è configurato in portale di Azure per stabilire una connessione protetta.
1. Abilitare LDAP sicuro (LDAPs) per un dominio gestito Azure AD Domain Services.
    1. Selezionare il dominio **HDIFabrikam.com** dalla portale di Azure.
    1. Fare clic su **LDAP sicuro** in **Gestisci**.
    1. Nella schermata **LDAP sicuro** fare clic su **Abilita** in **LDAP sicuro**.
    1. Individuare il file di certificato con estensione pfx esportato nel computer.
    1. Immettere la password del certificato.

    ![Abilita LDAP sicuro](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0113.png)

1. Ora che è stato abilitato LDAP sicuro, assicurarsi che sia raggiungibile abilitando la porta 636.
    1. Fare clic sul gruppo di sicurezza di rete **AADDS-HDIFabrikam.com-NSG** nel gruppo di risorse **HDIFabrikam-centralus** .
    1. In **Impostazioni** fare clic su **regole** > di sicurezza in ingresso**Aggiungi**.
    1. Nella schermata **Aggiungi regola di sicurezza in ingresso** immettere le proprietà seguenti e fare clic su **Aggiungi**:

        | Proprietà | Value |
        |---|---|
        | Source | Any |
        | Source port ranges | * |
        | Destination | Any |
        | Destination port range | 636 |
        | Protocol | Any |
        | Action | Allow |
        | Priority | \<Numero desiderato\> |
        | Name | Port_LDAP_636 |

    ![Regola di sicurezza in ingresso](./media/apache-domain-joined-create-configure-enterprise-security-cluster/add-inbound-security-rule.png)

1. `HDIFabrikamManagedIdentity`è l'identità gestita assegnata dall'utente, il ruolo Collaboratore servizi di dominio HDInsight è abilitato per l'identità gestita che consente a questa identità di leggere, creare, modificare ed eliminare le operazioni di servizi di dominio.

    ![Crea identità gestita assegnata dall'utente](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0117.png)

## <a name="creating-enterprise-security-package-enabled-hdinsight-cluster"></a>Creazione di Enterprise Security Package cluster HDInsight abilitato

Per questo passaggio sono necessari i prerequisiti seguenti:

1. Creare un nuovo gruppo `HDIFabrikam-WestUS` di risorse nella località. `West US`
1. Creare una rete virtuale che ospiterà il cluster HDInsight abilitato per ESP.

    ```powershell
    $virtualNetwork = New-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-WestUS' -Location 'West US' -Name 'HDIFabrikam-HDIVNet' -AddressPrefix 10.1.0.0/16
    $subnetConfig = Add-AzVirtualNetworkSubnetConfig -Name 'SparkSubnet' -AddressPrefix 10.1.0.0/24 -VirtualNetwork $virtualNetwork
    $virtualNetwork | Set-AzVirtualNetwork
    ```

1. Creare una relazione peer tra la rete virtuale che ospita AADDS (`HDIFabrikam-AADDSVNET`) e la rete virtuale in cui verrà ospitato il cluster HDInsight abilitato per ESP (`HDIFabrikam-HDIVNet`). Usare il codice PowerShell seguente per eseguire il peering di queste due reti virtuali.

    ```powershell
    Add-AzVirtualNetworkPeering -Name 'HDIVNet-AADDSVNet' -RemoteVirtualNetworkId (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-CentralUS').Id -VirtualNetwork (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-WestUS')

    Add-AzVirtualNetworkPeering -Name 'AADDSVNet-HDIVNet' -RemoteVirtualNetworkId (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-WestUS').Id -VirtualNetwork (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-CentralUS')
    ```

1. Creare un nuovo account Azure Data Lake Storage Gen2, **Hdigen2store**, configurato con l'identità gestita dall'utente **HDIFabrikamManagedIdentity**. Per altre informazioni sulla creazione di account Data Lake Storage Gen2 abilitati con le identità gestite dall'utente, vedere [usare Azure Data Lake storage Gen2 con i cluster HDInsight di Azure](../hdinsight-hadoop-use-data-lake-storage-gen2.md).

1. Configurare un DNS personalizzato nella rete virtuale **HDIFabrikam-AADDSVNET** .
    1. Passare alla portale di Azure > **gruppi** > di risorse**OnPremADVRG** > **HDIFabrikam-AADDSVNET** > **DNS Servers**.
    1. Selezionare **personalizzato** e immettere `10.0.0.4` e `10.0.0.5`.
    1. Fare clic su **Save**.

        ![Salvare le impostazioni DNS personalizzate](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0123.png)

1. Creare un nuovo cluster HDInsight Spark abilitato per ESP.
    1. Fare clic su **personalizzato (dimensioni, impostazioni, app)** .
    2. Immettere i dettagli desiderati per le **nozioni di base**della sezione 1. Verificare che il **tipo di cluster** sia **Spark 2,3 (HDI 3,6)** e che il **gruppo di risorse** sia **HDIFabrikam-centralus**

    1. Nella sezione 2 **sicurezza e rete**eseguire le operazioni seguenti:
        1. Fare clic su **abilitato** in **Enterprise Security Package**.
        1. Fare clic su **utente amministratore cluster** e selezionare l'account **HDIAdmin** creato in precedenza come utente amministratore locale. Fare clic su **Seleziona**.

        1. Fare clic su **gruppo di accesso cluster** e quindi selezionare **HDIUserGroup**. Tutti gli utenti aggiunti a questo gruppo in futuro saranno in grado di accedere ai cluster HDInsight.

            ![Selezionare il gruppo di accesso al cluster](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0129.jpg)

    1. Completare gli altri passaggi della configurazione del cluster e verificare i dettagli sul **Riepilogo del cluster**. Fare clic su **Create**(Crea).

1. Accedere all'interfaccia utente di Ambari per il cluster appena creato in `https://CLUSTERNAME.azurehdinsight.net` usando il nome `hdiadmin@hdifabrikam.com` utente e la password dell'amministratore.

    ![accedere a Ambari](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0135.jpg)

1. Fare clic su **ruoli** nel dashboard del cluster.
1. Nella pagina **ruoli** immettere il gruppo **hdiusergroup** per assegnarlo al ruolo di **amministratore del cluster** in assegnare i **ruoli a questi**.

    ![Assegnare il ruolo di amministratore del cluster a hdiusergroup](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0137.jpg)

1. Aprire il client SSH e accedere al cluster usando il **hdiuser** creato in precedenza nella Active Directory locale.

    ![Accedere al cluster con SSH](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0139.jpg)

Se è possibile accedere con questo account, il cluster ESP è stato configurato correttamente per la sincronizzazione con Active Directory locale.

## <a name="next-steps"></a>Passaggi successivi

* [Introduzione alla sicurezza Apache Hadoop con Enterprise Security Package](hdinsight-security-overview.md)
