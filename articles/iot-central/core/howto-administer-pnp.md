---
title: Modificare le impostazioni dell'applicazione IoT Central di Azure | Microsoft Docs
description: Come amministratore, come gestire l'applicazione IoT Central di Azure cambiando il nome dell'applicazione, l'URL, il caricamento dell'immagine e l'eliminazione di un'applicazione
author: viv-liu
ms.author: viviali
ms.date: 10/10/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 7396b7dc22f0be77b9fc62a4dea379cf03a6befd
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2019
ms.locfileid: "72951809"
---
# <a name="change-iot-central-application-settings"></a>Modificare le impostazioni dell'applicazione IoT Central

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

Questo articolo descrive come un amministratore può gestire l'applicazione modificando il nome e l'URL dell'applicazione, caricando l'immagine ed eliminando un'applicazione nell'applicazione IoT Central di Azure.

Per accedere alla sezione **Amministrazione** e usarla, è necessario disporre del ruolo **Amministratore** per l'applicazione Azure IoT Central. All'utente che crea un'applicazione Azure IoT Central viene automaticamente assegnato il ruolo **Amministratore** per l'applicazione.

## <a name="change-application-name-and-url"></a>Modificare il nome e l'URL dell'applicazione

Nella pagina **Impostazioni applicazione** è possibile modificare il nome e l'URL dell'applicazione e quindi selezionare **Salva**.

![Pagina delle impostazioni dell'applicazione](media/howto-administer-pnp/image0-a.png)

Se l'amministratore crea un tema personalizzato per l'applicazione, questa pagina include un'opzione che consente di nascondere il **nome dell'applicazione** nell'interfaccia utente. Questa opzione è utile se il logo dell'applicazione nel tema personalizzato include il nome dell'applicazione. Per altre informazioni, vedere [personalizzare l'interfaccia utente di Azure IOT Central](./howto-customize-ui-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json).

> [!Note]
> Se si modifica l'URL, l'URL precedente può essere usato da un altro cliente di Azure IoT Central. In tal caso non sarà più disponibile. L'URL precedente a questo punto non funziona più ed è necessario indicare agli utenti quello nuovo da usare.

## <a name="delete-an-application"></a>Eliminare un'applicazione

Usare il pulsante **Elimina** per eliminare definitivamente l'applicazione IoT Central. Questa azione Elimina definitivamente tutti i dati associati all'applicazione.

> [!Note]
> Per eliminare un'applicazione, è necessario disporre anche delle autorizzazioni per eliminare risorse nella sottoscrizione di Azure scelta al momento della creazione dell'applicazione. Per altre informazioni, vedere [Usare il controllo degli accessi in base al ruolo per gestire l'accesso alle risorse della sottoscrizione di Azure](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure).

## <a name="manage-programmatically"></a>Gestire a livello di codice

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

Ora che si è appreso come amministrare l'applicazione IoT Central di Azure, il passaggio successivo suggerito consiste nell'acquisire familiarità con la [gestione di utenti e ruoli](howto-manage-users-roles-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) in Azure IOT Central.
