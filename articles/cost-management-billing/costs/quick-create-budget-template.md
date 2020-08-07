---
title: Avvio rapido - Creare un budget con un modello di Azure Resource Manager
description: Argomento di avvio rapido che illustra come creare un budget con un modello di Azure Resource Manager.
author: bandersmsft
ms.author: banders
tags: azure-resource-manager
ms.service: cost-management-billing
ms.topic: quickstart
ms.date: 07/28/2020
ms.custom: subject-armqs
ms.openlocfilehash: 70408a3ed6638ec76af113c24cc3c8190a44f55c
ms.sourcegitcommit: cee72954f4467096b01ba287d30074751bcb7ff4
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/30/2020
ms.locfileid: "87445995"
---
# <a name="quickstart-create-a-budget-with-an-arm-template"></a>Avvio rapido: Creare un budget con un modello di Resource Manager

I budget in Gestione costi consentono di pianificare e promuovere la responsabilizzazione nell'organizzazione. Con i budget, è possibile tenere traccia dei costi sostenuti per i servizi di Azure consumati o sottoscritti per un determinato periodo. Risultano utili per informare altri utenti in merito alle spese per una gestione proattiva dei costi e per monitorare l'andamento della spesa nel tempo. Quando vengono superate le soglie di budget create, vengono attivate le notifiche. Nessuna delle risorse è interessata e il consumo non viene interrotto. È possibile usare i budget per confrontare e tenere traccia della spesa durante l'analisi dei costi. Questo argomento di avvio rapido descrive come creare un budget usando un modello di Azure Resource Manager (modello di Resource Manager).

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

Se l'ambiente soddisfa i prerequisiti e si ha familiarità con l'uso dei modelli di Resource Manager, selezionare il pulsante **Distribuisci in Azure**. Il modello verrà aperto nel portale di Azure.

[![Distribuzione in Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fcreate-budget%2Fazuredeploy.json)

## <a name="prerequisites"></a>Prerequisiti

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

Se si ha una nuova sottoscrizione, non è possibile creare immediatamente un budget o usare altre funzionalità di Gestione costi. Potrebbero essere necessarie fino a 48 ore prima di poter usarle usare tutte.

I budget sono supportati per i tipi di account e gli ambiti di Azure seguenti:

- Ambiti di controllo degli accessi in base al ruolo di Azure
    - Gruppi di gestione
    - Subscription
- Ambiti del Contratto Enterprise
    - Account di fatturazione
    - department
    - Account di registrazione
- Singoli contratti
    - Account di fatturazione
- Ambiti del Contratto del cliente Microsoft
    - Account di fatturazione
    - Profilo di fatturazione
    - Sezione della fattura
    - Customer
- Scopi AWS
    - Account esterno
    - Sottoscrizione esterna

Per visualizzare i budget, è necessario effettuare almeno l'accesso in lettura per l'account Azure.

Per le sottoscrizioni Azure EA è necessario avere accesso in lettura per visualizzare i budget. Per creare e gestire i budget, è necessario disporre dell'autorizzazione di collaboratore.

Le autorizzazioni o gli ambiti di Azure riportati di seguito sono supportati per i budget per ogni sottoscrizione, in base a utente e gruppo. Per altre informazioni sugli ambiti, vedere [Informazioni e utilizzo degli ambiti](understand-work-scopes.md).

- Proprietario - Può creare, modificare o eliminare budget per una sottoscrizione.
- Collaboratore e collaboratore Gestione costi: può creare, modificare o eliminare i propri budget. Può modificare l'importo di budget per budget creati da altri utenti.
- Lettore e lettore Gestione costi: può visualizzare i budget per cui ha l'autorizzazione.

Per altre informazioni sull'assegnazione dell'autorizzazione ai dati di Gestione costi, vedere [Assegnare l'accesso ai dati di Gestione costi](assign-access-acm-data.md).

## <a name="review-the-template"></a>Rivedere il modello

Il modello usato in questo avvio rapido proviene dai [modelli di avvio rapido di Azure](https://azure.microsoft.com/resources/templates/create-budget).

:::code language="json" source="~/quickstart-templates/create-budget/azuredeploy.json" :::

Nel modello è definita una risorsa di Azure:

* [Microsoft.Consumption/budgets](/azure/templates/microsoft.consumption/budgets): crea un budget di Azure.

## <a name="deploy-the-template"></a>Distribuire il modello

1. Selezionare l'immagine seguente per accedere ad Azure e aprire un modello. Il modello crea un budget.

   [![Distribuzione in Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fcreate-budget%2Fazuredeploy.json)

2. Selezionare o immettere i valori seguenti.

   :::image type="content" source="./media/quick-create-budget-template/create-budget-using-template-portal.png" alt-text="Modello di Resource Manager, creare un budget, portale per la distribuzione]" lightbox="./media/quick-create-budget-template/create-budget-using-template-portal.png" :::
   
    * **Sottoscrizione**: selezionare una sottoscrizione di Azure.
    * **Gruppo di risorse**: se necessario, selezionare un gruppo di risorse esistente oppure scegliere **Crea nuovo**.
    * **Area**: selezionare un'area di Azure. Ad esempio **Stati Uniti centrali**.
    * **Budget Name** (Nome del budget): immettere un nome per il budget. Deve essere univoco all'interno di un gruppo di risorse. Sono consentiti solo caratteri alfanumerici, caratteri di sottolineatura e trattini.
    * **Importo**: immettere l'importo totale dei costi da monitorare con il budget.
    * **Time Grain** (Intervallo di tempo): immettere l'intervallo di tempo per un budget. I valori consentiti sono Mensile, Trimestrale o Annuale. Il budget viene reimpostato alla fine dell'intervallo di tempo.
    * **Data di inizio**: immettere la data di inizio con il primo giorno del mese in formato AAAA-MM-GG. Una data di inizio futura non deve essere successiva a tre mesi dalla data odierna. È possibile specificare una data di inizio passata nel periodo dell'intervallo di tempo.
    * **Data di fine**: immettere la data di fine del budget nel formato AAAA-MM-GG. 
    * **First Threshold** (Prima soglia): immettere un valore di soglia per la prima notifica. Quando il costo supera la soglia, viene inviata una notifica. Il valore è sempre percentuale e deve essere compreso tra 0 e 1000.
    * **Second Threshold** (Seconda soglia): immettere un valore di soglia per la seconda notifica. Quando il costo supera la soglia, viene inviata una notifica. Il valore è sempre percentuale e deve essere compreso tra 0 e 1000.
    * **Contact Roles** (Ruoli di contatto): immettere l'elenco dei ruoli di contatto a cui inviare la notifica del budget quando viene superata la soglia. I valori predefiniti sono Proprietario, Collaboratore e Lettore. Il formato previsto è `["Owner","Contributor","Reader"]`.
    * **Contact Emails** (Indirizzi di posta elettronica di contatto): immettere un elenco di indirizzi di posta elettronica a cui inviare la notifica del budget quando viene superata una soglia. Il formato previsto è `["user1@domain.com","user2@domain.com"]`.
    * **Contact Groups** (Gruppi di contatto): immettere un elenco di ID della risorsa di gruppi di azioni, come URI completi delle risorse, a cui inviare la notifica del budget quando viene superata la soglia. Accetta una matrice di stringhe. Il formato previsto è `["action group resource ID1","action group resource ID2"]`. Se non si vuole usare i gruppi di azioni, specificare `[]`.
    * **Resource Group Filter Values** (Valori filtro per i gruppi di risorse): immettere un elenco dei nomi di gruppi di risorse da filtrare. Il formato previsto è `["Resource Group Name1","Resource Group Name2"]`. Se non si vuole applicare un filtro, immettere `[]`. 
    * **Meter Category Filter Values** (Valori filtro per le categorie dei contatori): immettere un elenco di categorie dei contatori dei servizi di Azure. Il formato previsto è `["Meter Category1","Meter Category2"]`. Se non si vuole applicare un filtro, immettere `[]`.
   
3. In base al tipo di sottoscrizione di Azure, eseguire una di queste azioni:
   - Selezionare **Rivedi e crea**.
   - Esaminare le condizioni, selezionare **Accetto le condizioni riportate sopra** e quindi fare clic su **Acquista**.

4. Se si seleziona **Rivedi e crea**, il modello verrà convalidato. Selezionare **Crea**.  

   ![Modello di Resource Manager, budget, notifica nel portale per la distribuzione](./media/quick-create-budget-template/resource-manager-template-portal-deployment-notification.png)

Per distribuire il modello, si usa il portale di Azure. Oltre al portale di Azure, è anche possibile usare Azure PowerShell, l'interfaccia della riga di comando di Azure e l'API REST. Per informazioni sugli altri modelli di distribuzione, vedere [Distribuire modelli](../../azure-resource-manager/templates/deploy-powershell.md).

## <a name="validate-the-deployment"></a>Convalidare la distribuzione

Per verificare che il budget sia stato creato nel portale di Azure, passare a **Gestione costi e fatturazione** > selezionare un ambito > **Budget**. In alternativa, per visualizzare il budget è possibile usare l'interfaccia della riga di comando di Azure o gli script di Azure PowerShell seguenti.

# <a name="cli"></a>[Interfaccia della riga di comando](#tab/CLI)

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

In questo argomento di avvio rapido è stato creato un budget di Azure per la distribuzione. Per altre informazioni su Gestione costi e fatturazione di Azure e Azure Resource Manager, continuare con gli articoli seguenti.

- Vedere la panoramica di [Gestione costi e fatturazione](../cost-management-billing-overview.md)
- [Creare budget](tutorial-acm-create-budgets.md) nel portale di Azure
- Vedere altre informazioni su [Azure Resource Manager](../../azure-resource-manager/management/overview.md)
