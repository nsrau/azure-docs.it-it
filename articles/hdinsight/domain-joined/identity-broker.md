---
title: Usare ID Broker (anteprima) per la gestione delle credenziali, Azure HDInsightUse ID Broker (preview) for credential management- Azure HDInsight
description: Informazioni su HDInsight ID Broker per semplificare l'autenticazione per i cluster Apache Hadoop aggiunti al dominio.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: conceptual
ms.date: 12/12/2019
ms.openlocfilehash: 1e7eaf49fb8b62259b8c619c89edffd629dfde7f
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81685503"
---
# <a name="use-id-broker-preview-for-credential-management"></a>Usare ID Broker (anteprima) per la gestione delle credenzialiUse ID Broker (preview) for credential management

Questo articolo descrive come configurare e usare la funzionalità DI ID Broker in Azure HDInsight.This article describes how to set up and use the ID Broker feature in Azure HDInsight. È possibile usare questa funzionalità per accedere ad Apache Ambari tramite Azure Multi-Factor Authentication e ottenere i ticket Kerberos necessari senza la necessità di un filtro delle password in Servizi di dominio Azure Active Directory (Azure AD DS).

## <a name="overview"></a>Panoramica

ID Broker semplifica le impostazioni di autenticazione complesse negli scenari seguenti:ID Broker simplifies complex authentication setups in the following scenarios:

* L'organizzazione si basa sulla federazione per autenticare gli utenti per l'accesso alle risorse cloud. In precedenza, per usare i cluster HDInsight Enterprise Security Package (ESP), era necessario abilitare la sincronizzazione dell'hash delle password dall'ambiente locale ad Azure Active Directory. Questo requisito potrebbe essere difficile o indesiderabile per alcune organizzazioni.

* Si stanno creando soluzioni che utilizzano tecnologie che si basano su diversi meccanismi di autenticazione. Ad esempio, Apache Hadoop e Apache Ranger si basano su Kerberos, mentre Azure Data Lake Storage si basa su OAuth.

ID Broker fornisce un'infrastruttura di autenticazione unificata e rimuove il requisito di sincronizzazione degli ishe delle password con Azure AD DS. ID Broker è costituito da componenti in esecuzione in una macchina virtuale Windows Server (nodo ID Broker), insieme ai nodi del gateway del cluster. 

Il diagramma seguente mostra il flusso di autenticazione per tutti gli utenti, inclusi gli utenti federati, dopo l'abilitazione di ID Broker:

![Flusso di autenticazione con ID Broker](./media/identity-broker/identity-broker-architecture.png)

ID Broker consente di accedere ai cluster ESP utilizzando Multi-Factor Authentication, senza fornire alcuna password. Se è già stato eseguito l'accesso ad altri servizi di Azure, ad esempio il portale di Azure, è possibile accedere al cluster HDInsight con un'esperienza Single Sign-On (SSO).

## <a name="enable-hdinsight-id-broker"></a>Abilitare HDInsight ID BrokerEnable HDInsight ID Broker

Per creare un cluster ESP con ID Broker abilitato, attenersi alla seguente procedura:

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Seguire i passaggi di creazione di base per un cluster ESP. Per ulteriori informazioni, vedere [Creare un cluster HDInsight con ESP](apache-domain-joined-configure-using-azure-adds.md#create-an-hdinsight-cluster-with-esp).
1. Selezionare **Abilita HDInsight ID Broker**.

La funzionalità broker ID aggiungerà una macchina virtuale aggiuntiva al cluster. Questa macchina virtuale è il nodo BROKER ID e include componenti server per supportare l'autenticazione. Il nodo Broker ID è un dominio aggiunto al dominio di Azure AD DS.

![Opzione per abilitare ID Broker](./media/identity-broker/identity-broker-enable.png)

### <a name="using-azure-resource-manager-templates"></a>Uso dei modelli di Gestione risorse di Azure
Se si aggiunge un `idbrokernode` nuovo ruolo chiamato con gli attributi seguenti al profilo di calcolo del modello, il cluster verrà creato con il nodo del broker di ID abilitato:

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

## <a name="tool-integration"></a>Integrazione degli utensili

Il plug-in IntelliJ HDInsight è stato aggiornato per supportare OAuth.The HDInsight [IntelliJ plug-in](https://docs.microsoft.com/azure/hdinsight/spark/apache-spark-intellij-tool-plugin#integrate-with-hdinsight-identity-broker-hib) is updated to support OAuth. È possibile utilizzare questo plug-in per connettersi al cluster e inviare processi.

## <a name="ssh-access-without-a-password-hash-in-azure-ad-ds"></a>Accesso SSH senza hash della password in Azure AD DSS

Dopo aver abilitato ID Broker, sarà comunque necessario un hash della password archiviato in Azure AD DS per scenari SSH con account di dominio. Per SSH a una macchina virtuale aggiunta `kinit` a un dominio o per eseguire il comando, è necessario fornire una password. 

SSH authentication requires the hash to be available in Azure AD DS. Se si desidera utilizzare SSH solo per scenari amministrativi, è possibile creare un account solo cloud e utilizzarlo per SSH nel cluster. Altri utenti possono comunque usare gli strumenti Ambari o HDInsight (ad esempio il plug-in IntelliJ) senza avere l'hash della password disponibile in Servizi di dominio Active Directory di Azure.Other users can still use Ambari or HDInsight tools (such as the IntelliJ plug-in) without have the password hash available in Azure AD DS.

## <a name="clients-using-oauth-to-connect-to-hdinsight-gateway-with-id-broker-setup"></a>Client che utilizzano OAuth per connettersi al gateway HDInsight con la configurazione di ID Broker

Nella configurazione del broker ID, le app personalizzate e i client che si connettono al gateway possono essere aggiornati per acquisire prima il token OAuth richiesto. È possibile seguire i passaggi in questo [documento](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-app) per acquisire il token con le seguenti informazioni:

*   URI delle risorse OAuth:https://hib.azurehdinsight.net 
* AppId: 7865c1d2-f040-46cc-875f-831a1ef6a28a
*   Autorizzazione: (nome: Cluster.ReadWrite, id: 8f89faa0-ffef-4007-974d-4989b39ad77d)

## <a name="next-steps"></a>Passaggi successivi

* [Configurare un cluster HDInsight con Enterprise Security Package usando Servizi di dominio Azure Active DirectoryConfigure an HDInsight cluster with Enterprise Security Package by using Azure Active Directory Domain Services](apache-domain-joined-configure-using-azure-adds.md)
* [Sincronizzare gli utenti di Azure Active Directory con un cluster HDInsight](../hdinsight-sync-aad-users-to-cluster.md)
* [Monitorare le prestazioni di un cluster](../hdinsight-key-scenarios-to-monitor.md)
