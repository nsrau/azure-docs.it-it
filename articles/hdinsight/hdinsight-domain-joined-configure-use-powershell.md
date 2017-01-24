---
title: Configurare cluster HDInsight aggiunti al dominio usando Azure PowerShell | Documentazione Microsoft
description: Informazioni su come configurare i cluster HDInsight aggiunti al dominio usando Azure PowerShell
services: hdinsight
documentationcenter: 
author: saurinsh
manager: jhubbard
editor: cgronlun
tags: 
ms.assetid: a13b2f7a-612d-4800-bc92-7fc0524f3e89
ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/02/2016
ms.author: saurinsh
translationtype: Human Translation
ms.sourcegitcommit: 4f8d2956e9f0240392ba839b076d632ccc45d728
ms.openlocfilehash: 3217399ee868707309e96234d4954548b063534a


---
# <a name="configure-domain-joined-hdinsight-clusters-preview-using-azure-powershell"></a>Configurare cluster HDInsight aggiunti al dominio (anteprima) usando Azure PowerShell
Informazioni su come configurare un cluster Azure HDInsight con Azure Active Directory (Azure AD) e [Apache Ranger](http://hortonworks.com/apache/ranger/) usando Azure PowerShell. Viene fornito uno script di Azure PowerShell per velocizzare la configurazione ed evitare errori. HDInsight aggiunto al dominio può essere configurato solo nei cluster basati su Linux. Per altre informazioni, consultare [Introduce Domain-joined HDInsight clusters](hdinsight-domain-joined-introduction.md) (Introduzione ai cluster HDInsight aggiunti al dominio).

La configurazione tipica di un cluster HDInsight aggiunto al dominio prevede i passaggi seguenti:

1. Creare una rete virtuale classica di Azure per Azure AD.  
2. Creare e configurare Azure AD e Azure AD DS.
3. Aggiungere una VM alla rete virtuale classica per creare un'unità organizzativa. 
4. Creare un'unità organizzativa per Azure AD DS.
5. Creare una rete virtuale di HDInsight in modalità Azure Resource Manager.
6. Configurare zone DNS inverse per Azure AD DS.
7. Eseguire il peering delle due reti virtuali.
8. Creare un cluster HDInsight

Lo script di PowerShell fornito esegue i passaggi da 3 a 7. È necessario eseguire manualmente i passaggi 1 e 2.  Se si preferisce non usare Azure PowerShell, vedere [Configure Domain-joined HDInsight clusters](hdinsight-domain-joined-configure.md) (Configurare cluster HDInsight aggiunti al dominio). 

Un esempio di aspetto della topologia finale potrebbe essere il seguente:

![Topologia di HDInsight aggiunto al dominio](./media/hdinsight-domain-joined-configure/hdinsight-domain-joined-topology.png)

Dal momento che attualmente Azure AD supporta solo le reti virtuali classiche mentre i cluster HDInsight basati su Linux supportano solo reti virtuali basate su Azure Resource Manager, l'integrazione di Azure AD HDInsight richiede due reti virtuali e il peering tra di loro. Per informazioni sul confronto tra i due modelli vedere [Confronto tra distribuzione Azure Resource Manager e classica: comprensione dei modelli di distribuzione e dello stato delle risorse](../azure-resource-manager/resource-manager-deployment-model.md). Le due reti virtuali devono trovarsi nella stessa area di Azure AD DS.

> [!NOTE]
> In questa esercitazione si presuppone che non sia disponibile un account Azure. Se si dispone di un account, è possibile ignorare il passaggio 2.
> 
> 

## <a name="prerequisites"></a>Prerequisiti
Sono necessari gli elementi seguenti per eseguire questa esercitazione:

* Acquisire familiarità con [Azure AD Domain Services](https://azure.microsoft.com/services/active-directory-ds/) e con la struttura dei [prezzi](https://azure.microsoft.com/pricing/details/active-directory-ds/).
* Assicurarsi che la sottoscrizione sia consentita per questa anteprima pubblica. Per farlo, è possibile inviare un messaggio di posta elettronica a hdipreview@microsoft.com con l'ID sottoscrizione.
* Un certificato SSL firmato da un'autorità di firma per il dominio. Il certificato viene richiesto configurando LDAP sicuro. Non è possibile usare certificati autofirmati.
* Azure PowerShell.  Vedere [Install and configure Azure PowerShell](/powershell/azureps-cmdlets-docs) (Installare e configurare Azure PowerShell).

## <a name="create-an-azure-classic-vnet-for-your-azure-ad"></a>Creare una rete virtuale classica di Azure per Azure AD.
Per le istruzioni, vedere [qui](hdinsight-domain-joined-configure.md#create-an-azure-classic-vnet).

## <a name="create-and-configure-azure-ad-and-azure-ad-ds"></a>Creare e configurare Azure AD e Azure AD DS.
Per le istruzioni, vedere [qui](hdinsight-domain-joined-configure.md#create-and-configure-azure-ad-ds-for-your-azure-ad).

## <a name="run-the-powershell-script"></a>Eseguire lo script di PowerShell
Lo script di PowerShell può essere scaricato da [GitHub](https://github.com/hdinsight/DomainJoinedHDInsight). Estrarre il file ZIP e salvare i file in locale.

**Per modificare lo script di PowerShell**

1. Aprire run.ps1 usando Windows PowerShell ISE o qualsiasi editor di testo.
2. Specificare i valori per le variabili seguenti:
   
   * **$SubscriptionName**: nome della sottoscrizione di Azure in cui si vuole creare il cluster HDInsight. È già stata creata una rete virtuale classica in questa sottoscrizione. Ora verrà creata una rete virtuale di Azure Resource Manager per il cluster HDInsight nella sottoscrizione.
   * **$ClassicVNetName**: rete virtuale classica contenente Azure Active Directory Domain Services. Questa rete virtuale deve essere nella stessa sottoscrizione specificata sopra. Questa rete virtuale deve essere creata con il portale di Azure e non con il portale classico. Se si seguono le istruzioni in [Configure Domain-joined HDInsight clusters (Preview)](hdinsight-domain-joined-configure.md#create-an-azure-classic-vnet) (Configurare cluster HDInsight aggiunti al dominio - anteprima), il nome predefinito è contosoaadvnet.
   * **$ClassicResourceGroupName**: nome del gruppo di Resource Manager per la rete virtuale classica menzionata sopra, ad esempio contosoaadrg. 
   * **$ArmResourceGroupName**: nome del gruppo di risorse in cui si vuole creare il cluster HDInsight. È possibile usare lo stesso gruppo di risorse di $ArmResourceGroupName.  Se il gruppo di risorse non esiste, viene creato dallo script.
   * **$ArmVNetName**: nome della rete virtuale di Resource Manager in cui si vuole creare il cluster HDInsight. Questa rete virtuale verrà inserita in $ArmResourceGroupName.  Se la rete virtuale non esiste, verrà creata dallo script di PowerShell. Se esiste, deve fare parte del gruppo di risorse specificato sopra.
   * **$AddressVnetAddressSpace**: spazio indirizzi per la rete virtuale di Resource Manager. Assicurarsi che questo spazio indirizzi sia disponibile. Questo spazio indirizzi non può sovrapporsi allo spazio indirizzi della rete virtuale classica. Ad esempio, "10.1.0.0/16".
   * **$ArmVnetSubnetName**: nome della subnet della rete virtuale di Resource Manager in cui si vogliono inserire le VM del cluster HDInsight. Se la subnet non esiste, verrà creata dallo script di PowerShell. Se esiste, deve fare parte della rete virtuale specificata sopra.
   * **$AddressSubnetAddressSpace**: intervallo di indirizzi di rete per la subnet della rete virtuale di Resource Manager. Gli indirizzi IP delle VM del cluster HDInsight saranno compresi in questo intervallo di indirizzi della subnet. Ad esempio, "10.1.0.0/24".
   * **$ActiveDirectoryDomainName**: nome di dominio Azure AD a cui si vogliono aggiungere le VM del cluster HDInsight. Ad esempio, "contoso.onmicrosoft.com".
   * **$ClusterUsersGroups**: nome comune dei gruppi di sicurezza dall'istanza di AD che si vuole sincronizzare con il cluster HDInsight. Gli utenti di questo gruppo di sicurezza potranno accedere al dashboard del cluster usando le credenziali di dominio di Active Directory. Questi gruppi di sicurezza devono esistere in Active Directory. Ad esempio, "hiveusers" o "clusteroperatorusers".
   * **$OrganizationalUnitName**: unità organizzativa nel dominio, in cui si vogliono inserire le VM del cluster HDInsight e le entità servizio usate dal cluster. Se questa unità organizzativa non esiste, verrà creata dallo script di PowerShell. Ad esempio, "HDInsightOU".
3. Salvare le modifiche.

**Per eseguire lo script**

1. Eseguire **Windows PowerShell** come amministratore.
2. Passare alla cartella di run.ps1. 
3. Eseguire lo script digitando il nome file e premere **INVIO**.  Vengono visualizzate 3 finestre di dialogo di accesso:
   
   1. **Sign in to Azure classic portal** (Accedi al portale di Azure classico): immettere le credenziali usate per accedere al portale di Azure classico. È necessario avere creato l'istanza di Azure AD e di Azure Active Directory Domain Services con queste credenziali.
   2. **Sign in to Azure Resource Manager portal** (Accedi al portale di Azure Resource Manager): immettere le credenziali usate per accedere al portale di Azure Resource Manager.
   3. **Nome utente di dominio**: immettere le credenziali del nome utente di dominio che sarà l'amministratore nel cluster HDInsight. Se è stata creata un'istanza di Azure AD da zero, questo utente deve essere stato creato usando questa documentazione. 
      
      > [!IMPORTANT]
      > Immettere il nome utente in questo formato: 
      > 
      > Nomedominio\nomeutente (ad esempio, contoso.onmicrosoft.com\clusteradmin)
      > 
      > 
      
      Questo utente deve avere 3 privilegi: aggiungere i computer al dominio di Active Directory specificato, creare le entità servizio e gli oggetti computer nell'unità organizzativa specificata e aggiungere le regole del proxy DNS inverso.

Durante la creazione di zone DNS inverso, lo script chiederà di immettere un'ID rete. Questo ID rete deve essere il prefisso dell'indirizzo della rete virtuale di Resource Manager. Se, ad esempio, lo spazio indirizzi della subnet della rete virtuale di Resource Manager è 10.2.0.0/24, immettere 10.2.0.0/24 quando lo strumento chiede l'ID rete. 

## <a name="create-hdinsight-cluster"></a>Creare un cluster HDInsight
In questa sezione viene creato un cluster Hadoop basato su Linux in HDInsight usando il portale di Azure o il [modello di Azure Resource Manager](../azure-resource-manager/resource-group-template-deploy.md). Per altri metodi di creazione di cluster e per informazioni sulle impostazioni, vedere l'articolo sulla [creazione di cluster HDInsight](hdinsight-hadoop-provision-linux-clusters.md). Per altre informazioni sull'uso di un modello di Resource Manager per creare cluster Hadoop in HDInsight, vedere [Creare cluster Hadoop in HDInsight tramite modelli di Resource Manager](hdinsight-hadoop-create-windows-clusters-arm-templates.md)

**Per creare un cluster HDInsight aggiunto al dominio usando il portale di Azure**

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Fare clic su **Nuovo**, **Intelligence e analisi**, quindi su **HDInsight**.
3. Nel pannello **Nuovo cluster HDInsight** immettere o selezionare i valori seguenti:
   
   * **Nome cluster**: immettere un nuovo nome per il cluster HDInsight aggiunto al dominio.
   * **Sottoscrizione**: selezionare la sottoscrizione di Azure usata per creare il cluster.
   * **Configurazione cluster**:
     
     * **Tipo di cluster**: Hadoop. HDInsight aggiunto al dominio è attualmente supportato solo nei cluster Hadoop.
     * **Sistema operativo**: Linux.  HDInsight aggiunto al dominio è supportato solo nei cluster HDInsight basati su Linux.
     * **Versione**: Hadoop 2.7.3 (HDI 3.5). HDInsight aggiunto al dominio è supportato solo nei cluster HDInsight versione 3.5.
     * **Tipo di cluster**: PREMIUM
       
       Fare clic su **Seleziona** per salvare le modifiche.
   * **Credenziali**: configurare le credenziali sia per l'utente del cluster che per l'utente SSH.
   * **Origine dati**: creare un nuovo account di archiviazione oppure usarne uno esistente come account di archiviazione predefinito per il cluster HDInsight. La posizione deve essere la stessa delle due reti virtuali.  La posizione corrisponde anche a quella del cluster HDInsight.
   * **Prezzi**: selezionare il numero di nodi del ruolo di lavoro del cluster.
   * **Configurazioni avanzate**: 
     
     * **Aggiunta al dominio e rete virtuale/subnet**: 
       
       * **Impostazioni dominio**: 
         
         * **Nome dominio**: contoso.onmicrosoft.com
         * **Domain user name** (Nome utente dominio): immettere il nome dell'utente del dominio. Questo dominio deve disporre dei seguenti privilegi: aggiunta di computer al dominio e inserimento nell'unità organizzativa configurata in precedenza; creazione di entità di servizio nell'unità organizzativa configurata in precedenza; creazione di voci di DNS inverso. Questo utente del dominio diventerà l'amministratore di HDInsight aggiunto al dominio.
         * **Password del dominio**: immettere la password del dominio.
         * **Unità organizzativa**: immettere il nome distinto dell'unità organizzativa configurata in precedenza. Ad esempio: OU=HDInsightOU,DC=contoso,DC=onmicrosoft,DC=com
         * **URL LDAPS**: ldaps://contoso.onmicrosoft.com:636
         * **Access user group** (Gruppo di utenti con accesso): specificare il gruppo di protezione di cui si desidera sincronizzare gli utenti con il cluster. Ad esempio, HiveUsers.
           
           Fare clic su **Seleziona** per salvare le modifiche.
           
           ![Configurazione dell'impostazione del dominio nel portale HDInsight aggiunto al dominio](./media/hdinsight-domain-joined-configure/hdinsight-domain-joined-portal-domain-setting.png)
       * **Rete virtuale**: contosohdivnet
       * **Subnet**: Subnet1
         
         Fare clic su **Seleziona** per salvare le modifiche.        
         Fare clic su **Seleziona** per salvare le modifiche.
   * **Gruppo di risorse**: selezionare il gruppo di risorse usato per la rete virtuale HDInsight (contosohdirg).
4. Fare clic su **Crea**.  

Un'altra opzione per creare un cluster HDInsight aggiunto al dominio consiste nell'usare il modello di Azure Resource Manager. Per capire come, leggere la procedura seguente:

**Per creare un cluster HDInsight aggiunto al dominio usando un modello di Resource Manager**

1. Fare clic sull'immagine seguente per aprire un modello di Resource Manager nel portale di Azure. Il modello di Resource Manager è disponibile in un contenitore BLOB pubblico. 
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-domain-joined-hdinsight-cluster.json" target="_blank"><img src="./media/hdinsight-domain-joined-configure-use-powershell/deploy-to-azure.png" alt="Deploy to Azure"></a>
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
   * **ID rete virtuale**: /subscriptions/&lt;SubscriptionID>/resourceGroups/&lt;ResourceGroupName>/providers/Microsoft.Network/virtualNetworks/&lt;VNetName>
   * **Subnet rete virtuale**: /subscriptions/&lt;SubscriptionID>/resourceGroups/&lt;ResourceGroupName>/providers/Microsoft.Network/virtualNetworks/&lt;VNetName>/subnets/Subnet1
   * **Nome dominio**: contoso.onmicrosoft.com
   * **Organization Unit DN** (DN unità organizzativa): OU=HDInsightOU,DC=contoso,DC=onmicrosoft,DC=com
   * **Cluster Users Group D Ns** (DN gruppo di utenti cluster): "\"CN=HiveUsers,OU=AADDC Users,DC=<DomainName>,DC=onmicrosoft,DC=com\""
   * **URL LDAP**: ["ldaps://contoso.onmicrosoft.com:636"]
   * **DomainAdminUserName** (Nome utente amministratore dominio): (immettere il nome dell'utente amministratore del dominio)
   * **DomainAdminPassword** (Password amministratore dominio): (immettere la password dell'utente amministratore del dominio)
   * **Accetto le condizioni riportate sopra**: (selezionare)
   * **Aggiungi al dashboard**: (selezionare)
3. Fare clic su **Acquista**. Verrà visualizzato un nuovo riquadro con nome **Distribuzione dell'entità Distribuzione modello in corso**. La creazione di un cluster richiede circa 20 minuti. Dopo aver creato il cluster, è possibile fare clic sul pannello del cluster nel portale per aprirlo.

Al termine dell'esercitazione, è consigliabile eliminare il cluster. Con HDInsight, i dati vengono archiviati in Archiviazione di Azure ed è possibile eliminare tranquillamente un cluster quando non viene usato. Vengono addebitati i costi anche per i cluster HDInsight che non sono in uso. Poiché i costi per il cluster sono decisamente superiori a quelli per l'archiviazione, economicamente ha senso eliminare i cluster quando non vengono usati. Per istruzioni sull'eliminazione di un cluster, vedere [Gestire cluster Hadoop in HDInsight tramite il portale di Azure](hdinsight-administer-use-management-portal.md#delete-clusters).

## <a name="next-steps"></a>Passaggi successivi

* Per configurare i criteri ed eseguire query Hive, vedere [Configure Hive policies for Domain-joined HDInsight clusters](hdinsight-domain-joined-run-hive.md) (Configurare criteri Hive per cluster HDInsight aggiunti al dominio).
* Per usare SSH per la connessione a cluster HDInsight aggiunti al dominio, vedere [Usare SSH con Hadoop basato su Linux in HDInsight da Linux, Unix o OS X](hdinsight-hadoop-linux-use-ssh-unix.md#domainjoined).




<!--HONumber=Dec16_HO3-->


