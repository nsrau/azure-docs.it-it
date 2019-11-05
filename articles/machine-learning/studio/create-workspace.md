---
title: Creare un'area di lavoro di Machine Learning Studio (classica)
titleSuffix: Azure Machine Learning Studio (classic)
description: Per usare Azure Machine Learning Studio (versione classica), è necessario disporre di un'area di lavoro di Machine Learning Studio (classica). Quest'area di lavoro contiene tutti gli strumenti necessari per la creazione, la gestione e la pubblicazione di esperimenti.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: seodec18
ms.date: 12/07/2017
ms.openlocfilehash: 6302ded4ab951d0490b128989a45c41c013db7ea
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73493198"
---
# <a name="create-and-share-an-azure-machine-learning-studio-classic-workspace"></a>Creare e condividere un'area di lavoro di Azure Machine Learning Studio (classica)

Per usare Azure Machine Learning Studio (versione classica), è necessario disporre di un'area di lavoro di Machine Learning Studio (classica). Quest'area di lavoro contiene tutti gli strumenti necessari per la creazione, la gestione e la pubblicazione di esperimenti.

## <a name="create-a-studio-classic-workspace"></a>Creare un'area di lavoro di studio (classica)

1. Accedere al [portale di Azure](https://portal.azure.com/)

    > [!NOTE]
    > Per accedere e creare un'area di lavoro di studio (classica), è necessario essere un amministratore della sottoscrizione di Azure. 
    >
    > 

2. Fare clic su **+Nuovo**

3. Nella casella di ricerca digitare **Machine Learning Studio area di lavoro (classica)** e selezionare l'elemento corrispondente. Selezionare quindi **Crea** nella parte inferiore della pagina.

4. Immettere le informazioni sull'area di lavoro:

   - Il *nome dell'area di lavoro* può contenere al massimo 260 caratteri e non può terminare con uno spazio. Il nome non può includere questi caratteri: `< > * % & : \ ? + /`
   - Se si distribuiscono i servizi Web da quest'area di lavoro, vengono usati il *piano di servizio Web* che si sceglie (o si crea) e il *piano tariffario* selezionato.

     ![Creare una nuova area di lavoro di studio (classica)](./media/create-workspace/create-new-workspace.png)

5. Fare clic su **Create**.

> [!NOTE]
> Machine Learning Studio (classico) si basa su un account di archiviazione di Azure fornito per salvare i dati intermedi quando esegue il flusso di lavoro. Dopo la creazione dell'area di lavoro, se l'account di archiviazione viene eliminato o se le chiavi di accesso vengono cambiate, l'area di lavoro non funzionerà più e tutti gli esperimenti al suo interno avranno esito negativo.
Se si elimina accidentalmente l'account di archiviazione, ricrearlo con lo stesso nome e nella stessa area dell'account di archiviazione eliminato e risincronizzare la chiave di accesso. Se sono state modificate le chiavi di accesso dell'account di archiviazione, sincronizzarle di nuovo nell'area di lavoro usando il portale di Azure.

Una volta distribuita l'area di lavoro, è possibile aprirla nella versione classica di Machine Learning Studio.

1. Passare a Machine Learning Studio (versione classica) [https://studio.azureml.net/](https://studio.azureml.net/).

2. Selezionare l'area di lavoro nell'angolo in alto a destra.

    ![Selezionare l'area di lavoro](./media/create-workspace/open-workspace.png)

3. Fare clic su **esperimenti personali**.

    ![Aprire gli esperimenti](./media/create-workspace/my-experiments.png)

Per informazioni sulla gestione dell'area di lavoro di studio (classica), vedere [gestire un'area di lavoro di Azure Machine Learning Studio (classica)](manage-workspace.md).
Se si verifica un problema durante la creazione dell'area di lavoro, vedere [Guida alla risoluzione dei problemi: creare e connettersi a un'area di lavoro di Machine Learning Studio (classica)](troubleshooting-creating-ml-workspace.md).


## <a name="share-an-azure-machine-learning-studio-classic-workspace"></a>Condividere un'area di lavoro di Azure Machine Learning Studio (classica)
Una volta creata un'area di lavoro di Machine Learning Studio (classica), è possibile invitare gli utenti nell'area di lavoro per condividere l'accesso all'area di lavoro e a tutti i relativi esperimenti, set di impostazioni, notebook e così via. È possibile aggiungere utenti in uno dei due ruoli seguenti:

* **Utente**: un utente dell'area di lavoro può creare, aprire, modificare ed eliminare esperimenti, dataset e così via nell'area di lavoro.
* **Proprietario**: un proprietario può invitare e rimuovere gli utenti dell'area di lavoro in aggiunta alle operazioni consentite a un utente.

> [!NOTE]
> L'account amministratore che crea l'area di lavoro viene aggiunto automaticamente all'area di lavoro come proprietario. Agli altri amministratori o utenti della sottoscrizione non viene invece concesso automaticamente l'accesso all'area di lavoro, ma è necessario inviare loro un invito in modo esplicito.
> 
> 

### <a name="to-share-a-studio-classic-workspace"></a>Per condividere un'area di lavoro di studio (classica)

1. Accedere alla versione classica di Machine Learning Studio in [https://studio.azureml.net/Home](https://studio.azureml.net/Home)

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



