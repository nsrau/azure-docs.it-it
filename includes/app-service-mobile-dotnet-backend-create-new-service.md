---
title: File di inclusione
description: File di inclusione
services: app-service\mobile
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 06/20/2019
ms.author: crdun
ms.custom: include file
ms.openlocfilehash: 72a69359d412a7560472fbb73ec525ab5d4a4fce
ms.sourcegitcommit: 5cb0b6645bd5dff9c1a4324793df3fdd776225e4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/21/2019
ms.locfileid: "67325796"
---
1. Accedere al [portale di Azure].

2. Fare clic su **Crea una risorsa**.

3. Nella casella di ricerca, digitare **App Web**.
    
4. Nell'elenco dei risultati, selezionare **App Web** dal Marketplace.

5. Selezionare i **abbonamento** e **gruppo di risorse** (selezionare un gruppo di risorse _o_ crearne uno nuovo (usando lo stesso nome dell'app)).

6. Scegliere un valore univoco **nome** dell'app web.

7. Scegliere l'impostazione predefinita **Publish** opzione come **codice**.

8. Nel **stack di Runtime**, è necessario selezionare una versione sotto **ASP.NET** oppure **nodo**. Se si sta creando un back-end .NET, selezionare una versione in ASP.NET. In caso contrario, se la destinazione è un'applicazione basata sui nodi, selezionare uno della versione dal nodo.

9. Selezionare il diritto **sistema operativo**, Linux o Windows. 

10. Selezionare il **regione** dove si desidera che questa app da distribuire. 

11. Selezionare un valore appropriato **piano di servizio App** e fare clic su **revisione e creare**. 

12. In **Gruppo di risorse** selezionare un gruppo di risorse esistente _oppure_ crearne uno nuovo usando lo stesso nome dell'app.

13. Fare clic su **Create**(Crea). Prima di procedere, attendere il completamento della distribuzione del servizio. Osservare l'icona di notifica (a forma di campana) nell'intestazione del portale per informazioni sugli aggiornamenti dello stato.

14. Una volta completata la distribuzione, fare clic sui **dettagli di distribuzione** della sezione e quindi fare clic su risorsa di tipo **Web/Sites**. Lo si passerà all'App Web del servizio App appena creata. 

15. Fare clic sul **Configuration** pannello sotto **impostazioni** e nel **le impostazioni dell'applicazione**, fare clic sul **nuova impostazione dell'applicazione** pulsante.

16. Nel **impostazione dell'applicazione Add/Edit** pagina, immettere **nome** come **MobileAppsManagement_EXTENSION_VERSION** e lo stesso valore **più recente** e Fare clic su OK.

Sono tutti impostati su questa scheda appena creata l'app Web del servizio App come app per dispositivi mobili.

<!-- URLs. -->
[Portale di Azure]: https://portal.azure.com/