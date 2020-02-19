---
title: Specificare le immagini del Marketplace per un Lab in Azure Lab Services
description: Questo articolo illustra come specificare le immagini del Marketplace che Lab Creator può usare per creare Lab in un account Lab in Azure Lab Services.
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
ms.sourcegitcommit: 6e87ddc3cc961945c2269b4c0c6edd39ea6a5414
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/18/2020
ms.locfileid: "77444667"
---
# <a name="specify-marketplace-images-available-to-lab-creators"></a>Specificare le immagini del Marketplace disponibili per gli autori di lab
In quanto proprietario di un account del lab, è possibile specificare le immagini del Marketplace che gli autori di lab possono usare per creare lab nell'account del lab. 

## <a name="select-images-available-for-labs"></a>Selezionare le immagini disponibili per i Lab
Selezionare **Immagini del Marketplace** nel menu a sinistra. Per impostazione predefinita, verrà visualizzato l'elenco completo delle immagini (abilitate e disabilitate). È possibile filtrare l'elenco per visualizzare solo le immagini abilitate o disabilitate selezionando l'opzione **Enabled only**/**Disabled only** (Solo abilitate, Solo disabilitate) nell'elenco a discesa nella parte superiore. 
    
![Pagina Immagini del Marketplace](../media/tutorial-setup-lab-account/marketplace-images-page.png)

Nell'elenco vengono visualizzate solo le immagini del Marketplace che soddisfano le condizioni seguenti:
    
- Crea una singola macchina virtuale.
- Usa Azure Resource Manager per il provisioning di macchine virtuali
- Non richiede l'acquisto di un piano di licenze aggiuntivo

## <a name="disable-images-for-a-lab"></a>Disabilitare le immagini per un Lab 
Per disabilitare una singola immagine per un Lab, selezionare **... (puntini** di sospensione) nell'ultima colonna, quindi selezionare **Disabilita immagine**. 

![Disabilitare un'immagine](../media/tutorial-setup-lab-account/disable-one-image.png) 

In alternativa, è possibile selezionare la casella di controllo prima del nome dell'immagine e selezionare **Disattiva immagini selezionate** sulla barra degli strumenti. 

Per disabilitare più immagini contemporaneamente, selezionare le caselle di controllo prima dei nomi delle immagini e selezionare **Disattiva immagini selezionate** sulla barra degli strumenti. 

![Disabilitare più immagini](../media/tutorial-setup-lab-account/disable-multiple-images.png) 


## <a name="enable-images-for-a-lab"></a>Abilitare le immagini per un Lab
Per abilitare un'immagine disabilitata, selezionare **... (puntini** di sospensione) nell'ultima colonna, quindi selezionare **Abilita immagine**. In alternativa, è possibile selezionare la casella di controllo prima del nome dell'immagine e selezionare **Abilita immagini selezionate** sulla barra degli strumenti. 

Per disabilitare più immagini contemporaneamente, selezionare le caselle di controllo prima dei nomi delle immagini e selezionare **Abilita immagini selezionate** sulla barra degli strumenti. 

## <a name="next-steps"></a>Passaggi successivi
Vedere gli articoli seguenti:

- [Creare e gestire lab come proprietario](how-to-manage-classroom-labs.md)
- [Configurare e pubblicare modelli come proprietario](how-to-create-manage-template.md)
- [Configurare e controllare l'uso di un lab come proprietario](how-to-configure-student-usage.md)
- [Come utente di lab, accedere ai lab per le classi](how-to-use-classroom-lab.md)
