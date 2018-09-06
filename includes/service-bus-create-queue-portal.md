---
title: File di inclusione
description: File di inclusione
services: service-bus-messaging
author: spelluru
ms.service: service-bus-messaging
ms.topic: include
ms.date: 06/29/2018
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 7ed298fc8f13685c4872c4c54ba1e447debea79f
ms.sourcegitcommit: cb61439cf0ae2a3f4b07a98da4df258bfb479845
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/05/2018
ms.locfileid: "43702637"
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
