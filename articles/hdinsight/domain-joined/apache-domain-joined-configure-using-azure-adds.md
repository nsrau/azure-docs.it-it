---
title: Sicurezza aziendale con Azure AD DS-Azure HDInsight
description: Informazioni su come configurare e configurare un cluster di Enterprise Security Package HDInsight usando Azure Active Directory Domain Services.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: seodec18,seoapr2020
ms.date: 04/17/2020
ms.openlocfilehash: 2b4756990162817087b0904a764b97526c3545d6
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82186652"
---
# <a name="enterprise-security-package-configurations-with-azure-active-directory-domain-services-in-hdinsight"></a>Enterprise Security Package configurazioni con Azure Active Directory Domain Services in HDInsight

I cluster Enterprise Security Package (ESP) forniscono l'accesso multiutente nei cluster HDInsight di Azure. I cluster HDInsight con ESP sono connessi a un dominio. Questa connessione consente agli utenti del dominio di utilizzare le proprie credenziali di dominio per l'autenticazione con i cluster e l'esecuzione di Big Data processi.

Questo articolo illustra come configurare un cluster HDInsight con ESP usando Azure Active Directory Domain Services (Azure AD DS).

> [!NOTE]  
> ESP è disponibile a livello generale in HDInsight 3,6 e 4,0 per questi tipi di cluster: Apache Spark, Interactive, Hadoop e HBase. ESP per il tipo di cluster Apache Kafka è in anteprima solo con il supporto per il massimo sforzo. I cluster ESP creati prima della data GA di ESP (1 ottobre 2018) non sono supportati.

## <a name="enable-azure-ad-ds"></a>Abilitare Azure AD DS

> [!NOTE]  
> Solo gli amministratori tenant dispongono dei privilegi per abilitare Azure AD DS. Se l'archiviazione del cluster è Azure Data Lake Storage Gen1 o Gen2, è necessario disabilitare Azure Multi-Factor Authentication solo per gli utenti che dovranno accedere al cluster usando l'autenticazione Kerberos di base.
>
> È possibile usare gli [indirizzi IP attendibili](../../active-directory/authentication/howto-mfa-mfasettings.md#trusted-ips) o [l'accesso condizionale](../../active-directory/conditional-access/overview.md) per disabilitare multi-factor authentication per utenti specifici *solo* quando accedono all'intervallo IP per la rete virtuale del cluster HDInsight. Se si usa l'accesso condizionale, assicurarsi che l'endpoint del servizio Active Directory sia abilitato nella rete virtuale HDInsight.
>
> Se l'archiviazione del cluster è archiviazione BLOB di Azure, non disabilitare Multi-Factor Authentication.

L'abilitazione di Azure AD DS è un prerequisito prima che sia possibile creare un cluster HDInsight con ESP. Per ulteriori informazioni, vedere [Enable Azure Active Directory Domain Services by using the portale di Azure](../../active-directory-domain-services/tutorial-create-instance.md).

Quando Azure AD DS è abilitato, tutti gli utenti e gli oggetti avviano la sincronizzazione da Azure Active Directory (Azure AD) a Azure AD DS per impostazione predefinita. La durata dell'operazione di sincronizzazione dipende dal numero di oggetti in Azure AD. La sincronizzazione potrebbe richiedere alcuni giorni per centinaia di migliaia di oggetti.

Il nome di dominio usato con Azure AD DS deve avere una lunghezza di 39 caratteri o meno, per lavorare con HDInsight.

È possibile scegliere di sincronizzare solo i gruppi che devono accedere ai cluster HDInsight. Questa opzione di sincronizzazione che coinvolge solo determinati gruppi è chiamata *sincronizzazione con ambito*. Per istruzioni, vedere [configurare la sincronizzazione con ambito da Azure ad al dominio gestito](../../active-directory-domain-services/scoped-synchronization.md).

Quando si Abilita LDAP sicuro, inserire il nome di dominio nel nome del soggetto. E il nome alternativo del soggetto nel certificato. Se il nome di dominio è *contoso100.onmicrosoft.com*, assicurarsi che esista il nome esatto nel nome del soggetto del certificato e il nome alternativo del soggetto. Per altre informazioni, vedere [Configurare l'accesso LDAP sicuro (LDAPS) per un dominio gestito di Azure AD DS](../../active-directory-domain-services/tutorial-configure-ldaps.md).

Nell'esempio seguente viene creato un certificato autofirmato. Il nome di dominio *contoso100.onmicrosoft.com* è in `Subject` entrambi (nome soggetto) `DnsName` e (nome alternativo del soggetto).

```powershell
$lifetime=Get-Date
New-SelfSignedCertificate -Subject contoso100.onmicrosoft.com `
  -NotAfter $lifetime.AddDays(365) -KeyUsage DigitalSignature, KeyEncipherment `
  -Type SSLServerAuthentication -DnsName *.contoso100.onmicrosoft.com, contoso100.onmicrosoft.com
```

## <a name="check-azure-ad-ds-health-status"></a>Controllare lo stato di integrità di Azure AD DS

Visualizzare lo stato di integrità di Azure Active Directory Domain Services selezionando **integrità** nella categoria **Gestisci** . Verificare che lo stato di Azure AD DS sia verde (in esecuzione) e che la sincronizzazione sia stata completata.

![Stato Azure AD DS](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-health.png)

## <a name="create-and-authorize-a-managed-identity"></a>Creare e autorizzare un'identità gestita

Utilizzare un' *identità gestita assegnata dall'utente* per semplificare le operazioni protette di servizi di dominio. Quando si assegna il ruolo **collaboratore servizi di dominio HDInsight** all'identità gestita, è possibile leggere, creare, modificare ed eliminare le operazioni di servizi di dominio.

Alcune operazioni di servizi di dominio, ad esempio la creazione di unità organizzative e di entità servizio, sono necessarie per HDInsight Enterprise Security Package. È possibile creare identità gestite in qualsiasi sottoscrizione. Per altre informazioni sulle identità gestite in generale, vedere [identità gestite per le risorse di Azure](../../active-directory/managed-identities-azure-resources/overview.md). Per altre informazioni sul funzionamento delle identità gestite in Azure HDInsight, vedere [identità gestite in Azure HDInsight](../hdinsight-managed-identities.md).

Per configurare i cluster ESP, creare un'identità gestita assegnata dall'utente se non ne è già presente uno. Vedere [`Create, list, delete, or assign a role to a user-assigned managed identity by using the Azure portal`](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md).

Assegnare quindi il ruolo di **collaboratore di servizi di dominio HDInsight** all'identità gestita in **controllo di accesso** per Azure AD DS. Per eseguire questa assegnazione di ruolo, è necessario disporre dei privilegi di amministratore di DS Azure AD.

![Controllo di accesso di Azure Active Directory Domain Services](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-configure-managed-identity.png)

L'assegnazione del ruolo di **collaboratore di servizi di dominio HDInsight** garantisce che questa`on behalf of`identità disponga dell'accesso () appropriato per le operazioni di servizi di dominio nel dominio Azure AD DS. Queste operazioni includono la creazione e l'eliminazione di unità organizzative.

Una volta assegnato il ruolo all'identità gestita, l'amministratore di Azure AD DS gestisce chi lo utilizza. In primo luogo, l'amministratore seleziona l'identità gestita nel portale. Quindi seleziona il **controllo di accesso (IAM)** in Overview ( **Panoramica**). L'amministratore assegna il ruolo di **operatore di identità gestito** a utenti o gruppi che desiderano creare cluster ESP.

Ad esempio, l'amministratore di Azure AD DS può assegnare questo ruolo al gruppo **MarketingTeam** per l'identità gestita di **sjmsi** . Nell'immagine seguente è illustrato un esempio. Questa assegnazione garantisce che le persone giuste nell'organizzazione possano usare l'identità gestita per creare cluster ESP.

![Assegnazione del ruolo Operatore di identità gestite di HDInsight](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-managed-identity-operator-role-assignment.png)

## <a name="network-considerations"></a>Considerazioni per la rete

> [!NOTE]  
> Azure AD DS deve essere distribuito in una rete virtuale basata su Azure Resource Manager. Le reti virtuali classiche non sono supportate per Azure AD DS. Per ulteriori informazioni, vedere [Enable Azure Active Directory Domain Services by using the portale di Azure](../../active-directory-domain-services/tutorial-create-instance-advanced.md#create-and-configure-the-virtual-network).

Abilita Azure AD Domain Services. Quindi, un server DNS (Local Domain Name System) viene eseguito nel Active Directory macchine virtuali (VM). Configurare la rete virtuale Azure AD DS per usare questi server DNS personalizzati. Per individuare gli indirizzi IP corretti, selezionare **Proprietà** nella categoria **Gestisci** e cercare in **indirizzo IP nella rete virtuale**.

![Individuare gli indirizzi IP per i server DNS locali](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-dns1.png)

Modificare la configurazione dei server DNS nella rete virtuale Azure AD DS. Per usare questi indirizzi IP personalizzati, selezionare **server DNS** nella categoria **Impostazioni** . Selezionare quindi l'opzione **personalizzata** , immettere il primo indirizzo IP nella casella di testo e selezionare **Salva**. Aggiungere altri indirizzi IP usando la stessa procedura.

![Aggiornamento della configurazione DNS della rete virtuale](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-vnet-configuration.png)

È più semplice posizionare sia l'istanza Azure AD DS, sia il cluster HDInsight nella stessa rete virtuale di Azure. Se si prevede di usare reti virtuali diverse, è necessario eseguire il peering delle reti virtuali in modo che il controller di dominio sia visibile alle VM HDInsight. Per altre informazioni, vedere [peering di rete virtuale](../../virtual-network/virtual-network-peering-overview.md).

Dopo aver eseguito il peering delle reti virtuali, configurare la rete virtuale HDInsight per l'uso di un server DNS personalizzato. E immettere gli indirizzi IP privati Azure AD DS come indirizzi del server DNS. Quando entrambe le reti virtuali usano gli stessi server DNS, il nome di dominio personalizzato verrà risolto nell'indirizzo IP corretto e sarà raggiungibile da HDInsight. Se, ad esempio, il nome di `contoso.com`dominio è, dopo questo passaggio `ping contoso.com` dovrebbe risolversi a destra Azure ad IP DS.

![Configurazione dei server DNS personalizzati per una rete virtuale con peering](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-peered-vnet-configuration.png)

Se si usano regole del gruppo di sicurezza di rete (NSG) nella subnet HDInsight, è necessario consentire gli [IP richiesti](../hdinsight-management-ip-addresses.md) per il traffico in ingresso e in uscita.

Per testare la configurazione della rete, aggiungere una VM Windows alla rete virtuale o alla subnet HDInsight ed effettuare il ping del nome di dominio. (Dovrebbe risolversi in un indirizzo IP). Eseguire **LDP. exe** per accedere al dominio Azure AD DS. Quindi, aggiungere questa macchina virtuale Windows al dominio per verificare che tutte le chiamate RPC richieste siano state eseguite tra il client e il server.

Usare **nslookup** per confermare l'accesso di rete all'account di archiviazione. O qualsiasi database esterno che è possibile usare, ad esempio External metastore Hive o DB Ranger. Verificare che le [porte richieste](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd772723(v=ws.10)#communication-to-domain-controllers) siano consentite nelle regole NSG della subnet Azure AD DS, se NSG protegge Azure AD DS. Se l'aggiunta al dominio di questa macchina virtuale Windows ha esito positivo, è possibile continuare con il passaggio successivo e creare cluster ESP.

## <a name="create-an-hdinsight-cluster-with-esp"></a>Creare un cluster HDInsight con ESP

Dopo aver configurato correttamente i passaggi precedenti, il passaggio successivo consiste nel creare il cluster HDInsight con ESP abilitato. Quando si crea un cluster HDInsight, è possibile abilitare Enterprise Security Package nella scheda **sicurezza e rete** . Per un modello di Azure Resource Manager per la distribuzione, usare l'esperienza del portale una volta. Scaricare quindi il modello precompilato nella pagina **Rivedi e crea** per un riutilizzo futuro.

È anche possibile abilitare la funzionalità [HDINSIGHT ID Broker](identity-broker.md) durante la creazione del cluster. La funzionalità ID Broker consente di accedere a Ambari usando Multi-Factor Authentication e ottenere i ticket Kerberos necessari senza che siano necessari hash delle password in Azure AD DS.

> [!NOTE]  
> I primi sei caratteri dei nomi di cluster ESP devono essere univoci nell'ambiente in uso. Se, ad esempio, sono presenti più cluster ESP in reti virtuali diverse, scegliere una convenzione di denominazione che assicuri che i primi sei caratteri nei nomi del cluster siano univoci.

![Convalida del dominio per Azure HDInsight Enterprise Security Package](./media/apache-domain-joined-configure-using-azure-adds/azure-portal-cluster-security-networking-esp.png)

Dopo l'abilitazione di ESP, le configurazioni comuni errate relative a Azure AD DS vengono rilevate e convalidate automaticamente. Dopo aver risolto questi errori, è possibile procedere con il passaggio successivo.

![Convalida del dominio di Azure HDInsight Enterprise Security Package non riuscita](./media/apache-domain-joined-configure-using-azure-adds/azure-portal-cluster-security-networking-esp-error.png)

Quando si crea un cluster HDInsight con ESP, è necessario specificare i parametri seguenti:

* **Utente amministratore cluster**: scegliere un amministratore per il cluster dall'istanza di Azure AD DS sincronizzata. Questo account di dominio deve essere già sincronizzato e disponibile in Azure AD DS.

* **Gruppi di accesso al cluster**: i gruppi di sicurezza i cui utenti si vuole sincronizzare e che hanno accesso al cluster devono essere disponibili in Azure AD DS. Un esempio è il gruppo HiveUsers. Per altre informazioni, vedere [Creare un gruppo e aggiungere membri in Azure Active Directory](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

* **URL LDAPS**: un esempio è `ldaps://contoso.com:636`.

L'identità gestita creata può essere scelta dall'elenco a discesa **identità gestita assegnata dall'utente** durante la creazione di un nuovo cluster.

![Identità gestita di Active Directory Domain Services ESP di Azure HDInsight](./media/apache-domain-joined-configure-using-azure-adds/azure-portal-cluster-security-networking-identity.png).

## <a name="next-steps"></a>Passaggi successivi

* Per configurare i criteri Hive ed eseguire query Hive, vedere [Configurare criteri Apache Hive per cluster HDInsight con ESP](apache-domain-joined-run-hive.md).
* Per usare SSH per la connessione a cluster HDInsight con ESP, vedere [Usare SSH con Apache Hadoop basato su Linux in HDInsight da Linux, Unix o OS X](../hdinsight-hadoop-linux-use-ssh-unix.md#authentication-domain-joined-hdinsight).
