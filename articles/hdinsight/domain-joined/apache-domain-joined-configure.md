---
title: Configurare cluster HDInsight aggiunti al dominio - Azure | Microsoft Docs
description: Maggiori informazioni su come configurare i cluster HDInsight aggiunti al dominio
services: hdinsight
documentationcenter: 
author: saurinsh
manager: jhubbard
editor: cgronlun
tags: 
ms.assetid: 0cbb49cc-0de1-4a1a-b658-99897caf827c
ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/02/2016
ms.author: saurinsh
ms.openlocfilehash: 2c844ce8aec04c74a9c2dbecdd1b3effb286df97
ms.sourcegitcommit: 6a22af82b88674cd029387f6cedf0fb9f8830afd
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/11/2017
---
# <a name="configure-domain-joined-hdinsight-clusters-preview"></a>Configurare i cluster HDInsight aggiunti al dominio (anteprima)

In questo articolo viene descritto come configurare un cluster Azure HDInsight con Azure Active Directory (Azure AD) e [Apache Ranger](http://hortonworks.com/apache/ranger/) per sfruttare criteri avanzati di autenticazione e controllo degli accessi in base al ruolo.  HDInsight aggiunto al dominio può essere configurato solo nei cluster basati su Linux. Per altre informazioni, consultare [Introduce Domain-joined HDInsight clusters](apache-domain-joined-introduction.md) (Introduzione ai cluster HDInsight aggiunti al dominio).

> [!IMPORTANT]
> Oozie non è abilitato su HDInsight aggiunto al dominio.

Questo articolo è la prima esercitazione di una serie:

* Creare un cluster HDInsight connesso ad Azure AD (tramite la funzionalità Azure Directory Domain Services) con Apache Ranger abilitato.
* Creare e applicare criteri Hive tramite Apache Ranger e consentire agli utenti (ad esempio, i ricercatori) di connettersi a Hive usando strumenti basati su ODBC, come Excel, Tableau e così via. Microsoft sta lavorando per aggiungere presto altri carichi di lavoro, ad esempio HBase e Storm, a HDInsight aggiunto al dominio.

I nomi dei servizi di Azure devono essere globalmente univoci. In questa esercitazione vengono usati i nomi seguenti. Contoso è un nome fittizio. Sostituire *contoso* con un nome diverso quando si svolge l'esercitazione. 

**Nomi:**

| Proprietà | Valore |
| --- | --- |
| Directory di Azure AD |contosoaaddirectory |
| Nome di dominio di Azure AD |contoso (contoso.onmicrosoft.com) |
| Rete virtuale di HDInsight |contosohdivnet |
| Gruppo di risorse della rete virtuale di HDInsight |contosohdirg |
| Cluster HDInsight |contosohdicluster |

In questa esercitazione sono illustrati i passaggi per configurare un cluster HDInsight aggiunto al dominio. In ogni sezione sono riportati i link ad altri articoli con maggiori informazioni.

## <a name="prerequisite"></a>Prerequisiti:
* Acquisire familiarità con [Azure AD Domain Services](https://azure.microsoft.com/services/active-directory-ds/) e con la struttura dei [prezzi](https://azure.microsoft.com/pricing/details/active-directory-ds/).
* Assicurarsi che la sottoscrizione sia consentita per questa anteprima pubblica. Per farlo, è possibile inviare un messaggio di posta elettronica a hdipreview@microsoft.com con l'ID sottoscrizione.
* Un certificato SSL firmato da un'autorità di firma o un certificato autofirmato per il dominio. Il certificato è richiesto per la configurazione dell'accesso LDAP sicuro.

## <a name="procedures"></a>Procedures
1. Creare una rete virtuale di HDInsight in modalità Azure Resource Manager.
2. Creare e configurare Azure AD e Azure AD DS.
3. Creare un cluster HDInsight

> [!NOTE]
> In questa esercitazione si presuppone che non sia disponibile un account Azure. Se si dispone di un account, è possibile ignorare questo passaggio.
> 
> 

## <a name="create-a-resource-manager-vnet-for-hdinsight-cluster"></a>Creare una rete virtuale di Resource Manager per un cluster HDInsight
In questa sezione si creerà una rete virtuale di Azure Resource Manager che verrà usata per il cluster HDInsight. Per altre informazioni sulla creazione di una rete virtuale di Azure usando altri metodi, vedere [Creare una rete virtuale](../../virtual-network/virtual-networks-create-vnet-arm-pportal.md)

Dopo aver creato la rete virtuale, si configurerà Active Directory Domain Services per l'uso di questa rete virtuale.

**Per creare una rete virtuale di Resource Manager**

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Fare clic su **Nuovo**, quindi su **Rete** e infine su **Rete virtuale**. 
3. In **Selezionare un modello di distribuzione** scegliere **Resource Manager** e quindi fare clic su **Crea**.
4. Digitare o selezionare i valori seguenti:
   
   * **Nome**: contosohdivnet
   * **Spazio indirizzi**: 10.0.0.0/16.
   * **Nome subnet**: Subnet1
   * **Subnet address range**: 10.0.0.0/24
   * **Sottoscrizione**: (selezionare una sottoscrizione di Azure.)
   * **Gruppo di risorse**: contosohdirg
   * **Percorso**: selezionare lo stesso percorso delle macchine virtuali di Azure Active Directory. Ad esempio, contosoaadvnet.
5. Fare clic su **Crea**.

**Per configurare DNS per la rete virtuale di Resource Manager**

1. Nel [portale di Azure](https://portal.azure.com) fare clic su **More services** >  (Altri servizi) **Reti virtuali**. Assicurarsi di non fare clic su **Reti virtuali (classico)**.
2. Fare clic su **contosohdivnet**.
3. Fare clic su **Server DNS** nel lato sinistro del nuovo pannello.
4. Fare clic su **Personalizzato** e immettere i valori seguenti:
   
   * 10.0.0.4
   * 10.0.0.5     
     
5. Fare clic su **Salva**.

## <a name="create-and-configure-azure-ad-ds-for-your-azure-ad"></a>Creare e configurare Azure AD DS per Azure AD
In questa sezione si svolgeranno le seguenti operazioni:

1. Creare Azure AD.
2. Creare utenti di Azure AD. Questi utenti sono gli utenti del dominio. Il primo utente viene usato per configurare il cluster HDInsight con Azure AD.  Gli altri due utenti sono facoltativi per questa esercitazione. Verranno usati in [Configure Hive policies for Domain-joined HDInsight clusters](apache-domain-joined-run-hive.md) (Configurare criteri Hive per cluster HDInsight aggiunti al dominio) quando si creano criteri Apache Ranger.
3. Creare il gruppo di amministratori di AAD DC e aggiungere l'utente di Azure AD al gruppo. Questo utente viene usato per creare l'unità organizzativa.
4. Abilitare Azure AD Domain Services (Azure AD DS) per Azure AD.
5. Configurare LDAPS per Azure AD. Lightweight Directory Access Protocol (LDAP) viene usato per leggere e scrivere in Azure AD.

Se si preferisce usare un Azure AD esistente, è possibile ignorare i passaggi 1 e 2.

**Per creare Azure AD**

1. Nel [portale di Azure classico](https://manage.windowsazure.com) fare clic su **Nuovo** > **Servizi app** > **Active Directory** > **Directory** > **Creazione personalizzata**. 
2. Immettere o selezionare i valori seguenti:
   
   * **Nome**: contosoaaddirectory
   * **Nome di dominio**: contoso.  Il nome deve essere univoco a livello globale.
   * **Paese o area geografica**: selezionare il paese o l'area.
3. Fare clic su **Complete**.

**Creare un utente di Azure AD**

1. Nel [portale di Azure](https://portal.azure.com) fare clic su **Azure Active Directory** > **contosoaaddirectory** > **Utenti e gruppi**. 
2. Fare clic su **Tutti gli utenti** nel menu.
3. Fare clic su **Nuovo utente**.
4. Immettere un valore nei campi **Nome** e **Nome utente** e quindi fare clic su **Avanti**. 
5. Configurare il profilo utente. In **Ruolo** selezionare **Amministratore globale**, quindi **Avanti**.  Il ruolo di amministratore globale serve per creare unità organizzative.
6. Creare una copia della password temporanea.
7. Fare clic su **Crea**. Più avanti in questa esercitazione si userà questo utente amministratore globale per creare il cluster HDInsight.

Seguire la stessa procedura per creare altri due utenti con **ruolo utente** hiveuser1 e hiveuser2. Questi utenti verranno usati in [Configure Hive policies for Domain-joined HDInsight clusters](apache-domain-joined-run-hive.md) (Configurare criteri Hive per cluster HDInsight aggiunti al dominio).

**Per creare il gruppo di amministratori di AAD DC e aggiungere un utente di Azure AD**

1. Nel [portale di Azure](https://portal.azure.com) fare clic su **Azure Active Directory** > **contosoaaddirectory** > **Utenti e gruppi**. 
2. Fare clic su **Tutti i gruppi** nel menu in alto.
3. Fare clic su **Nuovo gruppo**.
4. Immettere o selezionare i valori seguenti:
   
   * **Nome**: AAD DC Administrators.  Non modificare il nome del gruppo.
   * **Tipo di appartenenza**: assegnato.
5. Fare clic su **Seleziona**.
6. Fare clic su **Membri**.
7. Selezionare il primo utente creato nel passaggio precedente e fare clic su **Seleziona**.
8. Ripetere gli stessi passaggi per creare un altro gruppo di nome **HiveUsers** e aggiungere i due utenti di Hive al gruppo.

Per altre informazioni vedere [Servizi di dominio Azure AD (anteprima) - Creare il gruppo "AAD DC Administrators"](../../active-directory-domain-services/active-directory-ds-getting-started.md).

**Per abilitare Azure AD DS per Azure AD**

1. Nel [portale di Azure](https://portal.azure.com) fare clic su **Crea una risorsa** > **Sicurezza e identità** > **Azure AD Domain Services** > **Aggiungi**. 
2. Immettere o selezionare i valori seguenti:
   * **Nome directory**: contosoaaddirectory
   * **Nome di dominio DNS**: mostra il nome DNS predefinito della directory di Azure. Ad esempio, contoso.onmicrosoft.com.
   * **Località**: selezionare un'area.
   * **Rete**: selezionare la rete virtuale e la subnet create in precedenza. Ad esempio, **contosohdivnet**.
3. Fare clic su **OK** nella pagina di riepilogo. Sotto le notifiche verrà visualizzato il messaggio **Distribuzione in corso**.
4. Attendere che non sia più visualizzato **Distribuzione in corso** e che compaiano dati nel campo **Indirizzo IP**. Verranno inseriti due indirizzi IP. Si tratta degli indirizzi IP dei controller di dominio di cui Domain Services ha effettuato il provisioning. Ogni indirizzo IP sarà visibile dopo che sarà stato effettuato il provisioning del controller di dominio corrispondente e quando questo sarà pronto. Annotare i due indirizzi IP. saranno necessarie più avanti.

Per altre informazioni vedere [Abilitare Azure Active Directory Domain Services tramite il portale di Azure](../../active-directory-domain-services/active-directory-ds-getting-started.md).

**Per sincronizzare la password**

Se si usa il proprio dominio, è necessario sincronizzare la password. Vedere [Abilitare la sincronizzazione password in Azure AD Domain Services per una directory di Azure AD solo cloud](../../active-directory-domain-services/active-directory-ds-getting-started-password-sync.md).

**Per configurare LDAPS per Azure AD**

1. Ottenere un certificato SSL firmato da un'autorità di firma per il dominio.
2. Nel [portale di Azure](https://portal.azure.com) fare clic su **Azure AD Domain Services** > **contoso.onmicrosoft.com**. 
3. Abilitare **LDAP sicuro**.
6. Seguire le istruzioni per specificare il file del certificato e la password.  
7. Attendere che compaiano dati nel campo **Certificato LDAP sicuro**. Questa operazione può richiedere 10 minuti o più.

> [!NOTE]
> Se sono in corso alcune attività in background in Azure AD DS, potrebbe essere visualizzato un errore durante il caricamento del certificato <i>There is an operation being performed for this tenant. Please try again later</i> (È in corso un'operazione per questo tenant. Riprovare più tardi).  Nel caso in cui si verificasse questo errore, riprovare più tardi. Il provisioning del secondo indirizzo IP del controller di dominio potrebbe richiedere fino a 3 ore.
> 
> 

Per altre informazioni, vedere [Configurare l'accesso LDAP sicuro (LDAPS) per un dominio gestito di Azure AD Domain Services](../../active-directory-domain-services/active-directory-ds-admin-guide-configure-secure-ldap.md).

## <a name="create-hdinsight-cluster"></a>Creare un cluster HDInsight
In questa sezione viene creato un cluster Hadoop basato su Linux in HDInsight usando il portale di Azure o il [modello di Azure Resource Manager](../../azure-resource-manager/resource-group-template-deploy.md). Per altri metodi di creazione di cluster e per informazioni sulle impostazioni, vedere l'articolo sulla [creazione di cluster HDInsight](../hdinsight-hadoop-provision-linux-clusters.md). Per altre informazioni sull'uso di un modello di Resource Manager per creare cluster Hadoop in HDInsight, vedere [Creare cluster Hadoop in HDInsight tramite modelli di Resource Manager](../hdinsight-hadoop-create-windows-clusters-arm-templates.md)

**Per creare un cluster HDInsight aggiunto al dominio usando il portale di Azure**

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Fare clic su **Nuovo**, **Intelligence e analisi**, quindi su **HDInsight**.
3. Nel pannello **Nuovo cluster HDInsight** immettere o selezionare i valori seguenti:
   
   * **Nome cluster**: immettere un nuovo nome per il cluster HDInsight aggiunto al dominio.
   * **Sottoscrizione**: selezionare la sottoscrizione di Azure usata per creare il cluster.
   * **Configurazione cluster**:
     
     * **Tipo di cluster**: Hadoop. HDInsight aggiunto al dominio è attualmente supportato solo nei cluster Hadoop, Spark e Interactive Query.
     * **Sistema operativo**: Linux.  HDInsight aggiunto al dominio è supportato solo nei cluster HDInsight basati su Linux.
     * **Versione**: HDI 3.6. HDInsight aggiunto al dominio è supportato solo nei cluster HDInsight versione 3.6.
     * **Tipo di cluster**: PREMIUM
       
       Fare clic su **Seleziona** per salvare le modifiche.
   * **Credenziali**: configurare le credenziali sia per l'utente del cluster che per l'utente SSH.
   * **Origine dati**: creare un nuovo account di archiviazione oppure usarne uno esistente come account di archiviazione predefinito per il cluster HDInsight. La posizione deve essere la stessa delle due reti virtuali.  La posizione corrisponde anche a quella del cluster HDInsight.
   * **Prezzi**: selezionare il numero di nodi del ruolo di lavoro del cluster.
   * **Configurazioni avanzate**: 
     
     * **Aggiunta al dominio e rete virtuale/subnet**: 
       
       * **Impostazioni dominio**: 
         
         * **Nome dominio**: contoso.onmicrosoft.com
         * **Domain user name** (Nome utente dominio): immettere il nome dell'utente del dominio. Questo dominio deve disporre i seguenti privilegi: aggiunta di computer al dominio e inserimento nell'unità organizzativa specificata durante la creazione del cluster; creazione di entità di servizio nell'unità organizzativa configurata in precedenza; creazione di voci di DNS inverso. Questo utente del dominio diventerà l'amministratore di HDInsight aggiunto al dominio.
         * **Password del dominio**: immettere la password del dominio.
         * **Unità organizzativa**: immettere il nome distinto dell'unità organizzativa che si desidera usare con il cluster HDInsight. Ad esempio: OU=HDInsightOU,DC=contoso,DC=onmicrosoft,DC=com. Se questa unità organizzativa non esiste, il cluster HDInsight tenterà di creare questa unità operativa. Assicurarsi che l'unità Organizzativa sia già presente o che l'account di dominio disponga delle autorizzazioni per crearne uno nuovo. Se si usa l'account di dominio che fa parte degli amministratori AADDC, si disporrà delle autorizzazioni necessarie per creare l'unità organizzativa.
         * **URL LDAPS**: ldaps://contoso.onmicrosoft.com:636
         * **Access user group** (Gruppo di utenti con accesso): specificare il gruppo di protezione di cui si desidera sincronizzare gli utenti con il cluster. Ad esempio, HiveUsers.
           
           Fare clic su **Seleziona** per salvare le modifiche.
           
           ![Configurazione dell'impostazione del dominio nel portale HDInsight aggiunto al dominio](./media/apache-domain-joined-configure/hdinsight-domain-joined-portal-domain-setting.png)
       * **Rete virtuale**: contosohdivnet
       * **Subnet**: Subnet1
         
         Fare clic su **Seleziona** per salvare le modifiche.        
         Fare clic su **Seleziona** per salvare le modifiche.
   * **Gruppo di risorse**: selezionare il gruppo di risorse usato per la rete virtuale HDInsight (contosohdirg).
4. Fare clic su **Crea**.  

Un'altra opzione per creare un cluster HDInsight aggiunto al dominio consiste nell'usare il modello di Azure Resource Manager. Per capire come, leggere la procedura seguente:

**Per creare un cluster HDInsight aggiunto al dominio usando un modello di Resource Manager**

1. Fare clic sull'immagine seguente per aprire un modello di Resource Manager nel portale di Azure. Il modello di Resource Manager è disponibile in un contenitore BLOB pubblico. 
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-domain-joined-hdinsight-cluster.json" target="_blank"><img src="./media/apache-domain-joined-configure/deploy-to-azure.png" alt="Deploy to Azure"></a>
2. Nel pannello **Parametri** inserire i valori seguenti:
   
   * **Sottoscrizione**: (selezionare una sottoscrizione di Azure).
   * **Gruppo di risorse**: fare clic su **Usa esistenti** e specificare lo stesso gruppo di risorse usate fino a questo momento.  Ad esempio, contosohdirg. 
   * **Posizione**: specificare la posizione del gruppo di risorse.
   * **Nome cluster**: immettere un nome per il cluster Hadoop che verrà creato. Ad esempio, contosohdicluster.
   * **Tipo di cluster**: selezionare un tipo di cluster.  Il valore predefinito è **hadoop**.
   * **Posizione**: selezionare una posizione per il cluster.  L'account di archiviazione predefinito usa la stessa posizione.
   * **Cluster Worker Node count** (Numero nodi ruolo di lavoro cluster): selezionare il numero di nodi del ruolo di lavoro.
   * **Cluster login name and password**: il nome dell'account di accesso predefinito è **admin**.
   * **SSH username and password**: il nome utente predefinito è **sshuser**.  È possibile rinominarlo. 
   * **ID rete virtuale**: /subscriptions/&lt;SubscriptionID&gt;/resourceGroups/&lt;ResourceGroupName&gt;/providers/Microsoft.Network/virtualNetworks/&lt;VNetName&gt;
   * **Subnet rete virtuale**: /subscriptions/&lt;SubscriptionID&gt;/resourceGroups/&lt;ResourceGroupName&gt;/providers/Microsoft.Network/virtualNetworks/&lt;VNetName&gt;/subnets/Subnet1
   * **Nome dominio**: contoso.onmicrosoft.com
   * **Organization Unit DN** (DN unità organizzativa): OU=HDInsightOU,DC=contoso,DC=onmicrosoft,DC=com
   * **Gruppo di utenti cluster DNs**: [\"UtentiHive\"]
   * **URL LDAP**: ["ldaps://contoso.onmicrosoft.com:636"]
   * **DomainAdminUserName** (Nome utente amministratore dominio): (immettere il nome dell'utente amministratore del dominio)
   * **DomainAdminPassword** (Password amministratore dominio): (immettere la password dell'utente amministratore del dominio)
   * **Accetto le condizioni riportate sopra**: (selezionare)
   * **Aggiungi al dashboard**: (selezionare)
3. Fare clic su **Acquista**. Verrà visualizzato un nuovo riquadro con nome **Distribuzione dell'entità Distribuzione modello in corso**. La creazione di un cluster richiede circa 20 minuti. Dopo aver creato il cluster, è possibile fare clic sul pannello del cluster nel portale per aprirlo.

Al termine dell'esercitazione, è consigliabile eliminare il cluster. Con HDInsight, i dati vengono archiviati in Archiviazione di Azure ed è possibile eliminare tranquillamente un cluster quando non viene usato. Vengono addebitati i costi anche per i cluster HDInsight che non sono in uso. Poiché i costi per il cluster sono decisamente superiori a quelli per l'archiviazione, economicamente ha senso eliminare i cluster quando non vengono usati. Per istruzioni sull'eliminazione di un cluster, vedere [Gestire cluster Hadoop in HDInsight tramite il portale di Azure](../hdinsight-administer-use-management-portal.md#delete-clusters).

## <a name="next-steps"></a>Passaggi successivi
* Per configurare i criteri ed eseguire query Hive, vedere [Configure Hive policies for Domain-joined HDInsight clusters](apache-domain-joined-run-hive.md) (Configurare criteri Hive per cluster HDInsight aggiunti al dominio).
* Per usare SSH per la connessione a cluster HDInsight aggiunti al dominio, vedere [Usare SSH con Hadoop basato su Linux in HDInsight da Linux, Unix o OS X](../hdinsight-hadoop-linux-use-ssh-unix.md#domainjoined).

