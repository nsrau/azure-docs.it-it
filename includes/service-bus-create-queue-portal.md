---
title: File di inclusione
description: File di inclusione
services: service-bus-messaging
author: sethmanheim
ms.service: service-bus-messaging
ms.topic: include
ms.date: 06/29/2018
ms.author: sethm
ms.custom: include file
ms.openlocfilehash: bad7e87cb194fb7d8846e7e675881658a1d33b1e
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/29/2018
ms.locfileid: "37138161"
---
Assicurarsi che sia già stato creato uno spazio dei nomi del bus di servizio, come illustrato [qui][namespace-how-to].

1. Accedere al [portale di Azure][azure-portal].
2. Nel riquadro di spostamento a sinistra del portale fare clic su **Bus di servizio**. Se l'opzione **Bus di servizio** non è visualizzata, fare clic su **Tutti i servizi**.
3. Fare clic sullo spazio dei nomi in cui si vuole creare la coda. In questo caso è **sbnstest1**.
   
    ![Creare una coda][createqueue1]
4. Nella finestra dello spazio dei nomi fare clic su **Code** e quindi nella finestra **Code** fare clic su **+ Coda**.
   
    ![Selezionare Code][createqueue2]
5. Immettere il nome della coda in **Nome** e lasciare le impostazioni predefinite per gli altri valori.
   
    ![Selezionare Nuovo][createqueue3]
6. Nella parte inferiore della finestra fare clic su **Crea**.

[createqueue1]: ./media/service-bus-create-queue-portal/create-queue1.png
[createqueue2]: ./media/service-bus-create-queue-portal/create-queue2.png
[createqueue3]: ./media/service-bus-create-queue-portal/create-queue3.png

[namespace-how-to]: ../articles/service-bus-messaging/service-bus-create-namespace-portal.md
[azure-portal]: https://portal.azure.com
