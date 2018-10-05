---
title: Configurare un cluster HDInsight con Enterprise Security Package usando Azure Active Directory Domain Services
description: Informazioni su come impostare e configurare un cluster HDInsight Enterprise Security Package usando Azure Active Directory Domain Services.
services: hdinsight
ms.service: hdinsight
author: omidm1
ms.author: omidm
ms.reviewer: jasonh
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: eb24aa0471604696de99f4878baef764cfef0a8b
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/27/2018
ms.locfileid: "47408355"
---
# <a name="configure-a-hdinsight-cluster-with-enterprise-security-package-by-using-azure-active-directory-domain-services"></a>Configurare un cluster HDInsight con Enterprise Security Package usando Azure Active Directory Domain Services

I cluster Enterprise Security Package (ESP) offrono un accesso multiutente sui cluster HDInsight di Azure. I cluster HDInsight con ESP sono collegati a un dominio, in modo che gli utenti del dominio possano usare le proprie credenziali per effettuare l'autenticazione con i cluster ed eseguire processi di Big Data. 

Questo articolo illustra come configurare un cluster HDInsight con ESP usando Azure Active Directory Domain Services (Azure AD DS).

>[!NOTE]
>ESP è disponibile a livello generale in HDI 3.6 per Hadoop, Spark e Interactive. ESP per tipi di cluster HBase e Kafka è disponibile in anteprima.

## <a name="enable-azure-ad-ds"></a>Abilitare Azure AD DS

L'abilitazione di Azure AD DS è un prerequisito per la creazione di un cluster HDInsight con ESP. Per altre informazioni vedere [Abilitare Azure Active Directory Domain Services tramite il portale di Azure](../../active-directory-domain-services/active-directory-ds-getting-started.md). 

Quando Azure Active Directory Domain Services è abilitato, tutti gli utenti e gli oggetti avviano la sincronizzazione da Azure Active Directory (AAD) ad Azure Active Directory Domain Services per impostazione predefinita. La durata dell'operazione di sincronizzazione dipende dal numero di oggetti in AAD. La sincronizzazione potrebbe richiedere alcuni giorni per centinaia di migliaia di oggetti. 

I clienti possono scegliere di sincronizzare solo i gruppi che devono accedere ai cluster HDInsight. Questa opzione di sincronizzazione che coinvolge solo determinati gruppi è chiamata *sincronizzazione con ambito*. Per istruzioni, vedere [Configurare la sincronizzazione con ambito da Azure AD al dominio gestito](https://docs.microsoft.com/en-us/azure/active-directory-domain-services/active-directory-ds-scoped-synchronization).

> [!NOTE]
> Solo gli amministratori dei tenant hanno i privilegi necessari per la creazione di un'istanza di Azure AD DS. L'autenticazione a più fattori deve essere disabilitata solo per gli utenti che accederanno al cluster.

Quando si abilita LDAP sicuro, inserire il nome di dominio nel nome del soggetto o il nome alternativo del soggetto nel certificato. Se ad esempio il nome di dominio è *contoso.com*, verificare che nel nome del soggetto del certificato o nel nome alternativo del soggetto sia presente il nome esatto. Per altre informazioni, vedere [Configurare l'accesso LDAP sicuro (LDAPS) per un dominio gestito di Azure AD-DS](../../active-directory-domain-services/active-directory-ds-admin-guide-configure-secure-ldap.md).

## <a name="check-aad-ds-health-status"></a>Controllare lo stato integrità di Azure Active Directory Domain Services

Visualizzare lo stato integrità di Azure Active Directory Domain Services selezionando **Integrità** nella categoria **Gestione**. Verificare che lo stato di Azure Active Directory Domain Services sia verde (in esecuzione) e che la sincronizzazione sia stata completata.

![Integrità di Azure Active Directory Domain Services](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-health.png)

## <a name="add-managed-identity"></a>Aggiungere identità gestita

Dopo che è stato abilitato Azure Active Directory Domain Services, creare un'identità gestita assegnata dall'utente e assegnarla al ruolo **HDInsight Domain Services Contributor** (Collaboratore Domain Services HDInsight) nel controllo di accesso di Azure Active Directory Domain Services.

![Controllo di accesso di Azure Active Directory Domain Services](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-configure-managed-identity.png)

L'assegnazione di un'identità gestita al ruolo **HDInsight Domain Services Contributor** (Collaboratore Domain Services HDInsight) garantisce che l'identità abbia l'accesso corretto per eseguire determinate operazioni di Domain Services sul dominio di Azure Active Directory Domain Services. Per altre informazioni, vedere [Che cosa sono le identità gestite per le risorse di Azure](../../active-directory/managed-identities-azure-resources/overview.md).

## <a name="create-a-hdinsight-cluster-with-esp"></a>Creazione di un cluster HDInsight con ESP

Il passaggio successivo consiste nel creare il cluster HDInsight con ESP abilitato usando Azure AD-DS.

È più semplice posizionare sia l'istanza Azure AD-DS, sia il cluster HDInsight nella stessa rete virtuale di Azure. Se si sceglie di posizionarli in reti virtuali diverse, è necessario eseguire il peering delle reti virtuali per consentire alle macchine virtuali HDInsight di avere la visibilità del controller di dominio per aggiungere le macchine virtuali. Per altre informazioni, vedere [Peering di rete virtuale](../../virtual-network/virtual-network-peering-overview.md). Per testare se il peering viene eseguito correttamente, aggiungere una macchina virtuale alla rete virtuale/subnet di HDInsight ed effettuare il ping del nome di dominio o eseguire **ldp.exe** per accedere al dominio di Azure Active Directory Domain Services.

Quando si crea un cluster HDInsight, è possibile abilitare Enterprise Security Package nella scheda personalizzata. 

![Sicurezza di Azure HDInsight e di rete](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-create-cluster-security-networking.png)

Una volta abilitato ESP, gli errori di configurazione comuni relativi ad Azure AD-DS verranno automaticamente rilevati e convalidati.

![Convalida del dominio HDInsight Enterprise Security Package di Azure](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-create-cluster-esp-domain-validate.png)

Rilevare in anticipo consente di risparmiare tempo perché permette di correggere gli errori prima di creare il cluster.

![Convalida del dominio del pacchetto di protezione di Azure HDInsight Enterprise non riuscita](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-create-cluster-esp-domain-validate-failed.png)

Quando si crea un cluster HDInsight con ESP, è necessario fornire i seguenti parametri:

- **Utente amministratore cluster**: scegliere un amministratore per il cluster da Azure AD-DS sincronizzato. Questo account deve essere già sincronizzato e disponibile in Azure Active Directory Domain Services.

- **Gruppi di accesso del cluster**: i gruppi di sicurezza di cui si vuole sincronizzare gli utenti con il cluster devono essere disponibili in Azure Active Directory Domain Services. Ad esempio, il gruppo HiveUsers. Per altre informazioni, vedere [Creare un gruppo e aggiungere membri in Azure Active Directory](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

- **URL LDAPS**: un esempio è ldaps://contoso.com:636.

La schermata seguente mostra le configurazioni corrette nel portale di Azure:

![Configurazione Active Directory Domain Services di HDInsight Azure con ESP](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-domain-joined-configuration-azure-aads-portal.png).

L'identità gestita creata può essere scelta nell'elenco a discesa Identità gestita assegnata dall'utente quando si crea un nuovo cluster.

![Configurazione Active Directory Domain Services di HDInsight Azure con ESP](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-identity-managed-identity.png).


## <a name="next-steps"></a>Passaggi successivi
* Per configurare i criteri Hive ed eseguire query Hive, vedere [Configurare criteri Hive per cluster HDInsight con ESP](apache-domain-joined-run-hive.md).
* Per usare SSH per la connessione a cluster HDInsight con ESP, vedere [Usare SSH con Hadoop basato su Linux in HDInsight da Linux, Unix o OS X](../hdinsight-hadoop-linux-use-ssh-unix.md#domainjoined).