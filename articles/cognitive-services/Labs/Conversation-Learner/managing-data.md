---
title: Gestione dei dati utente con Conversation Learner - Servizi cognitivi Microsoft | Microsoft Docs
titleSuffix: Azure
description: Informazioni su come gestire i dati utente con Conversation Learner.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 83c7e808e48733487e84d668236cdb327c21c44c
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/30/2019
ms.locfileid: "55249079"
---
# <a name="managing-user-data"></a>Gestione dei dati utente

Questa pagina descrive le informazioni registrate dal servizio cloud Conversation Learner durante i dialoghi con gli utenti finali.  Illustra anche come associare i log di Conversation Learner agli ID utente per poter recuperare o eliminare tutti i log associati a un determinato utente.

## <a name="overview-of-end-user-data-logging"></a>Panoramica della registrazione dei dati degli utenti finali

Per impostazione predefinita, il servizio cloud Conversation Learner registra le interazioni tra gli utenti finali e il bot.  Questi log sono importanti per migliorare il bot, perché consentono di identificare i casi in cui il bot ha estratto l'entità non corretta o ha selezionato l'azione errata.  Questi errori possono essere corretti accedendo alla pagina "Log Dialogs" (Dialoghi di log) dell'interfaccia utente, apportando le correzioni e archiviando il dialogo corretto come nuovo dialogo di training. Per altre informazioni, vedere l'esercitazione su come registrare i dialoghi.

## <a name="how-to-disable-logging"></a>Come disabilitare la registrazione

È possibile controllare se sono presenti conversazioni con gli utenti finali nella pagina "Settings" (Impostazioni) del modello di Conversation Learner.  È disponibile una casella di controllo "Log Conversations" (Registra conversazioni).  Deselezionando questa casella, le conversazioni con gli utenti finali non verranno registrate.

## <a name="what-is-logged"></a>Informazioni registrate 

Nei dialoghi di log, Conversation Learner archivia l'input utente, i valori delle entità, le azioni selezionate e i timestamp di ogni turno.  I log vengono archiviati per un determinato periodo di tempo e quindi rimossi. Per informazioni dettagliate, vedere la pagina della guida "Limiti e valori predefiniti".  

Conversation Learner crea un ID univoco per ogni dialogo registrato,  ma *non* archivia un ID utente con i dialoghi registrati.  

## <a name="associating-logged-dialogs-with-a-user-id"></a>Associazione dei dialoghi registrati a un ID utente

È spesso importante poter associare i dialoghi registrati all'ID dell'utente, ad esempio per poter recuperare o eliminare i dialoghi registrati di un determinato utente.  Dato che Conversation Learner non archivia un ID utente, questa associazione deve essere gestita dal codice dello sviluppatore.  

Per creare questo mapping, ottenere l'ID del dialogo registrato in `EntityDetectionCallback`, quindi nello spazio di archiviazione del bot archiviare l'associazione tra ID utente e dialogo registrato.  

```
cl.EntityDetectionCallback(async (text: string, memoryManager: ClientMemoryManager): Promise<void> => {

    // Get user and session info
    var sessionData = memoryManager.SessionInfo();

    // sessionData.sessionId is the ID of this logged dialog.
    // In your bot-specific data store, store an association
    // between your user identifier and this session ID.
    console.log(sessionData.logDialogId)

    // sessionData.userId and sessionData.userName are the 
    // user ID and user name as defined by the channel the user
    // is on (eg, Skype, Teams, Facebook Messenger, etc.).
    // For some channels, this will be undefined.
    // This information is NOT stored with the logged dialog.
    console.log(sessionData.userId);
    console.log(sessionData.userName);

})
```

## <a name="headers-for-http-calls"></a>Intestazioni per le chiamate HTTP

In ognuna delle chiamate HTTP riportate di seguito aggiungere questa intestazione:

```
Ocp-Apim-Subscription-Key=<LUIS_AUTHORING_KEY>
```

`<LUIS_AUTHORING_KEY>` è la chiave di creazione di LUIS per accedere alle applicazioni di Conversation Learner.

## <a name="how-to-obtain-raw-data-for-a-logged-dialog"></a>Come ottenere i dati non elaborati per un dialogo registrato

Per ottenere i dati non elaborati per un dialogo di log, è possibile usare questa chiamata HTTP:

```
GET https://westus.api.cognitive.microsoft.com/conversationlearner/v1.0/app/<appId>/logdialog/<logDialogId>
```

`<appId>` è il GUID del modello di Conversation Learner, mentre `<logDialgoId>` è l'ID del dialogo di log che si vuole recuperare.  

> [!NOTE]
> I dialoghi di log possono essere modificati dallo sviluppatore ed essere quindi archiviati come dialoghi di training.  In questo caso, Conversation Learner archivia l'ID del dialogo di log "di origine" con il dialogo di training.  È anche possibile "creare rami" di un dialogo di training nell'interfaccia utente. Se a un dialogo di training è associato l'ID di un dialogo di log di origine, i rami del dialogo di training verranno contrassegnati con lo stesso ID dialogo di log.

Per ottenere tutti i dialoghi di training derivati da un dialogo di log, seguire questa procedura.

Per prima cosa, recuperare tutti i dialoghi di training:

```
GET https://westus.api.cognitive.microsoft.com/conversationlearner/v1.0/app/<appId>/traindialogs
```

`<appId>` è il GUID del modello di Conversation Learner.  

Vengono così restituiti tutti i dialoghi di training.  Cercare il valore `sourceLogDialogId` associato nell'elenco e prendere nota del valore `trainDialogId` associato. 

Per ottenere un singolo dialogo di training in base all'ID:

```
GET https://westus.api.cognitive.microsoft.com/conversationlearner/v1.0/app/<appId>/traindialog/<trainDialogId>
```

`<appId>` è il GUID del modello di Conversation Learner, mentre `<trainDialogId>` è l'ID del dialogo di training che si vuole recuperare.  

## <a name="how-to-delete-a-logged-dialog"></a>Come eliminare un dialogo registrato

Se si vuole eliminare un dialogo di log in base al relativo ID, è possibile usare questa chiamata HTTP:

```
DELETE https://westus.api.cognitive.microsoft.com/conversationlearner/v1.0/app/<appId>/logdialog/<logDialogId>
```

`<appId>` è il GUID del modello di Conversation Learner, mentre `<logDialogId>` è l'ID del dialogo di log che si vuole eliminare. 

Se si vuole eliminare un dialogo di training in base al relativo ID, è possibile usare questa chiamata HTTP:

```
DELETE https://westus.api.cognitive.microsoft.com/conversationlearner/v1.0/app/<appId>/traindialog/<trainDialogId>
```

`<appId>` è il GUID del modello di Conversation Learner, mentre `<trainDialogId>` è l'ID del dialogo di training che si vuole eliminare. 
