---
title: Creare o aggiornare ruoli personalizzati di Azure usando il portale di Azure (anteprima)Create or update Azure custom roles using the Azure portal (Preview) - Azure RBAC
description: Informazioni su come creare ruoli personalizzati di Azure per il controllo degli accessi in base al ruolo di Azure usando il portale di Azure.Learn how to create Azure custom roles for Azure role-based access control (Azure RBAC) using the Azure portal. Viene illustrato come elencare, creare, aggiornare ed eliminare ruoli personalizzati.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/26/2020
ms.author: rolyon
ms.openlocfilehash: 3204cdf51f3f37588f684f801a811f569b337d13
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77674870"
---
# <a name="create-or-update-azure-custom-roles-using-the-azure-portal-preview"></a>Creare o aggiornare ruoli personalizzati di Azure usando il portale di Azure (anteprima)Create or update Azure custom roles using the Azure portal (Preview)

> [!IMPORTANT]
> I ruoli personalizzati di Azure con il portale di Azure sono attualmente in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate.
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Se i [ruoli predefiniti di Azure](built-in-roles.md) non soddisfano le esigenze specifiche dell'organizzazione, è possibile creare ruoli personalizzati di Azure personalizzati. Analogamente ai ruoli predefiniti, è possibile assegnare ruoli personalizzati a utenti, gruppi ed entità servizio in corrispondenza degli ambiti di sottoscrizione e gruppo di risorse. I ruoli personalizzati vengono archiviati in una directory di Azure Active Directory (Azure AD) e possono essere condivisi tra le sottoscrizioni. Ogni directory può avere fino a 5000 ruoli personalizzati. È possibile creare ruoli personalizzati usando il portale di Azure, Azure PowerShell, l'interfaccia della riga di comando di Azure o l'API REST. Questo articolo descrive come creare ruoli personalizzati usando il portale di Azure (attualmente in anteprima).

## <a name="prerequisites"></a>Prerequisiti

Per creare ruoli personalizzati è necessario:

- Avere le autorizzazioni per creare ruoli personalizzati, ad esempio [Proprietario](built-in-roles.md#owner) o [Amministratore Accesso utenti](built-in-roles.md#user-access-administrator)

## <a name="step-1-determine-the-permissions-you-need"></a>Passaggio 1: Determinare le autorizzazioni necessarieStep 1: Determine the permissions you need

Azure dispone di migliaia di autorizzazioni che è possibile includere nel ruolo personalizzato. Di seguito sono riportati quattro modi in cui è possibile determinare le autorizzazioni che è necessario aggiungere al ruolo personalizzato:

| Metodo | Descrizione |
| --- | --- |
| Esaminare i ruoli esistenti | È possibile esaminare i ruoli esistenti per vedere quali autorizzazioni vengono utilizzate. Per altre informazioni, vedere Ruoli predefiniti di Azure.For more information, see [Azure built-in roles](built-in-roles.md). |
| Cercare le autorizzazioni per parola chiave | Quando si crea un ruolo personalizzato tramite il portale di Azure, è possibile cercare le autorizzazioni per parola chiave. Ad esempio, è possibile cercare le autorizzazioni di *macchina virtuale* o *di fatturazione.* Questa funzionalità di ricerca viene descritta più avanti in [Passaggio 4: Autorizzazioni](#step-4-permissions). |
| Scarica tutte le autorizzazioni | Quando si crea un ruolo personalizzato usando il portale di Azure, è possibile scaricare tutte le autorizzazioni come file CSV e quindi eseguire ricerche in questo file. Nel riquadro **Aggiungi autorizzazioni** fare clic sul pulsante Scarica tutte **le autorizzazioni** per scaricare tutte le autorizzazioni. Per ulteriori informazioni sul riquadro Aggiungi autorizzazioni, vedere [Passaggio 4: Autorizzazioni](#step-4-permissions). |
| Visualizzare le autorizzazioni nei documenti | È possibile visualizzare le autorizzazioni disponibili nelle operazioni del provider di risorse di [Azure Resource Manager.](resource-provider-operations.md) |

## <a name="step-2-choose-how-to-start"></a>Passo 2: Scegli come iniziare

È possibile iniziare a creare un ruolo personalizzato in tre modi. È possibile clonare un ruolo esistente, iniziare da zero o iniziare con un file JSON. Il modo più semplice consiste nel trovare un ruolo esistente che disponga della maggior parte delle autorizzazioni necessarie e quindi clonarlo e modificarlo per lo scenario. 

### <a name="clone-a-role"></a>Clonare un ruoloClone a role

Se un ruolo esistente non dispone delle autorizzazioni necessarie, è possibile clonarlo e quindi modificare le autorizzazioni. Seguire questi passaggi per avviare la clonazione di un ruolo.

1. Nel portale di Azure aprire una sottoscrizione o un gruppo di risorse in cui si vuole che il ruolo personalizzato sia assegnabile e quindi aprire Controllo di **accesso (IAM).**

    La schermata seguente mostra la pagina Controllo di accesso (IAM) aperta per una sottoscrizione.

    ![Pagina di controllo di accesso (IAM) per un abbonamentoAccess control (IAM) page for a subscription](./media/custom-roles-portal/access-control-subscription.png)

1. Fare clic su **Ruoli** per visualizzare l'elenco di tutti i ruoli predefiniti e personalizzati.

1. Cercare un ruolo che si desidera clonare, ad esempio il ruolo Lettore fatturazione.

1. Alla fine della riga fare clic sui ellissi (**...**) e quindi fare clic su **Clona**.

    ![Menu di scelta rapida Clone](./media/custom-roles-portal/clone-menu.png)

    Verrà aperto l'editor dei ruoli personalizzati con l'opzione **Clona un ruolo** selezionata.

1. Procedere al [passaggio 3: Nozioni di base](#step-3-basics).

### <a name="start-from-scratch"></a>Iniziare da zero

Se si preferisce, è possibile seguire questi passaggi per avviare un ruolo personalizzato da zero.

1. Nel portale di Azure aprire una sottoscrizione o un gruppo di risorse in cui si vuole che il ruolo personalizzato sia assegnabile e quindi aprire Controllo di **accesso (IAM).**

1. Fare clic su **Aggiungi** e quindi su **Aggiungi ruolo personalizzato (anteprima)**.

    ![Aggiungere un menu del ruolo personalizzato](./media/custom-roles-portal/add-custom-role-menu.png)

    Verrà aperto l'editor dei ruoli personalizzati con l'opzione **Inizia da zero** selezionata.

1. Procedere al [passaggio 3: Nozioni di base](#step-3-basics).

### <a name="start-from-json"></a>Iniziare da JSONStart from JSON

Se si preferisce, è possibile specificare la maggior parte dei valori dei ruoli personalizzati in un file JSON. È possibile aprire il file nell'editor dei ruoli personalizzati, apportare ulteriori modifiche e quindi creare il ruolo personalizzato. Seguire questi passaggi per iniziare con un file JSON.

1. Creare un file JSON con il formato seguente:Create a JSON file that has the following format:

    ```json
    {
        "properties": {
            "roleName": "",
            "description": "",
            "assignableScopes": [],
            "permissions": [
                {
                    "actions": [],
                    "notActions": [],
                    "dataActions": [],
                    "notDataActions": []
                }
            ]
        }
    }
    ```

1. Nel file JSON specificare i valori per le varie proprietà. Ecco un esempio con alcuni valori aggiunti. Per informazioni sulle diverse proprietà, vedere [Informazioni sulle definizioni di ruolo](role-definitions.md).

    ```json
    {
        "properties": {
            "roleName": "Billing Reader Plus",
            "description": "Read billing data and download invoices",
            "assignableScopes": [
                "/subscriptions/11111111-1111-1111-1111-111111111111"
            ],
            "permissions": [
                {
                    "actions": [
                        "Microsoft.Authorization/*/read",
                        "Microsoft.Billing/*/read",
                        "Microsoft.Commerce/*/read",
                        "Microsoft.Consumption/*/read",
                        "Microsoft.Management/managementGroups/read",
                        "Microsoft.CostManagement/*/read",
                        "Microsoft.Support/*"
                    ],
                    "notActions": [],
                    "dataActions": [],
                    "notDataActions": []
                }
            ]
        }
    }
    ```
    
1. Nel portale di Azure aprire la pagina Controllo di accesso **(IAM).**

1. Fare clic su **Aggiungi** e quindi su **Aggiungi ruolo personalizzato (anteprima)**.

    ![Aggiungere un menu del ruolo personalizzato](./media/custom-roles-portal/add-custom-role-menu.png)

    Verrà aperto l'editor dei ruoli personalizzati.

1. Nella scheda Nozioni di base, in **Autorizzazioni di base**, selezionare Inizia da **JSON**.

1. Accanto alla casella Selezionare un file fare clic sul pulsante della cartella per aprire la finestra di dialogo Apri.

1. Selezionare il file JSON e quindi fare clic su **Apri**.

1. Procedere al [passaggio 3: Nozioni di base](#step-3-basics).

## <a name="step-3-basics"></a>Passaggio 3: Nozioni di baseStep 3: Basics

Nella scheda **Nozioni di base** specificare il nome, la descrizione e le autorizzazioni di base per il ruolo personalizzato.

1. Nella casella **Nome ruolo personalizzato** specificare un nome per il ruolo personalizzato. Il nome deve essere univoco per la directory di Azure AD. Il nome può includere lettere, numeri, spazi e caratteri speciali.

1. Nella casella **Descrizione** specificare una descrizione facoltativa per il ruolo personalizzato. Questo diventerà la descrizione comando per il ruolo personalizzato.

    L'opzione **Autorizzazioni di base** dovrebbe essere già impostata in base al passaggio precedente, ma è possibile modificarla.

    ![Scheda Nozioni di base con i valori specificati](./media/custom-roles-portal/basics-values.png)

## <a name="step-4-permissions"></a>Passaggio 4: AutorizzazioniStep 4: Permissions

Nella scheda **Autorizzazioni** specificare le autorizzazioni per il ruolo personalizzato. A seconda che sia stato clonato un ruolo o se sia stato avviato con JSON, la scheda Autorizzazioni potrebbe già elencare alcune autorizzazioni.

![Scheda Autorizzazioni di Crea ruolo personalizzato](./media/custom-roles-portal/permissions.png)

### <a name="add-or-remove-permissions"></a>Aggiungere o rimuovere autorizzazioni

Seguire questi passaggi per aggiungere o rimuovere autorizzazioni per il ruolo personalizzato.

1. Per aggiungere autorizzazioni, fare clic su **Aggiungi autorizzazioni** per aprire il riquadro Aggiungi autorizzazioni.

    In questo riquadro sono elencate tutte le autorizzazioni disponibili raggruppate in categorie diverse in un formato scheda. Ogni categoria rappresenta un provider di *risorse,* ovvero un servizio che fornisce risorse di Azure.Each category represents a resource provider , which is a service that supplies Azure resources.

1. Nella casella **Cerca un'autorizzazione** digitare una stringa per cercare le autorizzazioni. Ad esempio, cercare *la fattura* per trovare le autorizzazioni relative alla fattura.

    Verrà visualizzato un elenco di schede del provider di risorse in base alla stringa di ricerca. Per un elenco delle modalità di mapping dei provider di risorse ai servizi di Azure, vedere Provider di risorse per i servizi di Azure.For a list of how resource providers map to Azure services, see [Resource providers for Azure services.](../azure-resource-manager/management/azure-services-resource-providers.md)

    ![Riquadro Aggiungi autorizzazioni con il provider di risorse](./media/custom-roles-portal/add-permissions-provider.png)

1. Fare clic su una scheda del provider di risorse che potrebbe disporre delle autorizzazioni che si desidera aggiungere al ruolo personalizzato, ad esempio **Microsoft Billing**.

    Viene visualizzato un elenco delle autorizzazioni di gestione per il provider di risorse in base alla stringa di ricerca.

    ![Aggiungi elenco autorizzazioni](./media/custom-roles-portal/add-permissions-list.png)

1. Se si desiderano autorizzazioni applicabili al piano dati, fare clic su **Azioni dati**. In caso contrario, lasciare l'interruttore Azioni impostato su **Azioni** per elencare le autorizzazioni che si applicano al piano di gestione. Per ulteriori informazioni sulle differenze tra il piano di gestione e il piano dati, vedere [Gestione e operazioni sui dati](role-definitions.md#management-and-data-operations).

1. Se necessario, aggiornare la stringa di ricerca per perfezionare ulteriormente la ricerca.

1. Dopo aver trovato una o più autorizzazioni che si desidera aggiungere al ruolo personalizzato, aggiungere un segno di spunta accanto alle autorizzazioni. Ad esempio, aggiungere un segno di spunta accanto a **Altro : Scarica fattura** per aggiungere l'autorizzazione per scaricare le fatture.

1. Fare clic su **Aggiungi** per aggiungere l'autorizzazione all'elenco delle autorizzazioni.

    L'autorizzazione viene `Actions` aggiunta `DataActions`come un oggetto o un oggetto .

    ![Autorizzazione aggiunta](./media/custom-roles-portal/permissions-list-add.png)

1. Per rimuovere le autorizzazioni, fare clic sull'icona di eliminazione alla fine della riga. In questo esempio, poiché un utente non avrà `Microsoft.Support/*` bisogno della possibilità di creare ticket di supporto, l'autorizzazione può essere eliminata.

### <a name="add-wildcard-permissions"></a>Aggiungere autorizzazioni con caratteri jolly

A seconda di come si è scelto di\*avviare, è possibile disporre di autorizzazioni con caratteri jolly ( ) nell'elenco delle autorizzazioni. Un carattere jolly (\*) estende un'autorizzazione a tutto ciò che corrisponde alla stringa specificata. Si supponga, ad esempio, di voler aggiungere tutte le autorizzazioni correlate alla gestione dei costi di Azure e alle esportazioni. È possibile aggiungere tutte queste autorizzazioni:You could add all of these permissions:

```
Microsoft.CostManagement/exports/action
Microsoft.CostManagement/exports/read
Microsoft.CostManagement/exports/write
Microsoft.CostManagement/exports/delete
Microsoft.CostManagement/exports/run/action
```

Invece di aggiungere tutte queste autorizzazioni, è possibile aggiungere semplicemente un'autorizzazione con caratteri jolly. Ad esempio, l'autorizzazione con caratteri jolly seguente equivale alle cinque autorizzazioni precedenti. Ciò includerà anche tutte le autorizzazioni di esportazione future che potrebbero essere aggiunte.

```
Microsoft.CostManagement/exports/*
```

Se si desidera aggiungere una nuova autorizzazione con caratteri jolly, non è possibile aggiungerla utilizzando il riquadro **Aggiungi autorizzazioni.** Per aggiungere un'autorizzazione con caratteri jolly, è necessario aggiungerla manualmente usando la scheda **JSON.** Per altre informazioni, vedere [Passaggio 6: JSON](#step-6-json).

### <a name="exclude-permissions"></a>Escludi autorizzazioni

Se il ruolo dispone\*di un'autorizzazione con caratteri jolly ( ) e si desidera escludere o sottrarre autorizzazioni specifiche da tale autorizzazione, è possibile escluderle. Si supponga, ad esempio, di disporre dell'autorizzazione con caratteri jolly seguente:

```
Microsoft.CostManagement/exports/*
```

Se non si desidera consentire l'eliminazione di un'esportazione, è possibile escludere la seguente autorizzazione di eliminazione:

```
Microsoft.CostManagement/exports/delete
```

Quando si esclude un'autorizzazione, `NotActions` questa `NotDataActions`viene aggiunta come un o . Le autorizzazioni di gestione effettive vengono `Actions` calcolate aggiungendo tutte `NotActions`le autorizzazioni di gestione e quindi sottraendo tutti i file . Le autorizzazioni per i dati effettivi `DataActions` vengono calcolate aggiungendo `NotDataActions`tutti i file e quindi sottraendo tutti i file .

> [!NOTE]
> L'esclusione di un'autorizzazione non equivale a una negazione. L'esclusione delle autorizzazioni è semplicemente un modo pratico per sottrarre le autorizzazioni da un'autorizzazione con caratteri jolly.

1. Per escludere o sottrarre un'autorizzazione da un'autorizzazione con caratteri jolly consentita, fare clic su **Escludi autorizzazioni** per aprire il riquadro Escludi autorizzazioni.

    In questo riquadro è possibile specificare le autorizzazioni di gestione o di dati escluse o sottratte.

1. Dopo aver trovato una o più autorizzazioni che si desidera escludere, aggiungere un segno di spunta accanto alle autorizzazioni, quindi fare clic sul pulsante **Aggiungi.**

    ![Riquadro Esclusione autorizzazioni - autorizzazione selezionata](./media/custom-roles-portal/exclude-permissions-select.png)

    L'autorizzazione viene `NotActions` aggiunta `NotDataActions`come un oggetto o .

    ![Autorizzazione esclusa](./media/custom-roles-portal/exclude-permissions-list-add.png)

## <a name="step-5-assignable-scopes"></a>Passaggio 5: Ambiti assegnabiliStep 5: Assignable scopes

Nella scheda **Ambiti assegnabili** specificare dove il ruolo personalizzato è disponibile per l'assegnazione, ad esempio sottoscrizione o gruppo di risorse. A seconda di come si è scelto di iniziare, questa scheda potrebbe elencare l'ambito in cui è stata aperta la pagina Controllo di accesso (IAM). L'impostazione dell'ambito assegnabile nell'ambito radice ("/") non è supportata. Per questa anteprima, non è possibile aggiungere un gruppo di gestione come ambito assegnabile.

1. Fare clic su **Aggiungi ambiti assegnabili** per aprire il riquadro Aggiungi ambiti assegnabili.

    ![Scheda Ambiti assegnabili](./media/custom-roles-portal/assignable-scopes.png)

1. Fare clic su uno o più ambiti che si desidera utilizzare, in genere la sottoscrizione.

    ![Aggiungere ambiti assegnabili](./media/custom-roles-portal/add-assignable-scopes.png)

1. Fare clic sul pulsante **Aggiungi** per aggiungere l'ambito assegnabile.

## <a name="step-6-json"></a>Passaggio 6: JSONStep 6: JSON

Nella scheda **JSON** viene visualizzato il ruolo personalizzato formattato in JSON. Se lo si desidera, è possibile modificare direttamente il codice JSON. Se si desidera aggiungere\*un'autorizzazione con caratteri jolly ( ), è necessario utilizzare questa scheda.

1. Per modificare il codice JSON, fare clic su **Modifica**.

    ![Scheda JSON con il ruolo personalizzato](./media/custom-roles-portal/json.png)

1. Apportare modifiche al codice JSON.

    Se il codice JSON non è formattato correttamente, verranno visualizzate una linea frastagliata rossa e un indicatore nella grondaia verticale.

1. Al termine delle modifiche, fare clic su **Salva**.

## <a name="step-7-review--create"></a>Passo 7: Rivedere e creare

Nella scheda **Revisione e creazione** è possibile esaminare le impostazioni dei ruoli personalizzati.

1. Rivedere le impostazioni dei ruoli personalizzati.

    ![Scheda Rivedi e crea](./media/custom-roles-portal/review-create.png)

1. Fare clic su **Crea** per creare il ruolo personalizzato.

    Dopo alcuni istanti, viene visualizzata una finestra di messaggio che indica che il ruolo personalizzato è stato creato correttamente.

    ![Creare un messaggio di ruolo personalizzatoCreate custom role message](./media/custom-roles-portal/custom-role-success.png)

    Se vengono rilevati errori, verrà visualizzato un messaggio.

    ![Rivedere e creare errore](./media/custom-roles-portal/review-create-error.png)

1. Visualizzare il nuovo ruolo personalizzato nell'elenco **Ruoli.** Se il ruolo personalizzato non è visualizzato, fare clic su **Aggiorna**.

     La visualizzazione del ruolo personalizzato ovunque può richiedere alcuni minuti.

## <a name="list-custom-roles"></a>Elencare ruoli personalizzati

Seguire questa procedura per visualizzare i ruoli personalizzati.

1. Aprire una sottoscrizione o un gruppo di risorse e quindi aprire **Controllo di accesso (IAM)**.

1. Fare clic su **Ruoli** per visualizzare l'elenco di tutti i ruoli predefiniti e personalizzati.

1. Nell'elenco **Tipo** selezionare **CustomRole** per visualizzare solo i ruoli personalizzati.

    Se il ruolo personalizzato è stato appena creato e non è presente nell'elenco, fare clic su **Aggiorna**.

    ![Elenco dei ruoli personalizzati](./media/custom-roles-portal/custom-role-list.png)

## <a name="update-a-custom-role"></a>Aggiornare un ruolo personalizzato

1. Come descritto in precedenza in questo articolo, aprire l'elenco dei ruoli personalizzati.

1. Fare clic sui ellissi (**...**) per il ruolo personalizzato che si desidera aggiornare e quindi fare clic su **Modifica**. Si noti che non è possibile aggiornare i ruoli predefiniti.

    Il ruolo personalizzato viene aperto nell'editor.

    ![Menu dei ruoli personalizzati](./media/custom-roles-portal/edit-menu.png)

1. Utilizzare le diverse schede per aggiornare il ruolo personalizzato.

1. Al termine delle modifiche, fare clic sulla scheda **Revisione e creazione** per rivedere le modifiche.

1. Fare clic sul pulsante **Aggiorna** per aggiornare il ruolo personalizzato.

## <a name="delete-a-custom-role"></a>Eliminare un ruolo personalizzato

1. Come descritto in precedenza in questo articolo, aprire l'elenco dei ruoli personalizzati.

1. Rimuovere tutte le assegnazioni di ruolo che utilizzano il ruolo personalizzato.

1. Fare clic sui ellissi (**...**) per il ruolo personalizzato che si desidera eliminare e quindi fare clic su **Elimina**.

    ![Menu dei ruoli personalizzati](./media/custom-roles-portal/delete-menu.png)

    L'eliminazione completa del ruolo personalizzato può richiedere alcuni minuti.

## <a name="next-steps"></a>Passaggi successivi

- [Esercitazione: creare un ruolo personalizzato con Azure PowerShell](tutorial-custom-role-powershell.md)
- [Ruoli personalizzati in Azure](custom-roles.md)
- [Operazioni del provider di risorse di Azure Resource ManagerAzure Resource Manager resource provider operations](resource-provider-operations.md)
