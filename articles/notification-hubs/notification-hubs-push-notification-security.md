---
title: Sicurezza di Hub di notifica
description: In questo argomento viene illustrata la sicurezza degli hub di notifica di Azure.
services: notification-hubs
documentationcenter: .net
author: jwargo
manager: patniko
editor: spelluru
ms.assetid: 6506177c-e25c-4af7-8508-a3ddca9dc07c
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: multiple
ms.topic: article
ms.date: 01/04/2019
ms.author: jowargo
ms.openlocfilehash: bd9df12cbe941b868c769daccd02c1d81b39f7bd
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60776491"
---
# <a name="security-model-of-azure-notification-hubs"></a>Modello di sicurezza di Hub di notifica di Azure

## <a name="overview"></a>Panoramica

In questo argomento viene descritto il modello di protezione degli hub di notifica di Azure. Poiché gli hub di notifica sono un'entità del bus di servizio, implementano lo stesso modello di sicurezza del bus di servizio. Per altre informazioni, vedere gli argomenti sull’ [Autenticazione per il bus di servizio](https://msdn.microsoft.com/library/azure/dn155925.aspx) .

## <a name="shared-access-signature-security-sas"></a>Sicurezza della firma di accesso condiviso (SAS)

Gli hub di notifica implementano uno schema di sicurezza a livello di entità denominato firma di accesso condiviso (SAS, Shared Access Signature). Questo schema consente alle entità di messaggistica di dichiarare nella descrizione fino a 12 regole di autorizzazione che concedono diritti sull'entità.

Ogni regola contiene un nome, un valore di chiave (segreto condiviso) e un set di diritti, come illustrato nella sezione "Attestazioni di sicurezza". Quando si crea un hub di notifica, vengono create automaticamente due regole: uno con diritti di ascolto (utilizzata dall'applicazione client) e una con tutti i diritti (utilizzata dal back-end dell’app).

Quando si esegue la gestione delle registrazioni dalle app client, se le informazioni inviate tramite notifiche non sono riservate (ad esempio aggiornamenti meteorologici), un modo comune per accedere a un hub di notifica è assegnare il valore della chiave della regola di accesso solo in ascolto all'app client e il valore della chiave della regola di accesso completo al back-end dell'app.

Non è consigliabile integrare il valore della chiave nelle applicazioni client di Windows Store. Un modo per evitare di integrare il valore della chiave è consentire all'app client di recuperarlo dal back-end dell'app all'avvio.

È importante comprendere che la chiave con accesso in ascolto consente a un’app client la registrazione per qualsiasi tag. Se l'app deve limitare le registrazioni a tag specifici su client specifici (ad esempio, quando i tag rappresentano gli ID utente), il back-end dell’app deve eseguire le registrazioni. Per ulteriori informazioni, vedere Gestione della registrazione. Si noti che in questo modo, l'app client non avrà accesso diretto agli hub di notifica.

## <a name="security-claims"></a>Attestazioni di sicurezza

Analogamente ad altre entità, le operazioni degli hub di notifica sono consentite per tre attestazioni di sicurezza: ascolto, invio e gestione.

| Attestazione   | DESCRIZIONE                                          | Operazioni consentite |
| ------- | ---------------------------------------------------- | ------------------ |
| Attesa  | Creare o aggiornare, leggere ed eliminare singole registrazioni | Creare o aggiornare una registrazione<br><br>Leggere una registrazione<br><br>Leggere tutte le registrazioni per un handle<br><br>Eliminare una registrazione |
| Invio    | Inviare messaggi all'hub di notifica                | Send message |
| Gestisci  | CRUD negli hub di notifica (incluso l'aggiornamento delle credenziali PNS e le chiavi di sicurezza) e lettura delle registrazioni basata sui tag |Hub di notifica di creazione, aggiornamento, lettura ed eliminazione<br><br>Leggere le registrazioni per tag |

Gli hub di notifica accettano attestazioni concesse dai token del Controllo di accesso di Microsoft Azure e dai token di firma generati con chiavi condivise configurate direttamente nell’hub di notifica.
