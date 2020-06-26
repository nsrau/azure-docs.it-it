---
title: Avvio rapido - Creare un budget con un modello di Azure Resource Manager
description: Guida di avvio rapido che illustra come creare un budget con un modello di Azure Resource Manager.
author: bandersmsft
ms.author: banders
tags: azure-resource-manager
ms.service: cost-management-billing
ms.topic: quickstart
ms.date: 06/10/2020
ms.custom: subject-armqs
ms.openlocfilehash: dc37039d6777a77f9de247808329930f1621ee82
ms.sourcegitcommit: 537c539344ee44b07862f317d453267f2b7b2ca6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/11/2020
ms.locfileid: "84686410"
---
# <a name="quickstart-create-a-budget-with-an-azure-resource-manager-template"></a>Avvio rapido: Creare un budget con un modello di Azure Resource Manager

I budget in Gestione costi consentono di pianificare e promuovere la responsabilizzazione nell'organizzazione. Con i budget, è possibile tenere traccia dei costi sostenuti per i servizi di Azure consumati o sottoscritti per un determinato periodo. Risultano utili per informare altri utenti in merito alle spese per una gestione proattiva dei costi e per monitorare l'andamento della spesa nel tempo. Quando vengono superate le soglie di budget create, vengono attivate le notifiche. Nessuna delle risorse è interessata e il consumo non viene interrotto. È possibile usare i budget per confrontare e tenere traccia della spesa durante l'analisi dei costi. Questa guida di avvio rapido descrive come creare un budget usando un modello di Azure Resource Manager.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

## <a name="prerequisites"></a>Prerequisiti

Il modello di Azure Resource Manager supporta solo le sottoscrizioni di Azure per i contratti Enterprise (EA). Altri tipi di sottoscrizione non sono supportati dal modello.

Per creare e gestire i budget, è necessario disporre dell'autorizzazione di collaboratore. È possibile creare singoli budget per le sottoscrizioni EA e gruppi di risorse. Non è tuttavia possibile creare budget per gli account di fatturazione EA. Per le sottoscrizioni Azure EA è necessario avere accesso in lettura per visualizzare i budget.

Dopo aver creato un budget, per visualizzarlo, è necessario disporre almeno dell'accesso in lettura per l'account Azure.

Se si ha una nuova sottoscrizione, non è possibile creare immediatamente un budget o usare altre funzionalità di Gestione costi. Potrebbero essere necessarie fino a 48 ore prima di poter usarle usare tutte.

Le autorizzazioni o gli ambiti di Azure riportati di seguito sono supportati per i budget per ogni sottoscrizione, in base a utente e gruppo. Per altre informazioni sugli ambiti, vedere [Informazioni e utilizzo degli ambiti](understand-work-scopes.md).

- Proprietario - Può creare, modificare o eliminare budget per una sottoscrizione.
- Collaboratore e collaboratore Gestione costi: può creare, modificare o eliminare i propri budget. Può modificare l'importo di budget per budget creati da altri utenti.
- Lettore e lettore Gestione costi: può visualizzare i budget per cui ha l'autorizzazione.

Per altre informazioni sull'assegnazione dell'autorizzazione ai dati di Gestione costi, vedere [Assegnare l'accesso ai dati di Gestione costi](assign-access-acm-data.md).

## <a name="create-a-budget"></a>Creare un budget

### <a name="review-the-template"></a>Rivedere il modello

Il modello usato in questo avvio rapido proviene dai [modelli di avvio rapido di Azure](https://azure.microsoft.com/resources/templates/create-budget).

:::code language="json" source="~/quickstart-templates/create-budget/azuredeploy.json" range="1-146" highlight="110-139":::

Nel modello è definita una risorsa di Azure:

* [Microsoft.Consumption/budgets](/azure/templates/microsoft.consumption/budgets): crea un budget di Azure.

### <a name="deploy-the-template"></a>Distribuire il modello

1. Selezionare l'immagine seguente per accedere ad Azure e aprire un modello. Il modello crea un budget.

   [![Distribuzione in Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fcreate-budget%2Fazuredeploy.json)

2. Selezionare o immettere i valori seguenti.

   [![Modello di Resource Manager, creare un budget, portale per la distribuzione](./media/quick-create-budget-template/create-budget-using-template-portal.png)](./media/quick-create-budget-template/create-budget-using-template-portal.png#lightbox)

    * **Sottoscrizione**: selezionare una sottoscrizione di Azure.
    * **Gruppo di risorse**: selezionare **Crea nuovo**, immettere un nome univoco per il gruppo di risorse e quindi fare clic su **OK** o selezionare un gruppo di risorse esistente.
    * **Località**: selezionare una località. Ad esempio **Stati Uniti centrali**.
    * **Budget Name** (Nome del budget): immettere un nome per il budget. Deve essere univoco all'interno di un gruppo di risorse. Sono consentiti solo caratteri alfanumerici, caratteri di sottolineatura e trattini.
    * **Importo**: immettere l'importo totale o la quantità di utilizzo da monitorare con il budget.
    * **Budget Category** (Categoria budget): selezionare la categoria del budget, indipendentemente dal fatto che il budget monitori **Costi** o **Utilizzo**.
    * **Time Grain** (Intervallo di tempo): immettere l'intervallo di tempo per un budget. I valori consentiti sono Mensile, Trimestrale o Annuale. Il budget viene reimpostato alla fine dell'intervallo di tempo.
    * **Data di inizio**: immettere la data di inizio con il primo giorno del mese in formato AAAA-MM-GG. Una data di inizio futura non deve essere successiva a tre mesi dalla data odierna. È possibile specificare una data di inizio passata nel periodo dell'intervallo di tempo.
    * **Data di fine**: immettere la data di fine del budget nel formato AAAA-MM-GG. Se non viene specificata, il valore predefinito viene impostato su 10 anni dalla data di inizio.
    * **Operatore**: selezionare un operatore di confronto. I valori possibili sono EqualTo, GreaterThan o GreaterThanOrEqualTo.
    * **Soglia**: immettere un valore di soglia per la notifica. Quando il costo supera la soglia, viene inviata una notifica. Il valore è sempre percentuale e deve essere compreso tra 0 e 1000.
    * **Contact Emails** (Indirizzi di posta elettronica di contatto): immettere un elenco di indirizzi di posta elettronica a cui inviare la notifica del budget quando viene superata la soglia. Il formato previsto è `["user1@domain.com","user2@domain.com"]`.
    * **Contact Roles** (Ruoli di contatto): immettere l'elenco dei ruoli di contatto a cui inviare la notifica del budget quando viene superata la soglia. I valori predefiniti sono Proprietario, Collaboratore e Lettore. Il formato previsto è `["Owner","Contributor","Reader"]`.
    * **Contact Groups** (Gruppi di contatto): immettere un elenco di ID della risorsa di gruppi di azioni, come URI completi delle risorse, a cui inviare la notifica del budget quando viene superata la soglia. Accetta una matrice di stringhe. Il formato previsto è `["action group resource ID1","action group resource ID2"]`. Se non si vuole usare i gruppi di azioni, specificare `[]`.
    * **Resources Filter** (Filtro risorse): immettere un elenco di filtri per le risorse. Il formato previsto è `["Resource Filter Name1","Resource Filter Name2"]`. Se non si vuole applicare un filtro, immettere `[]`. Se si specifica un filtro delle risorse, è anche necessario immettere valori dei **filtri dei contatori**.
    * **Meters Filter** (Filtro contatori): immettere un elenco di filtri per i contatori, obbligatori per la categoria budget **Utilizzo**. Il formato previsto è `["Meter Filter Name1","Meter Filter Name2"]`. Se non è stato immesso un **filtro risorse**, immettere `[]`.
    * **Accetto le condizioni riportate sopra**: selezionare questa casella.

3. Selezionare **Acquisto**. Al termine della distribuzione del budget, si riceverà una notifica:

   ![Modello di Resource Manager, budget, notifica nel portale per la distribuzione](./media/quick-create-budget-template/resource-manager-template-portal-deployment-notification.png)

Per distribuire il modello, si usa il portale di Azure. Oltre al portale di Azure, è anche possibile usare Azure PowerShell, l'interfaccia della riga di comando di Azure e l'API REST. Per informazioni sugli altri modelli di distribuzione, vedere [Distribuire modelli](../../azure-resource-manager/templates/deploy-powershell.md).

## <a name="validate-the-deployment"></a>Convalidare la distribuzione

Per verificare che il budget sia stato creato nel portale di Azure, passare a **Gestione costi e fatturazione** > selezionare un ambito > **Budget**. In alternativa, per visualizzare il budget è possibile usare l'interfaccia della riga di comando di Azure o gli script di Azure PowerShell seguenti.

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
az consumption budget list
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
Get-AzConsumptionBudget
```

---

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non è più necessario un budget, eliminarlo utilizzando uno dei metodi seguenti:

### <a name="azure-portal"></a>Portale di Azure

Passare a **Gestione dei costi e fatturazione** > selezionare un ambito di fatturazione > **Budget** > selezionare un budget > quindi selezionare **Elimina budget**.

### <a name="command-line"></a>Riga di comando

È possibile rimuovere il budget usando l'interfaccia della riga di comando di Azure o Azure PowerShell.

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
echo "Enter the budget name:" &&
read budgetName &&
az consumption budget delete --budget-name $budgetName &&
echo "Press [ENTER] to continue ..."
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$budgetName = Read-Host -Prompt "Enter the budget name"
Remove-AzConsumptionBudget -Name $budgetName
Write-Host "Press [ENTER] to continue..."
```

---

## <a name="next-steps"></a>Passaggi successivi

In questa guida di avvio rapido è stato creato un budget di Azure per la distribuzione. Per altre informazioni su Gestione costi e fatturazione di Azure e Azure Resource Manager, continuare con gli articoli seguenti.

- Vedere la panoramica di [Gestione costi e fatturazione](../cost-management-billing-overview.md)
- [Creare budget](tutorial-acm-create-budgets.md) nel portale di Azure
- Vedere altre informazioni su [Azure Resource Manager](../../azure-resource-manager/management/overview.md)
