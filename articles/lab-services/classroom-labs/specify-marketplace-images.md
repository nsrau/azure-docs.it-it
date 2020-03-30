---
title: Specificare le immagini del marketplace per un lab in Azure Lab ServicesSpecify marketplace images for a lab in Azure Lab Services
description: Questo articolo illustra come specificare le immagini del Marketplace che l'autore del lab può usare per creare lab in un account lab in Azure Lab Services.This article shows you how to specify the Marketplace images that lab creator can use to create labs in a lab account in Azure Lab Services.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/14/2020
ms.author: spelluru
ms.openlocfilehash: ad56041f853d030e3a286610fe4872bffecaee12
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77444667"
---
# <a name="specify-marketplace-images-available-to-lab-creators"></a>Specificare le immagini del Marketplace disponibili per gli autori di lab
In quanto proprietario di un account del lab, è possibile specificare le immagini del Marketplace che gli autori di lab possono usare per creare lab nell'account del lab. 

## <a name="select-images-available-for-labs"></a>Selezionare le immagini disponibili per i laboratori
Selezionare **Immagini del Marketplace** nel menu a sinistra. Per impostazione predefinita, verrà visualizzato l'elenco completo delle immagini (abilitate e disabilitate). È possibile filtrare l'elenco per visualizzare solo le immagini abilitate/disabilitate selezionando l'opzione **Solo abilitato**/**Solo disattivato** dall'elenco a discesa in alto. 
    
![Pagina Immagini del Marketplace](../media/tutorial-setup-lab-account/marketplace-images-page.png)

Nell'elenco vengono visualizzate solo le immagini del Marketplace che soddisfano le condizioni seguenti:
    
- Crea una singola macchina virtuale.
- Usa Azure Resource Manager per il provisioning di macchine virtuali
- Non richiede l'acquisto di un piano di licenze aggiuntivo

## <a name="disable-images-for-a-lab"></a>Disabilitare le immagini per un labDisable images for a lab 
Per disabilitare una singola immagine per un lab, selezionare **... (ellipsi)** nell'ultima colonna e selezionare **Disattiva immagine**. 

![Disabilitare un'immagine](../media/tutorial-setup-lab-account/disable-one-image.png) 

In alternativa, selezionare la casella di controllo prima del nome dell'immagine e selezionare **Disattiva le immagini selezionate** sulla barra degli strumenti. 

Per disabilitare più immagini contemporaneamente, selezionare le caselle di controllo prima dei nomi delle immagini e selezionare **Disattiva le immagini selezionate** sulla barra degli strumenti. 

![Disabilitare più immagini](../media/tutorial-setup-lab-account/disable-multiple-images.png) 


## <a name="enable-images-for-a-lab"></a>Abilitare le immagini per un labEnable images for a lab
Per abilitare un'immagine disabilitata, selezionare **... (ellipsi)** nell'ultima colonna e selezionare **Abilita immagine**. In alternativa, selezionare la casella di controllo prima del nome dell'immagine e selezionare **Abilita immagini selezionate** sulla barra degli strumenti. 

Per disabilitare più immagini contemporaneamente, selezionare le caselle di controllo prima dei nomi delle immagini e selezionare **Abilita immagini selezionate** sulla barra degli strumenti. 

## <a name="next-steps"></a>Passaggi successivi
Vedere gli articoli seguenti:

- [Creare e gestire lab come proprietario](how-to-manage-classroom-labs.md)
- [Configurare e pubblicare modelli come proprietario](how-to-create-manage-template.md)
- [Configurare e controllare l'uso di un lab come proprietario](how-to-configure-student-usage.md)
- [Come utente di lab, accedere ai lab per le classi](how-to-use-classroom-lab.md)
