---
title: Ambiente di test di applicazioni LOB | Microsoft Docs
description: Informazioni su come creare un&quot;applicazione line-of-business basata su Web in un ambiente cloud ibrido per professionisti IT o test di sviluppo.
services: virtual-machines-windows
documentationcenter: 
author: JoeDavies-MSFT
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 92d2d8ce-60ed-4512-95e5-a7fe3b0ca00b
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 09/30/2016
ms.author: josephd
translationtype: Human Translation
ms.sourcegitcommit: 5919c477502767a32c535ace4ae4e9dffae4f44b
ms.openlocfilehash: c90b3ca27877babb5b1f82aa810d0197417f7e70


---
# <a name="set-up-a-web-based-lob-application-in-a-hybrid-cloud-for-testing"></a>Configurazione di un'applicazione LOB basata sul Web in un cloud ibrido per l'esecuzione di test
Questo argomento descrive la creazione di un ambiente cloud ibrido simulato per testare un'applicazione line-of-business basata su Web ospitata in Microsoft Azure. Di seguito è riportata la configurazione risultante.

![](./media/virtual-machines-windows-ps-hybrid-cloud-test-env-lob/virtual-machines-windows-ps-hybrid-cloud-test-env-lob-ph3.png)

Questa configurazione è costituita da:

* Una rete locale simulata ospitata in Azure (la rete virtuale TestLab).
* Una rete virtuale cross-premise ospitata in Azure (TestVNET).
* Una connessione VPN da rete virtuale a rete virtuale.
* Un server LOB basato sul Web, SQL Server e un controller di dominio secondario nella rete virtuale TestVNET.

Questa configurazione rappresenta una base e un punto di partenza comune da cui è possibile:

* Sviluppare e testare le applicazioni LOB ospitate in Internet Information Services (IIS) con un back-end del database di SQL Server 2014 in Azure.
* Eseguire il test di questo carico di lavoro IT basato sul cloud ibrido simulato.

La configurazione di questo ambiente di test cloud ibrido prevede tre fasi principali:

1. Configurare l'ambiente cloud ibrido simulato.
2. Configurare il computer SQL server (SQL1).
3. Configurare il server LOB (LOB1).

Questo carico di lavoro richiede una sottoscrizione di Azure. Se si ha una sottoscrizione di MSDN o di Visual Studio, vedere [Credito Azure mensile per sottoscrittori di Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/).

Per un esempio di applicazione LOB di produzione ospitata in Azure, vedere il progetto di architettura **Applicazioni line-of-business** in [Progetti e diagrammi dell’architettura del Software Microsoft](http://msdn.microsoft.com/dn630664).

## <a name="phase-1-set-up-the-simulated-hybrid-cloud-environment"></a>Fase 1: Configurare l'ambiente cloud ibrido simulato
Creare l'[ambiente di test cloud ibrido simulato](virtual-machines-windows-ps-hybrid-cloud-test-env-sim.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Poiché l'ambiente di test non richiede la presenza del server APP1 nella subnet Corpnet, è possibile arrestarlo per il momento.

Questa è la configurazione corrente.

![](./media/virtual-machines-windows-ps-hybrid-cloud-test-env-lob/virtual-machines-windows-ps-hybrid-cloud-test-env-lob-ph1.png)

## <a name="phase-2-configure-the-sql-server-computer-sql1"></a>Fase 2: Configurare il computer SQL server (SQL1)
Dal portale di Azure avviare il computer DC2, se necessario.

Successivamente, creare una macchina virtuale per SQL1 con questi comandi in un prompt dei comandi di Azure PowerShell nel computer locale. Per usare questi comandi, inserire i valori delle variabili e rimuovere i caratteri <and>.

    $rgName="<your resource group name>"
    $locName="<the Azure location of your resource group>"
    $saName="<your storage account name>"

    $vnet=Get-AzureRMVirtualNetwork -Name "TestVNET" -ResourceGroupName $rgName
    $subnet=Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name "TestSubnet"
    $pip=New-AzureRMPublicIpAddress -Name SQL1-NIC -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic
    $nic=New-AzureRMNetworkInterface -Name SQL1-NIC -ResourceGroupName $rgName -Location $locName -Subnet $subnet -PublicIpAddress $pip
    $vm=New-AzureRMVMConfig -VMName SQL1 -VMSize Standard_A4
    $storageAcc=Get-AzureRMStorageAccount -ResourceGroupName $rgName -Name $saName
    $vhdURI=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/SQL1-SQLDataDisk.vhd"
    Add-AzureRMVMDataDisk -VM $vm -Name "Data" -DiskSizeInGB 100 -VhdUri $vhdURI  -CreateOption empty

    $cred=Get-Credential -Message "Type the name and password of the local administrator account for the SQL Server computer." 
    $vm=Set-AzureRMVMOperatingSystem -VM $vm -Windows -ComputerName SQL1 -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
    $vm=Set-AzureRMVMSourceImage -VM $vm -PublisherName MicrosoftSQLServer -Offer SQL2014-WS2012R2 -Skus Standard -Version "latest"
    $vm=Add-AzureRMVMNetworkInterface -VM $vm -Id $nic.Id
    $storageAcc=Get-AzureRMStorageAccount -ResourceGroupName $rgName -Name $saName
    $osDiskUri=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/SQL1-OSDisk.vhd"
    $vm=Set-AzureRMVMOSDisk -VM $vm -Name "OSDisk" -VhdUri $osDiskUri -CreateOption fromImage
    New-AzureRMVM -ResourceGroupName $rgName -Location $locName -VM $vm

Usare il portale di Azure per connettersi a SQL1 con l'account amministratore locale di SQL1.

Configurare quindi le regole di Windows Firewall per consentire il traffico di SQL Server e per i test di connettività di base. Da un prompt dei comandi di Windows PowerShell a livello di amministratore in SQL1 eseguire questi comandi.

    New-NetFirewallRule -DisplayName "SQL Server" -Direction Inbound -Protocol TCP -LocalPort 1433,1434,5022 -Action allow 
    Set-NetFirewallRule -DisplayName "File and Printer Sharing (Echo Request - ICMPv4-In)" -enabled True
    ping dc2.corp.contoso.com

Il comando ping restituirà quattro risposte dall'indirizzo IP 192.168.0.4.

Aggiungere quindi il disco dati aggiuntivo in SQL1 come nuovo volume con la lettera di unità F:.

1. Nel riquadro sinistro di Server Manager fare clic su **Servizi file e archiviazione**, quindi scegliere **Dischi**.
2. Nel riquadro del contenuto nel gruppo **Dischi** fare clic su **disco 2** (con la **Partizione** impostata su **Sconosciuto**).
3. Fare clic su **Attività**, quindi su **Nuovo volume**.
4. Nella pagina Operazioni preliminari della creazione guidata nuovo volume fare clic su **Avanti**.
5. Nella pagina Selezionare il server e il disco fare clic su **Disco 2**, quindi selezionare **Avanti**. Quando richiesto, fare clic su **OK**.
6. Nella pagina Specifica dimensioni del volume fare clic su **Avanti**.
7. Nella pagina Assegnare a una lettera di unità o cartella fare clic su **Avanti**.
8. Nella pagina Selezionare le impostazioni del file system fare clic su **Avanti**.
9. Nella pagina Conferma selezioni, fare clic su **Crea**.
10. Al termine, fare clic su **Chiudi**.

Eseguire questi comandi al prompt dei comandi di Windows PowerShell in SQL1:

    md f:\Data
    md f:\Log
    md f:\Backup

Aggiungere SQL1 al dominio CORP di Windows Server Active Directory con questi comandi al prompt di Windows PowerShell in SQL1.

    Add-Computer -DomainName corp.contoso.com
    Restart-Computer

Usare l'account CORP\User1 quando viene richiesto di specificare le credenziali dell'account di dominio per il comando **Add-Computer**.

Dopo il riavvio, usare il portale di Azure per connettersi a SQL1 *con l'account amministratore locale di SQL1*.

Configurare quindi SQL Server 2014 in modo che usi l'unità F: per i nuovi database e per le autorizzazioni dell'account utente.

1. Dalla schermata Start digitare **SQL Server Management**, quindi fare clic su **SQL Server 2014 Management Studio**.
2. In **Connetti al server** fare clic su **Connetti**.
3. Nel riquadro dell'albero Esplora oggetti, fare doppio clic su **SQL1**, quindi fare clic su **Proprietà**.
4. Nella finestra **Proprietà server** fare clic su **Impostazioni database**.
5. Individuare **Percorsi predefiniti del Database** e impostare i valori seguenti: 
   * Per **Dati**, digitare il percorso **f:\Data**.
   * Per **Log**, digitare il percorso **f:\Log**.
   * Per **Backup**, digitare il percorso **f:\Backup**.
   * Nota: Solo i nuovi database utilizzeranno questi percorsi.
6. Fare clic su **OK** per chiudere la finestra.
7. Nel riquadro dell'albero **Esplora oggetti** aprire **Sicurezza**.
8. Fare clic con il pulsante destro del mouse su **Account di accesso**, quindi scegliere **Nuovo account di accesso**.
9. In **Nome account di accesso** digitare **CORP\User1**.
10. Nella pagina **Ruoli server** fare clic su **sysadmin**, quindi su **OK**.
11. Chiudere Microsoft SQL Server Management Studio.

Questa è la configurazione corrente.

![](./media/virtual-machines-windows-ps-hybrid-cloud-test-env-lob/virtual-machines-windows-ps-hybrid-cloud-test-env-lob-ph2.png)

## <a name="phase-3-configure-the-lob-server-lob1"></a>Fase 3: Configurare il server LOB (LOB1)
Creare prima di tutto una macchina virtuale per LOB1 con questi comandi in un prompt dei comandi di Azure PowerShell nel computer locale.

    $rgName="<your resource group name>"
    $locName="<your Azure location, such as West US>"
    $saName="<your storage account name>"

    $vnet=Get-AzureRMVirtualNetwork -Name "TestVNET" -ResourceGroupName $rgName
    $subnet=Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name "TestSubnet"
    $pip=New-AzureRMPublicIpAddress -Name LOB1-NIC -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic
    $nic=New-AzureRMNetworkInterface -Name LOB1-NIC -ResourceGroupName $rgName -Location $locName -Subnet $subnet -PublicIpAddress $pip
    $vm=New-AzureRMVMConfig -VMName LOB1 -VMSize Standard_A2
    $storageAcc=Get-AzureRMStorageAccount -ResourceGroupName $rgName -Name $saName
    $cred=Get-Credential -Message "Type the name and password of the local administrator account for LOB1."
    $vm=Set-AzureRMVMOperatingSystem -VM $vm -Windows -ComputerName LOB1 -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
    $vm=Set-AzureRMVMSourceImage -VM $vm -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2012-R2-Datacenter -Version "latest"
    $vm=Add-AzureRMVMNetworkInterface -VM $vm -Id $nic.Id
    $osDiskUri=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/LOB1-TestLab-OSDisk.vhd"
    $vm=Set-AzureRMVMOSDisk -VM $vm -Name LOB1-TestVNET-OSDisk -VhdUri $osDiskUri -CreateOption fromImage
    New-AzureRMVM -ResourceGroupName $rgName -Location $locName -VM $vm

Usare quindi il portale di Azure per connettersi a LOB1 con le credenziali dell'account amministratore locale di LOB1.

Configurare quindi una regola di Windows Firewall per permettere il traffico per il test della connettività di base. Da un prompt dei comandi di Windows PowerShell a livello di amministratore in LOB1 eseguire questi comandi.

    Set-NetFirewallRule -DisplayName "File and Printer Sharing (Echo Request - ICMPv4-In)" -enabled True
    ping dc2.corp.contoso.com

Il comando ping restituirà quattro risposte dall'indirizzo IP 192.168.0.4.

Aggiungere LOB1 al dominio CORP di Active Directory con questi comandi al prompt di Windows PowerShell.

    Add-Computer -DomainName corp.contoso.com
    Restart-Computer

Usare l'account CORP\User1 quando viene richiesto di specificare le credenziali dell'account di dominio per il comando **Add-Computer**.

Dopo il riavvio, usare il portale di Azure per connettersi a LOB1 con l'account CORP\User1 e la password.

Configurare LOB1 per IIS e verificare l'accesso da CLIENT1.

1. In Server Manager fare clic su **Aggiungi ruoli e funzionalità**.
2. Nella pagina **Prima di iniziare** fare clic su **Avanti**.
3. Nella pagina **Selezione tipo di installazione** fare clic su **Avanti**.
4. Nella pagina **Selezione server di destinazione** fare clic su **Avanti**.
5. Nella pagina **Ruoli server** fare clic su **Web Server (IIS)** nell'elenco **Ruoli**.
6. Quando richiesto, fare clic su **Aggiungi funzionalità**, quindi su **Avanti**.
7. Nella pagina **Selezione funzionalità** fare clic su **Avanti**.
8. Nella pagina **Server Web (IIS)** fare clic su **Avanti**.
9. Nella pagina **Selezione servizi ruolo** selezionare o deselezionare le caselle di controllo per i servizi necessari per i test dell'applicazione LOB, quindi fare clic su **Avanti**.
10. Nella pagina **Conferma selezioni per l'installazione** fare clic su **Installa**.
11. Attendere il completamento dell'installazione dei componenti, quindi fare clic su **Chiudi**.
12. Dal portale di Azure connettersi al computer CLIENT1 con le credenziali dell'account CORP\User1 e quindi avviare Internet Explorer.
13. Nella barra degli indirizzi digitare **http://lob1/**, quindi premere INVIO. Viene visualizzata la pagina Web IIS 8 predefinita.

Questa è la configurazione corrente.

![](./media/virtual-machines-windows-ps-hybrid-cloud-test-env-lob/virtual-machines-windows-ps-hybrid-cloud-test-env-lob-ph3.png)

Questo ambiente è ora pronto per la distribuzione dell'applicazione basata su Web in LOB1 e per i test delle funzionalità da CLIENT1 nella subnet Corpnet.

## <a name="next-step"></a>Passaggio successivo
* Aggiungere una nuova macchina virtuale usando il [portale di Azure](virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).




<!--HONumber=Nov16_HO3-->


