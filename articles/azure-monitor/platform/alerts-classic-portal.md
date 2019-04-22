---
title: Creare, visualizzare e gestire avvisi classici per le metriche con Monitoraggio di Azure
description: Informazioni su come usare il portale di Azure, l'interfaccia della riga di comando o Powershell per creare, visualizzare e gestire regole di avviso classico per le metriche.
author: snehithm
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/18/2018
ms.author: snmuvva
ms.openlocfilehash: 4a225dbc8e84d65a6ea25f63627599e5bb7d2ced
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2019
ms.locfileid: "59785325"
---
# <a name="create-view-and-manage-classic-metric-alerts-using-azure-monitor"></a>Creare, visualizzare e gestire avvisi classici per le metriche con Monitoraggio di Azure

Gli avvisi delle metriche classici in Monitoraggio di Azure consentono di ricevere una notifica quando una delle metriche supera una soglia. Avvisi delle metriche classici è una funzionalità precedente che consente di creare avvisi solo per le metriche non dimensionali. È disponibile una funzionalità più recente esistenti denominata Avvisi delle metriche con funzionalità migliorate rispetto agli avvisi delle metriche classici. Altre informazioni sulla nuova funzionalità degli avvisi delle metriche classiche sono disponibili nella [panoramica degli avvisi delle metriche](../../azure-monitor/platform/alerts-metric-overview.md). In questo articolo verrà illustrato come creare, visualizzare e gestire le regole di avviso classico per le metriche tramite il portale di Azure, l'interfaccia della riga di comando di Azure e Powershell.

## <a name="with-azure-portal"></a>Con il portale di Azure

1. Nel [portale](https://portal.azure.com/) individuare la risorsa da monitorare e quindi selezionarla.

2. Nella sezione **MONITORAGGIO** selezionare **Avvisi (versione classica)**. Il testo e l'icona possono variare leggermente per le diverse risorse. Se l'opzione **Avvisi (versione classica)** non è disponibile, cercarla in **Avvisi** o **Regole di avviso**.

    ![Monitoraggio](media/alerts-classic-portal/AlertRulesButton.png)

3. Selezionare il comando **Aggiungi avviso per la metrica (versione classica)** e compilare i campi.

    ![Aggiungi avviso](media/alerts-classic-portal/AddAlertOnlyParamsPage.png)

4. Specificare un valore per **Nome** per la regola di avviso. Compilare il campo **Descrizione**. La descrizione viene visualizzata anche nei messaggi di posta elettronica di notifica.

5. In **Metrica** selezionare la metrica da monitorare. Selezionare i valori relativi a **Condizione** e **Soglia** per la metrica. Scegliere inoltre il **Periodo** di tempo entro il quale la regola della metrica deve essere soddisfatta prima dell'attivazione dell'avviso. Se, ad esempio, si usa il periodo "Negli ultimi 5 minuti" e l'avviso deve rilevare un utilizzo della CPU superiore all'80%, l'avviso si attiva quando la CPU resta costantemente sopra all'80% per 5 minuti. Dopo la prima attivazione, l'avviso si attiverà di nuovo quando la CPU resta al di sotto dell'80% per 5 minuti. La misurazione della metrica relativa alla CPU avviene ogni minuto.

6. Selezionare **Invia messaggio di posta elettronica a proprietari, collaboratori e lettori** se si vuole che gli amministratori e i coamministratori ricevano una notifica tramite posta elettronica quando si attiva l'avviso.

7. Per aggiungere altri indirizzi di posta elettronica ai quali inviare una notifica quando viene attivato l'avviso, usare il campo **Indirizzi di posta elettronica aggiuntivi dell'amministratore**. Separare più messaggi di posta elettronica con punti e virgola, nel formato seguente: *messaggio di posta elettronica\@contoso.com;email2\@contoso.com*

8. Inserire un URI valido nel campo **Webhook** per eseguire la chiamata quando viene attivato l'avviso.

9. Se si usa Automazione di Azure, è possibile selezionare un runbook da eseguire quando viene attivato l'avviso.

10. Fare clic su **OK** per creare l'avviso.

Dopo pochi minuti l'avviso è funzionante e si attiva come descritto in precedenza.

Dopo aver creato un avviso, è possibile selezionarlo ed eseguire una delle attività seguenti:

* Visualizzare un grafico che mostra la soglia della metrica e i valori effettivi del giorno precedente.
* Modificarlo o eliminarlo.
* **Disabilitarlo** o **abilitarlo** per interrompere temporaneamente o riprendere la ricezione delle notifiche relative all'avviso.

## <a name="with-azure-cli"></a>Con l'interfaccia della riga di comando di Azure

Le sezioni precedenti descrivono come creare, visualizzare e gestire le regole di avviso per le metriche tramite il portale di Azure. Questa sezione descrive come eseguire la stessa operazione usando l'[interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest) multipiattaforma. [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview?view=azure-cli-latest) è il metodo più rapido per iniziare a usare l'interfaccia della riga di comando di Azure.

### <a name="get-all-classic-metric-alert-rules-in-a-resource-group"></a>Ottenere tutte le regole di avviso classico per le metriche in un gruppo di risorse

```azurecli
az monitor alert list --resource-group <group name>
```

### <a name="see-details-of-a-particular-classic-metric-alert-rule"></a>Visualizzare i dettagli di una particolare regole di avviso classico per le metriche

```azurecli
az monitor alert show --resource-group <group name> --name <alert name>
```

### <a name="create-a-classic-metric-alert-rule"></a>Creare una regola di avviso classico per le metriche

```azurecli
az monitor alert create --name <alert name> --resource-group <group name> \
    --action email <email1 email2 ...> \
    --action webhook <URI> \
    --target <target object ID> \
    --condition "<METRIC> {>,>=,<,<=} <THRESHOLD> {avg,min,max,total,last} ##h##m##s"
```

### <a name="delete-a-classic-metric-alert-rule"></a>Eliminare una regola di avviso classico per le metriche

```azurecli
az monitor alert delete --name <alert name> --resource-group <group name>
```

## <a name="with-powershell"></a>Con PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Questa sezione illustra come usare i comandi PowerShell per creare, visualizzare e gestire gli avvisi delle metriche classiche. Gli esempi in questo articolo illustrano come usare i cmdlet di Monitoraggio di Azure per gli avvisi classici per le metriche.

1. Se non è ancora stato fatto, configurare PowerShell per l'esecuzione sul computer. Per altre informazioni, vedere [Come installare e configurare PowerShell](/powershell/azure/overview). È anche possibile esaminare l'elenco completo di cmdlet di PowerShell di Monitoraggio di Azure nell'argomento relativo ai [cmdlet di Monitoraggio di Azure(Azure Insights)](https://docs.microsoft.com/powershell/module/az.applicationinsights).

2. Per prima cosa, accedere alla sottoscrizione di Azure.

    ```powershell
    Connect-AzAccount
    ```

3. Verrà visualizzata una schermata di accesso. Dopo aver effettuato l'accesso, vengono visualizzati l'account, l'ID tenant e l'ID della sottoscrizione predefinito. Tutti i cmdlet di Azure funzionano nel contesto della sottoscrizione predefinita. Per visualizzare l'elenco delle sottoscrizioni accessibili, usare il comando seguente:

    ```powershell
    Get-AzSubscription
    ```

4. Per modificare il contesto di lavoro in una sottoscrizione diversa, usare il comando seguente:

    ```powershell
    Set-AzContext -SubscriptionId <subscriptionid>
    ```

5. È possibile recuperare tutte le regole di avviso classico per la metrica in un gruppo di risorse:

    ```powershell
    Get-AzAlertRule -ResourceGroup montest
    ```

6. È possibile visualizzare i dettagli di una regole di avviso classico per le metriche

    ```powershell
    Get-AzAlertRule -Name simpletestCPU -ResourceGroup montest -DetailedOutput
    ```

7. È possibile recuperare tutte le regole di avviso impostate per una risorsa di destinazione. Ad esempio, tutte le regole di avviso impostate su una VM.

    ```powershell
    Get-AzAlertRule -ResourceGroup montest -TargetResourceId /subscriptions/s1/resourceGroups/montest/providers/Microsoft.Compute/virtualMachines/testconfig
    ```

8. Le regole di avviso classiche non possono più essere create tramite PowerShell. Per creare una regola di avviso è necessario usare le nuove ['Add-AzMetricAlertRule'](/powershell/module/az.monitor/add-azmetricalertrule) comando.

## <a name="next-steps"></a>Passaggi successivi

- [Creare un avviso di metrica classico con un modello di Resource Manager](../../azure-monitor/platform/alerts-enable-template.md).
- [Impostare un avviso di metrica classico per l'invio di una notifica a un sistema non Azure tramite un webhook](../../azure-monitor/platform/alerts-webhooks.md).
