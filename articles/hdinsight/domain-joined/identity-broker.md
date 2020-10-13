---
title: Usare ID Broker (anteprima) per la gestione delle credenziali-Azure HDInsight
description: Informazioni su HDInsight ID Broker per semplificare l'autenticazione per i cluster Apache Hadoop aggiunti a un dominio.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 09/23/2020
ms.openlocfilehash: 24f15b8a4d5a5afd3a2794fe686d3acb0036cdd8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91565327"
---
# <a name="azure-hdinsight-id-broker-preview"></a>Azure HDInsight ID Broker (anteprima)

Questo articolo descrive come configurare e usare la funzionalità HDInsight ID Broker (HIB) in Azure HDInsight. È possibile usare questa funzionalità per ottenere l'autenticazione OAuth moderna in Apache Ambari mentre è in corso l'applicazione di Multi-Factor Authentication (multi-factor authentication) senza la necessità di hash di password legacy in Azure Active Directory Domain Services (AAD-DS).

## <a name="overview"></a>Panoramica

HIB semplifica le configurazioni di autenticazione complesse negli scenari seguenti:

* L'organizzazione si affida alla Federazione per autenticare gli utenti per l'accesso alle risorse cloud. In precedenza, per usare i cluster HDInsight Enterprise Security Package (ESP), era necessario abilitare la sincronizzazione dell'hash delle password dall'ambiente locale al Azure Active Directory (Azure AD). Questo requisito potrebbe essere difficile o indesiderato per alcune organizzazioni.

* L'organizzazione desidera applicare l'autenticazione a più fattori per l'accesso basato su Web/HTTP ad Apache Ambari e altre risorse cluster.

HIB fornisce l'infrastruttura di autenticazione che consente la transizione del protocollo da OAuth (Modern) a Kerberos (legacy) senza la necessità di sincronizzare gli hash delle password per AAD-DS. Questa infrastruttura è costituita da componenti in esecuzione in una macchina virtuale Windows Server (nodo broker ID), insieme ai nodi del gateway del cluster.

Usare la tabella seguente per determinare la migliore opzione di autenticazione in base alle esigenze dell'organizzazione:

|Opzioni di autenticazione |Configurazione di HDInsight | Fattori da considerare |
|---|---|---|
| OAuth completo | ESP + HIB | 1. opzione più sicura (autenticazione a più fattori supportata) 2.    La sincronizzazione dell'hash di passaggio non è obbligatoria. 3.  Nessun accesso SSH/kinit/keytab per gli account locali, che non hanno l'hash della password in AAD-DS. 4.   Gli account solo cloud possono comunque SSH/kinit/keytab. 5. Accesso basato sul Web a Ambari tramite OAuth 6.  Richiede l'aggiornamento di app legacy (JDBC/ODBC e così via) per il supporto di OAuth.|
| OAuth e autenticazione di base | ESP + HIB | 1. accesso basato sul Web a Ambari tramite OAuth 2. Le app legacy continuano a usare l'autenticazione di base. 3. Multi-factor authentication deve essere disabilitato per l'accesso con autenticazione di base. 4. La sincronizzazione dell'hash di passaggio non è obbligatoria. 5. Nessun accesso SSH/kinit/keytab per gli account locali, che non hanno l'hash della password in AAD-DS. 6. Gli account solo cloud possono comunque SSH/kinit. |
| Autenticazione completamente Basic | ESP | 1. più simile alle configurazioni locali. 2. La sincronizzazione dell'hash delle password per AAD-DS è obbligatoria. 3. Gli account locali possono usare SSH/kinit o keytab. 4. È necessario disabilitare l'autenticazione a più fattori se l'archivio di backup è ADLS Gen2 |

Il diagramma seguente illustra il flusso di autenticazione basato su OAuth moderno per tutti gli utenti, inclusi gli utenti federati, dopo l'abilitazione di ID Broker:

:::image type="content" source="media/identity-broker/identity-broker-architecture.png" alt-text="Flusso di autenticazione con ID Broker":::

In questo diagramma, il client (ad esempio browser o app) deve prima acquisire il token OAuth e quindi presentare il token al gateway in una richiesta HTTP. Se è già stato effettuato l'accesso ad altri servizi di Azure, ad esempio la portale di Azure, è possibile accedere al cluster HDInsight con un'esperienza di Single Sign-On (SSO).

Potrebbero essere presenti molte applicazioni legacy che supportano solo l'autenticazione di base, ad esempio nome utente e password. Per questi scenari, è comunque possibile usare l'autenticazione di base HTTP per connettersi ai gateway del cluster. In questa configurazione è necessario garantire la connettività di rete dai nodi del gateway all'endpoint federativo (AD FS endpoint) per garantire una linea di visione diretta dai nodi del gateway. 

Il diagramma seguente illustra il flusso di autenticazione di base per gli utenti federati. Prima di tutto, il gateway tenta di completare l'autenticazione con il [flusso ROPC](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth-ropc) e, nel caso in cui non siano stati sincronizzati gli hash delle password per Azure ad, quindi esegue il fallback per individuare ad FS endpoint e completare l'autenticazione accedendo all'endpoint di ad FS.

:::image type="content" source="media/identity-broker/basic-authentication.png" alt-text="Flusso di autenticazione con ID Broker":::


## <a name="enable-hdinsight-id-broker"></a>Abilita HDInsight ID Broker

Per creare un cluster ESP con ID Broker abilitato, seguire questa procedura:

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Seguire i passaggi di creazione di base per un cluster ESP. Per altre informazioni, vedere [creare un cluster HDInsight con ESP](apache-domain-joined-configure-using-azure-adds.md#create-an-hdinsight-cluster-with-esp).
1. Selezionare **Enable HDINSIGHT ID Broker**.

La funzionalità ID Broker aggiungerà al cluster una macchina virtuale aggiuntiva. Questa macchina virtuale è il nodo ID Broker e include i componenti server per supportare l'autenticazione. Il nodo ID Broker è aggiunto al dominio Azure AD DS.

![Opzione per abilitare ID Broker](./media/identity-broker/identity-broker-enable.png)

### <a name="using-azure-resource-manager-templates"></a>Uso di modelli di Azure Resource Manager
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

Gli strumenti HDIsngith vengono aggiornati per il supporto nativo di OAuth. Si consiglia vivamente di usare questi strumenti per l'accesso moderno basato su OAuth ai cluster. Il [plug-in HDInsight IntelliJ](https://docs.microsoft.com/azure/hdinsight/spark/apache-spark-intellij-tool-plugin#integrate-with-hdinsight-identity-broker-hib) può essere usato per le applicazioni basate su Java, ad esempio scala. [Spark & hive Tools for vs code](https://docs.microsoft.com/azure/hdinsight/hdinsight-for-vscode) può essere usato per i processi PySpark e hive. Supportano sia i processi batch che quelli interattivi.

## <a name="ssh-access-without-a-password-hash-in-azure-ad-ds"></a>Accesso SSH senza un hash della password in Azure AD DS

|Opzioni SSH |Fattori da considerare |
|---|---|
| Account VM locale (ad esempio sshuser) | 1. questo account è stato specificato al momento della creazione del cluster. 2.  Nessun authication Kerberos per questo account |
| Account solo cloud (ad esempio alice@contoso.onmicrosoft.com ) | 1. l'hash della password è disponibile in AAD-DS 2. L'autenticazione Kerberos è possibile tramite SSH Kerberos |
| Account locale (ad esempio alice@contoso.com ) | 1. l'autenticazione Kerberos SSH è possibile solo se l'hash delle password è disponibile in AAD-DS, altrimenti l'utente non può eseguire SSH nel cluster |

Per SSH a una macchina virtuale aggiunta a un dominio o per eseguire il `kinit` comando, è necessario specificare una password. Per l'autenticazione Kerberos SSH è necessario che l'hash sia disponibile in AAD-DS. Se si vuole usare SSH solo per gli scenari amministrativi, è possibile creare un account solo cloud e usarlo per SSH nel cluster. Altri utenti locali possono comunque usare gli strumenti Ambari o HDInsight o l'autenticazione di base HTTP senza che sia disponibile l'hash della password in AAD-DS.

Se l'organizzazione non esegue la sincronizzazione degli hash delle password ad AAD-DS, è consigliabile creare un solo utente cloud in Azure AD e assegnarlo come amministratore del cluster durante la creazione del cluster e usarlo per scopi di amministrazione, che include l'accesso radice alle macchine virtuali tramite SSH.

Per risolvere i problemi di autenticazione, vedere la [Guida](https://docs.microsoft.com/azure/hdinsight/domain-joined/domain-joined-authentication-issues).

## <a name="clients-using-oauth-to-connect-to-hdinsight-gateway-with-hib"></a>Client che usano OAuth per connettersi al gateway di HDInsight con HIB

Nel programma di installazione di HIB, è possibile aggiornare le app e i client personalizzati che si connettono al gateway per acquisire prima il token OAuth necessario. È possibile seguire la procedura descritta in questo [documento](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-app) per acquisire il token con le informazioni seguenti:

*   URI risorsa OAuth: `https://hib.azurehdinsight.net` 
*   AppId: 7865c1d2-F040-46cc-875f-831a1ef6a28a
*   Autorizzazione: (nome: cluster. ReadWrite, ID: 8f89faa0-ffef-4007-974d-4989b39ad77d)

Dopo aver acquisito il token OAuth, è possibile usarlo nell'intestazione dell'autorizzazione della richiesta HTTP per il gateway del cluster (ad esempio, https:// <clustername> -int.azurehdinsight.NET). Ad esempio, un comando curl di esempio per l'API Apache Livio potrebbe avere un aspetto simile al seguente:
    
```bash
curl -k -v -H "Authorization: Bearer Access_TOKEN" -H "Content-Type: application/json" -X POST -d '{ "file":"wasbs://mycontainer@mystorageaccount.blob.core.windows.net/data/SparkSimpleTest.jar", "className":"com.microsoft.spark.test.SimpleFile" }' "https://<clustername>-int.azurehdinsight.net/livy/batches" -H "X-Requested-By:<username@domain.com>"
``` 

## <a name="next-steps"></a>Passaggi successivi

* [Configurare un cluster HDInsight con Enterprise Security Package tramite Azure Active Directory Domain Services](apache-domain-joined-configure-using-azure-adds.md)
* [Sincronizzare gli utenti di Azure Active Directory con un cluster HDInsight](../hdinsight-sync-aad-users-to-cluster.md)
* [Monitorare le prestazioni di un cluster](../hdinsight-key-scenarios-to-monitor.md)
