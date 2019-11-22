---
title: Creare un'app di monitoraggio pazienti continuo con Azure IoT Central | Microsoft Docs
description: Informazioni su come creare un'applicazione di monitoraggio pazienti continuo usando i modelli di applicazione di Azure IoT Central.
author: philmea
ms.author: philmea
ms.date: 09/24/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: eliotgra
ms.openlocfilehash: 97a215d8f111753c8fcc857fe4c48956c1236b3b
ms.sourcegitcommit: d47a30e54c5c9e65255f7ef3f7194a07931c27df
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73027451"
---
# <a name="tutorial-deploy-and-walkthrough-a-continuous-patient-monitoring-app-template"></a>Esercitazione: Distribuire ed esplorare un modello di app di monitoraggio pazienti continuo

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

Questa esercitazione illustra ai creatori di soluzioni come iniziare a distribuire un modello di applicazione di monitoraggio pazienti continuo IoT Central. Si apprenderà pertanto come distribuire il modello e quali sono gli elementi inclusi per impostazione predefinita. Verranno inoltre fornite informazioni sulle possibili operazioni successive.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Creare un modello di applicazione
> * Esplorare il modello di applicazione

## <a name="create-an-application-template"></a>Creare un modello di applicazione

Passare al [sito Web di gestione applicazioni Azure IoT Central](https://apps.azureiotcentral.com/). Selezionare **Compila** dalla barra di spostamento a sinistra e quindi fare clic sulla scheda **Servizi sanitari**. 

>[!div class="mx-imgBorder"] 
>![Gestione app - Servizi sanitari](media/app-manager-health.png)

Fare clic sul pulsante **Crea app** per iniziare a creare l'applicazione e quindi accedere con un account Microsoft personale oppure aziendale o dell'istituto di istruzione. Si aprirà la pagina **Nuova applicazione**.

>[!div class="mx-imgBorder"] 
>![Creare l'applicazione - Servizi sanitari](media/app-manager-health-create.png)

Per creare l'applicazione, seguire questa procedura:

1. Azure IoT Central suggerisce automaticamente un nome per l'applicazione in base al modello selezionato. È possibile accettare tale nome oppure immettere il nome descrittivo desiderato per l'applicazione, ad esempio **Continuous patient monitoring**. Azure IoT Central genera automaticamente anche un prefisso URL univoco in base al nome dell'applicazione. È possibile modificare questo prefisso URL in modo da renderlo più facile da ricordare.

2. È possibile scegliere se si vuole creare un'applicazione di tipo **Versione di valutazione** o un'applicazione **Con pagamento in base al consumo**. Le applicazioni di tipo **Versione di valutazione** sono gratuite per sette giorni prima della scadenza e consentono l'uso di un massimo di cinque dispositivi gratuiti. Possono essere convertite in applicazioni con pagamento in base al consumo in qualsiasi momento prima della scadenza. Se si crea un'applicazione di tipo Versione di valutazione, è necessario immettere le proprie informazioni di contatto e scegliere se ricevere informazioni e suggerimenti da Microsoft. Le applicazioni **Con pagamento in base al consumo** supportano fino a due dispositivi gratuiti e richiedono l'immissione delle informazioni relative alla sottoscrizione di Azure.

3. Fare clic su **Crea** nella parte inferiore della pagina per distribuire l'applicazione.

## <a name="walk-through-the-application-template"></a>Esplorare il modello di applicazione

### <a name="dashboards"></a>Dashboard

Dopo aver distribuito il modello di app, prima di tutto si passerà a **Lamna in-patient monitoring dashboard** (Dashboard di monitoraggio dei pazienti degenti Lamna). Lamna Healthcare è un sistema ospedaliero fittizio che include due strutture: Woodgrove Hospital e Burkville Hospital. In questo dashboard dell'operatore relativo a Woodgrove Hospital verranno visualizzate le informazioni e i dati di telemetria sui dispositivi in tale modello insieme a un set di comandi, processi e azioni che è possibile eseguire. Dal dashboard è possibile:

* Visualizzare i dati di telemetria e le proprietà del dispositivo, ad esempio il **livello della batteria** o lo stato di **connettività**.

* Visualizzare la **planimetria piano** e la posizione del dispositivo patch intelligente per i segni vitali.

* **Effettuare nuovamente il provisioning** della patch intelligente per i segni vitali per un nuovo paziente.

* Vedere un esempio di **dashboard del provider** che un team di assistenza ospedaliera potrebbe visualizzare per monitorare i pazienti.

* Modificare lo **stato paziente** del dispositivo per indicare se quest'ultimo è in uso per uno scenario con pazienti degenti o in modalità remota.

>[!div class="mx-imgBorder"] 
>![Pazienti degenti Lamna](media/lamna-in-patient.png)

È anche possibile fare clic su **Go to remote patient dashboard** (Vai al dashboard dei pazienti remoti) per visualizzare il secondo dashboard dell'operatore usato per Burkville Hospital. Tale dashboard contiene un set analogo di azioni, dati di telemetria e informazioni. È inoltre possibile visualizzare più dispositivi in uso e **aggiornare il firmware** di ognuno.

>[!div class="mx-imgBorder"] 
>![Pazienti remoti Lamna](media/lamna-remote.png)

In entrambi i dashboard è sempre possibile tornare a questa documentazione.

### <a name="device-templates"></a>Modelli di dispositivo

Se si fa clic sulla scheda **Modelli di dispositivo**, si noterà che fanno parte del modello due tipi diversi di dispositivi:

* **Smart Vitals Patch** (Patch intelligente per i segni vitali): questo dispositivo rappresenta una patch che misura diversi segni vitali utilizzabili per monitorare i pazienti all'interno e all'esterno dell'ospedale. Se si fa clic sul modello, si vedrà che la patch, oltre a inviare dati del dispositivo come il livello della batteria e la temperatura, invia dati relativi allo stato di salute del paziente, ad esempio la frequenza respiratoria e la pressione sanguigna.

* **Smart Knee Brace** (Tutore intelligente per il ginocchio): questo dispositivo rappresenta un tutore per il ginocchio che i pazienti potrebbero usare durante il recupero da un intervento di protesi al ginocchio. Se si fa clic su questo modello, si visualizzeranno ad esempio informazioni relative al raggio di azione del movimento articolare e all'accelerazione, oltre ai dati del dispositivo.

>[!div class="mx-imgBorder"] 
>![Modello di dispositivo patch intelligente per i segni vitali](media/smart-vitals-device-template.png)

Se si fa clic sulla scheda **Gruppi di dispositivi**, si noterà inoltre che per questi modelli di dispositivo vengono creati automaticamente gruppi di dispositivi.

### <a name="rules"></a>Regole

Se si passa alla scheda Regole, si noterà che nel modello di applicazione sono presenti tre regole:

* **Brace temperature high** (Temperatura tutore elevata): questa regola viene attivata quando la temperatura del tutore intelligente per il ginocchio supera i 35 &deg;C in un arco temporale di 5 minuti. È possibile usare questa regola per avvisare il paziente e il team di assistenza e raffreddare il dispositivo in modalità remota.

* **Fall detected** (Rilevata caduta): questa regola viene attivata se viene rilevata una caduta del paziente. È possibile usare questa regola per configurare un'azione allo scopo di dislocare un team operativo per assistere il paziente che è caduto.

* **Patch battery low** (Livello batteria basso): questa regola viene attivata quando il livello della batteria del dispositivo scende al di sotto del 10%. È possibile usare questa regola per attivare una notifica che segnali al paziente di caricare il dispositivo.

>[!div class="mx-imgBorder"] 
>![Regola per temperatura tutore elevata](media/brace-temp-rule.png)

### <a name="devices"></a>Dispositivi

Fare clic sulla scheda **Dispositivi** e quindi selezionare un'istanza di **Smart Knee Brace** (Tutore intelligente per il ginocchio). Si noterà che sono disponibili tre viste che consentono di esplorare le informazioni relative allo specifico dispositivo selezionato. Tali viste vengono create e pubblicate durante la creazione del modello per il dispositivo. Ciò significa che saranno coerenti per tutti i dispositivi collegati o simulati.

La vista **Dashboard** offre una panoramica dei dati di telemetria e delle proprietà, inviati dal dispositivo, che sono destinati all'operatore.

La scheda **Proprietà** consentirà di modificare le proprietà cloud e le proprietà di lettura/scrittura del dispositivo.

La scheda **Comandi** consentirà di eseguire comandi modellati come parte del modello di dispositivo.

>[!div class="mx-imgBorder"] 
>![Viste del tutore per il ginocchio](media/knee-brace-dashboard.png)

## <a name="clean-up-resources"></a>Pulire le risorse

Se non si intende continuare a usare questa applicazione, eliminarla scegliendo **Amministrazione > Impostazioni applicazione** e facendo clic su **Elimina**.

>[!div class="mx-imgBorder"] 
>![Eliminare l'app](media/admin-delete.png)

## <a name="next-steps"></a>Passaggi successivi

Passare all'articolo successivo per imparare a creare un dashboard del provider che si connetta all'applicazione IoT Central.

> [!div class="nextstepaction"]
> [Creare un dashboard del provider](howto-health-data-triage.md)