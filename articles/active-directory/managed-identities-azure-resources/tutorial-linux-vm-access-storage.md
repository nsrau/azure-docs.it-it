---
title: Usare un'identità gestita assegnata dal sistema per una macchina virtuale Linux per accedere ad Archiviazione di Azure
description: Esercitazione che illustra come usare un'identità gestita assegnata dal sistema per una macchina virtuale Linux per accedere ad Archiviazione di Azure.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/09/2018
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: bb7de72a435faf100d6992815ef8d5ec00cb3581
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "66236160"
---
# <a name="tutorial-use-a-linux-vm-system-assigned-managed-identity-to-access-azure-storage"></a>Esercitazione: Usare un'identità gestita assegnata dal sistema per una macchina virtuale Linux per accedere ad Archiviazione di Azure 

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Questa esercitazione mostra come usare un'identità gestita assegnata dal sistema per una macchina virtuale Linux per accedere ad Archiviazione di Azure. Si apprenderà come:

> [!div class="checklist"]
> * Creare un account di archiviazione
> * Creare un contenitore BLOB nell'account di archiviazione
> * Concedere all'identità gestita della VM Linux l'accesso a un contenitore di Archiviazione di Azure
> * Ottenere un token di accesso e usarlo per chiamare l'archiviazione di Azure

> [!NOTE]
> L'autenticazione di Azure Active Directory per l'Archiviazione di Azure è disponibile come anteprima pubblica.

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]

Per eseguire gli esempi di script dell'interfaccia della riga di comando in questa esercitazione sono disponibili due opzioni:

- Usare [Azure Cloud Shell](~/articles/cloud-shell/overview.md) tramite il portale di Azure o il pulsante **Prova**, che si trova nell'angolo in alto a destra di ogni blocco di codice.
- [Installare la versione più recente dell'interfaccia della riga di comando 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.23 o successiva) se si preferisce usare una console dell'interfaccia della riga di comando locale.

## <a name="create-a-storage-account"></a>Creare un account di archiviazione 

In questa sezione, si crea un account di archiviazione. 

1. Fare clic sul pulsante **+ Crea una risorsa** visualizzato nell'angolo in alto a sinistra nel portale di Azure.
2. Selezionare **Archiviazione** e quindi **Account di archiviazione: BLOB, File, Tabelle, Code**.
3. In **Nome**, immettere il nome dell'account di archiviazione.  
4. **Modello di distribuzione** e **Tipologia account** devono essere impostati su **Gestione di risorse** e **Archiviazione (utilizzo generico v1)** . 
5. Verificare che le impostazioni in **Sottoscrizione** e **Gruppo di risorse** corrispondano a quelle specificate al momento della creazione della macchina virtuale nel passaggio precedente.
6. Fare clic su **Create**(Crea).

    ![Creare un nuovo account di archiviazione](./media/msi-tutorial-linux-vm-access-storage/msi-storage-create.png)

## <a name="create-a-blob-container-and-upload-a-file-to-the-storage-account"></a>Creare un contenitore BLOB e caricare un file nell'account di archiviazione

Poiché i file richiedono l'archiviazione BLOB, è necessario creare un contenitore BLOB in cui archiviare il file. Nel contenitore BLOB caricare un file con il nuovo account di archiviazione.

1. Tornare all'account di archiviazione appena creato.
2. In **Servizio BLOB** fare clic su **Contenitori**.
3. Nella parte superiore della pagina fare clic su **+ Contenitore**.
4. In **Nuovo contenitore**, immettere il nome del contenitore e in **Livello di accesso pubblico** mantenere il valore predefinito.

    ![Creare un contenitore di archiviazione](./media/msi-tutorial-linux-vm-access-storage/create-blob-container.png)

5. Utilizzando un editor di propria scelta, creare un file denominato *hello world.txt* nel computer locale.  Aprire il file e aggiungere il testo (senza virgolette) "Hello world! :)"e quindi salvare il file. 

6. Per caricare un file nel nuovo contenitore creato, fare clic sul nome del contenitore, quindi fare clic su **Carica**
7. Nel riquadro **Caricamento BLOB**, in **File**, fare clic sull'icona della cartella e individuare il file **hello_world.txt** nel computer locale, selezionare il file, quindi fare clic su **Carica**.

    ![Caricare un file di testo](./media/msi-tutorial-linux-vm-access-storage/upload-text-file.png)

## <a name="grant-your-vm-access-to-an-azure-storage-container"></a>Concedere alla macchina virtuale l'accesso a un contenitore di Archiviazione di Azure 

È possibile utilizzare identità gestita della macchina virtuale per recuperare i dati nel BLOB di archiviazione di Azure.   

1. Tornare all'account di archiviazione appena creato.  
2. Fare clic sul collegamento **Controllo di accesso (IAM)** nel pannello di sinistra.  
3. Fare clic su **+ Aggiungi assegnazione di ruolo** nella parte superiore della pagina per aggiungere una nuova assegnazione di ruolo per la macchina virtuale.
4. In **Ruolo** scegliere **Lettore dei dati del BLOB di archiviazione** dall'elenco a discesa. 
5. Dall'elenco a discesa, in **Assegna accesso a** selezionare **Macchina virtuale**.  
6. Assicurarsi quindi che la sottoscrizione appropriata sia presente nell'elenco a discesa **Sottoscrizione** e quindi impostare **Gruppo di risorse** su **Tutti i gruppi di risorse**.  
7. In **Seleziona**, scegliere la macchina virtuale e quindi fare clic su **Salva**.

    ![Assegnare autorizzazioni](./media/tutorial-linux-vm-access-storage/access-storage-perms.png)

## <a name="get-an-access-token-and-use-it-to-call-azure-storage"></a>Ottenere un token di accesso e usarlo per chiamare l'archiviazione di Azure

Archiviazione di Azure supporta in modo nativo l'autenticazione di Azure AD, per poter accettare direttamente i token di accesso ottenuti usando l'identità gestita. Questo fa parte dell'integrazione di Archiviazione di Azure con Azure AD e non prevede l'inserimento di credenziali nella stringa di connessione.

Per completare i passaggi seguenti, si deve operare dalla VM creata in precedenza ed è necessario un client SSH per la connessione. Se si usa Windows, è possibile usare il client SSH nel [sottosistema Windows per Linux](https://msdn.microsoft.com/commandline/wsl/about). Per richiedere assistenza nella configurazione delle chiavi del client SSH, vedere [Come usare le chiavi SSH con Windows in Azure](~/articles/virtual-machines/linux/ssh-from-windows.md) o [Come creare e usare una coppia di chiavi SSH pubblica e privata per le macchine virtuali Linux in Azure](~/articles/virtual-machines/linux/mac-create-ssh-keys.md).

1. Nel portale di Azure passare a **Macchine virtuali**, selezionare la macchina virtuale Linux e quindi nella pagina **Panoramica** fare clic su **Connetti**. Copiare la stringa di connessione alla macchina virtuale.
2. **Connettersi** alla macchina virtuale usando un client SSH di propria scelta. 
3. Nella finestra del terminale usare CURL per eseguire una richiesta all'endpoint locale dell'identità gestita per ottenere un token di accesso per Archiviazione di Azure.
    
    ```bash
    curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fstorage.azure.com%2F' -H Metadata:true
    ```
4. A questo punto usare il token di accesso per accedere ad Archiviazione di Azure, ad esempio per leggere il contenuto del file di esempio caricato in precedenza nel contenitore. Sostituire i valori di `<STORAGE ACCOUNT>`, `<CONTAINER NAME>` e `<FILE NAME>` con i valori specificati in precedenza, e `<ACCESS TOKEN>` con il token restituito nel passaggio precedente.

   ```bash
   curl https://<STORAGE ACCOUNT>.blob.core.windows.net/<CONTAINER NAME>/<FILE NAME> -H "x-ms-version: 2017-11-09" -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

   La risposta contiene il contenuto del file:

   ```bash
   Hello world! :)
   ```

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è appreso come abilitare un'identità gestita assegnata dal sistema per una macchina virtuale Linux per accedere ad Archiviazione di Azure.  Per altre informazioni su Archiviazione di Azure, vedere:

> [!div class="nextstepaction"]
> [Archiviazione di Azure](/azure/storage/common/storage-introduction)
