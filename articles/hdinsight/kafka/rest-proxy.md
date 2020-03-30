---
title: Apache Kafka REST proxy - Azure HDInsight
description: Informazioni su come eseguire operazioni Apache Kafka usando un proxy REST Kafka in Azure HDInsight.Learn how to perform Apache Kafka operations using a Kafka REST proxy on Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: hrasheed
ms.service: hdinsight
ms.topic: conceptual
ms.date: 12/17/2019
ms.openlocfilehash: d99a3b803b80dc41990a63e647d3ba928deb31af
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77198906"
---
# <a name="interact-with-apache-kafka-clusters-in-azure-hdinsight-using-a-rest-proxy"></a>Interagire con i cluster Apache Kafka in Azure HDInsight usando un proxy RESTInteract with Apache Kafka clusters in Azure HDInsight using a REST proxy

Il proxy REST Kafka consente di interagire con il cluster Kafka tramite un'API REST su HTTP. Ciò significa che i client Kafka possono trovarsi all'esterno della rete virtuale. Inoltre, i client possono effettuare semplici chiamate HTTP per inviare e ricevere messaggi al cluster Kafka, anziché affidarsi alle librerie Kafka. Questa esercitazione illustra come creare un cluster Kafka abilitato per il proxy REST e fornire un codice di esempio che illustra come effettuare chiamate al proxy REST.

## <a name="rest-api-reference"></a>Informazioni di riferimento sulle API REST

Per la specifica completa delle operazioni supportate dall'API REST Kafka, vedere [HDInsight Kafka REST Proxy API Reference](https://docs.microsoft.com/rest/api/hdinsight-kafka-rest-proxy).

## <a name="background"></a>Background

![Architettura proxy REST Kafka](./media/rest-proxy/rest-proxy-architecture.png)

Per la specifica completa delle operazioni supportate dall'API, vedere [Apache Kafka REST Proxy API](https://docs.microsoft.com/rest/api/hdinsight-kafka-rest-proxy).

### <a name="rest-proxy-endpoint"></a>Endpoint proxy REST

La creazione di un cluster Kafka HDInsight con proxy REST crea un nuovo endpoint pubblico per il cluster, che è possibile trovare nel cluster HDInsight "Proprietà" nel portale di Azure.Creating an HDInsight Kafka cluster with REST proxy creates a new public endpoint for your cluster, which you can find in your HDInsight cluster "Properties" on the Azure portal.

### <a name="security"></a>Security

L'accesso al proxy REST Kafka viene gestito con i gruppi di sicurezza di Azure Active Directory.Access to the Kafka REST proxy is managed with Azure Active Directory security groups. Quando si crea il cluster Kafka con il proxy REST abilitato, si fornirà il gruppo di sicurezza di Azure Active Directory che deve avere accesso all'endpoint REST. I client Kafka (applicazioni) che devono accedere al proxy REST devono essere registrati in questo gruppo dal proprietario del gruppo. The group owner can do this via the Portal or via Powershell.

Prima di effettuare richieste all'endpoint proxy REST, l'applicazione client deve ottenere un token OAuth per verificare l'appartenenza al gruppo di sicurezza corretto. Di seguito è riportato un esempio di [applicazione client](#client-application-sample) che mostra come ottenere un token OAuth. Una volta che l'applicazione client dispone del token OAuth, deve passare tale token nella richiesta HTTP effettuata al proxy REST.

> [!NOTE]  
> Per altre informazioni sui gruppi di sicurezza di AAD, vedere [Gestire l'accesso a app e risorse usando i gruppi](../../active-directory/fundamentals/active-directory-manage-groups.md)di Azure Active Directory.See Manage app and resource access using Azure Active Directory groups , to learn more about AAD security groups. Per altre informazioni sul funzionamento dei token OAuth, vedere [Autorizzare l'accesso alle applicazioni Web](../../active-directory/develop/v1-protocols-oauth-code.md)di Azure Active Directory usando il flusso di concessione del codice OAuth 2.0.For more information on how OAuth tokens work, see Authorize access to Azure Active Directory web applications using the OAuth 2.0 code grant flow .

## <a name="prerequisites"></a>Prerequisiti

1. Registrare un'applicazione con Azure AD. The client applications that you write to interact with the Kafka REST proxy will use this application's ID and secret to authenticate to Azure.
1. Creare un gruppo di sicurezza di Azure AD e aggiungere l'applicazione registrata con Azure AD al gruppo di sicurezza. Questo gruppo di sicurezza verrà usato per controllare quali applicazioni sono autorizzate a interagire con il proxy REST. Per altre informazioni sulla creazione di gruppi di Azure AD, vedere [Creare un gruppo di base e aggiungere membri tramite Azure Active Directory.For](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)more information on creating Azure AD groups, see Create a basic group and add members using Azure Active Directory .

## <a name="create-a-kafka-cluster-with-rest-proxy-enabled"></a>Creare un cluster Kafka con il proxy REST abilitatoCreate a Kafka cluster with REST proxy enabled

1. Durante il flusso di lavoro di creazione del cluster Kafka, nella scheda "Sicurezza e rete", selezionare l'opzione "Abilita proxy REST Kafka".

     ![Abilitare il proxy REST Kafka e selezionare il gruppo di sicurezzaEnable Kafka REST proxy and select security group](./media/rest-proxy/azure-portal-cluster-security-networking-kafka-rest.png)

1. Fare clic su **Seleziona gruppo di sicurezza**. Nell'elenco dei gruppi di sicurezza selezionare il gruppo di sicurezza a cui si desidera accedere al proxy REST. È possibile utilizzare la casella di ricerca per trovare il gruppo di sicurezza appropriato. Fare clic sul pulsante **Seleziona** nella parte inferiore.

     ![Abilitare il proxy REST Kafka e selezionare il gruppo di sicurezzaEnable Kafka REST proxy and select security group](./media/rest-proxy/azure-portal-cluster-security-networking-kafka-rest2.png)

1. Completare i passaggi rimanenti per creare il cluster come descritto in Creare un cluster Apache Kafka in Azure HDInsight usando il portale di Azure.Complete the remaining steps to create the cluster as described in [Create Apache Kafka cluster in Azure HDInsight using Azure portal.](https://docs.microsoft.com/azure/hdinsight/kafka/apache-kafka-get-started)

1. Dopo aver creato il cluster, passare alle proprietà del cluster per registrare l'URL del proxy REST Kafka.

     ![visualizzare l'URL del proxy REST](./media/rest-proxy/apache-kafka-rest-proxy-view-proxy-url.png)

## <a name="client-application-sample"></a>Esempio di applicazione client

È possibile usare il codice python riportato di seguito per interagire con il proxy REST nel cluster Kafka.You can use the python code below to interact with the REST proxy on your Kafka cluster. Per utilizzare l'esempio di codice, attenersi alla seguente procedura:

1. Salvare il codice di esempio in un computer in cui è installato Python.Save the sample code on a machine with Python installed.
1. Installare le dipendenze python `pip3 install adal` `pip install msrestazure`necessarie eseguendo e .
1. Modificare la sezione di codice *Configurare queste proprietà* e aggiornare le proprietà seguenti per l'ambiente:
    1.  *ID tenant:* tenant di Azure in cui si trova la sottoscrizione.
    1.  *ID client:* ID dell'applicazione registrata nel gruppo di sicurezza.
    1.  *Segreto client:* segreto per l'applicazione registrata nel gruppo di sicurezza
    1.  *Kafkarest_endpoint:* ottenere questo valore dalla scheda "proprietà" della panoramica del cluster, come descritto nella sezione relativa alla [distribuzione.](#create-a-kafka-cluster-with-rest-proxy-enabled) Dovrebbe essere nel seguente formato –`https://<clustername>-kafkarest.azurehdinsight.net`
3. Dalla riga di comando, eseguire il file python eseguendo`python <filename.py>`

Il codice esegue le attività seguenti:

1. Recupera un token OAuth da Azure ADFetches an OAuth token from Azure AD
1. Viene illustrato come effettuare una richiesta al proxy REST Kafka

Per ulteriori informazioni su come ottenere i token OAuth in python, vedere [Python AuthenticationContext class](https://docs.microsoft.com/python/api/adal/adal.authentication_context.authenticationcontext?view=azure-python). È possibile che venga visualizzato un ritardo, mentre gli argomenti non creati o eliminati tramite il proxy REST Kafka vengono riflessi. Questo ritardo è dovuto all'aggiornamento della cache.

```python
#Required python packages
#pip3 install adal
#pip install msrestazure

import adal
from msrestazure.azure_active_directory import AdalAuthentication
from msrestazure.azure_cloud import AZURE_PUBLIC_CLOUD
import requests

#--------------------------Configure these properties-------------------------------#
# Tenant ID for your Azure Subscription
tenant_id = 'ABCDEFGH-1234-1234-1234-ABCDEFGHIJKL'
# Your Client Application Id
client_id = 'XYZABCDE-1234-1234-1234-ABCDEFGHIJKL'
# Your Client Credentials
client_secret = 'password'
# kafka rest proxy -endpoint
kafkarest_endpoint = "https://<clustername>-kafkarest.azurehdinsight.net"
#--------------------------Configure these properties-------------------------------#

#getting token
login_endpoint = AZURE_PUBLIC_CLOUD.endpoints.active_directory
resource = "https://hib.azurehdinsight.net"
context = adal.AuthenticationContext(login_endpoint + '/' + tenant_id)

token = context.acquire_token_with_client_credentials(
    resource,
    client_id,
    client_secret)

accessToken = 'Bearer ' + token['accessToken']

print(accessToken)

# relative url
getstatus = "/v1/metadata/topics"
request_url = kafkarest_endpoint + getstatus

# sending get request and saving the response as response object
response = requests.get(request_url, headers={'Authorization': accessToken})
print(response.content)
```

## <a name="next-steps"></a>Passaggi successivi

* [Documenti di riferimento dell'API proxy REST KafkaKafkafKAfkaf REST proxy API reference documents](https://docs.microsoft.com/rest/api/hdinsight-kafka-rest-proxy/)
