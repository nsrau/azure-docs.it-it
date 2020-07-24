---
title: Eseguire la migrazione a hub eventi di Azure per Apache Kafka
description: Questo articolo illustra in che modo i consumer e i producer che usano protocolli diversi (AMQP, Apache Kafka e HTTPS) possono scambiare eventi quando usano Hub eventi di Azure.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: cf585cdc0a43c595ec013ba78a2e3ae191c40b8d
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87090655"
---
# <a name="migrate-to-azure-event-hubs-for-apache-kafka-ecosystems"></a>Eseguire la migrazione a Hub eventi di Azure per ecosistemi Apache Kafka
Hub eventi di Azure espone un endpoint Apache Kafka, che consente di connettersi a hub eventi usando il protocollo Kafka. Apportando modifiche minime all'applicazione Kafka esistente, è possibile connettersi a hub eventi di Azure e sfruttare i vantaggi dell'ecosistema di Azure. Hub eventi per il supporto Kafka [Apache Kafka versione 1,0](https://kafka.apache.org/10/documentation.html) e successive.

## <a name="pre-migration"></a>Pre-migrazione 

### <a name="create-an-azure-account"></a>Creare un account Azure
Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) prima di iniziare.

### <a name="create-an-event-hubs-namespace"></a>Creare uno spazio dei nomi di Hub eventi
Seguire le istruzioni dettagliate riportate nell'articolo [creare un hub eventi](event-hubs-create.md) per creare uno spazio dei nomi di hub eventi e un hub eventi. 

### <a name="connection-string"></a>Stringa di connessione
Seguire i passaggi dell'articolo [ottenere la stringa di connessione dal portale](event-hubs-get-connection-string.md#get-connection-string-from-the-portal) . Prendere nota della stringa di connessione per usarla in seguito. 

### <a name="fully-qualified-domain-name-fqdn"></a>Nome di dominio completo (FQDN)
Potrebbe anche essere necessario il nome di dominio completo che punta allo spazio dei nomi dell'hub eventi. Il nome di dominio completo è disponibile all'interno della stringa di connessione come indicato di seguito:

`Endpoint=sb://`**`mynamespace.servicebus.windows.net`**`/;SharedAccessKeyName=XXXXXX;SharedAccessKey=XXXXXX`

Se lo spazio dei nomi di hub eventi viene distribuito in un cloud non pubblico, il nome di dominio può variare (ad esempio, \* . ServiceBus.chinacloudapi.cn, \* . ServiceBus.usgovcloudapi.NET o \* . ServiceBus.cloudapi.de).

## <a name="migration"></a>Migrazione 

### <a name="update-your-kafka-client-configuration"></a>Aggiornare la configurazione del client Kafka

Per connettersi a un hub eventi abilitato per Kafka, è necessario aggiornare le configurazioni del client Kafka. Se si verificano problemi durante la ricerca, provare a cercare dove `bootstrap.servers` è impostato nell'applicazione.

Inserire le configurazioni seguenti laddove è opportuno nell'applicazione. Assicurarsi di aggiornare i `bootstrap.servers` valori e `sasl.jaas.config` per indirizzare il client all'endpoint Kafka di hub eventi con l'autenticazione corretta. 

```
bootstrap.servers={MYNAMESPACE}.servicebus.windows.net:9093
request.timeout.ms=60000
security.protocol=SASL_SSL
sasl.mechanism=PLAIN
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{CONNECTION STRING TO YOUR NAMESPACE}";
``` 

Se `sasl.jaas.config` non è una configurazione supportata nel Framework, trovare le configurazioni usate per impostare il nome utente e la password di SASL e usarle in alternativa. Impostare il nome utente su `$ConnectionString` e la password per la stringa di connessione di hub eventi.

## <a name="post-migration"></a>Post-migrazione
Eseguire l'applicazione Kafka che invia gli eventi all'hub eventi. Verificare quindi che l'hub eventi riceva gli eventi usando il portale di Azure. Nella pagina **Panoramica** dello spazio dei nomi di hub eventi passare alla visualizzazione **messaggi** nella sezione **metrica** . Aggiornare la pagina per aggiornare il grafico. La conferma che i messaggi sono stati ricevuti potrebbe comparire dopo alcuni secondi. 

[![Verificare se l'hub eventi ha ricevuto i messaggi](./media/getstarted-dotnet-standard-send-v2/verify-messages-portal.png)](./media/getstarted-dotnet-standard-send-v2/verify-messages-portal.png#lightbox)


## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni su Hub eventi e Hub eventi per Kafka, vedere gli articoli seguenti:  

- [Guida alla risoluzione dei problemi di Apache Kafka per hub eventi](apache-kafka-troubleshooting-guide.md)
- [Domande frequenti-Hub eventi per Apache Kafka](apache-kafka-frequently-asked-questions.md)
- [Guida per gli sviluppatori Apache Kafka per hub eventi di Azure](apache-kafka-developer-guide.md)
- [Configurazioni consigliate](apache-kafka-configurations.md)