---
title: Autorizzare l'accesso con Azure Active Directory
description: Questo articolo fornisce informazioni su come autorizzare l'accesso alle risorse di hub eventi usando Azure Active Directory.
ms.topic: conceptual
ms.date: 06/23/2020
ms.openlocfilehash: 48d2bc1aa3389459077bb7b8df0ac63ce1e1a438
ms.sourcegitcommit: f796e1b7b46eb9a9b5c104348a673ad41422ea97
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/30/2020
ms.locfileid: "91566262"
---
# <a name="authorize-access-to-event-hubs-resources-using-azure-active-directory"></a>Autorizzare l'accesso alle risorse di hub eventi usando Azure Active Directory
Hub eventi di Azure supporta l'uso di Azure Active Directory (Azure AD) per autorizzare le richieste alle risorse di hub eventi. Con Azure AD, è possibile usare il controllo degli accessi in base al ruolo (RBAC) per concedere le autorizzazioni a un'entità di sicurezza, che può essere un utente o un'entità servizio dell'applicazione. Per ulteriori informazioni sui ruoli e le assegnazioni di ruolo, vedere [informazioni sui diversi ruoli](../role-based-access-control/overview.md).

## <a name="overview"></a>Panoramica
Quando un'entità di sicurezza (un utente o un'applicazione) tenta di accedere a una risorsa di hub eventi, la richiesta deve essere autorizzata. Con Azure AD, l'accesso a una risorsa è un processo in due passaggi. 

 1. In primo luogo, l'identità dell'entità di sicurezza viene autenticata e viene restituito un token OAuth 2,0. Il nome della risorsa per richiedere un token è `https://eventhubs.azure.net/` . Per i client Kafka, la risorsa per richiedere un token è `https://<namespace>.servicebus.windows.net` .
 1. Successivamente, il token viene passato come parte di una richiesta al servizio Hub eventi per autorizzare l'accesso alla risorsa specificata.

Il passaggio di autenticazione richiede che una richiesta dell'applicazione contenga un token di accesso OAuth 2.0 in fase di esecuzione. Se un'applicazione è in esecuzione in un'entità di Azure, ad esempio una VM di Azure, un set di scalabilità di macchine virtuali o un'app per le funzioni di Azure, può usare un'identità gestita per accedere alle risorse. Per informazioni su come autenticare le richieste effettuate da un'identità gestita al servizio Hub eventi, vedere [autenticare l'accesso alle risorse di hub eventi di Azure con Azure Active Directory e identità gestite per le risorse di Azure](authenticate-managed-identity.md). 

Il passaggio di autorizzazione richiede che uno o più ruoli di Azure siano assegnati all'entità di sicurezza. Hub eventi di Azure fornisce i ruoli di Azure che includono i set di autorizzazioni per le risorse di hub eventi. I ruoli assegnati a un'entità di sicurezza determinano le autorizzazioni che saranno disponibili nell'entità. Per altre informazioni sui ruoli di Azure, vedere [ruoli predefiniti di Azure per hub eventi di Azure](#azure-built-in-roles-for-azure-event-hubs). 

Le applicazioni native e le applicazioni Web che effettuano richieste a hub eventi possono anche autorizzare con Azure AD. Per informazioni su come richiedere un token di accesso e usarlo per autorizzare le richieste per le risorse di hub eventi, vedere [autenticare l'accesso a hub eventi di Azure con Azure ad da un'applicazione](authenticate-application.md). 

## <a name="assign-azure-roles-for-access-rights"></a>Assegnare i ruoli di Azure per i diritti di accesso
Azure Active Directory (Azure AD) autorizza i diritti di accesso alle risorse protette tramite il [controllo degli accessi in base al ruolo di Azure (RBAC di Azure)](../role-based-access-control/overview.md). Hub eventi di Azure definisce un set di ruoli predefiniti di Azure che comprende i set comuni di autorizzazioni usate per accedere ai dati dell'hub eventi ed è anche possibile definire ruoli personalizzati per l'accesso ai dati.

Quando un ruolo di Azure viene assegnato a un'entità di sicurezza Azure AD, Azure concede l'accesso a tali risorse per l'entità di sicurezza. L'ambito di accesso può essere limitato al livello di sottoscrizione, al gruppo di risorse, allo spazio dei nomi di hub eventi o a qualsiasi risorsa sottostante. Un'entità di sicurezza di Azure AD può essere un utente o un'entità servizio dell'applicazione oppure un'[identità gestita per le risorse di Azure](../active-directory/managed-identities-azure-resources/overview.md).

## <a name="azure-built-in-roles-for-azure-event-hubs"></a>Ruoli predefiniti di Azure per hub eventi di Azure
Azure fornisce i seguenti ruoli predefiniti di Azure per autorizzare l'accesso ai dati di hub eventi usando Azure AD e OAuth:

| Ruolo | Descrizione | 
| ---- | ----------- | 
| [Proprietario dati di hub eventi di Azure](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-owner) | Usare questo ruolo per concedere l'accesso completo alle risorse di hub eventi. |
| [Mittente dati di hub eventi di Azure](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-sender) | Usare questo ruolo per concedere l'accesso di trasmissione alle risorse di hub eventi. |
| [Ricevitore di dati di hub eventi di Azure](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-receiver) | Usare questo ruolo per concedere l'accesso a consumo/ricezione alle risorse di hub eventi. |

Per i ruoli predefiniti del registro dello schema, vedere [schema Registry Roles](schema-registry-overview.md#role-based-access-control).

## <a name="resource-scope"></a>Ambito risorsa 
Prima di assegnare un ruolo Controllo degli accessi in base al ruolo a un'entità di sicurezza, determinare l'ambito di accesso che dovrà avere. In base alle procedure consigliate, è sempre preferibile concedere solo l'ambito più restrittivo possibile.

L'elenco seguente descrive i livelli in cui è possibile definire l'ambito di accesso alle risorse di hub eventi, a partire dall'ambito più restrittivo:

- **Gruppo di consumer**: in questo ambito, l'assegnazione di ruolo si applica solo a questa entità. Attualmente, il portale di Azure non supporta l'assegnazione di un ruolo di Azure a un'entità di sicurezza a questo livello. 
- **Hub eventi**: l'assegnazione di ruolo si applica all'entità Hub eventi e al gruppo di consumer al suo interno.
- **Spazio dei nomi**: l'assegnazione di ruolo si estende all'intera topologia degli hub eventi nello spazio dei nomi e al gruppo di consumer associato.
- **Gruppo di risorse**: l'assegnazione di ruolo si applica a tutte le risorse di hub eventi nel gruppo di risorse.
- **Sottoscrizione**: l'assegnazione di ruolo si applica a tutte le risorse di hub eventi in tutti i gruppi di risorse nella sottoscrizione.

> [!NOTE]
> - Tenere presente che le assegnazioni di ruolo di Azure possono richiedere fino a cinque minuti per la propagazione. 
> - Questo contenuto si applica sia a hub eventi che a hub eventi per Apache Kafka. Per altre informazioni su Hub eventi per il supporto Kafka, vedere [Hub eventi per Kafka-sicurezza e autenticazione](event-hubs-for-kafka-ecosystem-overview.md#security-and-authentication).


Per ulteriori informazioni sulla definizione dei ruoli predefiniti, vedere informazioni sulle [definizioni](../role-based-access-control/role-definitions.md#management-and-data-operations)dei ruoli. Per informazioni sulla creazione di ruoli personalizzati di Azure, vedere [ruoli personalizzati di Azure](../role-based-access-control/custom-roles.md).



## <a name="samples"></a>Esempi
- [Esempi di Microsoft. Azure. EventHubs](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac). 
    
    Questi esempi usano la libreria **Microsoft. Azure. EventHubs** precedente, ma è possibile aggiornarla facilmente usando la libreria **Azure. Messaging. EventHubs** più recente. Per spostare l'esempio da usando la libreria precedente a una nuova, vedere la [Guida per eseguire la migrazione da Microsoft. Azure. EventHubs ad Azure. Messaging. EventHubs](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs/MigrationGuide.md).
- [Esempi di Azure. Messaging. EventHubs](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Azure.Messaging.EventHubs/ManagedIdentityWebApp)

    Questo esempio è stato aggiornato in modo da usare la libreria **Azure. Messaging. EventHubs** più recente.
- [Hub eventi per gli esempi Kafka-OAuth](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/oauth). 


## <a name="next-steps"></a>Passaggi successivi
- Informazioni su come assegnare un ruolo predefinito di Azure a un'entità di sicurezza, vedere [autenticare l'accesso alle risorse di hub eventi usando Azure Active Directory](authenticate-application.md).
- Informazioni [su come creare ruoli personalizzati con RBAC](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac/CustomRole).
- Informazioni [su come usare Azure Active Directory con eh](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac/AzureEventHubsSDK)

Vedere gli articoli correlati seguenti:

- [Autenticare le richieste a hub eventi di Azure da un'applicazione usando Azure Active Directory](authenticate-application.md)
- [Autenticare un'identità gestita con Azure Active Directory per accedere alle risorse di hub eventi](authenticate-managed-identity.md)
- [Autenticare le richieste a hub eventi di Azure usando le firme di accesso condiviso](authenticate-shared-access-signature.md)
- [Autorizzare l'accesso alle risorse di hub eventi usando le firme di accesso condiviso](authorize-access-shared-access-signature.md)
