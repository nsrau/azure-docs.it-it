---
title: Gestire versioni nelle app LUIS in Azure | Microsoft Docs
description: Informazioni su come gestire le versioni nelle applicazioni LUIS (Language Understanding).
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 03/29/2017
ms.author: diberry
ms.openlocfilehash: 4941cf533f1b860ead07a416d5af6f62a1978305
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/24/2018
ms.locfileid: "39226595"
---
# <a name="manage-versions"></a>Gestire le versioni

Ogni volta che si usa il modello, si crea una [versione](luis-concept-version.md) diversa dell'app. 

## <a name="set-active-version"></a>Impostare la versione attiva
Per usare le versioni, aprire l'app selezionando il relativo nome nella pagina **App personali** e quindi selezionare **Impostazioni** nella barra superiore.

![Pagina delle versioni](./media/luis-how-to-manage-versions/settings.png)

La pagina **Impostazioni** consente di configurare le impostazioni per l'intera app, inclusi collaboratori e versioni. 

## <a name="clone-a-version"></a>Clonare una versione
1. Nella pagina **Impostazioni**, dopo le sezioni relative alle impostazioni dell'app e ai collaboratori, trovare la riga con la versione da clonare. Selezionare il pulsante dei puntini di sospensione (***...*** ) all'estrema destra. 

    ![Proprietà nella riga della versione](./media/luis-how-to-manage-versions/version-section.png)

2. Selezionare **Clona** nell'elenco.

    ![Opzioni per le proprietà nella riga della versione](./media/luis-how-to-manage-versions/version-three-dots-modal.png)

3. Nella finestra di dialogo **Clone version** (Clona versione) digitare un nome per la nuova versione, ad esempio "0.2".

   ![Finestra di dialogo Clone Version (Clona versione)](./media/luis-how-to-manage-versions/version-clone-version-dialog.png)
 
 > [!NOTE]
 > L'ID della versione può essere composto solo da caratteri, cifre o '.' e non può superare i 10 caratteri.
 
 Verrà creata una nuova versione con il nome specificato, che verrà impostata come versione attiva.
 
  ![La versione viene creata e aggiunta all'elenco](./media/luis-how-to-manage-versions/new-version.png)

 > [!NOTE]
 > Come illustrato nella figura precedente, una versione pubblicata è associata a un contrassegno colorato, che indica il tipo di slot in cui è stata pubblicata: produzione (verde), staging (rosso) ed entrambi (nero). Per ogni versione pubblicata vengono visualizzate le date di training e di pubblicazione.

## <a name="set-active-version"></a>Impostare la versione attiva
1. Nella pagina **Impostazioni**, nell'elenco **Versioni**, selezionare il pulsante dei puntini di sospensione (***...***) all'estrema destra.

2. Nell'elenco popup selezionare **Imposta come attivo**.

    ![Impostare la versione attiva](./media/luis-how-to-manage-versions/set-active-version.png)

    La versione attiva è evidenziata in azzurro, come illustrato nello screenshot seguente:

    ![Versione attiva](./media/luis-how-to-manage-versions/set-active-version-done.png) 


## <a name="import-version"></a>Importare la versione
È possibile importare una versione da un file JSON. Una volta importata una versione, la nuova versione diventa quella attiva.

**Per importare una versione:**

1. Nella pagina **Impostazioni** selezionare **Import new version** (Importa nuova versione).

    ![Pulsante per l'importazione](./media/luis-how-to-manage-versions/import-version.png)

2. Selezionare **Sfoglia** e scegliere il file JSON.

    ![Finestra di dialogo di importazione della versione](./media/luis-how-to-manage-versions/import-version-dialog.png)

È necessario impostare un ID della versione solo se la versione nel file JSON è già presente nell'app.

## <a name="export-version"></a>Esportare una versione
È possibile esportare una versione in un file JSON.

**Per esportare una versione:**

1. Nella pagina **Impostazioni**, nell'elenco **Versioni**, selezionare il pulsante dei puntini di sospensione (***...***) all'estrema destra.

2. Selezionare **Esporta** nell'elenco popup di azioni e scegliere dove salvare il file.

## <a name="delete-a-version"></a>Eliminare una versione
È possibile eliminare le versioni, ma è necessario mantenere almeno una versione dell'app. È possibile eliminare tutte le versioni ad eccezione di quella attiva. 

1. Nella pagina **Impostazioni**, nell'elenco **Versioni**, selezionare il pulsante dei puntini di sospensione (***...***) all'estrema destra.

2. Selezionare **Elimina** nell'elenco popup di azioni e scegliere dove salvare il file.

    ![Conferma dell'eliminazione della versione](./media/luis-how-to-manage-versions/delete-menu.png) 


## <a name="rename-a-version"></a>Rinominare una versione
È possibile rinominare le versioni a condizione che il nome della versione non sia già in uso.  

1. Nella pagina **Impostazioni**, nell'elenco **Versioni**, selezionare il pulsante dei puntini di sospensione (***...***) all'estrema destra.

2. Selezionare **Rinomina** nell'elenco popup di azioni.

3. Immettere il nuovo nome della versione e selezionare **Fine**.

    ![Conferma della ridenominazione della versione](./media/luis-how-to-manage-versions/rename-popup.png) 
