---
title: Usare ID Broker (anteprima) per la gestione delle credenziali-Azure HDInsight
description: Informazioni su HDInsight ID Broker per semplificare l'autenticazione per i cluster Apache Hadoop aggiunti a un dominio.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: conceptual
ms.date: 12/12/2019
ms.openlocfilehash: 1bea8adbdb39d2ce83cfe7821ef052fdc1f1d512
ms.sourcegitcommit: dee7b84104741ddf74b660c3c0a291adf11ed349
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85921332"
---
# <a name="use-id-broker-preview-for-credential-management"></a>Usare ID Broker (anteprima) per la gestione delle credenziali

Questo articolo descrive come configurare e usare la funzionalità ID Broker in Azure HDInsight. È possibile usare questa funzionalità per accedere ad Apache Ambari tramite Azure Multi-Factor Authentication e ottenere i ticket Kerberos necessari senza che siano necessari hash delle password in Azure Active Directory Domain Services (Azure AD DS).

## <a name="overview"></a>Panoramica

ID Broker semplifica le configurazioni di autenticazione complesse negli scenari seguenti:

* L'organizzazione si affida alla Federazione per autenticare gli utenti per l'accesso alle risorse cloud. In precedenza, per usare i cluster HDInsight Enterprise Security Package (ESP), era necessario abilitare la sincronizzazione dell'hash delle password dall'ambiente locale al Azure Active Directory. Questo requisito potrebbe essere difficile o indesiderato per alcune organizzazioni.

* Si stanno creando soluzioni che usano tecnologie basate su diversi meccanismi di autenticazione. Ad esempio, Apache Hadoop e Apache Ranger si basano su Kerberos, mentre Azure Data Lake Storage si basa su OAuth.

ID Broker fornisce un'infrastruttura di autenticazione unificata e rimuove la necessità di sincronizzare gli hash delle password con Azure AD DS. ID Broker è costituito da componenti in esecuzione in una macchina virtuale Windows Server (nodo broker ID), insieme ai nodi del gateway del cluster. 

Il diagramma seguente illustra il flusso di autenticazione per tutti gli utenti, inclusi gli utenti federati, dopo l'abilitazione di ID Broker:

![Flusso di autenticazione con ID Broker](./media/identity-broker/identity-broker-architecture.png)

ID Broker consente di accedere ai cluster ESP usando Multi-Factor Authentication, senza fornire alcuna password. Se è già stato effettuato l'accesso ad altri servizi di Azure, ad esempio la portale di Azure, è possibile accedere al cluster HDInsight con un'esperienza di Single Sign-On (SSO).

## <a name="enable-hdinsight-id-broker"></a>Abilita HDInsight ID Broker

Per creare un cluster ESP con ID Broker abilitato, seguire questa procedura:

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Seguire i passaggi di creazione di base per un cluster ESP. Per altre informazioni, vedere [creare un cluster HDInsight con ESP](apache-domain-joined-configure-using-azure-adds.md#create-an-hdinsight-cluster-with-esp).
1. Selezionare **Enable HDINSIGHT ID Broker**.

La funzionalità ID Broker aggiungerà al cluster una macchina virtuale aggiuntiva. Questa macchina virtuale è il nodo ID Broker e include i componenti server per supportare l'autenticazione. Il nodo ID Broker è aggiunto al dominio Azure AD DS.

![Opzione per abilitare ID Broker](./media/identity-broker/identity-broker-enable.png)

### <a name="using-azure-resource-manager-templates"></a>Uso dei modelli di Gestione risorse di Azure
Se si aggiunge un nuovo ruolo denominato `idbrokernode` con gli attributi seguenti al profilo di calcolo del modello, il cluster verrà creato con il nodo ID Broker abilitato:

```json
.
.
.
"computeProfile": {
    "roles": [
        {
            "autoscale": null,
            "name": "headnode",
           ....
        },
        {
            "autoscale": null,
            "name": "workernode",
            ....
        },
        {
            "autoscale": null,
            "name": "idbrokernode",
            "targetInstanceCount": 1,
            "hardwareProfile": {
                "vmSize": "Standard_A2_V2"
            },
            "virtualNetworkProfile": {
                "id": "string",
                "subnet": "string"
            },
            "scriptActions": [],
            "dataDisksGroups": null
        }
    ]
}
.
.
.
```

## <a name="tool-integration"></a>Integrazione degli strumenti

Il [plug-in HDInsight IntelliJ](https://docs.microsoft.com/azure/hdinsight/spark/apache-spark-intellij-tool-plugin#integrate-with-hdinsight-identity-broker-hib) è stato aggiornato per supportare OAuth. È possibile utilizzare questo plug-in per connettersi al cluster e inviare i processi.

È anche possibile usare [Spark & gli strumenti hive per vs code](https://docs.microsoft.com/azure/hdinsight/hdinsight-for-vscode) per sfruttare i processi del notebook e inviare i processi.

## <a name="ssh-access-without-a-password-hash-in-azure-ad-ds"></a>Accesso SSH senza un hash della password in Azure AD DS

Dopo l'abilitazione di ID Broker, sarà comunque necessario un hash della password archiviato in Azure AD DS per gli scenari SSH con account di dominio. Per SSH a una macchina virtuale aggiunta a un dominio o per eseguire il `kinit` comando, è necessario specificare una password. 

Per l'autenticazione SSH è necessario che l'hash sia disponibile in Azure AD DS. Se si vuole usare SSH solo per gli scenari amministrativi, è possibile creare un account solo cloud e usarlo per SSH nel cluster. Altri utenti possono comunque usare gli strumenti Ambari o HDInsight (ad esempio il plug-in IntelliJ) senza che sia disponibile l'hash della password in Azure AD DS.

## <a name="clients-using-oauth-to-connect-to-hdinsight-gateway-with-id-broker-setup"></a>Client che usano OAuth per connettersi al gateway HDInsight con l'installazione di ID Broker

Nel programma di installazione di ID Broker è possibile aggiornare le app e i client personalizzati che si connettono al gateway per acquisire prima il token OAuth necessario. È possibile seguire la procedura descritta in questo [documento](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-app) per acquisire il token con le informazioni seguenti:

*   URI risorsa OAuth:`https://hib.azurehdinsight.net` 
* AppId: 7865c1d2-F040-46cc-875f-831a1ef6a28a
*   Autorizzazione: (nome: cluster. ReadWrite, ID: 8f89faa0-ffef-4007-974d-4989b39ad77d)

## <a name="next-steps"></a>Passaggi successivi

* [Configurare un cluster HDInsight con Enterprise Security Package tramite Azure Active Directory Domain Services](apache-domain-joined-configure-using-azure-adds.md)
* [Sincronizzare gli utenti di Azure Active Directory con un cluster HDInsight](../hdinsight-sync-aad-users-to-cluster.md)
* [Monitorare le prestazioni di un cluster](../hdinsight-key-scenarios-to-monitor.md)
