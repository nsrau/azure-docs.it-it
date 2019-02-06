---
title: Architettura di Azure HDInsight con Enterprise Security Package
description: Informazioni su come pianificare la sicurezza di HDInsight con Enterprise Security Package.
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: omidm
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 3e58c22048c9b71b00cffb0657fc924277304662
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/31/2019
ms.locfileid: "55462430"
---
# <a name="use-enterprise-security-package-in-hdinsight"></a>Uso di Enterprise Security Package in HDInsight

Il cluster Azure HDInsight standard è un cluster a utente singolo. Si tratta di un cluster adatto alla maggior parte delle aziende con team di piccole dimensioni che compilano carichi di lavoro di dati di grandi dimensioni. Ogni utente può creare un cluster dedicato diverso su richiesta ed eliminarlo quando non è più necessario. 

Molte aziende sono passate a un modello in cui i cluster sono gestiti dai team IT e condivisi tra più team delle applicazioni. Per queste aziende di grandi dimensioni è necessario l'accesso multiutente al cluster in Azure HDInsight.

HDInsight si basa su un provider di identità diffuso, Active Directory, in modo gestito. Grazie all'integrazione di HDInsight con [Azure Active Directory Domain Services (Azure Active Directory Domain Services)](../../active-directory-domain-services/active-directory-ds-overview.md), è possibile accedere ai cluster usando le credenziali di dominio. 

Le macchine virtuali (VM) in HDInsight sono parte del dominio specificato. Pertanto, tutti i servizi in esecuzione in HDInsight, come Apache Ambari, server Apache Hive, Apache Ranger, server Apache Spark Thrift e altri, funzionano senza problemi per l'utente autenticato. Gli amministratori possono quindi creare criteri di autorizzazione sicuri usando Apache Ranger per fornire il controllo di accesso basato sui ruoli per le risorse nel cluster.

## <a name="integrate-hdinsight-with-active-directory"></a>Integrare HDInsight con Active Directory

Apache Hadoop open source si basa su Kerberos per l'autenticazione e la sicurezza. Pertanto, i nodi del cluster HDInsight con Enterprise Security Package (ESP) vengono aggiunti a un dominio gestito da Azure Active Directory Domain Services. La sicurezza Kerberos è configurata per i componenti Hadoop nel cluster. 

Gli elementi seguenti vengono creati automaticamente:
- un'entità servizio per ogni componente Hadoop 
- un'entità computer per ogni computer aggiunto al dominio
- un'unità organizzativa (OU) per ogni cluster in cui archiviare le entità servizio e computer 

In sintesi, è necessario configurare un ambiente con gli elementi seguenti:

- Un dominio Active Directory (gestito tramite Azure Active Directory Domain Services).
- Secure LDAP (LDAPS) abilitato in Azure Active Directory Domain Services.
- Connettività di rete adeguata dalla rete virtuale HDInsight alla rete virtuale di Azure Active Directory Domain Services, se si scelgono reti virtuali separate. Una macchina virtuale all'interno della rete virtuale HDInsight dovrebbe avere una linea visiva verso Azure Active Directory Domain Services attraverso il peering di rete virtuale. Se HDInsight e Azure Active Directory Domain Services sono distribuiti nella stessa rete virtuale, la connettività viene stabilita automaticamente e non sono necessarie altre operazioni.

## <a name="set-up-different-domain-controllers"></a>Configurazione diversa dei controller di dominio
HDInsight attualmente supporta solo Azure Active Directory Domain Services come controller di dominio principale che il cluster userà per la comunicazione Kerberos. Ma sono possibili anche altre configurazioni complesse di Active Directory, purché tali configurazioni portino all'abilitazione di Azure Active Directory Domain Services per l'accesso a HDInsight.

### <a name="azure-active-directory-domain-services"></a>Servizi di dominio Azure Active Directory
[Azure Active Directory Domain Services](../../active-directory-domain-services/active-directory-ds-overview.md) fornisce un dominio gestito che è completamente compatibile con Windows Server Active Directory. Microsoft si occupa della gestione, dell'applicazione di patch e del monitoraggio del dominio AD in una configurazione a disponibilità elevata. È possibile distribuire il cluster senza doversi preoccupare di gestire i controller di dominio. 

Utenti, gruppi e password vengono sincronizzati da Azure Active Directory (Azure AD). La sincronizzazione unidirezionale dall'istanza di Azure AD per Azure Active Directory Domain Services consente agli utenti di accedere al cluster usando le stesse credenziali aziendali. 

Per altre informazioni, vedere [Configurare un cluster HDInsight con Enterprise Security Package usando Azure Active Directory Domain Services](./apache-domain-joined-configure-using-azure-adds.md).

### <a name="on-premises-active-directory-or-active-directory-on-iaas-vms"></a>Active Directory locale o Active Directory nelle macchine virtuali IaaS

Se si dispone di un'istanza di Active Directory locale o di configurazioni più complesse di Active Directory per il dominio, è possibile sincronizzare le identità in Azure AD tramite Azure AD Connect. È quindi possibile abilitare Azure Active Directory Domain Services in tale tenant di Active Directory. 

Poiché Kerberos si basa sugli hash delle password, è necessario [abilitare la sincronizzazione degli hash delle password in Azure Active Directory Domain Services](../../active-directory-domain-services/active-directory-ds-getting-started-password-sync.md). 

Se si usa la federazione con Active Directory Federation Services (ADFS), è necessario abilitare la sincronizzazione degli hash delle password (per una configurazione consigliata, vedere [qui](https://youtu.be/qQruArbu2Ew)) che risulta utile anche con il ripristino di emergenza in caso di errore dell'infrastruttura ADFS e di perdita della protezione delle credenziali. Per altre informazioni, vedere l'articolo [Abilitare la sincronizzazione degli hash delle password con la sincronizzazione di Azure AD Connect](../../active-directory/hybrid/how-to-connect-password-hash-synchronization.md). 

L'utilizzo di Active Directory locale o Active Directory sulle sole macchine virtuali IaaS, senza Azure AD e Azure Active Directory Domain Services, non è una configurazione supportata per i cluster HDInsight con ESP.

Se viene usata la federazione e gli hash delle password sono sincronizzati correttamente, ma si ricevono errori di autenticazione, verificare che l’autenticazione della password cloud dell’entità servizio powershell sia abilitata. In caso contrario, è necessario impostare un [criterio di individuazione area di autenticazione principale (HRD )](../../active-directory/manage-apps/configure-authentication-for-federated-users-portal.md) per il tenant AAD. Per verificare e impostare il criterio HRD:

 1. Installare il modulo Azure PowerShell

 ```
  Install-Module AzureAD
 ```

 2. ```Connect-AzureAD``` usando le credenziali di un amministratore globale (amministratore tenant)

 3. Controllare se è già stata creata l'entità servizio "Microsoft Azure Powershell"

```
 $powershellSPN = Get-AzureADServicePrincipal -SearchString "Microsoft Azure Powershell"
```

 4. Se non esiste (ad esempio se ($powershellSPN -q $null)), creare l'entità servizio

```
 $powershellSPN = New-AzureADServicePrincipal -AppId 1950a258-227b-4e31-a9cf-717495945fc2
```

 5. Creare e associare il criterio a questa entità servizio: 

```
 $policy = New-AzureADPolicy -Definition @("{`"HomeRealmDiscoveryPolicy`":{`"AllowCloudPasswordValidation`":true}}") -DisplayName EnableDirectAuth -Type HomeRealmDiscoveryPolicy

 Add-AzureADServicePrincipalPolicy -Id $powershellSPN.ObjectId -refObjectID $policy.ID
```

## <a name="next-steps"></a>Passaggi successivi

* [Configurare i cluster HDInsight con ESP](apache-domain-joined-configure-using-azure-adds.md)
* [Configurare i criteri Apache Hive per i cluster HDInsight con ESP](apache-domain-joined-run-hive.md)
* [Gestire i cluster HDInsight con ESP](apache-domain-joined-manage.md) 
