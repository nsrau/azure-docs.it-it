---
title: Configurare cluster HDInsight aggiunti al dominio usando Azure Active Directory Domain Services - Azure | Documentazione Microsoft
description: Informazioni su come configurare i cluster HDInsight aggiunti al dominio usando Azure Active Directory Domain Services
services: hdinsight
documentationcenter: 
author: saurinsh
manager: jhubbard
editor: cgronlun
tags: 
ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/10/2017
ms.author: saurinsh
ms.openlocfilehash: cf8532334f03f72691fa09e8dbdd02b78072cf38
ms.sourcegitcommit: 6a22af82b88674cd029387f6cedf0fb9f8830afd
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/11/2017
---
# <a name="configure-domain-joined-hdinsight-clusters-using-azure-active-directory-domain-services"></a>Configurare cluster HDInsight aggiunti al dominio usando Azure Active Directory Domain Services

I cluster aggiunti al dominio offrono alle aziende con più utenti funzionalità di sicurezza in HDInsight. I cluster HDInsight aggiunti al dominio sono collegati ai domini di Active Directory, così che gli utenti del dominio possano usare le proprie credenziali per effettuare l'autenticazione con i cluster ed eseguire lavori con big data. 

Esistono tre modi per configurare un controller di dominio in modo che i cluster HDInsight aggiunti al dominio possano connettersi a:

- Azure Active Directory Domain Services (Azure AD DS)
- Active Directory locale
- Controller di Dominio di Active Directory su macchine virtuali Azure IaaS

In questo articolo sono contenute informazioni su come configurare un cluster HDInsight aggiunto al dominio usando Azure Active Directory Domain Services.

## <a name="create-azure-adds"></a>Creazione di Azure Active Directory Domain Services

Prima di poter creare un cluster HDInsight, è necessario creare un Azure Active Directory Domain Services. Per creare un Azure Active Directory Domain Services, vedere [Abilitare Azure Active Directory Domain Services tramite il portale di Azure](../../active-directory-domain-services/active-directory-ds-getting-started.md). 

> [!NOTE]
> Solo l'amministratore tenant dispone dell'autorizzazione a creare servizi di dominio. 

Quando il servizio di dominio è stato sottoposto a provisioning, sarà necessario creare un account del servizio nel gruppo **di amministratori Azure Active Directory Domain Services** per creare il cluster HDInsight. L'account del servizio deve essere un amministratore globale in Azure AD.

## <a name="create-a-domain-joined-hdinsight-cluster"></a>Creare un cluster HDInsight aggiunto al dominio

Il passaggio successivo è la creazione del cluster HDInsight usando Microsoft Azure Active Directory Domain Services e l'account del servizio creati nella sessione precedente.

È più semplice posizionare sia il servizio di dominio Azure AD sia il cluster HDInsight nella stessa rete virtuale di Azure. Nel caso si trovino in reti virtuali diverse, si dovrà eseguire il peering di entrambe le reti. Per altre informazioni, vedere [Peering di rete virtuale](../../virtual-network/virtual-network-peering-overview.md).

Quando si crea un cluster HDInsight aggiunto al dominio, è necessario fornire i seguenti parametri:

- **Nome di dominio**: il nome di dominio associato ad Azure Active Directory Domain Services. Per esempio, contoso.onmicrosoft.com
- **Nome utente del dominio**: l'account del servizio nel gruppo di amministratori Azure Active Directory Domain Services creato nella sezione precedente. ad esempio hdiadmin@contoso.onmicrosoft.com. Questo utente del dominio è l'amministratore di questo cluster HDInsight.
- **Password del dominio**: la password dell'account di servizio.
- **Unità organizzativa**: immettere il nome distinto dell'unità organizzativa che si desidera usare con il cluster HDInsight. Per esempio: OU=HDInsightOU,DC=contoso,DC=onmicrosohift,DC=com. Se questa unità organizzativa non esiste, il cluster HDInsight tenterà di creare tale unità. 
- **URL LDAPS**: per esempio ldaps://contoso.onmicrosoft.com:636
- **Gruppo di utenti con accesso**: i gruppi di protezione di cui si desidera sincronizzare gli utenti con il cluster. Ad esempio, HiveUsers. Se si desidera specificare più gruppi di utenti, separarli con una virgola ",".
 
La schermata seguente mostra le configurazioni nel portale di Azure:

![Configurazione Active Directory Domain Services aggiunta al dominio di HDInsight Azure](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-domain-joined-configuration-azure-aads-portal.png).


## <a name="next-steps"></a>Passaggi successivi
* Per configurare i criteri ed eseguire query Hive, vedere [Configure Hive policies for Domain-joined HDInsight clusters](apache-domain-joined-run-hive.md) (Configurare criteri Hive per cluster HDInsight aggiunti al dominio).
* Per usare SSH per la connessione a cluster HDInsight aggiunti al dominio, vedere [Usare SSH con Hadoop basato su Linux in HDInsight da Linux, Unix o OS X](../hdinsight-hadoop-linux-use-ssh-unix.md#domainjoined).

