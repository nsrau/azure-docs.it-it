---
title: Sicurezza per gli hub di notifica
description: In questo argomento viene illustrata la sicurezza degli hub di notifica di Azure.
services: notification-hubs
documentationcenter: .net
author: ysxu
manager: erikre
editor: 
ms.assetid: 6506177c-e25c-4af7-8508-a3ddca9dc07c
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: multiple
ms.topic: article
ms.date: 06/29/2016
ms.author: yuaxu
ms.openlocfilehash: 7c3283799806135060bb8ca57ea398c93d1106bb
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2017
---
# <a name="security"></a>Sicurezza
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

| Attestazione | Descrizione | Operazioni consentite |
| --- | --- | --- |
| Attesa |Creare o aggiornare, leggere ed eliminare singole registrazioni |Creare o aggiornare una registrazione<br><br>Leggere una registrazione<br><br>Leggere tutte le registrazioni per un handle<br><br>Eliminare una registrazione |
| Invio |Inviare messaggi all'hub di notifica |Send message |
| Manage |CRUD negli hub di notifica (incluso l'aggiornamento delle credenziali PNS e le chiavi di sicurezza) e lettura delle registrazioni basata sui tag |Hub di notifica di creazione, aggiornamento, lettura ed eliminazione<br><br>Leggere le registrazioni per tag |

Gli hub di notifica accettano attestazioni concesse dai token del Controllo di accesso di Microsoft Azure e dai token di firma generati con chiavi condivise configurate direttamente nell’hub di notifica.

