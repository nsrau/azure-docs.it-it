---
title: 'Azure Active Directory Domain Services: Guida all&quot;amministrazione | Microsoft Docs'
description: Aggiungere una macchina virtuale Windows a un dominio gestito con Azure PowerShell e il modello di distribuzione classica.
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: stevenpo
editor: curtand
ms.assetid: 9143b843-7327-43c3-baab-6e24a18db25e
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/01/2016
ms.author: maheshu
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 4eb9a5c4087fc7e91d79a7789a261d590485555a


---
# <a name="join-a-windows-server-virtual-machine-to-a-managed-domain-using-powershell"></a>Aggiungere una macchina virtuale Windows Server a un dominio gestito usando PowerShell
> [!div class="op_single_selector"]
> * [Portale di Azure classico - Windows](active-directory-ds-admin-guide-join-windows-vm.md)
> * [PowerShell - Windows](active-directory-ds-admin-guide-join-windows-vm-classic-powershell.md)
> 
> 

<br>

> [!IMPORTANT]
> Azure offre due modelli di distribuzione per creare e usare le risorse: [Gestione risorse e la distribuzione classica](../azure-resource-manager/resource-manager-deployment-model.md). Questo articolo illustra l'uso del modello di distribuzione classica. Servizi di dominio Active Directory non supporta attualmente il modello Resource Manager.
> 
> 

In questi passaggi viene illustrato come personalizzare un set di comandi di Azure PowerShell per la creazione e la preconfigurazione di una macchina virtuale di Azure basata su Windows tramite un approccio con componenti principali. Questi passaggi consentono di creare una macchina virtuale di Azure basata su Windows e aggiungerla a un dominio gestito da Servizi di dominio Active Directory di Azure.

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

## <a name="step-3-step-by-step-walkthrough---provision-the-virtual-machine-and-join-it-to-the-managed-domain"></a>Passaggio 3: Procedura dettagliata: eseguire il provisioning della macchina virtuale e aggiungerla al dominio gestito
Ecco il set di comandi corrispondente di Azure PowerShell per creare la macchina virtuale, con righe vuote tra ogni blocco per migliorare la leggibilità.

Specificare le informazioni relative alla macchina virtuale di Windows di cui eseguire il provisioning.

    $family="Windows Server 2012 R2 Datacenter"
    $vmname="Contoso100-test"
    $vmsize="ExtraSmall"

Per i valori InstanceSize per le macchine virtuali di serie D-, DS- o G-, vedere [Dimensioni delle macchine virtuali e dei servizi cloud per Azure](https://msdn.microsoft.com/library/azure/dn197896.aspx).

Specificare le informazioni sul dominio gestito.

    $domaindns="contoso100.com"
    $domacctdomain="contoso100"

Specificare il nome del servizio cloud.

    $svcname="Contoso100-test"

Specificare il nome della rete virtuale a cui aggiungere la macchina virtuale. Assicurarsi che il dominio gestito da Servizi di dominio Active Directory di Azure sia disponibile nella rete virtuale.

    $vnetname="MyPreviewVnet"

Selezionare l'immagine di macchina virtuale da usare per eseguire il provisioning della macchina virtuale.

    $image=Get-AzureVMImage | where { $_.ImageFamily -eq $family } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1

Configurare la macchina virtuale: impostare il nome della macchina virtuale, le dimensioni delle istanze e l'immagine da usare.

    $vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image

Ottenere le credenziali dell'amministratore locale della macchina virtuale. Scegliere una password di amministratore locale sicura.

    $localadmincred=Get-Credential –Message "Type the name and password of the local administrator account."

Ottenere le credenziali di un account utente appartenente al gruppo 'AAD DC Administrators' per aggiungere la macchina virtuale al dominio gestito. Non specificare il nome di dominio. Nell'esempio, viene specificato 'bob' come nome utente.

    $domainadmincred=Get-Credential –Message "Now type the name (DO NOT INCLUDE THE DOMAIN) and password of an account in the AAD DC Administrators group, that has permission to add the machine to the domain."

Configurare la macchina virtuale: specificare il requisito di aggiunta al dominio e le credenziali necessarie.

    $vm1 | Add-AzureProvisioningConfig -AdminUsername $localadmincred.Username -Password $localadmincred.GetNetworkCredential().Password -WindowsDomain -Domain $domacctdomain -DomainUserName $domainadmincred.Username -DomainPassword $domainadmincred.GetNetworkCredential().Password -JoinDomain $domaindns

Impostare una subnet per la macchina virtuale.

    $vm1 | Set-AzureSubnet -SubnetNames "Subnet-1"

Facoltativo: puntare all'indirizzo IP del dominio. Se si impostano gli indirizzi IP del dominio gestito da Servizi di dominio Active Directory di Azure come server DNS della rete virtuale, non è necessario eseguire questo passaggio.

    $dns = New-AzureDns -Name 'contoso100-dc1' -IPAddress '10.0.0.4'

A questo punto, eseguire il provisioning della macchina virtuale Windows aggiunta al dominio.

    New-AzureVM –ServiceName $svcname -VMs $vm1 -VNetName $vnetname -Location "Central US" -DnsSettings $dns

<br>

## <a name="script-to-provision-a-windows-vm-and-automatically-join-it-to-an-aad-domain-services-managed-domain"></a>Script per eseguire il provisioning di una macchina virtuale Windows e aggiungerla automaticamente a un dominio gestito da Servizi di dominio Active Directory di Azure
Questo set di comandi di PowerShell crea una macchina virtuale per un server line-of-business che:

* Utilizzi l'immagine Windows Server 2012 R2 Datacenter.
* È una macchina virtuale molto piccola.
* È denominato contoso-test.
* Viene aggiunto automaticamente al dominio gestito contoso100.
* Viene aggiunto alla stessa rete virtuale del dominio gestito.

Di seguito è riportato lo script di esempio completo per creare la macchina virtuale Windows e aggiungerla automaticamente al dominio gestito da Servizi di dominio Active Directory di Azure.

    $family="Windows Server 2012 R2 Datacenter"
    $vmname="Contoso100-test"
    $vmsize="ExtraSmall"

    $domaindns="contoso100.com"
    $domacctdomain="contoso100"

    $svcname="Contoso100-test"
    $vnetname="MyPreviewVnet"

    $image=Get-AzureVMImage | where { $_.ImageFamily -eq $family } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1

    $vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image

    $localadmincred=Get-Credential –Message "Type the name and password of the local administrator account."

    $domainadmincred=Get-Credential –Message "Now type the name (not including the domain) and password of an account in the AAD DC Administrators group, that has permission to add the machine to the domain."

    $vm1 | Add-AzureProvisioningConfig -AdminUsername $localadmincred.Username -Password $localadmincred.GetNetworkCredential().Password -WindowsDomain -Domain $domacctdomain -DomainUserName $domainadmincred.Username -DomainPassword $domainadmincred.GetNetworkCredential().Password -JoinDomain $domaindns

    $vm1 | Set-AzureSubnet -SubnetNames "Subnet-1"

    $dns = New-AzureDns -Name 'contoso100-dc1' -IPAddress '10.0.0.4'

    New-AzureVM –ServiceName $svcname -VMs $vm1 -VNetName $vnetname -Location "Central US" -DnsSettings $dns

<br>

## <a name="related-content"></a>Contenuti correlati
* [Guida introduttiva di Azure AD Domain Services](active-directory-ds-getting-started.md)
* [Amministrare un dominio gestito di Servizi di dominio Azure AD](active-directory-ds-admin-guide-administer-domain.md)




<!--HONumber=Dec16_HO4-->


