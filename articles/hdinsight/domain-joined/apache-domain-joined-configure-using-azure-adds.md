---
title: Configurare i cluster HDInsight aggiunti al dominio usando AAD-DS
description: Informazioni su come configurare i cluster HDInsight aggiunti al dominio usando Azure Active Directory Domain Services
services: hdinsight
author: omidm1
manager: jhubbard
editor: cgronlun
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/30/2018
ms.author: omidm
ms.openlocfilehash: 8064940e0d0f035010a2521752d6f32f3f9ccd9f
ms.sourcegitcommit: 3c3488fb16a3c3287c3e1cd11435174711e92126
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/07/2018
ms.locfileid: "34849832"
---
# <a name="configure-domain-joined-hdinsight-clusters-using-azure-active-directory-domain-services"></a>Configurare cluster HDInsight aggiunti al dominio usando Azure Active Directory Domain Services

I cluster aggiunti a un dominio offrono l'accesso multiutente ai cluster HDInsight. I cluster HDInsight aggiunti a un dominio sono collegati a un dominio in modo che gli utenti del dominio possano usare le proprie credenziali per effettuare l'autenticazione nei cluster ed eseguire processi di Big Data. 

In questo articolo sono contenute informazioni su come configurare un cluster HDInsight aggiunto al dominio usando Azure Active Directory Domain Services.

## <a name="create-azure-adds"></a>Creazione di Azure Active Directory Domain Services

L'abilitazione di Azure AD Domain Services (AAD-DS) è un prerequisito per la creazione di un cluster HDInsight aggiunto a un dominio. Per abilitare un'istanza di AAD-DS, vedere [Abilitare Azure Active Directory Domain Services tramite il portale di Azure](../../active-directory-domain-services/active-directory-ds-getting-started.md). 

> [!NOTE]
> Solo gli amministratori dei tenant hanno i privilegi necessari per la creazione di un'istanza di AAD-DS. Se si usa l'archiviazione di Azure Data Lake come risorsa di archiviazione predefinita per HDInsight, verificare che il tenant di Azure AD predefinito per l'archiviazione di Azure Data Lake sia lo stesso del dominio per il cluster di HDInsight. Poiché Hadoop si basa su Kerberos e l'autenticazione di base, è necessario disabilitare l'autenticazione a più fattori per gli utenti che avranno accesso al cluster.

Dopo aver eseguito il provisioning dell'istanza di AAD-DS, è necessario creare un account del servizio in AAD, che verrà sincronizzato con AAD-DS, con le autorizzazioni appropriate. Se l'account del servizio esiste già, è necessario reimpostarne la password e attendere che venga eseguita la sincronizzazione con AAD-DS. Il ripristino comporterà la creazione dell'hash della password di Kerberos e la sincronizzazione con AAD-DS potrebbe richiedere fino a 30 minuti. L'account del servizio deve avere i privilegi seguenti:

- Aggiunta di computer al dominio e posizionamento delle entità di sicurezza del computer all'interno dell'unità organizzativa specificata in fase di creazione del cluster.
- Creazione delle entità di servizio nell'unità organizzativa specificata in fase di creazione del cluster.

> [!NOTE]
> Dato che Apache Zeppelin usa il nome di dominio per autenticare l'account del servizio di amministrazione, l'account del servizio DEVE avere lo stesso nome di dominio del suffisso UPN di Apache Zeppelin per funzionare correttamente.

Per altre informazioni sulle unità organizzative e sulla loro gestione, vedere [Creare un'unità organizzativa in AAD-DS](../../active-directory-domain-services/active-directory-ds-admin-guide-create-ou.md). 

L'accesso LDAP sicuro per il dominio gestito di AAD-DS è obbligatorio. Per abilitare l'accesso LDAP sicuro, vedere [Configurare l'accesso LDAP sicuro (LDAPS) per un dominio gestito di Azure AD Domain Services](../../active-directory-domain-services/active-directory-ds-admin-guide-configure-secure-ldap.md).

## <a name="create-a-domain-joined-hdinsight-cluster"></a>Creare un cluster HDInsight aggiunto al dominio

Il passaggio successivo è la creazione del cluster HDInsight usando AAD-DS e l'account del servizio creato nella sessione precedente.

Per semplicità, AAD-DS e il cluster HDInsight dovrebbero trovarsi nella stessa rete virtuale di Azure (VNet). Se si sceglie di posizionarli in reti virtuali diverse, è necessario eseguire il peering delle reti virtuali in modo che le macchine virtuali HDI abbiano la visibilità del controller di dominio per l'aggiunta delle macchine virtuali. Per altre informazioni, vedere [Peering di rete virtuale](../../virtual-network/virtual-network-peering-overview.md).

Quando si crea un cluster HDInsight aggiunto al dominio, è necessario fornire i seguenti parametri:

- **Nome di dominio**: il nome di dominio associato ad AAD-DS. Per esempio, contoso.onmicrosoft.com
- **Nome utente del dominio**: l'account del servizio nel dominio gestito creato nella sezione precedente. Ad esempio, hdiadmin@contoso.onmicrosoft.com. Questo utente del dominio diventerà l'amministratore del cluster HDInsight.
- **Password del dominio**: la password dell'account di servizio.
- **Unità organizzativa**: immettere il nome distinto dell'unità organizzativa che si desidera usare con il cluster HDInsight. Per esempio: OU=HDInsightOU,DC=contoso,DC=onmicrosohift,DC=com. Se questa unità organizzativa non esiste, il cluster HDInsight prova a creare l'unità organizzativa usando i privilegi dell'account del servizio. Ad esempio, se l'account del servizio si trova nel gruppo di amministratori di AAD-DS, l'account ha le autorizzazioni appropriate per creare un'unità organizzativa. In caso contrario, potrebbe essere necessario creare innanzitutto l'unità organizzativa e assegnare all'account del servizio il controllo completo dell'unità. Vedere [Creare un'unità organizzativa in AAD-DS](../../active-directory-domain-services/active-directory-ds-admin-guide-create-ou.md).

    > [!IMPORTANT]
    > È importante includere tutti i controller di dominio separati da virgola dopo l'unità organizzativa (OU), ad esempio OU=HDInsightOU,DC=contoso,DC=onmicrosohift,DC=com.

- **URL LDAPS**: per esempio ldaps://contoso.onmicrosoft.com:636

    > [!IMPORTANT]
    > Immettere l'URL completo, con ldaps:// e numero di porta (:636)

- **Gruppo di utenti con accesso**: i gruppi di protezione di cui si desidera sincronizzare gli utenti con il cluster. Ad esempio, HiveUsers. Se si desidera specificare più gruppi di utenti, separarli con una virgola ",".
 
La schermata seguente mostra le configurazioni nel portale di Azure:

![Configurazione Active Directory Domain Services aggiunta al dominio di HDInsight Azure](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-domain-joined-configuration-azure-aads-portal.png).


## <a name="next-steps"></a>Passaggi successivi
* Per configurare i criteri ed eseguire query Hive, vedere [Configure Hive policies for Domain-joined HDInsight clusters](apache-domain-joined-run-hive.md) (Configurare criteri Hive per cluster HDInsight aggiunti al dominio).
* Per usare SSH per la connessione a cluster HDInsight aggiunti al dominio, vedere [Usare SSH con Hadoop basato su Linux in HDInsight da Linux, Unix o OS X](../hdinsight-hadoop-linux-use-ssh-unix.md#domainjoined).

