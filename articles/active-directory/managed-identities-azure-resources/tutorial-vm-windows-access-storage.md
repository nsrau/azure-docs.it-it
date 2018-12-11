---
title: Usare un'identità gestita assegnata dal sistema per una macchina virtuale Windows per accedere ad Archiviazione di Azure
description: Esercitazione che illustra come usare un'identità gestita assegnata dal sistema per una macchina virtuale Windows per accedere ad Archiviazione di Azure.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: daveba
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/12/2018
ms.author: daveba
ms.openlocfilehash: 9c2280d2b571bf9f08adb69b67992376a71c1463
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/01/2018
ms.locfileid: "52723636"
---
# <a name="tutorial-use-a-windows-vm-system-assigned-managed-identity-to-access-azure-storage"></a>Esercitazione: Usare un'identità gestita assegnata dal sistema per una macchina virtuale Windows per accedere ad Archiviazione di Azure

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Questa esercitazione illustra come usare un'identità gestita assegnata dal sistema per una macchina virtuale Windows per accedere ad Archiviazione di Azure. Si apprenderà come:

> [!div class="checklist"]
> * Creare un contenitore BLOB nell'account di archiviazione
> * Concedere l'accesso a un account di archiviazione all'identità gestita assegnata dal sistema della macchina virtuale Windows 
> * Ottenere un accesso e usarlo per chiamare l'archiviazione di Azure 

> [!NOTE]
> L'autenticazione di Azure Active Directory per l'Archiviazione di Azure è disponibile come anteprima pubblica.

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]

## <a name="create-a-storage-account"></a>Creare un account di archiviazione 

In questa sezione, si crea un account di archiviazione. 

1. Fare clic sul pulsante **+ Crea una risorsa** visualizzato nell'angolo in alto a sinistra nel portale di Azure.
2. Selezionare **Archiviazione** e quindi **Account di archiviazione: BLOB, File, Tabelle, Code**.
3. In **Nome**, immettere il nome dell'account di archiviazione.  
4. **Modello di distribuzione** e **Tipologia account** devono essere impostati su **Gestione di risorse** e **Archiviazione (utilizzo generico v1)**. 
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

È possibile usare l'identità gestita assegnata dal sistema della macchina virtuale per recuperare i dati nel BLOB di archiviazione di Azure.   

1. Tornare all'account di archiviazione appena creato.  
2. Fare clic sul collegamento **Controllo di accesso (IAM)** nel pannello di sinistra.  
3. Fare clic su **+ Aggiungi assegnazione di ruolo** nella parte superiore della pagina per aggiungere una nuova assegnazione di ruolo per la macchina virtuale.
4. In **Ruolo**, nell'elenco a discesa, selezionare **Lettore dei dati del BLOB di archiviazione (anteprima)**. 
5. Dall'elenco a discesa, in **Assegna accesso a** selezionare **Macchina virtuale**.  
6. Assicurarsi quindi che la sottoscrizione appropriata sia presente nell'elenco a discesa **Sottoscrizione** e quindi impostare **Gruppo di risorse** su **Tutti i gruppi di risorse**.  
7. In **Seleziona**, scegliere la macchina virtuale e quindi fare clic su **Salva**. 

    ![Assegnare autorizzazioni](./media/tutorial-linux-vm-access-storage/access-storage-perms.png)

## <a name="get-an-access-token-and-use-it-to-call-azure-storage"></a>Ottenere un token di accesso e usarlo per chiamare l'archiviazione di Azure 

Archiviazione di Azure supporta in modo nativo l'autenticazione di Azure AD, per poter accettare direttamente i token di accesso ottenuti usando un'identità gestita. Questo fa parte dell'integrazione di Archiviazione di Azure con Azure AD e non prevede l'inserimento di credenziali nella stringa di connessione.

Ecco un esempio di codice .Net per l'apertura di una connessione ad Archiviazione di Azure usando un token di accesso e quindi la lettura del contenuto del file creato in precedenza. Questo codice deve essere eseguito nella macchina virtuale per poter accedere all'endpoint dell'identità gestita della macchina virtuale. Per usare il metodo del token di accesso è necessario .Net Framework 4.6 o una versione successiva. Sostituire il valore di `<URI to blob file>` di conseguenza. È possibile ottenere questo valore spostandosi sul file creato e caricato in archiviazione BLOB e copiando l'**URL** in **Proprietà** nella pagina **Panoramica**.

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using System.IO;
using System.Net;
using System.Web.Script.Serialization; 
using Microsoft.WindowsAzure.Storage.Auth;
using Microsoft.WindowsAzure.Storage.Blob;

namespace StorageOAuthToken
{
    class Program
    {
        static void Main(string[] args)
        {
            //get token
            string accessToken = GetMSIToken("https://storage.azure.com/");
           
            //create token credential
            TokenCredential tokenCredential = new TokenCredential(accessToken);

            //create storage credentials
            StorageCredentials storageCredentials = new StorageCredentials(tokenCredential);

            Uri blobAddress = new Uri("<URI to blob file>");

            //create block blob using storage credentials
            CloudBlockBlob blob = new CloudBlockBlob(blobAddress, storageCredentials);
        
            //retrieve blob contents
            Console.WriteLine(blob.DownloadText());
            Console.ReadLine();
        }

        static string GetMSIToken(string resourceID)
        {
            string accessToken = string.Empty;
            // Build request to acquire MSI token
            HttpWebRequest request = (HttpWebRequest)WebRequest.Create("http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=" + resourceID);
            request.Headers["Metadata"] = "true";
            request.Method = "GET";

            try
            {
                // Call /token endpoint
                HttpWebResponse response = (HttpWebResponse)request.GetResponse();

                // Pipe response Stream to a StreamReader, and extract access token
                StreamReader streamResponse = new StreamReader(response.GetResponseStream());
                string stringResponse = streamResponse.ReadToEnd();
                JavaScriptSerializer j = new JavaScriptSerializer();
                Dictionary<string, string> list = (Dictionary<string, string>)j.Deserialize(stringResponse, typeof(Dictionary<string, string>));
                accessToken = list["access_token"];
                return accessToken;
            }
            catch (Exception e)
            {
                string errorText = String.Format("{0} \n\n{1}", e.Message, e.InnerException != null ? e.InnerException.Message : "Acquire token failed");
                return accessToken;
            }
        }            
    }
}
```

La risposta contiene il contenuto del file:

`Hello world! :)`

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è appreso come abilitare un'identità assegnata dal sistema di una macchina virtuale Windows per accedere ad Archiviazione di Azure.  Per altre informazioni su Archiviazione di Azure, vedere:

> [!div class="nextstepaction"]
> [Archiviazione di Azure](/azure/storage/common/storage-introduction)



