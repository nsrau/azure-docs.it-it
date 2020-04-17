---
title: Come distribuire lo strumento di etichettatura di esempio riconoscitore di moduli
titleSuffix: Azure Cognitive Services
description: Scopri i diversi modi in cui puoi distribuire lo strumento di etichettatura degli esempi di riconoscitore di moduli per facilitare l'apprendimento supervisionato.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: how-to
ms.date: 04/14/2020
ms.author: pafarley
ms.openlocfilehash: 7ddb4b2cd465b5e9542d777d33b9bd8cb952becd
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81531338"
---
# <a name="deploy-the-sample-labeling-tool"></a>Distribuire lo strumento di etichettatura di esempio

Lo strumento di etichettatura dell'esempio di riconoscimento dei moduli è un'applicazione che fornisce una semplice interfaccia utente (UI), che è possibile utilizzare per etichettare manualmente i moduli (documenti) allo scopo di un apprendimento supervisionato. In questo articolo, ti forniremo link e istruzioni che ti indicheranno come:

* [Eseguire lo strumento di etichettatura di esempio in localeRun the sample labeling tool locally](#run-the-sample-labeling-tool-locally)
* [Distribuire lo strumento di etichettatura di esempio in un'istanza del contenitore di Azure (ACI)Deploy the sample labeling tool to an Azure Container Instance (ACI)](#deploy-with-azure-container-instances-aci)
* [Utilizzare e contribuire allo strumento di etichettatura dei moduli OCR open source](#open-source-on-github)

## <a name="run-the-sample-labeling-tool-locally"></a>Eseguire lo strumento di etichettatura di esempio in localeRun the sample labeling tool locally

Il modo più rapido per avviare l'etichettatura dei dati consiste nell'eseguire localmente lo strumento di etichettatura di esempio. La guida introduttiva seguente usa l'API REST del riconoscitore di moduli e lo strumento di etichettatura di esempio per eseguire il training di un modello personalizzato con dati etichettati manualmente. 

* [Guida introduttiva: assegnare un'etichetta ai moduli, eseguire](./quickstarts/label-tool.md)il training di un modello e analizzare un modulo utilizzando lo strumento di etichettatura di esempio.

## <a name="deploy-with-azure-container-instances-aci"></a>Distribuzione con istanze del contenitore di Azure (ACI)Deploy with Azure Container Instances (ACI)

Prima di iniziare, è importante notare che esistono due modi per distribuire lo strumento di etichettatura di esempio in un'istanza del contenitore di Azure (ACI). Entrambe le opzioni vengono utilizzate per eseguire lo strumento di etichettatura di esempio con ACI:Both options are used to run the sample labeling tool with ACI: 

* [Uso del portale di Azure](#azure-portal)
* [Uso dell'interfaccia della riga di comando di AzureUsing the Azure CLI](#azure-cli)

### <a name="azure-portal"></a>Portale di Azure

Seguire questi passaggi per creare una nuova risorsa usando il portale di Azure:Follow these steps to create a new resource using the Azure portal: 

1. Accedere al [portale di Azure](https://portal.azure.com/signin/index/).
2. Selezionare **Crea una risorsa**. 
3. Selezionare quindi **App Web**. 

   > [!div class="mx-imgBorder"]
   > ![Selezionare l'app Web](./media/quickstarts/formre-create-web-app.png)
   
4. Prima di tutto, assicurati che la scheda **Nozioni di base** sia selezionata. Ora, è necessario fornire alcune informazioni: 

   > [!div class="mx-imgBorder"]
   > ![Nozioni di base su seleziona](./media/quickstarts/formre-select-basics.png)
   * Sottoscrizione: selezionare una sottoscrizione di Azure esistenteSubscription - Select an existing Azure subscription
   * Gruppo di risorse: è possibile riutilizzare un gruppo di risorse esistente o crearne uno nuovo per questo progetto. È consigliabile creare un nuovo gruppo di risorse.
   * Nome: assegnare un nome all'app Web. 
   * Pubblica - Seleziona **contenitore Docker**
   * Sistema operativo - Selezionare **Linux**
   * Regione - Scegli una regione che abbia senso per te.
   * Piano Linux: selezionare un piano tariffario/piano per il servizio app. 

   > [!div class="mx-imgBorder"]
   > ![Configurare l'app Web](./media/quickstarts/formre-select-docker-linux.png)

5. Selezionare quindi la scheda **Docker.** 

   > [!div class="mx-imgBorder"]
   > ![Seleziona finestra mobile](./media/quickstarts/formre-select-docker.png)

6. Ora configuriamo il contenitore Docker. Tutti i campi sono obbligatori se non diversamente indicato:

   * Opzioni - Seleziona **contenitore singolo**
   * Origine immagine - Seleziona **registro privato** 
   * URL server - Impostare questa opzione su`https://mcr.microsoft.com`
   * Nome utente (facoltativo): crea un nome utente. 
   * Password (facoltativo): crea una password sicura che ricorderai.
   * Immagine e tag - Impostare questa opzione su`mcr.microsoft.com/azure-cognitive-services/custom-form/labeltool:latest`
   * Distribuzione continua: impostare questa opzione su **Attivato** se si desidera ricevere gli aggiornamenti automatici quando il team di sviluppo apporta modifiche allo strumento di etichettatura di esempio.
   * Comando di avvio - Impostare questa opzione su`./run.sh eula=accept`

   > [!div class="mx-imgBorder"]
   > ![Configurare Docker](./media/quickstarts/formre-configure-docker.png)

7. È tutto. Selezionare quindi **Revisione e creazione**, quindi **Crea** per distribuire l'app Web. Al termine, è possibile accedere all'app Web all'URL fornito nella **panoramica** della risorsa.

> [!NOTE]
> Quando si crea l'app Web, è anche possibile configurare l'autorizzazione/autenticazione. Questo non è necessario per iniziare. 

### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

In alternativa all'uso del portale di Azure, è possibile creare una risorsa usando l'interfaccia della riga di comando di Azure.As an alternative to using the Azure portal, you can create a resource using the Azure CLI. Prima di continuare, è necessario installare l'interfaccia della riga di comando di [Azure.](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) È possibile ignorare questo passaggio se si sta già lavorando con l'interfaccia della riga di comando di Azure.You can skip this step if you're already working with the Azure CLI. 

Ci sono alcune cose che devi sapere su questo comando:

* `DNS_NAME_LABEL=aci-demo-$RANDOM`genera un nome DNS casuale. 
* In questo esempio si presuppone che si disponga di un gruppo di risorse che è possibile usare per creare una risorsa. Sostituire `<resource_group_name>` con un gruppo di risorse valido associato alla sottoscrizione. 
* È necessario specificare dove si desidera creare la risorsa. Sostituire `<region name>` con l'area desiderata per l'app Web. 
* Questo comando accetta automaticamente l'EULA.

Dall'interfaccia della riga di comando di Azure eseguire questo comando per creare una risorsa dell'app Web per lo strumento di etichettatura di esempio:From the Azure CLI, run this command to create a web app resource for the sample labeling tool: 

```azurecli
DNS_NAME_LABEL=aci-demo-$RANDOM

az container create \
  --resource-group <resorunce_group_name> \
  --name <name> \
  --image mcr.microsoft.com/azure-cognitive-services/custom-form/labeltool \
  --ports 3000 \
  --dns-name-label $DNS_NAME_LABEL \
  --location <region name> \
  --cpu 2 \
  --memory 8
  --command-line "./run.sh eula=accept"
```

### <a name="connect-to-azure-ad-for-authorization"></a>Connettersi ad Azure AD per l'autorizzazioneConnect to Azure AD for authorization

È consigliabile connettere l'app Web ad Azure Active Directory. In questo modo solo gli utenti con credenziali valide possono accedere e usare l'app Web. Seguire le istruzioni in [Configurare l'app del servizio app](https://docs.microsoft.com/azure/app-service/configure-authentication-provider-aad) per connettersi ad Azure Active Directory.Follow the instructions in Configure your App Service app to connect to Azure Active Directory.

## <a name="open-source-on-github"></a>Open source on GitHub

Lo strumento di etichettatura dei moduli OCR è disponibile anche come progetto open source su GitHub. Si tratta di un'applicazione Web creata con React + Redux e scritta in TypeScript. Per altre informazioni o per contribuire, vedere [OCR Form Labeling Tool](https://github.com/microsoft/OCR-Form-Tools/blob/master/README.md).

## <a name="next-steps"></a>Passaggi successivi

Usare la Guida introduttiva [Al treno con etichette](./quickstarts/label-tool.md) per informazioni su come usare lo strumento per etichettare manualmente i dati di training ed eseguire l'apprendimento supervisionato.
