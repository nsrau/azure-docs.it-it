---
title: Proxy REST Apache Kafka - Azure HDInsight
description: Informazioni su come eseguire operazioni di Apache Kafka usando un proxy REST Kafka in Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: hrasheed
ms.service: hdinsight
ms.topic: how-to
ms.custom: has-adal-ref, devx-track-python
ms.date: 04/03/2020
ms.openlocfilehash: 57c2fb125547149a7fea6643a483e29f5fecb495
ms.sourcegitcommit: c28fc1ec7d90f7e8b2e8775f5a250dd14a1622a6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/13/2020
ms.locfileid: "88167046"
---
# <a name="interact-with-apache-kafka-clusters-in-azure-hdinsight-using-a-rest-proxy"></a>Interagire con cluster di Apache Kafka in Azure HDInsight usando un proxy REST

Il proxy REST Kafka consente di interagire con il cluster Kafka attraverso un'API REST tramite HTTP. Questa azione indica che i client Kafka possono trovarsi all'esterno della rete virtuale. I client possono eseguire semplici chiamate HTTP al cluster Kafka, invece di basarsi sulle librerie Kafka. Questo articolo illustra come creare un cluster Kafka abilitato per il proxy REST. Fornisce inoltre un esempio di codice che illustra come effettuare chiamate al proxy REST.

## <a name="rest-api-reference"></a>Informazioni di riferimento sulle API REST

Per le operazioni supportate dall'API REST Kafka, vedere le [informazioni di riferimento sull'API del proxy REST Kafka in HDInsight](https://docs.microsoft.com/rest/api/hdinsight-kafka-rest-proxy).

## <a name="background"></a>Background

![Struttura del proxy REST Kafka](./media/rest-proxy/rest-proxy-architecture.png)

Per la specifica completa delle operazioni supportate dall'API, vedere [API del proxy REST Apache Kafka](https://docs.microsoft.com/rest/api/hdinsight-kafka-rest-proxy).

### <a name="rest-proxy-endpoint"></a>Endpoint proxy REST

Quando si crea un cluster Kafka HDInsight con il proxy REST, viene creato un nuovo endpoint pubblico per il cluster, che è disponibile nelle **proprietà** del cluster HDInsight nel portale di Azure.

### <a name="security"></a>Sicurezza

L'accesso al proxy REST Kafka viene gestito con i gruppi di sicurezza di Azure Active Directory. Durante la creazione del cluster Kafka, fornire il gruppo di sicurezza di Azure AD con accesso all'endpoint REST. I client Kafka che devono accedere al proxy REST devono essere registrati in questo gruppo dal proprietario del gruppo. Il proprietario del gruppo può eseguire la registrazione tramite il portale o PowerShell.

Per le richieste di endpoint proxy REST, le applicazioni client devono ottenere un token OAuth. Il token viene usato per verificare l'appartenenza ai gruppi di sicurezza. Di seguito viene fornito un [esempio di applicazione client](#client-application-sample) che mostra come ottenere un token OAuth. L'applicazione client passa al proxy REST il token OAuth presente nella richiesta HTTP.

> [!NOTE]
> Per altre informazioni sui gruppi di sicurezza di AAD, vedere [Gestire le app e l'accesso alle risorse tramite i gruppi di Azure Active Directory](../../active-directory/fundamentals/active-directory-manage-groups.md). Per altre informazioni sul funzionamento dei token OAuth, vedere [Autorizzare l'accesso ad applicazioni Web di Azure Active Directory mediante il flusso di concessione di OAuth 2.0](../../active-directory/develop/v1-protocols-oauth-code.md).

## <a name="kafka-rest-proxy-with-network-security-groups"></a>Proxy REST Kafka con gruppi di sicurezza di rete
Se si porta la propria rete virtuale e si controlla il traffico di rete con gruppi di sicurezza di rete, consentire il traffico **in ingresso** sulla porta **9400** oltre alla porta 443. In questo modo si garantisce che il server proxy REST Kafka sia raggiungibile.

## <a name="prerequisites"></a>Prerequisiti

1. Registrare un'applicazione con Azure AD. Le applicazioni client scritte per interagire con il proxy REST Kafka useranno l'ID e il segreto di questa applicazione per l'autenticazione con Azure.

1. Creare un gruppo di sicurezza di Azure AD. Aggiungere l'applicazione registrata con Azure AD al gruppo di sicurezza come **membro** del gruppo. Questo gruppo di sicurezza verrà usato per controllare quali applicazioni sono autorizzate a interagire con il proxy REST. Per ulteriori informazioni sulla creazione di gruppi di Azure AD, vedere [Creare un gruppo di base e aggiungere membri con Azure Active Directory](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

    Verificare che il gruppo sia di tipo **Sicurezza**.
    ![Gruppo di sicurezza](./media/rest-proxy/rest-proxy-group.png)

    Verificare che l'applicazione sia membro del gruppo.
    ![Verificare l'appartenenza](./media/rest-proxy/rest-proxy-membergroup.png)

## <a name="create-a-kafka-cluster-with-rest-proxy-enabled"></a>Creare un cluster Kafka con proxy REST abilitato

Nella procedura seguente si usa il portale di Azure. Per un esempio in cui viene usata l'interfaccia della riga di comando di Azure, vedere [Creare il cluster del proxy REST Apache Kafka con l'interfaccia della riga di comando di Azure](tutorial-cli-rest-proxy.md).

1. Durante il flusso di lavoro di creazione del cluster Kafka nella scheda **Sicurezza + rete** selezionare l'opzione **Abilitare il proxy REST Kafka**.

     ![Abilitare il proxy REST Kafka e selezionare il gruppo di sicurezza](./media/rest-proxy/azure-portal-cluster-security-networking-kafka-rest.png)

1. Fare clic su **Seleziona il gruppo di sicurezza**. Nell'elenco dei gruppi di sicurezza selezionare il gruppo di sicurezza che deve accedere al proxy REST. È possibile usare la casella di ricerca per trovare il gruppo di sicurezza appropriato. Fare clic sul pulsante **Seleziona** in basso.

     ![Abilitare il proxy REST Kafka e selezionare il gruppo di sicurezza](./media/rest-proxy/azure-portal-cluster-security-networking-kafka-rest2.png)

1. Completare i passaggi rimanenti per creare il cluster come descritto in [Creare il cluster Apache Kafka in Azure HDInsight con il portale di Azure](https://docs.microsoft.com/azure/hdinsight/kafka/apache-kafka-get-started).

1. Dopo aver creato il cluster, passare alle proprietà del cluster per prendere nota dell'URL del proxy REST Kafka.

     ![Visualizzare l'URL del proxy REST](./media/rest-proxy/apache-kafka-rest-proxy-view-proxy-url.png)

## <a name="client-application-sample"></a>Esempio di applicazione client

È possibile usare il codice Python seguente per interagire con il proxy REST nel cluster Kafka. Per eseguire il codice di esempio, seguire questa procedura:

1. Salvare il codice di esempio in un computer in cui è installato Python.
1. Installare le dipendenze di Python obbligatorie eseguendo `pip3 install msal`.
1. Modificare la sezione di codice **Configure these properties** e aggiornare le proprietà seguenti per l'ambiente:

    |Proprietà |Descrizione |
    |---|---|
    |tenant_id|Tenant di Azure in cui si trova la sottoscrizione.|
    |client_id|ID dell'applicazione registrata nel gruppo di sicurezza.|
    |client_secret|Segreto per l'applicazione registrata nel gruppo di sicurezza.|
    |kafkarest_endpoint|Questo valore è indicato nella scheda **Proprietà** della panoramica del cluster, come descritto nella [sezione relativa alla distribuzione](#create-a-kafka-cluster-with-rest-proxy-enabled). Deve essere specificato nel formato seguente: `https://<clustername>-kafkarest.azurehdinsight.net`|

1. Dalla riga di comando eseguire il file Python con il comando `sudo python3 <filename.py>`

Il codice esegue le azioni seguenti:

1. Recupera un token OAuth da Azure AD.
1. Mostra come effettuare una richiesta al proxy REST Kafka.

Per altre informazioni su come ottenere i token OAuth in Python, vedere [Classe AuthenticationContext di Python](https://docs.microsoft.com/python/api/adal/adal.authentication_context.authenticationcontext?view=azure-python). È possibile notare un ritardo nella visualizzazione degli elementi `topics` non creati o eliminati tramite il proxy REST Kafka. Questo ritardo è dovuto all'aggiornamento della cache.

```python
#Required python packages
#pip3 install msal

import msal
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
response = requests.get(request_url, headers={'Authorization': 'Bearer ' + 'accessToken})
print(response.content)
```

Ecco un altro esempio su come ottenere un token da Azure per il proxy REST usando un comando curl. **Tenere presente che è necessario specificare `scope=https://hib.azurehdinsight.net/.default` durante il recupero di un token.**

```cmd
curl -X POST -H "Content-Type: application/x-www-form-urlencoded" -d 'client_id=<clientid>&client_secret=<clientsecret>&grant_type=client_credentials&scope=https://hib.azurehdinsight.net/.default' 'https://login.microsoftonline.com/<tenantid>/oauth2/v2.0/token'
```

## <a name="next-steps"></a>Passaggi successivi

* [Documentazione di riferimento dell'API del proxy REST Kafka](https://docs.microsoft.com/rest/api/hdinsight-kafka-rest-proxy/)
