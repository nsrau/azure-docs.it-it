---
title: Apache Kafka REST proxy - Azure HDInsight
description: Informazioni su come eseguire operazioni Apache Kafka usando un proxy REST Kafka in Azure HDInsight.Learn how to do Apache Kafka operations using a Kafka REST proxy on Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: hrasheed
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/03/2020
ms.openlocfilehash: 265e15713f8159e370ef22a197ffe931200a88f7
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2020
ms.locfileid: "81758989"
---
# <a name="interact-with-apache-kafka-clusters-in-azure-hdinsight-using-a-rest-proxy"></a>Interagire con i cluster Apache Kafka in Azure HDInsight usando un proxy RESTInteract with Apache Kafka clusters in Azure HDInsight using a REST proxy

Il proxy REST Kafka consente di interagire con il cluster Kafka tramite un'API REST su HTTP. Questa azione significa che i client Kafka possono trovarsi all'esterno della rete virtuale. I client possono effettuare semplici chiamate HTTP al cluster Kafka, anziché affidarsi alle librerie Kafka. Questo articolo illustra come creare un cluster Kafka abilitato per il proxy REST. Fornisce anche un codice di esempio che illustra come effettuare chiamate al proxy REST.

## <a name="rest-api-reference"></a>Informazioni di riferimento sulle API REST

Per le operazioni supportate dall'API REST Kafka, vedere [HDInsight Kafka REST Proxy API Reference](https://docs.microsoft.com/rest/api/hdinsight-kafka-rest-proxy).

## <a name="background"></a>Background

![Progettazione proxy REST Kafka](./media/rest-proxy/rest-proxy-architecture.png)

Per la specifica completa delle operazioni supportate dall'API, vedere [Apache Kafka REST Proxy API](https://docs.microsoft.com/rest/api/hdinsight-kafka-rest-proxy).

### <a name="rest-proxy-endpoint"></a>Endpoint proxy REST

La creazione di un cluster Kafka HDInsight con proxy REST crea un nuovo endpoint pubblico per il cluster, che è possibile trovare nelle proprietà del cluster HDInsight nel portale di Azure.Creating an HDInsight Kafka cluster with REST proxy creates a new public endpoint for your cluster, which you can find in your HDInsight cluster **Properties** on the Azure portal.

### <a name="security"></a>Sicurezza

L'accesso al proxy REST Kafka viene gestito con i gruppi di sicurezza di Azure Active Directory.Access to the Kafka REST proxy is managed with Azure Active Directory security groups. Quando si crea il cluster Kafka, fornire al gruppo di sicurezza di Azure AD l'accesso all'endpoint REST. I client Kafka che devono accedere al proxy REST devono essere registrati in questo gruppo dal proprietario del gruppo. The group owner can register via the Portal or via PowerShell.

Per le richieste di endpoint proxy REST, le applicazioni client devono ottenere un token OAuth. Il token viene utilizzato per verificare l'appartenenza al gruppo di sicurezza. Trovare un esempio di [applicazione client](#client-application-sample) di seguito che mostra come ottenere un token OAuth. L'applicazione client passa il token OAuth nella richiesta HTTP al proxy REST.

> [!NOTE]  
> Per altre informazioni sui gruppi di sicurezza di AAD, vedere [Gestire l'accesso a app e risorse usando i gruppi](../../active-directory/fundamentals/active-directory-manage-groups.md)di Azure Active Directory.See Manage app and resource access using Azure Active Directory groups , to learn more about AAD security groups. Per altre informazioni sul funzionamento dei token OAuth, vedere [Autorizzare l'accesso alle applicazioni Web](../../active-directory/develop/v1-protocols-oauth-code.md)di Azure Active Directory usando il flusso di concessione del codice OAuth 2.0.For more information on how OAuth tokens work, see Authorize access to Azure Active Directory web applications using the OAuth 2.0 code grant flow .

## <a name="prerequisites"></a>Prerequisiti

1. Registrare un'applicazione con Azure AD. The client applications that you write to interact with the Kafka REST proxy will use this application's ID and secret to authenticate to Azure.

1. Creare un gruppo di sicurezza di Azure AD. Aggiungere l'applicazione registrata con Azure AD al gruppo di sicurezza come **membro** del gruppo. Questo gruppo di sicurezza verrà usato per controllare quali applicazioni sono autorizzate a interagire con il proxy REST. Per altre informazioni sulla creazione di gruppi di Azure AD, vedere [Creare un gruppo di base e aggiungere membri tramite Azure Active Directory.For](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)more information on creating Azure AD groups, see Create a basic group and add members using Azure Active Directory .

    Verificare che il gruppo sia di tipo **Sicurezza**.
    ![Gruppo di sicurezza](./media/rest-proxy/rest-proxy-group.png)

    Convalidare che l'applicazione sia membro di Group.Validate that application is member of Group.
    ![Controlla l'iscrizione](./media/rest-proxy/rest-proxy-membergroup.png)

## <a name="create-a-kafka-cluster-with-rest-proxy-enabled"></a>Creare un cluster Kafka con il proxy REST abilitatoCreate a Kafka cluster with REST proxy enabled

1. Durante il flusso di lavoro di creazione del cluster Kafka, nella scheda **Sicurezza e rete,** selezionare l'opzione **Abilita proxy REST Kafka.**

     ![Abilitare il proxy REST Kafka e selezionare il gruppo di sicurezzaEnable Kafka REST proxy and select security group](./media/rest-proxy/azure-portal-cluster-security-networking-kafka-rest.png)

1. Fare clic su **Seleziona gruppo di sicurezza**. Nell'elenco dei gruppi di sicurezza selezionare il gruppo di sicurezza a cui si desidera accedere al proxy REST. È possibile utilizzare la casella di ricerca per trovare il gruppo di sicurezza appropriato. Fare clic sul pulsante **Seleziona** nella parte inferiore.

     ![Abilitare il proxy REST Kafka e selezionare il gruppo di sicurezzaEnable Kafka REST proxy and select security group](./media/rest-proxy/azure-portal-cluster-security-networking-kafka-rest2.png)

1. Completare i passaggi rimanenti per creare il cluster come descritto in Creare un cluster Apache Kafka in Azure HDInsight usando il portale di Azure.Complete the remaining steps to create the cluster as described in [Create Apache Kafka cluster in Azure HDInsight using Azure portal.](https://docs.microsoft.com/azure/hdinsight/kafka/apache-kafka-get-started)

1. Dopo aver creato il cluster, passare alle proprietà del cluster per registrare l'URL del proxy REST Kafka.

     ![visualizzare l'URL del proxy REST](./media/rest-proxy/apache-kafka-rest-proxy-view-proxy-url.png)

## <a name="client-application-sample"></a>Esempio di applicazione client

È possibile usare il codice python riportato di seguito per interagire con il proxy REST nel cluster Kafka.You can use the python code below to interact with the REST proxy on your Kafka cluster. Per utilizzare l'esempio di codice, attenersi alla seguente procedura:

1. Salvare il codice di esempio in un computer in cui è installato Python.Save the sample code on a machine with Python installed.
1. Installare le dipendenze python `pip3 install msal`necessarie eseguendo .
1. Modificare la sezione di codice **Configurare queste proprietà** e aggiornare le proprietà seguenti per l'ambiente:

    |Proprietà |Descrizione |
    |---|---|
    |ID tenant|Il tenant di Azure in cui si trova la sottoscrizione.|
    |ID client|ID dell'applicazione registrata nel gruppo di sicurezza.|
    |Client Secret|Segreto per l'applicazione registrata nel gruppo di sicurezza.|
    |Kafkarest_endpoint|Ottenere questo valore dalla scheda **Proprietà** della panoramica del cluster, come descritto nella sezione relativa alla [distribuzione.](#create-a-kafka-cluster-with-rest-proxy-enabled) Dovrebbe essere nel seguente formato –`https://<clustername>-kafkarest.azurehdinsight.net`|

1. Dalla riga di comando, eseguire il file python eseguendo`sudo python3 <filename.py>`

Questo codice esegue l'azione seguente:This code does the following action:

1. Recupera un token OAuth da Azure AD.
1. Viene illustrato come effettuare una richiesta al proxy REST Kafka.

Per ulteriori informazioni su come ottenere i token OAuth in python, vedere [Python AuthenticationContext class](https://docs.microsoft.com/python/api/adal/adal.authentication_context.authenticationcontext?view=azure-python). È possibile che `topics` venga visualizzato un ritardo mentre non vengono creati o eliminati tramite il proxy REST Kafka vengono riflessi. Questo ritardo è dovuto all'aggiornamento della cache.

```python
#Required python packages
#pip3 install msal

import msal

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

# Scope
scope = 'https://hib.azurehdinsight.net/.default'
#Authority
authority = 'https://login.microsoftonline.com/' + tenant_id

# Create a preferably long-lived app instance which maintains a token cache.
app = msal.ConfidentialClientApplication(
    client_id , client_secret, authority,
    #cache - For details on how look at this example: https://github.com/Azure-Samples/ms-identity-python-webapp/blob/master/app.py
    )

# The pattern to acquire a token looks like this.
result = None

result = app.acquire_token_for_client(scopes=[scope])

print(result)
accessToken = result['access_token']

# relative url
getstatus = "/v1/metadata/topics"
request_url = kafkarest_endpoint + getstatus

# sending get request and saving the response as response object
response = requests.get(request_url, headers={'Authorization': accessToken})
print(response.content)
```

Trovare di seguito un altro esempio su come ottenere un token da Azure per il proxy REST usando un comando curl. **Si noti `scope=https://hib.azurehdinsight.net/.default` che è necessario il specificato durante il recupero di un token.**

```cmd
curl -X POST -H "Content-Type: application/x-www-form-urlencoded" -d 'client_id=<clientid>&client_secret=<clientsecret>&grant_type=client_credentials&scope=https://hib.azurehdinsight.net/.default' 'https://login.microsoftonline.com/<tenantid>/oauth2/v2.0/token'
```

## <a name="next-steps"></a>Passaggi successivi

* [Documenti di riferimento dell'API proxy REST KafkaKafkafKAfkaf REST proxy API reference documents](https://docs.microsoft.com/rest/api/hdinsight-kafka-rest-proxy/)
