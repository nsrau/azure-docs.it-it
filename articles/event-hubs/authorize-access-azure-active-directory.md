---
title: Autorizzare l'accesso con Azure Active Directory
description: Questo articolo fornisce informazioni sull'autorizzazione dell'accesso alle risorse di Hub eventi tramite Azure Active Directory.This article provides information on authorizing access to Event Hubs resources using Azure Active Directory.
services: event-hubs
ms.service: event-hubs
documentationcenter: ''
author: spelluru
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: spelluru
ms.openlocfilehash: 1fa8f7a48c03ead7e939185b23834b3049b3e21c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80064854"
---
# <a name="authorize-access-to-event-hubs-resources-using-azure-active-directory"></a>Autorizzare l'accesso alle risorse di Hub eventi usando Azure Active DirectoryAuthorize access to Event Hubs resources using Azure Active Directory
Hub eventi di Azure supporta l'uso di Azure Active Directory (Azure AD) per autorizzare le richieste alle risorse di Hub di eventi. Con Azure AD è possibile usare il controllo degli accessi in base al ruolo per concedere autorizzazioni a un'entità di sicurezza, che può essere un utente o un'entità servizio dell'applicazione. Per ulteriori informazioni sui ruoli e le assegnazioni di ruolo, vedere [Understanding the different roles](../role-based-access-control/overview.md).

## <a name="overview"></a>Panoramica
Quando un'entità di sicurezza (un utente o un'applicazione) tenta di accedere a una risorsa Hub eventi, la richiesta deve essere autorizzata. Con Azure AD, l'accesso a una risorsa è un processo in due passaggi. 

 1. In primo luogo, l'identità dell'entità di sicurezza viene autenticata e viene restituito un token OAuth 2.0.First, the security principal's identity is authenticated, and an OAuth 2.0 token is returned. Il nome della risorsa `https://eventhubs.azure.net/`per richiedere un token è . Per i client Kafka, la `https://<namespace>.servicebus.windows.net`risorsa per richiedere un token è .
 1. Successivamente, il token viene passato come parte di una richiesta al servizio Hub eventi per autorizzare l'accesso alla risorsa specificata.

Il passaggio di autenticazione richiede che una richiesta dell'applicazione contenga un token di accesso OAuth 2.0 in fase di esecuzione. Se un'applicazione è in esecuzione all'interno di un'entità di Azure, ad esempio una macchina virtuale di Azure, un set di scalabilità di macchine virtuali o un'app Di Azure Function, può usare un'identità gestita per accedere alle risorse. Per informazioni su come autenticare le richieste effettuate da un'identità gestita al servizio Hub eventi, vedere [Autenticare l'accesso alle risorse di Hub eventi di Azure con Azure Active Directory e le identità gestite per](authenticate-managed-identity.md)le risorse di Azure. 

Il passaggio di autorizzazione richiede l'assegnazione di uno o più ruoli RBAC all'entità di sicurezza. Hub eventi di Azure fornisce ruoli RBAC che includono set di autorizzazioni per le risorse di Hub eventi. I ruoli assegnati a un'entità di sicurezza determinano le autorizzazioni di cui l'entità disticherà. Per altre informazioni sui ruoli RBAC, vedere Ruoli RBAC incorporati per Hub eventi di Azure.For more information about RBAC roles, see [Built-in RBAC roles for Azure Event Hubs.](#built-in-rbac-roles-for-azure-event-hubs) 

Le applicazioni native e le applicazioni Web che effettuano richieste agli hub eventi possono anche autorizzare con Azure AD. Per informazioni su come richiedere un token di accesso e usarlo per autorizzare le richieste per le risorse di Hub eventi, vedere [Autenticare l'accesso agli hub eventi di Azure con Azure AD da un'applicazione.](authenticate-application.md) 

## <a name="assign-rbac-roles-for-access-rights"></a>Assegnare ruoli RBAC per i diritti di accessoAssign RBAC roles for access rights
Azure Active Directory (Azure AD) autorizza diritti di accesso a risorse protette tramite il [controllo degli accessi in base al ruolo](../role-based-access-control/overview.md). Hub eventi di Azure definisce un set di ruoli RBAC predefiniti che includono set comuni di autorizzazioni usati per accedere ai dati dell'hub eventi ed è anche possibile definire ruoli personalizzati per l'accesso ai dati.

Quando un ruolo Controllo degli accessi in base al ruolo viene assegnato a un'entità di sicurezza di Azure AD, Azure concede l'accesso a tali risorse per tale entità di sicurezza. L'ambito dell'accesso può essere definito in base al livello di sottoscrizione, al gruppo di risorse, allo spazio dei nomi Hub eventi o a qualsiasi risorsa al di sotto di esso. Un'entità di sicurezza di Azure AD può essere un utente, un'entità servizio dell'applicazione o [un'identità gestita per](../active-directory/managed-identities-azure-resources/overview.md)le risorse di Azure.An Azure AD security principal may be a user, or an application service principal, or a managed identity for Azure resources.

## <a name="built-in-rbac-roles-for-azure-event-hubs"></a>Ruoli RBAC incorporati per hub eventi di AzureBuilt-in RBAC roles for Azure Event Hubs
Azure offre i ruoli RBAC predefiniti seguenti per autorizzare l'accesso ai dati di Hub eventi usando Azure AD e OAuth:

- [Proprietario dei dati di Hub](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-owner)eventi di Azure: usare questo ruolo per concedere l'accesso completo alle risorse di Hub eventi.
- [Mittente dati hub eventi](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-receiver)di Azure: usare questo ruolo per concedere l'accesso all'invio alle risorse di Hub eventi.
- [Ricevitore dati Hub eventi](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-sender)di Azure: usare questo ruolo per concedere l'accesso tramite consumer/ricezione alle risorse di Hub eventi.

## <a name="resource-scope"></a>Ambito della risorsa 
Prima di assegnare un ruolo Controllo degli accessi in base al ruolo a un'entità di sicurezza, determinare l'ambito di accesso che l'entità di sicurezza deve disporre. Le procedure consigliate stabiliscono che è sempre consigliabile concedere solo l'ambito più ristretto possibile.

Nell'elenco seguente vengono descritti i livelli in cui è possibile definire l'ambito dell'accesso alle risorse di Hub eventi, a partire dall'ambito più ristretto:

- **Gruppo di consumer:** in questo ambito, l'assegnazione di ruolo si applica solo a questa entità. Attualmente, il portale di Azure non supporta l'assegnazione di un ruolo Controllo degli accessi in base al ruolo a un'entità di sicurezza a questo livello. 
- **Hub eventi:** l'assegnazione del ruolo viene applicata all'entità Hub eventi e al gruppo di consumer al di sotto di essa.
- **Spazio dei nomi:** l'assegnazione dei ruoli si estende sull'intera topologia degli hub eventi nello spazio dei nomi e sul gruppo di consumer associato.
- Gruppo di **risorse:** l'assegnazione dei ruoli si applica a tutte le risorse di Hub eventi nel gruppo di risorse.
- **Sottoscrizione:** l'assegnazione dei ruoli si applica a tutte le risorse di Hub eventi in tutti i gruppi di risorse nella sottoscrizione.

> [!NOTE]
> - Tenere presente che la propagazione delle assegnazioni di ruolo RBAC può richiedere fino a cinque minuti. 
> - Questo contenuto si applica sia agli hub eventi che agli hub eventi per Apache Kafka. Per altre informazioni sugli hub eventi per il supporto Kafka, vedere [Hub eventi per Kafka - sicurezza e autenticazione](event-hubs-for-kafka-ecosystem-overview.md#security-and-authentication).


Per ulteriori informazioni sulla modalità di definizione dei ruoli predefiniti, vedere [Informazioni sulle definizioni](../role-based-access-control/role-definitions.md#management-and-data-operations)di ruolo . Per informazioni sulla creazione di ruoli RBAC personalizzati, vedere Creare ruoli personalizzati per il controllo degli accessi in base al ruolo di Azure.For information about creating custom RBAC roles, see [Create custom roles for Azure Role-Based Access Control](../role-based-access-control/custom-roles.md).



## <a name="samples"></a>Esempi
- Esempi di [Microsoft.Azure.EventHubs](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac). 
    
    Questi esempi usano la vecchia libreria **Microsoft.Azure.EventHubs,** ma è possibile aggiornarla facilmente usando la libreria **Azure.Messaging.EventHubs** più recente. Per spostare l'esempio dall'uso della libreria precedente a una nuova, vedere la [Guida per eseguire la migrazione da Microsoft.Azure.EventHubs ad Azure.Messaging.EventHubs](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs/migration-guide-from-v4.md).
- [Esempi di Azure.Messaging.EventHubsAzure.Messaging.EventHubs samples](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Azure.Messaging.EventHubs/ManagedIdentityWebApp)

    Questo esempio è stato aggiornato per usare la libreria **Azure.Messaging.EventHubs** più recente.
- [Hub di eventi per Kafka - Esempi OAuth](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/oauth). 


## <a name="next-steps"></a>Passaggi successivi
- Per informazioni su come assegnare un ruolo RBAC incorporato a un'entità di sicurezza, vedere [Autenticare l'accesso alle risorse degli hub eventi con Azure Active Directory.](authenticate-application.md)
- Informazioni su [come creare ruoli personalizzati con RBAC.](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac/CustomRole)
- Informazioni su [come usare Azure Active Directory con EH](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac/AzureEventHubsSDK)

Vedere i seguenti articoli correlati:

- [Autenticare le richieste agli hub eventi di Azure da un'applicazione tramite Azure Active DirectoryAuthenticate requests to Azure Event Hubs from an application using Azure Active Directory](authenticate-application.md)
- [Autenticare un'identità gestita con Azure Active Directory per accedere alle risorse degli hub eventiAuthenticate a managed identity with Azure Active Directory to access Event Hubs Resources](authenticate-managed-identity.md)
- [Autenticare le richieste agli hub eventi di Azure usando le firme di accesso condivisoAuthenticate requests to Azure Event Hubs using Shared Access Signatures](authenticate-shared-access-signature.md)
- [Autorizzare l'accesso alle risorse degli hub eventi usando le firme di accesso condivisoAuthorize access to Event Hubs resources using Shared Access Signatures](authorize-access-shared-access-signature.md)
