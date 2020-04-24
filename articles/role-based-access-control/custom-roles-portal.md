---
title: Creare o aggiornare i ruoli personalizzati di Azure usando il portale di Azure (anteprima)-RBAC di Azure
description: Informazioni su come creare ruoli personalizzati di Azure per il controllo degli accessi in base al ruolo di Azure (RBAC di Azure) usando il portale di Azure. Viene illustrato come elencare, creare, aggiornare ed eliminare ruoli personalizzati.
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
# <a name="create-or-update-azure-custom-roles-using-the-azure-portal-preview"></a>Creare o aggiornare i ruoli personalizzati di Azure usando il portale di Azure (anteprima)

> [!IMPORTANT]
> I ruoli personalizzati di Azure che usano il portale di Azure sono attualmente disponibili in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate.
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Se i [ruoli predefiniti di Azure](built-in-roles.md) non soddisfano le esigenze specifiche dell'organizzazione, è possibile creare ruoli personalizzati di Azure. Analogamente ai ruoli predefiniti, è possibile assegnare ruoli personalizzati a utenti, gruppi ed entità servizio negli ambiti di sottoscrizione e gruppo di risorse. I ruoli personalizzati vengono archiviati in una directory di Azure Active Directory (Azure AD) e possono essere condivisi tra le sottoscrizioni. Ogni directory può avere un massimo di 5000 ruoli personalizzati. I ruoli personalizzati possono essere creati usando il portale di Azure, Azure PowerShell, l'interfaccia della riga di comando di Azure o l'API REST. Questo articolo descrive come creare ruoli personalizzati usando il portale di Azure (attualmente disponibile in anteprima).

## <a name="prerequisites"></a>Prerequisiti

Per creare ruoli personalizzati è necessario:

- Avere le autorizzazioni per creare ruoli personalizzati, ad esempio [Proprietario](built-in-roles.md#owner) o [Amministratore Accesso utenti](built-in-roles.md#user-access-administrator)

## <a name="step-1-determine-the-permissions-you-need"></a>Passaggio 1: determinare le autorizzazioni necessarie

Azure dispone di migliaia di autorizzazioni che possono essere potenzialmente incluse nel ruolo personalizzato. Di seguito sono illustrati quattro modi per determinare le autorizzazioni che si desidera aggiungere al ruolo personalizzato:

| Metodo | Descrizione |
| --- | --- |
| Esaminare i ruoli esistenti | È possibile esaminare i ruoli esistenti per vedere quali autorizzazioni vengono usate. Per altre informazioni, vedere [ruoli predefiniti di Azure](built-in-roles.md). |
| Cerca autorizzazioni per parola chiave | Quando si crea un ruolo personalizzato usando il portale di Azure, è possibile cercare le autorizzazioni in base alla parola chiave. Ad esempio, è possibile cercare le autorizzazioni per la *macchina virtuale* o la *fatturazione* . Questa funzionalità di ricerca è descritta più avanti nel [passaggio 4: autorizzazioni](#step-4-permissions). |
| Scarica tutte le autorizzazioni | Quando si crea un ruolo personalizzato usando il portale di Azure, è possibile scaricare tutte le autorizzazioni come file CSV e quindi eseguire la ricerca nel file. Nel riquadro **Aggiungi autorizzazioni** fare clic sul pulsante **Scarica tutte le autorizzazioni** per scaricare tutte le autorizzazioni. Per ulteriori informazioni sul riquadro Aggiungi autorizzazioni, vedere [Step 4: Permissions](#step-4-permissions). |
| Visualizzare le autorizzazioni nella documentazione | È possibile visualizzare le autorizzazioni disponibili in [Azure Resource Manager operazioni del provider di risorse](resource-provider-operations.md). |

## <a name="step-2-choose-how-to-start"></a>Passaggio 2: scegliere la modalità di avvio

È possibile iniziare a creare un ruolo personalizzato in tre modi. È possibile clonare un ruolo esistente, iniziare da zero o iniziare con un file JSON. Il modo più semplice consiste nell'individuare un ruolo esistente con la maggior parte delle autorizzazioni necessarie e quindi clonarlo e modificarlo per lo scenario. 

### <a name="clone-a-role"></a>Clonare un ruolo

Se un ruolo esistente non dispone delle autorizzazioni necessarie, è possibile clonarlo e quindi modificare le autorizzazioni. Per avviare la clonazione di un ruolo, seguire questa procedura.

1. Nella portale di Azure aprire una sottoscrizione o un gruppo di risorse in cui si desidera assegnare il ruolo personalizzato e quindi aprire controllo di **accesso (IAM)**.

    La schermata seguente mostra la pagina controllo di accesso (IAM) aperta per una sottoscrizione.

    ![Pagina controllo di accesso (IAM) per una sottoscrizione](./media/custom-roles-portal/access-control-subscription.png)

1. Fare clic su **Ruoli** per visualizzare l'elenco di tutti i ruoli predefiniti e personalizzati.

1. Cercare un ruolo che si desidera clonare, ad esempio il ruolo lettura fatturazione.

1. Alla fine della riga fare clic sui puntini di sospensione (**...**) e quindi fare clic su **clona**.

    ![Menu di scelta rapida clone](./media/custom-roles-portal/clone-menu.png)

    Verrà aperto l'editor dei ruoli personalizzati con l'opzione **clona un ruolo** selezionata.

1. Procedere al [passaggio 3: Nozioni di base](#step-3-basics).

### <a name="start-from-scratch"></a>Iniziare da zero

Se si preferisce, è possibile seguire questa procedura per avviare un ruolo personalizzato da zero.

1. Nella portale di Azure aprire una sottoscrizione o un gruppo di risorse in cui si desidera assegnare il ruolo personalizzato e quindi aprire controllo di **accesso (IAM)**.

1. Fare clic su **Aggiungi** e quindi su **Aggiungi ruolo personalizzato (anteprima)**.

    ![Menu Aggiungi ruolo personalizzato](./media/custom-roles-portal/add-custom-role-menu.png)

    Verrà aperto l'editor dei ruoli personalizzati con l'opzione **Avvia da zero** selezionata.

1. Procedere al [passaggio 3: Nozioni di base](#step-3-basics).

### <a name="start-from-json"></a>Inizia da JSON

Se si preferisce, è possibile specificare la maggior parte dei valori di ruolo personalizzati in un file JSON. È possibile aprire il file nell'editor dei ruoli personalizzati, apportare ulteriori modifiche e quindi creare il ruolo personalizzato. Seguire questa procedura per iniziare con un file JSON.

1. Creare un file JSON con il formato seguente:

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

1. Nel file JSON specificare i valori per le varie proprietà. Di seguito è riportato un esempio con alcuni valori aggiunti. Per informazioni sulle diverse proprietà, vedere [comprendere le definizioni dei ruoli](role-definitions.md).

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
    
1. Nella portale di Azure aprire la pagina **controllo di accesso (IAM)** .

1. Fare clic su **Aggiungi** e quindi su **Aggiungi ruolo personalizzato (anteprima)**.

    ![Menu Aggiungi ruolo personalizzato](./media/custom-roles-portal/add-custom-role-menu.png)

    Verrà aperto l'editor dei ruoli personalizzati.

1. Nella scheda nozioni di base, in **autorizzazioni di base**, selezionare **Avvia da JSON**.

1. Accanto alla casella Selezionare un file fare clic sul pulsante cartella per aprire la finestra di dialogo Apri.

1. Selezionare il file JSON e quindi fare clic su **Apri**.

1. Procedere al [passaggio 3: Nozioni di base](#step-3-basics).

## <a name="step-3-basics"></a>Passaggio 3: Nozioni fondamentali

Nella scheda **nozioni di base** specificare il nome, la descrizione e le autorizzazioni di base per il ruolo personalizzato.

1. Nella casella **nome ruolo personalizzato** specificare un nome per il ruolo personalizzato. Il nome deve essere univoco per la directory Azure AD. Il nome può includere lettere, numeri, spazi e caratteri speciali.

1. Nella casella **Descrizione** specificare una descrizione facoltativa per il ruolo personalizzato. Questa diventerà la descrizione comando per il ruolo personalizzato.

    L'opzione **delle autorizzazioni di base** deve essere già impostata in base al passaggio precedente, ma è possibile modificarla.

    ![Scheda nozioni di base con valori specificati](./media/custom-roles-portal/basics-values.png)

## <a name="step-4-permissions"></a>Passaggio 4: autorizzazioni

Nella scheda **autorizzazioni** specificare le autorizzazioni per il ruolo personalizzato. A seconda che il ruolo sia stato clonato o che sia stato avviato con JSON, è possibile che nella scheda autorizzazioni siano già elencate alcune autorizzazioni.

![Scheda autorizzazioni di Crea ruolo personalizzato](./media/custom-roles-portal/permissions.png)

### <a name="add-or-remove-permissions"></a>Aggiungi o Rimuovi autorizzazioni

Attenersi alla procedura seguente per aggiungere o rimuovere le autorizzazioni per il ruolo personalizzato.

1. Per aggiungere autorizzazioni, fare clic su **Aggiungi autorizzazioni** per aprire il riquadro Aggiungi autorizzazioni.

    In questo riquadro sono elencate tutte le autorizzazioni disponibili raggruppate in categorie diverse in un formato di scheda. Ogni categoria rappresenta un *provider di risorse*, ovvero un servizio che fornisce le risorse di Azure.

1. Nella casella **Cerca autorizzazione** Digitare una stringa in cui cercare le autorizzazioni. Ad esempio, cercare la *fattura* per trovare le autorizzazioni relative alla fattura.

    Verrà visualizzato un elenco di schede del provider di risorse in base alla stringa di ricerca. Per un elenco delle modalità di mapping dei provider di risorse ai servizi di Azure, vedere [provider di risorse per i servizi di Azure](../azure-resource-manager/management/azure-services-resource-providers.md).

    ![Riquadro Aggiungi autorizzazioni con provider di risorse](./media/custom-roles-portal/add-permissions-provider.png)

1. Fare clic su una scheda del provider di risorse che potrebbe avere le autorizzazioni che si desidera aggiungere al ruolo personalizzato, ad esempio **fatturazione Microsoft**.

    Viene visualizzato un elenco delle autorizzazioni di gestione per il provider di risorse in base alla stringa di ricerca.

    ![Aggiungi elenco di autorizzazioni](./media/custom-roles-portal/add-permissions-list.png)

1. Se si stanno cercando le autorizzazioni che si applicano al piano dati, fare clic su **azioni dati**. In caso contrario, lasciare le azioni impostate su **azioni** per elencare le autorizzazioni che si applicano al piano di gestione. Per ulteriori informazioni sulle differenze tra il piano di gestione e il piano dati, vedere [operazioni di gestione e dati](role-definitions.md#management-and-data-operations).

1. Se necessario, aggiornare la stringa di ricerca per affinare ulteriormente la ricerca.

1. Dopo aver individuato una o più autorizzazioni che si desidera aggiungere al ruolo personalizzato, aggiungere un segno di spunta accanto alle autorizzazioni. Ad esempio, aggiungere un segno di spunta accanto ad **altro: Scarica fattura** per aggiungere l'autorizzazione per scaricare le fatture.

1. Fare clic su **Aggiungi** per aggiungere l'autorizzazione all'elenco di autorizzazioni.

    L'autorizzazione viene aggiunta come `Actions` o. `DataActions`

    ![Autorizzazione aggiunta](./media/custom-roles-portal/permissions-list-add.png)

1. Per rimuovere le autorizzazioni, fare clic sull'icona Elimina alla fine della riga. In questo esempio, poiché un utente non deve avere la possibilità di creare ticket di supporto, `Microsoft.Support/*` l'autorizzazione può essere eliminata.

### <a name="add-wildcard-permissions"></a>Aggiungi autorizzazioni con caratteri jolly

A seconda della modalità con cui si è scelto di avviare, è possibile che nell'elenco\*di autorizzazioni siano disponibili le autorizzazioni con caratteri jolly (). Un carattere jolly\*() estende un'autorizzazione a tutti gli elementi che corrispondono alla stringa fornita. Si supponga, ad esempio, di voler aggiungere tutte le autorizzazioni relative a gestione costi di Azure ed esportazioni. È possibile aggiungere tutte le autorizzazioni seguenti:

```
Microsoft.CostManagement/exports/action
Microsoft.CostManagement/exports/read
Microsoft.CostManagement/exports/write
Microsoft.CostManagement/exports/delete
Microsoft.CostManagement/exports/run/action
```

Anziché aggiungere tutte queste autorizzazioni, è possibile aggiungere solo un'autorizzazione con caratteri jolly. Ad esempio, l'autorizzazione con caratteri jolly seguente equivale alle cinque autorizzazioni precedenti. Sono incluse anche eventuali autorizzazioni di esportazione future che potrebbero essere aggiunte.

```
Microsoft.CostManagement/exports/*
```

Se si desidera aggiungere una nuova autorizzazione con caratteri jolly, non è possibile aggiungerla utilizzando il riquadro **Aggiungi autorizzazioni** . Per aggiungere un'autorizzazione con caratteri jolly, è necessario aggiungerla manualmente usando la scheda **JSON** . Per altre informazioni, vedere [passaggio 6: JSON](#step-6-json).

### <a name="exclude-permissions"></a>Escludi autorizzazioni

Se il ruolo dispone di un'autorizzazione\*con carattere jolly () e si desidera escludere o sottrarre autorizzazioni specifiche da tale autorizzazione con caratteri jolly, è possibile escluderle. Si immagini, ad esempio, di disporre dell'autorizzazione con caratteri jolly seguente:

```
Microsoft.CostManagement/exports/*
```

Se non si desidera consentire l'eliminazione di un'esportazione, è possibile escludere l'autorizzazione di eliminazione seguente:

```
Microsoft.CostManagement/exports/delete
```

Quando si esclude un'autorizzazione, questa viene aggiunta come `NotActions` o. `NotDataActions` Le autorizzazioni di gestione effettive vengono calcolate aggiungendo tutti i `Actions` e quindi sottraendo tutti i `NotActions`. Le autorizzazioni effettive per i dati vengono calcolate aggiungendo tutti `DataActions` i e quindi sottraendo tutti i `NotDataActions`.

> [!NOTE]
> L'esclusione di un'autorizzazione non equivale a un oggetto Deny. L'esclusione delle autorizzazioni è semplicemente un modo pratico per sottrarre le autorizzazioni da un'autorizzazione con caratteri jolly.

1. Per escludere o sottrarre un'autorizzazione da un'autorizzazione con caratteri jolly consentiti, fare clic su **Escludi autorizzazioni** per aprire il riquadro Escludi autorizzazioni.

    In questo riquadro è possibile specificare le autorizzazioni di gestione o dei dati escluse o sottratte.

1. Dopo aver individuato una o più autorizzazioni da escludere, aggiungere un segno di spunta accanto alle autorizzazioni e quindi fare clic sul pulsante **Aggiungi** .

    ![Riquadro autorizzazioni di esclusione-autorizzazione selezionata](./media/custom-roles-portal/exclude-permissions-select.png)

    L'autorizzazione viene aggiunta come `NotActions` o. `NotDataActions`

    ![Autorizzazione esclusa](./media/custom-roles-portal/exclude-permissions-list-add.png)

## <a name="step-5-assignable-scopes"></a>Passaggio 5: ambiti assegnabili

Nella scheda **ambiti assegnabili** specificare la posizione in cui il ruolo personalizzato è disponibile per l'assegnazione, ad esempio sottoscrizione o gruppo di risorse. A seconda di come si è scelto di avviare, questa scheda potrebbe elencare l'ambito in cui è stata aperta la pagina controllo di accesso (IAM). L'impostazione dell'ambito assegnabile sull'ambito radice ("/") non è supportata. Per questa anteprima, non è possibile aggiungere un gruppo di gestione come ambito assegnabile.

1. Fare clic su **Aggiungi ambiti assegnabili** per aprire il riquadro Aggiungi ambiti assegnabili.

    ![Scheda ambiti assegnabili](./media/custom-roles-portal/assignable-scopes.png)

1. Fare clic su uno o più ambiti che si desidera utilizzare, in genere la sottoscrizione.

    ![Aggiungere ambiti assegnabili](./media/custom-roles-portal/add-assignable-scopes.png)

1. Fare clic sul pulsante **Aggiungi** per aggiungere l'ambito assegnabile.

## <a name="step-6-json"></a>Passaggio 6: JSON

Nella scheda **JSON** viene visualizzato il ruolo personalizzato formattato in JSON. Se lo si desidera, è possibile modificare direttamente il codice JSON. Se si desidera aggiungere un'autorizzazione con carattere\*jolly (), è necessario utilizzare questa scheda.

1. Per modificare il codice JSON, fare clic su **modifica**.

    ![Scheda JSON che mostra il ruolo personalizzato](./media/custom-roles-portal/json.png)

1. Apportare modifiche al file JSON.

    Se il formato JSON non è corretto, verrà visualizzata una linea irregolare rossa e un indicatore nella barra verticale.

1. Al termine della modifica, fare clic su **Salva**.

## <a name="step-7-review--create"></a>Passaggio 7: esaminare e creare

Nella scheda **Verifica e crea** è possibile esaminare le impostazioni del ruolo personalizzato.

1. Esaminare le impostazioni del ruolo personalizzato.

    ![Scheda Rivedi e crea](./media/custom-roles-portal/review-create.png)

1. Fare clic su **Crea** per creare il ruolo personalizzato.

    Dopo alcuni istanti, viene visualizzata una finestra di messaggio che indica che il ruolo personalizzato è stato creato correttamente.

    ![Creazione di un messaggio di ruolo personalizzato](./media/custom-roles-portal/custom-role-success.png)

    Se vengono rilevati errori, verrà visualizzato un messaggio.

    ![Verifica + crea errore](./media/custom-roles-portal/review-create-error.png)

1. Visualizzare il nuovo ruolo personalizzato nell'elenco **ruoli** . Se il ruolo personalizzato non è visibile, fare clic su **Aggiorna**.

     Potrebbero essere necessari alcuni minuti prima che il ruolo personalizzato venga visualizzato ovunque.

## <a name="list-custom-roles"></a>Elencare ruoli personalizzati

Per visualizzare i ruoli personalizzati, seguire questa procedura.

1. Aprire una sottoscrizione o un gruppo di risorse e quindi aprire **controllo di accesso (IAM)**.

1. Fare clic su **Ruoli** per visualizzare l'elenco di tutti i ruoli predefiniti e personalizzati.

1. Nell'elenco **tipo** selezionare **CustomRole** per visualizzare solo i ruoli personalizzati.

    Se il ruolo personalizzato è stato appena creato e non viene visualizzato nell'elenco, fare clic su **Aggiorna**.

    ![Elenco dei ruoli personalizzati](./media/custom-roles-portal/custom-role-list.png)

## <a name="update-a-custom-role"></a>Aggiornare un ruolo personalizzato

1. Come descritto in precedenza in questo articolo, aprire l'elenco dei ruoli personalizzati.

1. Fare clic sui puntini di sospensione (**..**.) per il ruolo personalizzato che si desidera aggiornare, quindi fare clic su **modifica**. Si noti che non è possibile aggiornare i ruoli predefiniti.

    Il ruolo personalizzato viene aperto nell'editor.

    ![Menu ruolo personalizzato](./media/custom-roles-portal/edit-menu.png)

1. Utilizzare le diverse schede per aggiornare il ruolo personalizzato.

1. Al termine delle modifiche, fare clic sulla scheda **Verifica + crea** per esaminare le modifiche.

1. Fare clic sul pulsante **Aggiorna** per aggiornare il ruolo personalizzato.

## <a name="delete-a-custom-role"></a>Eliminare un ruolo personalizzato

1. Come descritto in precedenza in questo articolo, aprire l'elenco dei ruoli personalizzati.

1. Rimuovere le assegnazioni di ruolo che usano il ruolo personalizzato.

1. Fare clic sui puntini di sospensione (**..**.) per il ruolo personalizzato che si desidera eliminare e quindi fare clic su **Elimina**.

    ![Menu ruolo personalizzato](./media/custom-roles-portal/delete-menu.png)

    Potrebbero essere necessari alcuni minuti prima che il ruolo personalizzato venga eliminato completamente.

## <a name="next-steps"></a>Passaggi successivi

- [Esercitazione: creare un ruolo personalizzato con Azure PowerShell](tutorial-custom-role-powershell.md)
- [Ruoli personalizzati in Azure](custom-roles.md)
- [Operazioni del provider di risorse Azure Resource Manager](resource-provider-operations.md)
