---
title: Creare un endpoint privato in collegamento privato di Azure
description: In questo argomento di avvio rapido si usa un modello di Azure Resource Manager per creare un endpoint privato.
services: private-link
author: mblanco77
ms.service: private-link
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 05/26/2020
ms.author: allensu
ms.openlocfilehash: 553d36a86671617417b6b9b1ea47966c3ba3fdf6
ms.sourcegitcommit: e0785ea4f2926f944ff4d65a96cee05b6dcdb792
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/21/2020
ms.locfileid: "88705810"
---
# <a name="quickstart-create-a-private-endpoint-by-using-an-arm-template"></a>Avvio rapido: Creare un endpoint privato con un modello di Azure Resource Manager

In questo argomento di avvio rapido si usa un modello di Azure Resource Manager per creare un endpoint privato.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

È anche possibile completare questa guida di avvio rapido usando il [portale di Azure](create-private-endpoint-portal.md), [Azure PowerShell](create-private-endpoint-powershell.md) o l'[interfaccia della riga di comando di Azure](create-private-endpoint-cli.md).

Se l'ambiente soddisfa i prerequisiti e si ha familiarità con l'uso dei modelli di Resource Manager, selezionare il pulsante **Distribuisci in Azure**. Il modello verrà aperto nel portale di Azure.

[![Distribuzione in Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-private-endpoint-sql%2Fazuredeploy.json)

## <a name="prerequisites"></a>Prerequisiti

È necessario un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="review-the-template"></a>Rivedere il modello

Questo modello crea un endpoint privato per un'istanza del database SQL di Azure.

Il modello usato in questo avvio rapido proviene dai [modelli di avvio rapido di Azure](https://azure.microsoft.com/resources/templates/101-private-endpoint-sql/).

:::code language="json" source="~/quickstart-templates/101-private-endpoint-sql/azuredeploy.json":::

Nel modello sono definite più risorse di Azure:

- [**Microsoft.Sql/servers**](/azure/templates/microsoft.sql/servers): istanza del database SQL con il database di esempio.
- [**Microsoft.Sql/servers/databases**](/azure/templates/microsoft.sql/servers/databases): database di esempio.
- [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks): rete virtuale in cui è distribuito l'endpoint privato.
- [**Microsoft.Network/privateEndpoints**](/azure/templates/microsoft.network/privateendpoints): endpoint privato per accedere all'istanza del database SQL.
- [**Microsoft.Network/privateDnsZones**](/azure/templates/microsoft.network/privatednszones): zona usata per risolvere l'indirizzo IP dell'endpoint privato.
- [**Microsoft.Network/privateDnsZones/virtualNetworkLinks**](/azure/templates/microsoft.network/privatednszones/virtualnetworklinks)
- [**Microsoft.Network/privateEndpoints/privateDnsZoneGroups**](/azure/templates/microsoft.network/privateendpoints/privateDnsZoneGroups): gruppo di zone usato per associare l'endpoint privato a una zona DNS privato.
- [**Microsoft.Network/publicIpAddresses**](/azure/templates/microsoft.network/publicIpAddresses): indirizzo IP pubblico usato per accedere alla macchina virtuale.
- [**Microsoft.Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces): interfaccia di rete per la macchina virtuale.
- [**Microsoft.Compute/virtualMachines**](/azure/templates/microsoft.compute/virtualmachines): macchina virtuale usata per testare la connessione privata con endpoint privato all'istanza del database SQL.

## <a name="deploy-the-template"></a>Distribuire il modello

Ecco come distribuire il modello di Resource Manager in Azure:

1. Selezionare **Distribuisci in Azure** per accedere ad Azure e aprire il modello. Il modello crea l'endpoint privato, l'istanza del database SQL, l'infrastruttura di rete e una macchina virtuale da convalidare.

   [![Distribuzione in Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-private-endpoint-sql%2Fazuredeploy.json)

2. Selezionare o creare un gruppo di risorse.
3. Digitare l'account di accesso e la password dell'amministratore SQL.
4. Specificare il nome utente e la password dell'amministratore della macchina virtuale.
5. Esaminare le condizioni per l'utilizzo del servizio. Per accettarle, selezionare **Accetto le condizioni riportate sopra** > **Acquista**. Il completamento della distribuzione può richiedere più di 20 minuti.

## <a name="validate-the-deployment"></a>Convalidare la distribuzione

> [!NOTE]
> Il modello di Azure Resource Manager genera un nome univoco per la risorsa macchina virtuale myVm<b>{UniqueId}</b> e per la risorsa database SQL SqlServer<b>{UniqueId}</b>. Sostituire il valore generato per **{uniqueid}** .

### <a name="connect-to-a-vm-from-the-internet"></a>Connettersi a una VM da Internet

Connettersi alla macchina virtuale _myVm{uniqueid}_ da Internet come indicato di seguito:

1. Nella barra di ricerca del portale immettere _myVm{uniqueid}_ .

2. Selezionare **Connetti**. Verrà visualizzata la finestra **Connetti alla macchina virtuale**.

3. Selezionare **Scarica file RDP**. Azure crea e scarica nel computer un file Remote Desktop Protocol con estensione _.rdp_.

4. Aprire il file con estensione rdp scaricato.

   a. Quando richiesto, selezionare **Connetti**.

   b. Immettere il nome utente e la password specificati quando è stata creata la macchina virtuale.

      > [!NOTE]
      > Potrebbe essere necessario selezionare **Altre opzioni** > **Usa un account diverso** per specificare le credenziali immesse al momento della creazione della macchina virtuale.

5. Selezionare **OK**.

6. Durante il processo di accesso potrebbe essere visualizzato un avviso relativo al certificato. Se si riceve un avviso relativo al certificato, selezionare **Sì** oppure **Continua**.

7. Quando viene visualizzato il desktop della macchina virtuale, ridurlo a icona per tornare al desktop locale.

### <a name="access-the-sql-database-server-privately-from-the-vm"></a>Accedere al server di database SQL privatamente dalla macchina virtuale

Di seguito viene illustrata la procedura per la connessione al server di database SQL dalla macchina virtuale tramite l'endpoint privato.

1.  Nel Desktop remoto di _myVM{uniqueid}_ aprire PowerShell.
2.  Immettere quanto segue: nslookup sqlserver{uniqueid}.database.windows.net. 
    Verrà visualizzato un messaggio simile al seguente:

    ```
      Server:  UnKnown
      Address:  168.63.129.16
      Non-authoritative answer:
      Name:    sqlserver.privatelink.database.windows.net
      Address:  10.0.0.5
      Aliases:  sqlserver.database.windows.net
    ```

3.  Installare SQL Server Management Studio.
4.  In **Connetti a server** immettere o selezionare queste informazioni:
    - **Tipo di server**: Selezionare **Motore di database**.
    - **Nome server**: Selezionare **sqlserver{uniqueid}.database.windows.net**.
    - **Nome utente**: immettere il nome utente specificato durante la creazione.
    - **Password**: immettere la password specificata durante la creazione.
    - **Memorizza la password**: Selezionare  **Sì**.

5.  Selezionare **Connetti**.
6.  Dal menu a sinistra passare a **Database**.
7.  Facoltativamente, è possibile creare o eseguire query sulle informazioni di _sample-db_.
8.  Chiudere la connessione Desktop remoto a _myVm{uniqueid}_ .

## <a name="clean-up-resources"></a>Pulire le risorse

Quando le risorse create con l'endpoint privato non sono più necessarie, eliminare il gruppo di risorse. Oltre all'endpoint privato verranno rimosse tutte le risorse correlate.

Per eliminare il gruppo di risorse, chiamare il cmdlet `Remove-AzResourceGroup`:

```azurepowershell-interactive
Remove-AzResourceGroup -Name <your resource group name>
```

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni su [Collegamento privato di Azure](private-link-overview.md).
