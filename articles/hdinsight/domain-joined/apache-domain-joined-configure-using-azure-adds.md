---
title: Configurare un cluster HDInsight aggiunto al dominio con Azure Active Directory Domain Services
description: Informazioni su come configurare un cluster HDInsight aggiunto al dominio usando Azure Active Directory Domain Services
services: hdinsight
author: omidm1
ms.author: omidm
manager: jhubbard
editor: cgronlun
ms.service: hdinsight
ms.topic: conceptual
ms.date: 07/17/2018
ms.openlocfilehash: 45cb9590e6dd0d8260f6e63b80caeca894f0fd44
ms.sourcegitcommit: b9786bd755c68d602525f75109bbe6521ee06587
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/18/2018
ms.locfileid: "39126035"
---
# <a name="configure-a-domain-joined-hdinsight-cluster-by-using-azure-active-directory-domain-services"></a>Configurare un cluster HDInsight aggiunto al dominio con Azure Active Directory Domain Services

I cluster aggiunti a un dominio offrono l'accesso multiutente ai cluster Azure HDInsight. I cluster HDInsight aggiunti a un dominio sono collegati al dominio, in modo che gli utenti del dominio possano usare le proprie credenziali per effettuare l'autenticazione ai cluster ed eseguire processi di Big Data. 

Questo articolo illustra come configurare un cluster HDInsight aggiunto al dominio usando Azure Active Directory Domain Services (Azure AD DS).

## <a name="enable-azure-ad-ds"></a>Abilitare Azure AD DS

L'abilitazione di Azure AD DS è un prerequisito per la creazione di un cluster HDInsight aggiunto al dominio. Per altre informazioni, vedere [Abilitare Azure Active Directory Domain Services tramite il portale di Azure](../../active-directory-domain-services/active-directory-ds-getting-started.md). 

> [!NOTE]
> Solo gli amministratori dei tenant hanno i privilegi necessari per la creazione di un'istanza di Azure AD DS. Se si usa Azure Data Lake Storage Gen1 come risorsa di archiviazione predefinita per HDInsight, verificare che il tenant di Azure AD predefinito per Azure Data Lake Storage Gen1 sia lo stesso del dominio per il cluster HDInsight. Poiché Hadoop si basa su Kerberos e sull'autenticazione di base, è necessario disabilitare l'autenticazione a più fattori per gli utenti che avranno accesso al cluster.

Dopo il provisioning dell'istanza di Azure AD DS, creare un account del servizio in Azure Active Directory (Azure AD) con le autorizzazioni appropriate. Se questo account del servizio esiste già, reimpostarne la password e attendere fino alla sincronizzazione con Azure AD DS. Questo ripristino comporterà la creazione dell'hash della password Kerberos e può richiedere fino a 30 minuti per la sincronizzazione con Azure AD DS. 

L'account del servizio deve avere i privilegi seguenti:

- Aggiunta di computer al dominio e posizionamento delle entità di sicurezza del computer all'interno dell'unità organizzativa specificata in fase di creazione del cluster.
- Creazione delle entità di servizio nell'unità organizzativa specificata in fase di creazione del cluster.

> [!NOTE]
> Dato che Apache Zeppelin usa il nome di dominio per autenticare l'account del servizio di amministrazione, l'account del servizio *deve* avere lo stesso nome di dominio del suffisso UPN per consentire ad Apache Zeppelin di funzionare correttamente.

Per altre informazioni sulle unità organizzative e sulla relativa gestione, vedere [Create an OU on an Azure AD DS managed domain](../../active-directory-domain-services/active-directory-ds-admin-guide-create-ou.md) (Creare un'unità organizzativa in un dominio gestito di Azure AD DS). 

LDAP è un protocollo sicuro per un dominio gestito di Azure AD DS. Per altre informazioni, vedere [Configurare l'accesso LDAP sicuro (LDAPS) per un dominio gestito di Azure AD DS](../../active-directory-domain-services/active-directory-ds-admin-guide-configure-secure-ldap.md).

## <a name="create-a-domain-joined-hdinsight-cluster"></a>Creare un cluster HDInsight aggiunto al dominio

Il passaggio successivo consiste nella creazione del cluster HDInsight usando Azure AD DS e l'account del servizio creato nella sessione precedente.

È più semplice posizionare sia l'istanza Azure AD DS, sia il cluster HDInsight nella stessa rete virtuale di Azure. Se si sceglie di posizionarli in reti virtuali diverse, è necessario eseguire il peering delle reti virtuali per consentire alle macchine virtuali HDInsight di avere la visibilità del controller di dominio per aggiungere le macchine virtuali. Per altre informazioni, vedere [Peering di rete virtuale](../../virtual-network/virtual-network-peering-overview.md).

Quando si crea un cluster HDInsight aggiunto al dominio, è necessario fornire i seguenti parametri:

- **Nome di dominio**: nome di dominio associato ad Azure AD DS. Un esempio è contoso.onmicrosoft.com.
- **Nome utente del dominio**: account del servizio nel dominio gestito del controller di dominio di Azure AD DS creato nella sezione precedente. Un esempio è hdiadmin@contoso.onmicrosoft.com. Questo utente del dominio diventerà l'amministratore del cluster HDInsight.
- **Password del dominio**: password dell'account di servizio.
- **Unità organizzativa**: nome distinto dell'unità organizzativa (OU) da usare con il cluster HDInsight. Un esempio è OU=HDInsightOU,DC=contoso,DC=onmicrosoft,DC=com. Se questa unità organizzativa non esiste, il cluster HDInsight prova a creare l'unità organizzativa usando i privilegi dell'account del servizio. Ad esempio, se l'account del servizio è nel gruppo di amministratori di Azure AD DS, ha le autorizzazioni appropriate per creare un'unità organizzativa. In caso contrario, può essere necessario creare innanzitutto l'unità organizzativa e fornire il controllo completo dell'account del servizio per tale unità organizzativa. Per altre informazioni, vedere [Creare un'unità organizzativa in un dominio gestito di Azure AD DS](../../active-directory-domain-services/active-directory-ds-admin-guide-create-ou.md).

    > [!IMPORTANT]
    > Includere tutti i controller di dominio separati da virgola dopo l'unità organizzativa (OU), ad esempio OU=HDInsightOU,DC=contoso,DC=onmicrosoft,DC=com.

- **URL LDAPS**: un esempio è ldaps://contoso.onmicrosoft.com:636.

    > [!IMPORTANT]
    > Immettere l'URL completo, con "ldaps://" e numero di porta (:636).

- **Gruppo di utenti con accesso**: gruppi di protezione di cui si vogliono sincronizzare gli utenti con il cluster. Ad esempio, HiveUsers. Per specificare più gruppi di utenti, separarli con un punto e virgola (;).
 
La schermata seguente mostra le configurazioni nel portale di Azure:

![Configurazione Active Directory Domain Services aggiunta al dominio di HDInsight Azure](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-domain-joined-configuration-azure-aads-portal.png).


## <a name="next-steps"></a>Passaggi successivi
* Per configurare i criteri Hive ed eseguire query Hive, vedere [Configurare criteri Hive per cluster HDInsight aggiunti al dominio](apache-domain-joined-run-hive.md).
* Per usare SSH per la connessione a cluster HDInsight aggiunti al dominio, vedere [Usare SSH con Hadoop basato su Linux in HDInsight da Linux, Unix o OS X](../hdinsight-hadoop-linux-use-ssh-unix.md#domainjoined).

