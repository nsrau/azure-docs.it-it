---
title: Proxy REST di Apache Kafka-Azure HDInsight
description: Informazioni su come eseguire operazioni di Apache Kafka usando un proxy REST Kafka in Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: hrasheed
ms.service: hdinsight
ms.topic: conceptual
ms.date: 12/17/2019
ms.openlocfilehash: b53fc3af71ce872c9ca9f513139c8179fd4165ed
ms.sourcegitcommit: f0f73c51441aeb04a5c21a6e3205b7f520f8b0e1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/05/2020
ms.locfileid: "77031399"
---
# <a name="interact-with-apache-kafka-clusters-in-azure-hdinsight-using-a-rest-proxy"></a>Interagire con i cluster di Apache Kafka in Azure HDInsight usando un proxy REST

Il proxy REST Kafka consente di interagire con il cluster Kafka tramite un'API REST su HTTP. Ciò significa che i client Kafka possono trovarsi all'esterno della rete virtuale. Inoltre, i client possono eseguire semplici chiamate HTTP per inviare e ricevere messaggi al cluster Kafka, invece di basarsi sulle librerie Kafka.  

## <a name="background"></a>Background

### <a name="architecture"></a>Architettura

Senza un proxy REST, i client Kafka devono trovarsi nella stessa VNet del cluster Kafka o di un VNet con peering. Il proxy REST consente di connettere Producer o consumer di dati che si trovano ovunque. La distribuzione del proxy REST crea un nuovo endpoint pubblico per il cluster, che è possibile trovare nelle impostazioni del portale.

Per la specifica completa delle operazioni supportate dall'API, vedere [Apache Kafka API del proxy Rest](https://docs.microsoft.com/rest/api/hdinsight-kafka-rest-proxy).

### <a name="security"></a>Sicurezza

L'accesso al proxy REST Kafka viene gestito con Azure Active Directory gruppi di sicurezza. Per altre informazioni, vedere [gestire l'accesso alle app e alle risorse usando gruppi di Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-manage-groups).

Quando si crea il cluster Kafka con il proxy REST abilitato, verrà fornito il gruppo di sicurezza AAD che dovrebbe avere accesso all'endpoint REST. I client Kafka (applicazioni) che necessitano dell'accesso al proxy REST devono essere registrati in questo gruppo dal proprietario del gruppo. Il proprietario del gruppo può eseguire questa operazione tramite il portale o tramite PowerShell.

Prima di effettuare richieste all'endpoint proxy REST, l'applicazione client deve ottenere un token OAuth per verificare l'appartenenza del gruppo di sicurezza corretto. Per altre informazioni sul funzionamento dei token OAuth, vedere [autorizzare l'accesso alle applicazioni web Azure Active Directory usando il flusso di concessione del codice OAuth 2,0](../../active-directory/develop/v1-protocols-oauth-code.md). Per un esempio di recupero di un token OAuth in Python, vedere [esempio di applicazione client](#client-application-sample)

Quando l'applicazione client ha il token OAuth, deve passare il token nella richiesta HTTP effettuata al proxy REST.

## <a name="prerequisites"></a>Prerequisiti

1. Registrare un'applicazione con Azure AD. Le applicazioni client scritte per interagire con il proxy REST Kafka useranno l'ID e il segreto dell'applicazione per l'autenticazione in Azure.
1. Creare un gruppo di sicurezza Azure AD e aggiungere l'applicazione registrata con Azure AD al gruppo di sicurezza. Questo gruppo di sicurezza verrà usato per controllare quali applicazioni possono interagire con il proxy REST. Per ulteriori informazioni sulla creazione di gruppi di Azure AD, vedere [creare un gruppo di base e aggiungere membri utilizzando Azure Active Directory](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

## <a name="create-a-kafka-cluster-with-rest-proxy-enabled"></a>Creare un cluster Kafka con il proxy REST abilitato

1. Durante il flusso di lavoro di creazione del cluster Kafka, nella scheda "sicurezza e rete" selezionare l'opzione "Abilita proxy REST Kafka".

     ![Abilitare il proxy REST Kafka e selezionare il gruppo di sicurezza](./media/rest-proxy/azure-portal-cluster-security-networking-kafka-rest.png)

1. Fare clic su **Seleziona gruppo di sicurezza**. Dall'elenco dei gruppi di sicurezza selezionare il gruppo di sicurezza a cui si vuole accedere al proxy REST. È possibile utilizzare la casella di ricerca per trovare il gruppo di sicurezza appropriato. Fare clic sul pulsante **Seleziona** nella parte inferiore.

     ![Abilitare il proxy REST Kafka e selezionare il gruppo di sicurezza](./media/rest-proxy/azure-portal-cluster-security-networking-kafka-rest2.png)

1. Completare i passaggi rimanenti per creare il cluster come descritto in [creare Apache Kafka cluster in Azure HDInsight usando portale di Azure](https://docs.microsoft.com/azure/hdinsight/kafka/apache-kafka-get-started).

1. Una volta creato il cluster, passare alle proprietà del cluster per registrare l'URL del proxy REST Kafka.

     ![Visualizza URL proxy REST](./media/rest-proxy/apache-kafka-rest-proxy-view-proxy-url.png)

## <a name="client-application-sample"></a>Esempio di applicazione client

È possibile usare il codice Python riportato di seguito per interagire con il proxy REST nel cluster Kafka. Il codice esegue le attività seguenti:

1. Recupera un token OAuth da Azure AD
1. Crea l'argomento specificato
1. Invia messaggi all'argomento
1. Utilizza i messaggi di questo argomento

Per altre informazioni su come ottenere i token OAuth in Python, vedere [Classe AuthenticationContext di Python](https://docs.microsoft.com/python/api/adal/adal.authentication_context.authenticationcontext?view=azure-python). È possibile che venga visualizzato un ritardo mentre gli argomenti non creati o eliminati tramite il proxy REST Kafka vengono riflessi in tale posizione. Questo ritardo è dovuto all'aggiornamento della cache.

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

Per utilizzare l'esempio di codice, attenersi alla seguente procedura:

1. Salvare il codice di esempio in un computer in cui è installato Python.
1. Installare le dipendenze di Python necessarie eseguendo `pip3 install adal` e `pip install msrestazure`.
1. Modificare il codice e aggiornare le proprietà seguenti per l'ambiente:
    1.  *ID tenant* : il tenant di Azure in cui si trova la sottoscrizione.
    1.  *ID client* : l'ID dell'applicazione registrata nel gruppo di sicurezza.
    1.  *Segreto client* : il segreto per l'applicazione registrata nel gruppo di sicurezza
    1.  *Kafkarest_endpoint* : ottenere questo valore dalla scheda "Properties" (proprietà) nella panoramica del cluster, come descritto nella [sezione relativa alla distribuzione](#create-a-kafka-cluster-with-rest-proxy-enabled). Il formato deve essere il seguente: `https://<clustername>-kafkarest.azurehdinsight.net`
3. Dalla riga di comando eseguire il file Python eseguendo `python <filename.py>`

## <a name="next-steps"></a>Passaggi successivi

* [Documenti di riferimento dell'API del proxy REST Kafka](https://docs.microsoft.com/rest/api/hdinsight-kafka-rest-proxy/)
* [Esercitazione: usare le API Producer e consumer di Apache Kafka](apache-kafka-producer-consumer-api.md)