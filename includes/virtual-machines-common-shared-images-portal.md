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
ms.sourcegitcommit: bc193bc4df4b85d3f05538b5e7274df2138a4574
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/10/2019
ms.locfileid: "73903656"
---
## <a name="sign-in-to-azure"></a>Accedere ad Azure 

Accedere al portale di Azure all'indirizzo https://portal.azure.com.

> [!NOTE]
> Se è stata eseguita la registrazione per usare le raccolte di immagini condivise durante l'anteprima, potrebbe essere necessario registrare nuovamente il provider di `Microsoft.Compute`. Aprire [cloud Shell](https://shell.azure.com/bash) e digitare: `az provider register -n Microsoft.Compute`

## <a name="create-an-image-gallery"></a>Creare un raccolta di immagini

Una raccolta di immagini è la risorsa principale usata per l'abilitazione della condivisione di immagini. I caratteri consentiti per i nomi delle raccolte sono lettere maiuscole o minuscole, numeri e punti. Il nome della raccolta non può contenere trattini.  I nomi di raccolta devono essere univoci all'interno della sottoscrizione. 

L'esempio seguente crea una raccolta denominata *myGallery* nel gruppo di risorse *myGalleryRG*.

1. Selezionare **Crea risorsa** nell'angolo superiore sinistro del portale di Azure.
1. Usare il tipo **raccolta immagini condivise** nella casella di ricerca e selezionare **raccolta immagini condivise** nei risultati.
1. Nella pagina **raccolta immagini condivise** fare clic su **Crea**.
1. Selezionare la sottoscrizione corretta.
1. In **gruppo di risorse**selezionare **Crea nuovo** e digitare *myGalleryRG* per nome.
1. In **nome** *digitare raccolta* per il nome della raccolta.
1. Lasciare l'impostazione predefinita per **Region**.
1. È possibile digitare una breve descrizione della raccolta, ad esempio la *raccolta immagini per i test.* e quindi fare clic su **Verifica + crea**.
1. Al termine della convalida, selezionare **Crea**.
1. Al termine della distribuzione, selezionare **Vai alla risorsa**.
   
## <a name="create-an-image-definition"></a>Creare una definizione dell'immagine 

Le definizioni di immagine creano un raggruppamento logico per le immagini. Vengono utilizzati per gestire le informazioni sulle versioni delle immagini create al suo interno. I nomi delle definizioni di immagine possono essere costituiti da lettere maiuscole o minuscole, cifre, punti, trattini e punti. Per ulteriori informazioni sui valori che è possibile specificare per la definizione di un'immagine, vedere [definizioni di immagine](https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries#image-definitions).

Creare la definizione dell'immagine della raccolta all'interno della raccolta. In questo esempio l'immagine della raccolta è denominata *myImageDefinition*.

1. Nella pagina relativa alla nuova raccolta immagini selezionare **Aggiungi nuova definizione immagine** nella parte superiore della pagina. 
1. Per **nome definizione immagine**digitare *myImageDefinition*.
1. Per **sistema operativo**, selezionare l'opzione corretta in base alla VM di origine.
1. Per la **generazione di macchine virtuali**, selezionare l'opzione in base alla VM di origine. Nella maggior parte dei casi, si tratta di *generazione 1*. Per altre informazioni, vedere [supporto per le macchine virtuali di seconda generazione](https://docs.microsoft.com/azure/virtual-machines/windows/generation-2).
1. Per **lo stato del sistema operativo**, selezionare l'opzione in base alla VM di origine. Per ulteriori informazioni, vedere [generalizzato e specializzato](../articles/virtual-machines/linux/shared-image-galleries.md#generalized-and-specialized-images).
1. Per **Publisher digitare Server**di *pubblicazione*. 
1. Per l' **offerta**, digitare " *offerta*".
1. Per **SKU**, digitare *mySKU*.
1. Al termine, selezionare **Verifica + crea**.
1. Dopo che la definizione dell'immagine ha superato la convalida, selezionare **Crea**.
1. Al termine della distribuzione, selezionare **Vai alla risorsa**.


## <a name="create-an-image-version"></a>Creare una versione di immagine

Creare una versione dell'immagine da un'immagine gestita. In questo esempio la versione dell'immagine è *1.0.0* e viene replicata nei datacenter degli *Stati Uniti centrali* e degli *Stati Uniti centro-meridionali*. Quando si scelgono le aree di destinazione per la replica, tenere presente che è necessario includere anche l'area di *origine* come destinazione per la replica.

I caratteri consentiti per le versioni delle immagini sono numeri e punti. I numeri devono essere compresi nell'intervallo di un valore Integer a 32 bit. Formato: *MajorVersion*. *MinorVersion*. *Patch*.

I passaggi per la creazione di una versione di immagine sono leggermente diversi, a seconda che l'origine sia un'immagine generalizzata o uno snapshot di una macchina virtuale specializzata. 

### <a name="option-generalized"></a>Opzione: generalizzata

1. Nella pagina per la definizione dell'immagine selezionare **Aggiungi versione** nella parte superiore della pagina.
1. In **Region (area**) selezionare l'area in cui è archiviata l'immagine gestita. È necessario creare le versioni delle immagini nella stessa area dell'immagine gestita da cui vengono create.
1. Per **nome**Digitare *1.0.0*. Il nome della versione dell'immagine deve seguire la *principale*. *minore*. formato della *patch* usando numeri interi. 
1. In **immagine di origine**selezionare l'immagine gestita di origine dall'elenco a discesa.
1. In **Escludi dal più recente**lasciare il valore predefinito *No*.
1. Per la **Data di fine della vita**, selezionare una data del calendario che risale a un paio di mesi in futuro.
1. In **replica**lasciare il **numero di repliche predefinito** pari a 1. È necessario eseguire la replica nell'area di origine, quindi lasciare la prima replica come predefinita e quindi selezionare una seconda area di replica come *Stati Uniti orientali*.
1. Al termine, selezionare **Rivedi e crea**. Azure convaliderà la configurazione.
1. Quando la versione dell'immagine supera la convalida, selezionare **Crea**.
1. Al termine della distribuzione, selezionare **Vai alla risorsa**.

La replica dell'immagine in tutte le aree di destinazione può richiedere del tempo.

### <a name="option-specialized"></a>Opzione: specializzata

1. Nella pagina per la definizione dell'immagine selezionare **Aggiungi versione** nella parte superiore della pagina.
1. In **Region (area**) selezionare l'area in cui è archiviato lo snapshot. È necessario creare le versioni dell'immagine nella stessa area dell'origine da cui vengono create.
1. Per **nome**Digitare *1.0.0*. Il nome della versione dell'immagine deve seguire la *principale*. *minore*. formato della *patch* usando numeri interi. 
1. In **snapshot del disco del sistema operativo**selezionare lo snapshot dalla VM di origine dall'elenco a discesa. Se la macchina virtuale di origine ha un disco dati che si vuole includere, selezionare il numero di **lun** corretto dall'elenco a discesa e quindi selezionare lo snapshot del disco dati per lo **snapshot del disco dati**. 
1. In **Escludi dal più recente**lasciare il valore predefinito *No*.
1. Per la **Data di fine della vita**, selezionare una data del calendario che risale a un paio di mesi in futuro.
1. In **replica**lasciare il **numero di repliche predefinito** pari a 1. È necessario eseguire la replica nell'area di origine, quindi lasciare la prima replica come predefinita e quindi selezionare una seconda area di replica come *Stati Uniti orientali*.
1. Al termine, selezionare **Rivedi e crea**. Azure convaliderà la configurazione.
1. Quando la versione dell'immagine supera la convalida, selezionare **Crea**.
1. Al termine della distribuzione, selezionare **Vai alla risorsa**.

## <a name="share-the-gallery"></a>Condividere la raccolta

Si consiglia di condividere l'accesso a livello di raccolta immagini. Di seguito viene illustrata la condivisione della raccolta appena creata.

1. Aprire il [portale di Azure](https://portal.azure.com).
1. Nel menu a sinistra selezionare **gruppi di risorse**. 
1. Nell'elenco dei gruppi di risorse selezionare **myGalleryRG**. Viene aperto il pannello del gruppo di risorse.
1. Nel menu a sinistra della pagina **myGalleryRG** selezionare **controllo di accesso (IAM)** . 
1. In **Aggiungi un'assegnazione di ruolo**selezionare **Aggiungi**. Viene visualizzato il riquadro **Aggiungi assegnazione ruolo** . 
1. In **ruolo**selezionare **Reader**.
1. In **assegna accesso a**lasciare l'impostazione predefinita **Azure ad utente, gruppo o entità servizio**.
1. In **Seleziona**Digitare l'indirizzo di posta elettronica della persona che si vuole invitare.
1. Se l'utente è all'esterno dell'organizzazione, verrà visualizzato il messaggio **a cui verrà inviato un messaggio di posta elettronica che consente loro di collaborare con Microsoft.** Selezionare l'utente con l'indirizzo di posta elettronica e quindi fare clic su **Salva**.

Se l'utente è all'esterno dell'organizzazione, riceverà un invito tramite posta elettronica per partecipare all'organizzazione. L'utente deve accettare l'invito, quindi potrà visualizzare la raccolta e tutte le definizioni e le versioni di immagine nell'elenco delle risorse.

