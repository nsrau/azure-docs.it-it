---
title: Configurare un cluster HDInsight con Enterprise Security Package usando Azure Active Directory Domain Services
description: Informazioni su come impostare e configurare un cluster HDInsight Enterprise Security Package usando Azure Active Directory Domain Services
services: hdinsight
ms.service: hdinsight
author: omidm1
ms.author: omidm
ms.reviewer: jasonh
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: a5b377381fd540c2a9f1d85e0cb7edce32c2dae8
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/24/2018
ms.locfileid: "46968374"
---
# <a name="configure-a-hdinsight-cluster-with-enterprise-security-package-by-using-azure-active-directory-domain-services"></a>Configurare un cluster HDInsight con Enterprise Security Package usando Azure Active Directory Domain Services

I cluster Enterprise Security Package (ESP) offrono un accesso multiutente sui cluster HDInsight di Azure. I cluster HDInsight con ESP sono collegati a un dominio, in modo che gli utenti del dominio possano usare le proprie credenziali per effettuare l'autenticazione con i cluster ed eseguire processi di Big Data. 

Questo articolo illustra come configurare un cluster HDInsight con ESP usando Azure Active Directory Domain Services (Azure AD DS).

>[!NOTE]
>ESP è disponibile in HDI 3.6 + per Hadoop, Spark e Interactive. ESP per tipi di cluster HBase è disponibile in anteprima.


## <a name="enable-azure-ad-ds"></a>Abilitare Azure AD DS

L'abilitazione di Azure AD DS è un prerequisito per la creazione di un cluster HDInsight con ESP. Per altre informazioni vedere [Abilitare Azure Active Directory Domain Services tramite il portale di Azure](../../active-directory-domain-services/active-directory-ds-getting-started.md). 

> [!NOTE]
> Solo gli amministratori dei tenant hanno i privilegi necessari per la creazione di un'istanza di Azure AD DS. Se si usa Azure Data Lake Storage Gen1 come risorsa di archiviazione predefinita per HDInsight, verificare che il tenant di Azure AD predefinito per Azure Data Lake Storage Gen1 sia lo stesso del dominio per il cluster HDInsight. Poiché Hadoop si basa su Kerberos e sull'autenticazione di base, è necessario disabilitare l'autenticazione a più fattori per gli utenti che avranno accesso al cluster.

LDAP è un protocollo sicuro per un dominio gestito di Azure AD-DS. Quando si abilita LDAPS, inserire il nome di dominio nel nome del soggetto o il nome alternativo del soggetto nel certificato. Per altre informazioni, vedere [Configurare l'accesso LDAP sicuro (LDAPS) per un dominio gestito di Azure AD-DS](../../active-directory-domain-services/active-directory-ds-admin-guide-configure-secure-ldap.md).

## <a name="add-managed-identity"></a>Aggiungere identità gestita

Dopo che è stato abilitato Azure AD-DS, creare un'identità gestita e assegnarla al ruolo **HDInsight Domain Services Contributor** nel controllo di accesso di Azure AD-DS.

![Controllo di accesso di Azure Active Directory Domain Services](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-configure-managed-identity.png)

Per altre informazioni, vedere [Che cosa sono le identità gestite per le risorse di Azure](../../active-directory/managed-identities-azure-resources/overview.md).

## <a name="create-a-hdinsight-cluster-with-esp"></a>Creazione di un cluster HDInsight con ESP

Il passaggio successivo consiste nel creare il cluster HDInsight con ESP abilitato usando Azure AD-DS.

È più semplice posizionare sia l'istanza Azure AD-DS, sia il cluster HDInsight nella stessa rete virtuale di Azure. Se si sceglie di posizionarli in reti virtuali diverse, è necessario eseguire il peering delle reti virtuali per consentire alle macchine virtuali HDInsight di avere la visibilità del controller di dominio per aggiungere le macchine virtuali. Per altre informazioni, vedere [Peering di rete virtuale](../../virtual-network/virtual-network-peering-overview.md).

Quando si crea un cluster HDInsight, è possibile abilitare Enterprise Security Package per connettere il cluster con Azure AD-DS. 

![Sicurezza di Azure HDInsight e di rete](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-create-cluster-security-networking.png)

Una volta abilitato ESP, gli errori di configurazione comuni relativi ad Azure AD-DS verranno automaticamente rilevati e convalidati.

![Convalida del dominio HDInsight Enterprise Security Package di Azure](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-create-cluster-esp-domain-validate.png)

Rilevare in anticipo consente di risparmiare tempo perché permette di correggere gli errori prima di creare il cluster.

![Convalida del dominio del pacchetto di protezione di Azure HDInsight Enterprise non riuscita](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-create-cluster-esp-domain-validate-failed.png)

Quando si crea un cluster HDInsight con ESP, è necessario fornire i seguenti parametri:

- **Utente amministratore cluster**: scegliere un amministratore per il cluster da Azure AD-DS sincronizzato.

- **Gruppi di accesso del cluster**: i gruppi di sicurezza di cui si desidera sincronizzare gli utenti con il cluster devono essere sincronizzati e disponibili in Azure AD-DS. Ad esempio, HiveUsers. Per specificare più gruppi di utenti, separarli con un punto e virgola (;). I gruppi devono essere presenti nella directory prima del provisioning. Per altre informazioni, vedere [Creare un gruppo e aggiungere membri in Azure Active Directory](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md). Se il gruppo non esiste, si verifica un errore: "Gruppo HiveUsers non trovato in Active Directory".

- **URL LDAPS**: un esempio è ldaps://contoso.onmicrosoft.com:636.

    > [!IMPORTANT]
    > Immettere l'URL completo, con "ldaps://" e numero di porta (:636).

La schermata seguente mostra le configurazioni nel portale di Azure:

   ![Configurazione Active Directory Domain Services di HDInsight Azure con ESP](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-domain-joined-configuration-azure-aads-portal.png).


## <a name="next-steps"></a>Passaggi successivi
* Per configurare i criteri Hive ed eseguire query Hive, vedere [Configurare criteri Hive per cluster HDInsight con ESP](apache-domain-joined-run-hive.md).
* Per usare SSH per la connessione a cluster HDInsight con ESP, vedere [Usare SSH con Hadoop basato su Linux in HDInsight da Linux, Unix o OS X](../hdinsight-hadoop-linux-use-ssh-unix.md#domainjoined).

