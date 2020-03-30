---
title: Esportare un'applicazione di Azure IoT Central Documenti Microsoft
description: In qualità di gestore di soluzioni, si desidera esportare un modello di applicazione per poterlo riutilizzare.
author: dominicbetts
ms.author: dobett
ms.date: 12/09/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: f50c7e8dcb33fd2ed95829286aaf815926d9fb3f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80157622"
---
# <a name="export-your-application"></a>Esportare l'applicazione



In questo articolo viene descritto come, come gestore di soluzioni, esportare un'applicazione IoT Central per poterla riutilizzare.

Sono disponibili due opzioni:

- È possibile creare una copia dell'applicazione se è sufficiente creare una copia duplicata dell'applicazione.
- È possibile creare un modello di applicazione dall'applicazione se si prevede di creare più copie.

## <a name="copy-your-application"></a>Copiare l'applicazione

È possibile creare una copia di una qualsiasi applicazione, senza tuttavia includere eventuali istanze di dispositivi, la cronologia dei dati dei dispositivi e i dati degli utenti. La copia utilizza un piano tariffario standard che ti verrà fatturato. Non è possibile creare un'applicazione che utilizza il piano tariffario gratuito copiando un'applicazione.

Selezionare **Copia**. Nella finestra di dialogo, immettere i dettagli per la nuova applicazione. Quindi selezionare **Copia** per confermare che si desidera continuare. Per altre informazioni sui campi del modulo, vedere la guida introduttiva [Creare un'applicazione.](quick-deploy-iot-central.md)

![Pagina delle impostazioni dell'applicazione](media/howto-use-app-templates/appcopy2.png)

Dopo che l'operazione di copia dell'app ha esito positivo, è possibile passare alla nuova applicazione usando il collegamento.

![Pagina delle impostazioni dell'applicazione](media/howto-use-app-templates/appcopy3a.png)

La copia di un'applicazione copia anche la definizione delle regole e l'azione di posta elettronica. Alcune azioni, ad esempio Flow e App per la logica, sono legate a regole specifiche tramite l'ID regola. Quando una regola viene copiata in un'applicazione diversa, ottiene il proprio ID regola. In questo caso, gli utenti dovranno creare una nuova azione e quindi associarvi la nuova regola. In generale, è consigliabile controllare le regole e le azioni per assicurarsi che siano aggiornate nella nuova app.

> [!WARNING]
> Se un dashboard include riquadri che visualizzano informazioni su dispositivi specifici, i riquadri mostrano **Che la risorsa richiesta non è stata trovata** nella nuova applicazione. È necessario riconfigurare questi riquadri per visualizzare informazioni sui dispositivi nella nuova applicazione.

## <a name="create-an-application-template"></a>Creare un modello di applicazione

Quando si crea un'applicazione Azure IoT Central, è possibile scegliere tra i modelli di esempio predefiniti. È inoltre possibile creare modelli di applicazione personalizzati da applicazioni IoT Central esistenti. È quindi possibile utilizzare i propri modelli di applicazione quando si creano nuove applicazioni.

Quando si crea un modello di applicazione, include gli elementi seguenti dell'applicazione esistente:When you create an application template, it includes the following items from your existing application:

- Dashboard dell'applicazione predefinito, incluso il layout del dashboard e tutti i riquadri definiti.
- Modelli di dispositivo, inclusi misure, impostazioni, proprietà, comandi e dashboard.
- Regole. Vengono incluse tutte le definizioni delle regole. Tuttavia le azioni, ad eccezione delle azioni di posta elettronica, non sono incluse.
- Set di dispositivi, incluse le condizioni e i dashboard.

> [!WARNING]
> Se un dashboard include riquadri che visualizzano informazioni su dispositivi specifici, i riquadri mostrano **Che la risorsa richiesta non è stata trovata** nella nuova applicazione. È necessario riconfigurare questi riquadri per visualizzare informazioni sui dispositivi nella nuova applicazione.

Quando si crea un modello di applicazione, non sono inclusi gli elementi seguenti:When you create an application template, it doesn't include the following items:

- Dispositivi
- Utenti
- Definizioni dei processi
- Definizioni di esportazione dati continue

Aggiungere manualmente questi elementi a tutte le applicazioni create da un modello di applicazione.

Per creare un modello di applicazione da un'applicazione IoT Central esistente:

1. Passare alla sezione **Amministrazione** dell'applicazione.
1. Selezionare **Esportazione modello di applicazione**.
1. Nella pagina **Esportazione modello applicazione** immettere un nome e una descrizione per il modello.
1. Selezionare il pulsante **Esporta** per creare il modello di applicazione. È ora possibile copiare il **collegamento condivisibile** che consente a un utente di creare una nuova applicazione dal modello:You can now copy the Shareable Link that enables someone to create a new application from the template:

![Creare un modello di applicazione](media/howto-use-app-templates/create-template.png)

### <a name="use-an-application-template"></a>Usare un modello di applicazioneUse an application template

Per utilizzare un modello di applicazione per creare una nuova applicazione IoT Central, è necessario un **collegamento convisibile**creato in precedenza . Incollare il **link condivisibile** nella barra degli indirizzi del browser. Viene visualizzata la pagina **Crea un'applicazione** con il modello di applicazione personalizzato selezionato:The Create an application page displays with your custom application template selected:

![Creare un'applicazione da un modelloCreate an application from a template](media/howto-use-app-templates/create-app.png)

Seleziona il tuo piano tariffario e compila gli altri campi del modulo. Selezionare **quindi Crea** per creare una nuova applicazione IoT Central dal modello di applicazione.

### <a name="manage-application-templates"></a>Gestire i modelli di applicazione

Nella pagina **Esportazione modello applicazione** è possibile eliminare o aggiornare il modello di applicazione.

Se si elimina un modello di applicazione, non è più possibile utilizzare il collegamento condivisibile generato in precedenza per creare nuove applicazioni.

Per aggiornare il modello di applicazione, modificare il nome o la descrizione del modello nella pagina **Esportazione modello di applicazione.** Quindi selezionare nuovamente il pulsante **Esporta.** Questa azione genera un nuovo **collegamento condivisibile** e invalida qualsiasi URL **di collegamento condivisibile** precedente.

## <a name="next-steps"></a>Passaggi successivi

Dopo aver appreso come usare i modelli di applicazione, il passaggio successivo consigliato consiste nell'imparare a [gestire IoT Central dal portale](howto-manage-iot-central-from-portal.md) di Azure
