---
title: Creare una macchina virtuale Windows con PowerShell | Microsoft Docs
description: Creare macchine virtuali Windows con Azure PowerShell e il modello di distribuzione classica.
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 42c0d4be-573c-45d1-b9b0-9327537702f7
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 09/27/2016
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: 356de369ec5409e8e6e51a286a20af70a9420193
ms.openlocfilehash: d9b713460f98104017ae73ea27f30b0d8d1ca7ee
ms.lasthandoff: 03/27/2017


---
# <a name="create-a-windows-virtual-machine-with-powershell-and-the-classic-deployment-model"></a>Creare macchine virtuali Windows con il modello di distribuzione classica e PowerShell
> [!div class="op_single_selector"]
> * [Portale di Azure classico - Windows](tutorial.md)
> * [PowerShell - Windows](create-powershell.md)
> 
> 

<br>

> [!IMPORTANT] 
> Azure offre due diversi modelli di distribuzione per creare e usare le risorse: [Gestione risorse e la distribuzione classica](../../../resource-manager-deployment-model.md). Questo articolo illustra l'uso del modello di distribuzione classica. Microsoft consiglia di usare il modello di Gestione risorse per le distribuzioni più recenti. Informazioni su come [eseguire questa procedura con il modello di Resource Manager](../../virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

In questi passaggi viene illustrato come personalizzare un set di comandi di Azure PowerShell per la creazione e la preconfigurazione di una macchina virtuale di Azure basata su Windows tramite un approccio con componenti principali. È possibile usare questo processo per creare rapidamente un set di comandi per una nuova macchina virtuale basata su Windows ed espandere una distribuzione esistente oppure creare più set di comandi in grado di generare rapidamente un ambiente personalizzato di sviluppo/test o per professionisti IT.

Questi passaggi seguono un approccio basato sul completamento di valori predefiniti per la creazione di set di comandi di Azure PowerShell. Questo approccio può essere utile se non si è esperti di PowerShell o per sapere semplicemente quali valori specificare per una corretta configurazione. Gli utenti esperti di PowerShell possono usare i comandi sostituendo le variabili (le righe che iniziano con "$") con i propri valori.

Se non è ancora stato installato, attenersi alle istruzioni incluse nell’argomento [Come installare e configurare Azure PowerShell](/powershell/azureps-cmdlets-docs) per installare Azure PowerShell nel computer locale. Quindi, aprire un prompt dei comandi di Windows PowerShell.

## <a name="step-1-add-your-account"></a>Passaggio 1: Aggiungere l'account
1. Al prompt di PowerShell digitare **Add-AzureAccount** e premere **INVIO**. 
2. Digitare l'indirizzo di posta elettronica associato alla sottoscrizione di Azure e fare clic su **Continua**. 
3. Digitare la password per l'account. 
4. Fare clic su **Accedi**.

## <a name="step-2-set-your-subscription-and-storage-account"></a>Passaggio 2: Impostare l'account di archiviazione e la sottoscrizione
Impostare la sottoscrizione di Azure e l'account di archiviazione eseguendo questi comandi al prompt dei comandi di Windows PowerShell. Sostituire tutti gli elementi all'interno delle virgolette, inclusi i caratteri < e >, con i nomi corretti.

    $subscr="<subscription name>"
    $staccount="<storage account name>"
    Select-AzureSubscription -SubscriptionName $subscr –Current
    Set-AzureSubscription -SubscriptionName $subscr -CurrentStorageAccountName $staccount

È possibile ottenere il nome della sottoscrizione corretto dalla proprietà SubscriptionName dell'output del comando **Get-AzureSubscription** . È possibile ottenere il nome dell'account di archiviazione corretto dalla proprietà Label dell'output del comando **Get-AzureStorageAccount** dopo aver eseguito il comando **Select-AzureSubscription**.

## <a name="step-3-determine-the-imagefamily"></a>Passaggio 3: determinare il valore ImageFamily
Il passaggio successivo prevede la determinazione del valore ImageFamily o Label per l'immagina specifica corrispondente alla macchina virtuale di Azure da creare. Con questo comando è possibile ottenere l'elenco di valori ImageFamily disponibili.

    Get-AzureVMImage | select ImageFamily -Unique

Di seguito sono riportati alcuni esempi di valori ImageFamily per i computer basati su Windows:

* Windows Server 2012 R2 Datacenter
* Windows Server 2008 R2 SP1,
* Windows Server 2016 Technical Preview 4
* SQL Server 2012 SP1 Enterprise in Windows Server 2012

Se si trova l'immagine che si sta cercando, aprire una nuova istanza dell'editor di testo preferito o l'ambiente PowerShell Integrated Scripting Environment (ISE). Copiare quanto segue nel nuovo file di testo o in PowerShell ISE, sostituendo il valore ImageFamily.

    $family="<ImageFamily value>"
    $image=Get-AzureVMImage | where { $_.ImageFamily -eq $family } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1

In alcuni casi, il nome dell'immagine si trova nella proprietà Label anziché nel valore ImageFamily. Se l'immagine che si sta cercando non viene trovata con la proprietà ImageFamily, elencare le immagini in base alla proprietà Label con questo comando.

    Get-AzureVMImage | select Label -Unique

Se si trova l'immagine corretta con questo comando, aprire una nuova istanza dell'editor di testo preferito o l'ambiente PowerShell ISE. Copiare quanto segue nel nuovo file di testo o in PowerShell ISE, sostituendo il valore Label.

    $label="<Label value>"
    $image = Get-AzureVMImage | where { $_.Label -eq $label } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1

## <a name="step-4-build-your-command-set"></a>Passaggio 4: compilare il set di comandi
Compilare il resto del set di comandi copiando il seguente set appropriato di blocchi nel nuovo file di testo o in ISE, quindi compilare i valori delle variabili rimuovendo i caratteri < e >. Vedere i due [esempi](#examples) alla fine di questo articolo per avere un'idea del risultato finale.

Avviare il set di comandi scegliendo uno dei due seguenti blocchi di comandi (obbligatorio).

Opzione 1: specificare un nome di macchina virtuale e una dimensione.

    $vmname="<machine name>"
    $vmsize="<Specify one: Small, Medium, Large, ExtraLarge, A5, A6, A7, A8, A9>"
    $vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image

Opzione 2: specificare un nome, la dimensione e il nome del set di disponibilità.

    $vmname="<machine name>"
    $vmsize="<Specify one: Small, Medium, Large, ExtraLarge, A5, A6, A7, A8, A9>"
    $availset="<set name>"
    $vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image -AvailabilitySetName $availset

Per i valori InstanceSize per le macchine virtuali di serie D-, DS- o G-, vedere [Dimensioni delle macchine virtuali e dei servizi cloud per Azure](https://msdn.microsoft.com/library/azure/dn197896.aspx).

> [!NOTE]
> Se si dispone di un contratto Enterprise con Software Assurance e si intende sfruttare il [vantaggio dell'uso ibrido](https://azure.microsoft.com/pricing/hybrid-use-benefit/) di Windows Server, aggiungere il parametro **-LicenseType** al cmdlet **New-AzureVMConfig**, passando il valore **Windows_Server** come caso d'uso tipico.  Controllare che si stia usando un'immagine caricata, poiché con il vantaggio dell'uso ibrido non è consentito usare un'immagine standard della raccolta.
> 
> 

Facoltativamente, per un computer Windows autonomo, specificare l'account amministratore locale e la password.

    $cred=Get-Credential -Message "Type the name and password of the local administrator account."
    $vm1 | Add-AzureProvisioningConfig -Windows -AdminUsername $cred.Username -Password $cred.GetNetworkCredential().Password

Scegliere una password complessa. Per verificarne il livello di complessità, vedere [Controllo password: utilizzo di password complesse](https://www.microsoft.com/security/pc-security/password-checker.aspx).

Facoltativamente, per aggiungere il computer Windows a un dominio di Active Directory esistente, specificare l'account e la password di amministratore locale, il dominio e infine il nome e la password di un account di dominio.

    $cred1=Get-Credential –Message "Type the name and password of the local administrator account."
    $cred2=Get-Credential –Message "Now type the name (not including the domain) and password of an account that has permission to add the machine to the domain."
    $domaindns="<FQDN of the domain that the machine is joining>"
    $domacctdomain="<domain of the account that has permission to add the machine to the domain>"
    $vm1 | Add-AzureProvisioningConfig -AdminUsername $cred1.Username -Password $cred1.GetNetworkCredential().Password -WindowsDomain -Domain $domacctdomain -DomainUserName $cred2.Username -DomainPassword $cred2.GetNetworkCredential().Password -JoinDomain $domaindns

Per altre opzioni di preconfigurazione per le macchine virtuali basate su Windows, vedere la sintassi per i set di parametri **Windows** e **WindowsDomain** in [Add-AzureProvisioningConfig](https://msdn.microsoft.com/library/azure/dn495299.aspx).

Facoltativamente, assegnare alla macchina virtuale un indirizzo IP specifico, noto come DIP statico.

    $vm1 | Set-AzureStaticVNetIP -IPAddress <IP address>

È possibile verificare la disponibilità di uno specifico indirizzo IP con:

    Test-AzureStaticVNetIP –VNetName <VNet name> –IPAddress <IP address>

Facoltativamente, assegnare la macchina virtuale a una subnet specifica in una rete virtuale di Azure.

    $vm1 | Set-AzureSubnet -SubnetNames "<name of the subnet>"

Facoltativamente, aggiungere un disco dati alla macchina virtuale.

    $disksize=<size of the disk in GB>
    $disklabel="<the label on the disk>"
    $lun=<Logical Unit Number (LUN) of the disk>
    $hcaching="<Specify one: ReadOnly, ReadWrite, None>"
    $vm1 | Add-AzureDataDisk -CreateNew -DiskSizeInGB $disksize -DiskLabel $disklabel -LUN $lun -HostCaching $hcaching

Per un controller di dominio di Active Directory, impostare $hcaching su "None".

Facoltativamente, aggiungere la macchina virtuale a un set con carico bilanciato esistente per il traffico esterno.

    $protocol="<Specify one: tcp, udp>"
    $localport=<port number of the internal port>
    $pubport=<port number of the external port>
    $endpointname="<name of the endpoint>"
    $lbsetname="<name of the existing load-balanced set>"
    $probeprotocol="<Specify one: tcp, http>"
    $probeport=<TCP or HTTP port number of probe traffic>
    $probepath="<URL path for probe traffic>"
    $vm1 | Add-AzureEndpoint -Name $endpointname -Protocol $protocol -LocalPort $localport -PublicPort $pubport -LBSetName $lbsetname -ProbeProtocol $probeprotocol -ProbePort $probeport -ProbePath $probepath

Infine, scegliere uno di questi blocchi di comandi richiesti per la creazione della macchina virtuale.

Opzione 1: creare la macchina virtuale in un servizio cloud esistente.

    New-AzureVM –ServiceName "<short name of the cloud service>" -VMs $vm1

Il nome breve del servizio cloud è il nome visualizzato nell'elenco dei servizi cloud nel portale di Azure classico o nell'elenco dei gruppi di risorse nel portale di Azure.

Opzione 2: creare la macchina virtuale in un servizio cloud e in una rete virtuale esistenti.

    $svcname="<short name of the cloud service>"
    $vnetname="<name of the virtual network>"
    New-AzureVM –ServiceName $svcname -VMs $vm1 -VNetName $vnetname

## <a name="step-5-run-your-command-set"></a>Passaggio 5: eseguire il set di comandi
Rivedere il set di comandi di Azure PowerShell compilato nell'editor di testo o in PowerShell ISE costituito da più blocchi di comandi del passaggio 4. Assicurarsi di aver specificato tutte le variabili necessarie e che siano presenti i valori corretti. Assicurarsi inoltre che siano stati rimossi tutti i caratteri < e >.

Se si usa un editor di testo, copiare il set di comandi negli Appunti, quindi fare clic con il pulsante destro del mouse sul prompt dei comandi aperto di Windows PowerShell. Il set di comandi verrà emesso come una serie di comandi di PowerShell e verrà creata la macchina virtuale di Azure. In alternativa, eseguire il comando set in PowerShell ISE.

Se si crea nuovamente questa macchina virtuale o una simile, è possibile:

* Salvare questo set di comandi come file di script di PowerShell (*.ps1).
* Salvare questo set di comandi come Runbook di automazione di Azure nella sezione **Automazione** del portale di Azure classico.

## <a id="examples"></a>Esempi:
Di seguito sono riportati due esempi d'uso dei passaggi precedenti per compilare i set di comandi di Azure PowerShell per la creazione di macchine virtuali Azure basate su Windows.

### <a name="example-1"></a>Esempio 1
Un set di comandi di PowerShell è necessario per creare la macchina virtuale iniziale per un controller di dominio di Active Directory che:

* Utilizzi l'immagine Windows Server 2012 R2 Datacenter.
* Sia denominato AZDC1.
* Sia un computer autonomo.
* Disponga di un disco dati aggiuntivo di 20 GB.
* Abbia l'indirizzo IP statico 192.168.244.4.
* Si trovi nella subnet BackEnd della rete virtuale AZDatacenter.
* Si trovi nel servizio cloud Azure-TailspinToys.

Ecco il set di comandi corrispondente di Azure PowerShell per creare la macchina virtuale, con righe vuote tra ogni blocco per migliorare la leggibilità.

    $family="Windows Server 2012 R2 Datacenter"
    $image=Get-AzureVMImage | where { $_.ImageFamily -eq $family } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1
    $vmname="AZDC1"
    $vmsize="Medium"
    $vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image

    $cred=Get-Credential -Message "Type the name and password of the local administrator account."
    $vm1 | Add-AzureProvisioningConfig -Windows -AdminUsername $cred.Username -Password $cred.GetNetworkCredential().Password

    $vm1 | Set-AzureSubnet -SubnetNames "BackEnd"

    $vm1 | Set-AzureStaticVNetIP -IPAddress 192.168.244.4

    $disksize=20
    $disklabel="DCData"
    $lun=0
    $hcaching="None"
    $vm1 | Add-AzureDataDisk -CreateNew -DiskSizeInGB $disksize -DiskLabel $disklabel -LUN $lun -HostCaching $hcaching

    $svcname="Azure-TailspinToys"
    $vnetname="AZDatacenter"
    New-AzureVM –ServiceName $svcname -VMs $vm1 -VNetName $vnetname

### <a name="example-2"></a>Esempio 2
Un set di comandi di PowerShell è necessario per creare una macchina virtuale per un server line-of-business che:

* Utilizzi l'immagine Windows Server 2012 R2 Datacenter.
* Sia denominato LOB1.
* Sia membro del dominio corp.contoso.com.
* Disponga di un disco dati aggiuntivo di 200 GB.
* Si trovi nella subnet FrontEnd della rete virtuale AZDatacenter.
* Si trovi nel servizio cloud Azure-TailspinToys.

Ecco il set di comandi corrispondente di Azure PowerShell per creare la macchina virtuale.

    $family="Windows Server 2012 R2 Datacenter"
    $image=Get-AzureVMImage | where { $_.ImageFamily -eq $family } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1
    $vmname="LOB1"
    $vmsize="Large"
    $vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image

    $cred1=Get-Credential –Message "Type the name and password of the local administrator account."
    $cred2=Get-Credential –Message "Now type the name (not including the domain) and password of an account that has permission to add the machine to the domain."
    $domaindns="corp.contoso.com"
    $domacctdomain="CORP"
    $vm1 | Add-AzureProvisioningConfig -AdminUsername $cred1.Username -Password $cred1.GetNetworkCredential().Password -WindowsDomain -Domain $domacctdomain -DomainUserName $cred2.Username -DomainPassword $cred2.GetNetworkCredential().Password -JoinDomain $domaindns

    $vm1 | Set-AzureSubnet -SubnetNames "FrontEnd"

    $disksize=200
    $disklabel="LOBData"
    $lun=0
    $hcaching="ReadWrite"
    $vm1 | Add-AzureDataDisk -CreateNew -DiskSizeInGB $disksize -DiskLabel $disklabel -LUN $lun -HostCaching $hcaching

    $svcname="Azure-TailspinToys"
    $vnetname="AZDatacenter"
    New-AzureVM –ServiceName $svcname -VMs $vm1 -VNetName $vnetname


## <a name="next-steps"></a>Passaggi successivi
Se è necessario un disco del sistema operativo superiore a 127 GB, è possibile [espandere l'unità del sistema operativo](../../virtual-machines-windows-expand-os-disk.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).


