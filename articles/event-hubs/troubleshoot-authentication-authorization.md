---
title: Risolvere i problemi di autenticazione e autorizzazione-Hub eventi di Azure
description: Questo articolo fornisce informazioni sulla risoluzione dei problemi di autenticazione e autorizzazione con hub eventi di Azure.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: cd5f48dfb146a027f0b95b4ddea3dc054a315c6e
ms.sourcegitcommit: f796e1b7b46eb9a9b5c104348a673ad41422ea97
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/30/2020
ms.locfileid: "91566228"
---
# <a name="troubleshoot-authentication-and-authorization-issues---azure-event-hubs"></a>Risolvere i problemi di autenticazione e autorizzazione-Hub eventi di Azure
L'articolo [risolvere i problemi di connettività](troubleshooting-guide.md) fornisce suggerimenti per la risoluzione dei problemi di connettività con hub eventi di Azure. Questo articolo fornisce suggerimenti e consigli per la risoluzione dei problemi di autenticazione e autorizzazione con hub eventi di Azure. 

## <a name="if-you-are-using-azure-active-directory"></a>Se si usa Azure Active Directory
Se si usa Azure Active Directory (Azure AD) per l'autenticazione e l'autorizzazione con hub eventi di Azure, verificare che l'identità che accede a hub eventi sia un membro del **ruolo di Azure** appropriato nell' **ambito di risorse** corretto (gruppo di consumer, Hub eventi, spazio dei nomi, gruppo di risorse o sottoscrizione).

### <a name="azure-roles"></a>Ruoli di Azure
- [Proprietario dei dati di hub eventi di Azure](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-owner) per l'accesso completo alle risorse di hub eventi.
- [Mittente dati di hub eventi di Azure](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-receiver) per l'accesso di invio.
- [Ricevitore di dati di hub eventi di Azure](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-sender) per l'accesso di ricezione.

Per i ruoli predefiniti del registro dello schema, vedere [schema Registry Roles](schema-registry-overview.md#role-based-access-control).

### <a name="resource-scopes"></a>Ambiti delle risorse
- **Gruppo di consumer**: in questo ambito, l'assegnazione di ruolo si applica solo a questa entità. Attualmente, il portale di Azure non supporta l'assegnazione di un ruolo di Azure a un'entità di sicurezza a questo livello. 
- **Hub eventi**: l'assegnazione di ruolo si applica all'entità Hub eventi e al gruppo di consumer al suo interno.
- **Spazio dei nomi**: l'assegnazione di ruolo si estende all'intera topologia degli hub eventi nello spazio dei nomi e al gruppo di consumer associato.
- **Gruppo di risorse**: l'assegnazione di ruolo si applica a tutte le risorse di hub eventi nel gruppo di risorse.
- **Sottoscrizione**: l'assegnazione di ruolo si applica a tutte le risorse di hub eventi in tutti i gruppi di risorse nella sottoscrizione.

Per altre informazioni, vedere gli articoli seguenti:

- [Autenticare un'applicazione con Azure Active Directory per accedere alle risorse di hub eventi](authenticate-application.md)
- [Autorizzare l'accesso alle risorse di hub eventi usando Azure Active Directory](authorize-access-azure-active-directory.md)

## <a name="if-you-are-using-shared-access-signatures-sas"></a>Se si usano firme di accesso condiviso (SAS)
Se [si usa la](authenticate-shared-access-signature.md)firma di accesso condiviso, seguire questa procedura: 

- Verificare che la chiave SAS utilizzata sia corretta. In caso contrario, usare la chiave SAS corretta.
- Verificare che la chiave disponga delle autorizzazioni appropriate (trasmissione, ricezione o gestione). In caso contrario, usare una chiave con l'autorizzazione necessaria. 
- Controllare se la chiave è scaduta. Si consiglia di rinnovare correttamente la firma di accesso condiviso prima della scadenza. Se è presente una differenza di orario tra il client e i nodi del servizio Hub eventi, il token di autenticazione potrebbe scadere prima che il client lo realizzi. Gli account di implementazione correnti differiscono il clock fino a 5 minuti, ovvero il client rinnova il token 5 minuti prima della scadenza. Se pertanto lo sfasamento di clock è maggiore di 5 minuti, il client può osservare errori di autenticazione intermittenti.
- Se l' **ora di inizio** della firma di accesso condiviso è impostata su **ora**, è possibile che vengano visualizzati errori intermittenti per i primi minuti a causa dello sfasamento del clock (differenze nell'ora corrente in computer diversi). Impostare l'ora di inizio su almeno 15 minuti nel passato oppure non impostarla affatto. Lo stesso vale in genere anche per l'ora di scadenza. 

Per altre informazioni, vedere gli articoli seguenti: 

- [Eseguire l'autenticazione tramite firme di accesso condiviso (SAS)](authenticate-shared-access-signature.md). 
- [Autorizzazione dell'accesso alle risorse di hub eventi tramite firme di accesso condiviso](authorize-access-shared-access-signature.md)

## <a name="next-steps"></a>Passaggi successivi

Vedere gli articoli seguenti:

* [Risolvere i problemi di connettività](troubleshooting-guide.md)
