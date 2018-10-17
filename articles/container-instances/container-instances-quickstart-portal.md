---
title: "Guida introduttiva: eseguire un'applicazione in Istanze di contenitore di Azure"
description: In questa guida introduttiva si userà il portale di Azure per distribuire un'applicazione in esecuzione in un contenitore Docker in Istanze di contenitore di Azure
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: quickstart
ms.date: 10/02/2018
ms.author: danlep
ms.custom: mvc
ms.openlocfilehash: 6ab7680559400c0a270142563ecf7e251c2cd1b8
ms.sourcegitcommit: 67abaa44871ab98770b22b29d899ff2f396bdae3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/08/2018
ms.locfileid: "48857721"
---
# <a name="quickstart-run-an-application-in-azure-container-instances"></a>Guida introduttiva: eseguire un'applicazione in Istanze di contenitore di Azure

Le Istanze di contenitore di Azure consentono di eseguire i contenitori Docker in Azure in modo semplice e rapido, senza la necessità di distribuire macchine virtuali o usare una piattaforma di orchestrazione di contenitori completa come Kubernetes. In questa guida introduttiva viene usato il portale di Azure per creare un contenitore in Azure e renderne disponibile l'applicazione con un nome di dominio completo (FQDN). Dopo la configurazione di alcune impostazioni e la distribuzione del contenitore, è possibile passare all'applicazione in esecuzione:

![App distribuita in Istanze di contenitore di Azure visualizzata nel browser][aci-portal-07]

## <a name="sign-in-to-azure"></a>Accedere ad Azure

Accedere al portale di Azure all'indirizzo https://portal.azure.com.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito][azure-free-account] prima di iniziare.

## <a name="create-a-container-instance"></a>Creare un'istanza di contenitore

Selezionare **Crea una risorsa** > **Contenitori** > **Istanze di contenitore**.

![Iniziare a creare una nuova istanza di contenitore nel portale di Azure][aci-portal-01]

Immettere i valori seguenti nelle caselle di testo **Nome contenitore**, **Immagine del contenitore** e **Gruppo di risorse**. Lasciare invariati gli altri valori predefiniti e quindi selezionare **OK**.

* Nome contenitore: `mycontainer`
* Immagine del contenitore: `microsoft/aci-helloworld`
* Gruppo di risorse: **Crea nuovo** > `myResourceGroup`

![Configurazione delle impostazioni di base per una nuova istanza di contenitore nel portale di Azure][aci-portal-03]

Istanze di contenitore di Azure permette di creare contenitori Windows e Linux. Per questa guida introduttiva, lasciare l'impostazione predefinita **Linux** per distribuire l'immagine `microsoft/aci-helloworld` basata su Linux.

In **Configurazione** specificare un'**Etichetta del nome DNS** per il contenitore. Il nome deve essere univoco all'interno dell'area di Azure in cui si crea l'istanza di contenitore. Il contenitore sarà pubblicamente raggiungibile in `<dns-name-label>.<region>.azurecontainer.io`.

Lasciare le altre impostazioni in **Configurazione** impostate sui valori predefiniti e quindi selezionare **OK** per convalidare la configurazione.

![Configurazione di una nuova istanza di contenitore nel portale di Azure][aci-portal-04]

Al termine della convalida, viene visualizzato un riepilogo delle impostazioni del contenitore. Selezionare **OK** per inviare la richiesta di distribuzione del contenitore.

![Riepilogo delle impostazioni per una nuova istanza di contenitore nel portale di Azure][aci-portal-05]

All'avvio della distribuzione, viene visualizzata una notifica che indica che la distribuzione è in corso. Un'altra notifica viene visualizzata dopo che il gruppo di contenitori è stato distribuito.

![Stato di avanzamento della creazione di una nuova istanza di contenitore nel portale di Azure][aci-portal-08]

Aprire la panoramica per il gruppo di contenitori passando a **Gruppi di risorse** > **myResourceGroup** > **mycontainer**. Prendere nota dell'**FQDN** (nome di dominio completo) dell'istanza di contenitore, nonché dello **Stato**.

![Panoramica del gruppo di contenitori nel portale di Azure][aci-portal-06]

Quando lo **Stato** è *In esecuzione*, passare al nome di dominio completo del contenitore nel browser.

![App distribuita usando Istanze di contenitore di Azure visualizzata nel browser][aci-portal-07]

Congratulazioni! Configurando poche impostazioni è stata distribuita un'applicazione accessibile pubblicamente in Istanze di contenitore di Azure.

## <a name="view-container-logs"></a>Visualizzare i log dei contenitori

La visualizzazione dei log per un'istanza di contenitore è utile per risolvere i problemi con il contenitore o l'applicazione eseguita.

Per visualizzare i log del contenitore, in **Impostazioni** selezionare **Contenitori** e quindi **Log**. Dovrebbe essere visualizzata la richiesta HTTP GET generata quando è stata visualizzata l'applicazione nel browser.

![Log dei contenitori nel portale di Azure][aci-portal-11]

## <a name="clean-up-resources"></a>Pulire le risorse

Quando il contenitore non serve più, selezionare **Panoramica** per l'istanza di contenitore *mycontainer* e quindi selezionare **Elimina**.

![Eliminazione dell'istanza di contenitore nel portale di Azure][aci-portal-09]

Selezionare **Sì** quando viene visualizzata la finestra di dialogo di conferma.

![Conferma dell'eliminazione di un'istanza di contenitore nel portale di Azure][aci-portal-10]

## <a name="next-steps"></a>Passaggi successivi

In questa guida introduttiva è stata creata un'istanza di contenitore di Azure da un'immagine nel registro nell'hub Docker pubblico. Per provare a creare un'immagine del contenitore e a distribuirla da un registro contenitori di Azure privato, passare all'esercitazione su Istanze di contenitore di Azure.

> [!div class="nextstepaction"]
> [Esercitazione su Istanze di contenitore di Azure](./container-instances-tutorial-prepare-app.md)

<!-- IMAGES -->
[aci-portal-01]: ./media/container-instances-quickstart-portal/qs-portal-01.png
[aci-portal-03]: ./media/container-instances-quickstart-portal/qs-portal-03.png
[aci-portal-04]: ./media/container-instances-quickstart-portal/qs-portal-04.png
[aci-portal-05]: ./media/container-instances-quickstart-portal/qs-portal-05.png
[aci-portal-06]: ./media/container-instances-quickstart-portal/qs-portal-06.png
[aci-portal-07]: ./media/container-instances-quickstart-portal/qs-portal-07.png
[aci-portal-08]: ./media/container-instances-quickstart-portal/qs-portal-08.png
[aci-portal-09]: ./media/container-instances-quickstart-portal/qs-portal-09.png
[aci-portal-10]: ./media/container-instances-quickstart-portal/qs-portal-10.png
[aci-portal-11]: ./media/container-instances-quickstart-portal/qs-portal-11.png

<!-- LINKS - External -->
[azure-free-account]: https://azure.microsoft.com/free/