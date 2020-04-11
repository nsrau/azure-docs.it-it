---
title: Convalidare le regole per l'appartenenza ai gruppi dinamici (anteprima) - Azure AD Documenti Microsoft
description: Come testare i membri rispetto a una regola di appartenenza per un gruppo dinamico in Azure Active Directory.How to test members against a membership rule for a dynamic groups in Azure Active Directory.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 04/10/2020
ms.author: curtand
ms.reviewer: yukarppa
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 84e081f62949f4cf3f7ae375b2c60ee5b032d974
ms.sourcegitcommit: fb23286d4769442631079c7ed5da1ed14afdd5fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/10/2020
ms.locfileid: "81115521"
---
# <a name="validate-a-dynamic-group-membership-rule-preview-in-azure-active-directory"></a>Convalidare una regola di appartenenza a un gruppo dinamico (anteprima) in Azure Active DirectoryValidate a dynamic group membership rule (preview) in Azure Active Directory

Azure Active Directory (Azure AD) ora fornisce i mezzi per convalidare le regole dei gruppi dinamici (nell'anteprima pubblica). Nella scheda **Convalida regole** è possibile convalidare la regola dinamica rispetto ai membri del gruppo di esempio per verificare che la regola funzioni come previsto. Quando si creano o si aggiornano regole di gruppo dinamiche, gli amministratori desiderano sapere se un utente o un dispositivo sarà membro del gruppo. Ciò consente di valutare se l'utente o il dispositivo soddisfa i criteri della regola e facilita la risoluzione dei problemi quando non è prevista l'appartenenza.

## <a name="step-by-step-walk-through"></a>Passeggiata passo-passo

Per iniziare, passare a**Gruppi**di **Azure Active Directory** > . Selezionare un gruppo dinamico esistente o crearne uno nuovo e fare clic su Regole di appartenenza dinamiche. È quindi possibile visualizzare la scheda **Convalida regole.**

![Individuare la scheda Convalida regole e iniziare con una regola esistente](./media/groups-dynamic-rule-validation/validate-tab.png)

Nella scheda **Convalida regole** è possibile selezionare gli utenti per convalidare le appartenenze. È possibile selezionare 20 utenti o dispositivi contemporaneamente.

![Aggiungere utenti per convalidare la regola esistente](./media/groups-dynamic-rule-validation/validate-tab-add-users.png)

Dopo aver scelto gli utenti o i dispositivi dalla selezione e **Seleziona,** la convalida verrà avviata automaticamente e verranno visualizzati i risultati della convalida.

![Visualizzare i risultati della convalida della regola](./media/groups-dynamic-rule-validation/validate-tab-results.png)

I risultati indicano se un utente è membro del gruppo o meno. Se la regola non è valida o si verifica un problema di rete, il risultato verrà visualizzato come **Sconosciuto**. In caso di **sconosciuto**, il messaggio di errore dettagliato descriverà il problema e le azioni necessarie.

![Visualizzare i dettagli dei risultati della convalida della regola](./media/groups-dynamic-rule-validation/validate-tab-view-details.png)

Verrà attivata la modifica della regola e verrà attivata la convalida delle appartenenze. Per capire perché l'utente non è un membro del gruppo, fare clic su "Visualizza dettagli" e i dettagli di verifica mostreranno il risultato di ogni espressione che compone la regola. Fare clic **su OK** per uscire.

## <a name="next-steps"></a>Passaggi successivi

- [Regole di appartenenza dinamiche per i gruppi](groups-dynamic-membership.md)
