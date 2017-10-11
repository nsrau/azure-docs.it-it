---
title: Creare e gestire una macchina virtuale di Azure tramite Java | Microsoft Docs
description: Usare Java e Azure Resource Manager per distribuire una macchina virtuale e tutte le relative risorse di supporto.
services: virtual-machines-windows
documentationcenter: 
author: davidmu1
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 07/17/2017
ms.author: davidmu
ms.openlocfilehash: b9e739a07c5863577285fb3a221b372b385c6762
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/29/2017
---
# <a name="create-and-manage-windows-vms-in-azure-using-java"></a>Creare e gestire macchine virtuali Windows in Azure tramite Java

Una [macchina virtuale di Azure](overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (VM) richiede diverse risorse di supporto di Azure. Questo articolo descrive come creare, gestire ed eliminare le risorse della macchina virtuale usando Java. Si apprenderà come:

> [!div class="checklist"]
> * Creare un progetto Maven
> * Aggiungere le dipendenze
> * Creare le credenziali
> * Creare le risorse
> * Eseguire le attività di gestione
> * Eliminare le risorse
> * Eseguire l'applicazione

L'esecuzione di questi passaggi richiede circa 20 minuti.

## <a name="create-a-maven-project"></a>Creare un progetto Maven

1. Installare [Java](http://www.oracle.com/technetwork/java/javase/downloads/index.html), se non lo si è già fatto.
2. Installare [Maven](http://maven.apache.org/download.cgi).
3. Creare una nuova cartella e il progetto:
    
    ```
    mkdir java-azure-test
    cd java-azure-test
    
    mvn archetype:generate -DgroupId=com.fabrikam -DartifactId=testAzureApp -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

## <a name="add-dependencies"></a>Aggiungere le dipendenze

1. Nella cartella `testAzureApp` aprire il file `pom.xml` e aggiungere la configurazione della build al &lt;progetto&gt; per abilitare la compilazione dell'applicazione:

    ```xml
    <build>
      <plugins>
        <plugin>
            <groupId>org.codehaus.mojo</groupId>
            <artifactId>exec-maven-plugin</artifactId>
            <configuration>
                <mainClass>com.fabrikam.testAzureApp.App</mainClass>
            </configuration>
        </plugin>
      </plugins>
    </build>
    ```

2. Aggiungere le dipendenze necessarie per accedere ad Azure SDK per Java.

    ```xml
    <dependency>
      <groupId>com.microsoft.azure</groupId>
      <artifactId>azure</artifactId>
      <version>1.1.0</version>
    </dependency>
    <dependency>
      <groupId>com.microsoft.azure</groupId>
      <artifactId>azure-mgmt-compute</artifactId>
      <version>1.1.0</version>
    </dependency>
    <dependency>
      <groupId>com.microsoft.azure</groupId>
      <artifactId>azure-mgmt-resources</artifactId>
      <version>1.1.0</version>
    </dependency>
    <dependency>
      <groupId>com.microsoft.azure</groupId>
      <artifactId>azure-mgmt-network</artifactId>
      <version>1.1.0</version>
    </dependency>
    <dependency>
      <groupId>com.squareup.okio</groupId>
      <artifactId>okio</artifactId>
      <version>1.13.0</version>
    </dependency>
    <dependency> 
      <groupId>com.nimbusds</groupId>
      <artifactId>nimbus-jose-jwt</artifactId>
      <version>3.6</version>
    </dependency>
    <dependency>
      <groupId>net.minidev</groupId>
      <artifactId>json-smart</artifactId>
      <version>1.0.6.3</version>
    </dependency>
    <dependency>
      <groupId>javax.mail</groupId>
      <artifactId>mail</artifactId>
      <version>1.4.5</version>
    </dependency>
    ```

3. Salvare il file.

## <a name="create-credentials"></a>Creare le credenziali

Prima di iniziare questo passaggio, assicurarsi di avere accesso a un'[entità servizio Active Directory](../../azure-resource-manager/resource-group-create-service-principal-portal.md). È inoltre necessario registrare l'ID dell'applicazione, la chiave di autenticazione e l'ID del tenant che saranno necessari in un passaggio successivo.

### <a name="create-the-authorization-file"></a>Creare il file di autorizzazione

1. Creare un file denominato `azureauth.properties` e aggiungervi queste proprietà:

    ```
    subscription=<subscription-id>
    client=<application-id>
    key=<authentication-key>
    tenant=<tenant-id>
    managementURI=https://management.core.windows.net/
    baseURL=https://management.azure.com/
    authURL=https://login.windows.net/
    graphURL=https://graph.windows.net/
    ```

    Sostituire **&lt;subscription-id&gt;** con l'identificatore della sottoscrizione, **&lt;application-id&gt;** con l'identificatore dell'applicazione Active Directory, **&lt;authentication-key&gt;** con la chiave dell'applicazione e **&lt;tenant-id&gt;** con l'identificatore del tenant.

2. Salvare il file.
3. Impostare una variabile di ambiente denominata AZURE_AUTH_LOCATION nella shell con il percorso completo al file di autenticazione.

### <a name="create-the-management-client"></a>Creare il client di gestione

1. Aprire il file `App.java` in `src\main\java\com\fabrikam` e verificare che questa istruzione package si trovi all'inizio del file:

    ```java
    package com.fabrikam.testAzureApp;
    ```

2. Sotto l'istruzione package aggiungere queste istruzioni import:
   
    ```java
    import com.microsoft.azure.management.Azure;
    import com.microsoft.azure.management.compute.AvailabilitySet;
    import com.microsoft.azure.management.compute.AvailabilitySetSkuTypes;
    import com.microsoft.azure.management.compute.CachingTypes;
    import com.microsoft.azure.management.compute.InstanceViewStatus;
    import com.microsoft.azure.management.compute.DiskInstanceView;
    import com.microsoft.azure.management.compute.VirtualMachine;
    import com.microsoft.azure.management.compute.VirtualMachineSizeTypes;
    import com.microsoft.azure.management.network.PublicIPAddress;
    import com.microsoft.azure.management.network.Network;
    import com.microsoft.azure.management.network.NetworkInterface;
    import com.microsoft.azure.management.resources.ResourceGroup;
    import com.microsoft.azure.management.resources.fluentcore.arm.Region;
    import com.microsoft.azure.management.resources.fluentcore.model.Creatable;
    import com.microsoft.rest.LogLevel;
    import java.io.File;
    import java.util.Scanner;
    ```

2. Per creare le credenziali di Active Directory necessarie per effettuare richieste, aggiungere il codice seguente al metodo main della classe App:
   
    ```java
    try {    
        final File credFile = new File(System.getenv("AZURE_AUTH_LOCATION"));
        Azure azure = Azure.configure()
            .withLogLevel(LogLevel.BASIC)
            .authenticate(credFile)
            .withDefaultSubscription();
    } catch (Exception e) {
        System.out.println(e.getMessage());
        e.printStackTrace();
    }

    ```

## <a name="create-resources"></a>Creare le risorse

### <a name="create-the-resource-group"></a>Creare il gruppo di risorse.

Tutte le risorse devono essere contenute in un [gruppo di risorse](../../azure-resource-manager/resource-group-overview.md).

Per specificare i valori per l'applicazione e creare il gruppo di risorse, aggiungere questo codice al blocco try nel metodo main:

```java
System.out.println("Creating resource group...");
ResourceGroup resourceGroup = azure.resourceGroups()
    .define("myResourceGroup")
    .withRegion(Region.US_EAST)
    .create();
```

### <a name="create-the-availability-set"></a>Creare il set di disponibilità

I [set di disponibilità](tutorial-availability-sets.md) semplificano la manutenzione delle macchine virtuali usate dall'applicazione.

Per creare il set di disponibilità, aggiungere questo codice al blocco try nel metodo main:

```java
System.out.println("Creating availability set...");
AvailabilitySet availabilitySet = azure.availabilitySets()
    .define("myAvailabilitySet")
    .withRegion(Region.US_EAST)
    .withExistingResourceGroup("myResourceGroup")
    .withSku(AvailabilitySetSkuTypes.MANAGED)
    .create();
```
### <a name="create-the-public-ip-address"></a>Creare l'indirizzo IP pubblico

Un [indirizzo IP pubblico](../../virtual-network/virtual-network-ip-addresses-overview-arm.md) è necessario per comunicare con la macchina virtuale.

Per creare l'indirizzo IP pubblico per la macchina virtuale, aggiungere questo codice al blocco try nel metodo main:

```java
System.out.println("Creating public IP address...");
PublicIPAddress publicIPAddress = azure.publicIPAddresses()
    .define("myPublicIP")
    .withRegion(Region.US_EAST)
    .withExistingResourceGroup("myResourceGroup")
    .withDynamicIP()
    .create();
```

### <a name="create-the-virtual-network"></a>Creare la rete virtuale

Una macchina virtuale deve essere inclusa in una subnet di una [rete virtuale](../../virtual-network/virtual-networks-overview.md).

Per creare una subnet e una rete virtuale, aggiungere questo codice al blocco try nel metodo main:

```java
System.out.println("Creating virtual network...");
Network network = azure.networks()
    .define("myVN")
    .withRegion(Region.US_EAST)
    .withExistingResourceGroup("myResourceGroup")
    .withAddressSpace("10.0.0.0/16")
    .withSubnet("mySubnet","10.0.0.0/24")
    .create();
```

### <a name="create-the-network-interface"></a>Creare l'interfaccia di rete

Una macchina virtuale richiede un'interfaccia di rete per comunicare nella rete virtuale.

Per creare un'interfaccia di rete, aggiungere questo codice al blocco try nel metodo main:

```java
System.out.println("Creating network interface...");
NetworkInterface networkInterface = azure.networkInterfaces()
    .define("myNIC")
    .withRegion(Region.US_EAST)
    .withExistingResourceGroup("myResourceGroup")
    .withExistingPrimaryNetwork(network)
    .withSubnet("mySubnet")
    .withPrimaryPrivateIPAddressDynamic()
    .withExistingPrimaryPublicIPAddress(publicIPAddress)
    .create();
```

### <a name="create-the-virtual-machine"></a>Creare la macchina virtuale

Dopo avere creato tutte le risorse di supporto, è possibile creare una macchina virtuale.

Per creare la macchina virtuale, aggiungere questo codice al blocco try nel metodo main:

```java
System.out.println("Creating virtual machine...");
VirtualMachine virtualMachine = azure.virtualMachines()
    .define("myVM")
    .withRegion(Region.US_EAST)
    .withExistingResourceGroup("myResourceGroup")
    .withExistingPrimaryNetworkInterface(networkInterface)
    .withLatestWindowsImage("MicrosoftWindowsServer", "WindowsServer", "2012-R2-Datacenter")
    .withAdminUsername("azureuser")
    .withAdminPassword("Azure12345678")
    .withComputerName("myVM")
    .withExistingAvailabilitySet(availabilitySet)
    .withSize("Standard_DS1")
    .create();
Scanner input = new Scanner(System.in);
System.out.println("Press enter to get information about the VM...");
input.nextLine();
```

> [!NOTE]
> Questa esercitazione illustra come creare una macchina virtuale in cui è in esecuzione una versione del sistema operativo Windows Server. Per altre informazioni sulla selezione di altre immagini, vedere [Esplorare e selezionare immagini delle macchine virtuali di Azure con Windows PowerShell e l'interfaccia della riga di comando di Azure](../linux/cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
> 
>

Se si desidera usare un disco esistente invece di un'immagine del marketplace, usare questo codice: 

```java
ManagedDisk managedDisk = azure.disks.define("myosdisk") 
    .withRegion(Region.US_EAST) 
    .withExistingResourceGroup("myResourceGroup") 
    .withWindowsFromVhd("https://mystorage.blob.core.windows.net/vhds/myosdisk.vhd") 
    .withSizeInGB(128) 
    .withSku(DiskSkuTypes.PremiumLRS) 
    .create(); 

azure.virtualMachines.define("myVM") 
    .withRegion(Region.US_EAST) 
    .withExistingResourceGroup("myResourceGroup") 
    .withExistingPrimaryNetworkInterface(networkInterface) 
    .withSpecializedOSDisk(managedDisk, OperatingSystemTypes.Windows) 
    .withExistingAvailabilitySet(availabilitySet) 
    .withSize(VirtualMachineSizeTypes.StandardDS1) 
    .create(); 
``` 

## <a name="perform-management-tasks"></a>Eseguire le attività di gestione

Durante il ciclo di vita di una macchina virtuale si eseguono attività di gestione come l'avvio, l'arresto o l'eliminazione della macchina virtuale. È inoltre possibile creare codice per automatizzare le attività ripetitive o complesse.

Quando si deve eseguire un'operazione con la macchina virtuale, è necessario ottenere un'istanza della VM. Aggiungere questo codice al blocco try nel metodo main:

```java
VirtualMachine vm = azure.virtualMachines().getByResourceGroup("myResourceGroup", "myVM");
```

### <a name="get-information-about-the-vm"></a>Ottenere informazioni sulla VM

Per ottenere informazioni sulla macchina virtuale, aggiungere questo codice al blocco try nel metodo main:

```java
System.out.println("hardwareProfile");
System.out.println("    vmSize: " + vm.size());
System.out.println("storageProfile");
System.out.println("  imageReference");
System.out.println("    publisher: " + vm.storageProfile().imageReference().publisher());
System.out.println("    offer: " + vm.storageProfile().imageReference().offer());
System.out.println("    sku: " + vm.storageProfile().imageReference().sku());
System.out.println("    version: " + vm.storageProfile().imageReference().version());
System.out.println("  osDisk");
System.out.println("    osType: " + vm.storageProfile().osDisk().osType());
System.out.println("    name: " + vm.storageProfile().osDisk().name());
System.out.println("    createOption: " + vm.storageProfile().osDisk().createOption());
System.out.println("    caching: " + vm.storageProfile().osDisk().caching());
System.out.println("osProfile");
System.out.println("    computerName: " + vm.osProfile().computerName());
System.out.println("    adminUserName: " + vm.osProfile().adminUsername());
System.out.println("    provisionVMAgent: " + vm.osProfile().windowsConfiguration().provisionVMAgent());
System.out.println("    enableAutomaticUpdates: " + vm.osProfile().windowsConfiguration().enableAutomaticUpdates());
System.out.println("networkProfile");
System.out.println("    networkInterface: " + vm.primaryNetworkInterfaceId());
System.out.println("vmAgent");
System.out.println("  vmAgentVersion: " + vm.instanceView().vmAgent().vmAgentVersion());
System.out.println("    statuses");
for(InstanceViewStatus status : vm.instanceView().vmAgent().statuses()) {
    System.out.println("    code: " + status.code());
    System.out.println("    displayStatus: " + status.displayStatus());
    System.out.println("    message: " + status.message());
    System.out.println("    time: " + status.time());
}
System.out.println("disks");
for(DiskInstanceView disk : vm.instanceView().disks()) {
    System.out.println("  name: " + disk.name());
    System.out.println("  statuses");
    for(InstanceViewStatus status : disk.statuses()) {
        System.out.println("    code: " + status.code());
        System.out.println("    displayStatus: " + status.displayStatus());
        System.out.println("    time: " + status.time());
    }
}
System.out.println("VM general status");
System.out.println("  provisioningStatus: " + vm.provisioningState());
System.out.println("  id: " + vm.id());
System.out.println("  name: " + vm.name());
System.out.println("  type: " + vm.type());
System.out.println("VM instance status");
for(InstanceViewStatus status : vm.instanceView().statuses()) {
    System.out.println("  code: " + status.code());
    System.out.println("  displayStatus: " + status.displayStatus());
}
System.out.println("Press enter to continue...");
input.nextLine();   
```

### <a name="stop-the-vm"></a>Arrestare la VM

È possibile arrestare una macchina virtuale e mantenere tutte le sue impostazioni, pur continuando a vedersela addebitata oppure è possibile arrestare una macchina virtuale e deallocarla. Quando una macchina virtuale viene deallocata, lo stesso viene fatto per tutte le risorse associate e la fatturazione termina.

Per arrestare la macchina virtuale senza deallocarla, aggiungere questo codice al blocco try nel metodo main:

```java
System.out.println("Stopping vm...");
vm.powerOff();
System.out.println("Press enter to continue...");
input.nextLine();
```

Per deallocare la macchina virtuale, sostituire la chiamata PowerOff con il codice seguente:

```java
vm.deallocate();
```

### <a name="start-the-vm"></a>Avviare la VM

Per avviare la macchina virtuale, aggiungere questo codice al blocco try nel metodo main:

```java
System.out.println("Starting vm...");
vm.start();
System.out.println("Press enter to continue...");
input.nextLine();
```

### <a name="resize-the-vm"></a>Ridimensionare la VM

Al momento di decidere le dimensioni della macchina virtuale, è necessario considerare molti aspetti della distribuzione. Per altre informazioni, vedere [Dimensioni delle macchine virtuali](sizes.md).  

Per modificare le dimensioni della macchina virtuale, aggiungere questo codice al blocco try nel metodo main:

```java
System.out.println("Resizing vm...");
vm.update()
    .withSize(VirtualMachineSizeTypes.STANDARD_DS2)
    .apply();
System.out.println("Press enter to continue...");
input.nextLine();
```

### <a name="add-a-data-disk-to-the-vm"></a>Aggiungere un disco dati alla VM

Per aggiungere un disco dati alla macchina virtuale con dimensioni di 2 GB, un numero di unità logica pari a 0 e una memorizzazione nella cache di tipo ReadWrite, aggiungere questo codice al blocco try nel metodo main:

```java
System.out.println("Adding data disk...");
vm.update()
    .withNewDataDisk(2, 0, CachingTypes.READ_WRITE)
    .apply();
System.out.println("Press enter to delete resources...");
input.nextLine();
```

## <a name="delete-resources"></a>Eliminare le risorse

Poiché vengono applicati addebiti per le risorse usate in Azure, è sempre consigliabile eliminare le risorse che non sono più necessarie. Per eliminare le macchine virtuali e tutte le risorse di supporto, è sufficiente eliminare il gruppo di risorse.

1. Per eliminare il gruppo di risorse, aggiungere questo codice al blocco try nel metodo main:
   
```java
System.out.println("Deleting resources...");
azure.resourceGroups().deleteByName("myResourceGroup");
```

2. Salvare il file App.java.

## <a name="run-the-application"></a>Eseguire l'applicazione

L'esecuzione completa dell'applicazione console dall'inizio alla fine richiederà circa cinque minuti.

1. Per eseguire l'applicazione, usare questo comando di Maven:

    ```
    mvn compile exec:java
    ```

2. Prima di premere **INVIO** per avviare l'eliminazione delle risorse, è consigliabile dedicare alcuni minuti alla verifica della creazione delle risorse nel Portale di Azure. Fare clic sullo stato della distribuzione per visualizzare le informazioni corrispondenti.


## <a name="next-steps"></a>Passaggi successivi
* Altre informazioni sull'uso delle [librerie di Azure per Java](https://docs.microsoft.com/en-us/java/azure/java-sdk-azure-overview).

