---
author: rockboyfor
ms.service: virtual-machines
ms.topic: include
origin.date: 10/26/2018
ms.date: 11/26/2018
ms.author: v-yeche
ms.openlocfilehash: e128f3c67a41322d9c25a8d6941e937729760bf4
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "62097666"
---
In questo passaggio si creerà una regola del firewall per aprire la porta probe per l'endpoint con carico bilanciato (59999, come specificato in precedenza) e un'altra regola per aprire la porta del listener del gruppo disponibilità. Poiché è stato creato l'endpoint con carico bilanciato nelle VM contenenti repliche del gruppo di disponibilità, è necessario aprire la porta probe e la porta del listener sulle rispettive macchine virtuali.

1. Nelle macchine virtuali che ospitano le repliche, avviare **Windows Firewall con sicurezza avanzata**.

2. Fare clic con il pulsante destro del mouse su **Regole connessioni in entrata** e quindi scegliere **Nuova regola**.

3. Nella pagina **Tipo di regola** selezionare **Porta** e quindi fare clic su **Avanti**.

4. Nella pagina **Protocollo e porte** selezionare **TCP**, digitare **59999** nella casella **Porte locali specifiche** e quindi fare clic su **Avanti**.

5. Nella pagina **Azione** mantenere selezionata l'opzione **Consenti la connessione** e quindi fare clic su **Avanti**.

6. Nella pagina **Profilo** accettare le impostazioni predefinite e fare clic su **Avanti**.

7. Nella pagina **Nome** specificare nella casella di testo **Nome** un nome per la regola, ad esempio **Porta probe del listener AlwaysOn** e quindi fare clic su **Fine**.

8. Ripetere i passaggi precedenti per la porta del listener del gruppo di disponibilità (come specificato in precedenza nel parametro $EndpointPort dello script) e specificare un nome di regola appropriato, ad esempio **Porta del Listener AlwaysOn**.

<!-- Update_Description: update meta properties -->