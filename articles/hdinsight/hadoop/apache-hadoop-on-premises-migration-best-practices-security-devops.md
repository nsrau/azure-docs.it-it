---
title: Eseguire la migrazione di cluster Apache Hadoop locali in Azure HDInsight - Procedure consigliate per DevOps e sicurezza
description: Informazioni sulle procedure consigliate per DevOps e sicurezza relative alla migrazione di cluster Hadoop locali in Azure HDInsight.
services: hdinsight
author: hrasheed-msft
ms.reviewer: ashishth
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 10/25/2018
ms.author: hrasheed
ms.openlocfilehash: 9489d6e8780a30c5c54ee307d6c45c4bc2eb0e5d
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/31/2018
ms.locfileid: "50419283"
---
# <a name="migrate-on-premises-apache-hadoop-clusters-to-azure-hdinsight---security-and-devops-best-practices"></a>Eseguire la migrazione di cluster Apache Hadoop locali in Azure HDInsight - Procedure consigliate per DevOps e sicurezza

Questo articolo include consigli per la sicurezza e DevOps in sistemi Azure HDInsight. L'articolo fa parte di una serie di documenti che descrivono le procedure consigliate per facilitare la migrazione di sistemi Apache Hadoop locali ad Azure HDInsight.

## <a name="use-the-enterprise-security-package-to-secure-and-govern-the-cluster"></a>Usare il pacchetto Enterprise Security Package per proteggere e regolare il cluster

Il pacchetto Enterprise Security Package (ESP) supporta l'autenticazione basata su Active Directory, il supporto multiutente e il controllo degli accessi in base al ruolo. Quando si sceglie l'opzione ESP, il cluster HDInsight viene aggiunto al dominio di Active Directory e l'amministratore dell'azienda può configurare il controllo degli accessi in base al ruolo per la sicurezza di Hive usando Apache Ranger. L'amministratore può anche controllare l'accesso ai dati da parte dei dipendenti e qualsiasi modifica apportata ai criteri di controllo di accesso.

Le funzionalità ESP sono attualmente disponibili in anteprima e solo nei seguenti tipi di cluster: Apache Hadoop, Apache Spark, Apache HBase, Apache Kafka e Apache Interactive Query.

Usare la procedura seguente per distribuire il cluster HDInsight aggiunto al dominio:

- Distribuire Azure Active Directory (AAD) passando il nome del dominio
- Distribuire Azure Active Directory Domain Services (AAD DS)
- Creare la Rete virtuale e la subnet richieste
- Distribuire una macchina virtuale nella Rete virtuale per gestire AAD DS
- Aggiungere la macchina virtuale al dominio
- Installare gli strumenti di Active Directory e Domain Name System (DNS)
- Chiedere all'amministratore di AAD DS di creare un'unità organizzativa (UO)
- Abilitare il protocollo LDAP sicuro per AAD DS
- Creare un account del servizio in Azure Active Directory con autorizzazione di amministratore di lettura e scrittura delegata per l'unità organizzativa. L'account del servizio può quindi aggiungere le macchine virtuali al dominio e inserire entità macchina virtuale all'interno dell'unità organizzativa. Può anche creare entità servizio nell'unità organizzativa che si specifica quando si crea il cluster.

    > [!Note]
    > L'account del servizio non deve necessariamente essere l'account amministratore del dominio di Active Directory

- Distribuire il cluster ESP HDInsight impostando i parametri seguenti:
    - **Nome di dominio**: nome di dominio associato ad Azure AD DS.
    - **Nome utente del dominio**: account del servizio nel dominio gestito dal controller di dominio di Azure AD DS creato nella sezione precedente, ad esempio `hdiadmin@contoso.onmicrosoft.com`. Questo utente del dominio diventerà l'amministratore del cluster HDInsight.
    - **Password del dominio**: la password dell'account di servizio.
    - **Unità organizzativa**: nome distinto dell'unità organizzativa (UO) da usare con il cluster HDInsight, ad esempio `OU=HDInsightOU,DC=contoso,DC=onmicrosoft,DC=com`. Se questa unità organizzativa non esiste, il cluster HDInsight prova a crearne una usando i privilegi dell'account del servizio.
    - **URL LDAPS**: ad esempio `ldaps://contoso.onmicrosoft.com:636`.
    - **Gruppo utenti di accesso**: gruppi di sicurezza di cui si desidera sincronizzare gli utenti con il cluster, ad esempio `HiveUsers`. Per specificare più gruppi utenti, separarli con punti e virgola (;). Il gruppo o i gruppi devono essere presenti nella directory prima che venga creato il cluster.

Per altre informazioni, vedere gli articoli seguenti:

- [Introduzione alla sicurezza Hadoop con i cluster HDInsight aggiunti al dominio](../domain-joined/apache-domain-joined-introduction.md)
- [Pianificare cluster Hadoop aggiunti a un dominio di Azure in HDInsight](../domain-joined/apache-domain-joined-architecture.md)
- [Configurare un cluster HDInsight aggiunto al dominio con Azure Active Directory Domain Services](../domain-joined/apache-domain-joined-configure-using-azure-adds.md)
- [Sincronizzare gli utenti di Azure Active Directory con un cluster HDInsight](../hdinsight-sync-aad-users-to-cluster.md)
- [Configurare criteri Hive in HDInsight aggiunto al dominio](../domain-joined/apache-domain-joined-run-hive.md)
- [Eseguire Apache Oozie in cluster HDInsight Hadoop aggiunti a un dominio](../domain-joined/hdinsight-use-oozie-domain-joined-clusters.md)

## <a name="implement-end-to-end-enterprise-security-management"></a>Implementare la gestione della sicurezza aziendale end-to-end

È possibile implementare la sicurezza aziendale end-to-end usando i controlli seguenti:

- **Pipeline di dati privati e protetti (sicurezza a livello di perimetro)**
    - La sicurezza a livello di perimetro può essere conseguita attraverso Reti virtuali di Azure, gruppi di sicurezza di rete e il servizio gateway

- **Autenticazione e autorizzazione per l'accesso ai dati**
    - Creare un cluster HDInsight aggiunto al dominio usando Azure Active Directory Domain Services (Enterprise Security Package)
    - Usare Ambari per definire l'accesso in base al ruolo alle risorse del cluster per gli utenti di AD
    - Usare Apache Ranger per impostare i criteri di controllo di accesso per Hive a livello di tabella/colonna/riga.
    - L'accesso SSH al cluster può essere limitato al solo amministratore.

- **Controllo**
    - Visualizzare e segnalare tutti gli accessi ai dati e alle risorse del cluster HDInsight.
    - Visualizzare e segnalare tutte le modifiche ai criteri di controllo di accesso

- **Crittografia**
    - Crittografia lato server trasparente con chiavi gestite da Microsoft o dal cliente.
    - Nella crittografia di transito usando la crittografia lato client, https e Transport Layer Security (TLS)

Per altre informazioni, vedere gli articoli seguenti:

- [Panoramica di Reti virtuali di Azure](../../virtual-network/virtual-networks-overview.md)
- [Panoramica dei gruppi di sicurezza di rete di Azure](../../virtual-network/security-overview.md)
- [Peering di rete virtuale di Azure](../../virtual-network/virtual-network-peering-overview.md)
- [Guida alla sicurezza di Archiviazione di Azure](../../storage/common/storage-security-guide.md)
- [Crittografia del servizio di archiviazione di Azure a riposo](../../storage/common/storage-service-encryption.md)

## <a name="use-monitoring--alerting"></a>Usare le funzionalità di monitoraggio e di invio di avvisi

Per altre informazioni, vedere l'articolo:

[Panoramica di Monitoraggio di Azure](../../azure-monitor/overview.md)

## <a name="upgrade-clusters"></a>Aggiornare i cluster

Eseguire sempre l'aggiornamento alla versione più recente di HDInsight per usufruire dei vantaggi offerti dalle funzionalità più recenti. La procedura seguente consente di aggiornare il cluster alla versione più recente:

1. Creare un nuovo cluster HDInsight TEST usando la versione più recente disponibile di HDInsight.
1. Eseguire i test sul nuovo cluster per assicurarsi che i processi e i carichi di lavoro vengano eseguiti come previsto.
1. Modificare i processi, le applicazioni o i carichi di lavoro in base alle esigenze.
1. Eseguire il backup tutti i dati temporanei archiviati localmente sui nodi del cluster,
1. Eliminare il cluster esistente.
1. Creare un cluster con la versione più recente di HDInsight nella stessa subnet di rete virtuale, usando gli stessi dati e lo stesso archivio di metadati predefiniti del cluster precedente.
1. Importare i dati temporanei di cui è stata eseguita una copia di backup.
1. Avviare processi/continuare l'elaborazione con il nuovo cluster.

Per altre informazioni, vedere l'articolo: [Eseguire l'aggiornamento del cluster HDInsight a una versione più recente](../hdinsight-upgrade-cluster.md)

## <a name="patch-cluster-operating-systems"></a>Applicare patch ai sistemi operativi del cluster

In qualità di servizio Hadoop gestito, HDInsight si occupa dell'applicazione di patch al sistema operativo delle macchine virtuali usate dai cluster HDInsight.

Per altre informazioni, vedere [Applicazione di patch del sistema operativo per HDInsight](../hdinsight-os-patching.md)

## <a name="post-migration"></a>Dopo la migrazione

1. **Monitorare e aggiornare le applicazioni**: apportare in modo iterativo le modifiche necessarie a processi, elaborazioni e script.
2. **Eseguire test**: eseguire in modo iterativo test sul funzionamento e sulle prestazioni.
3. **Ottimizzare**: risolvere tutti i problemi di prestazioni in base ai risultati dei test precedenti e quindi eseguire di nuovo i test per verificare se le prestazioni sono migliorate.

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni su [HDInsight 4.0](https://docs.microsoft.com/azure/hdinsight/hadoop/apache-hadoop-introduction)