---
title: Usare i modelli di applicazione in Azure IoT Central | Microsoft Docs
description: Come usare i set di dispositivi nell'applicazione Azure IoT Central in qualità di operatore.
author: dominicbetts
ms.author: dobett
ms.date: 05/30/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: a26f70c5a61f3855a3de991072a7e84103e87b69
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66499220"
---
# <a name="use-application-templates"></a>Usare i modelli di applicazione

Questo articolo descrive come, come un gestore della soluzione, creare e usare modelli di applicazione.

Quando si crea un'applicazione Azure IoT Central, è possibile scegliere di modelli di esempio incorporata. È anche possibile creare i propri modelli di applicazione da applicazioni IoT Central esistenti. È quindi possibile usare i propri modelli di applicazione quando si creano nuove applicazioni.

Quando si crea un modello di applicazione, include gli elementi seguenti dall'applicazione esistente:

- Il dashboard dell'applicazione di predefinito, tra cui il layout del dashboard e tutte le sezioni che sono state definite.
- Modelli di dispositivo, inclusi le misure, le impostazioni, proprietà, comandi e dashboard.
- Regole. Sono incluse tutte le definizioni delle regole. Tuttavia le azioni, ad eccezione di azioni di posta elettronica, non sono incluse.
- Set di dispositivi, incluse le relative condizioni e i dashboard.

> [!WARNING]
> Se un dashboard contiene riquadri che consentono di visualizzare informazioni sui dispositivi specifici, quindi mostrano i riquadri **la risorsa richiesta non è stata trovata** nella nuova applicazione. È necessario riconfigurare questi riquadri per visualizzare le informazioni sui dispositivi in una nuova applicazione.

Quando si crea un modello di applicazione, non include gli elementi seguenti:

- Dispositivi
- Utenti
- Definizioni dei processi
- Definizioni di esportazione continua dei dati

Aggiungere manualmente questi elementi per tutte le applicazioni create da un modello di applicazione.

## <a name="create-an-application-template"></a>Creare un modello di applicazione

Per creare un modello di applicazione da un'applicazione IoT Central esistente:

1. Andare alla **amministrazione** sezione nell'applicazione.
1. Selezionare **esportazione del modello applicazione**.
1. Nel **Esporta modello di applicazione** pagina, immettere un nome e una descrizione per il modello.
1. Selezionare il **esportare** pulsante per creare il modello di applicazione. È ora possibile copiare il **collegamento condivisibile** che consente a un utente per creare una nuova applicazione dal modello:

![Creare un modello di applicazione](media/howto-use-app-templates/create-template.png)

## <a name="use-an-application-template"></a>Usare un modello di applicazione

Per usare un modello di applicazione per creare una nuova applicazione IoT Central, è necessario un creato in precedenza **collegamento condivisibile**. Incollare il **collegamento condivisibile** nella barra degli indirizzi del browser. Il **creare un'applicazione** pagina vengono visualizzati con il modello di applicazione personalizzato selezionato:

![Creare un'applicazione da un modello](media/howto-use-app-templates/create-app.png)

Selezionare il piano di pagamento e compilare gli altri campi nel form. Quindi selezionare **Create** per creare una nuova applicazione IoT Central dal modello di applicazione.

## <a name="manage-application-templates"></a>Gestire i modelli di applicazione

Nel **Esporta modello di applicazione** pagina, è possibile eliminare o aggiornare il modello di applicazione.

Se si elimina un modello di applicazione, è non possibile utilizzare il collegamento condivisibile generato in precedenza non sono più di creare nuove applicazioni.

Per aggiornare il modello di applicazione, modificare il nome del modello o la descrizione nel **Esporta modello di applicazione** pagina. Quindi selezionare il **esportare** nuovamente clic sul pulsante. Questa azione genera una nuova **collegamento condivisibile** e invalida qualsiasi precedente **collegamento condivisibile** URL.

## <a name="next-steps"></a>Passaggi successivi

Ora che si è appreso come usare i modelli di applicazione, il passaggio successivo consigliato sia per informazioni su come [gestire IoT Central dal portale di Azure](howto-manage-iot-central-from-portal.md)
