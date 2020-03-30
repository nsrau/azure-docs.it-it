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
1. Accedere al [portale]di Azure .

2. Fare clic su **Crea una risorsa**.

3. Nella casella di ricerca digitare **App Web**.
    
4. Nell'elenco dei risultati selezionare **App Web** dal Marketplace.

5. Selezionare la sottoscrizione e il **gruppo di risorse** (selezionare un gruppo di risorse esistente o crearne uno nuovo (usando lo stesso nome dell'app).Select your **Subscription** and Resource Group (select an existing resource group _or_ create a new one (using the same name as your app)).

6. Scegliere un **nome** univoco dell'app Web.

7. Scegliere l'opzione **di pubblicazione** predefinita come **Codice**.

8. Nello **stack Runtime**, è necessario selezionare una versione in **ASP.NET** o **Nodo**. Se si sta creando un back-end .NET, selezionare una versione in ASP.NET. In caso contrario, se si sta la destinazione di un'applicazione basata su nodo, selezionare una delle versioni da Nodo.

9. Seleziona il **sistema operativo**giusto, Linux o Windows. 

10. Selezionare **l'area** in cui si desidera distribuire l'app. 

11. Selezionare il piano di **servizio app** appropriato e premere **Rivedi e crea**. 

12. In **Gruppo di risorse** selezionare un gruppo di risorse esistente _oppure_ crearne uno nuovo usando lo stesso nome dell'app.

13. Fare clic su **Crea**. Prima di procedere, attendere il completamento della distribuzione del servizio. Osservare l'icona di notifica (a forma di campana) nell'intestazione del portale per informazioni sugli aggiornamenti dello stato.

14. Una volta completata la distribuzione, fare clic sulla sezione **Dettagli distribuzione** e quindi fare clic sulla risorsa di tipo **Microsoft Web/sites**. Si passerà all'app Web del servizio app appena creata. 

15. Fare clic sul pannello **Configurazione** in **Impostazioni** e in **Impostazioni applicazione**fare clic sul pulsante **Nuova impostazione applicazione.**

16. Nella pagina **Aggiungi/Modifica impostazione applicazione** immettere **Nome** come **MobileAppsManagement_EXTENSION_VERSION** e Valore come **più recente** e premere OK.

È tutto pronto per utilizzare questa app Web del servizio app appena creata come app per dispositivi mobili.

<!-- URLs. -->
[Portale di Azure]: https://portal.azure.com/