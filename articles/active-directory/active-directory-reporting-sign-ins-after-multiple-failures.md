---
title: "Accessi dopo più errori"
description: "Un report indica gli utenti che hanno eseguito correttamente l&quot;accesso dopo più tentativi di accesso consecutivi non riusciti."
services: active-directory
documentationcenter: 
author: SSalahAhmed
manager: femila
editor: 
ms.assetid: e4ec1a39-9c20-418f-8a75-6497d0117176
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/04/2016
ms.author: saah;kenhoff
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: e55e0145adbdb1f41a8b8753d5555f20e96bf161


---
# <a name="sign-ins-after-multiple-failures"></a>Accessi dopo più errori
Questo report indica gli utenti che hanno eseguito correttamente l'accesso dopo più tentativi di accesso consecutivi non riusciti. Le possibili cause includono:

* L'utente ha dimenticato la password</li><li>L’utente è vittima di un attacco di forza bruta per l’individuazione della password riuscito

I risultati di questo report mostreranno il numero di tentativi di accesso consecutivi non riusciti effettuati prima dell’accesso riuscito e un timestamp associato con il primo accesso riuscito.

**Impostazioni report**: è possibile configurare il numero minimo di tentativi di accesso consecutivi non riusciti che devono verificarsi affinché l’informazione sia segnalata nel report. Quando si apportano modifiche a questa impostazione si noti che le modifiche non si applicano a eventuali accessi non riusciti attualmente visualizzati nel report. Verranno tuttavia applicate a tutti gli accessi successivi. Le modifiche a questo report possono essere apportate solo dagli amministratori autorizzati.

![Accessi dopo più errori](./media/active-directory-reporting-sign-ins-after-multiple-failures/signInsAfterMultipleFailures.PNG)




<!--HONumber=Nov16_HO3-->


