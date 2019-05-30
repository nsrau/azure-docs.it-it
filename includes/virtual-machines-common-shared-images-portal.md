---
title: File di inclusione
description: File di inclusione
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 04/29/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 291ec651061b7a8a3ea3c0645a6bd6581d529ef6
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/27/2019
ms.locfileid: "66244991"
---
## <a name="sign-in-to-azure"></a>Accedi ad Azure 

Accedere al portale di Azure all'indirizzo https://portal.azure.com.

> [!NOTE]
> Se è registrata per l'uso di raccolte di immagini condivise durante l'anteprima, si potrebbe essere necessario registrare nuovamente il `Microsoft.Compute` provider. Aprire [Cloud Shell](https://shell.azure.com/bash) e digitare: `az provider register -n Microsoft.Compute`

## <a name="create-an-image-gallery"></a>Creare un raccolta di immagini

Una raccolta di immagini è la risorsa principale usata per l'abilitazione della condivisione di immagini. I caratteri consentiti per i nomi delle raccolte sono lettere maiuscole o minuscole, numeri e punti. Il nome della raccolta non può contenere trattini.  I nomi di raccolta devono essere univoci all'interno della sottoscrizione. 

L'esempio seguente crea una raccolta denominata *myGallery* nel gruppo di risorse *myGalleryRG*.

1. Selezionare **Crea risorsa** nell'angolo superiore sinistro del portale di Azure.
1. Usare il tipo **raccolta immagini condivise** nella casella di ricerca e selezionare **raccolta immagini condivise** nei risultati.
1. Nel **raccolta di immagini condivise** pagina, fare clic su **crea**.
1. Selezionare la sottoscrizione corretta.
1. Nelle **gruppo di risorse**, selezionare **Crea nuovo** e digitare *myGalleryRG* per il nome.
1. Nelle **Name**, digitare *myGallery* per il nome della raccolta.
1. Il valore predefinito per **regione**.
1. È possibile digitare una breve descrizione della raccolta, ad esempio *la raccolta di immagini per il test.* e quindi fare clic su **revisione + Crea**.
1. Dopo la convalida ha esito positivo, selezionare **Create**.
1. Al termine della distribuzione, selezionare **Vai alla risorsa**.
   
## <a name="create-an-image-definition"></a>Creare una definizione dell'immagine 

Le definizioni di immagini crea un raggruppamento logico per le immagini. Vengono utilizzati per gestire le informazioni sulle versioni di immagini che vengono creati in esse contenute. I nomi definizione di immagine possono essere costituiti da periodi, cifre, punti, trattini e lettere maiuscole o minuscole. Per altre informazioni sui valori è possibile specificare per una definizione dell'immagine, vedere [le definizioni di immagine](https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries#image-definitions).

Creare la definizione dell'immagine della raccolta all'interno della raccolta. In questo esempio, l'immagine della raccolta è denominata *myImageDefinition*.

1. Nella pagina per la nuova raccolta di immagini, selezionare **aggiungere una nuova definizione di immagine** dalla parte superiore della pagina. 
1. Per la **nome dell'immagine definition**, digitare *myImageDefinition*.
1. Per la **sistema operativo**, selezionare l'opzione corretta in base l'immagine di origine.
1. Per la **server di pubblicazione**, digitare *myPublisher*. 
1. Per la **offrono**, digitare *myOffer*.
1. Per la **SKU**, digitare *mySKU*.
1. Al termine, selezionare **revisione + Crea**.
1. Dopo la definizione dell'immagine supera la convalida, selezionare **Create**.
1. Al termine della distribuzione, selezionare **Vai alla risorsa**.


## <a name="create-an-image-version"></a>Creare una versione di immagine

Creare una versione di immagine da un'immagine gestita. In questo esempio la versione dell'immagine è *1.0.0* e viene replicata nei datacenter degli *Stati Uniti centrali* e degli *Stati Uniti centro-meridionali*. Quando si scelgono le aree di destinazione per la replica, tenere presente che anche necessario includere il *origine* area come destinazione per la replica.

I caratteri consentiti per le versioni delle immagini sono numeri e punti. I numeri devono essere compresi nell'intervallo di un valore Integer a 32 bit. Formato: *MajorVersion*.*MinorVersion*.*Patch*.

1. Nella pagina per la definizione dell'immagine, selezionare **Aggiungi versione** dalla parte superiore della pagina.
1. Nelle **regione**, selezionare l'area in cui è archiviato l'immagine gestita. Dovranno essere creati nella stessa area dell'immagine gestiti da che vengono creati versioni dell'immagine.
1. Per la **Name**, digitare *1.0.0*. Il nome di versione dell'immagine deve seguire *principali*. *minori*. *patch* formattare con numeri interi. 
1. Nelle **immagine di origine**, selezionare l'immagine gestita di origine dall'elenco a discesa.
1. Nelle **Escludi dal più recente**, lasciare il valore predefinito *No*.
1. Per la **fine della vita**, selezionare una data dal calendario di un paio di mesi in futuro.
1. Nelle **replica**, lasciare il **replica numero predefinito di** come 1. È necessario eseguire la replica nell'area di origine, quindi lasciare la replica prima come predefinita e quindi selezionare una seconda area di replica sia *Stati Uniti orientali*.
1. Al termine, selezionare **revisione + Crea**. Azure verrà convalidata la configurazione.
1. Quando si passa la convalida versione dell'immagine, selezionare **Create**.
1. Al termine della distribuzione, selezionare **Vai alla risorsa**.

Può richiedere un po' di tempo per replicare l'immagine in tutte le aree di destinazione.

## <a name="share-the-gallery"></a>Condividere la raccolta

È consigliabile che si condivide l'accesso a livello di raccolta di immagini. Di seguito illustra la condivisione della raccolta appena creato.

1. Aprire il [portale di Azure](https://portal.azure.com).
1. Nel menu a sinistra, selezionare **gruppi di risorse**. 
1. Nell'elenco dei gruppi di risorse, selezionare **myGalleryRG**. Verrà aperto il pannello del gruppo di risorse.
1. Nel menu a sinistra del **myGalleryRG** pagina, selezionare **controllo di accesso (IAM)** . 
1. Sotto **aggiungere un'assegnazione di ruolo**, selezionare **Add**. Il **aggiungere un'assegnazione di ruolo** riquadro viene aperto. 
1. Sotto **ruolo**, selezionare **lettore**.
1. Sotto **assegna accesso a**, lasciare il valore predefinito **utente, gruppo o entità servizio Azure AD**.
1. Sotto **seleziona**, digitare l'indirizzo di posta elettronica della persona che si desidera invitare.
1. Se l'utente è all'esterno dell'organizzazione, si verrà visualizzato il messaggio **questo utente verrà inviato un messaggio di posta elettronica che consente loro di collaborare con Microsoft.** Selezionare l'utente con l'indirizzo di posta elettronica e quindi fare clic su **salvare**.

Se l'utente è all'esterno dell'organizzazione, si otterrà un invito tramite posta elettronica a unirsi all'organizzazione. L'utente dovrà accettare l'invito, quindi saranno in grado di visualizzare la raccolta e tutte le definizioni di immagini e versioni nei rispettivi elenchi di risorse.

