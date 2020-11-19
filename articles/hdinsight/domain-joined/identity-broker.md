---
title: Broker ID Azure HDInsight (HIB)
description: Informazioni su Azure HDInsight ID Broker per semplificare l'autenticazione per i cluster Apache Hadoop aggiunti a un dominio.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 11/03/2020
ms.openlocfilehash: 9a2bda0a526c307ae17d8415f6f24423ddf51b63
ms.sourcegitcommit: f6236e0fa28343cf0e478ab630d43e3fd78b9596
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/19/2020
ms.locfileid: "94917767"
---
# <a name="azure-hdinsight-id-broker-hib"></a>Broker ID Azure HDInsight (HIB)

Questo articolo descrive come configurare e usare la funzionalità Azure HDInsight ID Broker. È possibile usare questa funzionalità per ottenere l'autenticazione OAuth moderna in Apache Ambari mentre è in corso l'applicazione dell'autenticazione a più fattori senza la necessità di hash di password legacy in Azure Active Directory Domain Services (Azure AD DS).

## <a name="overview"></a>Panoramica

HDInsight ID Broker semplifica le configurazioni di autenticazione complesse negli scenari seguenti:

* L'organizzazione si affida alla Federazione per autenticare gli utenti per l'accesso alle risorse cloud. In precedenza, per usare i cluster HDInsight Enterprise Security Package, era necessario abilitare la sincronizzazione dell'hash delle password dall'ambiente locale al Azure Active Directory (Azure AD). Questo requisito potrebbe essere difficile o indesiderato per alcune organizzazioni.
* L'organizzazione vuole applicare l'autenticazione a più fattori per l'accesso basato su Web o su HTTP ad Apache Ambari e altre risorse cluster.

HDInsight ID Broker fornisce l'infrastruttura di autenticazione che consente la transizione del protocollo da OAuth (Modern) a Kerberos (legacy) senza la necessità di sincronizzare gli hash delle password per Azure AD DS. Questa infrastruttura è costituita da componenti in esecuzione in una macchina virtuale (VM) Windows Server con il nodo HDInsight ID Broker abilitato, insieme ai nodi del gateway del cluster.

Usare la tabella seguente per determinare la migliore opzione di autenticazione in base alle esigenze dell'organizzazione.

|Opzioni di autenticazione |Configurazione di HDInsight | Fattori da considerare |
|---|---|---|
| OAuth completo | Enterprise Security Package + HDInsight ID Broker | Opzione più sicura. (L'autenticazione a più fattori è supportata). La sincronizzazione dell'hash di passaggio *non* è obbligatoria. Nessun accesso SSH/kinit/keytab per gli account locali, che non hanno un hash della password in Azure AD DS. Gli account solo cloud possono comunque SSH/kinit/keytab. Accesso basato sul Web a Ambari tramite OAuth. Per il supporto di OAuth, è necessario aggiornare le app legacy, ad esempio JDBC/ODBC.|
| OAuth e autenticazione di base | Enterprise Security Package + HDInsight ID Broker | Accesso basato sul Web a Ambari tramite OAuth. Le app legacy continuano a usare l'autenticazione di base. L'autenticazione a più fattori deve essere disabilitata per l'accesso con autenticazione di base. La sincronizzazione dell'hash di passaggio *non* è obbligatoria. Nessun accesso SSH/kinit/keytab per gli account locali, che non hanno un hash della password in Azure AD DS. Gli account solo cloud possono comunque SSH/kinit. |
| Autenticazione completamente Basic | Enterprise Security Package | In modo analogo alle configurazioni locali. La sincronizzazione dell'hash delle password per Azure AD DS è obbligatoria. Gli account locali possono usare SSH/kinit o keytab. È necessario disabilitare l'autenticazione a più fattori se l'archivio di backup è Azure Data Lake Storage Gen2. |

Il diagramma seguente illustra il flusso di autenticazione basato su OAuth moderno per tutti gli utenti, inclusi gli utenti federati, dopo l'abilitazione di HDInsight ID Broker:

:::image type="content" source="media/identity-broker/identity-broker-architecture.png" alt-text="Diagramma che mostra il flusso di autenticazione con HDInsight ID Broker.":::

In questo diagramma, il client, ovvero un browser o un'app, deve prima acquisire il token OAuth. Quindi presenta il token al gateway in una richiesta HTTP. Se è già stato effettuato l'accesso ad altri servizi di Azure, ad esempio la portale di Azure, è possibile accedere al cluster HDInsight con un'esperienza Single Sign-On.

Potrebbero essere presenti molte applicazioni legacy che supportano solo l'autenticazione di base, ovvero nome utente e password. Per questi scenari, è comunque possibile usare l'autenticazione di base HTTP per connettersi ai gateway del cluster. In questa configurazione è necessario garantire la connettività di rete dai nodi del gateway all'endpoint Active Directory Federation Services (AD FS) per garantire una linea di visione diretta dai nodi del gateway.

Il diagramma seguente illustra il flusso di autenticazione di base per gli utenti federati. In primo luogo, il gateway tenta di completare l'autenticazione usando il [flusso ROPC](../../active-directory/develop/v2-oauth-ropc.md). Se non sono stati sincronizzati gli hash delle password per Azure AD, viene rilevata l'individuazione dell'endpoint AD FS e viene completata l'autenticazione accedendo all'endpoint di AD FS.

:::image type="content" source="media/identity-broker/basic-authentication.png" alt-text="Diagramma che mostra l'architettura con l'autenticazione di base.":::


## <a name="enable-hdinsight-id-broker"></a>Abilita HDInsight ID Broker

Per creare un cluster Enterprise Security Package con HDInsight ID Broker abilitato:

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Seguire i passaggi di creazione di base per un cluster Enterprise Security Package. Per altre informazioni, vedere [creare un cluster HDInsight con Enterprise Security Package](apache-domain-joined-configure-using-azure-adds.md#create-an-hdinsight-cluster-with-esp).
1. Selezionare **Enable HDINSIGHT ID Broker**.

La funzionalità HDInsight ID Broker aggiunge una macchina virtuale aggiuntiva al cluster. Questa macchina virtuale è il nodo HDInsight ID Broker e include i componenti server per supportare l'autenticazione. Il nodo HDInsight ID Broker è aggiunto al dominio Azure AD DS.

![Diagramma che mostra l'opzione per abilitare HDInsight ID Broker.](./media/identity-broker/identity-broker-enable.png)

### <a name="use-azure-resource-manager-templates"></a>Usare i modelli di Gestione risorse di Azure

Se si aggiunge un nuovo ruolo denominato `idbrokernode` con gli attributi seguenti al profilo di calcolo del modello, il cluster verrà creato con il nodo HDINSIGHT ID Broker abilitato:

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
            "targetInstanceCount": 2,
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

Per visualizzare un esempio completo di un modello ARM, vedere il modello pubblicato [qui](https://github.com/Azure-Samples/hdinsight-enterprise-security/tree/main/ESP-HIB-PL-Template).


## <a name="tool-integration"></a>Integrazione degli strumenti

Gli strumenti HDInsight vengono aggiornati per il supporto nativo di OAuth. Usare questi strumenti per l'accesso moderno basato su OAuth ai cluster. Il [plug-in HDInsight IntelliJ](../spark/apache-spark-intellij-tool-plugin.md#integrate-with-hdinsight-identity-broker-hib) può essere usato per le applicazioni basate su Java, ad esempio scala. È possibile usare [gli strumenti Spark e hive per Visual Studio Code](../hdinsight-for-vscode.md) per i processi PySpark e hive. Gli strumenti supportano entrambi i processi batch e Interactive.

## <a name="ssh-access-without-a-password-hash-in-azure-ad-ds"></a>Accesso SSH senza un hash della password in Azure AD DS

|Opzioni SSH |Fattori da considerare |
|---|---|
| Account VM locale (ad esempio, sshuser) | Questo account è stato specificato al momento della creazione del cluster. Non esiste alcuna autenticazione Kerberos per questo account. |
| Account solo cloud (ad esempio, alice@contoso.onmicrosoft.com ) | L'hash della password è disponibile in Azure AD DS. L'autenticazione Kerberos è possibile tramite SSH Kerberos. |
| Account locale (ad esempio, alice@contoso.com ) | L'autenticazione Kerberos SSH è possibile solo se nell'Azure AD DS è disponibile un hash della password. In caso contrario, l'utente non può eseguire SSH sul cluster. |

Per SSH a una macchina virtuale aggiunta a un dominio o per eseguire il `kinit` comando, è necessario specificare una password. Per l'autenticazione Kerberos SSH è necessario che l'hash sia disponibile in Azure AD DS. Se si vuole usare SSH solo per gli scenari amministrativi, è possibile creare un account solo cloud e usarlo per SSH nel cluster. Altri utenti locali possono comunque usare gli strumenti Ambari o HDInsight o l'autenticazione di base HTTP senza che sia disponibile l'hash della password in Azure AD DS.

Se l'organizzazione non esegue la sincronizzazione degli hash delle password per Azure AD DS, è consigliabile creare un utente solo cloud nella Azure AD. Quindi assegnarlo come amministratore del cluster quando si crea il cluster e usarlo a scopo di amministrazione. È possibile usarlo per ottenere l'accesso alla radice alle macchine virtuali tramite SSH.

Per risolvere i problemi di autenticazione, vedere [questa guida](./domain-joined-authentication-issues.md).

## <a name="clients-using-oauth-to-connect-to-an-hdinsight-gateway-with-hdinsight-id-broker"></a>Client che usano OAuth per connettersi a un gateway HDInsight con HDInsight ID Broker

Nel programma di installazione di HDInsight ID Broker, è possibile aggiornare le app e i client personalizzati che si connettono al gateway per acquisire prima il token OAuth necessario. Per acquisire il token con le informazioni seguenti, attenersi alla procedura descritta in [questo documento](../../storage/common/storage-auth-aad-app.md) :

*   URI risorsa OAuth: `https://hib.azurehdinsight.net` 
*   AppId: 7865c1d2-F040-46cc-875f-831a1ef6a28a
*   Autorizzazione: (nome: cluster. ReadWrite, ID: 8f89faa0-ffef-4007-974d-4989b39ad77d)

Dopo aver acquisito il token OAuth, usarlo nell'intestazione dell'autorizzazione della richiesta HTTP per il gateway del cluster (ad esempio, https:// <clustername> -int.azurehdinsight.NET). Un comando curl di esempio per l'API Apache Livio potrebbe avere un aspetto simile a questo esempio:
    
```bash
curl -k -v -H "Authorization: Bearer Access_TOKEN" -H "Content-Type: application/json" -X POST -d '{ "file":"wasbs://mycontainer@mystorageaccount.blob.core.windows.net/data/SparkSimpleTest.jar", "className":"com.microsoft.spark.test.SimpleFile" }' "https://<clustername>-int.azurehdinsight.net/livy/batches" -H "X-Requested-By:<username@domain.com>"
``` 

Per l'uso di Oneline e Livio, è anche possibile seguire i codici di esempio disponibili [qui](https://github.com/Azure-Samples/hdinsight-enterprise-security/tree/main/HIB/HIBSamples) per configurare il client per l'uso di OAuth e la connessione al cluster.

## <a name="faq"></a>Domande frequenti
### <a name="what-app-is-created-by-hdinsight-in-aad"></a>Quale app viene creata da HDInsight in AAD?
Per ogni cluster, un'applicazione di terze parti verrà registrata in AAD con l'URI del cluster come identifierUri (ad esempio https://clustername.azurehdinsight.net ).

### <a name="why-are-users-prompted-for-consent-before-using-hib-enabled-clusters"></a>Perché gli utenti hanno richiesto il consenso prima di usare i cluster abilitati per HIB?
In AAD, il consenso è necessario per tutte le applicazioni di terze parti prima di poter autenticare gli utenti o accedere ai dati.

### <a name="can-the-consent-be-approved-programatically"></a>Il consenso può essere approvato a livello?
Microsoft Graph API consente di automatizzare il consenso, vedere la [documentazione dell'API](https://docs.microsoft.com/graph/api/resources/oauth2permissiongrant?view=graph-rest-1.0) la sequenza per automatizzare il consenso è:

* Registrare un'app e concedere all'app le autorizzazioni Application. ReadWrite. all per accedere Microsoft Graph
* Dopo aver creato un cluster, eseguire una query per l'app cluster in base all'URI dell'identificatore
* Registrare il consenso per l'app

Quando il cluster viene eliminato, HDInsight Elimina l'app e non è necessario pulire alcun consenso.

 


## <a name="next-steps"></a>Passaggi successivi

* [Configurare un cluster HDInsight con Enterprise Security Package tramite Azure Active Directory Domain Services](apache-domain-joined-configure-using-azure-adds.md)
* [Sincronizzare gli utenti di Azure Active Directory con un cluster HDInsight](../hdinsight-sync-aad-users-to-cluster.md)
* [Monitorare le prestazioni di un cluster](../hdinsight-key-scenarios-to-monitor.md)
