---
title: Eseguire la migrazione agli hub eventi di Azure per Apache KafkaMigrate to Azure Event Hubs for Apache Kafka
description: Questo articolo illustra in che modo i consumer e i producer che usano protocolli diversi (AMQP, Apache Kafka e HTTPS) possono scambiare eventi quando usano Hub eventi di Azure.
services: event-hubs
documentationcenter: ''
author: ShubhaVijayasarathy
manager: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/01/2020
ms.author: shvija
ms.openlocfilehash: 32b08e565b86af8f6373c9848211646128bb346d
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81677359"
---
# <a name="migrate-to-azure-event-hubs-for-apache-kafka-ecosystems"></a>Eseguire la migrazione agli hub eventi di Azure per gli ecosistemi Apache KafkaMigrate to Azure Event Hubs for Apache Kafka Ecosystems
Hub eventi di Azure espone un endpoint Apache Kafka, che consente di connettersi agli hub eventi usando il protocollo Kafka.Azure Event Hubs exposes an Apache Kafka endpoint, which enables you to connect to Event Hubs using the Kafka protocol. Apportando modifiche minime all'applicazione Kafka esistente, è possibile connettersi agli hub eventi di Azure e sfruttare i vantaggi dell'ecosistema di Azure.By making minimal changes to your existing Kafka application, you can connect to Azure Event Hubs and reap the benefits of the Azure ecosystem. Gli hub eventi per Kafka supportano [Apache Kafka versione 1.0](https://kafka.apache.org/10/documentation.html) e successive.

## <a name="pre-migration"></a>Pre-migrazione 

### <a name="create-an-azure-account"></a>Creare un account Azure
Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) prima di iniziare.

### <a name="create-an-event-hubs-namespace"></a>Creare uno spazio dei nomi di Hub eventi
Seguire le istruzioni dettagliate nell'articolo [Creare un hub eventi](event-hubs-create.md) per creare uno spazio dei nomi Hub eventi e un hub eventi. 

### <a name="connection-string"></a>Stringa di connessione
Seguire i passaggi descritti nell'articolo Ottenere la stringa di [connessione dal portale.](event-hubs-get-connection-string.md#get-connection-string-from-the-portal) E, annotare la stringa di connessione per un uso successivo. 

### <a name="fully-qualified-domain-name-fqdn"></a>Nome di dominio completo (FQDN)
Potrebbe essere necessario anche il nome di dominio completo che punta allo spazio dei nomi dell'hub eventi. Il nome di dominio completo è disponibile all'interno della stringa di connessione come indicato di seguito:The FQDN can be found within your connection string as follows:

`Endpoint=sb://`**`mynamespace.servicebus.windows.net`**`/;SharedAccessKeyName=XXXXXX;SharedAccessKey=XXXXXX`

Se lo spazio dei nomi Hub eventi viene distribuito in un cloud \*non \*pubblico, il \*nome di dominio potrebbe essere diverso (ad esempio, servicebus.chinacloudapi.cn, servicebus.usgovcloudapi.net o .servicebus.cloudapi.de).

## <a name="migration"></a>Migrazione 

### <a name="update-your-kafka-client-configuration"></a>Aggiornare la configurazione del client Kafka

Per connettersi a un hub eventi abilitato per Kafka, è necessario aggiornare le configurazioni del client Kafka. Se hai problemi a trovare il tuo, `bootstrap.servers` prova a cercare dove è impostato nell'applicazione.

Inserire le configurazioni seguenti ovunque abbia senso nell'applicazione. Assicurarsi di `bootstrap.servers` aggiornare i valori e `sasl.jaas.config` per indirizzare il client all'endpoint Kafka degli hub eventi con l'autenticazione corretta. 

```
bootstrap.servers={MYNAMESPACE}.servicebus.windows.net:9093
request.timeout.ms=60000
security.protocol=SASL_SSL
sasl.mechanism=PLAIN
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{CONNECTION STRING TO YOUR NAMESPACE}";
``` 

Se `sasl.jaas.config` non è una configurazione supportata nel framework, individuare le configurazioni utilizzate per impostare il nome utente e la password SASL e usarli invece. Impostare il `$ConnectionString` nome utente su e la password sulla stringa di connessione hub eventi.

## <a name="post-migration"></a>Post-migrazione
Eseguire l'applicazione Kafka che invia eventi all'hub eventi. Verificare quindi che l'hub eventi riceva gli eventi tramite il portale di Azure.Then, verify that the event hub receives the events using the Azure portal. Nella pagina **Panoramica** dello spazio dei nomi Hub eventi passare alla visualizzazione **Messaggi** nella sezione **Metriche.** Aggiornare la pagina per aggiornare il grafico. La conferma che i messaggi sono stati ricevuti potrebbe comparire dopo alcuni secondi. 

[![Verificare se l'hub eventi ha ricevuto i messaggi](./media/getstarted-dotnet-standard-send-v2/verify-messages-portal.png)](./media/getstarted-dotnet-standard-send-v2/verify-messages-portal.png#lightbox)


## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni su Hub eventi e Hub eventi per Kafka, vedere gli articoli seguenti:  

- [Guida alla risoluzione dei problemi di Apache Kafka per gli hub eventi](apache-kafka-troubleshooting-guide.md)
- [Domande frequenti - Hub di eventi per Apache Kafka](apache-kafka-frequently-asked-questions.md)
- [Apache Kafka developer guide for Azure Event Hubs](apache-kafka-developer-guide.md)
- [Configurazioni consigliate](https://github.com/Azure/azure-event-hubs-for-kafka/blob/master/CONFIGURATION.md)