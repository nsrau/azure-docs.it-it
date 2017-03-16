---
title: Gestire le macchine virtuali con Azure Resource Manager e C# | Microsoft Docs
description: Gestire le macchine virtuali con Azure Resource Manager e C#.
services: virtual-machines-windows
documentationcenter: 
author: davidmu1
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 93898bad-b861-4359-9a4b-348e1d491822
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 03/01/2017
ms.author: davidmu
translationtype: Human Translation
ms.sourcegitcommit: e96eacd689ead229d0c48d15f53a932b903c5281
ms.openlocfilehash: d32dd39b7835e327e5bc1a40237da43617f6e61c
ms.lasthandoff: 03/02/2017


---
# <a name="manage-azure-virtual-machines-using-azure-resource-manager-and-c"></a>Gestire le macchine virtuali di Azure con Azure Resource Manager e C# #

Le attività in questo articolo spiegano come gestire le macchine virtuali, ad esempio l'avvio, l'arresto e l'aggiornamento.

## <a name="set-up-visual-studio"></a>Configurare Visual Studio

### <a name="create-a-project"></a>Creare un progetto

Verificare che Visual Studio sia installato e creare un'applicazione console da usare per gestire le macchine virtuali.

1. Se non è già installato, installare [Visual Studio](https://www.visualstudio.com/).
2. In Visual Studio fare clic su **File** > **Nuovo** > **Progetto**.
3. In **Modelli** > **Visual C#** selezionare **Applicazione console**, immettere il nome e il percorso del progetto e fare clic su **OK**.

### <a name="install-libraries"></a>Installare le librerie

I pacchetti NuGet sono il modo più semplice per installare le librerie necessarie per eseguire le operazioni in questo articolo. Per ottenere le librerie necessarie in Visual Studio, eseguire questa procedura:

1. Fare clic con il pulsante destro del mouse sul nome del progetto in Esplora soluzioni, scegliere **Gestisci pacchetti NuGet** e fare clic su **Sfoglia**.
2. Digitare *Microsoft.IdentityModel.Clients.ActiveDirectory* nella casella di ricerca, quindi fare clic su **Installa** e seguire le istruzioni per installare il pacchetto.
3. Nella parte superiore della pagina selezionare **Includi versione preliminare**. Digitare *Microsoft.Azure.Management.Compute* nella casella di ricerca, quindi fare clic su **Installa** e seguire le istruzioni per installare il pacchetto.

A questo punto è possibile iniziare a usare le librerie per gestire le macchine virtuali.

### <a name="create-credentials-and-add-variables"></a>Creare credenziali e aggiungere variabili

Per interagire con Azure Resource Manager, assicurarsi di avere accesso a un'[entità servizio Active Directory](../azure-resource-manager/resource-group-authenticate-service-principal.md). Dall'entità servizio si acquisisce un token per autenticare le richieste ad Azure Resource Manager.

1. Aprire il file Program.cs per il progetto creato e quindi aggiungere le istruzioni using seguenti all'inizio del file:

    ```   
    using Microsoft.Azure;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using Microsoft.Azure.Management.Compute;
    using Microsoft.Azure.Management.Compute.Models;
    using Microsoft.Rest;
    ```

2. Aggiungere le variabili al metodo Main della classe Program per specificare il nome del gruppo di risorse, il nome della macchina virtuale e l'identificatore della sottoscrizione:

    ```   
    var groupName = "myResourceGroup";
    var vmName = "myVM";  
    var subscriptionId = "subsciptionId";
    ```

    È possibile reperire l'ID sottoscrizione nel pannello Sottoscrizioni nel Portale di Azure.    

3. Aggiungere questo metodo alla classe Program per ottenere il token necessario per creare le credenziali.
   
    ```    
    private static async Task<AuthenticationResult> GetAccessTokenAsync()
    {
      var cc = new ClientCredential("{client-id}", "{client-secret}");
      var context = new AuthenticationContext("https://login.windows.net/{tenant-id}");
      var token = await context.AcquireTokenAsync("https://management.azure.com/", cc);
      if (token == null)
      {
        throw new InvalidOperationException("Could not get the token");
      }
      return token;
    }
    ```

    Sostituire questi valori:
    
    - *{client-id}* con l'identificatore dell'applicazione Azure Active Directory. È possibile trovare questo identificatore nel pannello Proprietà dell'applicazione AD. Per individuare l'applicazione AD nel Portale di Azure, fare clic su **Azure Active Directory** nel menu di risorse e quindi fare clic su **Registrazioni per l'app**.
    - *{client-secret}* con la chiave di accesso dell'applicazione AD. È possibile trovare questo identificatore nel pannello Proprietà dell'applicazione AD.
    - *{tenant-id}* con l'identificatore del tenant della sottoscrizione. È possibile trovare l'identificatore del tenant nel pannello Proprietà di Azure Active Directory nel Portale di Azure. È indicato come *ID directory*.

4. Per chiamare il metodo aggiunto prima, aggiungere questo codice al metodo Main nel file Program.cs:
   
    ```
    var token = GetAccessTokenAsync();
    var credential = new TokenCredentials(token.Result.AccessToken);
    ```

5. Salvare il file Program.cs.

## <a name="display-information-about-a-virtual-machine"></a>Visualizzare informazioni relative a una macchina virtuale

1. Aggiungere questo metodo alla classe Program nel progetto creato in precedenza:

    ```   
    public static async void GetVirtualMachineAsync(
      TokenCredentials credential, 
      string groupName, 
      string vmName, 
      string subscriptionId)
    {
      Console.WriteLine("Getting information about the virtual machine...");
   
      var computeManagementClient = new ComputeManagementClient(credential)
        { SubscriptionId = subscriptionId };
      var vmResult = await computeManagementClient.VirtualMachines.GetAsync(
        groupName, 
        vmName, 
        InstanceViewTypes.InstanceView);
   
      Console.WriteLine("hardwareProfile");
      Console.WriteLine("   vmSize: " + vmResult.HardwareProfile.VmSize);
   
      Console.WriteLine("\nstorageProfile");
      Console.WriteLine("  imageReference");
      Console.WriteLine("    publisher: " + vmResult.StorageProfile.ImageReference.Publisher);
      Console.WriteLine("    offer: " + vmResult.StorageProfile.ImageReference.Offer);
      Console.WriteLine("    sku: " + vmResult.StorageProfile.ImageReference.Sku);
      Console.WriteLine("    version: " + vmResult.StorageProfile.ImageReference.Version);
      Console.WriteLine("  osDisk");
      Console.WriteLine("    osType: " + vmResult.StorageProfile.OsDisk.OsType);
      Console.WriteLine("    name: " + vmResult.StorageProfile.OsDisk.Name);
      Console.WriteLine("    createOption: " + vmResult.StorageProfile.OsDisk.CreateOption);
      Console.WriteLine("    uri: " + vmResult.StorageProfile.OsDisk.Vhd.Uri);
      Console.WriteLine("    caching: " + vmResult.StorageProfile.OsDisk.Caching);
      Console.WriteLine("\nosProfile");
      Console.WriteLine("  computerName: " + vmResult.OsProfile.ComputerName);
      Console.WriteLine("  adminUsername: " + vmResult.OsProfile.AdminUsername);
      Console.WriteLine("  provisionVMAgent: " + vmResult.OsProfile.WindowsConfiguration.ProvisionVMAgent.Value);
      Console.WriteLine("  enableAutomaticUpdates: " + vmResult.OsProfile.WindowsConfiguration.EnableAutomaticUpdates.Value);
   
      Console.WriteLine("\nnetworkProfile");
      foreach (NetworkInterfaceReference nic in vmResult.NetworkProfile.NetworkInterfaces)
      {
        Console.WriteLine("  networkInterface id: " + nic.Id);
      }
   
      Console.WriteLine("\nvmAgent");
      Console.WriteLine("  vmAgentVersion" + vmResult.InstanceView.VmAgent.VmAgentVersion);
      Console.WriteLine("    statuses");
      foreach (InstanceViewStatus stat in vmResult.InstanceView.VmAgent.Statuses)
      {
        Console.WriteLine("    code: " + stat.Code);
        Console.WriteLine("    level: " + stat.Level);
        Console.WriteLine("    displayStatus: " + stat.DisplayStatus);
        Console.WriteLine("    message: " + stat.Message);
        Console.WriteLine("    time: " + stat.Time);
      }
   
      Console.WriteLine("\ndisks");
      foreach (DiskInstanceView idisk in vmResult.InstanceView.Disks)
      {
        Console.WriteLine("  name: " + idisk.Name);
        Console.WriteLine("  statuses");
        foreach (InstanceViewStatus istat in idisk.Statuses)
        {
          Console.WriteLine("    code: " + istat.Code);
          Console.WriteLine("    level: " + istat.Level);
          Console.WriteLine("    displayStatus: " + istat.DisplayStatus);
          Console.WriteLine("    time: " + istat.Time);
        }
      }
   
      Console.WriteLine("\nVM general status");
      Console.WriteLine("  provisioningStatus: " + vmResult.ProvisioningState);
      Console.WriteLine("  id: " + vmResult.Id);
      Console.WriteLine("  name: " + vmResult.Name);
      Console.WriteLine("  type: " + vmResult.Type);
      Console.WriteLine("  location: " + vmResult.Location);
      Console.WriteLine("\nVM instance status");
      foreach (InstanceViewStatus istat in vmResult.InstanceView.Statuses)
      {
        Console.WriteLine("\n  code: " + istat.Code);
        Console.WriteLine("  level: " + istat.Level);
        Console.WriteLine("  displayStatus: " + istat.DisplayStatus);
      }
    }
    ```

2. Per chiamare il metodo appena aggiunto, aggiungere questo codice al metodo Main:

    ```   
    GetVirtualMachineAsync(
      credential,
      groupName,
      vmName,
      subscriptionId);
    Console.WriteLine("\nPress enter to continue...");
    Console.ReadLine();
    ```

3. Salvare il file Program.cs.

4. Fare clic su **Avvia** in Visual Studio e accedere ad Azure AD usando lo stesso nome utente e la password specificati per la sottoscrizione.
   
    Quando si esegue questo metodo, il risultato visualizzato sarà simile all'esempio seguente:
   
        Getting information about the virtual machine...
        hardwareProfile
          vmSize: Standard_D1_v2
   
        storageProfile
          imageReference
            publisher: MicrosoftWindowsServer
            offer: WindowsServer
            sku: 2012-R2-Datacenter
            version: latest
          osDisk
            osType: Windows
            name: myosdisk
            createOption: FromImage
            uri: http://store1.blob.core.windows.net/vhds/myosdisk.vhd
            caching: ReadWrite
   
          osProfile
            computerName: vm1
            adminUsername: account1
            provisionVMAgent: True
            enableAutomaticUpdates: True
   
          networkProfile
            networkInterface 
              id: /subscriptions/{subscription-id}
                /resourceGroups/rg1/providers/Microsoft.Network/networkInterfaces/nc1
   
          vmAgent
            vmAgentVersion2.7.1198.766
            statuses
            code: ProvisioningState/succeeded
            level: Info
            displayStatus: Ready
            message: GuestAgent is running and accepting new configurations.
            time: 4/13/2016 8:35:32 PM
   
          disks
            name: myosdisk
            statuses
              code: ProvisioningState/succeeded
              level: Info
              displayStatus: Provisioning succeeded
              time: 4/13/2016 8:04:36 PM
   
          VM general status
            provisioningStatus: Succeeded
            id: /subscriptions/{subscription-id}
              /resourceGroups/rg1/providers/Microsoft.Compute/virtualMachines/vm1
            name: vm1
            type: Microsoft.Compute/virtualMachines
            location: centralus
   
          VM instance status
            code: ProvisioningState/succeeded
              level: Info
              displayStatus: Provisioning succeeded
            code: PowerState/running
              level: Info
              displayStatus: VM running

## <a name="stop-a-virtual-machine"></a>Arrestare una macchina virtuale

Per arrestare una macchina virtuale, è possibile procedere in due modi. È possibile arrestare una macchina virtuale e mantenere tutte le sue impostazioni, pur continuando a vedersela addebitata oppure è possibile arrestare una macchina virtuale e deallocarla. Quando una macchina virtuale viene deallocata, lo stesso viene fatto per tutte le risorse associate e la fatturazione termina..

1. Impostare come commento l'eventuale codice aggiunto in precedenza al metodo Main, ad eccezione del codice per ottenere le credenziali.

2. Aggiungere il metodo alla classe Program:

    ```   
    public static async void StopVirtualMachineAsync(
      TokenCredentials credential, 
      string groupName, 
      string vmName, 
      string subscriptionId)
    {
      Console.WriteLine("Stopping the virtual machine...");
      var computeManagementClient = new ComputeManagementClient(credential)
        { SubscriptionId = subscriptionId };
      await computeManagementClient.VirtualMachines.PowerOffAsync(groupName, vmName);
    }
    ```

    Per deallocare la macchina virtuale, sostituire la chiamata PowerOff con il codice seguente:
    
    ```
    computeManagementClient.VirtualMachines.Deallocate(groupName, vmName);
    ```

3. Per chiamare il metodo appena aggiunto, aggiungere questo codice al metodo Main:
    
    ```
    StopVirtualMachineAsync(
      credential,
      groupName,
      vmName,
      subscriptionId);
    Console.WriteLine("\nPress enter to continue...");
    Console.ReadLine();
    ```

4. Salvare il file Program.cs.

5. Fare clic su **Avvia** in Visual Studio e accedere ad Azure AD usando lo stesso nome utente e la password specificati per la sottoscrizione.
   
    Lo stato della macchina virtuale passerà a Arrestato. Se è stato eseguito il metodo con la chiamata Deallocate, lo stato risulterà Arrestato (deallocato).

## <a name="start-a-virtual-machine"></a>Avviare una macchina virtuale

1. Impostare come commento l'eventuale codice aggiunto in precedenza al metodo Main, ad eccezione del codice per ottenere le credenziali.

2. Aggiungere il metodo alla classe Program:

    ```   
    public static async void StartVirtualMachineAsync(
      TokenCredentials credential, 
      string groupName, 
      string vmName, 
      string subscriptionId)
    {
      Console.WriteLine("Starting the virtual machine...");
      var computeManagementClient = new ComputeManagementClient(credential)
        { SubscriptionId = subscriptionId };
      await computeManagementClient.VirtualMachines.StartAsync(groupName, vmName);
    }
    ```

3. Per chiamare il metodo appena aggiunto, aggiungere questo codice al metodo Main:

    ```   
    StartVirtualMachineAsync(
      credential,
      groupName,
      vmName,
      subscriptionId);
    Console.WriteLine("\nPress enter to continue...");
    Console.ReadLine();
    ```

4. Salvare il file Program.cs.

5. Fare clic su **Avvia** in Visual Studio e accedere ad Azure AD usando lo stesso nome utente e la password specificati per la sottoscrizione.
   
    Lo stato della macchina virtuale dovrebbe passare a In esecuzione.

## <a name="restart-a-running-virtual-machine"></a>Riavviare una macchina virtuale in esecuzione

1. Impostare come commento l'eventuale codice aggiunto in precedenza al metodo Main, ad eccezione del codice per ottenere le credenziali.

2. Aggiungere il metodo alla classe Program:

    ```   
    public static async void RestartVirtualMachineAsync(
      TokenCredentials credential,
      string groupName,
      string vmName,
      string subscriptionId)
    {
      Console.WriteLine("Restarting the virtual machine...");
      var computeManagementClient = new ComputeManagementClient(credential)
        { SubscriptionId = subscriptionId };
      await computeManagementClient.VirtualMachines.RestartAsync(groupName, vmName);
    }
    ```

3. Per chiamare il metodo appena aggiunto, aggiungere questo codice al metodo Main:

    ```   
    RestartVirtualMachineAsync(
      credential,
      groupName,
      vmName,
      subscriptionId);
    Console.WriteLine("\nPress enter to continue...");
    Console.ReadLine();
    ```

4. Salvare il file Program.cs.

5. Fare clic su **Avvia** in Visual Studio e accedere ad Azure AD usando lo stesso nome utente e la password specificati per la sottoscrizione.

## <a name="resize-a-virtual-machine"></a>Ridimensionare una macchina virtuale

Questo esempio mostra come modificare le dimensioni di una macchina virtuale in esecuzione.

1. Impostare come commento l'eventuale codice aggiunto in precedenza al metodo Main, ad eccezione del codice per ottenere le credenziali.

2. Aggiungere il metodo alla classe Program:

    ```   
    public static async void UpdateVirtualMachineAsync(
      TokenCredentials credential, 
      string groupName, 
      string vmName, 
      string subscriptionId)
    {
      Console.WriteLine("Updating the virtual machine...");
      var computeManagementClient = new ComputeManagementClient(credential)
        { SubscriptionId = subscriptionId };
      var vmResult = await computeManagementClient.VirtualMachines.GetAsync(groupName, vmName);
      vmResult.HardwareProfile.VmSize = "Standard_D2_v2";
      await computeManagementClient.VirtualMachines.CreateOrUpdateAsync(groupName, vmName, vmResult);
    }
    ```

3. Per chiamare il metodo appena aggiunto, aggiungere questo codice al metodo Main:

    ```   
    UpdateVirtualMachineAsync(
      credential,
      groupName,
      vmName,
      subscriptionId);
    Console.WriteLine("\nPress enter to continue...");
    Console.ReadLine();
    ```

4. Salvare il file Program.cs.

5. Fare clic su **Avvia** in Visual Studio e accedere ad Azure AD usando lo stesso nome utente e la password specificati per la sottoscrizione.
   
    Le dimensioni della macchina virtuale passeranno a Standard_D2_v2.

## <a name="add-a-data-disk-to-a-virtual-machine"></a>Aggiungere un disco dati a una macchina virtuale

Questo esempio illustra come aggiungere un disco dati a una macchina virtuale funzionante.

1. Impostare come commento l'eventuale codice aggiunto in precedenza al metodo Main, ad eccezione del codice per ottenere le credenziali.

2. Aggiungere il metodo alla classe Program:

    ```   
    public static async void AddDataDiskAsync(
      TokenCredentials credential, 
      string groupName, 
      string vmName, 
      string subscriptionId)
    {
      Console.WriteLine("Adding the disk to the virtual machine...");
      var computeManagementClient = new ComputeManagementClient(credential)
        { SubscriptionId = subscriptionId };
      var vmResult = await computeManagementClient.VirtualMachines.GetAsync(groupName, vmName);
      vmResult.StorageProfile.DataDisks.Add(
        new DataDisk
          {
            Lun = 0,
            Name = "mydatadisk1",
            Vhd = new VirtualHardDisk
              {
                Uri = "https://mystorage1.blob.core.windows.net/vhds/mydatadisk1.vhd"
              },
            CreateOption = DiskCreateOptionTypes.Empty,
            DiskSizeGB = 2,
            Caching = CachingTypes.ReadWrite
          });
      await computeManagementClient.VirtualMachines.CreateOrUpdateAsync(groupName, vmName, vmResult);
    }
    ```

    Sostituire mystorage1 con il nome dell'account di archiviazione in cui sono archiviati i dischi per la macchina virtuale.

3. Per chiamare il metodo appena aggiunto, aggiungere questo codice al metodo Main:
    
    ```
    AddDataDiskAsync(
      credential,
      groupName,
      vmName,
      subscriptionId);
    Console.WriteLine("\nPress enter to continue...");
    Console.ReadLine();
    ```

4. Salvare il file Program.cs.

5. Fare clic su **Avvia** in Visual Studio e accedere ad Azure AD usando lo stesso nome utente e la password specificati per la sottoscrizione.

## <a name="delete-a-virtual-machine"></a>Eliminare una macchina virtuale

1. Impostare come commento l'eventuale codice aggiunto in precedenza al metodo Main, ad eccezione del codice per ottenere le credenziali.

2. Aggiungere il metodo alla classe Program:
   
    ```
    public static async void DeleteVirtualMachineAsync(
      TokenCredentials credential, 
      string groupName, 
      string vmName, 
      string subscriptionId)
    {
      Console.WriteLine("Deleting the virtual machine...");
      var computeManagementClient = new ComputeManagementClient(credential)
        { SubscriptionId = subscriptionId };
      await computeManagementClient.VirtualMachines.DeleteAsync(groupName, vmName);
    }
    ```

3. Per chiamare il metodo appena aggiunto, aggiungere questo codice al metodo Main:
    
    ```
    DeleteVirtualMachineAsync(
      credential,
      groupName,
      vmName,
      subscriptionId);
    Console.WriteLine("\nPress enter to continue...");
    Console.ReadLine();
    ```

4. Salvare il file Program.cs.

5. Fare clic su **Avvia** in Visual Studio e accedere ad Azure AD usando lo stesso nome utente e la password specificati per la sottoscrizione.

## <a name="next-steps"></a>Passaggi successivi

- Se si sono verificati problemi con una distribuzione, vedere [Risolvere errori comuni durante la distribuzione di risorse in Azure con Azure Resource Manager](../azure-resource-manager/resource-manager-common-deployment-errors.md).
- Informazioni su come distribuire una macchina virtuale e le relative risorse di supporto sono disponibili in [Distribuire una macchina virtuale di Azure con C#](virtual-machines-windows-csharp.md).
- Per usare un modello per creare una macchina virtuale, vedere le informazioni contenute in [Distribuire una macchina virtuale di Azure con C# e un modello di Resource Manager](virtual-machines-windows-csharp-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).



