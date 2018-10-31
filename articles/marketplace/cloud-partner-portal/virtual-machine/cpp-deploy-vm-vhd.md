---
title: Distribuire una macchina virtuale dai dischi rigidi virtuali per Azure Marketplace | Microsoft Docs
description: Questo articolo illustra come registrare una macchina virtuale da un disco rigido virtuale distribuito in Azure.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: pbutlerm
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 10/19/2018
ms.author: pbutlerm
ms.openlocfilehash: 2771549af29b3e717d117afb42de6db03fbee226
ms.sourcegitcommit: 17633e545a3d03018d3a218ae6a3e4338a92450d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/22/2018
ms.locfileid: "49639127"
---
# <a name="deploy-a-vm-from-your-vhds"></a>Distribuire una macchina virtuale dai dischi rigidi virtuali

Questo articolo illustra come registrare una macchina virtuale (VM) da un Azure distribuito disco rigido virtuale (VHD).  Elenca gli strumenti necessari e indica come usarli per creare un'immagine di macchina virtuale utente e quindi distribuirla in Azure usando il [portale di Microsoft Azure](https://ms.portal.azure.com/) o script di PowerShell. 

Dopo aver caricato uno o più dischi rigidi virtuali, ovvero il disco rigido virtuale del sistema operativo generalizzato e zero o più dischi rigidi virtuali dei dischi dati, in un account di archiviazione di Azure, è possibile registrarli come immagine di macchina virtuale degli utenti ed eseguirne il test. Dal momento che il disco rigido virtuale dl sistema operativo è generalizzato, non è possibile distribuire direttamente la macchina virtuale specificando l'URL del disco.

Per altre informazioni sulle immagini di macchina virtuale, vedere i post di blog seguenti:

- [Immagine VM](https://azure.microsoft.com/blog/vm-image-blog-post/)
- [Procedure di PowerShell per immagini di macchina virtuale](https://azure.microsoft.com/blog/vm-image-powershell-how-to-blog-post/)


## <a name="set-up-the-necessary-tools"></a>Configurare gli strumenti necessari

Se non è già stato fatto, installare Azure PowerShell e l'interfaccia della riga di comando di Azure, usando queste istruzioni:

<!-- TD: Change the following URLs (in this entire topic) to relative paths.-->

- [Installare Azure PowerShell in Windows con PowerShellGet](https://docs.microsoft.com/powershell/azure/install-azurerm-ps)
- [Installare l'interfaccia della riga di comando di Azure 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli)


## <a name="create-a-user-vm-image"></a>Creare un'immagine di macchina virtuale degli utenti

Successivamente viene creata un'immagine non gestita dal disco rigido virtuale generalizzato.

#### <a name="capture-the-vm-image"></a>Acquisire l'immagine della macchina virtuale

Seguire le istruzioni indicate nell'articolo seguente sull'acquisizione della macchina virtuale che corrisponde all'acceso usato:

-  PowerShell: [Come creare un'immagine di macchina virtuale non gestita da una macchina virtuale di Azure](../../../virtual-machines/windows/capture-image-resource.md)
-  Interfaccia della riga di comando di Azure: [Come creare un'immagine di una macchina virtuale o un disco rigido virtuale](../../../virtual-machines/linux/capture-image.md)
-  API: [Macchine virtuali - Acquisizione](https://docs.microsoft.com/rest/api/compute/virtualmachines/capture)

### <a name="generalize-the-vm-image"></a>Generalizzare l'immagine di macchina virtuale

Poiché l'immagine dell'utente è stata generata da un disco rigido virtuale generalizzato in precedenza, è necessario che sia generalizzata.  Anche in questo caso selezionare l'articolo seguente che corrisponde al meccanismo di accesso.  Il disco potrebbe già essere stato generalizzato al momento dell'acquisizione.

-  PowerShell: [Generalizzare la macchina virtuale](https://docs.microsoft.com/azure/virtual-machines/windows/sa-copy-generalized#generalize-the-vm)
-  Interfaccia della riga di comando di Azure: [Passaggio 2: Creare l'immagine della macchina virtuale](https://docs.microsoft.com/azure/virtual-machines/linux/capture-image#step-2-create-vm-image)
-  API: [Macchine virtuali - Generalizzazione](https://docs.microsoft.com/rest/api/compute/virtualmachines/generalize)


## <a name="deploy-a-vm-from-a-user-vm-image"></a>Distribuire una macchina virtuale da un'immagine di macchina virtuale degli utenti

Successivamente si distribuirà una macchina virtuale da un'immagine di un utente tramite il portale di Azure o PowerShell.

<!-- TD: Recapture following hilited images and replace with red-box. -->

### <a name="deploy-a-vm-from-azure-portal"></a>Distribuire una macchina virtuale dal portale di Azure

Per distribuire la macchina virtuale dell'utente dal portale di Azure, eseguire questa procedura.

1.  Accedere al [portale di Azure](https://portal.azure.com).

2.  Fare clic su **Nuovo**, eseguire una ricerca in **Distribuzione modello** e quindi selezionare **Creare un modello personalizzato nell'editor**.  <br/>
  ![Creare il modello di distribuzione del disco rigido virtuale nel portale di Azure](./media/publishvm_021.png)

3. Copiare e incollare questo [modello JSON](./cpp-deploy-json-template.md) nell'editor e quindi fare clic su **Salva**. <br/>
  ![Salvare il modello di distribuzione del disco rigido virtuale nel portale di Azure](./media/publishvm_022.png)

4. Specificare i valori dei parametri per le pagine delle proprietà di **Distribuzione personalizzata**.

   <table> <tr> <td valign="top"> <img src="./media/publishvm_023.png" alt="Custom deployment property page 1"> </td> <td valign="top"> <img src="./media/publishvm_024.png" alt="Custom deployment property page 2"> </td> </tr> </table> <br/> 

   |  **Parametro**              |   **Descrizione**                                                            |
   |  -------------              |   ---------------                                                            |
   | Nome account di archiviazione utente   | Nome dell'account di archiviazione in cui si trova il disco rigido virtuale generalizzato                    |
   | Nome contenitore di archiviazione utente | Nome del contenitore in cui si trova il disco rigido virtuale generalizzato                          |
   | Nome DNS per indirizzo IP pubblico      | Nome DNS dell'indirizzo IP pubblico                                                           |
   | Nome utente amministratore             | Nome utente dell'account di amministratore per la nuova macchina virtuale                                  |
   | Password amministratore              | Password dell'account di amministratore per la nuova macchina virtuale                                  |
   | Tipo di sistema operativo                     | Sistema operativo della macchina virtuale: `Windows` \| `Linux`                                    |
   | ID sottoscrizione             | Identificatore della sottoscrizione selezionata                                      |
   | Località                    | Località geografica della distribuzione                                        |
   | Dimensioni macchina virtuale                     | [Dimensioni della macchina virtuale di Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sizes), ad esempio `Standard_A2` |
   | Nome indirizzo IP pubblico      | Nome dell'indirizzo IP pubblico dell'utente                                               |
   | Nome macchina virtuale                     | Nome della nuova macchina virtuale                                                           |
   | Nome della rete virtuale        | Nome della rete virtuale usata dalla macchina virtuale                                   |
   | Nome NIC                    | Nome della scheda di rete in esecuzione nella rete virtuale               |
   | URL VHD                     | URL del disco rigido virtuale del sistema operativo completo                                                     |
   |  |  |
            
5. Dopo aver immesso questi valori, fare clic su **Acquisto**. 

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

<!-- TD: The following is a marketplace-publishing article and may be out-of-date.  TD: update and move topic.
For help with issues, see [Troubleshooting common issues encountered during VHD creation](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation-troubleshooting) for additional assistance.
-->

## <a name="next-steps"></a>Passaggi successivi

Dopo aver distribuito la macchina virtuale, è possibile [configurare la macchina virtuale](./cpp-configure-vm.md).
