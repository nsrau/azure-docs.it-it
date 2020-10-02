---
title: "Esercitazione: Creare un'app per il monitoraggio continuo dei pazienti con Azure IoT Central | Microsoft Docs"
description: Questa esercitazione illustra come creare un'applicazione per il monitoraggio continuo dei pazienti usando i modelli di applicazione di Azure IoT Central.
author: philmea
ms.author: philmea
ms.date: 09/24/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: eliotgra
ms.openlocfilehash: 1967a2fb5adebe01ef4bff8d58f7832bffe95762
ms.sourcegitcommit: 03662d76a816e98cfc85462cbe9705f6890ed638
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/15/2020
ms.locfileid: "90531271"
---
# <a name="tutorial-deploy-and-walkthrough-a-continuous-patient-monitoring-app-template"></a>Esercitazione: Distribuire ed esplorare un modello di app di monitoraggio pazienti continuo

Questa esercitazione illustra ai creatori di soluzioni come iniziare a distribuire un modello di applicazione di monitoraggio pazienti continuo IoT Central. Si apprenderà come distribuire e usare il modello.

In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
> * Creare un modello di applicazione
> * Esplorare il modello di applicazione

## <a name="create-an-application-template"></a>Creare un modello di applicazione

Passare al [sito Web di gestione applicazioni Azure IoT Central](https://apps.azureiotcentral.com/). Selezionare **Compila** sulla barra di spostamento sinistra e quindi selezionare la scheda **Servizi sanitari**.

:::image type="content" source="media/app-manager-health.png" alt-text="Modello di app Servizi sanitari":::

Selezionare il pulsante **Crea app** per iniziare a creare l'applicazione e quindi accedere con un account Microsoft personale oppure aziendale o dell'istituto di istruzione. Verrà visualizzata la pagina **Nuova applicazione**.

![Creare l'applicazione - Servizi sanitari](media/app-manager-health-create.png)

![Creare l'applicazione Servizi sanitari - Info di fatturazione](media/app-manager-health-create-billinginfo.png)

Per creare l'applicazione, seguire questa procedura:

1. Azure IoT Central suggerisce automaticamente un nome per l'applicazione in base al modello selezionato. È possibile accettare tale nome oppure immettere il nome descrittivo desiderato per l'applicazione, ad esempio **Continuous patient monitoring**. Azure IoT Central genera automaticamente anche un prefisso URL univoco in base al nome dell'applicazione. È possibile modificare questo prefisso URL in modo da renderlo più facile da ricordare.

2. È possibile specificare se si vuole creare l'applicazione con il piano tariffario *gratuito* o con uno dei piani tariffari *standard*. Le applicazioni create con il piano tariffario gratuito sono gratuite per sette giorni prima della scadenza e consentono l'uso di un massimo di cinque dispositivi gratuiti. È possibile spostare un'applicazione dal piano gratuito a un piano tariffario standard in qualsiasi momento prima della scadenza. Se si sceglie il piano gratuito, è necessario immettere le informazioni di contatto e specificare se si vogliono ricevere informazioni e suggerimenti da Microsoft. Le applicazioni create con un piano standard supportano fino a due dispositivi gratuiti e richiedono l'immissione delle informazioni relative alla sottoscrizione di Azure ai fini della fatturazione.

3. Fare clic su **Crea** nella parte inferiore della pagina per distribuire l'applicazione.

## <a name="walk-through-the-application-template"></a>Esplorare il modello di applicazione

### <a name="dashboards"></a>Dashboard

Dopo aver distribuito il modello di app, prima di tutto si passerà a **Lamna in-patient monitoring dashboard** (Dashboard di monitoraggio dei pazienti degenti Lamna). Lamna Healthcare è un sistema ospedaliero fittizio che include due strutture: Woodgrove Hospital e Burkville Hospital. Nel dashboard operatore di Woodgrove Hospital è possibile:

* Visualizzare i dati di telemetria e le proprietà del dispositivo, ad esempio il **livello della batteria** o lo stato di **connettività**.

* Visualizzare la **planimetria piano** e la posizione del dispositivo patch intelligente per i segni vitali.

* **Effettuare di nuovo il provisioning** della patch intelligente per i segni vitali per un nuovo paziente.

* Vedere un esempio di **dashboard del provider** che un team di assistenza ospedaliera potrebbe visualizzare per monitorare i pazienti.

* Modificare lo **stato paziente** del dispositivo per indicare se quest'ultimo è in uso per uno scenario con pazienti degenti o in modalità remota.

:::image type="content" source="media/lamna-in-patient.png" alt-text="Modello di app Servizi sanitari":::

È anche possibile fare clic su **Go to remote patient dashboard** (Vai al dashboard dei pazienti remoti) per visualizzare il dashboard operatore di Burkville Hospital. Tale dashboard contiene un set analogo di azioni, dati di telemetria e informazioni. È anche possibile visualizzare più dispositivi in uso e scegliere di **aggiornare il firmware** in ognuno.

:::image type="content" source="media/lamna-remote.png" alt-text="Modello di app Servizi sanitari":::

### <a name="device-templates"></a>Modelli di dispositivo

Se si seleziona **Modelli di dispositivo**, vengono visualizzati i due tipi di dispositivo nel modello:

* **Smart Vitals Patch** (Patch intelligente per i segni vitali): questo dispositivo rappresenta una patch che misura tipi diversi di segni vitali. Viene usato per monitorare i pazienti degenti in ospedale e quelli esterni. Se si seleziona il modello, si vedrà che la patch invia sia i dati del dispositivo, come il livello della batteria e la temperatura, che quelli relativi allo stato di salute del paziente, ad esempio la frequenza respiratoria e la pressione sanguigna.

* **Smart Knee Brace** (Tutore intelligente per il ginocchio): questo dispositivo rappresenta un tutore per il ginocchio che i pazienti potrebbero usare per la riabilitazione dopo un intervento di protesi al ginocchio. Se si seleziona questo modello, si visualizzano informazioni relative al raggio di azione del movimento articolare e all'accelerazione, oltre ai dati del dispositivo.

:::image type="content" source="media/smart-vitals-device-template.png" alt-text="Modello di app Servizi sanitari":::

### <a name="device-groups"></a>Gruppi di dispositivi

Usare i gruppi di dispositivi per raggruppare a livello logico un set di dispositivi su cui eseguire query o operazioni in blocco.

Se si seleziona la scheda Gruppi di dispositivi, viene visualizzato un gruppo di dispositivi predefinito per ogni modello di dispositivo dell'applicazione. Vengono inoltre creati altri due gruppi di dispositivi di esempio, **Provision devices** (Dispositivi per il provisioning) e **Devices with outdated firmware** (Dispositivi con firmware obsoleto). Questi gruppi di dispositivi di esempio possono essere usati come input per eseguire alcuni [processi](#jobs) nell'applicazione.

### <a name="rules"></a>Regole

Se si seleziona **Regole**, nel modello vengono visualizzate le tre regole seguenti:

* **Brace temperature high** (Temperatura tutore elevata): questa regola viene attivata quando la temperatura del tutore intelligente per il ginocchio supera i 35 &deg;C in un arco temporale di 5 minuti. Usare questa regola per avvisare il paziente e il team di assistenza e raffreddare il dispositivo in remoto.

* **Fall detected** (Rilevata caduta): questa regola viene attivata se viene rilevata una caduta del paziente. Usare questa regola per configurare un'azione allo scopo di inviare un team operativo per assistere il paziente che è caduto.

* **Patch battery low** (Livello batteria basso): questa regola viene attivata quando il livello della batteria del dispositivo scende al di sotto del 10%. Usare questa regola per attivare una notifica che segnali al paziente di caricare il dispositivo.

:::image type="content" source="media/brace-temp-rule.png" alt-text="Modello di app Servizi sanitari":::

### <a name="jobs"></a>Processi

I processi consentono di eseguire operazioni in blocco su un set di dispositivi, usando i [gruppi di dispositivi](#device-groups) come input. Il modello di applicazione include due processi di esempio che un operatore può eseguire:

* **Aggiornare il firmware del tutore per il ginocchio**: questo processo trova i dispositivi nel gruppo di dispositivi **Devices with outdated firmware** (Dispositivi con firmware obsoleto) ed esegue un comando per aggiornarli alla versione più recente del firmware. Questo processo di esempio presuppone che i dispositivi siano in grado di gestire un comando di **aggiornamento** e quindi di recuperare i file del firmware dal cloud.  

* **Eseguire nuovamente il provisioning dei dispositivi**: un set di dispositivi è stato di recente restituito all'ospedale. Questo processo individua i dispositivi nel gruppo **Provision devices** (Dispositivi per il provisioning) ed esegue un comando per effettuarne di nuovo il provisioning per la serie successiva di pazienti.

### <a name="devices"></a>Dispositivi

Selezionare la scheda **Dispositivi** e quindi selezionare un'istanza di **Smart Knee Brace** (Tutore intelligente per il ginocchio). Sono disponibili tre visualizzazioni per esplorare le informazioni relative allo specifico dispositivo selezionato. Tali visualizzazioni vengono create e pubblicate durante la creazione del modello per il dispositivo. Pertanto, saranno coerenti per tutti i dispositivi connessi o simulati.

La visualizzazione **Dashboard** offre una panoramica dei dati di telemetria e delle proprietà inviati dal dispositivo e destinati all'operatore.

La scheda **Proprietà** consente di modificare le proprietà cloud e le proprietà di lettura/scrittura del dispositivo.

La scheda **Comandi** consente di eseguire comandi nel dispositivo.

:::image type="content" source="media/knee-brace-dashboard.png" alt-text="Modello di app Servizi sanitari":::

### <a name="data-export"></a>Esportazione dati

L'esportazione dei dati consente di esportare continuamente i dati del dispositivo in altri servizi di Azure tra cui l'[API di Azure per FHIR](concept-continuous-patient-monitoring-architecture.md#export-to-azure-api-for-fhir).

## <a name="clean-up-resources"></a>Pulire le risorse

Se non si intende continuare a usare questa applicazione, eliminarla scegliendo **Amministrazione > Impostazioni applicazione** e facendo clic su **Elimina**.

:::image type="content" source="media/admin-delete.png" alt-text="Modello di app Servizi sanitari":::

## <a name="next-steps"></a>Passaggi successivi

Passare all'articolo successivo per imparare a creare un dashboard del provider che si connetta all'applicazione IoT Central.

> [!div class="nextstepaction"]
> [Creare un dashboard del provider](howto-health-data-triage.md)