---
title: Specificare le immagini del Marketplace per un Lab in Azure Lab Services
description: Questo articolo illustra come specificare le immagini del Marketplace che Lab Creator può usare per creare Lab in un account Lab in Azure Lab Services.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 5c81c8d7a15d67055729a29e98f5b7e30f3d0764
ms.sourcegitcommit: 5e5a0abe60803704cf8afd407784a1c9469e545f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96434737"
---
# <a name="specify-marketplace-images-available-to-lab-creators"></a>Specificare le immagini del Marketplace disponibili per gli autori di lab
In quanto proprietario di un account del lab, è possibile specificare le immagini del Marketplace che gli autori di lab possono usare per creare lab nell'account del lab. 

## <a name="select-images-available-for-labs"></a>Selezionare le immagini disponibili per i Lab
Selezionare **Immagini del Marketplace** nel menu a sinistra. Per impostazione predefinita, verrà visualizzato l'elenco completo delle immagini (abilitate e disabilitate). È possibile filtrare l'elenco in modo da visualizzare solo le immagini abilitate o **Enabled only** disabilitate selezionando l'opzione solo / **disabilitato solo disabilitato** nell'elenco a discesa nella parte superiore. 
    
![Pagina Immagini del Marketplace](./media/tutorial-setup-lab-account/marketplace-images-page.png)

Nell'elenco vengono visualizzate solo le immagini del Marketplace che soddisfano le condizioni seguenti:
    
- Crea una singola macchina virtuale.
- Usa Azure Resource Manager per il provisioning di macchine virtuali
- Non richiede l'acquisto di un piano di licenze aggiuntivo

## <a name="disable-images-for-a-lab"></a>Disabilitare le immagini per un Lab 
Per disabilitare una singola immagine per un Lab, selezionare **... (puntini** di sospensione) nell'ultima colonna, quindi selezionare **Disabilita immagine**. 

![Disabilitare un'immagine](./media/tutorial-setup-lab-account/disable-one-image.png) 

In alternativa, è possibile selezionare la casella di controllo prima del nome dell'immagine e selezionare **Disattiva immagini selezionate** sulla barra degli strumenti. 

Per disabilitare più immagini contemporaneamente, selezionare le caselle di controllo prima dei nomi delle immagini e selezionare **Disattiva immagini selezionate** sulla barra degli strumenti. 

![Disabilitare più immagini](./media/tutorial-setup-lab-account/disable-multiple-images.png) 


## <a name="enable-images-for-a-lab"></a>Abilitare le immagini per un Lab
Per abilitare un'immagine disabilitata, selezionare **... (puntini** di sospensione) nell'ultima colonna, quindi selezionare **Abilita immagine**. In alternativa, è possibile selezionare la casella di controllo prima del nome dell'immagine e selezionare **Abilita immagini selezionate** sulla barra degli strumenti. 

Per disabilitare più immagini contemporaneamente, selezionare le caselle di controllo prima dei nomi delle immagini e selezionare **Abilita immagini selezionate** sulla barra degli strumenti. 

## <a name="enable-images-at-the-time-of-lab-creation"></a>Abilita le immagini al momento della creazione del Lab
È possibile abilitare più immagini al momento della creazione del Lab: 

1. Accedere al [sito Web di Azure Lab Services](https://labs.azure.com) usando le credenziali del **proprietario dell'account Lab**
2. Selezionare l'immagine della macchina virtuale predefinita o la freccia verso il basso. 
3. Selezionare **Abilita altre opzioni** per le immagini. 

    ![Abilita altre opzioni per le immagini](./media/specify-marketplace-images/enable-more-images-menu.png)
4. Per abilitare le immagini selezionate, seguire le istruzioni riportate nella sezione precedente. 
5. Potrebbe essere necessario chiudere la nuova finestra del **Lab** e riaprirla per visualizzare le immagini selezionate nel passaggio precedente. 



## <a name="next-steps"></a>Passaggi successivi
Vedere gli articoli seguenti:

- [Creare e gestire lab come proprietario](how-to-manage-classroom-labs.md)
- [Configurare e pubblicare modelli come proprietario](how-to-create-manage-template.md)
- [Configurare e controllare l'uso di un lab come proprietario](how-to-configure-student-usage.md)
- [Come utente del Lab, Access Labs](how-to-use-classroom-lab.md)
