---
title: Proxy REST di Apache Kafka-Azure HDInsight
description: Informazioni su come eseguire operazioni di Apache Kafka usando un proxy REST Kafka in Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: hrasheed
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/03/2020
ms.custom: has-adal-ref
ms.openlocfilehash: affdbfba125b7e9b3f3fe250a56af30e9efe816e
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/30/2020
ms.locfileid: "82611007"
---
# <a name="interact-with-apache-kafka-clusters-in-azure-hdinsight-using-a-rest-proxy"></a>Interagire con i cluster di Apache Kafka in Azure HDInsight usando un proxy REST

Il proxy REST Kafka consente di interagire con il cluster Kafka tramite un'API REST su HTTP. Questa azione significa che i client Kafka possono trovarsi all'esterno della rete virtuale. I client possono eseguire semplici chiamate HTTP al cluster Kafka, invece di basarsi sulle librerie Kafka. Questo articolo illustra come creare un cluster Kafka abilitato per il proxy REST. Fornisce inoltre un codice di esempio che illustra come effettuare chiamate al proxy REST.

## <a name="rest-api-reference"></a>Informazioni di riferimento sulle API REST

Per le operazioni supportate dall'API REST Kafka, vedere informazioni di [riferimento sull'API del proxy REST di HDInsight Kafka](https://docs.microsoft.com/rest/api/hdinsight-kafka-rest-proxy).

## <a name="background"></a>Background

![Progettazione del proxy REST Kafka](./media/rest-proxy/rest-proxy-architecture.png)

Per la specifica completa delle operazioni supportate dall'API, vedere [Apache Kafka API del proxy Rest](https://docs.microsoft.com/rest/api/hdinsight-kafka-rest-proxy).

### <a name="rest-proxy-endpoint"></a>Endpoint proxy REST

La creazione di un cluster HDInsight Kafka con il proxy REST crea un nuovo endpoint pubblico per il cluster, che è possibile trovare nelle **Proprietà** del cluster HDInsight nel portale di Azure.

### <a name="security"></a>Sicurezza

L'accesso al proxy REST Kafka viene gestito con Azure Active Directory gruppi di sicurezza. Quando si crea il cluster Kafka, fornire il Azure AD gruppo di sicurezza con l'accesso all'endpoint REST. I client Kafka che devono accedere al proxy REST devono essere registrati in questo gruppo dal proprietario del gruppo. Il proprietario del gruppo può registrarsi tramite il portale o tramite PowerShell.

Per le richieste di endpoint proxy REST, le applicazioni client devono ottenere un token OAuth. Il token viene usato per verificare l'appartenenza ai gruppi di sicurezza. Trovare un [esempio di applicazione client](#client-application-sample) di seguito che Mostra come ottenere un token OAuth. L'applicazione client passa il token OAuth nella richiesta HTTP al proxy REST.

> [!NOTE]
> Per altre informazioni sui gruppi di sicurezza di AAD, vedere [gestire l'accesso alle risorse e alle app usando gruppi di Azure Active Directory](../../active-directory/fundamentals/active-directory-manage-groups.md). Per altre informazioni sul funzionamento dei token OAuth, vedere [autorizzare l'accesso alle applicazioni web Azure Active Directory usando il flusso di concessione del codice OAuth 2,0](../../active-directory/develop/v1-protocols-oauth-code.md).

## <a name="prerequisites"></a>Prerequisiti

1. Registrare un'applicazione con Azure AD. Le applicazioni client scritte per interagire con il proxy REST Kafka useranno l'ID e il segreto dell'applicazione per l'autenticazione in Azure.

1. Creare un gruppo di sicurezza Azure AD. Aggiungere l'applicazione registrata con Azure AD al gruppo di sicurezza come **membro** del gruppo. Questo gruppo di sicurezza verrà usato per controllare quali applicazioni possono interagire con il proxy REST. Per ulteriori informazioni sulla creazione di gruppi di Azure AD, vedere [creare un gruppo di base e aggiungere membri utilizzando Azure Active Directory](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

    Verificare che il gruppo sia di tipo **sicurezza**.
    ![Gruppo di sicurezza](./media/rest-proxy/rest-proxy-group.png)

    Verificare che l'applicazione sia membro del gruppo.
    ![Verifica appartenenza](./media/rest-proxy/rest-proxy-membergroup.png)

## <a name="create-a-kafka-cluster-with-rest-proxy-enabled"></a>Creare un cluster Kafka con il proxy REST abilitato

1. Durante il flusso di lavoro di creazione del cluster Kafka, nella scheda **Security + networking** selezionare l'opzione **Enable Kafka Rest proxy** .

     ![Abilitare il proxy REST Kafka e selezionare il gruppo di sicurezza](./media/rest-proxy/azure-portal-cluster-security-networking-kafka-rest.png)

1. Fare clic su **Seleziona gruppo di sicurezza**. Dall'elenco dei gruppi di sicurezza selezionare il gruppo di sicurezza a cui si vuole accedere al proxy REST. È possibile utilizzare la casella di ricerca per trovare il gruppo di sicurezza appropriato. Fare clic sul pulsante **Seleziona** nella parte inferiore.

     ![Abilitare il proxy REST Kafka e selezionare il gruppo di sicurezza](./media/rest-proxy/azure-portal-cluster-security-networking-kafka-rest2.png)

1. Completare i passaggi rimanenti per creare il cluster come descritto in [creare Apache Kafka cluster in Azure HDInsight usando portale di Azure](https://docs.microsoft.com/azure/hdinsight/kafka/apache-kafka-get-started).

1. Una volta creato il cluster, passare alle proprietà del cluster per registrare l'URL del proxy REST Kafka.

     ![Visualizza URL proxy REST](./media/rest-proxy/apache-kafka-rest-proxy-view-proxy-url.png)

## <a name="client-application-sample"></a>Esempio di applicazione client

È possibile usare il codice Python riportato di seguito per interagire con il proxy REST nel cluster Kafka. Per utilizzare l'esempio di codice, attenersi alla seguente procedura:

1. Salvare il codice di esempio in un computer in cui è installato Python.
1. Installare le dipendenze di Python necessarie `pip3 install msal`eseguendo.
1. Modificare la sezione di codice **configurare queste proprietà** e aggiornare le proprietà seguenti per l'ambiente:

    |Proprietà |Descrizione |
    |---|---|
    |ID tenant|Il tenant di Azure in cui si trova la sottoscrizione.|
    |ID client|ID dell'applicazione registrata nel gruppo di sicurezza.|
    |Client Secret|Il segreto per l'applicazione registrata nel gruppo di sicurezza.|
    |Kafkarest_endpoint|Ottenere questo valore dalla scheda **Proprietà** nella panoramica del cluster, come descritto nella [sezione relativa alla distribuzione](#create-a-kafka-cluster-with-rest-proxy-enabled). Deve avere il formato seguente:`https://<clustername>-kafkarest.azurehdinsight.net`|

1. Dalla riga di comando eseguire il file Python eseguendo`sudo python3 <filename.py>`

Questo codice esegue l'azione seguente:

1. Recupera un token OAuth da Azure AD.
1. Mostra come effettuare una richiesta al proxy REST di Kafka.

Per altre informazioni su come ottenere i token OAuth in Python, vedere [Classe AuthenticationContext di Python](https://docs.microsoft.com/python/api/adal/adal.authentication_context.authenticationcontext?view=azure-python). È possibile che venga visualizzato un `topics` ritardo durante la creazione o l'eliminazione tramite il proxy Rest Kafka. Questo ritardo è dovuto all'aggiornamento della cache.

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

Trovare sotto un altro esempio su come ottenere un token da Azure per il proxy REST usando un comando curl. **Si noti che è necessario `scope=https://hib.azurehdinsight.net/.default` l'oggetto specificato durante il recupero di un token.**

```cmd
curl -X POST -H "Content-Type: application/x-www-form-urlencoded" -d 'client_id=<clientid>&client_secret=<clientsecret>&grant_type=client_credentials&scope=https://hib.azurehdinsight.net/.default' 'https://login.microsoftonline.com/<tenantid>/oauth2/v2.0/token'
```

## <a name="next-steps"></a>Passaggi successivi

* [Documenti di riferimento dell'API del proxy REST Kafka](https://docs.microsoft.com/rest/api/hdinsight-kafka-rest-proxy/)
