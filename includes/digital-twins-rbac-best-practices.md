---
title: File di inclusione
description: File di inclusione
services: digital-twins
author: kingdomofends
ms.service: digital-twins
ms.topic: include
ms.date: 11/13/2018
ms.author: adgera
ms.custom: include file
ms.openlocfilehash: 42aa275e692e4e2e9b7ca38825c828c1f56247fb
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/14/2018
ms.locfileid: "51628157"
---
Il controllo degli accessi in base al ruolo è una strategia di sicurezza basata sull'ereditarietà per la gestione degli accessi, delle autorizzazioni e dei ruoli. I ruoli discendenti ereditano le autorizzazioni dai ruoli padre. Le autorizzazioni, tuttavia, possono essere assegnate anche se non sono state ereditate da un ruolo padre e possono essere assegnate anche per personalizzare un ruolo in base alle esigenze.

È possibile, ad esempio, che un amministratore dello spazio richieda l'accesso globale per poter eseguire qualsiasi operazione in uno spazio specifico. L'accesso include tutti i nodi sottostanti. Per un installatore di dispositivi possono essere sufficienti le autorizzazioni di *lettura* e *aggiornamento* per i dispositivi e i sensori.

In ogni caso, ai ruoli sono concesse le *autorizzazioni di accesso strettamente necessarie* per completare le attività assegnate in base al principio del privilegio minimo. In base a questo principio, a un'identità è concesso *solamente*:

* Il livello di accesso necessario per completare il processo assegnato.
* Un ruolo appropriato e limitato allo svolgimento del processo assegnato.

>[!IMPORTANT]
> Seguire sempre il principio del privilegio minimo.

Di seguito sono riportate altre due procedure importanti relative al controllo degli accessi in base al ruolo:

> [!div class="checklist"]
> * Controllare periodicamente le assegnazioni di ruolo per verificare che ogni ruolo disponga delle autorizzazioni corrette.
> * Pulire i ruoli e le assegnazioni in caso di variazione dei ruoli o delle assegnazioni degli utenti.