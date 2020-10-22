---
title: Usare il sito delle soluzioni Azure IoT - Azure | Microsoft Docs
description: Viene descritto come usare il sito Web AzureIoTSolutions.com per distribuire acceleratori di soluzioni.
author: dominicbetts
manager: philmea
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 12/13/2018
ms.author: dobett
ms.openlocfilehash: f5d816b3d44a6fadd8dd462a14e62de4cdac2a00
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/22/2020
ms.locfileid: "92370611"
---
# <a name="use-the-azureiotsolutionscom-site-to-deploy-your-solution-accelerator"></a>Usare il sito azureiotsolutions.com per distribuire acceleratori di soluzioni

È possibile distribuire acceleratori di soluzioni di Azure IoT nella sottoscrizione di Azure da [AzureIoTSolutions.com](https://www.azureiotsolutions.com/Accelerators). AzureIoTSolutions.com ospita sia acceleratori di soluzioni open source Microsoft che di partner. Questi acceleratori di soluzioni sono allineati all'[Architettura di riferimento di Azure IoT](https://aka.ms/iotrefarchitecture). È possibile usare il sito per distribuire rapidamente un acceleratore di soluzione come ambiente demo o di produzione.

![AzureIoTSolutions.com](media/iot-accelerators-permissions/iotsolutionscom.png)

> [!TIP]
> Se è necessario un maggiore controllo sul processo di distribuzione, è possibile usare l'[interfaccia della riga di comando per distribuire un acceleratore di soluzione](iot-accelerators-remote-monitoring-deploy-cli.md).

È possibile distribuire gli acceleratori di soluzioni nelle configurazioni seguenti:

* **Standard**: distribuzione di un'infrastruttura espansa per lo sviluppo di un ambiente di produzione. Il servizio Azure Container distribuisce i microservizi in diverse macchine virtuali di Azure. Kubernetes orchestra i contenitori Docker che ospitano i singoli microservizi.
* **Basic**: versione di costo ridotta per una dimostrazione o per testare una distribuzione. Tutti i microservizi vengono distribuiti in un'unica macchina virtuale di Azure.
* **Locale**: distribuzione di un computer locale per i test e lo sviluppo. Questo approccio prevede la distribuzione dei microservizi in un contenitore Docker locale e la connessione a Hub IoT, Azure Cosmos DB e ai servizi di archiviazione di Azure nel cloud.

Ogni acceleratore di soluzione usa una diversa combinazione di servizi di Azure, ad esempio Hub IoT, Analisi di flusso di Azure e Cosmos DB. Per altre informazioni, visitare [AzureIoTSolutions.com](https://www.azureiotsolutions.com/Accelerators) e selezionare un acceleratore di soluzione.

## <a name="sign-in-at-azureiotsolutionscom"></a>Accedere al sito azureiotsolutions.com

Prima di poter distribuire un acceleratore di soluzione, è necessario accedere ad AzureIoTSolutions.com usando le credenziali associate a una sottoscrizione di Azure. Se l'account è associato a più di un tenant di Microsoft Azure Active Directory (AD), è possibile usare l'**elenco a discesa di selezione dell'account** per scegliere la directory da usare.

Le autorizzazioni per distribuire gli acceleratori di soluzioni, gestire gli utenti e gestire i servizi di Azure dipendono dal proprio ruolo nella directory selezionata. I ruoli di Azure AD comuni associati agli acceleratori di soluzioni includono:

* **Amministratore globale**: possono essere presenti molti [amministratori globali](../active-directory/roles/permissions-reference.md) per ogni tenant di Azure ad:

  * Quando si crea un tenant di Azure AD, si è per impostazione predefinita l'amministratore globale del tenant.
  * L'amministratore globale può distribuire acceleratori di soluzioni di base e standard.

* **Utente di dominio**: possono essere presenti molti utenti del dominio per ogni tenant Azure ad. Un utente di dominio può distribuire un acceleratore di soluzione di base.

* **Utente Guest**: possono essere presenti molti utenti guest per Azure ad tenant. Gli utenti guest non possono distribuire un acceleratore di soluzione nel tenant di Azure AD.

Per altre informazioni sugli utenti e i ruoli in Azure AD, vedere le risorse seguenti:

* [Creare utenti in Azure Active Directory](../active-directory/fundamentals/active-directory-users-profile-azure-portal.md)
* [Assegnare utenti alle app](../active-directory/manage-apps/assign-user-or-group-access-portal.md)

## <a name="choose-your-device"></a>Scegliere il dispositivo

Il sito AzureIoTSolutions.com è collegato al [catalogo di dispositivi Azure Certified per IoT](https://catalog.azureiotsolutions.com/).

Il catalogo elenca centinaia di dispositivi di hardware IoT certificati, che è possibile collegare agli acceleratori di soluzioni per iniziare a creare soluzioni IoT.

![Catalogo dei dispositivi](media/iot-accelerators-permissions/devicecatalog.png)

I produttori di hardware possono fare clic su **Diventare un Partner** per informazioni sulla collaborazione con Microsoft al programma Certified per IoT.

## <a name="next-steps"></a>Passaggi successivi

Per provare uno degli acceleratori di soluzioni IoT, vedere le guide introduttive:

* [Provare una soluzione di monitoraggio remoto](quickstart-remote-monitoring-deploy.md)
* [Provare una soluzione di fabbrica connessa](quickstart-connected-factory-deploy.md)
* [Provare una soluzione di manutenzione predittiva](quickstart-predictive-maintenance-deploy.md)
* [Provare una soluzione di simulazione dei dispositivi](quickstart-device-simulation-deploy.md)
