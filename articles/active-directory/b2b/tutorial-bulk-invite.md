---
title: Esercitazione per invitare in blocco utenti di Collaborazione B2B - Azure Active Directory | Microsoft Docs
description: In questa esercitazione si apprenderà come usare PowerShell e un file CSV per inviare inviti in blocco a utenti esterni di Collaborazione B2B di Azure AD.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: tutorial
ms.date: 9/19/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: ec1a6ea8f363f2ddd4a9568700d5bff3330443c0
ms.sourcegitcommit: 2ed6e731ffc614f1691f1578ed26a67de46ed9c2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/19/2019
ms.locfileid: "71128731"
---
# <a name="tutorial-bulk-invite-azure-ad-b2b-collaboration-users-preview"></a>Esercitazione: Invitare in blocco utenti di Collaborazione B2B di Azure AD (anteprima)

|     |
| --- |
| Questo articolo descrive una funzionalità di anteprima pubblica di Azure Active Directory. Per altre informazioni sulle anteprime, vedere [Condizioni per l'utilizzo supplementari per le anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).|
|     |


Se si usa Collaborazione B2B di Azure Active Directory (Azure AD) per collaborare con partner esterni, è possibile invitare nell'organizzazione più utenti guest contemporaneamente. Questa esercitazione descrive come usare il portale di Azure per inviare inviti in blocco a utenti esterni. In particolare, si eseguiranno le operazioni seguenti:

> [!div class="checklist"]
> * Usare **Invita utenti in blocco (anteprima)**  per preparare un file con valori delimitati da virgole (CSV) contenente le informazioni utente e le preferenze di invito
> * Caricare il file con estensione csv in Azure AD
> * Verificare che gli utenti siano stati aggiunti alla directory

Se non si ha Azure Active Directory, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare. 

## <a name="prerequisites"></a>Prerequisiti

Sono necessari almeno due account di posta elettronica di test a cui inviare gli inviti. Gli account devono essere esterni all'organizzazione. È possibile usare qualsiasi tipo di account, inclusi gli account di social networking, come gli indirizzi gmail.com o outlook.com.

## <a name="invite-guest-users-in-bulk"></a>Invitare utenti guest in blocco

1. Accedere al portale di Azure con un account di amministratore utenti nell'organizzazione.
2. Nel riquadro di spostamento selezionare **Azure Active Directory**.
3. In **Gestisci** selezionare **Utenti** > **Invita in blocco**.
4. Nella pagina **Invita utenti in blocco (anteprima)** selezionare **Scarica** per scaricare un file CSV valido con le proprietà di invito.

    ![Pulsante di download in Invita in blocco](media/tutorial-bulk-invite/bulk-invite-button.png)

5. Aprire il file CSV e aggiungere una riga per ogni utente guest. I valori obbligatori sono:

   * **Indirizzo di posta elettronica da invitare**: utente che riceverà un invito

   * **URL di reindirizzamento**: URL al quale viene reindirizzato l'utente invitato dopo aver accettato l'invito

    ![Esempio di un file CSV con utenti guest immessi](media/tutorial-bulk-invite/bulk-invite-csv.png)

   > [!NOTE]
   > Non usare virgole in **Messaggio di invito personalizzato** perché impediranno l'analisi corretta del messaggio.

6. Salvare il file.
7. Nella pagina **Invita utenti in blocco (anteprima)** individuare il file in **Caricare il file CSV**. Quando si seleziona il file, viene avviata la convalida del file CSV. 
8. Dopo la convalida del contenuto del file, viene visualizzato il messaggio **Il file è stato caricato**. Se sono presenti errori, è necessario correggerli prima di poter inviare il processo.
9. Quando il file supera la convalida, selezionare **Invia** per avviare l'operazione in blocco di Azure per l'aggiunta degli inviti. 
10. Per visualizzare lo stato del processo, selezionare **Fare clic qui per visualizzare lo stato di ogni operazione**. In alternativa, è possibile selezionare **Risultati dell'operazione in blocco (anteprima)** nella sezione **Attività**. Per informazioni dettagliate su ogni voce all'interno dell'operazione in blocco, selezionare i valori nelle colonne **N. operazioni riuscite**, **N. errori** o **Richieste totali**. Se si verificano errori, verranno elencati i motivi dell'errore.

    ![Esempio di risultati dell'operazione in blocco](media/tutorial-bulk-invite/bulk-operation-results.png)

11. Al termine del processo, verrà visualizzata una notifica per indicare la corretta esecuzione dell'operazione in blocco.

## <a name="verify-guest-users-in-the-directory"></a>Verificare gli utenti guest nella directory

Per verificare che gli utenti guest aggiunti siano presenti nella directory, usare il portale di Azure o PowerShell.

### <a name="view-guest-users-in-the-azure-portal"></a>Visualizzare gli utenti guest nel portale di Azure

1. Accedere al portale di Azure con un account di amministratore utenti nell'organizzazione.
2. Nel riquadro di spostamento selezionare **Azure Active Directory**.
3. In **Gestisci** selezionare **Utenti**.
4. In **Mostra** selezionare **Solo utenti guest** e verificare che gli utenti aggiunti siano elencati.

### <a name="view-guest-users-with-powershell"></a>Visualizzare utenti guest con PowerShell

Eseguire il comando seguente:

```powershell
 Get-AzureADUser -Filter "UserType eq 'Guest'"
```

Dovrebbero essere elencati gli utenti invitati, con il nome dell'entità utente nel formato *indirizzopostaelettronica*#EXT#\@*dominio*. Ad esempio *lstokes_fabrikam.com#EXT#\@contoso.onmicrosoft.com*, in cui contoso.onmicrosoft.com è l'organizzazione da cui sono stati inviati gli inviti.

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non è più necessario, è possibile eliminare gli account utente di test presenti nella directory nella pagina Utenti del portale di Azure selezionando la casella di controllo accanto all'utente guest e quindi scegliendo **Elimina**. 

In alternativa, per eliminare un account utente è possibile eseguire il comando PowerShell seguente:

```powershell
 Remove-AzureADUser -ObjectId "<UPN>"
```

Ad esempio: `Remove-AzureADUser -ObjectId "lstokes_fabrikam.com#EXT#@contoso.onmicrosoft.com"`

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono stati inviati inviti in blocco ad utenti guest all'esterno dell'organizzazione. Successivamente si apprenderà come funziona il processo di riscatto dell'invito.

> [!div class="nextstepaction"]
> [Informazioni sul processo di riscatto dell'invito di Collaborazione B2B di Azure AD](redemption-experience.md)
