---
title: Distribuire una macchina virtuale dai dischi rigidi virtuali per Azure Marketplace
description: Questo articolo illustra come registrare una macchina virtuale da un disco rigido virtuale distribuito in Azure.
services: Azure, Marketplace, Cloud Partner Portal,
author: qianw211
ms.service: marketplace
ms.topic: article
ms.date: 08/08/2019
ms.author: evansma
ms.openlocfilehash: 1aa946c813de41423d4fb2ba5b3aa5274db90f39
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/09/2019
ms.locfileid: "68934973"
---
# <a name="deploy-a-vm-from-your-vhds"></a>Distribuire una macchina virtuale dai VHD

Questa sezione illustra come distribuire una macchina virtuale (VM) da un Azure distribuito disco rigido virtuale (VHD).  Elenca gli strumenti necessari e indica come usarli per creare un'immagine di macchina virtuale dell'utente e quindi distribuirla in Azure usando script di PowerShell.

Dopo aver caricato uno o più dischi rigidi virtuali, ovvero il disco rigido virtuale del sistema operativo generalizzato e zero o più dischi rigidi virtuali dei dischi dati, in un account di archiviazione di Azure, è possibile registrarli come immagine di macchina virtuale degli utenti ed eseguirne il test. Dal momento che il disco rigido virtuale dl sistema operativo è generalizzato, non è possibile distribuire direttamente la macchina virtuale specificando l'URL del disco.

Per altre informazioni sulle immagini di macchina virtuale, vedere i post di blog seguenti:

- [Immagine VM](https://azure.microsoft.com/blog/vm-image-blog-post/)
- [Procedure di PowerShell per immagini di macchina virtuale](https://azure.microsoft.com/blog/vm-image-powershell-how-to-blog-post/)

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

## <a name="prerequisite-install-the-necessary-tools"></a>Prerequisito: installare gli strumenti necessari

Se non è già stato fatto, installare Azure PowerShell e l'interfaccia della riga di comando di Azure, usando queste istruzioni:

- [Installare Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-Az-ps)
- [Installare l'interfaccia da riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli)


## <a name="deployment-steps"></a>Passaggi di distribuzione

È necessario usare la procedura seguente per creare e distribuire un'immagine di macchina virtuale dell'utente:

1. Creare l'immagine di macchina virtuale dell'utente, che comporta l'acquisizione e la generalizzazione dell'immagine. 
2. Creare i certificati e archiviarli in un nuovo Azure Key Vault. Un certificato è necessario per stabilire una connessione WinRM sicura con la macchina virtuale.  Vengono forniti un modello di Azure Resource Manager e uno script di Azure PowerShell. 
3. Distribuire la macchina virtuale da un'immagine di macchina virtuale dell'utente, usando il modello e lo script forniti.

Dopo aver distribuito la macchina virtuale, è possibile [certificare l'immagine di macchina virtuale](./cpp-certify-vm.md).

1. Fare clic su **Nuovo**, eseguire una ricerca in **Distribuzione modello** e quindi selezionare **Creare un modello personalizzato nell'editor**.  <br/>
   ![Creare il modello di distribuzione del disco rigido virtuale nel portale di Azure](./media/publishvm_021.png)

1. Copiare e incollare questo [modello JSON](./cpp-deploy-json-template.md) nell'editor e quindi fare clic su **Salva**. <br/>
   ![Salvare il modello di distribuzione del disco rigido virtuale nel portale di Azure](./media/publishvm_022.png)

1. Specificare i valori dei parametri per le pagine delle proprietà di **Distribuzione personalizzata**.

   <table> <tr> <td valign="top"> <img src="./media/publishvm_023.png" alt="Custom deployment property page 1"> </td> <td valign="top"> <img src="./media/publishvm_024.png" alt="Custom deployment property page 2"> </td> </tr> </table> <br/> 

   |  **Parametro**              |   **Descrizione**                                                            |
   |  -------------              |   ---------------                                                            |
   | Nome account di archiviazione utente   | Nome dell'account di archiviazione in cui si trova il disco rigido virtuale generalizzato                    |
   | Nome contenitore di archiviazione utente | Nome del contenitore in cui si trova il disco rigido virtuale generalizzato                          |
   | Nome DNS per indirizzo IP pubblico      | Nome DNS IP pubblico. Il nome DNS è della VM, che verrà definito nel portale di Azure, una volta distribuita l'offerta.  |
   | Nome utente amministratore             | Nome utente dell'account di amministratore per la nuova macchina virtuale                                  |
   | Password amministratore              | Password dell'account di amministratore per la nuova macchina virtuale                                  |
   | Tipo di sistema operativo                     | Sistema operativo della macchina virtuale: `Windows` \| `Linux`                                    |
   | ID sottoscrizione             | Identificatore della sottoscrizione selezionata                                      |
   | Location                    | Località geografica della distribuzione                                        |
   | Dimensioni macchina virtuale                     | [Dimensioni della macchina virtuale di Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sizes), ad esempio `Standard_A2` |
   | Nome indirizzo IP pubblico      | Nome dell'indirizzo IP pubblico dell'utente                                               |
   | Nome macchina virtuale                     | Nome della nuova macchina virtuale                                                           |
   | Nome della rete virtuale        | Nome della rete virtuale usata dalla macchina virtuale                                   |
   | Nome NIC                    | Nome della scheda di rete in esecuzione nella rete virtuale               |
   | URL VHD                     | URL del disco rigido virtuale del sistema operativo completo                                                     |
   |  |  |
            
1. Dopo aver immesso questi valori, fare clic su **Acquisto**. 

Azure avvierà la distribuzione creando una nuova macchina virtuale con il disco rigido virtuale non gestito specificato nel percorso di account di archiviazione indicato.  È possibile monitorare lo stato nel portale di Azure facendo clic su **Macchine virtuali** nel lato sinistro del portale.  Dopo che la macchina virtuale è stata creata, lo stato verrà modificato da `Starting` in `Running`. 


### <a name="deploy-a-vm-from-powershell"></a>Distribuire una macchina virtuale da PowerShell

Per distribuire una macchina virtuale di grandi dimensioni dall'immagine di macchina virtuale generalizzata creata, usare questi cmdlet.

``` powershell
    $img = Get-AzureVMImage -ImageName "myVMImage"
    $user = "user123"
    $pass = "adminPassword123"
    $myVM = New-AzureVMConfig -Name "VMImageVM" -InstanceSize "Large" -ImageName $img.ImageName | Add-AzureProvisioningConfig -Windows -AdminUsername $user -Password $pass
    New-AzureVM -ServiceName "VMImageCloudService" -VMs $myVM -Location "West US" -WaitForBoot
```


## <a name="next-steps"></a>Passaggi successivi

Successivamente, verrà [creata un'immagine di macchina virtuale dell'utente](cpp-create-user-image.md) per la propria soluzione.

