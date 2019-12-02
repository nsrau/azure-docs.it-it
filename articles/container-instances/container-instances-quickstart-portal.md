---
title: "Avvio rapido: Distribuire un contenitore Docker in un'istanza di contenitore - Portale"
description: In questo argomento di avvio rapido viene usato il portale di Azure per distribuire rapidamente un'app Web in contenitori, che viene eseguita in un'istanza di contenitore Azure isolata
services: container-instances
author: dlepow
manager: gwallace
ms.service: container-instances
ms.topic: quickstart
ms.date: 04/17/2019
ms.author: danlep
ms.custom: seodec18, mvc
ms.openlocfilehash: e0c5ba57c7664a64c1b11bed215f419f31630d39
ms.sourcegitcommit: 85e7fccf814269c9816b540e4539645ddc153e6e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/26/2019
ms.locfileid: "74533519"
---
# <a name="quickstart-deploy-a-container-instance-in-azure-using-the-azure-portal"></a>Guida introduttiva: Distribuzione dell'istanza di contenitore in Azure con il portale di Azure

Istanze di Azure Container consente di eseguire i contenitori Docker serverless in Azure in modo semplice e rapido. Distribuire un'applicazione in un'istanza di contenitore su richiesta quando non è necessaria una piattaforma di orchestrazione di contenitori completa come il servizio Azure Kubernetes.

In questa guida introduttiva viene usato il portale di Azure per distribuire un contenitore Docker isolato e renderne disponibile l'applicazione con un nome di dominio completo (FQDN). Dopo la configurazione di alcune impostazioni e la distribuzione del contenitore, è possibile passare all'applicazione in esecuzione:

![App distribuita in Istanze di Azure Container visualizzata nel browser][aci-portal-07]

## <a name="sign-in-to-azure"></a>Accedere ad Azure

Accedere al portale di Azure all'indirizzo https://portal.azure.com.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito][azure-free-account] prima di iniziare.

## <a name="create-a-container-instance"></a>Creare un'istanza di contenitore

Selezionare **Crea una risorsa** > **Contenitori** > **Istanze di Container**.

![Iniziare a creare una nuova istanza di contenitore nel portale di Azure][aci-portal-01]

Nella pagina **Generale**, immettere i seguenti valori nelle caselle di testo **Gruppo risorse**, **Nome contenitore** e **Immagine del contenitore**. Lasciare invariati gli altri valori predefiniti e quindi selezionare **OK**.

* Gruppo di risorse: **Crea nuovo** > `myresourcegroup`
* Nome contenitore: `mycontainer`
* Immagine del contenitore: `mcr.microsoft.com/azuredocs/aci-helloworld`

![Configurazione delle impostazioni di base per una nuova istanza di contenitore nel portale di Azure][aci-portal-03]

Per questo Avvio rapido, usare l'impostazione predefinita **Tipo di immagine** di **Pubblico** per distribuire l'immagine pubblica `aci-helloworld` Microsoft. In questa immagine Linux è inclusa una piccola app Web scritta in Node.js che distribuisce una pagina HTML statica.

Nella pagina **Rete** specificare un'**etichetta del nome DNS** per il contenitore. Il nome deve essere univoco all'interno dell'area di Azure in cui si crea l'istanza di contenitore. Il contenitore sarà pubblicamente raggiungibile in `<dns-name-label>.<region>.azurecontainer.io`. Se si riceve il messaggio di errore "L'etichetta del nome DNS non è disponibile", provare con un'etichetta del nome DNS diversa.

![Configurazione di una nuova istanza di contenitore nel portale di Azure][aci-portal-04]

Lasciare invariate le altre impostazioni predefinite e quindi selezionare **Verifica e Crea**.

Al termine della convalida, viene visualizzato un riepilogo delle impostazioni del contenitore. Selezionare **Crea** per inviare la richiesta di distribuzione del contenitore.

![Riepilogo delle impostazioni per una nuova istanza di contenitore nel portale di Azure][aci-portal-05]

All'avvio della distribuzione, viene visualizzata una notifica che indica che la distribuzione è in corso. Un'altra notifica viene visualizzata dopo che il gruppo di contenitori è stato distribuito.

Aprire la panoramica per il gruppo di contenitori passando a **Gruppi di risorse** > **myresourcegroup** > **mycontainer**. Prendere nota dell'**FQDN** (nome di dominio completo) dell'istanza di contenitore, nonché dello **Stato**.

![Panoramica del gruppo di contenitori nel portale di Azure][aci-portal-06]

Quando lo **Stato** è *In esecuzione*, passare al nome di dominio completo del contenitore nel browser.

![App distribuita usando Istanze di Azure Container visualizzata nel browser][aci-portal-07]

Congratulazioni! Configurando poche impostazioni è stata distribuita un'applicazione accessibile pubblicamente in Istanze di Azure Container.

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

In questo argomento di avvio rapido è stata creata un'istanza di contenitore di Azure da un'immagine Microsoft pubblica. Per provare a creare un'immagine del contenitore e a distribuirla da un Registro Azure Container privato, passare all'esercitazione su Istanze di Azure Container.

> [!div class="nextstepaction"]
> [Esercitazione su Istanze di Azure Container](./container-instances-tutorial-prepare-app.md)

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