---
title: Configurare i cluster HDInsight aggiunti al dominio usando AAD-DS
description: Informazioni su come configurare i cluster HDInsight aggiunti al dominio usando Azure Active Directory Domain Services
services: hdinsight
author: omidm1
manager: jhubbard
editor: cgronlun
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/17/2018
ms.author: omidm
ms.openlocfilehash: 060ca8040f514ec1df48c2ca4568cbbb2a529267
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/19/2018
---
# <a name="configure-domain-joined-hdinsight-clusters-using-azure-active-directory-domain-services"></a>Configurare cluster HDInsight aggiunti al dominio usando Azure Active Directory Domain Services

I cluster aggiunti al dominio offrono alle aziende con più utenti funzionalità di sicurezza in HDInsight. I cluster HDInsight aggiunti al dominio sono collegati ai domini di Active Directory, così che gli utenti del dominio possano usare le proprie credenziali per effettuare l'autenticazione con i cluster ed eseguire lavori con big data. 

In questo articolo sono contenute informazioni su come configurare un cluster HDInsight aggiunto al dominio usando Azure Active Directory Domain Services.

> [!NOTE]
> Active Directory in macchine virtuali IaaS di Azure non è più supportata.

## <a name="create-azure-adds"></a>Creazione di Azure Active Directory Domain Services

Prima di poter creare un cluster HDInsight, è necessario creare un Azure Active Directory Domain Services. Per creare un Azure Active Directory Domain Services, vedere [Abilitare Azure Active Directory Domain Services tramite il portale di Azure](../../active-directory-domain-services/active-directory-ds-getting-started.md). 

> [!NOTE]
> Solo l'amministratore tenant dispone dell'autorizzazione a creare servizi di dominio. Se si usa l'archiviazione di Azure Data Lake come risorsa di archiviazione predefinita per HDInsight, verificare che il tenant di Azure AD predefinito per l'archiviazione di Azure Data Lake sia lo stesso del dominio per il cluster di HDInsight. Per assicurare il funzionamento di questa configurazione con Azure Data Lake Store, è necessario disabilitare l'autenticazione a più fattori per gli utenti che avranno accesso al cluster.

Dopo aver sottoposto il servizio di dominio AAD a provisioning, sarà necessario creare un account del servizio in AAD, che verrà sincronizzato con AAD-DS, con le autorizzazioni necessarie per creare il cluster HDInsight. Se questo account del servizio esiste già, è necessario reimpostarne la password e attendere fino a quando non viene eseguita la sincronizzazione con AAD-DS. Questo ripristino comporterà la creazione dell'hash della password di Kerberos, che potrebbe richiedere fino a 30 minuti. Questo account del servizio deve disporre dei privilegi seguenti:

- Aggiunta di computer al dominio e posizionamento delle entità di sicurezza del computer all'interno dell'unità organizzativa specificata in fase di creazione del cluster.
- Creazione delle entità di servizio nell'unità organizzativa specificata in fase di creazione del cluster.

Abilitare LDAP sicuro (LDAPS) per un dominio gestito di Azure AD Domain Services. Per abilitare LDAP sicuro, vedere [Configurare l'accesso LDAP sicuro (LDAPS) per un dominio gestito di Azure AD Domain Services](../../active-directory-domain-services/active-directory-ds-admin-guide-configure-secure-ldap.md).

## <a name="create-a-domain-joined-hdinsight-cluster"></a>Creare un cluster HDInsight aggiunto al dominio

Il passaggio successivo è la creazione del cluster HDInsight usando Microsoft Azure Active Directory Domain Services e l'account del servizio creati nella sessione precedente.

È più semplice posizionare sia il servizio di dominio Azure AD sia il cluster HDInsight nella stessa rete virtuale di Azure. Nel caso si trovino in reti virtuali diverse, si dovrà eseguire il peering di entrambe le reti. Per altre informazioni, vedere [Peering di rete virtuale](../../virtual-network/virtual-network-peering-overview.md).

Quando si crea un cluster HDInsight aggiunto al dominio, è necessario fornire i seguenti parametri:

- **Nome di dominio**: il nome di dominio associato ad Azure Active Directory Domain Services. Per esempio, contoso.onmicrosoft.com
- **Nome utente del dominio**: l'account del servizio in Azure AD DC creato nella sezione precedente. Ad esempio, hdiadmin@contoso.onmicrosoft.com. Questo utente del dominio diventerà l'amministratore del cluster di HDInsight aggiunto al dominio.
- **Password del dominio**: la password dell'account di servizio.
- **Unità organizzativa**: immettere il nome distinto dell'unità organizzativa che si desidera usare con il cluster HDInsight. Per esempio: OU=HDInsightOU,DC=contoso,DC=onmicrosohift,DC=com. Se questa unità organizzativa non esiste, il cluster HDInsight tenterà di creare tale unità. 
- **URL LDAPS**: per esempio ldaps://contoso.onmicrosoft.com:636
- **Gruppo di utenti con accesso**: i gruppi di protezione di cui si desidera sincronizzare gli utenti con il cluster. Ad esempio, HiveUsers. Se si desidera specificare più gruppi di utenti, separarli con una virgola ",".
 
> [!NOTE]
> Dato che Apache Zeppelin usa il nome di dominio per autenticare l'account del servizio di amministrazione, l'account del servizio DEVE avere lo stesso nome di dominio del suffisso UPN di Apache Zeppelin per funzionare correttamente.
 
La schermata seguente mostra le configurazioni nel portale di Azure:

![Configurazione Active Directory Domain Services aggiunta al dominio di HDInsight Azure](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-domain-joined-configuration-azure-aads-portal.png).


## <a name="next-steps"></a>Passaggi successivi
* Per configurare i criteri ed eseguire query Hive, vedere [Configure Hive policies for Domain-joined HDInsight clusters](apache-domain-joined-run-hive.md) (Configurare criteri Hive per cluster HDInsight aggiunti al dominio).
* Per usare SSH per la connessione a cluster HDInsight aggiunti al dominio, vedere [Usare SSH con Hadoop basato su Linux in HDInsight da Linux, Unix o OS X](../hdinsight-hadoop-linux-use-ssh-unix.md#domainjoined).

