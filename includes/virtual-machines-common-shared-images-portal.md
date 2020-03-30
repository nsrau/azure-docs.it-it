---
title: File di inclusione
description: File di inclusione
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 11/06/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 729e757c69887bbdce324e2d8383c970995dc94a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "73903656"
---
## <a name="sign-in-to-azure"></a>Accedere ad Azure 

Accedere al portale di Azure all'indirizzo https://portal.azure.com.

> [!NOTE]
> Se ti sei registrato per utilizzare le raccolte di immagini condivise `Microsoft.Compute` durante l'anteprima, potrebbe essere necessario registrare nuovamente il provider. Aprire [Cloud Shell](https://shell.azure.com/bash) e digitare:`az provider register -n Microsoft.Compute`

## <a name="create-an-image-gallery"></a>Creare un raccolta di immagini

Una raccolta di immagini è la risorsa principale usata per l'abilitazione della condivisione di immagini. I caratteri consentiti per i nomi delle raccolte sono lettere maiuscole o minuscole, numeri e punti. Il nome della raccolta non può contenere trattini.  I nomi di raccolta devono essere univoci all'interno della sottoscrizione. 

L'esempio seguente crea una raccolta denominata *myGallery* nel gruppo di risorse *myGalleryRG*.

1. Selezionare **Crea risorsa** nell'angolo superiore sinistro del portale di Azure.
1. Utilizzare il tipo **Raccolta immagini condivise** nella casella di ricerca e selezionare Raccolta immagini **condivise** nei risultati.
1. Nella pagina **Raccolta immagini condivise** fare clic su **Crea**.
1. Selezionare la sottoscrizione corretta.
1. In **Gruppo di**risorse selezionare Crea **nuovo** e digitare *myGalleryRG* come nome.
1. In **Nome**digitare *myGallery* come nome della raccolta.
1. Lasciare l'impostazione predefinita per **Regione**.
1. È possibile digitare una breve descrizione della raccolta, ad esempio Raccolta immagini per il *test.* , quindi fare clic su **Revisione e creazione**.
1. Al termine della convalida, selezionare **Crea**.
1. Al termine della distribuzione, selezionare **Vai alla risorsa**.
   
## <a name="create-an-image-definition"></a>Creare una definizione dell'immagine 

Le definizioni delle immagini creano un raggruppamento logico per le immagini. Vengono utilizzati per gestire le informazioni sulle versioni dell'immagine create al loro interno. I nomi delle definizioni di immagine possono essere costituiti da lettere maiuscole o minuscole, cifre, punti, trattini e punti. Per ulteriori informazioni sui valori che è possibile specificare per una definizione di immagine, vedere [Definizioni di immagine](https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries#image-definitions).

Creare la definizione dell'immagine della raccolta all'interno della raccolta. In questo esempio l'immagine della raccolta è denominata *myImageDefinition*.

1. Nella pagina della nuova raccolta immagini selezionare **Aggiungi una nuova definizione** di immagine nella parte superiore della pagina. 
1. Per **Nome definizione immagine digitare** *myImageDefinition*.
1. Per **Sistema operativo**selezionare l'opzione corretta in base alla macchina virtuale di origine.
1. Per **la generazione di macchine virtuali,** selezionare l'opzione in base alla macchina virtuale di origine. Nella maggior parte dei casi, questo sarà *Gen 1*. Per altre informazioni, vedere Supporto per macchine virtuali di generazione 2.For more information, see [Support for generation 2 VMs](https://docs.microsoft.com/azure/virtual-machines/windows/generation-2).
1. Per **Stato del sistema operativo**selezionare l'opzione in base alla macchina virtuale di origine. Per ulteriori informazioni, vedere [Generalized and specialized](../articles/virtual-machines/linux/shared-image-galleries.md#generalized-and-specialized-images).
1. Per **Publisher digitare** *myPublisher*. 
1. Per **Offerta**, digitare *myOffer*.
1. Per **SKU**digitare *mySKU*.
1. Al termine, selezionare **Revisione e creazione**.
1. Dopo che la definizione dell'immagine supera la convalida, selezionare **Crea**.
1. Al termine della distribuzione, selezionare **Vai alla risorsa**.


## <a name="create-an-image-version"></a>Creare una versione di immagine

Creare una versione dell'immagine da un'immagine gestita. In questo esempio la versione dell'immagine è *1.0.0* e viene replicata nei datacenter degli *Stati Uniti centro-occidentali* e degli *Stati Uniti centro-meridionali*. Quando si scelgono le aree di destinazione per la replica, tenere presente che è necessario includere anche l'area di *origine* come destinazione per la replica.

I caratteri consentiti per le versioni delle immagini sono numeri e punti. I numeri devono essere compresi nell'intervallo di un valore Integer a 32 bit. Formato: *MajorVersion*. *MinorVersion*. *Patch*.

I passaggi per la creazione di una versione dell'immagine sono leggermente diversi, a seconda che l'origine sia un'immagine generalizzata o uno snapshot di una macchina virtuale specializzata. 

### <a name="option-generalized"></a>Opzione: Generalizzata

1. Nella pagina per la definizione dell'immagine, seleziona **Aggiungi versione** nella parte superiore della pagina.
1. In **Regione**selezionare l'area in cui è archiviata l'immagine gestita. Le versioni dell'immagine devono essere create nella stessa area da cui vengono create.
1. In **Nome**digitare *1.0.0*. Il nome della versione dell'immagine deve seguire *major*. *minore*. formato *patch* utilizzando numeri interi. 
1. In **Immagine di origine**selezionare l'immagine gestita di origine dall'elenco a discesa.
1. In **Escludi dall'ultima ,** lasciare il valore predefinito *No*.
1. Per **Data di fine vita**, selezionare una data dal calendario che è un paio di mesi nel futuro.
1. In **Replica**lasciare il **numero di repliche predefinito su** 1. È necessario eseguire la replica nell'area di origine, quindi lasciare la prima replica come predefinita e quindi selezionare una seconda area di replica come *Stati Uniti orientali*.
1. Al termine, selezionare **Revisione e creazione**. Azure convaliderà la configurazione.
1. Quando la versione dell'immagine supera la convalida, selezionare **Crea**.
1. Al termine della distribuzione, selezionare **Vai alla risorsa**.

La replica dell'immagine in tutte le aree di destinazione può richiedere un po' di tempo.

### <a name="option-specialized"></a>Opzione: Specializzata

1. Nella pagina per la definizione dell'immagine, seleziona **Aggiungi versione** nella parte superiore della pagina.
1. In **Area**selezionare l'area in cui è archiviato lo snapshot. Le versioni dell'immagine devono essere create nella stessa area da cui vengono create.
1. In **Nome**digitare *1.0.0*. Il nome della versione dell'immagine deve seguire *major*. *minore*. formato *patch* utilizzando numeri interi. 
1. In **Snapshot disco del sistema operativo**selezionare lo snapshot dalla macchina virtuale di origine dall'elenco a discesa. Se la macchina virtuale di origine include un disco dati da includere, selezionare il numero **LUN** corretto dall'elenco a discesa e quindi selezionare lo snapshot del disco dati per **Snapshot del disco dati**. 
1. In **Escludi dall'ultima ,** lasciare il valore predefinito *No*.
1. Per **Data di fine vita**, selezionare una data dal calendario che è un paio di mesi nel futuro.
1. In **Replica**lasciare il **numero di repliche predefinito su** 1. È necessario eseguire la replica nell'area di origine, quindi lasciare la prima replica come predefinita e quindi selezionare una seconda area di replica come *Stati Uniti orientali*.
1. Al termine, selezionare **Revisione e creazione**. Azure convaliderà la configurazione.
1. Quando la versione dell'immagine supera la convalida, selezionare **Crea**.
1. Al termine della distribuzione, selezionare **Vai alla risorsa**.

## <a name="share-the-gallery"></a>Condividere la galleria

È consigliabile condividere l'accesso a livello di raccolta immagini. Di seguito viene illustrata la condivisione della raccolta appena creata.

1. Aprire il [portale di Azure](https://portal.azure.com).
1. Nel menu a sinistra selezionare **Gruppi di risorse.** 
1. Nell'elenco dei gruppi di risorse selezionare **myGalleryRG**. Verrà aperto il pannello per il gruppo di risorse.
1. Nel menu a sinistra della pagina **myGalleryRG** selezionare **Controllo di accesso (IAM)**. 
1. In **Aggiungi un'assegnazione**di ruolo selezionare **Aggiungi**. Verrà aperto il riquadro **Aggiungi assegnazione ruolo.** 
1. In **Ruolo**selezionare **Lettore**.
1. In **Assegna accesso a**, lasciare l'impostazione predefinita Di Azure AD **utente, gruppo o entità servizio**.
1. In **Seleziona**digitare l'indirizzo di posta elettronica della persona che si desidera invitare.
1. Se l'utente è esterno all'organizzazione, verrà visualizzato il messaggio **Verrà inviato un messaggio di posta elettronica che consente di collaborare con Microsoft.** Selezionare l'utente con l'indirizzo di posta elettronica e quindi fare clic su **Salva**.

Se l'utente si trova all'esterno dell'organizzazione, verrà eseguito un invito tramite posta elettronica a iscriversi all'organizzazione. L'utente deve accettare l'invito, quindi sarà in grado di visualizzare la raccolta e tutte le definizioni e le versioni delle immagini nell'elenco di risorse.

