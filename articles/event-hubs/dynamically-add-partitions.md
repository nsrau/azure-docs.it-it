---
title: Aggiungere in modo dinamico partizioni a un hub eventi in Hub eventi di Azure
description: Questo articolo illustra come aggiungere partizioni in modo dinamico a un hub eventi in Hub eventi di Azure.
ms.topic: how-to
ms.date: 06/23/2020
ms.openlocfilehash: ea0477dcc695c7a2fb936daadc3679c94bfac12f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85317948"
---
# <a name="dynamically-add-partitions-to-an-event-hub-apache-kafka-topic-in-azure-event-hubs"></a>Aggiungere in modo dinamico partizioni a un hub eventi (argomento Apache Kafka) in Hub eventi di Azure
Hub eventi fornisce lo streaming di messaggi tramite un modello consumer partizionato in cui ogni consumer legge solo un sottoinsieme specifico, o partizione, del flusso di messaggi. Questo modello consente la scalabilità orizzontale per l'elaborazione di eventi e fornisce altre funzionalità incentrate sul flusso non disponibili in code e argomenti. Una partizione è una sequenza ordinata di eventi contenuta in un hub eventi. Man mano che arrivano, i nuovi eventi vengono aggiunti alla fine di questa sequenza. Per altre informazioni sulle partizioni in generale, vedere [Partizioni](event-hubs-scalability.md#partitions)

È possibile specificare il numero di partizioni al momento della creazione di un hub eventi. In alcuni scenari potrebbe essere necessario aggiungere partizioni dopo che è stato creato l'hub eventi. Questo articolo descrive come aggiungere partizioni in modo dinamico a un hub eventi esistente. 

> [!IMPORTANT]
> L'aggiunta dinamica di partizioni è disponibile solo nei cluster **dedicati** degli hub eventi.

> [!NOTE]
> Per i client Apache Kafka, un **hub eventi** viene associato a un **argomento Kafka**. Per altre associati tra Hub eventi di Azure e Apache Kafka, vedere la sezione relativa all'[associazione concettuale tra Kafka e Hub eventi](event-hubs-for-kafka-ecosystem-overview.md#kafka-and-event-hub-conceptual-mapping)


## <a name="update-the-partition-count"></a>Aggiornare il numero di partizioni
Questa sezione illustra come aggiornare il numero di partizioni di un hub eventi in modi diversi (PowerShell, interfaccia della riga di comando e così via).

### <a name="powershell"></a>PowerShell
Usare il comando PowerShell [Set-AzureRmEventHub](/powershell/module/azurerm.eventhub/Set-AzureRmEventHub?view=azurermps-6.13.0) per aggiornare le partizioni in un hub eventi. 

```azurepowershell-interactive
Set-AzureRmEventHub -ResourceGroupName MyResourceGroupName -Namespace MyNamespaceName -Name MyEventHubName -partitionCount 12
```

### <a name="cli"></a>CLI
Usare il comando dell'interfaccia della riga di comando [az eventhubs eventhub update](/cli/azure/eventhubs/eventhub?view=azure-cli-latest#az-eventhubs-eventhub-update) per aggiornare le partizioni in un hub eventi. 

```azurecli-interactive
az eventhubs eventhub update --resource-group MyResourceGroupName --namespace-name MyNamespaceName --name MyEventHubName --partition-count 12
```

### <a name="resource-manager-template"></a>Modello di Resource Manager
Aggiornare il valore della proprietà `partitionCount` nel modello di Resource Manager e ridistribuire il modello per aggiornare la risorsa. 

```json
    {
        "apiVersion": "2017-04-01",
        "type": "Microsoft.EventHub/namespaces/eventhubs",
        "name": "[concat(parameters('namespaceName'), '/', parameters('eventHubName'))]",
        "location": "[parameters('location')]",
        "dependsOn": [
            "[resourceId('Microsoft.EventHub/namespaces', parameters('namespaceName'))]"
        ],
        "properties": {
            "messageRetentionInDays": 7,
            "partitionCount": 12
        }
    }
```

### <a name="apache-kafka"></a>Apache Kafka
Usare l'API `AlterTopics` (ad esempio, tramite l'interfaccia della riga di comando **kafka-topics**) per aumentare il numero di partizioni. Per informazioni dettagliate, vedere la sezione relativa alla [modifica degli argomenti di Kafka](http://kafka.apache.org/documentation/#basic_ops_modify_topic). 

## <a name="event-hubs-clients"></a>Client di Hub eventi
Verrà ora esaminato il comportamento dei client di Hub eventi quando il numero di partizioni viene aggiornato in un hub eventi. 

Quando si aggiunge una partizione a un hub eventi esistente, il client dell'hub eventi riceve un'eccezione di tipo "MessagingException" dal servizio indicante ai client che i metadati dell'entità (l'entità è l'hub eventi e i metadati sono le informazioni sulla partizione) sono stati modificati. I client riapriranno automaticamente i collegamenti AMQP, che preleveranno le informazioni sui metadati modificati. I client quindi funzionano normalmente.

### <a name="senderproducer-clients"></a>Client mittenti/producer
Hub eventi offre tre opzioni di mittente:

- **Mittente partizione**: in questo scenario, i client inviano eventi direttamente a una partizione. Sebbene le partizioni siano identificabili e sia possibile inviarvi direttamente gli eventi, questo modello non è consigliato. L'aggiunta di partizioni non ha alcun effetto su questo scenario. Si consiglia di riavviare le applicazioni in modo che possano rilevare le partizioni appena aggiunte. 
- **Mittente chiave di partizione**: in questo scenario, i client inviano gli eventi con una chiave in modo che tutti gli eventi appartenenti a tale chiave si trovino nella stessa partizione. In questo caso, il servizio esegue l'hashing della chiave e la instrada alla partizione corrispondente. L'aggiornamento del numero di partizioni può causare problemi di ordinamento dovuti alla modifica dell'hashing. Pertanto, se si è interessati all'ordinamento, assicurarsi che l'applicazione usi tutti gli eventi delle partizioni esistenti prima di aumentare il numero di partizioni.
- **Mittente round robin (impostazione predefinita)** : in questo scenario, il servizio Hub eventi esegue il meccanismo di round robin per gli eventi tra le partizioni. Il servizio Hub eventi riconosce le modifiche al numero di partizioni ed eseguirà l'invio a nuove partizioni entro pochi secondi dalla modifica del numero di partizioni.

### <a name="receiverconsumer-clients"></a>Client destinatari/consumer
Hub eventi fornisce destinatari diretti e una semplice libreria di consumer denominato [host del processore di eventi (SDK precedente)](event-hubs-event-processor-host.md) oppure [host del processore di eventi (nuovo SDK)](event-processor-balance-partition-load.md).

- **Destinatari diretti**: i destinatari diretti restano in ascolto di partizioni specifiche. Il comportamento di runtime non è influenzato quando le partizioni vengono scalate orizzontalmente per un hub eventi. L'applicazione che usa destinatari diretti deve occuparsi di raccogliere le nuove partizioni e di assegnare i destinatari di conseguenza.
- **Host del processore di eventi**: questo client non aggiorna automaticamente i metadati dell'entità. Non esegue quindi alcun rilevamento all'aumento del numero di partizioni. La ricreazione di un'istanza del processore di eventi determina il recupero dei metadati di un'entità, che a sua volta crea nuovi BLOB per le partizioni appena aggiunte. I BLOB preesistenti non saranno interessati. È consigliabile riavviare tutte le istanze del processore di eventi per garantire che tutte le istanze riconoscano le partizioni appena aggiunte e che il bilanciamento del carico venga gestito correttamente tra i consumer.

    Se si usa la versione precedente di .NET SDK ([WindowsAzure.ServiceBus](https://www.nuget.org/packages/WindowsAzure.ServiceBus/)), l'host del processore di eventi rimuove un checkpoint esistente al riavvio se il numero di partizioni nel checkpoint non corrisponde al numero di partizioni recuperato dal servizio. Questo comportamento può avere un effetto sull'applicazione. 

## <a name="apache-kafka-clients"></a>Client Apache Kafka
Questa sezione descrive in che modo si comportano i client Apache Kafka che usano l'endpoint Kafka di Hub eventi di Azure quando il numero di partizioni viene aggiornato per un hub eventi. 

I client Kafka che usano Hub eventi con il protocollo Apache Kafka si comportano in modo diverso rispetto ai client dell'hub eventi che usano il protocollo AMQP. I client Kafka aggiornano i metadati una volta ogni `metadata.max.age.ms` millisecondi. Questo valore viene specificato nelle configurazioni client. Le librerie `librdkafka` usano la stessa configurazione. Gli aggiornamenti dei metadati indicano ai client le modifiche di servizio, incluso l'aumento del numero di partizioni. Per un elenco di configurazioni, vedere le [configurazioni Apache Kafka per Hub eventi](https://github.com/Azure/azure-event-hubs-for-kafka/blob/master/CONFIGURATION.md)

### <a name="senderproducer-clients"></a>Client mittenti/producer
I producer stabiliscono sempre che le richieste di invio contengano la destinazione della partizione per ogni set di record prodotti. Quindi, tutto il partizionamento di produzione viene eseguito sul lato client con la visualizzazione dei metadati del broker da parte del producer. Una volta aggiunte le nuove partizioni alla visualizzazione dei metadati del producer, sono disponibili per le richieste del producer.

### <a name="consumerreceiver-clients"></a>Client destinatari/consumer
Quando un membro del gruppo di consumer esegue un aggiornamento dei metadati e preleva le partizioni appena create, il membro avvia il ribilanciamento del gruppo. I metadati del consumer vengono quindi aggiornati per tutti i membri del gruppo e le nuove partizioni vengono assegnate dal responsabile del ribilanciamento assegnato.

## <a name="recommendations"></a>Consigli

- Se si usa la chiave di partizione con le applicazioni producer e l'ordinamento in una partizione dipende dall'hashing della chiave, non è consigliabile aggiungere partizioni in modo dinamico. 

    > [!IMPORTANT]
    > Mentre i dati esistenti mantengono l'ordinamento, l'hashing della partizione viene interrotto per i messaggi con hashing dopo la modifica del numero di partizioni a causa dell'aggiunta di partizioni.
- L'aggiunta di una partizione a un argomento o a un'istanza di hub eventi esistente è consigliata nei casi seguenti:
    - Quando si usa il metodo round robin (impostazione predefinita) per l'invio di eventi
     - In caso di strategie di partizionamento predefinite Kafka, ad esempio la strategia StickyAssignor


## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sulle partizioni, vedere [Partizioni](event-hubs-scalability.md#partitions).

