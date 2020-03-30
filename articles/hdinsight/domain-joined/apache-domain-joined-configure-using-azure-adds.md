---
title: Sicurezza aziendale con Azure AD DS - Azure HDInsight
description: Informazioni su come configurare un cluster HDInsight Enterprise Security Package tramite Servizi di dominio Azure Active Directory.Learn how to set up and configure an HDInsight Enterprise Security Package cluster by using Azure Active Directory Domain Services.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: seodec18
ms.date: 02/03/2020
ms.openlocfilehash: cf239cbf69f3816e5ec03e07e2bd5fe370308f22
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79272838"
---
# <a name="enterprise-security-package-configurations-with-azure-active-directory-domain-services-in-hdinsight"></a>Configurazioni dei pacchetti di sicurezza aziendali con Servizi di dominio Azure Active Directory in HDInsight

I cluster Enterprise Security Package (ESP) forniscono l'accesso multiutente nei cluster HDInsight di Azure.Enterprise Security Package (ESP) clusters provide multiuser access on Azure HDInsight clusters. I cluster HDInsight con ESP sono collegati a un dominio, in modo che gli utenti del dominio possano usare le proprie credenziali per effettuare l'autenticazione con i cluster ed eseguire processi di Big Data.

In questo articolo viene illustrato come configurare un cluster HDInsight con ESP usando Servizi di dominio Azure Active Directory .Azure AD DS.

> [!NOTE]  
> ESP è generalmente disponibile in HDInsight 3.6 e 4.0 per questi tipi di cluster: Apache Spark, Interactive, Hadoop e HBase. ESP per il tipo di cluster Apache Kafka è in anteprima solo con il supporto per il massimo sforzo. I cluster ESP creati prima della data ESP GA (1 ottobre 2018) non sono supportati.

## <a name="enable-azure-ad-ds"></a>Abilitare Azure AD DS

> [!NOTE]  
> Solo gli amministratori tenant dispongono dei privilegi per abilitare Servizi di dominio Active Directory di Azure.Only tenant administrators have the privileges to enable Azure AD DS. Se l'archiviazione cluster è Azure Data Lake Storage Gen1 o Gen2, è necessario disabilitare Azure Multi-Factor Authentication solo per gli utenti che dovranno accedere al cluster usando l'autenticazione Kerberos di base. 
>
> È possibile usare gli indirizzi IP attendibili o [l'accesso condizionale](../../active-directory/conditional-access/overview.md) per disabilitare L'autenticazione a più fattori per utenti specifici solo quando accedono all'intervallo IP per la rete virtuale del cluster HDInsight.You can use [trusted IPs](../../active-directory/authentication/howto-mfa-mfasettings.md#trusted-ips) or Conditional Access to disable Multi-Factor Authentication for specific users *only* when they're accessing the IP range for the HDInsight cluster's virtual network. Se si usa l'accesso condizionale, assicurarsi che l'endpoint del servizio Active Directory sia abilitato nella rete virtuale HDInsight.If you're using Conditional Access, make sure that the Active Directory service endpoint in enabled on the HDInsight virtual network.
>
> Se l'archiviazione cluster è Archiviazione BLOB di Azure, non disabilitare l'autenticazione a più fattori.

L'abilitazione di Servizi di dominio Azure AD è un prerequisito prima di poter creare un cluster HDInsight con ESP. Per altre informazioni, vedere Abilitare Servizi di dominio Azure Active Directory tramite il portale di Azure.For more information, see [Enable Azure Active Directory Domain Services by using the Azure portal.](../../active-directory-domain-services/tutorial-create-instance.md) 

Quando Servizi di dominio Active Directory di Azure è abilitato, per impostazione predefinita tutti gli utenti e gli oggetti avviano la sincronizzazione da Azure Active Directory (Azure AD) ad Azure AD DS. La durata dell'operazione di sincronizzazione dipende dal numero di oggetti in Azure AD. La sincronizzazione potrebbe richiedere alcuni giorni per centinaia di migliaia di oggetti. 

Il nome di dominio usato con Azure AD DS deve essere massimo di 39 caratteri per poter essere utilizzato con HDInsight.The domain name that you use with Azure AD DS must be 39 characters or fewer, to work with HDInsight.

È possibile scegliere di sincronizzare solo i gruppi che devono accedere ai cluster HDInsight.You can choose to sync only the groups that need access to the HDInsight clusters. Questa opzione di sincronizzazione che coinvolge solo determinati gruppi è chiamata *sincronizzazione con ambito*. Per istruzioni, vedere Configurare la [sincronizzazione con ambito da Azure AD al dominio gestito.](../../active-directory-domain-services/scoped-synchronization.md)

Quando si abilita LDAP protetto, inserire il nome di dominio nel nome del soggetto e il nome alternativo del soggetto nel certificato. Se, ad esempio, il nome di dominio è *contoso100.onmicrosoft.com*, verificare che nel nome del soggetto del certificato e nel nome alternativo del soggetto sia presente il nome esatto. Per altre informazioni, vedere [Configurare l'accesso LDAP sicuro (LDAPS) per un dominio gestito di Azure AD DS](../../active-directory-domain-services/tutorial-configure-ldaps.md). 

Nell'esempio seguente viene creato un certificato autofirmato. Il nome *di* dominio `Subject` contoso100.onmicrosoft.com è `DnsName` sia (nome soggetto) che (nome alternativo del soggetto).

```powershell
$lifetime=Get-Date
New-SelfSignedCertificate -Subject contoso100.onmicrosoft.com `
  -NotAfter $lifetime.AddDays(365) -KeyUsage DigitalSignature, KeyEncipherment `
  -Type SSLServerAuthentication -DnsName *.contoso100.onmicrosoft.com, contoso100.onmicrosoft.com
```

## <a name="check-azure-ad-ds-health-status"></a>Controllare lo stato di integrità di Azure AD DSCheck Azure AD DS health status
Visualizzare lo stato di integrità di Servizi di dominio Azure Active Directory selezionando Integrità nella categoria **Gestisci.View** the health status of Azure Active Directory Domain Services by selecting **Health** in the Manage category. Verificare che lo stato di Azure AD DS sia verde (in esecuzione) e che la sincronizzazione sia stata completata.

![Integrità di Azure AD DSAzure AD DS health](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-health.png)

## <a name="create-and-authorize-a-managed-identity"></a>Creare e autorizzare un'identità gestitaCreate and authorize a managed identity

È possibile utilizzare *un'identità gestita assegnata dall'utente* per semplificare e proteggere le operazioni dei servizi di dominio. Quando si assegna il ruolo **Collaboratore servizi** di dominio HDInsight all'identità gestita, è possibile leggere, creare, modificare ed eliminare le operazioni dei servizi di dominio. 

Alcune operazioni di servizi di dominio, ad esempio la creazione di unità utente e le entità servizio, sono necessarie per HDInsight Enterprise Security Package.Certain domain services operations, such as creating OUs and service principals, are needed for HDInsight Enterprise Security Package. È possibile creare identità gestite in qualsiasi sottoscrizione. Per altre informazioni sulle identità gestite in generale, vedere Identità gestite per le risorse di Azure.For more information on managed identities in general, see [Managed identities for Azure resources](../../active-directory/managed-identities-azure-resources/overview.md). Per altre informazioni sul funzionamento delle identità gestite in Azure HDInsight, vedere Identità gestite in Azure HDInsight.For more information on how managed identities work in Azure HDInsight, see [Managed identities in Azure HDInsight.](../hdinsight-managed-identities.md)

Per configurare i cluster ESP, creare un'identità gestita assegnata dall'utente, se non ne è già presente una. Per istruzioni, vedere [Creare, elencare, eliminare o assegnare un ruolo a un'identità gestita assegnata dall'utente tramite il portale di Azure.For instructions, see Create, list, delete, or assign a role to a user-assigned managed identity by using the Azure portal](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md). 

Assegnare quindi il ruolo **Collaboratore di Servizi di dominio HDInsight** all'identità gestita nel **controllo di accesso** per Servizi di dominio Active Directory. Per eseguire questa assegnazione di ruolo, sono necessari privilegi di amministratore di Azure AD DS.

![Controllo di accesso di Azure Active Directory Domain Services](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-configure-managed-identity.png)

L'assegnazione del ruolo Di collaboratore servizi di dominio HDInsight garantisce che questa identità disponga dell'accesso appropriato (per conto di) per eseguire operazioni sui servizi di dominio di dominio active Directory.Assigning the **HDInsight Domain Services Contributor** role ensures that this identity has proper (on behalf of) access to perform domain services operations on the Azure AD DS domain. Queste operazioni includono la creazione e l'eliminazione di aree utente.

Dopo aver creato l'identità gestita e aver assegnato il ruolo corretto, l'amministratore di Servizi di dominio Active Directory di Azure può configurare chi può usare questa identità gestita. Innanzitutto, l'amministratore seleziona l'identità gestita nel portale, quindi seleziona **Controllo di accesso (IAM)** in **Panoramica**. Quindi, a destra, l'amministratore assegna il ruolo **Operatore identità gestite** agli utenti o ai gruppi che desiderano creare cluster ESP HDInsight. 

Ad esempio, l'amministratore di Servizi di dominio Azure AD può assegnare questo ruolo al gruppo **MarketingTeam** per l'identità gestita **sjmsi,** come illustrato nell'immagine seguente. Questa assegnazione garantisce che gli utenti giusti nell'organizzazione possano utilizzare l'identità gestita per creare cluster ESP.

![Assegnazione del ruolo Operatore di identità gestite di HDInsight](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-managed-identity-operator-role-assignment.png)

## <a name="network-considerations"></a>Considerazioni sulla rete

> [!NOTE]  
> Servizi di dominio Active Directory di Azure deve essere distribuito in una rete virtuale basata su Azure Resource Manager.Azure AD DS must be deployed in an Azure Resource Manager-based virtual network. Le reti virtuali classiche non sono supportate per Servizi di dominio Active Directory di Azure.Classic virtual networks are not supported for Azure AD DS. Per altre informazioni, vedere Abilitare Servizi di dominio Azure Active Directory tramite il portale di Azure.For more information, see [Enable Azure Active Directory Domain Services by using the Azure portal.](../../active-directory-domain-services/tutorial-create-instance-advanced.md#create-and-configure-the-virtual-network)

Dopo aver abilitato Servizi di dominio Active Directory di Azure, viene eseguito un server DNS (Domain Name System) locale nelle macchine virtuali (VM) di Active Directory. Configurare la rete virtuale di Servizi di dominio Active Directory di Azure per l'uso di questi server DNS personalizzati. Per individuare gli indirizzi IP corretti, selezionare **Proprietà** nella categoria **Gestisci** e cercare in **IP ADDRESS ON VIRTUAL NETWORK**.

![Individuare gli indirizzi IP per i server DNS locali](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-dns1.png)

Modificare la configurazione dei server DNS nella rete virtuale di Servizi di dominio Active Directory di Azure per usare questi indirizzi IP personalizzati selezionando **I server DNS** nella categoria **Impostazioni.** Selezionare quindi l'opzione **Personalizzato,** immettere il primo indirizzo IP nella casella di testo e selezionare **Salva**. Aggiungere altri indirizzi IP utilizzando la stessa procedura.

![Aggiornamento della configurazione DNS della rete virtuale](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-vnet-configuration.png)

È più semplice posizionare sia l'istanza Azure AD DS, sia il cluster HDInsight nella stessa rete virtuale di Azure. Se si prevede di usare reti virtuali diverse, è necessario eseguire il peernetwork in tali reti virtuali in modo che il controller di dominio sia visibile alle macchine virtuali HDInsight.If you plan to use different virtual networks, you must peer those virtual networks so that the domain controller is visible to HDInsight VMs. Per altre informazioni, vedere [Peering di rete virtuale](../../virtual-network/virtual-network-peering-overview.md). 

Dopo aver eseguito il peered nelle reti virtuali, configurare la rete virtuale HDInsight per l'uso di un server DNS personalizzato e immettere gli indirizzi IP privati di Azure AD DS come indirizzi del server DNS. Quando entrambe le reti virtuali usano gli stessi server DNS, il nome di dominio personalizzato verrà risolto nell'indirizzo IP corretto e raggiungibile da HDInsight.When both virtual networks use the same DNS servers, your custom domain name will resolve to the right IP and will be reachable from HDInsight. Ad esempio, se il `contoso.com`nome di dominio `ping contoso.com` è , dopo questo passaggio deve essere risolto nell'IP di Azure AD DS corretto.

![Configurazione di server DNS personalizzati per una rete virtuale con peering](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-peered-vnet-configuration.png)

Se si usano regole del gruppo di sicurezza di rete (NSG) nella subnet HDInsight, è necessario consentire gli [indirizzi IP necessari](../hdinsight-management-ip-addresses.md) per il traffico in ingresso e in uscita.

Per verificare se la rete è configurata correttamente, aggiungere una macchina virtuale Windows alla rete virtuale/subnet HDInsight ed eseguire il ping del nome di dominio. (Dovrebbe risolversi in un IP.) Eseguire **ldp.exe** per accedere al dominio di Azure AD DS. Aggiungere quindi questa macchina virtuale Windows al dominio per verificare che tutte le chiamate RPC necessarie abbiano esito positivo tra il client e il server. 

È anche possibile usare **nslookup** per confermare l'accesso di rete all'account di archiviazione o a qualsiasi database esterno che è possibile usare (ad esempio, metastore Hive esterno o Ranger DB). Assicurarsi che tutte le [porte necessarie](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd772723(v=ws.10)#communication-to-domain-controllers) siano consentite nelle regole del gruppo di sicurezza di rete della subnet di Azure Active Directory, se un gruppo di sicurezza di rete consente di proteggere Azure AD DS. Se l'aggiunta al dominio di questa macchina virtuale Windows ha esito positivo, è possibile continuare con il passaggio successivo e creare cluster ESP.

## <a name="create-an-hdinsight-cluster-with-esp"></a>Creare un cluster HDInsight con ESPCreate an HDInsight cluster with ESP

Dopo aver configurato correttamente i passaggi precedenti, il passaggio successivo consiste nel creare il cluster HDInsight con ESP abilitato. Quando si crea un cluster HDInsight, è possibile abilitare pacchetto di sicurezza aziendale nella scheda **Sicurezza e rete.** Se si preferisce usare un modello di Azure Resource Manager per la distribuzione, usare una volta l'esperienza del portale e scaricare il modello precompilato nella pagina **Revisione e creazione** per riutilizzarli in futuro. 

È anche possibile abilitare la funzionalità [HDInsight ID Broker](identity-broker.md) durante la creazione del cluster. La funzionalità BROKER ID consente di accedere ad Ambari usando Multi-Factor Authentication e ottenere i ticket Kerberos necessari senza la necessità di errori di password in Azure AD DS.

> [!NOTE]  
> I primi sei caratteri dei nomi di cluster ESP devono essere univoci nell'ambiente in uso. Ad esempio, se si dispone di più cluster ESP in reti virtuali diverse, scegliere una convenzione di denominazione che garantisca che i primi sei caratteri nei nomi dei cluster siano univoci.

![Convalida del dominio per il pacchetto di sicurezza aziendale di Azure HDInsightDomain validation for Azure HDInsight Enterprise Security Package](./media/apache-domain-joined-configure-using-azure-adds/azure-portal-cluster-security-networking-esp.png)

Dopo aver abilitato ESP, le configurazioni errate comuni correlate a Servizi di dominio Active Directory di Azure vengono rilevate e convalidate automaticamente. Dopo aver risolto questi errori, è possibile continuare con il passaggio successivo.

![Convalida del dominio del pacchetto di sicurezza aziendale di Azure HDInsight non riuscitaAzure HDInsight Enterprise Security Package failed domain validation](./media/apache-domain-joined-configure-using-azure-adds/azure-portal-cluster-security-networking-esp-error.png)

Quando si crea un cluster HDInsight con ESP, è necessario fornire i parametri seguenti:When you create an HDInsight cluster with ESP, you must supply the following parameters:

- **Utente amministratore cluster:** scegliere un amministratore per il cluster dall'istanza sincronizzata di Servizi di dominio Azure AD. Questo account di dominio deve essere già sincronizzato e disponibile in Servizi di dominio Active Directory di Azure.This domain account must be already synced and available in Azure AD DS.

- Gruppi di accesso al cluster: i gruppi di sicurezza di cui si desidera sincronizzare gli utenti e hanno accesso al cluster devono essere disponibili in Servizi di dominio Active Directory di **Azure.Cluster access groups:** The security groups whose users you want to sync and have access to the cluster should be available in Azure AD DS. Un esempio è il gruppo HiveUsers.An example is the HiveUsers group. Per altre informazioni, vedere [Creare un gruppo e aggiungere membri in Azure Active Directory](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

- **URL LDAPS**: `ldaps://contoso.com:636`Un esempio è .

L'identità gestita creata può essere scelta dall'elenco a discesa **Identità gestita assegnata dall'utente** durante la creazione di un nuovo cluster.

![Identità gestita di Servizi di dominio Active Directory di Azure HDInsight ESPAzure HDInsight ESP Domain Services managed identity](./media/apache-domain-joined-configure-using-azure-adds/azure-portal-cluster-security-networking-identity.png).

## <a name="next-steps"></a>Passaggi successivi

* Per configurare i criteri Hive ed eseguire query Hive, vedere [Configurare criteri Apache Hive per cluster HDInsight con ESP](apache-domain-joined-run-hive.md).
* Per usare SSH per la connessione a cluster HDInsight con ESP, vedere [Usare SSH con Apache Hadoop basato su Linux in HDInsight da Linux, Unix o OS X](../hdinsight-hadoop-linux-use-ssh-unix.md#authentication-domain-joined-hdinsight).
