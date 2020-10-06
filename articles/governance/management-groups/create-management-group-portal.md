---
title: Creare un gruppo di gestione con il portale
description: In questo argomento di avvio rapido si usa il portale di Azure per creare un gruppo di gestione con cui organizzare le risorse in una gerarchia.
ms.date: 08/31/2020
ms.topic: quickstart
ms.openlocfilehash: c8cb7b8bd3ad33d1f315670bfbb1782972e6e97f
ms.sourcegitcommit: f8d2ae6f91be1ab0bc91ee45c379811905185d07
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/10/2020
ms.locfileid: "89661738"
---
# <a name="quickstart-create-a-management-group"></a>Avvio rapido: Creare un gruppo di gestione

I gruppi di gestione sono contenitori che semplificano la gestione dell'accesso, dei criteri e della conformità tra più sottoscrizioni. Creare questi contenitori per creare una gerarchia efficiente ed efficace utilizzabile con [Criteri di Azure](../policy/overview.md) e [Controllo degli accessi in base al ruolo di Azure](../../role-based-access-control/overview.md). Per altre informazioni sui gruppi di gestione, vedere [Organizzare le risorse con i gruppi di gestione di Azure](overview.md).

La creazione del primo gruppo di gestione nella directory può richiedere fino a 15 minuti. La prima volta vengono eseguiti processi che eseguono la configurazione del servizio dei gruppi di gestione all'interno di Azure per la directory. Al termine dei processi, si riceve una notifica. Per altre informazioni, vedere [Configurazione iniziale dei gruppi di gestione](./overview.md#initial-setup-of-management-groups).

## <a name="prerequisites"></a>Prerequisiti 

- Se non si ha una sottoscrizione di Azure, creare un account [gratuito](https://azure.microsoft.com/free/) prima di iniziare.

- Se la [protezione della gerarchia](./how-to/protect-resource-hierarchy.md#setting---require-authorization) non è abilitata, qualsiasi utente di Azure AD nel tenant può creare un gruppo di gestione senza avere autorizzazioni di scrittura assegnate per tale gruppo di gestione. Questo nuovo gruppo di gestione diventa un elemento figlio del gruppo di gestione radice o del [gruppo di gestione predefinito](./how-to/protect-resource-hierarchy.md#setting---default-management-group) e all'autore viene assegnato il ruolo "Proprietario". Il servizio del gruppo di gestione offre questa possibilità per evitare che siano necessarie assegnazioni di ruolo a livello di radice. Nessun utente ha accesso al gruppo di gestione radice quando viene creato. Per evitare il problema di reperire gli amministratori globali di Azure AD per iniziare a usare i gruppi di gestione, è consentita la creazione dei gruppi di gestione iniziali al livello radice.

### <a name="create-in-portal"></a>Creazione nel portale

1. Accedere al [portale di Azure](https://portal.azure.com).

1. Selezionare **Tutti i servizi** > **Gestione e governance**.

1. Selezionare **Gruppi di gestione**.

1. Selezionare **+ Aggiungi gruppo di gestione**.

   :::image type="content" source="./media/main.png" alt-text="Screenshot della pagina Gruppi di gestione che illustra le sottoscrizioni e i gruppi di gestione figlio." border="false":::

1. Lasciare selezionata l'opzione **Crea nuovo** e compilare il campo ID gruppo di gestione.

   - L'**ID del gruppo di gestione** è l'identificatore univoco della directory usato per inviare i comandi per questo gruppo di gestione. Questo identificatore non è modificabile dopo la creazione, perché viene usato nell'intero sistema di Azure per identificare questo gruppo. Il [gruppo di gestione radice](./overview.md#root-management-group-for-each-directory) viene creato automaticamente con un ID che corrisponde all'ID di Azure Active Directory. Per tutti gli altri gruppi di gestione, assegnare un ID univoco.
   - Il nome visualizzato è il nome che viene visualizzato nel portale di Azure. Un nome visualizzato separato è un campo facoltativo al momento della creazione del gruppo di gestione e può essere modificato in qualsiasi momento.

   :::image type="content" source="./media/create_context_menu.png" alt-text="Screenshot della pagina Gruppi di gestione che illustra le sottoscrizioni e i gruppi di gestione figlio.":::

1. Selezionare **Salva**.

## <a name="clean-up-resources"></a>Pulire le risorse

Per rimuovere il gruppo di gestione creato, seguire questa procedura:

1. Selezionare **Tutti i servizi** > **Gestione e governance**.

1. Selezionare **Gruppi di gestione**.

1. Trovare il gruppo di gestione creato, selezionarlo, quindi selezionare **Dettagli** accanto al nome.
   Quindi selezionare **Elimina** e confermare la richiesta.

## <a name="next-steps"></a>Passaggi successivi

In questo argomento di avvio rapido è stato creato un gruppo di gestione per organizzare la gerarchia delle risorse. Il gruppo di gestione può contenere sottoscrizioni o altri gruppi di gestione.

Per altre informazioni sui gruppi di gestione e su come gestire la gerarchia delle risorse, continuare con:

> [!div class="nextstepaction"]
> Gestire le risorse con i gruppi di gestione