---
title: Esercitazione per l'invio di inviti in blocco a utenti di Collaborazione B2B - Azure AD
description: In questa esercitazione si apprenderà come usare PowerShell e un file CSV per inviare inviti in blocco a utenti esterni di Collaborazione B2B di Azure AD.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: tutorial
ms.date: 05/07/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: f0f88b310bc00881e66ee8e8b5f2d40616d60315
ms.sourcegitcommit: 4e5560887b8f10539d7564eedaff4316adb27e2c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/06/2020
ms.locfileid: "87906913"
---
# <a name="tutorial-bulk-invite-azure-ad-b2b-collaboration-users"></a>Esercitazione: Invitare in blocco utenti di Collaborazione B2B di Azure AD

Se si usa Collaborazione B2B di Azure Active Directory (Azure AD) per collaborare con partner esterni, è possibile invitare nell'organizzazione più utenti guest contemporaneamente. Questa esercitazione descrive come usare il portale di Azure per inviare inviti in blocco a utenti esterni. In particolare, si eseguiranno le operazioni seguenti:

> [!div class="checklist"]
> * Uso di **Invita utenti in blocco** per preparare un file con valori delimitati da virgole (CSV) contenente le informazioni utente e le preferenze di invito
> * Caricare il file con estensione csv in Azure AD
> * Verificare che gli utenti siano stati aggiunti alla directory

Se non si ha Azure Active Directory, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="understand-the-csv-template"></a>Informazioni sul modello CSV

Scaricare e compilare il modello CSV di caricamento in blocco per invitare correttamente in blocco gli utenti guest di Azure AD. Il modello CSV scaricato sarà simile a questo esempio:

![Foglio di calcolo per il caricamento con callout che illustrano lo scopo e i valori di ogni riga e colonna](media/tutorial-bulk-invite/understand-template.png)

### <a name="csv-template-structure"></a>Struttura del modello CSV

Un modello CSV scaricato contiene le righe descritte di seguito.

- **Numero di versione**: nel file CSV di caricamento deve essere inclusa la prima riga contenente il numero di versione.
- **Intestazioni di colonna**: il formato delle intestazioni di colonna è &lt;*Nome elemento*&gt; [NomeProprietà] &lt;*Required o vuoto*&gt;. Ad esempio: `Email address to invite [inviteeEmail] Required`. Alcune versioni precedenti del modello potrebbero presentare lievi variazioni.
- **Riga di esempi**: nel modello è stata inclusa una riga di esempi di valori accettabili per ogni colonna. È necessario rimuovere la riga degli esempi e sostituirla con le proprie voci.

### <a name="additional-guidance"></a>Indicazioni aggiuntive

- Le prime due righe del modello di caricamento non devono essere rimosse o modificate. In caso contrario, il caricamento non potrà essere elaborato.
- Le colonne obbligatorie sono riportate per prime.
- Non è consigliabile aggiungere nuove colonne al modello. Le colonne aggiunte saranno ignorate e non verranno elaborate.
- È consigliabile scaricare l'ultima versione del modello CSV il più spesso possibile.

## <a name="prerequisites"></a>Prerequisiti

Sono necessari almeno due account di posta elettronica di test a cui inviare gli inviti. Gli account devono essere esterni all'organizzazione. È possibile usare qualsiasi tipo di account, inclusi gli account di social networking, come gli indirizzi gmail.com o outlook.com.

## <a name="invite-guest-users-in-bulk"></a>Invitare utenti guest in blocco

1. Accedere al portale di Azure con un account di amministratore utenti nell'organizzazione.
2. Nel riquadro di spostamento selezionare **Azure Active Directory**.
3. In **Gestisci** selezionare **Utenti** > **Invita in blocco**.
4. Nella pagina **Invita utenti in blocco** selezionare **Scarica** per scaricare un modello con estensione csv valido con le proprietà di invito.

    ![Pulsante di download in Invita in blocco](media/tutorial-bulk-invite/bulk-invite-button.png)

5. Aprire il modello con estensione csv e aggiungere una riga per ogni utente guest. I valori obbligatori sono:

   * **Indirizzo di posta elettronica da invitare**: utente che riceverà un invito

   * **URL di reindirizzamento**: URL al quale viene reindirizzato l'utente invitato dopo aver accettato l'invito

    ![Esempio di un file CSV con utenti guest immessi](media/tutorial-bulk-invite/bulk-invite-csv.png)

   > [!NOTE]
   > Non usare virgole in **Messaggio di invito personalizzato** perché impediranno l'analisi corretta del messaggio.

6. Salvare il file.
7. Nella pagina **Invita utenti in blocco** individuare il file in **Caricare il file CSV**. Quando si seleziona il file, viene avviata la convalida del file CSV. 
8. Dopo la convalida del contenuto del file, viene visualizzato il messaggio **Il file è stato caricato**. Se sono presenti errori, è necessario correggerli prima di poter inviare il processo.
9. Quando il file supera la convalida, selezionare **Invia** per avviare l'operazione in blocco di Azure per l'aggiunta degli inviti. 
10. Per visualizzare lo stato del processo, selezionare **Fare clic qui per visualizzare lo stato di ogni operazione**. In alternativa, è possibile selezionare **Risultati dell'operazione in blocco** nella sezione **Attività**. Per informazioni dettagliate su ogni voce all'interno dell'operazione in blocco, selezionare i valori nelle colonne **N. operazioni riuscite**, **N. errori** o **Richieste totali**. Se si verificano errori, verranno elencati i motivi dell'errore.

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
