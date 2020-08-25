---
title: Argomento di avvio rapido sulla distribuzione automatizzata di VM con Configurazione app di Azure
description: Questo argomento di avvio rapido illustra come usare il modulo di Azure PowerShell e i modelli di Azure Resource Manager per distribuire un archivio di Configurazione app di Azure. I valori dell'archivio verranno quindi usati per distribuire una VM.
author: lisaguthrie
ms.author: lcozzens
ms.date: 08/11/2020
ms.topic: quickstart
ms.service: azure-app-configuration
ms.custom:
- mvc
- subject-armqs
ms.openlocfilehash: 9b609d4571d6240f428a0210aa5108ff19dc753b
ms.sourcegitcommit: 3bf69c5a5be48c2c7a979373895b4fae3f746757
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/14/2020
ms.locfileid: "88235180"
---
# <a name="quickstart-automated-vm-deployment-with-app-configuration-and-resource-manager-template-arm-template"></a>Avvio rapido: Distribuzione automatizzata di macchine virtuali con Configurazione app e un modello di Azure Resource Manager

Informazioni su come usare i modelli di Azure Resource Manager e Azure PowerShell per distribuire un archivio di Configurazione app di Azure, come aggiungere le coppie chiave-valore nell'archivio e come usare le coppie chiave-valore nell'archivio per distribuire una risorsa di Azure come una macchina virtuale di Azure in questo esempio.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Se l'ambiente soddisfa i prerequisiti e si ha familiarità con l'uso dei modelli di Resource Manager, selezionare il pulsante **Distribuisci in Azure**. Il modello verrà aperto nel portale di Azure.

[![Distribuzione in Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-app-configuration-store%2Fazuredeploy.json)

## <a name="prerequisites"></a>Prerequisiti

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="review-the-templates"></a>Esaminare i modelli

I modelli usati in questo argomento di avvio rapido provengono dai [modelli di avvio rapido di Azure](https://azure.microsoft.com/resources/templates/). Il [primo modello](https://azure.microsoft.comresources/templates/101-app-configuration-store/) crea un archivio di Configurazione app:

:::code language="json" source="~/quickstart-templates/101-app-configuration-store/azuredeploy.json" range="1-37" highlight="27-35":::

Nel modello è definita una risorsa di Azure:

- [Microsoft.AppConfiguration/configurationStores](/azure/templates/microsoft.appconfiguration/2019-10-01/configurationstores): crea un archivio di Configurazione app.

Il [secondo modello](https://azure.microsoft.com/resources/templates/101-app-configuration/) crea una macchina virtuale usando le coppie chiave-valore dell'archivio. Prima di questo passaggio, è necessario aggiungere le coppie chiave-valore tramite il portale o l'interfaccia della riga di comando di Azure.

:::code language="json" source="~/quickstart-templates/101-app-configuration/azuredeploy.json" range="1-217" highlight="77, 181,189":::

## <a name="deploy-the-templates"></a>Distribuire i modelli

### <a name="create-an-app-configuration-store"></a>Creare un archivio di Configurazione app

1. Selezionare l'immagine seguente per accedere ad Azure e aprire un modello. Il modello crea un archivio di Configurazione app.

    [![Distribuzione in Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-app-configuration-store%2Fazuredeploy.json)

1. Selezionare o immettere i valori seguenti.

    - **Sottoscrizione**: selezionare la sottoscrizione di Azure usata per creare l'archivio di Configurazione app.
    - **Gruppo di risorse**: selezionare **Crea nuovo** per creare un nuovo gruppo di risorse a meno che non si voglia usare un gruppo di risorse esistente.
    - **Area**: selezionare una località per il gruppo di risorse.  Ad esempio **Stati Uniti orientali**.
    - **Nome archivio di configurazione**: immettere un nuovo nome per l'archivio di Configurazione app.
    - **Posizione**: specificare la posizione dell'archivio di Configurazione app.  Usare il valore predefinito.
    - **Nome SKU**: specificare il nome dello SKU dell'archivio di Configurazione app. Usare il valore predefinito.

1. Selezionare **Rivedi e crea**.
1. Verificare che nella pagina venga visualizzato **Convalida superata** e quindi selezionare **Crea**.

Prendere nota del nome del gruppo di risorse e del nome dell'archivio di Configurazione app.  Questi valori saranno necessari per distribuire la macchina virtuale
### <a name="add-vm-configuration-key-values"></a>Aggiungere le coppie chiave-valore di configurazione della VM

Dopo aver creato un archivio di Configurazione app, è possibile usare il portale di Azure o l'interfaccia della riga di comando di Azure per aggiungere le coppie chiave-valore all'archivio.

1. Accedere al [portale di Azure](https://portal.azure.com) e quindi passare all'archivio di Configurazione app appena creato.
1. Selezionare **Esplora configurazioni** nel menu a sinistra.
1. Selezionare **Crea** per aggiungere le coppie chiave-valore seguenti:

   |Chiave|Valore|Etichetta|
   |-|-|-|
   |windowsOsVersion|2019-Datacenter|template|
   |diskSizeGB|1023|template|

   Lasciare vuoto il campo **Tipo di contenuto**.

Per usare l'interfaccia della riga di comando di Azure, vedere [Usare coppie chiave-valore in un archivio di Configurazione app di Azure](./scripts/cli-work-with-keys.md).

### <a name="deploy-vm-using-stored-key-values"></a>Distribuire una VM con le coppie chiave-valore archiviate

Ora che sono state aggiunte le coppie chiave-valore nell'archivio, è possibile distribuire una VM usando un modello di Azure Resource Manager. Il modello fa riferimento alle chiavi **windowsOsVersion** e **diskSizeGB** create.

> [!WARNING]
> I modelli di Azure Resource Manager non possono fare riferimento alle chiavi in un archivio di Configurazione app con collegamento privato abilitato.

1. Selezionare l'immagine seguente per accedere ad Azure e aprire un modello. Il modello crea una macchina virtuale usando le coppie chiave-valore archiviate nell'archivio di Configurazione app.

    [![Distribuzione in Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-app-configuration%2Fazuredeploy.json)

1. Selezionare o immettere i valori seguenti.

    - **Sottoscrizione**: selezionare la sottoscrizione di Azure usata per creare la macchina virtuale.
    - **Gruppo di risorse**: specificare lo stesso gruppo di risorse dell'archivio di Configurazione app oppure selezionare **Crea nuovo** per creare un nuovo gruppo di risorse.
    - **Area**: selezionare una località per il gruppo di risorse.  Ad esempio **Stati Uniti orientali**.
    - **Posizione**: specificare la posizione della macchina virtuale. Usare il valore predefinito.
    - **Nome utente amministratore**: specificare un nome utente amministratore per la macchina virtuale.
    - **Password amministratore**: specificare la password di amministratore per la macchina virtuale.
    - **Etichetta del nome di dominio**: specificare un nome di dominio univoco.
    - **Nome dell'account di archiviazione**: specificare un nome univoco per un account di archiviazione associato alla macchina virtuale.
    - **Gruppo di risorse dell'archivio di Configurazione app**: specificare il gruppo di risorse che contiene l'archivio di Configurazione app.
    - **Nome dell'archivio di Configurazione app**: specificare il nome dell'archivio di Configurazione app di Azure.
    - **Chiave SKU della macchina virtuale**: specificare **windowsOsVersion**.  Si tratta del nome del valore della chiave aggiunto all'archivio.
    - **Chiave dimensioni disco**: specificare **diskSizeGB**. Si tratta del nome del valore della chiave aggiunto all'archivio.

1. Selezionare **Rivedi e crea**.
1. Verificare che nella pagina venga visualizzato **Convalida superata** e quindi selezionare **Crea**.

## <a name="review-deployed-resources"></a>Esaminare le risorse distribuite

1. Accedere al [portale di Azure](https://portal.azure.com) e quindi passare alla macchina virtuale appena creata.
1. Selezionare **Panoramica** nel menu a sinistra e verificare che lo **SKU** sia **2019-Datacenter**.
1. Selezionare **Dischi** nel menu a sinistra e verificare che il valore delle dimensioni del disco dati sia **2013**.

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non sono più necessari, eliminare il gruppo di risorse, l'archivio di Configurazione app, la VM e tutte le risorse correlate. Se si prevede di usare l'archivio di Configurazione app o la VM in futuro, è possibile evitare di eliminarli. Se non si intende continuare a usare il processo, eliminare tutte le risorse create con questa guida introduttiva eseguendo il cmdlet seguente:

```azurepowershell-interactive
Remove-AzResourceGroup `
  -Name $resourceGroup
```

## <a name="next-steps"></a>Passaggi successivi

In questo argomento di avvio rapido è stata distribuita una macchina virtuale usando un modello di Azure Resource Manager e le coppie chiave-valore di Configurazione app di Azure.

Per informazioni sulla creazione di altre applicazioni con Configurazione app di Azure, continuare con l'articolo seguente:

> [!div class="nextstepaction"]
> [Avvio rapido: Creare un'app ASP.NET Core con Configurazione app di Azure](quickstart-aspnet-core-app.md)
