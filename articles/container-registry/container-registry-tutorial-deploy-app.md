---
title: Esercitazione per Registro contenitori di Azure - Distribuire un'app Web dal Registro contenitori di Azure
description: Distribuire un'app Web basata su Linux usando un'immagine del contenitore da un registro contenitori di Azure con replica geografica. Seconda parte di una serie in tre parti.
services: container-registry
author: mmacy
manager: timlt
ms.service: container-registry
ms.topic: tutorial
ms.date: 10/24/2017
ms.author: marsma
ms.custom: mvc
ms.openlocfilehash: d775a17cb8069a7521788d850d7d52b92cc67526
ms.sourcegitcommit: a48e503fce6d51c7915dd23b4de14a91dd0337d8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/05/2017
---
# <a name="deploy-web-app-from-azure-container-registry"></a>Distribuire un'app Web dal Registro contenitori di Azure

Questa esercitazione è la seconda di una serie in tre parti. Nella [prima parte](container-registry-tutorial-prepare-registry.md), è stato creato un registro contenitori privato, con replica geografica; è stata quindi creata un'immagine del contenitore poi inserita nel registro contenitori. Questo articolo illustra come distribuire il contenitore in due istanze di App Web in due diverse aree di Azure, per sfruttare i vantaggi della struttura simile alla rete del registro contenitori con replica geografica.

In questa esercitazione, la seconda della serie, vengono illustrate le seguenti attività:

> [!div class="checklist"]
> * Distribuire un'immagine del contenitore in due istanze di *App Web per i contenitori*
> * Verificare l'applicazione distribuita

Se non è ancora stato creato il registro contenitori con replica geografica e non è stata inserita l'immagine dell'applicazione di esempio nei contenitori nel registro, tornare all'esercitazione precedente della serie, [Preparare un Registro contenitori di Azure con replica geografica](container-registry-tutorial-prepare-registry.md).

Nella parte successiva della serie viene aggiornata l'applicazione e quindi inserita una nuova immagine del contenitore nel registro. Infine, si esamina ogni istanza dell'app Web in esecuzione per visualizzare le modifiche automaticamente applicate a entrambe, che mostrano il Registro contenitori di Azure con replica geografica e i webhook in azione.

## <a name="automatic-deployment-to-web-apps-for-containers"></a>Distribuzione automatica nell'app Web per contenitori

Registro contenitori di Azure offre il supporto per la distribuzione di applicazioni nei contenitori direttamente in[App Web per contenitori](../app-service/containers/index.yml). In questa esercitazione, viene usato il portale di Azure per distribuire l'immagine del contenitore creato nell'esercitazione precedente nei due piani di app Web ubicati in aree diverse di Azure.

Quando si distribuisce un'app Web da un'immagine del contenitore nel registro contenitori e si dispone di un registro contenitori con replica geografica nella stessa area, Registro contenitori di Azure crea automaticamente un [webhook](container-registry-webhook.md) di distribuzione dell'immagine. Quando viene inserita una nuova immagine nel repository del contenitore, il webhook preleva la modifica e distribuisce automaticamente la nuova immagine del contenitore nell'app Web.

## <a name="deploy-a-web-app-for-containers-instance"></a>Distribuire un'istanza di App Web per contenitori

In questo passaggio viene creata un'istanza di App Web per contenitori nell'area degli *Stati Uniti occidentali*.

Accedere al [portale di Azure](https://portal.azure.com) e passare al registro contenitori creato nell'esercitazione precedente.

Selezionare **Repository** > **acr-helloworld**, quindi fare clic con il pulsante destro del mouse sul tag **v1** in **Tag** e selezionare **Deploy to web app** (Distribuisci nell'app Web).

![Distribuzione nel servizio app nel portale di Azure][deploy-app-portal-01]

In **App Web per contenitori** specificare i valori seguenti per ogni impostazione:

| Impostazione | Valore |
|---|---|
| **Nome del sito** | Un nome univoco globale per l'app Web. In questo esempio viene usato il formato `<acrName>-westus` per identificare facilmente il registro e l'area da cui viene distribuita l'app Web. |
| **Gruppo di risorse** | **Usare il**  > `myResourceGroup` esistente |
| **Percorso o piano del servizio App** | Creare un nuovo piano denominato `plan-westus` nell'area degli **Stati Uniti occidentali**. |
| **Immagine** | `acr-helloworld:v1`

Selezionare **Crea** per eseguire il provisioning dell'app Web per l'area degli *Stati Uniti occidentali*.

![App Web nella configurazione di Linux nel portale di Azure][deploy-app-portal-02]

## <a name="view-the-deployed-web-app"></a>Visualizzare l'app Web distribuita

Dopo aver completato la distribuzione, è possibile visualizzare l'applicazione in esecuzione passando al relativo URL nel browser.

Nel portale, selezionare **Servizi app**, quindi l'app Web di cui è stato eseguito il provisioning nel passaggio precedente. In questo esempio, l'app Web è denominata *uniqueregistryname-westus*.

Selezionare l'URL con collegamento ipertestuale dell'app Web in alto a destra nella panoramica di **Servizio app** per visualizzare l'applicazione in esecuzione nel browser.

![App Web nella configurazione di Linux nel portale di Azure][deploy-app-portal-04]

Una volta distribuita l'immagine Docker dal Registro contenitori con replica geografica, il sito visualizza un'immagine che rappresenta l'area di Azure che ospita il registro contenitori.

![Applicazione Web distribuita visualizzata in un browser][deployed-app-westus]

## <a name="deploy-second-web-app-for-containers-instance"></a>Distribuire una seconda istanza di App Web per contenitori

Usare la procedura descritta nella sezione precedente per distribuire una seconda app Web nell'area degli *Stati Uniti orientali*. In **App Web per contenitori** specificare i valori seguenti:

| Impostazione | Valore |
|---|---|
| **Nome del sito** | Un nome univoco globale per l'app Web. In questo esempio viene usato il formato `<acrName>-eastus` per identificare facilmente il registro e l'area da cui viene distribuita l'app Web. |
| **Gruppo di risorse** | **Usare il**  > `myResourceGroup` esistente |
| **Percorso o piano del servizio App** | Creare un nuovo piano denominato `plan-eastus` nell'area degli **Stati Uniti orientali**. |
| **Immagine** | `acr-helloworld:v1`

Selezionare **Crea** per eseguire il provisioning dell'app Web per l'area degli *Stati Uniti orientali*.

![App Web nella configurazione di Linux nel portale di Azure][deploy-app-portal-06]

## <a name="view-the-deployed-web-app"></a>Visualizzare l'app Web distribuita

Come visto prima, è possibile visualizzare l'applicazione in esecuzione passando al relativo URL nel browser.

Nel portale, selezionare **Servizi app**, quindi l'app Web di cui è stato eseguito il provisioning nel passaggio precedente. In questo esempio, l'app Web è denominata *uniqueregistryname-eastus*.

Selezionare l'URL con collegamento ipertestuale dell'app Web in alto a destra in **Panoramica di Servizio app** per visualizzare l'applicazione in esecuzione nel browser.

![App Web nella configurazione di Linux nel portale di Azure][deploy-app-portal-07]

Una volta distribuita l'immagine Docker dal Registro contenitori con replica geografica, il sito visualizza un'immagine che rappresenta l'area di Azure che ospita il registro contenitori.

![Applicazione Web distribuita visualizzata in un browser][deployed-app-eastus]

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono state distribuite due istanze di app Web per contenitori da un registro contenitori con replica geografica di Azure. In questa esercitazione sono stati completati i passaggi seguenti:

> [!div class="checklist"]
> * Distribuzione di un'immagine del contenitore in due istanze di *App Web per i contenitori*
> * Verifica dell'applicazione distribuita

Passare all'esercitazione successiva per aggiornare e quindi distribuire una nuova immagine del contenitore nel registro contenitori, quindi verificare che le app Web in esecuzione in entrambe le aree siano state aggiornate automaticamente.

> [!div class="nextstepaction"]
> [Distribuire un aggiornamento nell'immagine del contenitore con replica geografica](./container-registry-tutorial-deploy-update.md)

<!-- IMAGES -->
[deploy-app-portal-01]: ./media/container-registry-tutorial-deploy-app/deploy-app-portal-01.png
[deploy-app-portal-02]: ./media/container-registry-tutorial-deploy-app/deploy-app-portal-02.png
[deploy-app-portal-03]: ./media/container-registry-tutorial-deploy-app/deploy-app-portal-03.png
[deploy-app-portal-04]: ./media/container-registry-tutorial-deploy-app/deploy-app-portal-04.png
[deploy-app-portal-05]: ./media/container-registry-tutorial-deploy-app/deploy-app-portal-05.png
[deploy-app-portal-06]: ./media/container-registry-tutorial-deploy-app/deploy-app-portal-06.png
[deploy-app-portal-07]: ./media/container-registry-tutorial-deploy-app/deploy-app-portal-07.png
[deployed-app-westus]: ./media/container-registry-tutorial-deploy-app/deployed-app-westus.png
[deployed-app-eastus]: ./media/container-registry-tutorial-deploy-app/deployed-app-eastus.png