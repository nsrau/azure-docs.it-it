---
title: Creare e gestire una macchina virtuale Windows in Azure usando Python | Microsoft Docs
description: Informazioni sull'uso di Python per creare e gestire una macchina virtuale Windows in Azure.
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
ms.date: 06/22/2017
ms.author: davidmu
ms.translationtype: HT
ms.sourcegitcommit: 6e76ac40e9da2754de1d1aa50af3cd4e04c067fe
ms.openlocfilehash: 8ac757a84a932c434b508aa33c343c866a40bf02
ms.contentlocale: it-it
ms.lasthandoff: 07/31/2017

---

# <a name="create-and-manage-windows-vms-in-azure-using-python"></a>Creare e gestire macchine virtuali Windows in Azure usando Python

Una [macchina virtuale di Azure](overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (VM) richiede diverse risorse di supporto di Azure. Questo articolo descrive come creare, gestire ed eliminare le risorse della macchina virtuale usando Python. Si apprenderà come:

> [!div class="checklist"]
> * Creare un progetto di Visual Studio
> * Installare i pacchetti
> * Creare le credenziali
> * Creare le risorse
> * Eseguire le attività di gestione
> * Eliminare le risorse
> * Eseguire l'applicazione

L'esecuzione di questi passaggi richiede circa 20 minuti.

## <a name="create-a-visual-studio-project"></a>Creare un progetto di Visual Studio

1. Se non è già installato, installare [Visual Studio](https://docs.microsoft.com/visualstudio/install/install-visual-studio). Selezionare **Sviluppo Python** nella pagina Carichi di lavoro e quindi fare clic su **Installa**. Nel riepilogo si noti che **Python 3 a 64 bit (3.6.0)** viene selezionato automaticamente. Se Visual Studio è già stato installato, è possibile aggiungere il carico di lavoro Python usando l'utilità di avvio di Visual Studio.
2. Dopo aver installato e avviato Visual Studio, fare clic su **File** > **Nuovo** > **Progetto**.
3. Fare clic su **Modelli** > **Python** > **Applicazione Python**, immettere *myPythonProject* come nome del progetto, selezionare il percorso del progetto e quindi fare clic su **OK**.

## <a name="install-packages"></a>Installare i pacchetti

1. In Esplora soluzioni, in *myPythonProject*, fare clic con il pulsante destro del mouse su **Ambienti Python** e quindi selezionare **Aggiungi ambiente virtuale**.
2. Nella schermata Aggiungi ambiente virtuale accettare il nome predefinito *env*, verificare che *Python 3.6 (64 bit)* sia selezionato per l'interprete di base e quindi fare clic su **Crea**.
3. Fare clic con il pulsante destro del mouse sull'ambiente *env* appena creato, fare clic su **Installa pacchetto Python**, immettere *azure* nella casella di ricerca e quindi premere INVIO.

La corretta installazione dei pacchetti di Azure verrà indicata nelle finestre di output. 

## <a name="create-credentials"></a>Creare le credenziali

Prima di iniziare questo passaggio, verificare di disporre di un'[entità servizio Active Directory](../../azure-resource-manager/resource-group-create-service-principal-portal.md). È inoltre necessario registrare l'ID dell'applicazione, la chiave di autenticazione e l'ID del tenant che saranno necessari in un passaggio successivo.

1. Aprire il file *myPythonProject.py* che è stato creato e quindi aggiungere questo codice per consentire l'esecuzione dell'applicazione:

    ```python
    if __name__ == "__main__":
    ```

2. Per importare il codice necessario, aggiungere queste istruzioni all'inizio del file con estensione py:

    ```python
    from azure.common.credentials import ServicePrincipalCredentials
    from azure.mgmt.resource import ResourceManagementClient
    from azure.mgmt.compute import ComputeManagementClient
    from azure.mgmt.network import NetworkManagementClient
    from azure.mgmt.compute.models import DiskCreateOption
    ```

3. Nel file con estensione py aggiungere quindi le variabili dopo le istruzioni di importazione per specificare i valori comuni usati nel codice:
   
    ```
    SUBSCRIPTION_ID = 'subscription-id'
    GROUP_NAME = 'myResourceGroup'
    LOCATION = 'westus'
    VM_NAME = 'myVM'
    ```

    Sostituire **subscription-id** con l'identificatore della sottoscrizione.

4. Per creare le credenziali di Active Directory necessarie per inoltrare le richieste, aggiungere questa funzione dopo le variabili nel file con estensione py:

    ```python
    def get_credentials():
        credentials = ServicePrincipalCredentials(
            client_id = 'application-id',
            secret = 'authentication-key',
            tenant = 'tenant-id'
        )

        return credentials
    ```

    Sostituire **application-id**, **authentication-key** e **tenant-id** con i valori raccolti durante la creazione dell'entità servizio di Azure Active Directory.

5. Per chiamare la funzione aggiunta in precedenza, aggiungere questo codice sotto l'istruzione **if** alla fine del file con estensione py:

    ```python
    credentials = get_credentials()
    ```

## <a name="create-resources"></a>Creare le risorse
 
### <a name="initialize-management-clients"></a>Inizializzare i client di gestione

I client di gestione sono necessari per creare e gestire le risorse tramite Python SDK in Azure. Per creare i client di gestione, aggiungere questo codice sotto l'istruzione **if** alla fine del file con estensione py:

```python
resource_group_client = ResourceManagementClient(
    credentials, 
    SUBSCRIPTION_ID
)
network_client = NetworkManagementClient(
    credentials, 
    SUBSCRIPTION_ID
)
compute_client = ComputeManagementClient(
    credentials, 
    SUBSCRIPTION_ID
)
```

### <a name="create-the-vm-and-supporting-resources"></a>Creare la macchina virtuale e le risorse di supporto

Tutte le risorse devono essere contenute in un [gruppo di risorse](../../azure-resource-manager/resource-group-overview.md).

1. Per creare un gruppo di risorse, aggiungere questa funzione dopo le variabili nel file con estensione py:

    ```python
    def create_resource_group(resource_group_client):
        resource_group_params = { 'location':LOCATION }
        resource_group_result = resource_group_client.resource_groups.create_or_update(
            GROUP_NAME, 
            resource_group_params
        )
    ```

2. Per chiamare la funzione aggiunta in precedenza, aggiungere questo codice sotto l'istruzione **if** alla fine del file con estensione py:

    ```python
    create_resource_group(resource_group_client)
    input('Resource group created. Press enter to continue...')
    ```

I [set di disponibilità](tutorial-availability-sets.md) semplificano la manutenzione delle macchine virtuali usate dall'applicazione.

1. Per creare un set di disponibilità, aggiungere questa funzione dopo le variabili nel file con estensione py:
   
    ```python
    def create_availability_set(compute_client):
        avset_params = {
            'location': LOCATION,
            'sku': { 'name': 'Aligned' },
            'platform_fault_domain_count': 3
        }
        availability_set_result = compute_client.availability_sets.create_or_update(
            GROUP_NAME,
            'myAVSet',
            avset_params
        )
    ```

2. Per chiamare la funzione aggiunta in precedenza, aggiungere questo codice sotto l'istruzione **if** alla fine del file con estensione py:

    ```python
    create_availability_set(compute_client)
    print("------------------------------------------------------")
    input('Availability set created. Press enter to continue...')
    ```

Un [indirizzo IP pubblico](../../virtual-network/virtual-network-ip-addresses-overview-arm.md) è necessario per comunicare con la macchina virtuale.

1. Per creare un indirizzo IP pubblico per la macchina virtuale, aggiungere questa funzione dopo le variabili nel file con estensione py:

    ```python
    def create_public_ip_address(network_client):
        public_ip_addess_params = {
            'location': LOCATION,
            'public_ip_allocation_method': 'Dynamic'
        }
        creation_result = network_client.public_ip_addresses.create_or_update(
            GROUP_NAME,
            'myIPAddress',
            public_ip_addess_params
        )

        return creation_result.result()
    ```

2. Per chiamare la funzione aggiunta in precedenza, aggiungere questo codice sotto l'istruzione **if** alla fine del file con estensione py:

    ```python
    creation_result = create_public_ip_address(network_client)
    print("------------------------------------------------------")
    print(creation_result)
    input('Press enter to continue...')
    ```

Una macchina virtuale deve essere inclusa in una subnet di una [rete virtuale](../../virtual-network/virtual-networks-overview.md).

1. Per creare una rete virtuale, aggiungere questa funzione dopo le variabili nel file con estensione py:

    ```python
    def create_vnet(network_client):
        vnet_params = {
            'location': LOCATION,
            'address_space': {
                'address_prefixes': ['10.0.0.0/16']
            }
        }
        creation_result = network_client.virtual_networks.create_or_update(
            GROUP_NAME,
            'myVNet',
            vnet_params
        )
        return creation_result.result()
    ```

2. Per chiamare la funzione aggiunta in precedenza, aggiungere questo codice sotto l'istruzione **if** alla fine del file con estensione py:
   
    ```python
    creation_result = create_vnet(network_client)
    print("------------------------------------------------------")
    print(creation_result)
    input('Press enter to continue...')
    ```

3. Per aggiungere una subnet alla rete virtuale, aggiungere questa funzione dopo le variabili nel file con estensione py:
    
    ```python
    def create_subnet(network_client):
        subnet_params = {
            'address_prefix': '10.0.0.0/24'
        }
        creation_result = network_client.subnets.create_or_update(
            GROUP_NAME,
            'myVNet',
            'mySubnet',
            subnet_params
        )

        return creation_result.result()
    ```
        
4. Per chiamare la funzione aggiunta in precedenza, aggiungere questo codice sotto l'istruzione **if** alla fine del file con estensione py:
   
    ```python
    creation_result = create_subnet(network_client)
    print("------------------------------------------------------")
    print(creation_result)
    input('Press enter to continue...')
    ```

Una macchina virtuale richiede un'interfaccia di rete per comunicare nella rete virtuale.

1. Per creare un'interfaccia di rete, aggiungere questa funzione dopo le variabili nel file con estensione py:

    ```python
    def create_nic(network_client):
        subnet_info = network_client.subnets.get(
            GROUP_NAME, 
            'myVNet', 
            'mySubnet'
        )
        publicIPAddress = network_client.public_ip_addresses.get(
            GROUP_NAME,
            'myIPAddress'
        )
        nic_params = {
            'location': LOCATION,
            'ip_configurations': [{
                'name': 'myIPConfig',
                'public_ip_address': publicIPAddress,
                'subnet': {
                    'id': subnet_info.id
                }
            }]
        }
        creation_result = network_client.network_interfaces.create_or_update(
            GROUP_NAME,
            'myNic',
            nic_params
        )

        return creation_result.result()
    ```

2. Per chiamare la funzione aggiunta in precedenza, aggiungere questo codice sotto l'istruzione **if** alla fine del file con estensione py:

    ```python
    creation_result = create_nic(network_client)
    print("------------------------------------------------------")
    print(creation_result)
    input('Press enter to continue...')
    ```

Dopo avere creato tutte le risorse di supporto, è possibile creare una macchina virtuale.

1. Per creare una macchina virtuale, aggiungere questa funzione dopo le variabili nel file con estensione py:
   
    ```python
    def create_vm(network_client, compute_client):  
        nic = network_client.network_interfaces.get(
            GROUP_NAME, 
            'myNic'
        )
        avset = compute_client.availability_sets.get(
            GROUP_NAME,
            'myAVSet'
        )
        vm_parameters = {
            'location': LOCATION,
            'os_profile': {
                'computer_name': VM_NAME,
                'admin_username': 'azureuser',
                'admin_password': 'Azure12345678'
            },
            'hardware_profile': {
                'vm_size': 'Standard_DS1'
            },
            'storage_profile': {
                'image_reference': {
                    'publisher': 'MicrosoftWindowsServer',
                    'offer': 'WindowsServer',
                    'sku': '2012-R2-Datacenter',
                    'version': 'latest'
                }
            },
            'network_profile': {
                'network_interfaces': [{
                    'id': nic.id
                }]
            },
            'availability_set': {
                'id': avset.id
            }
        }
        creation_result = compute_client.virtual_machines.create_or_update(
            GROUP_NAME, 
            VM_NAME, 
            vm_parameters
        )
    
        return creation_result.result()
    ```

    > [!NOTE]
    > Questa esercitazione illustra come creare una macchina virtuale in cui è in esecuzione una versione del sistema operativo Windows Server. Per altre informazioni sulla selezione di altre immagini, vedere [Esplorare e selezionare immagini delle macchine virtuali di Azure con Windows PowerShell e l'interfaccia della riga di comando di Azure](../linux/cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
    > 
    > 

2. Per chiamare la funzione aggiunta in precedenza, aggiungere questo codice sotto l'istruzione **if** alla fine del file con estensione py:

    ```python
    creation_result = create_vm(network_client, compute_client)
    print("------------------------------------------------------")
    print(creation_result)
    input('Press enter to continue...')
    ```

## <a name="perform-management-tasks"></a>Eseguire le attività di gestione

Durante il ciclo di vita di una macchina virtuale si eseguono attività di gestione come l'avvio, l'arresto o l'eliminazione della macchina virtuale. È inoltre possibile creare codice per automatizzare le attività ripetitive o complesse.

### <a name="get-information-about-the-vm"></a>Ottenere informazioni sulla VM

1. Per ottenere informazioni sulla macchina virtuale, aggiungere questa funzione dopo le variabili nel file con estensione py:

    ```python
    def get_vm(compute_client):
        vm = compute_client.virtual_machines.get(GROUP_NAME, VM_NAME, expand='instanceView')
        print("hardwareProfile")
        print("   vmSize: ", vm.hardware_profile.vm_size)
        print("\nstorageProfile")
        print("  imageReference")
        print("    publisher: ", vm.storage_profile.image_reference.publisher)
        print("    offer: ", vm.storage_profile.image_reference.offer)
        print("    sku: ", vm.storage_profile.image_reference.sku)
        print("    version: ", vm.storage_profile.image_reference.version)
        print("  osDisk")
        print("    osType: ", vm.storage_profile.os_disk.os_type.value)
        print("    name: ", vm.storage_profile.os_disk.name)
        print("    createOption: ", vm.storage_profile.os_disk.create_option.value)
        print("    caching: ", vm.storage_profile.os_disk.caching.value)
        print("\nosProfile")
        print("  computerName: ", vm.os_profile.computer_name)
        print("  adminUsername: ", vm.os_profile.admin_username)
        print("  provisionVMAgent: {0}".format(vm.os_profile.windows_configuration.provision_vm_agent))
        print("  enableAutomaticUpdates: {0}".format(vm.os_profile.windows_configuration.enable_automatic_updates))
        print("\nnetworkProfile")
        for nic in vm.network_profile.network_interfaces:
            print("  networkInterface id: ", nic.id)
        print("\nvmAgent")
        print("  vmAgentVersion", vm.instance_view.vm_agent.vm_agent_version)
        print("    statuses")
        for stat in vm_result.instance_view.vm_agent.statuses:
            print("    code: ", stat.code)
            print("    displayStatus: ", stat.display_status)
            print("    message: ", stat.message)
            print("    time: ", stat.time)
        print("\ndisks");
        for disk in vm.instance_view.disks:
            print("  name: ", disk.name)
            print("  statuses")
            for stat in disk.statuses:
                print("    code: ", stat.code)
                print("    displayStatus: ", stat.display_status)
                print("    time: ", stat.time)
        print("\nVM general status")
        print("  provisioningStatus: ", vm.provisioning_state)
        print("  id: ", vm.id)
        print("  name: ", vm.name)
        print("  type: ", vm.type)
        print("  location: ", vm.location)
        print("\nVM instance status")
        for stat in vm.instance_view.statuses:
            print("  code: ", stat.code)
            print("  displayStatus: ", stat.display_status)
    ```
2. Per chiamare la funzione aggiunta in precedenza, aggiungere questo codice sotto l'istruzione **if** alla fine del file con estensione py:

    ```python
    get_vm(compute_client)
    print("------------------------------------------------------")
    input('Press enter to continue...')
    ```

### <a name="stop-the-vm"></a>Arrestare la VM

È possibile arrestare una macchina virtuale e mantenere tutte le sue impostazioni, pur continuando a vedersela addebitata oppure è possibile arrestare una macchina virtuale e deallocarla. Quando una macchina virtuale viene deallocata, lo stesso viene fatto per tutte le risorse associate e la fatturazione termina.

1. Per arrestare la macchina virtuale senza deallocarla, aggiungere questa funzione dopo le variabili nel file con estensione py:

    ```python
    def stop_vm(compute_client):
        compute_client.virtual_machines.power_off(GROUP_NAME, VM_NAME)
    ```

    Per deallocare la macchina virtuale, sostituire la chiamata power_off con il codice seguente:

    ```python
    compute_client.virtual_machines.deallocate(GROUP_NAME, VM_NAME)
    ```

2. Per chiamare la funzione aggiunta in precedenza, aggiungere questo codice sotto l'istruzione **if** alla fine del file con estensione py:

    ```python
    stop_vm(compute_client)
    input('Press enter to continue...')
    ```

### <a name="start-the-vm"></a>Avviare la VM

1. Per avviare la macchina virtuale, aggiungere questa funzione dopo le variabili nel file con estensione py:

    ```python
    def start_vm(compute_client):
        compute_client.virtual_machines.start(GROUP_NAME, VM_NAME)
    ```

2. Per chiamare la funzione aggiunta in precedenza, aggiungere questo codice sotto l'istruzione **if** alla fine del file con estensione py:

    ```python
    start_vm(compute_client)
    input('Press enter to continue...')
    ```

### <a name="resize-the-vm"></a>Ridimensionare la VM

Al momento di decidere le dimensioni della macchina virtuale, è necessario considerare molti aspetti della distribuzione. Per altre informazioni, vedere [Dimensioni delle macchine virtuali](sizes.md).

1. Per modificare le dimensioni della macchina virtuale, aggiungere questa funzione dopo le variabili nel file con estensione py:

    ```python
    def update_vm(compute_client):
        vm = compute_client.virtual_machines.get(GROUP_NAME, VM_NAME)
        vm.hardware_profile.vm_size = 'Standard_DS3'
        update_result = compute_client.virtual_machines.create_or_update(
            GROUP_NAME, 
            VM_NAME, 
            vm
        )

    return update_result.result()
    ```

2. Per chiamare la funzione aggiunta in precedenza, aggiungere questo codice sotto l'istruzione **if** alla fine del file con estensione py:

    ```python
    update_result = update_vm(compute_client)
    print("------------------------------------------------------")
    print(update_result)
    input('Press enter to continue...')
    ```

### <a name="add-a-data-disk-to-the-vm"></a>Aggiungere un disco dati alla VM

Le macchine virtuali possono disporre di uno o più [dischi dati](../../storage/storage-about-disks-and-vhds-windows.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) archiviati in dischi rigidi virtuali.

1. Per aggiungere un disco dati alla macchina virtuale, aggiungere questa funzione dopo le variabili nel file con estensione py: 

    ```python
    def add_datadisk(compute_client):
        disk_creation = compute_client.disks.create_or_update(
            GROUP_NAME,
            'myDataDisk1',
            {
                'location': LOCATION,
                'disk_size_gb': 1,
                'creation_data': {
                    'create_option': DiskCreateOption.empty
                }
            }
        )
        data_disk = disk_creation.result()
        vm = compute_client.virtual_machines.get(GROUP_NAME, VM_NAME)
        add_result = vm.storage_profile.data_disks.append({
            'lun': 1,
            'name': 'myDataDisk1',
            'create_option': DiskCreateOption.attach,
            'managed_disk': {
                'id': data_disk.id
            }
        })
        add_result = compute_client.virtual_machines.create_or_update(
            GROUP_NAME,
            VM_NAME,
            vm)

        return add_result.result()
    ```

2. Per chiamare la funzione aggiunta in precedenza, aggiungere questo codice sotto l'istruzione **if** alla fine del file con estensione py:

    ```python
    add_result = add_datadisk(compute_client)
    print("------------------------------------------------------")
    print(add_result)
    input('Press enter to continue...')
    ```

## <a name="delete-resources"></a>Eliminare le risorse

Dal momento che le risorse usate in Azure vengono addebitate, è sempre consigliabile eliminare le risorse non più necessarie. Per eliminare le macchine virtuali e tutte le risorse di supporto, è sufficiente eliminare il gruppo di risorse.

1. Per eliminare il gruppo di risorse e tutte le risorse, aggiungere questa funzione dopo le variabili nel file con estensione py:
   
    ```python
    def delete_resources(resource_group_client):
        resource_group_client.resource_groups.delete(GROUP_NAME)
    ```

2. Per chiamare la funzione aggiunta in precedenza, aggiungere questo codice sotto l'istruzione **if** alla fine del file con estensione py:
   
    ```python
    delete_resources(resource_group_client)
    ```

3. Salvare *myPythonProject.py*.

## <a name="run-the-application"></a>Eseguire l'applicazione

1. Per eseguire l'applicazione console, fare clic su **Avvia** in Visual Studio.

2. Premere **INVIO** dopo che è stato restituito lo stato di ogni risorsa. Nelle informazioni sullo stato dovrebbe essere visualizzato lo stato di provisioning **Completato**. Dopo che la macchina virtuale è stata creata, si avrà l'opportunità di eliminare tutte le risorse create. Prima di premere **INVIO** per iniziare a eliminare le risorse, è consigliabile dedicare qualche minuto alla verifica della loro creazione nel portale di Azure. Se il portale di Azure è aperto, potrebbe essere necessario aggiornare il pannello per visualizzare le nuove risorse.  

    L'esecuzione completa dell'applicazione console dall'inizio alla fine richiederà circa cinque minuti. L'eliminazione di tutte le risorse e del gruppo di risorse potrebbe richiedere alcuni minuti dopo l'esecuzione completa dell'applicazione.


## <a name="next-steps"></a>Passaggi successivi

- Se si sono verificati problemi con la distribuzione, è consigliabile vedere come [risolvere i problemi della distribuzione di gruppi di risorse con il portale di Azure](../../resource-manager-troubleshoot-deployments-portal.md)
- Altre informazioni sulla [libreria Python di Azure](https://docs.microsoft.com/python/api/overview/azure/?view=azure-python)


