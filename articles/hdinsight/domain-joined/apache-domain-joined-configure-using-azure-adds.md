---
title: Enterprise Security Package con Azure Active Directory in HDInsight
description: Informazioni su come impostare e configurare un cluster HDInsight Enterprise Security Package usando Azure Active Directory Domain Services.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: seodec18
ms.date: 10/02/2019
ms.openlocfilehash: 5989aca2b577621c31fe486877ea006cb25d47b5
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/08/2019
ms.locfileid: "72030372"
---
# <a name="enterprise-security-package-configurations-with-azure-active-directory-domain-services-in-hdinsight"></a>Enterprise Security Package configurazioni con Azure Active Directory Domain Services in HDInsight

I cluster Enterprise Security Package (ESP) offrono un accesso multiutente sui cluster HDInsight di Azure. I cluster HDInsight con ESP sono collegati a un dominio, in modo che gli utenti del dominio possano usare le proprie credenziali per effettuare l'autenticazione con i cluster ed eseguire processi di Big Data.

Questo articolo illustra come configurare un cluster HDInsight con ESP usando Azure Active Directory Domain Services (Azure AD DS).

> [!NOTE]  
> ESP è disponibile a livello generale in HDInsight 3,6 e 4,0 per i tipi di cluster: Apache Spark, Interactive, Hadoop e HBase. ESP per il tipo di cluster Apache Kafka è in anteprima solo con il supporto per il massimo sforzo. I cluster ESP creati prima della data GA di ESP (1 ottobre 2018) non sono supportati.

## <a name="enable-azure-ad-ds"></a>Abilitare Azure AD DS

> [!NOTE]  
> Solo gli amministratori dei tenant hanno i privilegi necessari per abilitare Azure AD DS. Se l'archiviazione cluster è Azure Data Lake Storage (ADLS) Gen1 o Gen2, è necessario disabilitare Multi-Factor Authentication (multi-factor authentication) solo per gli utenti che dovranno accedere al cluster usando le autenticazioni Kerberos di base. È possibile usare gli [indirizzi IP attendibili](../../active-directory/authentication/howto-mfa-mfasettings.md#trusted-ips) o [l'accesso condizionale](../../active-directory/conditional-access/overview.md) per disabilitare l'autenticazione a più fattori per utenti specifici solo quando accedono all'intervallo IP VNET del cluster HDInsight. Se si usa l'accesso condizionale, assicurarsi che l'endpoint del servizio Active Directory in sia abilitato in HDInsight VNET.
>
> Se l'archiviazione del cluster viene eseguita in Archiviazione BLOB di Azure (WASB), non disabilitare MFA.

L'abilitazione di Azure Active Directory Domain Services è un prerequisito per la creazione di un cluster HDInsight con ESP. Per altre informazioni vedere [Abilitare Azure Active Directory Domain Services tramite il portale di Azure](../../active-directory-domain-services/tutorial-create-instance.md). 

Quando Azure Active Directory Domain Services è abilitato, tutti gli utenti e gli oggetti avviano la sincronizzazione da Azure Active Directory (AAD) ad Azure Active Directory Domain Services per impostazione predefinita. La durata dell'operazione di sincronizzazione dipende dal numero di oggetti in Azure AD. La sincronizzazione potrebbe richiedere alcuni giorni per centinaia di migliaia di oggetti. 

Per usare HDInsight, il nome di dominio usato con Azure AD-DS deve essere di 39 caratteri o meno.

È possibile scegliere di sincronizzare solo i gruppi che devono accedere ai cluster HDInsight. Questa opzione di sincronizzazione che coinvolge solo determinati gruppi è chiamata *sincronizzazione con ambito*. Per istruzioni, vedere [Configurare la sincronizzazione con ambito da Azure AD al dominio gestito](../../active-directory-domain-services/scoped-synchronization.md).

Quando si abilita LDAP sicuro, inserire il nome di dominio nel nome del soggetto e il nome alternativo del soggetto nel certificato. Se, ad esempio, il nome di dominio è *contoso100.onmicrosoft.com*, verificare che nel nome del soggetto del certificato e nel nome alternativo del soggetto sia presente il nome esatto. Per altre informazioni, vedere [Configurare l'accesso LDAP sicuro (LDAPS) per un dominio gestito di Azure AD-DS](../../active-directory-domain-services/tutorial-configure-ldaps.md). Di seguito è riportato un esempio di creazione di un certificato autofirmato che presenta il nome di dominio *contoso100.onmicrosoft.com* sia nel nome del soggetto sia in DnsName (il nome alternativo del soggetto):

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

L'**identità gestita assegnata dall'utente** viene usata per semplificare e proteggere le operazioni dei servizi di dominio. Quando si assegna l'identità gestita al ruolo Collaboratore per Servizi di dominio HDInsight, tale identità può leggere, creare, modificare ed eliminare le operazioni dei servizi di dominio. Per la Enterprise Security Package HDInsight sono necessarie alcune operazioni di servizi di dominio, ad esempio la creazione di unità organizzative e di entità servizio. È possibile creare identità gestite in qualsiasi sottoscrizione. Per altre informazioni sulle identità gestite in generale, vedere [identità gestite per le risorse di Azure](../../active-directory/managed-identities-azure-resources/overview.md). Per altre informazioni sul funzionamento delle identità gestite in Azure HDInsight, vedere [identità gestite in Azure HDInsight](../hdinsight-managed-identities.md).

Per configurare i cluster ESP, creare un'identità gestita assegnata dall'utente se non è già disponibile. Per le istruzioni, vedere [Creare, elencare, eliminare o assegnare un ruolo a un'identità gestita assegnata dall'utente mediante il portale di Azure](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md). Assegnare quindi l'identità gestita al ruolo **Collaboratore per HDInsight Domain Services** nel controllo di accesso di Azure Active Directory Domain Services; per eseguire questa assegnazione di ruolo sono necessari i privilegi di amministratore di Azure Active Directory Domain Services.

![Controllo di accesso di Azure Active Directory Domain Services](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-configure-managed-identity.png)

L'assegnazione di un'identità gestita al ruolo **Collaboratore per HDInsight Domain Services** garantisce che l'identità disponga dell'accesso corretto (per conto di) per eseguire operazioni dei servizi di dominio come la creazione o l'eliminazione di unità organizzative nel dominio di Azure Active Directory Domain Services.

Dopo aver creato l'identità gestita e aver assegnato il ruolo corretto, l'amministratore di Azure Active Directory Domain Services può configurare gli utenti che possono usare questa identità. Per configurare gli utenti, l'amministratore deve selezionare l'identità gestita nel portale e quindi fare clic su **Controllo di accesso (IAM)** in **Panoramica**. Assegnare quindi, a destra, il ruolo **Operatore di identità gestite** agli utenti o ai gruppi che vogliono creare cluster HDInsight ESP. Ad esempio, l'amministratore di AAD-DS può assegnare questo ruolo al gruppo **MarketingTeam** per l'identità gestita di **sjmsi** , come illustrato nella figura seguente. Ciò garantisce che l'accesso per l'uso di questa identità gestita con la finalità di creare i cluster ESP sia concesso solo alle persone idonee.

![Assegnazione del ruolo Operatore di identità gestite di HDInsight](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-managed-identity-operator-role-assignment.png)

## <a name="networking-considerations"></a>Considerazioni sulla rete

> [!NOTE]  
> Azure AD-DS deve essere distribuito in un vNET basato su Azure Resource Manager. Le reti virtuali classiche non sono supportate per Azure Active Directory Domain Services. Per altre informazioni vedere [Abilitare Azure Active Directory Domain Services tramite il portale di Azure](../../active-directory-domain-services/tutorial-create-instance.md#create-and-configure-the-virtual-network).

Dopo aver abilitato Azure Active Directory Domain Services, un server DNS (Domain Name Service) locale viene eseguito nelle macchine virtuali (VM) AD. Configurare la rete virtuale (VNET) di Azure Active Directory Domain Services per usare i server DNS personalizzati. Per individuare gli indirizzi IP corretti, selezionare **Proprietà** nella categoria **Gestisci** e osservare gli indirizzi IP elencati sotto **Indirizzo IP nella rete virtuale**.

![Individuare gli indirizzi IP per i server DNS locali](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-dns1.png)

Modificare la configurazione dei server DNS nella rete virtuale di Azure Active Directory Domain Services per usare gli indirizzi IP personalizzati selezionando **Server DNS** nella categoria **Impostazioni**. Fare quindi clic sul pulsante di opzione accanto a **Personalizzato**, immettere il primo indirizzo IP nella casella di testo seguente e fare clic su **Salva**. Aggiungere altri indirizzi IP seguendo la stessa procedura.

![Aggiornamento della configurazione DNS della rete virtuale](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-vnet-configuration.png)

È più semplice posizionare sia l'istanza Azure AD-DS, sia il cluster HDInsight nella stessa rete virtuale di Azure. Se si prevede di usare reti virtuali diverse, è necessario eseguire il peering di tali reti in modo che il controller di dominio sia visibile alle VM HDI. Per altre informazioni, vedere [Peering di rete virtuale](../../virtual-network/virtual-network-peering-overview.md). 

Dopo aver eseguito il peering delle reti virtuali, configurare la rete virtuale HDInsight per l'uso di un server DNS personalizzato e inserire gli indirizzi IP privati di Azure Active Directory Domain Services come indirizzi di server DNS. Quando entrambe le reti virtuali usano gli stessi server DNS, il nome del dominio personalizzato verrà risolto nell'indirizzo IP corretto e sarà raggiungibile da HDInsight. Se, ad esempio, il nome di dominio è `contoso.com`, dopo questo passaggio `ping contoso.com` dovrebbe risolversi nell'IP Azure AD-DS destro.

![Configurazione dei server DNS personalizzati per la rete virtuale di cui è stato eseguito il peering](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-peered-vnet-configuration.png)

Se si usano regole gruppi di sicurezza di rete (NSG) nella subnet HDInsight, è necessario consentire gli [IP richiesti](../hdinsight-management-ip-addresses.md) per il traffico in ingresso e in uscita.

**Per testare** se la rete è configurata correttamente, aggiungere una macchina virtuale Windows alla rete virtuale/subnet HDInsight, eseguire il ping del nome di dominio (deve essere risolto in un indirizzo IP) e quindi eseguire **ldp.exe** per accedere al dominio di Azure Active Directory Domain Services. Infine, **aggiungere la VM Windows al dominio per verificare** che tutte le chiamate RPC necessarie tra il client e il server abbiano esito positivo. È anche possibile usare **nslookup** per verificare l'accesso di rete all'account di archiviazione o a qualsiasi database esterno in uso, ad esempio un database Ranger o un metastore Hive esterno.
Verificare che tutte le [porte necessarie](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd772723(v=ws.10)#communication-to-domain-controllers) siano presenti nell'elenco degli elementi consentiti nelle regole del gruppo di sicurezza di rete della subnet di Azure Active Directory Domain Services, nel caso in cui Domain Services sia protetto da un gruppo di sicurezza di rete. Se l'aggiunta al dominio di questa macchina virtuale Windows ha esito positivo, è possibile continuare con il passaggio successivo e creare cluster ESP.

## <a name="create-a-hdinsight-cluster-with-esp"></a>Creazione di un cluster HDInsight con ESP

Dopo aver configurato i passaggi precedenti in modo corretto, il passaggio successivo consiste nel creare il cluster HDInsight con ESP abilitato. Quando si crea un cluster HDInsight, è possibile abilitare Enterprise Security Package nella scheda **sicurezza e rete** . Se si preferisce usare un modello di Azure Resource Manager per la distribuzione, usare l'esperienza del portale una volta e scaricare il modello precompilato nella pagina **Verifica e crea** per riutilizzarlo in futuro.

> [!NOTE]  
> I primi sei caratteri dei nomi di cluster ESP devono essere univoci nell'ambiente in uso. Se, ad esempio, si hanno più cluster ESP in reti virtuali diverse, è necessario scegliere una convenzione di denominazione in grado di assicurare che i primi sei caratteri dei nomi di cluster siano univoci.

![Convalida del dominio HDInsight Enterprise Security Package di Azure](./media/apache-domain-joined-configure-using-azure-adds/azure-portal-cluster-security-networking-esp.png)

Una volta abilitato ESP, gli errori di configurazione comuni relativi ad Azure AD-DS verranno automaticamente rilevati e convalidati. Dopo la correzione di questi errori, è possibile procedere con il passaggio successivo:

![Convalida del dominio del pacchetto di protezione di Azure HDInsight Enterprise non riuscita](./media/apache-domain-joined-configure-using-azure-adds/azure-portal-cluster-security-networking-esp-error.png)

Quando si crea un cluster HDInsight con ESP, è necessario fornire i seguenti parametri:

- **Utente amministratore del cluster**: scegliere un amministratore per il cluster da Azure AD-DS sincronizzato. Questo account di dominio deve essere già sincronizzato e disponibile in Azure Active Directory Domain Services.

- **Gruppi di accesso al cluster**: i gruppi di sicurezza i cui utenti si intende sincronizzare e far accedere al cluster devono essere disponibili in Azure Active Directory Domain Services. Ad esempio, il gruppo HiveUsers. Per altre informazioni, vedere [Creare un gruppo e aggiungere membri in Azure Active Directory](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

- **URL LDAPS**: Un esempio è `ldaps://contoso.com:636`.

L'identità gestita creata può essere scelta nell'elenco a discesa Identità gestita assegnata dall'utente quando si crea un nuovo cluster.

![Identità gestita di Active Directory Domain Services ESP di Azure HDInsight](./media/apache-domain-joined-configure-using-azure-adds/azure-portal-cluster-security-networking-identity.png).

## <a name="next-steps"></a>Passaggi successivi

* Per configurare i criteri Hive ed eseguire query Hive, vedere [Configurare criteri Apache Hive per cluster HDInsight con ESP](apache-domain-joined-run-hive.md).
* Per usare SSH per la connessione a cluster HDInsight con ESP, vedere [Usare SSH con Apache Hadoop basato su Linux in HDInsight da Linux, Unix o OS X](../hdinsight-hadoop-linux-use-ssh-unix.md#domainjoined).
