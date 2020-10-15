---
title: Usare i flussi di lavoro per integrare l'applicazione Azure IoT Central con altri servizi cloud | Microsoft Docs
description: Questo articolo spiega in che modo i generatori di soluzioni possono eseguire la configurazione di regole e azioni che integrano l'applicazione IoT Central con altri servizi cloud. Per creare una regola avanzata, è possibile usare un connettore IoT Central in Power Automate o App per la logica di Azure.
author: dominicbetts
ms.author: dobett
ms.date: 05/12/2020
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 037598212773ca75abbdd086fe0577e0660f2218
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91398579"
---
# <a name="use-workflows-to-integrate-your-azure-iot-central-application-with-other-cloud-services"></a>Usare i flussi di lavoro per integrare l'applicazione Azure IoT Central con altri servizi cloud

*Le informazioni di questo articolo sono destinate ai generatori di soluzioni.*

È possibile creare regole in IoT Central che attivino azioni, come l'invio di un'e-mail, in risposta a condizioni basate su telemetria, come il superamento di una soglia di temperatura da parte di un dispositivo.

Il connettore Azure IoT Central V3 per Power automatizzate e app per la logica di Azure consente di creare regole più avanzate per automatizzare le operazioni in IoT Central:

- Quando una regola viene attivata nell'app Azure IoT Central, può attivare un flusso di lavoro in Power Automate o App per la logica di Azure. Questi flussi di lavoro possono eseguire azioni in altri servizi cloud, ad esempio Microsoft 365 o un servizio di terze parti.
- Un evento in un altro servizio cloud, ad esempio Microsoft 365, può attivare un flusso di lavoro in Power automatici o app per la logica di Azure. Questi flussi di lavoro possono eseguire azioni o recuperare dati dall'applicazione IoT Central.

## <a name="prerequisites"></a>Prerequisiti

Per completare la procedura descritta in questa guida pratica, è necessaria una sottoscrizione di Azure attiva. Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

Per configurare la soluzione è necessaria un'applicazione IoT Central versione 3. Per informazioni su come controllare la versione dell'applicazione, vedere [informazioni sull'applicazione](./howto-get-app-info.md). Per informazioni su come creare un'applicazione IoT Central, vedere [creare un'applicazione Azure IOT Central](./quick-deploy-iot-central.md).

> [!NOTE]
> Se si usa un'applicazione IoT Central versione 2, vedere [creare flussi di lavoro con il connettore IOT Central in app per la logica di Azure nel](https://docs.microsoft.com/previous-versions/azure/iot-central/core/howto-build-azure-logic-apps) sito della documentazione sulle versioni precedenti e usare il connettore Azure IOT Central V2

## <a name="trigger-a-workflow-from-a-rule"></a>Attivare un flusso di lavoro da una regola

Prima di poter attivare un flusso di lavoro in Power Automate o App per la logica di Azure, è necessaria una regola nell'applicazione IoT Central. Per altre informazioni, vedere [Configurare regole e azioni in Azure IoT Central](./howto-configure-rules.md).

Per aggiungere il connettore **Azure IOT Central V3-Preview** come trigger in Power automatizzate:

1. In Power Automate selezionare **+ Crea**, quindi la scheda **Personalizzato**.
1. Cercare *IOT Central*e selezionare il connettore **Azure IOT Central V3-Preview** .
1. Nell'elenco dei trigger selezionare **When a rule is fired (preview)** .
1. Nel passaggio **When a rule is fired** selezionare l'applicazione IoT Central e la regola che si sta usando.

Per aggiungere il connettore **Azure IOT Central V3-Preview** come trigger in app per la logica di Azure:

1. In **Progettazione app per la logica** selezionare il modello **App per la logica vuota**.
1. Nella finestra di progettazione selezionare la scheda **Personalizzato**.
1. Cercare *IOT Central*e selezionare il connettore **Azure IOT Central V3-Preview** .
1. Nell'elenco dei trigger selezionare **When a rule is fired (preview)** .
1. Nel passaggio **When a rule is fired** selezionare l'applicazione IoT Central e la regola che si sta usando.

:::image type="content" source="./media/howto-configure-rules-advanced/triggers.png" alt-text="Trovare il connettore Azure IoT Central - Preview e scegliere il trigger":::

È ora possibile aggiungere altri passaggi al flusso di lavoro per creare lo scenario di integrazione.

## <a name="run-an-action"></a>Eseguire un'azione

È possibile eseguire azioni in un'applicazione IoT Central dai flussi di lavoro di Power Automate e App per la logica di Azure. Per prima cosa, creare il flusso di lavoro e usare un connettore per definire un trigger per avviare il flusso di lavoro. Usare quindi il connettore **Azure IOT Central V3-Preview** come azione.

Per aggiungere il connettore **Azure IOT Central V3-Preview** come azione in Power automatizzate:

1. In Power Automate, nel pannello **Scegliere un'azione**, selezionare la scheda **Personalizzato**.
1. Cercare *IOT Central* e selezionare il connettore **Azure IOT Central V3-Preview** .
1. Nell'elenco delle azioni selezionare l'azione IoT Central che si intende usare.
1. Nel passaggio relativo all'azione completare la configurazione per l'azione selezionata. Selezionare quindi **Salva**.

Per aggiungere il connettore **Azure IOT Central V3-Preview** come azione in app per la logica di Azure:

1. In **Progettazione app per la logica**, nel pannello **Scegliere un'azione**, selezionare la scheda **Personalizzato**.
1. Cercare *IOT Central*e selezionare il connettore **Azure IOT Central V3-Preview** .
1. Nell'elenco delle azioni selezionare l'azione IoT Central che si intende usare.
1. Nel passaggio relativo all'azione completare la configurazione per l'azione selezionata. Selezionare quindi **Salva**.

:::image type="content" source="./media/howto-configure-rules-advanced/actions.png" alt-text="Trovare il connettore Azure IoT Central - Preview e scegliere il trigger":::

## <a name="list-of-actions"></a>Elenco di azioni

Nell'elenco seguente vengono illustrate tutte le azioni IoT Central disponibili nel connettore **Azure IOT Central V3-Preview** e le relative opzioni di configurazione. Molti campi possono avere contenuto generato dinamicamente. Un passaggio precedente, ad esempio, potrebbe determinare l'ID del dispositivo su cui agisce il passaggio corrente.

### <a name="create-or-update-a-device"></a>Create or update a device

Usare questa azione per creare o aggiornare un dispositivo nell'applicazione IoT Central.

| Campo | Descrizione |
| ----- | ----------- |
| Applicazione | Effettuare una scelta dall'elenco di applicazioni IoT Central. |
| Dispositivo | ID univoco del dispositivo da creare o aggiornare. |
| Approved | Scegliere se il dispositivo è stato approvato per la connessione a IoT Central. |
| Device Description | Descrizione dettagliata del dispositivo. |
| Device Name | Nome visualizzato del dispositivo. |
| Modello di dispositivo | Effettuare una scelta dall'elenco di modelli di dispositivo nell'applicazione IoT Central. |
| Simulated | Scegliere se il dispositivo è simulato. |

### <a name="delete-a-device"></a>Eliminare un dispositivo

Usare questa azione per eliminare un dispositivo nell'applicazione IoT Central.

| Campo | Descrizione |
| ----- | ----------- |
| Applicazione | Effettuare una scelta dall'elenco di applicazioni IoT Central. |
| Dispositivo | ID univoco del dispositivo da eliminare. |

### <a name="execute-a-device-command"></a>Execute a device command

Usare questa azione per eseguire un comando definito in una delle interfacce del dispositivo.

| Campo | Descrizione |
| ----- | ----------- |
| Applicazione | Effettuare una scelta dall'elenco di applicazioni IoT Central. |
| Dispositivo | ID univoco del dispositivo da eliminare. |
| Device Component | Interfaccia nel modello di dispositivo che contiene il comando. |
| Comando del dispositivo | Scegliere uno dei comandi nell'interfaccia selezionata. |
| Modello di dispositivo | Effettuare una scelta dall'elenco di modelli di dispositivo nell'applicazione IoT Central. |
| Device Command Request Payload | Se il comando richiede un payload della richiesta, aggiungerlo qui.  |

> [!NOTE]
> Non è possibile scegliere un componente del dispositivo fino a quando non è stato scelto un modello di dispositivo.

### <a name="get-a-device-by-id"></a>Get a device by ID

Usare questa azione per recuperare i dettagli del dispositivo.

| Campo | Descrizione |
| ----- | ----------- |
| Applicazione | Effettuare una scelta dall'elenco di applicazioni IoT Central. |
| Dispositivo | ID univoco del dispositivo da eliminare. |

È possibile usare i dettagli restituiti nelle espressioni dinamiche in altre azioni. I dettagli del dispositivo restituiti includono: **Approved**, **body**, **Device Description**, **Device Name**, **Device Template**, **Provisioned** e **Simulated**.

### <a name="get-device-cloud-properties"></a>Get device cloud properties

Usare questa azione per recuperare i valori delle proprietà cloud per un dispositivo specifico.

| Campo | Descrizione |
| ----- | ----------- |
| Applicazione | Effettuare una scelta dall'elenco di applicazioni IoT Central. |
| Dispositivo | ID univoco del dispositivo da eliminare. |
| Modello di dispositivo | Effettuare una scelta dall'elenco di modelli di dispositivo nell'applicazione IoT Central. |

È possibile usare i valori delle proprietà cloud restituiti nelle espressioni dinamiche in altre azioni.

### <a name="get-device-properties"></a>Get device properties

Usare questa azione per recuperare i valori delle proprietà per un dispositivo specifico.

| Campo | Descrizione |
| ----- | ----------- |
| Applicazione | Effettuare una scelta dall'elenco di applicazioni IoT Central. |
| Dispositivo | ID univoco del dispositivo da eliminare. |
| Modello di dispositivo | Effettuare una scelta dall'elenco di modelli di dispositivo nell'applicazione IoT Central. |

È possibile usare i valori delle proprietà restituiti nelle espressioni dinamiche in altre azioni.

### <a name="get-device-telemetry-value"></a>Get device telemetry value

Usare questa azione per recuperare i valori di telemetria per un dispositivo specifico.

| Campo | Descrizione |
| ----- | ----------- |
| Applicazione | Effettuare una scelta dall'elenco di applicazioni IoT Central. |
| Dispositivo | ID univoco del dispositivo da eliminare. |
| Modello di dispositivo | Effettuare una scelta dall'elenco di modelli di dispositivo nell'applicazione IoT Central. |

È possibile usare i valori di telemetria restituiti nelle espressioni dinamiche in altre azioni.

### <a name="update-device-cloud-properties"></a>Update device cloud properties

Usare questa azione per aggiornare i valori delle proprietà cloud per un dispositivo specifico.

| Campo | Descrizione |
| ----- | ----------- |
| Applicazione | Effettuare una scelta dall'elenco di applicazioni IoT Central. |
| Dispositivo | ID univoco del dispositivo da eliminare. |
| Modello di dispositivo | Effettuare una scelta dall'elenco di modelli di dispositivo nell'applicazione IoT Central. |
| Proprietà cloud | Dopo aver scelto un modello di dispositivo, viene aggiunto un campo per ogni proprietà cloud definita nel modello. |

### <a name="update-device-properties"></a>Update device properties

Usare questa azione per aggiornare i valori delle proprietà scrivibili per un dispositivo specifico.

| Campo | Descrizione |
| ----- | ----------- |
| Applicazione | Effettuare una scelta dall'elenco di applicazioni IoT Central. |
| Dispositivo | ID univoco del dispositivo da eliminare. |
| Modello di dispositivo | Effettuare una scelta dall'elenco di modelli di dispositivo nell'applicazione IoT Central. |
| Writeable properties | Dopo aver scelto un modello di dispositivo, viene aggiunto un campo per ogni proprietà scrivibile definita nel modello. |

## <a name="next-steps"></a>Passaggi successivi

Dopo aver appreso come creare una regola avanzata nell'applicazione Azure IoT Central, è possibile apprendere come [analizzare i dati dei dispositivi nell'applicazione Azure IoT Central](howto-create-analytics.md)
