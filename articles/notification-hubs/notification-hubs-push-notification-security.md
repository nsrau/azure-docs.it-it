---
title: Modello di sicurezza di Hub di notifica
description: Informazioni sul modello di sicurezza per gli hub di notifica di Azure.Learn about the security model for Azure Notification Hubs.
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
ms.openlocfilehash: b871775bc7a6d795e86147ae9cffa27bdd2f3348
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76263762"
---
# <a name="notification-hubs-security"></a>Sicurezza degli hub di notifica

## <a name="overview"></a>Panoramica

In questo argomento viene descritto il modello di protezione degli hub di notifica di Azure.

## <a name="shared-access-signature-security"></a>Sicurezza della firma di accesso condivisoShared Access Signature security

Hub di notifica implementa uno schema di sicurezza a livello di entità denominato firma di *accesso condiviso.* Ogni regola contiene un nome, un valore di chiave (segreto condiviso) e un set di diritti, come illustrato più avanti in [Attestazioni](#security-claims)di sicurezza . 

Quando si crea un hub, vengono create automaticamente due regole: una con diritti di **ascolto** (usi dall'app client) e una con **tutti i** diritti (usi dal back-end dell'app):

- **DefaultListenSharedAccessSignature:** concede solo l'autorizzazione **di ascolto.**
- **DefaultFullSharedAccessSignature**: concede le autorizzazioni **Listen**, **Manage**e **Send** . Questo criterio deve essere usato solo nel back-end dell'app. Non utilizzarlo nelle applicazioni client; utilizzare un criterio con solo accesso in **ascolto.** Per creare nuovi criteri di accesso personalizzati con un nuovo token di firma di accesso condiviso, vedere Token di firma di [accesso condiviso per](#sas-tokens-for-access-policies) i criteri di accesso più avanti in questo articolo.

Quando si esegue la gestione delle registrazioni dalle app client, se le informazioni inviate tramite notifiche non sono riservate (ad esempio aggiornamenti meteorologici), un modo comune per accedere a un hub di notifica è assegnare il valore della chiave della regola di accesso solo in ascolto all'app client e il valore della chiave della regola di accesso completo al back-end dell'app.

Le app non devono incorporare il valore della chiave nelle app client di Windows Store. chiedere invece all'app client di recuperarlo dal back-end dell'app all'avvio.

La chiave con accesso **in ascolto** consente a un'app client di registrarsi per qualsiasi tag. Se l'app deve limitare le registrazioni a tag specifici a client specifici (ad esempio, quando i tag rappresentano ID utente), il back-end dell'app deve eseguire le registrazioni. Per ulteriori informazioni, vedere [Gestione registrazioni](notification-hubs-push-notification-registration-management.md). Si noti che in questo modo, l'app client non avrà accesso diretto agli hub di notifica.

## <a name="security-claims"></a>Attestazioni di sicurezza

Analogamente ad altre entità, le operazioni dell'hub di notifica sono consentite per tre attestazioni di sicurezza: **Listen**, **Send**e **Manage**.

| Attestazione   | Descrizione                                          | Operazioni consentite |
| ------- | ---------------------------------------------------- | ------------------ |
| Attesa  | Creare o aggiornare, leggere ed eliminare singole registrazioni | Creare o aggiornare una registrazione<br><br>Leggere una registrazione<br><br>Leggere tutte le registrazioni per un handle<br><br>Elimina registrazione |
| Send    | Inviare messaggi all'hub di notifica                | Send message |
| Gestione  | CRUD negli hub di notifica (incluso l'aggiornamento delle credenziali PNS e le chiavi di sicurezza) e lettura delle registrazioni basata sui tag |Crea/Aggiorna/Lettura/Elimina hub<br><br>Leggere le registrazioni per tag |

Hub di notifica accetta token di firma di accesso condiviso generati con chiavi condivise configurate direttamente nell'hub.

Non è possibile inviare una notifica a più di uno spazio dei nomi. Gli spazi dei nomi sono contenitori logici per gli hub di notifica e non sono coinvolti nell'invio di notifiche.

Usare i criteri di accesso a livello di spazio dei nomi (credenziali) per le operazioni a livello di spazio dei nomi. ad esempio: elencare gli hub, creare o eliminare hub, ecc. Solo i criteri di accesso a livello di hub consentono di inviare notifiche.

### <a name="sas-tokens-for-access-policies"></a>Token di firma di accesso condiviso per i criteri di accesso

Per creare una nuova attestazione di sicurezza o per visualizzare le chiavi di accesso livello di accesso utenti windows esistenti, eseguire le operazioni seguenti:To create a new security claim or to view existing SAS keys, do the following:

1. Accedere al portale di Azure.
2. Selezionare **Tutte le risorse**.
3. Selezionare il nome dell'hub di notifica per il quale si desidera creare l'attestazione o visualizzare la chiave di accesso base.
4. Nel menu a sinistra selezionare Criteri di **accesso**.
5. Selezionare **Nuovo criterio** per creare una nuova attestazione di sicurezza. Assegnare un nome al criterio e selezionare le autorizzazioni che si desidera concedere. Quindi selezionare **OK**.
6. La stringa di connessione completa (inclusa la nuova chiave di accesso utente) viene visualizzata nella finestra Criteri di accesso. È possibile copiare questa stringa negli Appunti per un utilizzo successivo.

Per estrarre la chiave di accesso livello di accesso base da un criterio specifico, selezionare il pulsante **Copia** accanto al criterio contenente la chiave di accesso livello di accesso base desiderata. Incollare questo valore in un percorso temporaneo, quindi copiare la parte della chiave di accesso base della stringa di connessione. In questo esempio vengono utilizzati uno spazio dei nomi Hub di notifica denominato **mytestnamespace1**e un criterio denominato **policy2**. La chiave di accesso condiviso è il valore vicino alla fine della stringa, specificato da **SharedAccessKey**:

```shell
Endpoint=sb://mytestnamespace1.servicebus.windows.net/;SharedAccessKeyName=policy2;SharedAccessKey=<SAS key value here>
```

![Ottenere le chiavi di accesso sASGet SAS keys](media/notification-hubs-push-notification-security/access1.png)

## <a name="next-steps"></a>Passaggi successivi

- [Panoramica degli hub di notifica](notification-hubs-push-notification-overview.md)
