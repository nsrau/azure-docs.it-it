---
title: Gestire l'applicazione IoT Central di Azure | Microsoft Docs
description: Come amministratore, come gestire l'applicazione IoT Central di Azure cambiando il nome dell'applicazione, l'URL, il caricamento dell'immagine, la copia e l'eliminazione di un'applicazione
author: viv-liu
ms.author: viviali
ms.date: 08/26/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 4c729d2502464df021df4d03e989d489b2d1ecea
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2019
ms.locfileid: "72954591"
---
# <a name="manage-your-iot-central-application"></a>Gestire l'applicazione IoT Central

[!INCLUDE [iot-central-original-pnp](../../../includes/iot-central-original-pnp-note.md)]

Questo articolo descrive come un amministratore può gestire l'applicazione modificando il nome e l'URL dell'applicazione, caricando l'immagine, è anche possibile apprendere come copiare ed eliminare un'applicazione nell'applicazione IoT Central di Azure.

Per accedere alla sezione **Amministrazione** e usarla, è necessario disporre del ruolo **Amministratore** per l'applicazione Azure IoT Central. All'utente che crea un'applicazione Azure IoT Central viene automaticamente assegnato il ruolo **Amministratore** per l'applicazione. 

## <a name="change-application-name-and-url"></a>Modificare il nome e l'URL dell'applicazione

Nella pagina **Impostazioni applicazione** è possibile modificare il nome e l'URL dell'applicazione e quindi selezionare **Salva**.

![Pagina delle impostazioni dell'applicazione](media/howto-administer/image0-a.png)

Se l'amministratore crea un tema personalizzato per l'applicazione, questa pagina include un'opzione che consente di nascondere il **nome dell'applicazione** nell'interfaccia utente. Questa operazione è utile se il logo dell'applicazione nel tema personalizzato include il nome dell'applicazione. Per altre informazioni, vedere [personalizzare l'interfaccia utente di Azure IOT Central](./howto-customize-ui.md).

> [!Note]
> Se si modifica l'URL, l'URL precedente può essere usato da un altro cliente di Azure IoT Central. In tal caso non sarà più disponibile. L'URL precedente a questo punto non funziona più ed è necessario indicare agli utenti quello nuovo da usare.

## <a name="prepare-and-upload-image"></a>Preparare e caricare immagini

Per modificare l'immagine dell'applicazione, vedere [Preparare e caricare immagini nell'applicazione Azure IoT Central](howto-prepare-images.md).

## <a name="copy-an-application"></a>Copiare un'applicazione

È possibile creare una copia di una qualsiasi applicazione, senza tuttavia includere eventuali istanze di dispositivi, la cronologia dei dati dei dispositivi e i dati degli utenti. La copia è un'applicazione con pagamento in base al consumo a cui verranno addebitati i costi. Non è possibile creare una versione di valutazione di un'applicazione in questo modo.

Selezionare **copia**. Nella finestra di dialogo immettere i dettagli per la nuova applicazione con pagamento in base al consumo. Quindi selezionare **copia** per confermare che si vuole continuare. Altre informazioni sui campi in questo modulo sono disponibili nella guida introduttiva [Creare un'applicazione](quick-deploy-iot-central.md).

![Pagina delle impostazioni dell'applicazione](media/howto-administer/appcopy2.png)

Una volta completata l'operazione di copia dell'app, è possibile passare alla nuova applicazione usando il collegamento.

![Pagina delle impostazioni dell'applicazione](media/howto-administer/appcopy3a.png)

La copia di un'applicazione copia anche la definizione di regole e l'azione di posta elettronica. Alcune azioni come Flow, app per la logica e così via sono associate a regole specifiche tramite l'ID regola. Quando una regola viene copiata in un'altra applicazione, ottiene il proprio ID regola. In questo caso, gli utenti dovranno creare una nuova azione e quindi associarvi la nuova regola. In generale, è consigliabile controllare le regole e le azioni per assicurarsi che siano aggiornate nella nuova app...

> [!WARNING]
> Se un dashboard include riquadri che visualizzano informazioni su dispositivi specifici, i riquadri mostrano che **la risorsa richiesta non è stata trovata** nella nuova applicazione. È necessario riconfigurare questi riquadri per visualizzare le informazioni sui dispositivi nella nuova applicazione.

## <a name="delete-an-application"></a>Eliminare un'applicazione

Usare il pulsante **Elimina** per eliminare definitivamente l'applicazione IoT Central. Questa azione Elimina definitivamente tutti i dati associati all'applicazione.

> [!Note]
> Per eliminare un'applicazione, è necessario disporre anche delle autorizzazioni per eliminare risorse nella sottoscrizione di Azure scelta al momento della creazione dell'applicazione. Per altre informazioni, vedere [Usare il controllo degli accessi in base al ruolo per gestire l'accesso alle risorse della sottoscrizione di Azure](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure).


## <a name="manage-programatically"></a>Gestisci a livello

I pacchetti SDK di Azure Resource Manager di IoT Central sono disponibili per Node, Python, C#, Ruby, Java e Go. È possibile utilizzare questi pacchetti per creare, elencare, aggiornare o eliminare IoT Central applicazioni. I pacchetti includono helper per gestire l'autenticazione e la gestione degli errori.

È possibile trovare esempi di come usare gli SDK di Azure Resource Manager in [https://github.com/emgarten/iotcentral-arm-sdk-examples](https://github.com/emgarten/iotcentral-arm-sdk-examples).

Per altre informazioni, vedere i repository e i pacchetti di GitHub seguenti:

| Linguaggio | Repository | Pacchetto |
| ---------| ---------- | ------- |
| Nodo | [https://github.com/Azure/azure-sdk-for-node](https://github.com/Azure/azure-sdk-for-node) | [https://www.npmjs.com/package/azure-arm-iotcentral](https://www.npmjs.com/package/azure-arm-iotcentral)
| Python |[https://github.com/Azure/azure-sdk-for-python](https://github.com/Azure/azure-sdk-for-python) | [https://pypi.org/project/azure-mgmt-iotcentral](https://pypi.org/project/azure-mgmt-iotcentral)
| C# | [https://github.com/Azure/azure-sdk-for-net](https://github.com/Azure/azure-sdk-for-net) | [https://www.nuget.org/packages/Microsoft.Azure.Management.IotCentral](https://www.nuget.org/packages/Microsoft.Azure.Management.IotCentral)
| Ruby | [https://github.com/Azure/azure-sdk-for-ruby](https://github.com/Azure/azure-sdk-for-ruby) | [https://rubygems.org/gems/azure_mgmt_iot_central](https://rubygems.org/gems/azure_mgmt_iot_central)
| Java | [https://github.com/Azure/azure-sdk-for-java](https://github.com/Azure/azure-sdk-for-java) | [https://search.maven.org/search?q=a:azure-mgmt-iotcentral](https://search.maven.org/search?q=a:azure-mgmt-iotcentral)
| Go | [https://github.com/Azure/azure-sdk-for-go](https://github.com/Azure/azure-sdk-for-go) | [https://github.com/Azure/azure-sdk-for-go](https://github.com/Azure/azure-sdk-for-go)

## <a name="next-steps"></a>Passaggi successivi
 
Ora che si è appreso come amministrare l'applicazione IoT Central di Azure, il passaggio successivo suggerito consiste nell'acquisire familiarità con la [gestione di utenti e ruoli](howto-manage-users-roles.md) in Azure IOT Central.