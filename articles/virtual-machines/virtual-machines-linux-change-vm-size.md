<properties
   pageTitle="Ridimensionare una VM Linux | Microsoft Azure"
   description="Scalare in verticale o orizzontale una macchina virtuale di Linux, modificando le dimensioni della VM."
   services="virtual-machines-linux"
   documentationCenter="na"
   authors="mikewasson"
   manager="roshar"
   editor=""
   tags=""/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="05/16/2016"
   ms.author="mikewasson"/>


# Come ridimensionare una VM di Linux

## Panoramica 

Dopo aver eseguito il provisioning di una macchina virtuale (VM), è possibile scalare la macchina virtuale in verticale o orizzontale modificando le [dimensioni della VM][vm-sizes]. In alcuni casi, è necessario prima deallocare la macchina virtuale. Questa situazione può verificarsi se le nuove dimensioni non sono disponibili nel cluster hardware che ospita la VM.

In questo articolo viene illustrato come ridimensionare una VM Linux mediante l'[Interfaccia della riga di comando di Azure][azure-cli].

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]Modello di distribuzione classica.


## Ridimensionare una VM di Linux 

Per ridimensionare una VM, eseguire la procedura seguente.

1. Eseguire il comando dell'Interfaccia della riga di comando seguente. Questo comando elenca le dimensioni delle VM che sono disponibili nel cluster hardware in cui è ospitata la VM.

    ```
    azure vm sizes -g <resource-group> --vm-name <vm-name>
    ```

2. Se le dimensioni desiderate sono nell'elenco, eseguire il comando seguente per ridimensionare la VM.

    ```
    azure vm set -g <resource-group> --vm-size <new-vm-size> -n <vm-name>  
        --enable-boot-diagnostics --boot-diagnostics-storage-uri
        https://<storage-account-name>.blob.core.windows.net/ 
    ```

    Durante questo processo verrà riavviata la VM. Dopo il riavvio, si rimappano il sistema operativo esistente e i dischi di dati. Qualsiasi elemento presente nel disco temporaneo andrà perso.

    L'uso dell'opzione `--enable-boot-diagnostics` consente alla [diagnostica di avvio][boot-diagnostics] di registrare eventuali errori correlati all'avvio.

3. In caso contrario, se la dimensione desiderata non è nell'elenco, eseguire i comandi seguenti per deallocare la VM, ridimensionarla e quindi riavviare la VM.

    ```
    azure vm deallocate -g <resource-group> <vm-name>
    azure vm set -g <resource-group> --vm-size <new-vm-size> -n <vm-name>  
        --enable-boot-diagnostics --boot-diagnostics-storage-uri
        https://<storage-account-name>.blob.core.windows.net/ 
    azure vm start -g <resource-group> <vm-name>
    ```

   > [AZURE.WARNING] La deallocazione della VM rilascia anche degli indirizzi IP dinamici assegnati alla VM. I dischi del sistema operativo e dei dati non sono coinvolti.
   
## Passaggi successivi

Per una maggiore scalabilità, eseguire più istanze di VM e la scalabilità orizzontale. Per altre informazioni, vedere [Ridimensionare automaticamente macchine virtuali Linux in un set di scalabilità di macchine virtuali][scale-set].

<!-- links -->
   
[azure-cli]: ../xplat-cli-install.md
[boot-diagnostics]: https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/
[scale-set]: ../virtual-machine-scale-sets/virtual-machine-scale-sets-linux-autoscale.md
[vm-sizes]: virtual-machines-linux-sizes.md

<!---HONumber=AcomDC_0518_2016-->