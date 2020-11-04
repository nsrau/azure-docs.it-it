---
title: "ML Studio (classico): creare un'area di lavoro-Azure"
description: Per usare Azure Machine Learning Studio (versione classica), è necessario disporre di un'area di lavoro di Machine Learning Studio (classica). Quest'area di lavoro contiene tutti gli strumenti necessari per la creazione, la gestione e la pubblicazione di esperimenti.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 12/07/2017
ms.openlocfilehash: 3fc7fd1f3f7615c0622b684e3da4020c7da601f5
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93322879"
---
# <a name="create-and-share-an-machine-learning-studio-classic-workspace"></a>Creare e condividere un'area di lavoro di Machine Learning Studio (classica)

**SI APPLICA A:**  ![sì](../../../includes/media/aml-applies-to-skus/yes.png)Machine Learning Studio (versione classica)   ![no ](../../../includes/media/aml-applies-to-skus/no.png)[Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)

Per usare Azure Machine Learning Studio (versione classica), è necessario disporre di un'area di lavoro di Machine Learning Studio (classica). Quest'area di lavoro contiene tutti gli strumenti necessari per la creazione, la gestione e la pubblicazione di esperimenti.

## <a name="create-a-studio-classic-workspace"></a>Creare un'area di lavoro di studio (classica)

Per aprire un'area di lavoro in Machine Learning Studio (versione classica), è necessario aver eseguito l'accesso all'account Microsoft usato per creare l'area di lavoro oppure è necessario ricevere un invito dal proprietario per partecipare all'area di lavoro. Dal portale di Azure è possibile gestire l'area di lavoro e anche configurare l'accesso.

1. Accedere al [portale di Azure](https://portal.azure.com/)

    > [!NOTE]
    > Per accedere e creare un'area di lavoro di studio (classica), è necessario essere un amministratore della sottoscrizione di Azure. 
    >
    > 

2. Fare clic su **+ nuovo**

3. Nella casella di ricerca digitare **Machine Learning Studio area di lavoro (classica)** e selezionare l'elemento corrispondente. Selezionare quindi **Crea** nella parte inferiore della pagina.

4. Immettere le informazioni sull'area di lavoro:

   - Il *nome dell'area di lavoro* può contenere al massimo 260 caratteri e non può terminare con uno spazio. Il nome non può includere questi caratteri: `< > * % & : \ ? + /`
   - Se si distribuiscono i servizi Web da quest'area di lavoro, vengono usati il *piano di servizio Web* che si sceglie (o si crea) e il *piano tariffario* selezionato.

     ![Creare una nuova area di lavoro di studio (classica)](./media/create-workspace/create-new-workspace.png)

5. Fare clic su **Crea**.

   Machine Learning è attualmente disponibile in un numero limitato di aree. Se la sottoscrizione non include una di queste aree, è possibile che venga visualizzato il messaggio di errore "non sono presenti sottoscrizioni nelle aree consentite".  Per richiedere l'aggiunta di un'area alla sottoscrizione, creare una nuova richiesta al supporto tecnico Microsoft dal portale di Azure, scegliere **Fatturazione** come tipo di problema, quindi seguire le istruzioni per inviare la richiesta.


> [!NOTE]
> Machine Learning Studio (classico) si basa su un account di archiviazione di Azure fornito per salvare i dati intermedi quando esegue il flusso di lavoro. Dopo la creazione dell'area di lavoro, se l'account di archiviazione viene eliminato o se le chiavi di accesso vengono cambiate, l'area di lavoro non funzionerà più e tutti gli esperimenti al suo interno avranno esito negativo.
Se si elimina accidentalmente l'account di archiviazione, ricrearlo con lo stesso nome e nella stessa area dell'account di archiviazione eliminato e risincronizzare la chiave di accesso. Se sono state modificate le chiavi di accesso dell'account di archiviazione, sincronizzarle di nuovo nell'area di lavoro usando il portale di Azure.

Una volta distribuita l'area di lavoro, è possibile aprirla in Machine Learning Studio (classica).

1. Passare a Machine Learning Studio (versione classica) all'indirizzo [https://studio.azureml.net/](https://studio.azureml.net/) .

2. Selezionare l'area di lavoro nell'angolo in alto a destra.

    ![Selezionare l'area di lavoro](./media/create-workspace/open-workspace.png)

3. Fare clic su **esperimenti personali**.

    ![Aprire gli esperimenti](./media/create-workspace/my-experiments.png)

Per informazioni sulla gestione dell'area di lavoro di studio (classica), vedere [gestire un'area di lavoro di Azure Machine Learning Studio (classica)](manage-workspace.md).
Se si verifica un problema durante la creazione dell'area di lavoro, vedere [Guida alla risoluzione dei problemi: creare e connettersi a un'area di lavoro di Machine Learning Studio (classica)](index.yml).


## <a name="share-an-azure-machine-learning-studio-classic-workspace"></a>Condividere un'area di lavoro di Azure Machine Learning Studio (classica)
Una volta creata un'area di lavoro di Machine Learning Studio (classica), è possibile invitare gli utenti nell'area di lavoro per condividere l'accesso all'area di lavoro e a tutti i relativi esperimenti, set di impostazioni e così via. È possibile aggiungere utenti in uno dei due ruoli seguenti:

* **Utente** : un utente dell'area di lavoro può creare, aprire, modificare ed eliminare esperimenti, dataset e così via nell'area di lavoro.
* **Proprietario** : un proprietario può invitare e rimuovere gli utenti dell'area di lavoro in aggiunta alle operazioni consentite a un utente.

> [!NOTE]
> L'account amministratore che crea l'area di lavoro viene aggiunto automaticamente all'area di lavoro come proprietario. Agli altri amministratori o utenti della sottoscrizione non viene invece concesso automaticamente l'accesso all'area di lavoro, ma è necessario inviare loro un invito in modo esplicito.
> 
> 

### <a name="to-share-a-studio-classic-workspace"></a>Per condividere un'area di lavoro di studio (classica)

1. Accedi a Machine Learning Studio (versione classica) all'indirizzo [https://studio.azureml.net/Home](https://studio.azureml.net/Home)

2. Nel pannello sinistro fare clic su **IMPOSTAZIONI**

3. Fare clic sulla scheda **utenti**

4. Fare clic su **INVITE MORE USERS (INVITA ALTRI UTENTI)** in fondo alla pagina

    ![Impostazioni di Studio](./media/create-workspace/settings.png)

5. Immettere uno o più indirizzi di posta elettronica. L'utente ha bisogno di un account Microsoft o di un account aziendale valido (da Azure Active Directory).

6. Scegliere se si desidera aggiungere l'utente come proprietario o utente.

7. Fare clic sul segno di spunta **OK**.

Ogni utente aggiunto riceverà un messaggio di posta elettronica con istruzioni per l'accesso all'area di lavoro condivisa.

> [!NOTE]
> Per poter distribuire o gestire i servizi Web nell'area di lavoro, l'utente deve essere un collaboratore o un amministratore nella sottoscrizione di Azure. 

## <a name="troubleshoot-storage-accounts"></a>Risolvere i problemi degli account di archiviazione


Il servizio Machine Learning necessita di un account di archiviazione per archiviare i dati. È possibile usare un account di archiviazione esistente oppure è possibile creare un nuovo account di archiviazione quando si crea la nuova area di lavoro Machine Learning Studio (classica) (se si dispone di una quota per creare un nuovo account di archiviazione).

Dopo aver creato la nuova area di lavoro Machine Learning Studio (classica), è possibile accedere a Machine Learning Studio (classica) usando il account Microsoft usato per creare l'area di lavoro. Se viene visualizzato il messaggio di errore "area di lavoro non trovata" (simile allo screenshot seguente), attenersi alla procedura seguente per eliminare i cookie del browser.

![Area di lavoro non trovata](media/troubleshooting-creating-ml-workspace/screen3.png)

**Per eliminare i cookie dal browser**

1. Se si usa Internet Explorer, fare clic sul pulsante **Strumenti** nell'angolo superiore destro, quindi selezionare **Opzioni Internet**.  

   ![Opzioni Internet](media/troubleshooting-creating-ml-workspace/screen4.png)

2. Nella scheda **Generale** fare clic su **Elimina**.

   ![Scheda Generale](media/troubleshooting-creating-ml-workspace/screen5.png)

3. Nella finestra di dialogo **Elimina cronologia esplorazioni** assicurarsi che l'opzione **Cookie e dati di siti Web** sia selezionata, quindi fare clic su **Elimina**.

   ![Eliminare i cookie](media/troubleshooting-creating-ml-workspace/screen6.png)

Dopo l'eliminazione dei cookie, riavviare il browser e passare alla pagina [Microsoft Azure Machine Learning Studio (classica)](https://studio.azureml.net) . Quando vengono richiesti nome utente e password, immettere lo stesso account Microsoft usato per creare l'area di lavoro.


## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sulla gestione di un'area di lavoro, vedere [gestire un'area di lavoro di Azure Machine Learning Studio (classica)](manage-workspace.md).