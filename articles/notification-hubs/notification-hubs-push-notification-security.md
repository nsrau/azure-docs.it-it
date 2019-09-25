---
title: Sicurezza di Hub di notifica
description: Questo argomento descrive la sicurezza per gli hub di notifica di Azure.
services: notification-hubs
documentationcenter: .net
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: ''
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: multiple
ms.topic: article
ms.date: 09/23/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 09/23/2019
ms.openlocfilehash: a9598f6a01e5536351fb20b7c352a5eaf5746042
ms.sourcegitcommit: a6718e2b0251b50f1228b1e13a42bb65e7bf7ee2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2019
ms.locfileid: "71273628"
---
# <a name="notification-hubs-security"></a>Sicurezza di hub di notifica

## <a name="overview"></a>Panoramica

In questo argomento viene descritto il modello di protezione degli hub di notifica di Azure.

## <a name="shared-access-signature-security"></a>Sicurezza della firma di accesso condiviso

Hub di notifica implementa uno schema di sicurezza a livello di entità denominato *firma di accesso condiviso* (SAS). Ogni regola contiene un nome, un valore di chiave (segreto condiviso) e un set di diritti, come illustrato più avanti in [attestazioni di sicurezza](#security-claims). 

Quando si crea un hub, vengono create automaticamente due regole: una con diritti di **ascolto** (che l'app client USA) e l'altra con **tutti** i diritti (usati dal back-end dell'app):

- **DefaultListenSharedAccessSignature**: concede solo l'autorizzazione **Listen** .
- **DefaultFullSharedAccessSignature**: concede le autorizzazioni **Listen**, **Manage**e **Send** . Questo criterio deve essere usato solo nel back-end dell'app. Non usarla nelle applicazioni client. usare un criterio solo con accesso in **ascolto** . Per creare nuovi criteri di accesso personalizzati con un nuovo token SAS, vedere [token di firma di accesso condiviso per i criteri di accesso](#sas-tokens-for-access-policies) più avanti in questo articolo.

Quando si esegue la gestione delle registrazioni dalle app client, se le informazioni inviate tramite notifiche non sono riservate (ad esempio aggiornamenti meteorologici), un modo comune per accedere a un hub di notifica è assegnare il valore della chiave della regola di accesso solo in ascolto all'app client e il valore della chiave della regola di accesso completo al back-end dell'app.

Le app non devono incorporare il valore della chiave nelle app client di Windows Store. al contrario, chiedere all'app client di recuperarla dal back-end dell'app all'avvio.

La chiave con accesso in **ascolto** consente a un'app client di registrarsi per qualsiasi tag. Se l'app deve limitare le registrazioni a specifici tag a client specifici, ad esempio quando i tag rappresentano gli ID utente, il back-end dell'app deve eseguire le registrazioni. Per ulteriori informazioni, vedere [gestione delle registrazioni](notification-hubs-push-notification-registration-management.md). Si noti che in questo modo, l'app client non avrà accesso diretto agli hub di notifica.

## <a name="security-claims"></a>Attestazioni di sicurezza

Analogamente ad altre entità, le operazioni degli hub di notifica sono consentite per tre attestazioni di sicurezza: **Ascolto**, **invio**e **gestione**.

| Attestazione   | Descrizione                                          | Operazioni consentite |
| ------- | ---------------------------------------------------- | ------------------ |
| Essere in ascolto  | Creare o aggiornare, leggere ed eliminare singole registrazioni | Creare o aggiornare una registrazione<br><br>Leggere una registrazione<br><br>Leggere tutte le registrazioni per un handle<br><br>Eliminare una registrazione |
| Invio    | Inviare messaggi all'hub di notifica                | Invia messaggio |
| Gestisci  | CRUD negli hub di notifica (incluso l'aggiornamento delle credenziali PNS e le chiavi di sicurezza) e lettura delle registrazioni basata sui tag |Crea/aggiorna/leggi/Elimina Hub<br><br>Leggere le registrazioni per tag |

Hub di notifica accetta i token SAS generati con chiavi condivise configurate direttamente nell'hub.

Non è possibile inviare una notifica a più di uno spazio dei nomi. Gli spazi dei nomi sono contenitori logici per gli hub di notifica e non sono interessati all'invio di notifiche.

Usare i criteri di accesso a livello di spazio dei nomi (credenziali) per le operazioni a livello di spazio dei nomi. ad esempio: elenco di hub, creazione o eliminazione di hub e così via. Solo i criteri di accesso a livello di hub consentono di inviare le notifiche.

### <a name="sas-tokens-for-access-policies"></a>Token SAS per i criteri di accesso

Per creare una nuova attestazione di sicurezza o per visualizzare le chiavi SAS esistenti, effettuare le seguenti operazioni:

1. Accedere al portale di Azure.
2. Selezionare **Tutte le risorse**.
3. Selezionare il nome dell'hub di notifica per il quale si vuole creare l'attestazione o visualizzare la chiave SAS.
4. Nel menu a sinistra selezionare **criteri di accesso**.
5. Selezionare **nuovo criterio** per creare una nuova attestazione di sicurezza. Assegnare un nome al criterio e selezionare le autorizzazioni che si desidera concedere. Selezionare **OK**.
6. La stringa di connessione completa (inclusa la nuova chiave SAS) viene visualizzata nella finestra criteri di accesso. È possibile copiare questa stringa negli Appunti per un uso successivo.

Per estrarre la chiave SAS da un criterio specifico, selezionare il pulsante **copia** accanto ai criteri che contengono la chiave SAS desiderata. Incollare questo valore in un percorso temporaneo, quindi copiare la parte della chiave SAS della stringa di connessione. Questo esempio usa uno spazio dei nomi di hub di notifica denominato **mytestnamespace1**e un criterio denominato **policy2**. La chiave SAS è il valore vicino alla fine della stringa, specificata da **SharedAccessKey**:

```shell
Endpoint=sb://mytestnamespace1.servicebus.windows.net/;SharedAccessKeyName=policy2;SharedAccessKey=<SAS key value here>
```

![Ottenere le chiavi SAS](media/notification-hubs-push-notification-security/access1.png)

## <a name="next-steps"></a>Passaggi successivi

- [Panoramica dell'Hub di notifica](notification-hubs-push-notification-overview.md)
