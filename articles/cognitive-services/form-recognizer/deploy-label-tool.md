---
title: Come distribuire lo strumento di assegnazione di etichette di esempio per il form Recognizer
titleSuffix: Azure Cognitive Services
description: Informazioni sui diversi modi in cui è possibile distribuire lo strumento di assegnazione di etichette di esempio di form Recognizer per facilitare l'apprendimento supervisionato.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: how-to
ms.date: 04/14/2020
ms.author: pafarley
ms.openlocfilehash: df800938d568af0b94cfb1d368ef32e9b085b6eb
ms.sourcegitcommit: d76108b476259fe3f5f20a91ed2c237c1577df14
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2020
ms.locfileid: "92913110"
---
# <a name="deploy-the-sample-labeling-tool"></a>Distribuire lo strumento di etichettatura di esempio

Il form Recognizer Sample label Tool è un'applicazione che fornisce un'interfaccia utente semplice, che è possibile usare per etichettare manualmente i moduli (documenti) allo scopo dell'apprendimento supervisionato. In questo articolo verranno forniti collegamenti e istruzioni che illustrano come:

* [Eseguire lo strumento di assegnazione di etichette di esempio localmente](#run-the-sample-labeling-tool-locally)
* [Distribuire lo strumento di assegnazione di etichette di esempio in un'istanza di contenitore di Azure (ACI)](#deploy-with-azure-container-instances-aci)
* [Usare e contribuire allo strumento per l'assegnazione di etichette dei moduli OCR Open Source](#open-source-on-github)

## <a name="run-the-sample-labeling-tool-locally"></a>Eseguire lo strumento di assegnazione di etichette di esempio localmente

Il modo più rapido per avviare l'assegnazione di etichette ai dati consiste nell'eseguire lo strumento di assegnazione di etichette di esempio in locale. La Guida introduttiva seguente usa l'API REST del sistema di riconoscimento dei moduli e lo strumento di assegnazione di etichette di esempio per eseguire il training di un modello personalizzato con dati con etichetta manuale. 

* [Guida introduttiva: etichettare moduli, eseguire il training di un modello e analizzare un modulo usando lo strumento di assegnazione di etichette di esempio](./quickstarts/label-tool.md).

## <a name="deploy-with-azure-container-instances-aci"></a>Distribuire con istanze di contenitore di Azure (ACI)

Prima di iniziare, è importante tenere presente che esistono due modi per distribuire lo strumento di assegnazione di etichette di esempio in un'istanza di contenitore di Azure (ACI). Entrambe le opzioni vengono usate per eseguire lo strumento di assegnazione di etichette di esempio con ACI: 

* [Uso del portale di Azure](#azure-portal)
* [Con l'interfaccia della riga di comando di Azure](#azure-cli)

### <a name="azure-portal"></a>Portale di Azure

Seguire questa procedura per creare una nuova risorsa usando il portale di Azure: 

1. Accedere al [portale di Azure](https://portal.azure.com/signin/index/).
2. Selezionare **Crea una risorsa** . 
3. Quindi selezionare **app Web** . 

   > [!div class="mx-imgBorder"]
   > ![Selezionare l'app Web](./media/quickstarts/formre-create-web-app.png)
   
4. Assicurarsi prima di tutto che sia selezionata la scheda **nozioni di base** . A questo punto, sarà necessario fornire alcune informazioni: 

   > [!div class="mx-imgBorder"]
   > ![Seleziona nozioni di base](./media/quickstarts/formre-select-basics.png)
   * Sottoscrizione: selezionare una sottoscrizione di Azure esistente
   * Gruppo di risorse: è possibile riusare un gruppo di risorse esistente o crearne uno nuovo per questo progetto. È consigliabile creare un nuovo gruppo di risorse.
   * Nome: assegnare un nome all'app Web. 
   * Publish-selezionare il **contenitore Docker**
   * Sistema operativo-selezionare **Linux**
   * Area: scegliere un'area che abbia senso per l'utente.
   * Piano Linux: selezionare un piano tariffario/piano per il servizio app. 

   > [!div class="mx-imgBorder"]
   > ![Configurare l'app Web](./media/quickstarts/formre-select-docker-linux.png)

5. Selezionare quindi la scheda **Docker** . 

   > [!div class="mx-imgBorder"]
   > ![Selezionare Docker](./media/quickstarts/formre-select-docker.png)

6. A questo punto è possibile configurare il contenitore docker. Tutti i campi sono obbligatori se non diversamente specificato:

    # <a name="v20"></a>[v2.0](#tab/v2-0)  
   * Opzioni-selezionare un **singolo contenitore**
   * Origine immagine-selezionare il **Registro di sistema privato** 
   * URL server: impostare questa impostazione su `https://mcr.microsoft.com`
   * Username (facoltativo): creare un nome utente. 
   * Password (facoltativo): creare una password sicura da ricordare.
   * Image e Tag: impostare questa impostazione su `mcr.microsoft.com/azure-cognitive-services/custom-form/labeltool:latest`
   * Distribuzione continua: impostare questa impostazione **su on** se si desidera ricevere aggiornamenti automatici quando il team di sviluppo apporta modifiche allo strumento di assegnazione di etichette di esempio.
   * Comando di avvio: impostare questa impostazione su `./run.sh eula=accept`

    # <a name="v21-preview"></a>[v2.1.preview](#tab/v2-1) 
   * Opzioni-selezionare un **singolo contenitore**
   * Origine immagine-selezionare il **Registro di sistema privato** 
   * URL server: impostare questa impostazione su `https://mcr.microsoft.com`
   * Username (facoltativo): creare un nome utente. 
   * Password (facoltativo): creare una password sicura da ricordare.
   * Image e Tag: impostare questa impostazione su `mcr.microsoft.com/azure-cognitive-services/custom-form/labeltool:2.1.012970002-amd64-preview`
   * Distribuzione continua: impostare questa impostazione **su on** se si desidera ricevere aggiornamenti automatici quando il team di sviluppo apporta modifiche allo strumento di assegnazione di etichette di esempio.
   * Comando di avvio: impostare questa impostazione su `./run.sh eula=accept`
    
    ---

   > [!div class="mx-imgBorder"]
   > ![Configurare Docker](./media/quickstarts/formre-configure-docker.png)

7. È tutto. Selezionare quindi **Verifica + crea** e quindi **Crea** per distribuire l'app Web. Al termine, è possibile accedere all'app Web nell'URL fornito nella **Panoramica** della risorsa.

> [!NOTE]
> Quando si crea l'app Web, è anche possibile configurare l'autorizzazione/autenticazione. Questa operazione non è necessaria per iniziare. 

### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

In alternativa all'uso del portale di Azure, è possibile creare una risorsa usando l'interfaccia della riga di comando di Azure. Prima di continuare, è necessario installare l'interfaccia della riga di comando di [Azure](/cli/azure/install-azure-cli). È possibile ignorare questo passaggio se si sta già lavorando con l'interfaccia della riga di comando di Azure. 

È necessario conoscere alcuni aspetti di questo comando:

* `DNS_NAME_LABEL=aci-demo-$RANDOM` genera un nome DNS casuale. 
* Questo esempio presuppone che si disponga di un gruppo di risorse che è possibile usare per creare una risorsa. Sostituire `<resource_group_name>` con un gruppo di risorse valido associato alla sottoscrizione. 
* È necessario specificare la posizione in cui si vuole creare la risorsa. Sostituire `<region name>` con l'area desiderata per l'app Web. 
* Questo comando accetta automaticamente il contratto di licenza.

Dall'interfaccia della riga di comando di Azure eseguire questo comando per creare una risorsa dell'app Web per lo strumento di assegnazione di etichette di esempio: 


# <a name="v20"></a>[v2.0](#tab/v2-0)   
```azurecli
DNS_NAME_LABEL=aci-demo-$RANDOM

az container create \
  --resource-group <resource_group_name> \
  --name <name> \
  --image mcr.microsoft.com/azure-cognitive-services/custom-form/labeltool \
  --ports 3000 \
  --dns-name-label $DNS_NAME_LABEL \
  --location <region name> \
  --cpu 2 \
  --memory 8 \
  --command-line "./run.sh eula=accept"
``` 
# <a name="v21-preview"></a>[v2.1.preview](#tab/v2-1)    
```azurecli
DNS_NAME_LABEL=aci-demo-$RANDOM

az container create \
  --resource-group <resource_group_name> \
  --name <name> \
  --image mcr.microsoft.com/azure-cognitive-services/custom-form/labeltool:2.1.012970002-amd64-preview \
  --ports 3000 \
  --dns-name-label $DNS_NAME_LABEL \
  --location <region name> \
  --cpu 2 \
  --memory 8 \
  --command-line "./run.sh eula=accept"
```

---

### <a name="connect-to-azure-ad-for-authorization"></a>Connetti a Azure AD per l'autorizzazione

È consigliabile connettere l'app Web a Azure Active Directory. In questo modo si garantisce che solo gli utenti con credenziali valide possano accedere e usare l'app Web. Seguire le istruzioni riportate in [configurare l'app del servizio app](../../app-service/configure-authentication-provider-aad.md) per connettersi a Azure Active Directory.

## <a name="open-source-on-github"></a>Open Source su GitHub

Lo strumento per l'assegnazione di etichette dei moduli OCR è disponibile anche come progetto open source su GitHub. Si tratta di un'applicazione Web creata con React + Redux e scritta in TypeScript. Per altre informazioni o per contribuire, vedere [OCR Form Labeling Tool](https://github.com/microsoft/OCR-Form-Tools/blob/master/README.md).

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come usare lo strumento per etichettare manualmente i dati di training ed eseguire l'apprendimento supervisionato, usare la Guida introduttiva [con le etichette](./quickstarts/label-tool.md) .