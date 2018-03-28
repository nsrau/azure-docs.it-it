---
title: Distribuire un'applicazione Java di Service Fabric in un cluster | Microsoft Docs
description: In questa esercitazione si apprenderà come distribuire un'applicazione Java di Service Fabric in un cluster di Azure Service Fabric.
services: service-fabric
documentationcenter: java
author: suhuruli
manager: msfussell
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: java
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/26/2018
ms.author: suhuruli
ms.custom: mvc
ms.openlocfilehash: 5245e53429278f2a346077cdb70426aaca339488
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/16/2018
---
# <a name="tutorial-deploy-a-java-application-to-a-service-fabric-cluster-in-azure"></a>Esercitazione: Distribuire un'applicazione Java in un cluster di Service Fabric in Azure
Questa esercitazione è la terza parte di una serie e illustra come distribuire un'applicazione di Service Fabric in un cluster in Azure.

Nella terza parte della serie si apprenderà come:

> [!div class="checklist"]
> * Creare un cluster Linux protetto in Azure 
> * Distribuire un'applicazione nel cluster

In questa serie di esercitazioni si apprenderà come:
> [!div class="checklist"]
> *  [Compilare un'applicazione Reliable Services di Service Fabric per Java](service-fabric-tutorial-create-java-app.md)
> * [Distribuire l'applicazione ed eseguirne il debug in un cluster locale](service-fabric-tutorial-debug-log-local-cluster.md)
> * Distribuire l'applicazione in un cluster di Azure
> * [Configurare il monitoraggio e la diagnostica per l'applicazione](service-fabric-tutorial-java-elk.md)
> * [Configurare CI/CD](service-fabric-tutorial-java-jenkins.md)

## <a name="prerequisites"></a>prerequisiti
Prima di iniziare questa esercitazione:
- Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- [Installare l'interfaccia della riga di comando di Azure 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
- Installare Service Fabric SDK per [Mac](service-fabric-get-started-mac.md) o [Linux](service-fabric-get-started-linux.md)
- [Installare Python 3](https://wiki.python.org/moin/BeginnersGuide/Download)

## <a name="create-a-service-fabric-cluster-in-azure"></a>Creare un cluster di Service Fabric in Azure

La procedura seguente crea le risorse necessarie per distribuire l'applicazione in un cluster di Service Fabric. Vengono inoltre configurate le risorse necessarie per monitorare l'integrità della soluzione usando lo stack ELK (Elasticsearch, Logstash, Kibana). In particolare viene usato [Hub eventi](https://azure.microsoft.com/services/event-hubs/) come sink per i log di Service Fabric, configurato per l'invio dei log dal cluster di Service Fabric all'istanza di Logstash. 

1. Aprire un terminale e scaricare il pacchetto seguente che contiene gli script helper necessari e i modelli per creare le risorse in Azure

    ```bash
    git clone https://github.com/Azure-Samples/service-fabric-java-quickstart.git
    ```

2. Accedere all'account Azure 

    ```bash
    az login
    ```

3. Impostare la sottoscrizione di Azure da usare per creare le risorse 

    ```bash
    az account set --subscription [SUBSCRIPTION-ID]
    ``` 

4. Dalla cartella *service-fabric-java-quickstart/AzureCluster*, eseguire questo comando per creare un certificato del cluster in Key Vault. Il certificato viene usato per proteggere il cluster di Service Fabric. Specificare l'area (deve essere la stessa area in cui si trova il cluster di Service Fabric), il nome del gruppo di risorse dell'insieme di credenziali delle chiavi, il nome dell'insieme di credenziali delle chiavi, la password del certificato e il nome DNS del cluster. 

    ```bash
    ./new-service-fabric-cluster-certificate.sh [REGION] [KEY-VAULT-RESOURCE-GROUP] [KEY-VAULT-NAME] [CERTIFICATE-PASSWORD] [CLUSTER-DNS-NAME-FOR-CERTIFICATE]
    
    Example: ./new-service-fabric-cluster-certificate.sh 'westus' 'testkeyvaultrg' 'testkeyvault' '<password>' 'testservicefabric.westus.cloudapp.azure.com'
    ```

    Il comando precedente restituisce le informazioni seguenti, che dovranno essere annotate per usarle in seguito.

    ```
    Source Vault Resource Id: /subscriptions/<subscription_id>/resourceGroups/testkeyvaultrg/providers/Microsoft.KeyVault/vaults/<name>
    Certificate URL: https://<name>.vault.azure.net/secrets/<cluster-dns-name-for-certificate>/<guid>
    Certificate Thumbprint: <THUMBPRINT>
    ```

5. Creare un gruppo di risorse per l'account di archiviazione per archiviare i log 

    ```bash
    az group create --location [REGION] --name [RESOURCE-GROUP-NAME]
    
    Example: az group create --location westus --name teststorageaccountrg
    ```

6. Creare un account di archiviazione che verrà usato per archiviare i log che verranno prodotti

    ```bash
    az storage account create -g [RESOURCE-GROUP-NAME] -l [REGION] --name [STORAGE-ACCOUNT-NAME] --kind Storage
    
    Example: az storage account create -g teststorageaccountrg -l westus --name teststorageaccount --kind Storage
    ```

7. Accedere al [portale di Azure](https://portal.azure.com) e passare alla scheda **Firma di accesso condiviso** per l'account di archiviazione. Generare il token di firma di accesso condiviso nel modo seguente. 

    ![Generare la firma di accesso condiviso per l'archiviazione](./media/service-fabric-tutorial-java-deploy-azure/storagesas.png)

8. Copiare l'URL di firma di accesso condiviso dell'account e conservarlo per usarlo durante la creazione del cluster di Service Fabric. L'URL sarà simile al seguente:

    ```
    https://teststorageaccount.table.core.windows.net/?sv=2017-04-17&ss=bfqt&srt=sco&sp=rwdlacup&se=2018-01-31T03:24:04Z&st=2018-01-30T19:24:04Z&spr=https,http&sig=IrkO1bVQCHcaKaTiJ5gilLSC5Wxtghu%2FJAeeY5HR%2BPU%3D
    ```

9. Creare un gruppo di risorse contenente le risorse di Hub eventi. Hub eventi viene usato per inviare messaggi da Service Fabric al server che esegue le risorse ELK.

    ```bash
    az group create --location [REGION] --name [RESOURCE-GROUP-NAME]
    
    Example: az group create --location westus --name testeventhubsrg
    ```

10. Creare una risorsa di Hub eventi con il comando seguente. Seguire le istruzioni per immettere i dettagli per namespaceName, eventHubName, consumerGroupName, sendAuthorizationRule e receiveAuthorizationRule. 

    ```bash
    az group deployment create -g [RESOURCE-GROUP-NAME] --template-file eventhubsdeploy.json
    
    Example: 
    az group deployment create -g testeventhubsrg --template-file eventhubsdeploy.json
    Please provide string value for 'namespaceName' (? for help): testeventhubnamespace
    Please provide string value for 'eventHubName' (? for help): testeventhub
    Please provide string value for 'consumerGroupName' (? for help): testeventhubconsumergroup
    Please provide string value for 'sendAuthorizationRuleName' (? for help): sender
    Please provide string value for 'receiveAuthorizationRuleName' (? for help): receiver
    ```

    Copiare il contenuto del campo **output** nell'output JSON del comando precedente. Le informazioni sul mittente vengono usate quando viene creato il cluster di Service Fabric. Il nome e la chiave del destinatario devono essere salvati per essere usati nell'esercitazione successiva, quando si configurerà il servizio Logstash per la ricezione dei messaggi da Hub eventi. Il BLOB seguente è un esempio di output JSON:     
    
    ```json
    "outputs": {
        "receiver Key": {
            "type": "String",
            "value": "[KEY]"
        },
        "receiver Name": {
            "type": "String",
            "value": "receiver"
        },
        "sender Key": {
            "type": "String",
            "value": "[KEY]"
        },
        "sender Name": {
            "type": "String",
            "value": "sender"
        }
    }
    ```

11. Eseguire lo script *eventhubssastoken.py* per generare l'URL di firma di accesso condiviso per la risorsa di Hub eventi creata. Questo URL di firma di accesso condiviso viene usato dal cluster di Service Fabric per inviare log a Hub eventi. Viene quindi usato il criterio **sender** per generare l'URL. Lo script restituisce l'URL di firma di accesso condiviso per la risorsa di Hub eventi usata nel passaggio seguente:

    ```python
    python3 eventhubssastoken.py 'testeventhubs' 'testeventhubs' 'sender' '[PRIMARY-KEY]'
    ```

    Copiare il valore del campo **sr** nell'output JSON restituito. Il valore del campo **sr** è il token di firma di accesso condiviso per Hub eventi. L'URL seguente è un esempio di campo **sr**:

    ```bash
    https%3A%2F%2Ftesteventhubs.servicebus.windows.net%2Ftesteventhubs&sig=7AlFYnbvEm%2Bat8ALi54JqHU4i6imoFxkjKHS0zI8z8I%3D&se=1517354876&skn=<policy_name>
    ```

    L'URL di firma di accesso condiviso per Hub eventi segue la struttura https://<namespacename>.servicebus.windows.net/<eventhubsname>?sr=<sastoken>. Ad esempio: https://testeventhubs.servicebus.windows.net/testeventhubs?sr=https%3A%2F%2Ftesteventhubs.servicebus.windows.net%2Ftesteventhubs&sig=7AlFYnbvEm%2Bat8ALi54JqHU4i6imoFxkjKHS0zI8z8I%3D&se=1517354876&skn=sender

12. Aprire il file *sfdeploy.parameters.json* e sostituire il contenuto seguente dei passaggi precedenti 

    ```
    "applicationDiagnosticsStorageAccountName": {
        "value": "teststorageaccount"
    },
    "applicationDiagnosticsStorageAccountSasToken": {
        "value": "[SAS-URL-STORAGE-ACCOUNT]"
    },
    "loggingEventHubSAS": {
        "value": "[SAS-URL-EVENT-HUBS]"
    }
    ```

13. Eseguire questo comando per creare il cluster di Service Fabric

    ```bash
    az sf cluster create --location 'westus' --resource-group 'testlinux' --template-file sfdeploy.json --parameter-file sfdeploy.parameters.json --secret-identifier <certificate_url_from_step4>
    ```

## <a name="deploy-your-application-to-the-cluster"></a>Distribuire l'applicazione nel cluster

1. Prima di distribuire l'applicazione è necessario aggiungere il frammento di codice seguente al file *Voting/VotingApplication/ApplicationManifest.xml*. Il campo **X509FindValue** è l'identificazione personale restituita dal passaggio 4 della sezione **Creare un cluster di Service Fabric in Azure**. Questo frammento di codice è annidato sotto il campo **ApplicationManifest** (campo radice). 

    ```xml
    <Certificates>
          <SecretsCertificate X509FindType="FindByThumbprint" X509FindValue="[CERTIFICATE-THUMBPRINT]" />
    </Certificates>
    ```

2. Per distribuire l'applicazione in questo cluster è necessario usare SFCTL per stabilire una connessione con il cluster. SFCTL richiede un file PEM con chiave pubblica e privata per la connessione al cluster. Eseguire quindi questo comando per generare un file PEM con la chiave pubblica e privata. 

    ```bash
    openssl pkcs12 -in testservicefabric.westus.cloudapp.azure.com.pfx -out sfctlconnection.pem -nodes -passin pass:<password>
    ```

3. Eseguire questo comando per connettersi al cluster.

    ```bash
    sfctl cluster select --endpoint https://testlinuxcluster.westus.cloudapp.azure.com:19080 --pem sfctlconnection.pem --no-verify
    ```

4. Per distribuire l'applicazione, passare alla cartella *Voting/Scripts* ed eseguire lo script **install.sh**. 

    ```bash
    ./install.sh
    ```

5. Per accedere a Service Fabric Explorer, aprire un browser a scelta e digitare https://testlinuxcluster.westus.cloudapp.azure.com:19080. Scegliere il certificato dall'archivio certificati che si intende usare per connettersi a questo endpoint. Se si usa un computer Linux, i certificati generati dallo script *new-service-fabric-cluster-certificate.sh* devono essere importati in Chrome per visualizzare Service Fabric Explorer. Se si usa un computer Mac è necessario installare il file PFX in Keychain. Si noterà che l'applicazione è stata installata nel cluster. 

    ![Java SFX in Azure](./media/service-fabric-tutorial-java-deploy-azure/sfxjavaonazure.png)

6. Per accedere all'applicazione, digitare https://testlinuxcluster.westus.cloudapp.azure.com:8080 

    ![App Voting Java in Azure](./media/service-fabric-tutorial-java-deploy-azure/votingappjavaazure.png)

7. Per disinstallare l'applicazione dal cluster, eseguire lo script *uninstall.sh* disponibile nella cartella **Scripts** 

    ```bash
    ./uninstall.sh
    ```

## <a name="next-steps"></a>Passaggi successivi
Questa esercitazione illustra come:

> [!div class="checklist"]
> * Creare un cluster Linux protetto in Azure 
> * Creare le risorse necessarie per il monitoraggio con ELK 
> * Facoltativo: Come usare cluster di entità per provare Service Fabric

Passare all'esercitazione successiva:
> [!div class="nextstepaction"]
> [Configurare monitoraggio e diagnostica](service-fabric-tutorial-java-elk.md)