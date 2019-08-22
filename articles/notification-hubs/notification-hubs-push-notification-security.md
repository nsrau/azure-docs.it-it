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
ms.date: 05/31/2019
ms.author: jowargo
ms.openlocfilehash: 73a6d0eaab286dec9d02bb55eb75f0781bcffcc4
ms.sourcegitcommit: a3a40ad60b8ecd8dbaf7f756091a419b1fe3208e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/21/2019
ms.locfileid: "69891579"
---
# <a name="notification-hubs-security"></a>Sicurezza di Hub di notifica

## <a name="overview"></a>Panoramica

In questo argomento viene descritto il modello di protezione degli hub di notifica di Azure.

## <a name="shared-access-signature-security-sas"></a>Sicurezza della firma di accesso condiviso (SAS)

Gli hub di notifica implementano uno schema di sicurezza a livello di entità denominato firma di accesso condiviso (SAS, Shared Access Signature). Ogni regola contiene un nome, un valore di chiave (segreto condiviso) e un set di diritti, come illustrato in [attestazioni di sicurezza](#security-claims). Quando si crea un hub di notifica, vengono create automaticamente due regole: una con diritti di **ascolto** (che l'app client USA) e l'altra con **tutti** i diritti (usati dal back-end dell'app).

Quando si esegue la gestione delle registrazioni dalle app client, se le informazioni inviate tramite notifiche non sono riservate (ad esempio aggiornamenti meteorologici), un modo comune per accedere a un hub di notifica è assegnare il valore della chiave della regola di accesso solo in ascolto all'app client e il valore della chiave della regola di accesso completo al back-end dell'app.

Le app non devono incorporare il valore della chiave nelle app client di Windows Store, ma devono essere recuperate dall'app client dal back-end dell'app all'avvio.

La chiave con accesso in **ascolto** consente a un'app client di registrarsi per qualsiasi tag. Se l'app deve limitare le registrazioni a specifici tag a client specifici, ad esempio quando i tag rappresentano gli ID utente, il back-end dell'app deve eseguire le registrazioni. Per ulteriori informazioni, vedere [gestione delle registrazioni](notification-hubs-push-notification-registration-management.md). Si noti che in questo modo, l'app client non avrà accesso diretto agli hub di notifica.

## <a name="security-claims"></a>Attestazioni di sicurezza

Analogamente ad altre entità, le operazioni degli hub di notifica sono consentite per tre attestazioni di sicurezza: **Ascolto**, **invio**e **gestione**.

| Attestazione   | Descrizione                                          | Operazioni consentite |
| ------- | ---------------------------------------------------- | ------------------ |
| Ascolto  | Creare o aggiornare, leggere ed eliminare singole registrazioni | Creare o aggiornare una registrazione<br><br>Leggere una registrazione<br><br>Leggere tutte le registrazioni per un handle<br><br>Eliminare una registrazione |
| Invio    | Inviare messaggi all'hub di notifica                | Invia messaggio |
| Gestisci  | CRUD negli hub di notifica (incluso l'aggiornamento delle credenziali PNS e le chiavi di sicurezza) e lettura delle registrazioni basata sui tag |Hub di notifica di creazione, aggiornamento, lettura ed eliminazione<br><br>Leggere le registrazioni per tag |

Hub di notifica accetta token di firma generati con chiavi condivise configurate direttamente nell'hub di notifica.

Non è possibile inviare una notifica a più di uno spazio dei nomi. Gli spazi dei nomi sono contenitori logici per gli hub di notifica e non sono interessati all'invio di notifiche.
I criteri di accesso a livello di spazio dei nomi possono essere usati per le operazioni a livello di spazio dei nomi, ad esempio: elenco di hub di notifica, creazione o eliminazione di hub di notifica e così via. Solo i criteri di accesso a livello di hub consentono di inviare le notifiche.
