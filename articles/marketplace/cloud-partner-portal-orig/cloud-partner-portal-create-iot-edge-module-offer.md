---
title: Creare un'offerta di modulo IoT Edge | Microsoft Docs
description: Come pubblicare un nuovo modulo IoT Edge per il Marketplace.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/19/2018
ms.author: pbutlerm
ms.openlocfilehash: bf7d639c682e443f29b31b3c6d7438e89c406fde
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/04/2018
ms.locfileid: "52838634"
---
# <a name="how-to-publish-a-new-iot-edge-module-in-the-cloud-partner-portal"></a>Come pubblicare un nuovo modulo IoT Edge nel portale Cloud Partner

Questo articolo descrive i passaggi per la pubblicazione di una nuova offerta di modulo IoT Edge.

## <a name="prerequisites"></a>Prerequisiti

I seguenti prerequisiti si applicano alla pubblicazione di un modulo IoT Edge per Azure Marketplace.

-   Accedere al [portale Cloud Partner (CPP)](https://cloudpartner.azure.com/#alloffers). Per altre informazioni, consultare la [guida alla pubblicazione](https://docs.microsoft.com/azure/marketplace/marketplace-publishers-guide).

-   Ospitare il modulo IoT Edge in un Registro contenitori di Azure.

-   Avere pronti i metadati del modulo IoT Edge (incluso l'elenco non completo):

    -   Title

    -   Descrizione (formato HTML di base)

    -   Logo in formato di immagine png nelle dimensioni seguenti: 40 x 40 pixel, 90 x 90 pixel, 115 x 115 pixel, 255 x 115 pixel.

    -   Condizioni per l'utilizzo e informativa sulla privacy

<a name="prepare-your-iot-edge-module-listing-in-cpp"></a>Preparare l'inserzione del modulo IoT Edge in CPP
-------------------------------------------

### <a name="create-a-new-offer-of-the-type-iot-edge-module"></a>Creare una nuova offerta del tipo di modulo IoT Edge 

Seguire questa procedura per preparare l'inserzione di modulo IoT Edge:

-   Accedere all'[account CPP](https://cloudpartner.azure.com/).

>[!Note]
>Per informazioni generali sul portale Cloud Partner, è possibile controllare le [informazioni sulla documentazione](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-getting-started-with-the-cloud-partner-portal)

-   Selezionare **Nuova offerta**, quindi **Modulo IoT Edge**.

>[!NOTE]
>Un modulo IoT Edge è un contenitore che è progettato appositamente per l'esecuzione su IoT Edge. Gli scenari interessati da un modulo IoT Edge devono essere utili in un contesto di IoT Edge. Include inoltre le impostazioni di configurazione predefinite per rendere la distribuzione su un dispositivo IoT Edge semplice e immediata. Il contenitore può includere anche lo SDK di un modulo IoT Edge per consentire la comunicazione con l'edgeHub e hub IoT.

### <a name="define-your-offer-settings"></a>Definire le impostazioni dell'offerta

Nella scheda Impostazioni dell'offerta, immettere le informazioni per l'offerta.

![Identità dell'offerta](./media/cloud-partner-portal-create-iot-edge-module-offer/offer-identity.png)


-   L'**ID offerta** identifica l'offerta in CPP in modo univoco e può essere usata negli URL destinati ai clienti.

-   Il **Nome** è visualizzabile dall'utente solo come riferimento di questa offerta in CPP.

### <a name="create-one-or-more-skus"></a>Creare uno o più SKU

Ogni SKU corrisponde a un'immagine del contenitore. È necessario usare almeno uno SKU ed è possibile aggiungerne più di uno. Uno SKU è composto da due parti:

-   Metadati dello SKU

-   Metadati del contenitore

**Per creare uno SKU:**

Selezionare la scheda **SKU**, quindi **Nuovo SKU**.

![Nuovo SKU](./media/cloud-partner-portal-create-iot-edge-module-offer/SKUs.png)

I metadati SKU contengono i campi obbligatori seguenti:
- ID SKU - identificatore univoco.
- Titolo: il titolo SKU, fino a 50 caratteri.
- Riepilogo: una breve descrizione, fino a 100 caratteri.
- Descrizione - una descrizione lunga.
- Nascondi questo SKU - Il valore predefinito è **No**.
   
![Dettagli SKU](./media/cloud-partner-portal-create-iot-edge-module-offer/SKU-settings.png)

#### <a name="iot-edge-module-metadata-and-the-container-registry"></a>Il registro contenitori e i metadati del modulo IoT Edge

I metadati del modulo IoT Edge includono informazioni di riferimento dell'immagine archiviate in Registro Azure Container. Azure Marketplace consente di copiare l'immagine nel Registro di sistema del marketplace pubblico ed è disponibile per i clienti dopo la certificazione. Tutte le richieste dell'utente di utilizzare un'immagine del modulo IoT Edge vengono gestite dal registro contenitori di Marketplace.

![Immagini del contenitore](./media/cloud-partner-portal-create-iot-edge-module-offer/container-images.png)

**Immagini del contenitore**

I dettagli del Repository di immagini presenta i campi obbligatori seguenti:

-   **ID sottoscrizione**: l'id della sottoscrizione di Azure in cui il Registro ACR è presente.

-   **Nome del gruppo di risorse**: il nome del gruppo di risorse del registro di ACR.

-   **Nome del Registro**: il nome del Registro contenitori di Azure.

-   **Nome del repository**: il nome del repository. Una volta impostato, questo valore non può più essere modificato. Il nome deve essere univoco in modo che nessun'altra offerta dell'account abbia lo stesso nome.

-   **Nome utente**: il nome utente associato al Registro contenitori di Azure (nome utente amministratore).

-   **Password**: la password associata al Registro contenitori di Azure.

    >[!Note]
    >Il nome utente e la password sono necessarie per garantire che i partner abbiano accesso all'istanza di Registro Azure Container descritta nel processo di pubblicazione.

Quando si pubblica un'immagine del modulo IoT Edge, è possibile aggiungere uno o più tag di immagine. Assicurarsi di aggiungere un "ultimo" tag (predefinito) al modulo in modo che sia possibile identificare facilmente il modulo durante il test.

È inoltre possibile specificare i seguenti valori del modulo IoT Edge:

-   **createOptions**: createOptions predefinito da passare in modo che questo modulo IoT Edge possa essere avviato immediatamente.

-   **gemello:**: gemello predefinito da passare in modo che questo modulo IoT Edge possa essere avviato immediatamente quando si usa lo SDK del modulo IoT.

-   **route:**: route predefiniti da passare in modo che questo modulo IoT Edge possa essere avviato immediatamente se si usa lo SDK del modulo IoT.

### <a name="describe-your-iot-edge-module-for-your-customers"></a>Descrizione del modulo IoT Edge per i clienti

Nella scheda Marketplace, aggiungere il contenuto di marketing specifico. Queste informazioni costituiscono ciò che sarà visibile pubblicamente ed elencato in Azure Marketplace.

![Panoramica del modulo IoT Edge](./media/cloud-partner-portal-create-iot-edge-module-offer/overview.png)

-   **Titolo**: il titolo del modulo IoT Edge per il pubblico.

-   **Riepilogo**: riepilogo del modulo IoT Edge visibile al pubblico nella maggior parte delle pagine, ad esempio le pagine di navigazione.

-   **Riepilogo lungo**: riepilogo del modulo IoT Edge visibile al pubblico quando viene visualizzato il modulo. 

-   **Descrizione**: la descrizione del modulo IoT Edge visibile al pubblico nella pagina dei dettagli del prodotto (è possibile usare i tag HTML di base per formattare la descrizione).

-   **Identificatore di marketing**: identificatore univoco che viene usato per creare l'URL del prodotto. Questo URL è nel formato seguente: *azuremarketplace.microsoft.com/enus/marketplace/apps/yourpublisherid.youriotedgemodulemarketingidentifier*.

-   **ID di sottoscrizione di anteprima**: gli utenti autorizzati ad accedere a queste sottoscrizioni saranno in grado di visualizzare il modulo IoT Edge dopo aver ottenuto la certificazione e prima che venga pubblicato.

-   **Collegamenti utili**: è possibile aggiungere fino a 10 collegamenti che verranno visualizzati nella pagina dei dettagli del prodotto.

-   **Categorie consigliate**: selezionare fino a cinque categorie. Verranno visualizzati nella pagina dei dettagli del prodotto. Attualmente, tutti i moduli di IoT Edge vengono visualizzati nelle pagine di navigazione nelle categorie *Internet delle cose \> modulo IoT Edge*.

-   **Loghi**: caricare le immagini del logo del modulo IoT Edge nel formato PNG. Usare esattamente le dimensioni seguenti: 40 x 40 pixel, 90 x 90 pixel, 115 x 115 pixel, 255 x 115 pixel.

-   **Screenshot**: gli screenshot vengono visualizzati nella pagina dei dettagli del prodotto. Si tratta di un ottimo metodo per comunicare in modo visivo ciò che fa il modulo IoT Edge e come funziona. Ad esempio, è possibile mostrare diagrammi di architettura o illustrazioni di casi d'uso.

-   **Video**: video che vengono visualizzati nella pagina dei dettagli del prodotto. Si tratta di un ottimo metodo per comunicare in modo visivo ciò che fa il modulo IoT Edge e come funziona.

-   **Gestione dei clienti potenziali**:è possibile scegliere un sistema per raccogliere tutti i clienti potenziali che mostrano interesse nel prodotto.

-   **Privacy**: è necessario disporre di un URL che punta all'informativa sulla privacy.

-   **Condizioni per l'utilizzo**: devono essere previste condizioni per l'utilizzo. È possibile usare i tag HTML per formattare questa pagina o fare in modo che punti a una delle altre pagine.

### <a name="enter-your-support-contact-information"></a>Immettere le informazioni di contatto per l'assistenza

Nella scheda Assistenza, fornire informazioni su **Supporto tecnico** e **Assistenza clienti**.

![Contatti del supporto tecnico](./media/cloud-partner-portal-create-iot-edge-module-offer/support-contact-info.png)


## <a name="certify-your-iot-edge-module"></a>Certificare il modulo IoT Edge

Dopo aver fornito tutte le informazioni necessarie, selezionare **Pubblica** per inviare il modulo IoT Edge e procedere alla certificazione. Si noterà una sequenza temporale che mostra i passaggi della procedura di certificazione.

**Verifica del modulo**

Il modulo viene verificato dal nostro team di certificazione. Dopo che il modulo è stato certificato, si otterrà un collegamento privato per testare il modulo. Se è necessario apportare cambiamenti al termine del test, modificare i metadati dell'offerta e inviare di nuovo il modulo al team di certificazione. 

## <a name="publish-your-iot-edge-module"></a>Pubblicare il modulo IoT Edge

Dopo aver completato il test e si è pronti alla pubblicazione, selezionare **Go Live** per pubblicare il modulo IoT Edge.

>[!Important]
>Se si vuole che il modulo IoT Edge sia annunciato in occasione dell'evento Ignite, il modulo deve essere reso pubblico dal 23/09/2018.
