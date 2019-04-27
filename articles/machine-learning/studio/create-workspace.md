---
title: Creare un'area di lavoro di Machine Learning Studio
titleSuffix: Azure Machine Learning Studio
description: Per usare Azure Machine Learning Studio, deve essere presente un'area di lavoro di Machine Learning Studio. Quest'area di lavoro contiene tutti gli strumenti necessari per la creazione, la gestione e la pubblicazione di esperimenti.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: seodec18
ms.date: 12/07/2017
ms.openlocfilehash: 7aeee4f24f6c7133ad978bc0c6c7fb8853bc4c35
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "62109349"
---
# <a name="create-and-share-an-azure-machine-learning-studio-workspace"></a>Creare e condividere un'area di lavoro di Azure Machine Learning Studio

Per usare Azure Machine Learning Studio, deve essere presente un'area di lavoro di Machine Learning Studio. Quest'area di lavoro contiene tutti gli strumenti necessari per la creazione, la gestione e la pubblicazione di esperimenti.

## <a name="create-a-studio-workspace"></a>Creare un'area di lavoro di Studio

1. Accedere al [portale di Azure](https://portal.azure.com/)

    > [!NOTE]
    > Per accedere e creare un'area di lavoro di Studio è necessario il ruolo di amministratore di una sottoscrizione di Azure. 
    >
    > 

2. Fare clic su **+Nuovo**

3. Nella casella di ricerca, digitare **Area di lavoro di Machine Learning Studio** e selezionare l'elemento corrispondente. Selezionare quindi **Crea** nella parte inferiore della pagina.

4. Immettere le informazioni sull'area di lavoro:

   - Il *nome dell'area di lavoro* può contenere al massimo 260 caratteri e non può terminare con uno spazio. Il nome non può includere questi caratteri: `< > * % & : \ ? + /`
   - Se si distribuiscono i servizi Web da quest'area di lavoro, vengono usati il *piano di servizio Web* che si sceglie (o si crea) e il *piano tariffario* selezionato.

     ![Creare una nuova area di lavoro di Studio](./media/create-workspace/create-new-workspace.png)

5. Fare clic su **Create**(Crea).

> [!NOTE]
> Machine Learning Studio si basa su un account di archiviazione di Azure specificato dall'utente per salvare i dati intermedi quando esegue il flusso di lavoro. Dopo la creazione dell'area di lavoro, se l'account di archiviazione viene eliminato o se le chiavi di accesso vengono cambiate, l'area di lavoro non funzionerà più e tutti gli esperimenti al suo interno avranno esito negativo.
Se si elimina accidentalmente l'account di archiviazione, ricrearlo con lo stesso nome e nella stessa area dell'account di archiviazione eliminato e risincronizzare la chiave di accesso. Se sono state modificate le chiavi di accesso dell'account di archiviazione, sincronizzarle di nuovo nell'area di lavoro usando il portale di Azure.

Dopo aver distribuito l'area di lavoro è possibile aprirla in Machine Learning Studio.

1. Accedere a Machine Learning Studio all'indirizzo[https://studio.azureml.net/](https://studio.azureml.net/).

2. Selezionare l'area di lavoro nell'angolo in alto a destra.

    ![Selezionare l'area di lavoro](./media/create-workspace/open-workspace.png)

3. Fare clic su **esperimenti personali**.

    ![Aprire gli esperimenti](./media/create-workspace/my-experiments.png)

Per informazioni sulla gestione dell'area di lavoro di Studio, vedere [Gestire un'area di lavoro di Azure Machine Learning Studio](manage-workspace.md).
Se si verifica un problema durante la creazione dell'area di lavoro, vedere [Guida per la risoluzione dei problemi: Creare e connettersi a un'area di lavoro di Machine Learning Studio](troubleshooting-creating-ml-workspace.md).


## <a name="share-an-azure-machine-learning-studio-workspace"></a>Condividere un'area di lavoro di Azure Machine Learning Studio
Dopo la creazione di un'area di lavoro di Machine Learning Studio, è possibile invitare utenti nell'area di lavoro per condividere l'accesso a questa e agli esperimenti, ai set di dati, ai notebook e così via al suo interno. È possibile aggiungere gli utenti in due ruoli:

* **Utente**: un utente dell'area di lavoro può creare, aprire, modificare ed eliminare esperimenti, dataset e così via nell'area di lavoro.
* **Proprietario**: un proprietario può invitare e rimuovere gli utenti dell'area di lavoro in aggiunta alle operazioni consentite a un utente.

> [!NOTE]
> L'account amministratore che crea l'area di lavoro viene aggiunto automaticamente all'area di lavoro come proprietario. Agli altri amministratori o utenti della sottoscrizione non viene invece concesso automaticamente l'accesso all'area di lavoro, ma è necessario inviare loro un invito in modo esplicito.
> 
> 

### <a name="to-share-a-studio-workspace"></a>Per condividere un'area di lavoro di Studio

1. Accedere a Machine Learning Studio all'indirizzo [https://studio.azureml.net/Home](https://studio.azureml.net/Home).

2. Nel pannello sinistro fare clic su **IMPOSTAZIONI**

3. Fare clic sulla scheda **UTENTI**

4. Fare clic su **INVITE MORE USERS (INVITA ALTRI UTENTI)** in fondo alla pagina

    ![Impostazioni di Studio](./media/create-workspace/settings.png)

5. Immettere uno o più indirizzi di posta elettronica. L'utente ha bisogno di un account Microsoft o di un account aziendale valido (da Azure Active Directory).

6. Scegliere se si desidera aggiungere l'utente come proprietario o utente.

7. Fare clic sul segno di spunta **OK**.

Ogni utente aggiunto riceverà un messaggio di posta elettronica con istruzioni per l'accesso all'area di lavoro condivisa.

> [!NOTE]
> Per poter distribuire o gestire i servizi Web nell'area di lavoro, l'utente deve essere un collaboratore o un amministratore nella sottoscrizione di Azure. 



