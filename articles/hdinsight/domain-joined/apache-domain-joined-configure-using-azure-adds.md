---
title: Configurazione di Enterprise Security Package con Azure Active Directory Domain Services - Azure HDInsight
description: Informazioni su come impostare e configurare un cluster HDInsight Enterprise Security Package usando Azure Active Directory Domain Services.
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: hrasheed
ms.topic: conceptual
ms.date: 10/09/2018
ms.custom: seodec18
ms.openlocfilehash: 7ad494a3a1ce657951a0afab4d5ca838821927ad
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2019
ms.locfileid: "55158818"
---
# <a name="configure-a-hdinsight-cluster-with-enterprise-security-package-by-using-azure-active-directory-domain-services"></a>Configurare un cluster HDInsight con Enterprise Security Package usando Azure Active Directory Domain Services

I cluster Enterprise Security Package (ESP) offrono un accesso multiutente sui cluster HDInsight di Azure. I cluster HDInsight con ESP sono collegati a un dominio, in modo che gli utenti del dominio possano usare le proprie credenziali per effettuare l'autenticazione con i cluster ed eseguire processi di Big Data. 

Questo articolo illustra come configurare un cluster HDInsight con ESP usando Azure Active Directory Domain Services (Azure AD DS).

>[!NOTE]  
>ESP è disponibile a livello generale in HDI 3.6 per Apache Hadoop, Apache Spark e Interactive. ESP per tipi di cluster Apache HBase e Apache Kafka è disponibile in anteprima.

## <a name="enable-azure-ad-ds"></a>Abilitare Azure AD DS

> [!NOTE]  
> Solo gli amministratori dei tenant hanno i privilegi necessari per abilitare Azure AD DS. Se l'archiviazione cluster è Azure Data Lake Storage (ADLS) Gen1 o Gen2, disabilitare l'autenticazione a più fattori (MFA) solo per gli utenti che dovranno accedere al cluster usando le autenticazioni Kerberos di base. È possibile usare gli [IP attendibili](https://docs.microsoft.com/en-us/azure/active-directory/authentication/howto-mfa-mfasettings#trusted-ips) o l'[accesso condizionale](https://docs.microsoft.com/en-us/azure/active-directory/conditional-access/overview) per disabilitare l'autenticazione a più fattori per utenti specifici SOLO quando accedono all'intervallo di indirizzi IP della rete virtuale del cluster HDInsight. Se si usa l'accesso condizionale, assicurarsi che l'endpoint di servizio AD sia abilitato nella rete virtuale di HDInsight.
>
>Se l'archiviazione del cluster viene eseguita in Archiviazione BLOB di Azure (WASB), non disabilitare MFA.



L'abilitazione di Azure Active Directory Domain Services è un prerequisito per la creazione di un cluster HDInsight con ESP. Per altre informazioni vedere [Abilitare Azure Active Directory Domain Services tramite il portale di Azure](../../active-directory-domain-services/active-directory-ds-getting-started.md). 

Quando Azure Active Directory Domain Services è abilitato, tutti gli utenti e gli oggetti avviano la sincronizzazione da Azure Active Directory (AAD) ad Azure Active Directory Domain Services per impostazione predefinita. La durata dell'operazione di sincronizzazione dipende dal numero di oggetti in Azure AD. La sincronizzazione potrebbe richiedere alcuni giorni per centinaia di migliaia di oggetti. 

I clienti possono scegliere di sincronizzare solo i gruppi che devono accedere ai cluster HDInsight. Questa opzione di sincronizzazione che coinvolge solo determinati gruppi è chiamata *sincronizzazione con ambito*. Per istruzioni, vedere [Configurare la sincronizzazione con ambito da Azure AD al dominio gestito](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-scoped-synchronization).

Quando si abilita LDAP sicuro, inserire il nome di dominio nel nome del soggetto e il nome alternativo del soggetto nel certificato. Se, ad esempio, il nome di dominio è *contoso100.onmicrosoft.com*, verificare che nel nome del soggetto del certificato e nel nome alternativo del soggetto sia presente il nome esatto. Per altre informazioni, vedere [Configurare l'accesso LDAP sicuro (LDAPS) per un dominio gestito di Azure AD-DS](../../active-directory-domain-services/active-directory-ds-admin-guide-configure-secure-ldap.md). Di seguito è riportato un esempio di creazione di un certificato autofirmato che presenta il nome di dominio *contoso100.onmicrosoft.com* sia nel nome del soggetto sia in DnsName (il nome alternativo del soggetto):

```powershell
$lifetime=Get-Date
New-SelfSignedCertificate -Subject contoso100.onmicrosoft.com `
  -NotAfter $lifetime.AddDays(365) -KeyUsage DigitalSignature, KeyEncipherment `
  -Type SSLServerAuthentication -DnsName *.contoso100.onmicrosoft.com, contoso100.onmicrosoft.com
``` 

## <a name="check-azure-ad-ds-health-status"></a>Controllare lo stato di integrità di Azure Active Directory Domain Services
Visualizzare lo stato integrità di Azure Active Directory Domain Services selezionando **Integrità** nella categoria **Gestione**. Verificare che lo stato di Azure Active Directory Domain Services sia verde (in esecuzione) e che la sincronizzazione sia stata completata.

![Integrità di Azure Active Directory Domain Services](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-health.png)

## <a name="create-and-authorize-a-managed-identity"></a>Creare e autorizzare un'identità gestita

L'**identità gestita assegnata dall'utente** viene usata per semplificare e proteggere le operazioni dei servizi di dominio. Quando si assegna l'identità gestita al ruolo Collaboratore per Servizi di dominio HDInsight, tale identità può leggere, creare, modificare ed eliminare le operazioni dei servizi di dominio. Alcune operazioni dei servizi di dominio, ad esempio la creazione di unità organizzative ed entità servizio, sono necessarie per HDInsight Enterprise Security Package. È possibile creare identità gestite in qualsiasi sottoscrizione. Per altre informazioni, vedere [Identità gestite per le risorse di Azure](../../active-directory/managed-identities-azure-resources/overview.md).

Per configurare i cluster ESP, creare un'identità gestita assegnata dall'utente se non è già disponibile. Per le istruzioni, vedere [Creare, elencare, eliminare o assegnare un ruolo a un'identità gestita assegnata dall'utente mediante il portale di Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal). Assegnare quindi l'identità gestita al ruolo **Collaboratore per HDInsight Domain Services** nel controllo di accesso di Azure Active Directory Domain Services; per eseguire questa assegnazione di ruolo sono necessari i privilegi di amministratore di Azure Active Directory Domain Services.

![Controllo di accesso di Azure Active Directory Domain Services](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-configure-managed-identity.png)

L'assegnazione di un'identità gestita al ruolo **Collaboratore per HDInsight Domain Services** garantisce che l'identità disponga dell'accesso corretto (per conto di) per eseguire operazioni dei servizi di dominio come la creazione o l'eliminazione di unità organizzative nel dominio di Azure Active Directory Domain Services.

Dopo aver creato l'identità gestita e aver assegnato il ruolo corretto, l'amministratore di Azure Active Directory Domain Services può configurare gli utenti che possono usare questa identità. Per configurare gli utenti, l'amministratore deve selezionare l'identità gestita nel portale e quindi fare clic su **Controllo di accesso (IAM)** in **Panoramica**. Assegnare quindi, a destra, il ruolo **Operatore di identità gestite** agli utenti o ai gruppi che vogliono creare cluster HDInsight ESP. Ad esempio, l'amministratore di Azure Active Directory Domain Services può assegnare questo ruolo al gruppo "MarketingTeam" per l'identità gestita "sjmsi", come illustrato nell'immagine seguente. Ciò garantisce che l'accesso per l'uso di questa identità gestita con la finalità di creare i cluster ESP sia concesso solo alle persone idonee.

![Assegnazione del ruolo Operatore di identità gestite di HDInsight](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-managed-identity-operator-role-assignment.png)

## <a name="networking-considerations"></a>Considerazioni sulla rete

> [!NOTE]  
> È necessario distribuire Azure Active Directory Domain Services in una rete virtuale basata su Azure Resource Manager. Le reti virtuali classiche non sono supportate per Azure Active Directory Domain Services. Per altre informazioni, vedere [Abilitare Azure Active Directory Domain Services tramite il portale di Azure](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started-network).

Dopo aver abilitato Azure Active Directory Domain Services, un server DNS (Domain Name Service) locale viene eseguito nelle macchine virtuali (VM) AD. Configurare la rete virtuale (VNET) di Azure Active Directory Domain Services per usare i server DNS personalizzati. Per individuare gli indirizzi IP corretti, selezionare **Proprietà** nella categoria **Gestisci** e osservare gli indirizzi IP elencati sotto **Indirizzo IP nella rete virtuale**.

![Individuare gli indirizzi IP per i server DNS locali](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-dns.png)

Modificare la configurazione dei server DNS nella rete virtuale di Azure Active Directory Domain Services per usare gli indirizzi IP personalizzati selezionando **Server DNS** nella categoria **Impostazioni**. Fare quindi clic sul pulsante di opzione accanto a **Personalizzato**, immettere il primo indirizzo IP nella casella di testo seguente e fare clic su **Salva**. Aggiungere altri indirizzi IP seguendo la stessa procedura.

![Aggiornamento della configurazione DNS della rete virtuale](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-vnet-configuration.png)

È più semplice posizionare sia l'istanza Azure AD-DS, sia il cluster HDInsight nella stessa rete virtuale di Azure. Se si prevede di usare reti virtuali diverse, è necessario eseguire il peering di tali reti in modo che il controller di dominio sia visibile alle VM HDI. Per altre informazioni, vedere [Peering di rete virtuale](../../virtual-network/virtual-network-peering-overview.md). 

Dopo aver eseguito il peering delle reti virtuali, configurare la rete virtuale HDInsight per l'uso di un server DNS personalizzato e inserire gli indirizzi IP privati di Azure Active Directory Domain Services come indirizzi di server DNS. Quando entrambe le reti virtuali usano gli stessi server DNS, il nome del dominio personalizzato verrà risolto nell'indirizzo IP corretto e sarà raggiungibile da HDInsight. Se ad esempio il nome di dominio è "contoso.com", eseguendo il ping al termine della procedura questo nome viene risolto nell'indirizzo IP corretto di Azure Active Directory Domain Services. 

![Configurazione dei server DNS personalizzati per la rete virtuale di cui è stato eseguito il peering](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-peered-vnet-configuration.png)

Se si usano regole dei gruppi di sicurezza di rete nella subnet HDInsight, è consigliabile consentire gli [indirizzi IP necessari](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-extend-hadoop-virtual-network#hdinsight-ip-1) per il traffico in entrata e in uscita. 

**Per testare** se la rete è configurata correttamente, aggiungere una macchina virtuale Windows alla rete virtuale/subnet HDInsight, eseguire il ping del nome di dominio (deve essere risolto in un indirizzo IP) e quindi eseguire **ldp.exe** per accedere al dominio di Azure Active Directory Domain Services. Infine, **aggiungere la VM Windows al dominio per verificare** che tutte le chiamate RPC necessarie tra il client e il server abbiano esito positivo. È anche possibile usare **nslookup** per verificare l'accesso di rete all'account di archiviazione o a qualsiasi database esterno in uso, ad esempio un database Ranger o un metastore Hive esterno.
Verificare che tutte le [porte necessarie](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd772723(v=ws.10)#communication-to-domain-controllers) siano presenti nell'elenco degli elementi consentiti nelle regole del gruppo di sicurezza di rete della subnet di Azure Active Directory Domain Services, nel caso in cui Domain Services sia protetto da un gruppo di sicurezza di rete. Se l'aggiunta al dominio di questa macchina virtuale Windows ha esito positivo, passare al passaggio successivo e creare i cluster ESP.

## <a name="create-a-hdinsight-cluster-with-esp"></a>Creazione di un cluster HDInsight con ESP

Dopo aver configurato i passaggi precedenti in modo corretto, il passaggio successivo consiste nel creare il cluster HDInsight con ESP abilitato. Quando si crea un cluster HDInsight, è possibile abilitare Enterprise Security Package nella scheda **personalizzata**. Se si preferisce usare un modello di Azure Resource Manager per la distribuzione, accedere al portale una sola volta e scaricare il modello precompilato disponibile nell'ultima pagina "Riepilogo" da conservare per un riutilizzo futuro.

![Convalida del dominio HDInsight Enterprise Security Package di Azure](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-create-cluster-esp-domain-validate.png)

Una volta abilitato ESP, gli errori di configurazione comuni relativi ad Azure AD-DS verranno automaticamente rilevati e convalidati. Dopo aver risolto questi errori, procedere con il passaggio successivo: 

![Convalida del dominio del pacchetto di protezione di Azure HDInsight Enterprise non riuscita](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-create-cluster-esp-domain-validate-failed.png)

Quando si crea un cluster HDInsight con ESP, è necessario fornire i seguenti parametri:

- **Utente amministratore del cluster**: scegliere un amministratore per il cluster da Azure AD-DS sincronizzato. Questo account di dominio deve essere già sincronizzato e disponibile in Azure Active Directory Domain Services.

- **Gruppi di accesso al cluster**: i gruppi di sicurezza i cui utenti si intende sincronizzare e far accedere al cluster devono essere disponibili in Azure Active Directory Domain Services. Ad esempio, il gruppo HiveUsers. Per altre informazioni, vedere [Creare un gruppo e aggiungere membri in Azure Active Directory](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

- **URL LDAPS**: un esempio è ldaps://contoso.com:636.

Lo screenshot seguente mostra una configurazione corretta nel portale di Azure:

![Configurazione Active Directory Domain Services di HDInsight Azure con ESP](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-domain-joined-configuration-azure-aads-portal.png).

L'identità gestita creata può essere scelta nell'elenco a discesa Identità gestita assegnata dall'utente quando si crea un nuovo cluster.

![Configurazione Active Directory Domain Services di HDInsight Azure con ESP](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-identity-managed-identity.png).


## <a name="next-steps"></a>Passaggi successivi
* Per configurare i criteri Hive ed eseguire query Hive, vedere [Configurare criteri Apache Hive per cluster HDInsight con ESP](apache-domain-joined-run-hive.md).
* Per usare SSH per la connessione a cluster HDInsight con ESP, vedere [Usare SSH con Apache Hadoop basato su Linux in HDInsight da Linux, Unix o OS X](../hdinsight-hadoop-linux-use-ssh-unix.md#domainjoined).
