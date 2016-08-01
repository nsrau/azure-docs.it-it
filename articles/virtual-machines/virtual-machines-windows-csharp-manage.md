<properties
	pageTitle="Gestire le macchine virtuali con Azure Resource Manager e C# | Microsoft Azure"
	description="Gestire le macchine virtuali con Azure Resource Manager e C#."
	services="virtual-machines-windows"
	documentationCenter=""
	authors="davidmu1"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="na"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/24/2016"
	ms.author="davidmu"/>

# Gestire le macchine virtuali di Azure con Azure Resource Manager e C#  

Le attività in questo articolo spiegano come gestire le macchine virtuali, ad esempio l'avvio, l'arresto e l'aggiornamento.

Per completare le attività di questo articolo, sono necessari gli elementi seguenti:

- [Visual Studio](http://msdn.microsoft.com/library/dd831853.aspx)
- [Un token di autenticazione](../resource-group-authenticate-service-principal.md)

## Creare un progetto di Visual Studio e installare i pacchetti

I pacchetti NuGet sono il modo più semplice per installare le librerie necessarie per completare le attività in questo articolo. È necessario installare le librerie Azure Active Directory Authentication Library e Computer Resource Provider Library. Per ottenere queste librerie in Visual Studio, seguire questa procedura:

1. Fare clic su **File** > **Nuovo** > **Progetto**.

2. In **Modelli** > **Visual C#** selezionare **Applicazione console**, immettere il nome e il percorso del progetto e fare clic su **OK**.

3. Fare clic con il pulsante destro del mouse sul nome del progetto in Esplora soluzioni e scegliere **Gestisci pacchetti NuGet**.

4. Digitare *Active Directory* nella casella di ricerca, fare clic su **Installa** per il pacchetto di Active Directory Authentication Library, quindi seguire le istruzioni per l'installazione del pacchetto.

5. Nella parte superiore della pagina selezionare **Includi versione preliminare**. Digitare *Microsoft.Azure.Management.Compute* nella casella di ricerca, fare clic su **Installa** per le librerie di calcolo .NET e quindi seguire le istruzioni per l'installazione del pacchetto.

A questo punto è possibile iniziare a usare le librerie per gestire le macchine virtuali.

## Configurare il progetto

Dopo la creazione dell'applicazione Azure Active Directory e l'installazione della libreria di autenticazione, formattare le informazioni sull'applicazione nelle credenziali usate per autenticare le richieste in Gestione risorse di Azure.

1. Aprire il file Program.cs per il progetto creato e quindi aggiungere le istruzioni using seguenti all'inizio del file:

        using Microsoft.Azure;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;
        using Microsoft.Azure.Management.Compute;
        using Microsoft.Azure.Management.Compute.Models;
        using Microsoft.Rest;
        
2. Aggiungere le variabili al metodo Main della classe Program per specificare i nomi delle risorse da gestire, la località delle risorse, ad esempio "Stati Uniti centrali" e l'identificatore della sottoscrizione:

        var groupName = "resource group name";
        var vmName = "virtual machine name";  
        var location = "location name";
        var subscriptionId = "subsciption id";

    Sostituire tutti i valori delle variabili con i nomi e l'identificatore che si vuole usare. Per trovare l'ID della sottoscrizione, è possibile eseguire il cmdlet Get-AzureRmSubscription.
    
3. Aggiungere questo metodo alla classe Program per ottenere il token necessario per creare le credenziali.

	    private static async Task<AuthenticationResult> GetAccessTokenAsync()
	    {
          var cc = new ClientCredential("{client-id}", "{client-secret}");
          var context = new AuthenticationContext("https://login.windows.net/{tenant-id}");
          var result = await context.AcquireTokenAsync("https://management.azure.com/", cc);
          if (result == null)
          {
            throw new InvalidOperationException("Could not get the token");
          }
          return result;
        }
	
    Sostituire {client-id} con l'identificatore dell'applicazione Azure Active Directory, {client-secret} con la chiave di accesso dell'applicazione di Active Directory e {tenant-id} con l'identificatore del tenant per la sottoscrizione. Per trovare l'ID tenant, eseguire Get-AzureRmSubscription. È possibile trovare la chiave di accesso mediante il portale di Azure.
    
4. Aggiungere questo codice al metodo Main nel file Program.cs per creare le credenziali:

        var token = GetAccessTokenAsync();
        var credential = new TokenCredentials(token.Result.AccessToken);

5. Salvare il file Program.cs.

## Visualizzare informazioni relative a una macchina virtuale

1. Aggiungere questo metodo alla classe Program nel progetto creato in precedenza:

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

2. Aggiungere questo codice al metodo Main per chiamare il metodo appena aggiunto:

        GetVirtualMachineAsync(
          credential,
          groupName,
          vmName,
          subscriptionId);
        Console.WriteLine("\nPress enter to continue...");
        Console.ReadLine();
    
3. Salvare il file Program.cs.

4. Fare clic su **Avvia** in Visual Studio e accedere ad Azure AD usando lo stesso nome utente e la password specificati per la sottoscrizione.

	Quando si esegue questo metodo, il risultato visualizzato sarà simile al seguente:
    
        Getting information about the virtual machine...
        hardwareProfile
          vmSize: Standard_A0

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

## Avviare una macchina virtuale

1. Impostare come commento l'eventuale codice aggiunto in precedenza al metodo Main, ad eccezione del codice per ottenere le credenziali.

2. Aggiungere il metodo alla classe Program:

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

3. Aggiungere questo codice al metodo Main per chiamare il metodo appena aggiunto:

        StartVirtualMachineAsync(
          credential,
          groupName,
          vmName,
          subscriptionId);
        Console.WriteLine("\nPress enter to continue...");
        Console.ReadLine();

4. Salvare il file Program.cs.

5. Fare clic su **Avvia** in Visual Studio e accedere ad Azure AD usando lo stesso nome utente e la password specificati per la sottoscrizione.

	Lo stato della macchina virtuale dovrebbe passare a In esecuzione.

## Arrestare una macchina virtuale

Per arrestare una macchina virtuale, è possibile procedere in due modi. È possibile arrestare una macchina virtuale mantenendone tutte le impostazioni correnti, ma in questo caso la macchina virtuale continuerà a essere addebitata. In alternativa, è possibile arrestare la macchina virtuale e deallocarla. In questo caso invece verranno deallocate anche tutte le risorse ad essa associate e la fatturazione della macchina virtuale verrà interrotta.

1. Impostare come commento l'eventuale codice aggiunto in precedenza al metodo Main, ad eccezione del codice per ottenere le credenziali.

2. Aggiungere il metodo alla classe Program:

        public static void StopVirtualMachineAsync(
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

	Per deallocare la macchina virtuale, sostituire la chiamata PowerOff con il codice seguente:

        computeManagementClient.VirtualMachines.Deallocate(groupName, vmName);

3. Aggiungere questo codice al metodo Main per chiamare il metodo appena aggiunto:

        StopVirtualMachineAsync(
          credential,
          groupName,
          vmName,
          subscriptionId);
        Console.WriteLine("\nPress enter to continue...");
        Console.ReadLine();

4. Salvare il file Program.cs.

5. Fare clic su **Avvia** in Visual Studio e accedere ad Azure AD usando lo stesso nome utente e la password specificati per la sottoscrizione.

    Lo stato della macchina virtuale passerà a Arrestato. Se è stato eseguito il metodo con la chiamata Deallocate, lo stato risulterà Arrestato (deallocato).

## Riavviare una macchina virtuale in esecuzione

1. Impostare come commento l'eventuale codice aggiunto in precedenza al metodo Main, ad eccezione del codice per ottenere le credenziali.

2. Aggiungere il metodo alla classe Program:

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

3. Aggiungere questo codice al metodo Main per chiamare il metodo appena aggiunto:

        RestartVirtualMachineAsync(
          credential,
          groupName,
          vmName,
          subscriptionId);
        Console.WriteLine("\nPress enter to continue...");
        Console.ReadLine();

4. Salvare il file Program.cs.

5. Fare clic su **Avvia** in Visual Studio e accedere ad Azure AD usando lo stesso nome utente e la password specificati per la sottoscrizione.

## Eliminare una macchina virtuale

1. Impostare come commento l'eventuale codice aggiunto in precedenza al metodo Main, ad eccezione del codice per ottenere le credenziali.

2. Aggiungere il metodo alla classe Program:

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

3. Aggiungere questo codice al metodo Main per chiamare il metodo appena aggiunto:

        DeleteVirtualMachineAsync(
          credential,
          groupName,
          vmName,
          subscriptionId);
        Console.WriteLine("\nPress enter to continue...");
        Console.ReadLine();

4. Salvare il file Program.cs.

5. Fare clic su **Avvia** in Visual Studio e accedere ad Azure AD usando lo stesso nome utente e la password specificati per la sottoscrizione.

## Aggiornare una macchina virtuale

Questo esempio mostra come modificare le dimensioni di una macchina virtuale in esecuzione.

1. Impostare come commento l'eventuale codice aggiunto in precedenza al metodo Main, ad eccezione del codice per ottenere le credenziali.

2. Aggiungere il metodo alla classe Program:

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
          vmResult.HardwareProfile.VmSize = "Standard_A1";
          await computeManagementClient.VirtualMachines.CreateOrUpdateAsync(groupName, vmName, vmResult);
        }

3. Aggiungere questo codice al metodo Main per chiamare il metodo appena aggiunto:

        UpdateVirtualMachineAsync(
          credential,
          groupName,
          vmName,
          subscriptionId);
        Console.WriteLine("\nPress enter to continue...");
        Console.ReadLine();

4. Salvare il file Program.cs.

5. Fare clic su **Avvia** in Visual Studio e accedere ad Azure AD usando lo stesso nome utente e la password specificati per la sottoscrizione.

    Le dimensioni della macchina virtuale passeranno a Standard\_A1
    
## Passaggi successivi

Se si sono verificati problemi con la distribuzione, è consigliabile vedere [Visualizzare le operazioni di distribuzione con il portale di Azure](../resource-manager-troubleshoot-deployments-portal.md).

<!---HONumber=AcomDC_0720_2016-->