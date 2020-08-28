---
title: Argomenti di sistema in griglia di eventi di Azure
description: Descrive gli argomenti di sistema in griglia di eventi di Azure.
ms.topic: conceptual
ms.date: 08/27/2020
ms.openlocfilehash: f5ca472ab5141207222987d476284813c2aacf56
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/27/2020
ms.locfileid: "89019097"
---
# <a name="system-topics-in-azure-event-grid"></a>Argomenti di sistema in griglia di eventi di Azure
Un argomento di sistema in griglia di eventi rappresenta uno o più eventi pubblicati da servizi di Azure, ad esempio archiviazione di Azure e hub eventi di Azure. Un argomento di sistema, ad esempio, può rappresentare **tutti gli eventi BLOB** o solo gli eventi BLOB **creati** e BLOB **eliminati** per un **account di archiviazione specifico**. In questo esempio, quando un BLOB viene caricato nell'account di archiviazione, il servizio di archiviazione di Azure pubblica un evento **BLOB creato** nell'argomento sistema in griglia di eventi, che quindi trasmette l'evento ai [sottoscrittori](event-handlers.md) dell'argomento che ricevono ed elaborano l'evento. 

> [!NOTE] 
> Solo i servizi di Azure possono pubblicare eventi negli argomenti di sistema. Pertanto, non si ottiene un endpoint o chiavi di accesso che è possibile usare per pubblicare eventi come per gli argomenti o i domini personalizzati.

## <a name="azure-services-that-support-system-topics"></a>Argomenti relativi ai servizi di Azure che supportano il sistema
Di seguito è riportato l'elenco corrente dei servizi di Azure che supportano la creazione di argomenti di sistema su di essi.

- [Configurazione app di Azure](event-schema-app-configuration.md)
- [Servizio app di Azure](event-schema-app-service.md)
- [Archiviazione BLOB di Azure](event-schema-blob-storage.md)
- [Registro Azure Container](event-schema-container-registry.md)
- [Hub eventi di Azure](event-schema-event-hubs.md)
- [Hub IoT Azure](event-schema-iot-hub.md)
- [Insieme di credenziali chiave Azure](event-schema-key-vault.md)
- [Azure Machine Learning](event-schema-machine-learning.md)
- [Mappe di Azure](event-schema-azure-maps.md)
- [Servizi multimediali di Azure](event-schema-media-services.md)
- [Gruppi di risorse di Azure](event-schema-resource-groups.md)
- [Bus di servizio di Azure](event-schema-service-bus.md)
- [Azure SignalR](event-schema-azure-signalr.md)
- [Sottoscrizioni di Azure](event-schema-subscriptions.md)

## <a name="system-topics-as-azure-resources"></a>Argomenti di sistema come risorse di Azure
In passato, un argomento di sistema era implicito e non era esposto per semplicità. Gli argomenti di sistema sono ora visibili come risorse di Azure e offrono le funzionalità seguenti:

- [Visualizzare gli argomenti di sistema nella portale di Azure](create-view-manage-system-topics.md#view-all-system-topics)
- Esportare i modelli di Gestione risorse per gli argomenti di sistema e le sottoscrizioni di eventi nella portale di Azure
- [Configurare i log di diagnostica per gli argomenti di sistema](enable-diagnostic-logs-topic.md#enable-diagnostic-logs-for-a-system-topic)
- Configurare avvisi per errori di pubblicazione e recapito 

## <a name="lifecycle-of-system-topics"></a>Ciclo di vita degli argomenti di sistema
È possibile creare un argomento di sistema in due modi: 

- Creare una [sottoscrizione di eventi in una risorsa di Azure come risorsa di estensione](/rest/api/eventgrid/version2020-06-01/eventsubscriptions/createorupdate), che crea automaticamente un argomento di sistema con il nome nel formato: `<Azure resource name>-<GUID>` . L'argomento di sistema creato in questo modo viene eliminato automaticamente quando viene eliminata l'ultima sottoscrizione di evento per l'argomento. 
- Creare un argomento di sistema per una risorsa di Azure e quindi creare una sottoscrizione di eventi per tale argomento di sistema. Quando si usa questo metodo, è possibile specificare un nome per l'argomento di sistema. L'argomento di sistema non viene eliminato automaticamente quando viene eliminata l'ultima sottoscrizione di evento. È necessario eliminarlo manualmente. 

    Quando si usa il portale di Azure, si usa sempre questo metodo. Quando si crea una sottoscrizione di eventi usando la [pagina **eventi** di una risorsa di Azure](blob-event-quickstart-portal.md#subscribe-to-the-blob-storage), viene creato prima l'argomento di sistema e quindi viene creata la sottoscrizione per l'argomento. Per creare un argomento di sistema in modo esplicito, è possibile usare la [pagina **argomenti del sistema di griglia di eventi** ](create-view-manage-system-topics.md#create-a-system-topic) e quindi creare una sottoscrizione per tale argomento. 

Quando si usa l' [interfaccia](create-view-manage-system-topics-cli.md)della riga di comando, [Rest](/rest/api/eventgrid/version2020-06-01/eventsubscriptions/createorupdate)o [Azure Resource Manager modello](create-view-manage-system-topics-arm.md), è possibile scegliere uno dei metodi descritti in precedenza. È consigliabile creare innanzitutto un argomento di sistema e quindi creare una sottoscrizione nell'argomento, in quanto si tratta dell'ultimo modo per creare argomenti di sistema.

La creazione dell'argomento di sistema non riesce se sono stati configurati criteri di Azure in modo che il servizio griglia di eventi non possa crearlo. Ad esempio, è possibile avere un criterio che consente la creazione di solo determinati tipi di risorse, ad esempio archiviazione di Azure, Hub eventi di Azure e così via, nella sottoscrizione. 

## <a name="location-and-resource-group-for-a-system-topic"></a>Percorso e gruppo di risorse per un argomento di sistema
Per le origini eventi di Azure che si trovano in un'area o in una località specifica, l'argomento di sistema viene creato nella stessa posizione dell'origine eventi di Azure. Se, ad esempio, si crea una sottoscrizione di eventi per un archivio BLOB di Azure nell'area Stati Uniti orientali, l'argomento di sistema viene creato nell'area Stati Uniti orientali. Per le origini eventi globali di Azure, ad esempio le sottoscrizioni di Azure, i gruppi di risorse o le mappe di Azure, griglia di eventi crea l'argomento di sistema in posizione **globale** . 

In generale, l'argomento di sistema viene creato nello stesso gruppo di risorse in cui si trova l'origine evento di Azure. Per le sottoscrizioni di eventi create nell'ambito della sottoscrizione di Azure, l'argomento di sistema viene creato nel gruppo di risorse **default-EventGrid** nell'area **Stati Uniti occidentali 2** . Se il gruppo di risorse non esiste, griglia di eventi di Azure lo crea prima di creare l'argomento di sistema. 

## <a name="next-steps"></a>Passaggi successivi
Vedere gli articoli seguenti: 

- [Creare, visualizzare e gestire gli argomenti di sistema utilizzando portale di Azure](create-view-manage-system-topics.md).
- [Creare, visualizzare e gestire gli argomenti del sistema di griglia di eventi usando l'interfaccia della riga di comando di Azure](create-view-manage-system-topics-cli.md)
- [Creare argomenti di sistema di griglia di eventi usando modelli di Azure Resource Manager](create-view-manage-system-topics-arm.md)
