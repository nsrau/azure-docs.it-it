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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "67325796"
---
1. Accedere al [portale di Azure].

2. Fare clic su **Crea una risorsa**.

3. Nella casella di ricerca digitare **app Web**.
    
4. Nell'elenco dei risultati selezionare **app Web** dal Marketplace.

5. Selezionare la **sottoscrizione** e il **gruppo di risorse** (selezionare un gruppo di risorse esistente _o_ crearne uno nuovo usando lo stesso nome dell'app).

6. Scegliere un **nome** univoco per l'app Web.

7. Scegliere l'opzione di **pubblicazione** predefinita come **codice**.

8. Nello **stack di runtime**è necessario selezionare una versione in **ASP.NET** o **node**. Se si compila un back-end .NET, selezionare una versione in ASP.NET. In caso contrario, se la destinazione è un'applicazione basata su nodi, selezionare una delle versioni dal nodo.

9. Selezionare il **sistema operativo**corretto, ovvero Linux o Windows. 

10. Selezionare l' **area** in cui si desidera distribuire l'app. 

11. Selezionare il **piano di servizio app** appropriato e fare clic su **Verifica e su Crea**. 

12. In **Gruppo di risorse** selezionare un gruppo di risorse esistente _oppure_ crearne uno nuovo usando lo stesso nome dell'app.

13. Fare clic su **Crea**. Prima di procedere, attendere il completamento della distribuzione del servizio. Osservare l'icona di notifica (a forma di campana) nell'intestazione del portale per informazioni sugli aggiornamenti dello stato.

14. Al termine della distribuzione, fare clic sulla sezione **Dettagli distribuzione** e quindi fare clic sulla risorsa di tipo **Microsoft. Web/sites**. Consente di accedere all'app Web del servizio app appena creata. 

15. Fare clic sul pannello **configurazione** in **Impostazioni** e in **Impostazioni applicazione**, fare clic sul pulsante **nuova impostazione applicazione** .

16. Nella pagina **Aggiungi/modifica impostazione applicazione** immettere il **nome** **MobileAppsManagement_EXTENSION_VERSION** e il valore come **più recente** e fare clic su OK.

È tutto pronto per usare questa app Web del servizio app appena creata come app per dispositivi mobili.

<!-- URLs. -->
[Azure portal]: https://portal.azure.com/