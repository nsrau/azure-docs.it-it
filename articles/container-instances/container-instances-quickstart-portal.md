---
title: Avvio rapido - Creare il primo contenitore di 	Istanze di contenitore di Azure con il portale di Azure
description: Distribuire e iniziare a usare Istanze di contenitore di Azure
services: container-instances
author: mmacy
manager: timlt
ms.service: container-instances
ms.topic: quickstart
ms.date: 09/25/2017
ms.author: marsma
ms.custom: mvc
ms.openlocfilehash: 7007fa8989f6d87028906918dbbc9c2998cd3bf4
ms.sourcegitcommit: a48e503fce6d51c7915dd23b4de14a91dd0337d8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/05/2017
---
# <a name="create-your-first-container-in-azure-container-instances"></a>Creare il primo contenitore in Istanze di contenitore di Azure

Istanze di contenitore di Azure semplifica la creazione e la gestione dei contenitori in Azure. In questo avvio rapido si crea un contenitore in Azure e lo si espone a Internet con un indirizzo IP pubblico. Questa operazione viene eseguita nel portale di Azure. Bastano pochi clic per visualizzare quanto segue nel browser:

![App distribuita usando Istanze di contenitore di Azure visualizzata nel browser][aci-app-browser]

## <a name="log-in-to-azure"></a>Accedere ad Azure

Accedere al portale di Azure all'indirizzo http://portal.azure.com.

## <a name="create-a-container-instance"></a>Creare un'istanza di contenitore

Selezionare **Nuovo** > **Contenitori** > **Istanze di contenitore di Azure (anteprima)**.

![Iniziare a creare una nuova istanza di contenitore nel portale di Azure][aci-portal-01]

Immettere i valori seguenti nelle caselle di testo **Nome contenitore**, **Immagine del contenitore** e **Gruppo di risorse**. Lasciare invariati gli altri valori predefiniti, quindi fare clic su **OK**.

* Nome contenitore: `mycontainer`
* Immagine del contenitore: `microsoft/aci-helloworld`
* Gruppo di risorse: `myResourceGroup`

![Configurazione delle impostazioni di base per una nuova istanza di contenitore nel portale di Azure][aci-portal-03]

Istanze di contenitore di Azure permette di creare contenitori Windows e Linux. In questo avvio rapido verranno usati i valori predefiniti di **Linux** perché nel passaggio precedente è stato specificato un contenitore basato su Linux (`microsoft/aci-helloworld`).

Lasciare le altre impostazioni in **Configurazione** impostate sui valori predefiniti, quindi fare clic su **OK** per convalidare la configurazione.

![Configurazione di una nuova istanza di contenitore nel portale di Azure][aci-portal-04]

Al termine della convalida, viene visualizzato un riepilogo delle impostazioni del contenitore. Selezionare **OK** per inviare la richiesta di distribuzione del contenitore.

![Riepilogo delle impostazioni per una nuova istanza di contenitore nel portale di Azure][aci-portal-05]

All'avvio della distribuzione, nel dashboard del portale viene inserito un riquadro che indica lo stato di avanzamento della distribuzione. Una volta completata la distribuzione, il riquadro viene aggiornato in modo da mostrare il nuovo gruppo di contenitori **mycontainer myc1**.

![Stato di avanzamento della creazione di una nuova istanza di contenitore nel portale di Azure][aci-portal-08]

Selezionare il gruppo di contenitori **mycontainer myc1** per visualizzarne le proprietà. Prendere nota dell'**Indirizzo IP** del gruppo di contenitori e dello **STATO** del contenitore.

![Panoramica del gruppo di contenitori nel portale di Azure][aci-portal-06]

Quando il contenitore passa allo stato **In esecuzione**, passare all'indirizzo IP annotato nel passaggio precedente per visualizzare l'applicazione ospitata nel nuovo contenitore.

![App distribuita usando Istanze di contenitore di Azure visualizzata nel browser][aci-app-browser]

<!-- IMAGES -->
[aci-portal-01]: ./media/container-instances-quickstart-portal/qs-portal-01.png
[aci-portal-02]: ./media/container-instances-quickstart-portal/qs-portal-02.png
[aci-portal-03]: ./media/container-instances-quickstart-portal/qs-portal-03.png
[aci-portal-04]: ./media/container-instances-quickstart-portal/qs-portal-04.png
[aci-portal-05]: ./media/container-instances-quickstart-portal/qs-portal-05.png
[aci-portal-06]: ./media/container-instances-quickstart-portal/qs-portal-06.png
[aci-app-browser]: ./media/container-instances-quickstart-portal/qs-portal-07.png
[aci-portal-08]: ./media/container-instances-quickstart-portal/qs-portal-08.png

## <a name="next-steps"></a>Passaggi successivi

In questo avvio rapido è stata creata un'istanza di contenitore di Azure da un'immagine in un repository pubblico dell'hub Docker. Per provare a creare personalmente e distribuire un contenitore in Istanze di contenitore di Azure tramite Registro contenitori di Azure, passare all'esercitazione su Istanze di contenitore di Azure.

> [!div class="nextstepaction"]
> [Esercitazioni su Istanze di contenitore di Azure](./container-instances-tutorial-prepare-app.md)