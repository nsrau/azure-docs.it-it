---
title: Esportare un'applicazione IoT Central di Azure | Microsoft Docs
description: In qualità di responsabile della soluzione, voglio esportare un modello di applicazione per poterlo riutilizzare.
author: dominicbetts
ms.author: dobett
ms.date: 10/17/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 0f756b3f97accdcf7cae132b593f00ad9cc599ce
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2019
ms.locfileid: "72949716"
---
# <a name="export-your-application-preview-features"></a>Esporta l'applicazione (funzionalità di anteprima)

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

Questo articolo descrive come Gestione soluzioni per esportare un'applicazione IoT Central per poterla riutilizzare.

Sono disponibili due opzioni:

- È possibile creare una copia dell'applicazione se è necessario solo creare una copia duplicata dell'applicazione.
- È possibile creare un modello di applicazione dall'applicazione se si prevede di creare più copie.

## <a name="copy-your-application"></a>Copiare l'applicazione

È possibile creare una copia di una qualsiasi applicazione, senza tuttavia includere eventuali istanze di dispositivi, la cronologia dei dati dei dispositivi e i dati degli utenti. La copia è un'applicazione con pagamento in base al consumo a cui verranno addebitati i costi. Non è possibile creare un'applicazione di valutazione copiando un'applicazione.

Selezionare **copia**. Nella finestra di dialogo immettere i dettagli per la nuova applicazione con pagamento in base al consumo. Quindi selezionare **copia** per confermare che si vuole continuare. Per ulteriori informazioni sui campi nel modulo, vedere la Guida introduttiva alla [creazione di un'applicazione](quick-deploy-iot-central-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) .

![Pagina delle impostazioni dell'applicazione](media/howto-use-app-templates-pnp/appcopy2.png)

Una volta completata l'operazione di copia dell'app, è possibile passare alla nuova applicazione usando il collegamento.

![Pagina delle impostazioni dell'applicazione](media/howto-use-app-templates-pnp/appcopy3a.png)

La copia di un'applicazione copia anche la definizione di regole e l'azione di posta elettronica. Alcune azioni, ad esempio Microsoft Flow e app per la logica, sono associate a regole specifiche tramite l'ID regola. Quando una regola viene copiata in un'altra applicazione, ottiene il proprio ID regola. In questo caso, gli utenti dovranno creare una nuova azione e quindi associarvi la nuova regola. In generale, è consigliabile controllare le regole e le azioni per assicurarsi che siano aggiornate nella nuova app...

> [!WARNING]
> Se un dashboard include riquadri che visualizzano informazioni su dispositivi specifici, i riquadri mostrano che **la risorsa richiesta non è stata trovata** nella nuova applicazione. È necessario riconfigurare questi riquadri per visualizzare le informazioni sui dispositivi nella nuova applicazione.

## <a name="create-an-application-template"></a>Creare un modello di applicazione

Quando si crea un'applicazione IoT Central di Azure, è possibile scegliere tra modelli di esempio predefiniti. È anche possibile creare modelli di applicazione personalizzati da applicazioni IoT Central esistenti. È quindi possibile usare i modelli di applicazione personalizzati quando si creano nuove applicazioni.

Quando si crea un modello di applicazione, include gli elementi seguenti dell'applicazione esistente:

- Il dashboard dell'applicazione predefinito, inclusi il layout del dashboard e tutti i riquadri definiti.
- Modelli di dispositivo, incluse misure, impostazioni, proprietà, comandi e dashboard.
- Regole. Sono incluse tutte le definizioni delle regole. Tuttavia, le azioni, ad eccezione delle azioni di posta elettronica, non sono incluse.
- Set di dispositivi, incluse le condizioni e i dashboard.

> [!WARNING]
> Se un dashboard include riquadri che visualizzano informazioni su dispositivi specifici, i riquadri mostrano che **la risorsa richiesta non è stata trovata** nella nuova applicazione. È necessario riconfigurare questi riquadri per visualizzare le informazioni sui dispositivi nella nuova applicazione.

Quando si crea un modello di applicazione, non include gli elementi seguenti:

- Dispositivi
- Utenti
- Definizioni processi
- Definizioni di esportazione dei dati continua

Aggiungere manualmente questi elementi a qualsiasi applicazione creata da un modello di applicazione.

Per creare un modello di applicazione da un'applicazione IoT Central esistente:

1. Passare alla sezione **Amministrazione** nell'applicazione.
1. Selezionare **Esporta modello di applicazione**.
1. Nella pagina **esportazione modello di applicazione** immettere un nome e una descrizione per il modello.
1. Selezionare il pulsante **Esporta** per creare il modello di applicazione. È ora possibile copiare il **collegamento condivisibile** che consente a un utente di creare una nuova applicazione dal modello:

![Creare un modello di applicazione](media/howto-use-app-templates-pnp/create-template.png)

### <a name="use-an-application-template"></a>Usare un modello di applicazione

Per usare un modello di applicazione per creare una nuova applicazione IoT Central, è necessario un **collegamento condiviso**creato in precedenza. Incollare il **collegamento condivisibile** nella barra degli indirizzi del browser. Viene visualizzata la pagina **Crea un'applicazione** con il modello di applicazione personalizzato selezionato:

![Creare un'applicazione da un modello](media/howto-use-app-templates-pnp/create-app.png)

Selezionare il piano di pagamento e compilare gli altri campi nel modulo. Selezionare quindi **Crea** per creare una nuova applicazione IoT Central dal modello di applicazione.

### <a name="manage-application-templates"></a>Gestire i modelli di applicazione

Nella pagina **esportazione modello di applicazione** è possibile eliminare o aggiornare il modello di applicazione.

Se si elimina un modello di applicazione, non è più possibile utilizzare il collegamento condivisibile generato in precedenza per creare nuove applicazioni.

Per aggiornare il modello di applicazione, modificare il nome o la descrizione del modello nella pagina **esportazione modello di applicazione** . Quindi selezionare di nuovo il pulsante **Esporta** . Questa azione genera un nuovo **collegamento condivisibile** e invalida eventuali URL di **collegamento condivisibili** precedenti.

## <a name="next-steps"></a>Passaggi successivi

Ora che si è appreso come usare i modelli di applicazione, il passaggio successivo suggerito consiste nell'apprendere come [gestire IOT Central dalla portale di Azure](howto-manage-iot-central-from-portal.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)
