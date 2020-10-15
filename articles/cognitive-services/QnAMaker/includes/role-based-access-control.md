---
title: includere file
description: includere file
ms.topic: include
ms.custom: include file
ms.date: 03/11/2020
ms.openlocfilehash: b34d3ed2481600cde7ffb3275d635b8a57926bd6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "83665949"
---
Per la collaborazione sono disponibili i seguenti ruoli:

|Ruolo|Funzionalità|Accesso all'API|Autorizzazioni delle API|
|--|--|--|--|
|Proprietario|Tutti|Chiave di autenticazione|Tutti|
|Autore di contributi|Tutti tranne la possibilità di aggiungere nuovi membri ai ruoli|Chiave di autenticazione|Tutti tranne la possibilità di aggiungere nuovi membri ai ruoli|
|Lettura QnA Maker<br>lettura|Esporta/Scarica<br>Test|Bearer token|1. scaricare l'API KB<br>2. elencare i KB per l'API utente<br>3. ottenere i dettagli della Knowledge base<br>4. scaricare le modifiche<br>Genera risposta |
|Editor QnA Maker<br>(lettura/scrittura)|Esporta/Scarica<br>Test<br>Aggiornamento KB<br>Esporta KB<br>Importa KB<br>Sostituisci KB<br>Creare la knowledge base|Bearer token|1. creare un'API KB<br>2. aggiornare l'API KB<br>3. sostituire l'API KB<br>4. sostituire le modifiche<br>5. "Train API" [nel nuovo modello di servizio v5]|
|Utente del servizio cognitivo<br>(lettura/scrittura/pubblicazione)|Tutti|Bearer token|Tutti|