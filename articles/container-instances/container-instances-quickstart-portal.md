---
title: Avvio rapido - Creare il primo contenitore di 	Istanze di contenitore di Azure con il portale di Azure
description: In questa guida introduttiva si userà il portale di Azure per distribuire un contenitore in Istanze di contenitore di Azure
services: container-instances
author: mmacy
manager: jeconnoc
ms.service: container-instances
ms.topic: quickstart
ms.date: 05/11/2018
ms.author: marsma
ms.custom: mvc
ms.openlocfilehash: 6aa6fb27b2aa7c8b9614e5812fadc629b1e185f8
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/12/2018
ms.locfileid: "34076239"
---
# <a name="quickstart-create-your-first-container-in-azure-container-instances"></a>Guida introduttiva: Creare il primo contenitore in Istanze di contenitore di Azure

Istanze di contenitore di Azure semplifica la creazione e gestione di contenitori Docker in Azure, senza dover eseguire il provisioning di macchine virtuali o di adottare un servizio di livello superiore. In questa guida introduttiva viene usato il portale di Azure per creare un contenitore in Azure, che viene quindi esposto a Internet con un nome di dominio completo (FQDN). Dopo aver configurato alcune impostazioni, nel browser verrà visualizzato quanto segue:

![App distribuita usando Istanze di contenitore di Azure visualizzata nel browser][aci-portal-07]

## <a name="sign-in-to-azure"></a>Accedere ad Azure

Accedere al portale di Azure all'indirizzo https://portal.azure.com.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito][azure-free-account] prima di iniziare.

## <a name="create-a-container-instance"></a>Creare un'istanza di contenitore

Selezionare **Crea una risorsa** > **Contenitori** > **Istanze di contenitore**.

![Iniziare a creare una nuova istanza di contenitore nel portale di Azure][aci-portal-01]

Immettere i valori seguenti nelle caselle di testo **Nome contenitore**, **Immagine del contenitore** e **Gruppo di risorse**. Lasciare invariati gli altri valori predefiniti e quindi selezionare **OK**.

* Nome contenitore: `mycontainer`
* Immagine del contenitore: `microsoft/aci-helloworld`
* Gruppo di risorse: `myResourceGroup`

![Configurazione delle impostazioni di base per una nuova istanza di contenitore nel portale di Azure][aci-portal-03]

Istanze di contenitore di Azure permette di creare contenitori Windows e Linux. Per questa guida introduttiva, lasciare l'impostazione predefinita **Linux** per distribuire l'immagine `microsoft/aci-helloworld` basata su Linux.

In **Configurazione** specificare un'**Etichetta del nome DNS** per il contenitore. Il nome deve essere univoco all'interno dell'area di Azure in cui si crea l'istanza di contenitore. Il contenitore sarà pubblicamente raggiungibile in `<dns-name-label>.<region>.azurecontainer.io`.

Lasciare le altre impostazioni in **Configurazione** impostate sui valori predefiniti e quindi selezionare **OK** per convalidare la configurazione.

![Configurazione di una nuova istanza di contenitore nel portale di Azure][aci-portal-04]

Al termine della convalida, viene visualizzato un riepilogo delle impostazioni del contenitore. Selezionare **OK** per inviare la richiesta di distribuzione del contenitore.

![Riepilogo delle impostazioni per una nuova istanza di contenitore nel portale di Azure][aci-portal-05]

All'avvio della distribuzione, nel dashboard del portale viene visualizzato un riquadro che indica che la distribuzione è in corso. Dopo la distribuzione, il riquadro visualizza la nuova istanza di contenitore.

![Stato di avanzamento della creazione di una nuova istanza di contenitore nel portale di Azure][aci-portal-08]

Selezionare l'istanza di contenitore **mycontainer** per visualizzare le relative proprietà. Prendere nota dell'**FQDN** (nome di dominio completo) dell'istanza di contenitore, nonché dello **Stato**.

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

In questa guida introduttiva è stata creata un'istanza di contenitore di Azure da un'immagine nel registro nell'hub Docker pubblico. Per provare a creare personalmente un'immagine del contenitore e a distribuirla in Istanze di contenitore di Azure da un registro contenitori di Azure privato, passare all'esercitazione su Istanze di contenitore di Azure.

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