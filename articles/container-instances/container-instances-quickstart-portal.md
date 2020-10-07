---
title: "Avvio rapido: Distribuire un contenitore Docker in un'istanza di contenitore - Portale"
description: In questo argomento di avvio rapido viene usato il portale di Azure per distribuire rapidamente un'app Web in contenitori, che viene eseguita in un'istanza di contenitore Azure isolata
ms.topic: quickstart
ms.date: 08/24/2020
ms.custom: seodec18, mvc, devx-track-js
ms.openlocfilehash: c8477bd91c3a02a2cd02d341c38c16da251902ae
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/05/2020
ms.locfileid: "91324536"
---
# <a name="quickstart-deploy-a-container-instance-in-azure-using-the-azure-portal"></a>Guida introduttiva: Distribuzione dell'istanza di contenitore in Azure con il portale di Azure

Istanze di Azure Container consente di eseguire i contenitori Docker serverless in Azure in modo semplice e rapido. Distribuire un'applicazione in un'istanza di contenitore su richiesta quando non è necessaria una piattaforma di orchestrazione di contenitori completa come il servizio Azure Kubernetes.

In questa guida introduttiva viene usato il portale di Azure per distribuire un contenitore Docker isolato e renderne disponibile l'applicazione con un nome di dominio completo (FQDN). Dopo la configurazione di alcune impostazioni e la distribuzione del contenitore, è possibile passare all'applicazione in esecuzione:

:::image type="content" source="media/container-instances-quickstart-portal/qs-portal-07.png" alt-text="App distribuita usando Istanze di Azure Container visualizzata nel browser":::

## <a name="sign-in-to-azure"></a>Accedere ad Azure

Accedere al portale di Azure all'indirizzo https://portal.azure.com.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito][azure-free-account] prima di iniziare.

## <a name="create-a-container-instance"></a>Creare un'istanza di contenitore

Selezionare **Crea una risorsa** > **Contenitori** > **Istanze di Container**.

:::image type="content" source="media/container-instances-quickstart-portal/qs-portal-01.png" alt-text="App distribuita usando Istanze di Azure Container visualizzata nel browser":::

Nella pagina **Generale**, immettere i seguenti valori nelle caselle di testo **Gruppo risorse**, **Nome contenitore** e **Immagine del contenitore**. Lasciare invariati gli altri valori predefiniti e quindi selezionare **OK**.

* Gruppo di risorse: **Crea nuovo** > `myresourcegroup`
* Nome contenitore: `mycontainer`
* Origine immagine: **immagini di avvio rapido**
* Immagine del contenitore: `mcr.microsoft.com/azuredocs/aci-helloworld` (Linux)

:::image type="content" source="media/container-instances-quickstart-portal/qs-portal-03.png" alt-text="App distribuita usando Istanze di Azure Container visualizzata nel browser":::

Per questo argomento di avvio rapido, usare le impostazioni predefinite per distribuire l'immagine `aci-helloworld` Microsoft pubblica. In questa immagine Linux di esempio è inclusa una piccola app Web scritta in Node.js che distribuisce una pagina HTML statica. È anche possibile usare le proprie immagini del contenitore archiviate in Registro Azure Container, Docker Hub o altri registri.

Nella pagina **Rete** specificare un'**etichetta del nome DNS** per il contenitore. Il nome deve essere univoco all'interno dell'area di Azure in cui si crea l'istanza di contenitore. Il contenitore sarà pubblicamente raggiungibile in `<dns-name-label>.<region>.azurecontainer.io`. Se si riceve il messaggio di errore "L'etichetta del nome DNS non è disponibile", provare con un'etichetta del nome DNS diversa.

:::image type="content" source="media/container-instances-quickstart-portal/qs-portal-04.png" alt-text="App distribuita usando Istanze di Azure Container visualizzata nel browser":::

Lasciare invariate le altre impostazioni predefinite e quindi selezionare **Verifica e Crea**.

Al termine della convalida, viene visualizzato un riepilogo delle impostazioni del contenitore. Selezionare **Crea** per inviare la richiesta di distribuzione del contenitore.

:::image type="content" source="media/container-instances-quickstart-portal/qs-portal-05.png" alt-text="App distribuita usando Istanze di Azure Container visualizzata nel browser":::

All'avvio della distribuzione, viene visualizzata una notifica che indica che la procedura è in corso. Un'altra notifica viene visualizzata dopo che il gruppo di contenitori è stato distribuito.

Aprire la panoramica per il gruppo di contenitori passando a **Gruppi di risorse** > **myresourcegroup** > **mycontainer**. Prendere nota dell'**FQDN** (nome di dominio completo) dell'istanza di contenitore, nonché dello **Stato**.

:::image type="content" source="media/container-instances-quickstart-portal/qs-portal-06.png" alt-text="App distribuita usando Istanze di Azure Container visualizzata nel browser":::

Quando lo **Stato** è *In esecuzione*, passare al nome di dominio completo del contenitore nel browser.

:::image type="content" source="media/container-instances-quickstart-portal/qs-portal-07.png" alt-text="App distribuita usando Istanze di Azure Container visualizzata nel browser":::

Congratulazioni! Configurando poche impostazioni è stata distribuita un'applicazione accessibile pubblicamente in Istanze di Azure Container.

## <a name="view-container-logs"></a>Visualizzare i log dei contenitori

La visualizzazione dei log per un'istanza di contenitore è utile per risolvere i problemi con il contenitore o l'applicazione eseguita.

Per visualizzare i log del contenitore, in **Impostazioni** selezionare **Contenitori** e quindi **Log**. Dovrebbe essere visualizzata la richiesta HTTP GET generata quando è stata visualizzata l'applicazione nel browser.

:::image type="content" source="media/container-instances-quickstart-portal/qs-portal-11.png" alt-text="App distribuita usando Istanze di Azure Container visualizzata nel browser":::


## <a name="clean-up-resources"></a>Pulire le risorse

Quando il contenitore non serve più, selezionare **Panoramica** per l'istanza di contenitore *mycontainer* e quindi selezionare **Elimina**.

:::image type="content" source="media/container-instances-quickstart-portal/qs-portal-09.png" alt-text="App distribuita usando Istanze di Azure Container visualizzata nel browser":::

Selezionare **Sì** quando viene visualizzata la finestra di dialogo di conferma.

:::image type="content" source="media/container-instances-quickstart-portal/qs-portal-10.png" alt-text="App distribuita usando Istanze di Azure Container visualizzata nel browser":::

## <a name="next-steps"></a>Passaggi successivi

In questo argomento di avvio rapido è stata creata un'istanza di contenitore di Azure da un'immagine Microsoft pubblica. Per provare a creare un'immagine del contenitore e a distribuirla da un Registro Azure Container privato, passare all'esercitazione su Istanze di Azure Container.

> [!div class="nextstepaction"]
> [Esercitazione su Istanze di Azure Container](./container-instances-tutorial-prepare-app.md)

<!-- LINKS - External -->
[azure-free-account]: https://azure.microsoft.com/free/