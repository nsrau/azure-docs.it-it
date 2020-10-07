---
title: "Avvio rapido: Creare un gruppo di gestione con l'interfaccia della riga di comando di Azure"
description: In questo argomento di avvio rapido si usa l'interfaccia della riga di comando di Azure per creare un gruppo di gestione con cui organizzare le risorse in una gerarchia.
ms.date: 08/31/2020
ms.topic: quickstart
ms.custom: devx-track-azurecli
ms.openlocfilehash: f07ae46c95f9ab9cc1ad973204ac5c50320fdf46
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/05/2020
ms.locfileid: "89236993"
---
# <a name="quickstart-create-a-management-group-with-the-azure-cli"></a>Avvio rapido: Creare un gruppo di gestione con l'interfaccia della riga di comando di Azure

I gruppi di gestione sono contenitori che semplificano la gestione dell'accesso, dei criteri e della conformità tra più sottoscrizioni. Creare questi contenitori per creare una gerarchia efficiente ed efficace utilizzabile con [Criteri di Azure](../policy/overview.md) e [Controllo degli accessi in base al ruolo di Azure](../../role-based-access-control/overview.md). Per altre informazioni sui gruppi di gestione, vedere [Organizzare le risorse con i gruppi di gestione di Azure](overview.md).

La creazione del primo gruppo di gestione nella directory può richiedere fino a 15 minuti. La prima volta vengono eseguiti processi che eseguono la configurazione del servizio dei gruppi di gestione all'interno di Azure per la directory. Al termine dei processi, si riceve una notifica. Per altre informazioni, vedere [Configurazione iniziale dei gruppi di gestione](./overview.md#initial-setup-of-management-groups).

## <a name="prerequisites"></a>Prerequisiti

- Se non si ha una sottoscrizione di Azure, creare un account [gratuito](https://azure.microsoft.com/free/) prima di iniziare.

- Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, questo argomento di avvio rapido richiede l'esecuzione dell'interfaccia della riga di comando di Azure versione 2.0.76 o successiva. Per trovare la versione, eseguire `az --version`. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli).

- Se la [protezione della gerarchia](./how-to/protect-resource-hierarchy.md#setting---require-authorization) non è abilitata, qualsiasi utente di Azure AD nel tenant può creare un gruppo di gestione senza avere autorizzazioni di scrittura assegnate per tale gruppo di gestione. Questo nuovo gruppo di gestione diventa un elemento figlio del gruppo di gestione radice o del [gruppo di gestione predefinito](./how-to/protect-resource-hierarchy.md#setting---default-management-group) e all'autore viene assegnato il ruolo "Proprietario". Il servizio del gruppo di gestione offre questa possibilità per evitare che siano necessarie assegnazioni di ruolo a livello di radice. Nessun utente ha accesso al gruppo di gestione radice quando viene creato. Per evitare il problema di reperire gli amministratori globali di Azure AD per iniziare a usare i gruppi di gestione, è consentita la creazione dei gruppi di gestione iniziali al livello radice.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

### <a name="create-in-the-azure-cli"></a>Creazione nell'interfaccia della riga di comando di Azure

Per l'interfaccia della riga di comando di Azure, usare il comando [az account management-group create](/cli/azure/account/management-group#az-account-management-group-create) per creare un nuovo gruppo di gestione. In questo esempio il valore di **name** del gruppo di gestione è _Contoso_.

```azurecli-interactive
az account management-group create --name 'Contoso'
```

Il valore di **name** è un identificatore univoco creato. Questo ID viene usato da altri comandi per fare riferimento a questo gruppo e non può essere cambiato in seguito.

Se si vuole visualizzare un nome diverso per il gruppo di gestione all'interno del portale di Azure, aggiungere il parametro **display-name**. Ad esempio, per creare un gruppo di gestione con GroupName Contoso e nome visualizzato "Contoso Group", usare il comando seguente:

```azurecli-interactive
az account management-group create --name 'Contoso' --display-name 'Contoso Group'
```

Negli esempi precedenti il nuovo gruppo di gestione viene creato nel gruppo di gestione radice. Per specificare un gruppo di gestione diverso come elemento padre, usare il parametro **parent** e specificare il nome del gruppo padre.

```azurecli-interactive
az account management-group create --name 'ContosoSubGroup' --parent 'Contoso'
```

## <a name="clean-up-resources"></a>Pulire le risorse

Per rimuovere il gruppo di gestione creato in precedenza, usare il comando [az account management-group delete](/cli/azure/account/management-group#az-account-management-group-delete):

```azurecli-interactive
az account management-group delete --name 'Contoso'
```

## <a name="next-steps"></a>Passaggi successivi

In questo argomento di avvio rapido è stato creato un gruppo di gestione per organizzare la gerarchia delle risorse. Il gruppo di gestione può contenere sottoscrizioni o altri gruppi di gestione.

Per altre informazioni sui gruppi di gestione e su come gestire la gerarchia delle risorse, continuare con:

> [!div class="nextstepaction"]
> [Gestire le risorse con i gruppi di gestione](./manage.md)