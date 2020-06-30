---
author: mikeparker104
ms.author: miparker
ms.date: 06/02/2020
ms.service: notification-hubs
ms.topic: include
ms.openlocfilehash: 7c3887367b6365ea3577bbff6cc19bf34d178ee6
ms.sourcegitcommit: 971a3a63cf7da95f19808964ea9a2ccb60990f64
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/19/2020
ms.locfileid: "85095505"
---
### <a name="create-a-notification-hub"></a>Creare un hub di notifica 

In questa sezione viene creato un hub di notifica e viene configurata l'autenticazione con **APNS**. È possibile usare un certificato push p12 o l'autenticazione basata su token. Se si vuole usare un hub di notifica che è già stato creato, è possibile ignorare il passaggio 5.

1. Accedere ad [Azure](https://portal.azure.com).

1. Fare clic su **Crea una risorsa**, quindi cercare e scegliere **Hub di notifica** e fare clic su **Crea**.

1. Aggiornare i campi seguenti, quindi fare clic su **Crea**:

    **DETTAGLI DI BASE**  

    **Sottoscrizione:** scegliere la sottoscrizione di destinazione dall'elenco a discesa **Sottoscrizione**  
    **Gruppo di risorse:** creare un nuovo **gruppo di risorse** o selezionarne uno esistente  

    **DETTAGLI DELLO SPAZIO DEI NOMI**  

    **Spazio dei nomi di Hub di notifica:** immettere un nome univoco globale per lo spazio dei nomi di **Hub di notifica**  

    > [!NOTE]
    > Verificare che sia selezionata l'opzione **Crea nuovo** per questo campo.

    **DETTAGLI DELL'HUB DI NOTIFICA**  

    **Hub di notifica:** immettere un nome per **Hub di notifica**  
    **Località:** scegliere una località idonea dall'elenco a discesa  
    **Piano tariffario:** mantenere l'opzione predefinita **Gratuito**  

    > [!NOTE]
    > A meno che non sia stato raggiunto il numero massimo di hub per il livello gratuito.

1. Dopo il provisioning di **Hub di notifica**, passare a tale risorsa.
1. Passare al nuovo **hub di notifica**.
1. Selezionare **Criteri di accesso** dall'elenco (in **GESTISCI**).
1. Prendere nota dei valori di **Nome criteri** insieme ai corrispondenti valori di **Stringa di connessione**.
