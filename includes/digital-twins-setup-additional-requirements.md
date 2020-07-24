---
author: baanders
description: file di inclusione per possibili requisiti aggiuntivi nell'installazione di dispositivi gemelli digitali di Azure
ms.service: digital-twins
ms.topic: include
ms.date: 7/22/2020
ms.author: baanders
ms.openlocfilehash: b490b4304dd74d8266b24d0ea5af58726b14d747
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/24/2020
ms.locfileid: "87125802"
---
È possibile che l'organizzazione richieda azioni aggiuntive dai proprietari delle sottoscrizioni per impostare correttamente la registrazione di un'app (e, di conseguenza, per completare la configurazione di un'istanza di Azure Digital Twins utilizzabile). I passaggi necessari possono variare a seconda delle impostazioni specifiche dell'organizzazione.

Di seguito sono riportate alcune possibili attività comuni che un proprietario potrebbe dover eseguire. Queste e altre operazioni possono essere eseguite dalla pagina [*registrazioni app Azure ad*](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps) nel portale di Azure.
* Concedere il consenso dell'amministratore per la registrazione dell'app. È possibile che l'organizzazione abbia il *consenso dell'amministratore necessario* attivato a livello globale in Azure ad per tutte le registrazioni di app all'interno della sottoscrizione. In tal caso, il proprietario dovrà selezionare questo pulsante per la società nella pagina *delle autorizzazioni dell'API* di registrazione dell'app affinché la registrazione dell'app sia valida:

    :::image type="content" source="../articles/digital-twins/media/how-to-set-up-instance/grant-admin-consent.png" alt-text="Visualizzazione del portale del pulsante ' concedi il consenso dell'amministratore ' in autorizzazioni API":::
  - Se il consenso è stato concesso correttamente, la voce per i dispositivi gemelli digitali di Azure dovrebbe mostrare un valore di *stato* _concesso per **(la società)** _
   
    :::image type="content" source="../articles/digital-twins/media/how-to-set-up-instance/granted-admin-consent.png" alt-text="Visualizzazione del portale del consenso dell'amministratore concesso per la società in autorizzazioni API":::
  - Il proprietario potrebbe preferire di ripetere questo processo per ogni registrazione di app creata o di eseguire una sola volta e di stabilire una singola registrazione di app condivise per tutte le istanze di Azure Digital Twins nella sottoscrizione da usare. Nel secondo scenario, il proprietario deve condividere l'ID *client* e l' *ID tenant* per la registrazione dell'app con gli sviluppatori che dovranno usare la registrazione dell'app. (Questo è il modo in cui viene eseguito nel tenant Microsoft).
* Attivare l'accesso client pubblico
* Impostare URL di risposta specifici per l'accesso Web e desktop
* Consenti i flussi di autenticazione OAuth2 impliciti

Per altre informazioni sulla registrazione dell'app e sulle diverse opzioni, vedere [*registrare un'applicazione con la piattaforma di identità Microsoft*](https://docs.microsoft.com/graph/auth-register-app-v2).

A questo punto si ha un'istanza di Azure Digital Twins pronta per l'uso, sono state assegnate le autorizzazioni per gestirla e sono state configurate le autorizzazioni per l'accesso di un'app client.