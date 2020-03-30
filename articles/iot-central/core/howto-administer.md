---
title: Modificare le impostazioni dell'applicazione di Azure IoT Central Documenti Microsoft
description: Come amministratore, come gestire l'applicazione Azure IoT Central modificando il nome dell'applicazione, l'URL, l'immagine di caricamento ed eliminare un'applicazione
author: viv-liu
ms.author: viviali
ms.date: 11/27/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 7b77ea9e0e1f322cb5ef0bc63885c3ccce1b76f6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80158683"
---
# <a name="change-iot-central-application-settings"></a>Modificare le impostazioni dell'applicazione IoT Central



Questo articolo descrive come, come amministratore, è possibile gestire l'applicazione modificando il nome e l'URL dell'applicazione, caricando l'immagine ed eliminando un'applicazione nell'applicazione Azure IoT Central.This article describes how, as an administrator, you can manage application by changing application name and URL, uploading image, and delete an application in your Azure IoT Central application.

Per accedere alla sezione **Amministrazione** e usarla, è necessario disporre del ruolo **Amministratore** per l'applicazione Azure IoT Central. All'utente che crea un'applicazione Azure IoT Central viene automaticamente assegnato il ruolo **Amministratore** per l'applicazione.

## <a name="change-application-name-and-url"></a>Modificare il nome e l'URL dell'applicazione

Nella pagina **Impostazioni applicazione** è possibile modificare il nome e l'URL dell'applicazione e quindi selezionare **Salva**.

![Pagina delle impostazioni dell'applicazione](media/howto-administer/image0-a.png)

Se l'amministratore crea un tema personalizzato per l'applicazione, questa pagina include un'opzione per nascondere il **nome dell'applicazione** nell'interfaccia utente. Questa opzione è utile se il logo dell'applicazione nel tema personalizzato include il nome dell'applicazione. Per altre informazioni, vedere [Personalizzare l'interfaccia utente di Azure IoT Central.For more information, see Customize the Azure IoT Central UI](./howto-customize-ui.md).

> [!Note]
> Se si modifica l'URL, l'URL precedente può essere usato da un altro cliente di Azure IoT Central. In tal caso non sarà più disponibile. L'URL precedente a questo punto non funziona più ed è necessario indicare agli utenti quello nuovo da usare.

## <a name="delete-an-application"></a>Eliminare un'applicazione

Usare il pulsante **Elimina** per eliminare definitivamente l'applicazione IoT Central. Questa azione elimina definitivamente tutti i dati associati all'applicazione.

> [!Note]
> Per eliminare un'applicazione, è necessario disporre anche delle autorizzazioni per eliminare risorse nella sottoscrizione di Azure scelta al momento della creazione dell'applicazione. Per altre informazioni, vedere [Usare il controllo degli accessi in base al ruolo per gestire l'accesso alle risorse della sottoscrizione di Azure](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure).

## <a name="manage-programmatically"></a>Gestire a livello di codice

I pacchetti SDK di Azure Resource Manager di IoT Central sono disponibili per Node, Python, C#, Ruby, Java e Go. È possibile utilizzare questi pacchetti per creare, elencare, aggiornare o eliminare applicazioni IoT Central.You can use these packages to create, list, update, or delete IoT Central applications. I pacchetti includono helper per gestire l'autenticazione e la gestione degli errori.

È possibile trovare esempi di come usare gli [https://github.com/emgarten/iotcentral-arm-sdk-examples](https://github.com/emgarten/iotcentral-arm-sdk-examples)SDK di Azure Resource Manager in .

Per altre informazioni, vedere i repository e i pacchetti Di GitHub seguenti:To learn more, see the following GitHub repositories and packages:

| Linguaggio | Archivio | Pacchetto |
| ---------| ---------- | ------- |
| Nodo | [https://github.com/Azure/azure-sdk-for-node](https://github.com/Azure/azure-sdk-for-node) | [https://www.npmjs.com/package/azure-arm-iotcentral](https://www.npmjs.com/package/azure-arm-iotcentral)
| Python |[https://github.com/Azure/azure-sdk-for-python](https://github.com/Azure/azure-sdk-for-python) | [https://pypi.org/project/azure-mgmt-iotcentral](https://pypi.org/project/azure-mgmt-iotcentral)
| C# | [https://github.com/Azure/azure-sdk-for-net](https://github.com/Azure/azure-sdk-for-net) | [https://www.nuget.org/packages/Microsoft.Azure.Management.IotCentral](https://www.nuget.org/packages/Microsoft.Azure.Management.IotCentral)
| Ruby | [https://github.com/Azure/azure-sdk-for-ruby](https://github.com/Azure/azure-sdk-for-ruby) | [https://rubygems.org/gems/azure_mgmt_iot_central](https://rubygems.org/gems/azure_mgmt_iot_central)
| Java | [https://github.com/Azure/azure-sdk-for-java](https://github.com/Azure/azure-sdk-for-java) | [https://search.maven.org/search?q=a:azure-mgmt-iotcentral](https://search.maven.org/search?q=a:azure-mgmt-iotcentral)
| Go | [https://github.com/Azure/azure-sdk-for-go](https://github.com/Azure/azure-sdk-for-go) | [https://github.com/Azure/azure-sdk-for-go](https://github.com/Azure/azure-sdk-for-go)

## <a name="next-steps"></a>Passaggi successivi

Dopo aver appreso come amministrare l'applicazione Azure IoT Central, il passaggio successivo consigliato consiste nell'apprendere [come gestire utenti e ruoli](howto-manage-users-roles.md) in Azure IoT Central.Now that you've learned about how to administer your Azure IoT Central application, the suggested next step is to learn about Manage users and roles in Azure IoT Central.
