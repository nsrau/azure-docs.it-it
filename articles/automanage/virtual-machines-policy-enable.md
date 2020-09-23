---
title: Abilitare la gestione automatici per le macchine virtuali tramite criteri di Azure
description: Informazioni su come abilitare la gestione autonoma di Azure per le macchine virtuali tramite criteri predefiniti di Azure nella portale di Azure.
author: ju-shim
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: how-to
ms.date: 09/04/2020
ms.author: jushiman
ms.openlocfilehash: 47f4085ff01526853fab29da2c1bc1a3e8998d23
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90937652"
---
# <a name="enable-automanage-for-virtual-machines-through-azure-policy"></a>Abilitare la gestione automatici per le macchine virtuali tramite criteri di Azure

Se si vuole abilitare la gestione autonoma per molte macchine virtuali, è possibile usare criteri predefiniti di [Azure](..\governance\azure-management.md). Questo articolo illustra in modo dettagliato come trovare i criteri corretti e come assegnarli per abilitare la gestione di automanage nella portale di Azure.


## <a name="prerequisites"></a>Prerequisiti

Se non si ha una sottoscrizione di Azure, [creare un account](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/) prima di iniziare.

> [!NOTE]
> Gli account di valutazione gratuiti non hanno accesso alle macchine virtuali usate in questa esercitazione. Eseguire l'aggiornamento a una sottoscrizione con pagamento in base al consumo.

> [!IMPORTANT]
> Per abilitare la gestione, è necessaria l'autorizzazione RBAC seguente: ruolo **proprietario** o **collaboratore** insieme ai ruoli di **amministratore accesso utenti** .


## <a name="sign-in-to-azure"></a>Accedere ad Azure

Accedere al [portale di Azure](https://portal.azure.com/).


## <a name="locate-and-assign-the-policy"></a>Individuare e assegnare i criteri

1. Passare a **criteri** nella portale di Azure
1. Vai al riquadro **definizioni**
1. Fare clic sull'elenco a discesa **categorie** per visualizzare le opzioni disponibili
1. Selezionare l'opzione **Abilita automanage-procedure consigliate per macchine virtuali di Azure**
1. A questo punto l'elenco verrà aggiornato per mostrare un criterio predefinito con un nome che inizia con *Abilita automanage...*
1. Fare clic sul nome dei criteri predefiniti delle *procedure consigliate per la gestione automatica-macchine virtuali di Azure*
1. Dopo aver fatto clic sul criterio, è ora possibile visualizzare la scheda **definizione**

    > [!NOTE]
    > La definizione di criteri di Azure viene usata per impostare i parametri di gestione automatica come il profilo di configurazione o l'account. Imposta anche i filtri che assicurano che i criteri vengano applicati solo alle VM corrette.

1. Fare clic sul pulsante **assegna** per creare un'assegnazione
1. Nella scheda **nozioni di base** compilare l' **ambito** impostando la *sottoscrizione* e il gruppo di *risorse*

    > [!NOTE]
    > L'ambito consente di definire le macchine virtuali a cui si applicano i criteri. È possibile impostare l'applicazione a livello di sottoscrizione o di gruppo di risorse. Se si imposta un gruppo di risorse, tutte le VM attualmente presenti in tale gruppo di risorse o le macchine virtuali future a cui si aggiungeranno automaticamente verranno abilitate automaticamente. 

1. Fare clic sulla scheda **parametri** e impostare l' **account di gestione** automatica e il **profilo di configurazione** desiderato 
1. Nella scheda **Revisione + creazione** esaminare le impostazioni
1. Per applicare l'assegnazione, fare clic su **Crea**
1. Visualizzare le assegnazioni nella scheda **assegnazioni** accanto a **definizione**

> [!NOTE]
> Il criterio inizierà a essere applicato sulle VM attualmente presenti nel gruppo di risorse o nella sottoscrizione.


## <a name="next-steps"></a>Passaggi successivi 

Altre informazioni su come abilitare la gestione automatici di Azure per le macchine virtuali tramite il portale di Azure. 

> [!div class="nextstepaction"]
> [Abilitare la gestione automatici per le macchine virtuali nella portale di Azure](quick-create-virtual-machines-portal.md)