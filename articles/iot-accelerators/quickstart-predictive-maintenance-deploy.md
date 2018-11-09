---
title: Provare una soluzione di manutenzione predittiva IoT basata sul cloud in Azure | Microsoft Docs
description: In questa guida introduttiva si vedrà come distribuire l'acceleratore della soluzione IoT di Azure per la manutenzione predittiva e quindi come accedere al dashboard della soluzione e usarlo.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: quickstart
ms.custom: mvc
ms.date: 10/26/2018
ms.author: dobett
ms.openlocfilehash: c56bdb5968b5f5ada902aa9212282c3cfebe2eaf
ms.sourcegitcommit: 0f54b9dbcf82346417ad69cbef266bc7804a5f0e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/26/2018
ms.locfileid: "50139840"
---
# <a name="quickstart-try-a-cloud-based-solution-to-run-a-predictive-maintenance-analysis-on-my-connected-devices"></a>Guida introduttiva: Provare una soluzione basata sul cloud per eseguire un'analisi di manutenzione predittiva sui dispositivi connessi

Questa guida introduttiva illustra come distribuire l'acceleratore di soluzione Manutenzione predittiva di Azure IoT per eseguire una simulazione di manutenzione predittiva basata sul cloud. Dopo aver distribuito l'acceleratore di soluzione, si usa la pagina **Dashboard** della soluzione per eseguire una simulazione di manutenzione predittiva usando i dati da un motore di aeromobile simulato. È possibile usare questo acceleratore di soluzione come punto di partenza per un'implementazione personalizzata o come strumento di apprendimento.

In questa simulazione, Fabrikam è una compagnia aerea locale dedicata a offrire esperienze eccezionali ai clienti a prezzi competitivi. Una causa di ritardi dei voli sono i problemi di manutenzione e la manutenzione dei motori degli aeromobili è particolarmente complessa. Fabrikam deve evitare a tutti i costi i guasti dei motori durante il volo, quindi controlla regolarmente i motori e pianifica la manutenzione in base a un programma. Tuttavia, i motori non sempre si usurano allo stesso modo. Sui motori viene eseguita manutenzione superflua. Ancora più importante, si verificano problemi che possono forzare a terra un aereo fino a quando non viene eseguita la manutenzione. Se un aereo si trova in un luogo in cui tecnici esperti o parti di ricambio non sono disponibili, questi problemi possono essere particolarmente costosi.

I motori degli aeromobili Fabrikam sono instrumentati con sensori che controllano le condizioni del motore durante il volo. Fabrikam ha anni di dati operativi e di guasto del motore da questi sensori. I data scientist di Fabrikam hanno usato questi dati per sviluppare un modello per prevedere la vita utile rimanente (RUL) di un motore di un aereo. Il modello usa una correlazione tra i dati provenienti da quattro dei sensori del motore e l'usura del motore che conduce a un eventuale guasto. Mentre Fabrikam continua a eseguire controlli di sicurezza periodici, ora può usare i modelli per calcolare il valore RUL per ogni motore dopo ogni volo. Fabrikam può ora prevedere i futuri punti di guasto e pianificare la manutenzione per ridurre al minimo il tempo di fermo a terra degli aeromobili. Questo processo riduce i costi operativi garantendo nel contempo la sicurezza dei passeggeri e dell'equipaggio.

Per completare questa guida introduttiva, è necessaria una sottoscrizione di Azure attiva.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="deploy-the-solution"></a>Distribuire la soluzione

Prima di distribuire l'acceleratore della soluzione nella sottoscrizione di Azure, è necessario impostare alcune opzioni di configurazione.

Passare ad [Acceleratori di soluzioni Microsoft Azure IoT](https://www.azureiotsolutions.com) e accedere usando le credenziali dell'account Azure.

Fare clic sul riquadro **Manutenzione predittiva**. Nella pagina **Manutenzione predittiva** fare clic su **Prova adesso**:

![Prova adesso](./media/quickstart-predictive-maintenance-deploy/predictivemaintenance.png)

Nella pagina **Crea soluzione Manutenzione predittiva** immettere un valore univoco per **Nome soluzione** per l'acceleratore della soluzione di manutenzione predittiva. Per questa guida introduttiva viene usato il nome **MyPredictiveMaintenance**.

Selezionare **Sottoscrizione** e **Area** da usare per distribuire l'acceleratore della soluzione. In genere, si sceglie l'area più vicina. Per questa guida introduttiva selezionare **Visual Studio Enterprise** e **Stati Uniti orientali**. È necessario essere [amministratore globale o utente](iot-accelerators-permissions.md) nella sottoscrizione.

Fare clic su **Create Solution** (Crea soluzione) per iniziare la distribuzione. Questo processo richiede almeno cinque minuti:

![Dettagli della soluzione di manutenzione predittiva](./media/quickstart-predictive-maintenance-deploy/createform.png)

## <a name="sign-in-to-the-solution"></a>Accedere alla soluzione

Al termine della distribuzione nella sottoscrizione di Azure, verrà visualizzato un segno di spunta verde e la dicitura **Pronto** nel riquadro della soluzione. È ora possibile accedere al dashboard dell'acceleratore di soluzione Manutenzione predittiva.

Nella pagina **Soluzioni di cui è stato effettuato il provisioning** fare clic sul nuovo acceleratore della soluzione di manutenzione predittiva. È possibile visualizzare informazioni sull'acceleratore della soluzione nel pannello visualizzato. Scegliere **Dashboard soluzione** per visualizzare l'acceleratore della soluzione di manutenzione predittiva:

![Riquadro della soluzione](./media/quickstart-predictive-maintenance-deploy/solutionpanel.png)

Fare clic su **Accetta** per accettare la richiesta di autorizzazioni. Il dashboard della soluzione di manutenzione predittiva viene visualizzato nel browser:

![Dashboard della soluzione](./media/quickstart-predictive-maintenance-deploy/solutiondashboard.png)

Fare clic su **Avvia simulazione** per iniziare la simulazione. Il dashboard viene popolato con cronologia del sensore, vita utile rimanente, cicli e cronologia della vita utile rimanente:

![Simulazione in esecuzione](./media/quickstart-predictive-maintenance-deploy/simulationrunning.png)

Quando la vita utile rimanente è inferiore a 160, una soglia arbitraria scelta a scopo dimostrativo, il portale della soluzione mostra un simbolo di avviso accanto alla visualizzazione della vita utile rimanente. Il portale della soluzione evidenzia anche in giallo il motore dell'aereo. Si noti come i valori della vita utile rimanente abbiano complessivamente una tendenza generale al ribasso, ma tendano a oscillare in alto o in basso. Questo comportamento dovuto alle diverse lunghezze dei cicli e all'accuratezza del modello.

![Avviso di simulazione](./media/quickstart-predictive-maintenance-deploy/simulationwarning.png)

La simulazione completa richiede circa 35 minuti per completare 148 cicli. La soglia di 160 per la vita utile rimanente viene raggiunta per la prima volta dopo circa 5 minuti ed entrambi i motori la raggiungono a circa 8 minuti.

La simulazione viene eseguita sul set di dati completo per 148 cicli e si stabilizza sui valori finali dei cicli e della vita utile rimanente.

È possibile arrestare la simulazione in qualsiasi punto, ma facendo clic su **Start Simulation** viene riprodotta la simulazione dall'inizio del set di dati.

## <a name="clean-up-resources"></a>Pulire le risorse

Se si prevede di esplorare la soluzione ulteriormente, lasciare distribuito l'acceleratore della soluzione di manutenzione predittiva.

Se l'acceleratore di soluzione non è più necessario, eliminarlo dalla pagina [Soluzioni di cui è stato effettuato il provisioning](https://www.azureiotsolutions.com/Accelerators#dashboard) selezionandolo e facendo clic su **Elimina soluzione**:

![Eliminare la soluzione](media/quickstart-predictive-maintenance-deploy/deletesolution.png)

## <a name="next-steps"></a>Passaggi successivi

In questa guida introduttiva è stato distribuito l'acceleratore della soluzione di manutenzione predittiva ed è stata eseguita una simulazione.

Per altre informazioni sull'acceleratore di soluzione e i motori di aeromobili simulati, continuare con l'articolo seguente.

> [!div class="nextstepaction"]
> [Panoramica dell'acceleratore di soluzioni di manutenzione predittiva](iot-accelerators-predictive-walkthrough.md)
