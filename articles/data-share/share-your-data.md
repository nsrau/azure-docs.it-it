---
title: Esercitazione - Condividere dati con clienti e partner usando Condivisione dati di Azure (anteprima)
description: Esercitazione - Condividere dati con clienti e partner usando Condivisione dati di Azure (anteprima)
author: joannapea
ms.service: data-share
ms.topic: tutorial
ms.date: 07/10/2019
ms.author: joanpo
ms.openlocfilehash: 01888f3656765b922c1b646e7ca8e07d81e799f3
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67838432"
---
# <a name="tutorial-share-your-data-using-azure-data-share-preview"></a>Esercitazione: Condividere dati con Condivisione dati di Azure (anteprima)

In questa esercitazione si apprenderà come configurare una nuova condivisione dati di Azure e iniziare a condividere i dati con clienti e partner all'esterno dell'organizzazione di Azure. 

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Creare una condivisione dati.
> * Aggiungere set di dati alla condivisione dati.
> * Abilitare una pianificazione della sincronizzazione per la condivisione dati. 
> * Aggiungere i destinatari alla condivisione dati. 

## <a name="prerequisites"></a>Prerequisiti

* Sottoscrizione di Azure: Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/) prima di iniziare.
* Account di archiviazione di Azure: se non se ne ha già uno, è possibile creare un [account di archiviazione di Azure](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account).
* L'autorizzazione per aggiungere l'assegnazione di ruolo all'account di archiviazione, inclusa nell'autorizzazione*Microsoft.Authorization/role assignments/write*. Questa autorizzazione è presente nel ruolo proprietario. 
* Indirizzo di posta elettronica per l'accesso ad Azure dei destinatari (gli alias di posta elettronica non funzionano).

## <a name="sign-in-to-the-azure-portal"></a>Accedere al portale di Azure

Accedere al [portale di Azure](https://portal.azure.com/).

## <a name="create-a-data-share-account"></a>Creare un account di condivisione dati

Creare una risorsa di condivisione dati di Azure in un gruppo di risorse di Azure.

1. Selezionare il pulsante **Crea una risorsa** (+) nell'angolo superiore sinistro del portale.

1. Cercare *Condivisione dati*.

1. Selezionare Condivisione dati (anteprima) e quindi **Crea**.

1. Compilare i dettagli di base della risorsa di condivisione dati di Azure con le informazioni seguenti. 

     **Impostazione** | **Valore consigliato** | **Descrizione campo**
    |---|---|---|
    | NOME | *datashareacount* | Specificare un nome per l'account di condivisione dati. |
    | Subscription | Sottoscrizione in uso | Selezionare la sottoscrizione di Azure da usare per l'account di condivisione dati.|
    | Resource group | *test-resource-group* | Usare un gruppo di risorse esistente oppure crearne uno nuovo. |
    | Location | *Stati Uniti orientali 2* | Selezionare un'area per l'account di condivisione dati.
    | | |

1. Selezionare **Crea** per effettuare il provisioning dell'account di condivisione dati. Il completamento del provisioning di un nuovo account di condivisione dati in genere impiega al massimo 2 minuti. 

1. Una volta completata la distribuzione, selezionare **Vai alla risorsa**.

## <a name="create-a-data-share"></a>Creare una condivisione dati

1. Passare alla pagina di panoramica della condivisione dati.

    ![Condividere i dati](./media/share-receive-data.png "Condividere i dati") 

1. Selezionare **Start sharing your data** (Inizia a condividere i dati).

1. Selezionare **Create** (Crea).   

1. Immettere i dettagli della condivisione dati. Specificare un nome, una descrizione del contenuto della condivisione e le condizioni per l'utilizzo (facoltativo). 

    ![EnterShareDetails](./media/enter-share-details.png "Immettere i dettagli della condivisione") 

1. Selezionare **Continua**

1. Per aggiungere set di dati alla condivisione dati, selezionare **Add Datasets** (Aggiungi set di dati). 

    ![Datasets](./media/datasets.png "Set di dati")

1. Selezionare il tipo di set di dati da aggiungere. 

    ![AddDatasets](./media/add-datasets.png "Aggiungere i set di dati")    

1. Passare all'oggetto che si vuole condividere e selezionare "Add Datasets" (Aggiungi set di dati). 

    ![SelectDatasets](./media/select-datasets.png "Selezionare i set di dati")    

1. Nella scheda Destinatari immettere l'indirizzo di posta elettronica del consumer di dati selezionando "+ Aggiungi il destinatario". 

    ![AddRecipients](./media/add-recipient.png "Aggiungere i destinatari") 

1. Selezionare **Continua**

1. Se si vuole consentire al consumer di dati di ottenere gli aggiornamenti incrementali dei dati, abilitare la pianificazione degli snapshot. 

    ![EnableSnapshots](./media/enable-snapshots.png "Abilitare gli snapshot") 

1. Selezionare un'ora di inizio e un intervallo di ricorrenza. 

1. Selezionare **Continua**

1. Nella scheda Rivedi e crea verificare il contenuto del pacchetto, le impostazioni, i destinatari e le impostazioni di sincronizzazione. Selezionare **Crea**

La condivisione dati di Azure è stata creata e il suo destinatario è ora pronto ad accettare l'invito. 

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è appreso come creare una condivisione dati di Azure e invitare destinatari. Per informazioni su come un consumer di dati può accettare e ricevere una condivisione dati, continuare con l'esercitazione [Accettare e ricevere dati](subscribe-to-data-share.md). 