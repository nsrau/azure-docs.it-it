<properties
   pageTitle="Come gestire gli elenchi di controllo di accesso per gli endpoint tramite PowerShell"
   description="Informazioni su come gestire gli elenchi di controllo di accesso con PowerShell"
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor="tysonn" />  
<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/15/2016"
   ms.author="jdial" />  

# Come gestire gli elenchi di controllo di accesso per gli endpoint tramite PowerShell

È possibile creare e gestire gli elenchi di controllo di accesso di rete per gli endpoint tramite Azure PowerShell o nel portale di gestione. Questo argomento illustra le procedure per le attività comuni che è possibile eseguire per tali elenchi usando PowerShell. Per l'elenco dei cmdlet di Azure PowerShell, vedere l'articolo relativo ai [cmdlet di gestione di Azure](http://go.microsoft.com/fwlink/?LinkId=317721). Per altre informazioni sugli elenchi di controllo di accesso, vedere l'articolo relativo alla [definizione di un elenco di controllo di accesso di rete](virtual-networks-acl.md). Se si intende gestire i propri elenchi di controllo di accesso tramite il portale di gestione, vedere [Come configurare gli endpoint a una macchina virtuale](../virtual-machines/virtual-machines-windows-classic-setup-endpoints.md).

## Gestire gli elenchi di controllo di accesso di rete tramite Azure PowerShell

È possibile usare i cmdlet di Azure PowerShell per creare, rimuovere e configurare (impostare) gli elenchi di controllo di accesso di rete. Di seguito sono riportati alcuni esempi di come configurare un elenco di controllo di accesso tramite PowerShell.

Per recuperare un elenco completo dei cmdlet PowerShell per gli elenchi di controllo di accesso, è possibile usare uno dei comandi seguenti:

	Get-Help *AzureACL*
	Get-Command -Noun AzureACLConfig

### Creare un elenco di controllo di accesso di rete con regole che consentano l'accesso da una subnet remota

L'esempio seguente illustra come creare un nuovo elenco di controllo di accesso contenente regole. Tale elenco viene quindi applicato all'endpoint di una macchina virtuale. Le regole dell'elenco di controllo di accesso nell'esempio seguente consentiranno l'accesso da una subnet remota. Per creare un nuovo elenco di controllo di accesso di rete con regole di autorizzazione per una subnet remota, aprire un'istanza di Azure PowerShell ISE. Copiare e incollare lo script seguente, configurandolo con valori personalizzati, e quindi eseguirlo.

1. Creare il nuovo oggetto elenco di controllo di accesso (ACL) di rete.

		$acl1 = New-AzureAclConfig

1. Impostare una regola che consenta l'accesso da una subnet remota. Nell'esempio seguente si imposta la regola *100* (che ha la priorità sulla regola 200 e superiori) per consentire alla subnet remota *10.0.0.0/8* di accedere all'endpoint della macchina virtuale. Sostituire i valori con quelli necessari per la propria configurazione. Il nome "SharePoint ACL config" deve essere sostituito dal nome descrittivo che si intende assegnare alla regola.

		Set-AzureAclConfig –AddRule –ACL $acl1 –Order 100 `
			–Action permit –RemoteSubnet "10.0.0.0/8" `
			–Description "SharePoint ACL config"

1. Per aggiungere altre regole, ripetere il cmdlet sostituendo i valori con quelli necessari per la propria configurazione. Ricordarsi di modificare il numero di regola per Order in base all'ordine in cui devono essere applicate le regole. La regola con il numero più basso ha la precedenza sulla regola con il numero più alto.

		Set-AzureAclConfig –AddRule –ACL $acl1 –Order 200 `
			–Action permit –RemoteSubnet "157.0.0.0/8" `
			–Description "web frontend ACL config"

1. È quindi possibile creare un nuovo endpoint (Add) o impostare l'elenco di controllo di accesso per un endpoint esistente (Set). In questo esempio si aggiungerà un nuovo endpoint di macchina virtuale denominato "web" e si aggiornerà l'endpoint con le impostazioni dell'elenco di controllo di accesso.

		Get-AzureVM –ServiceName $serviceName –Name $vmName `
		| Add-AzureEndpoint –Name "web" –Protocol tcp –Localport 80 - PublicPort 80 –ACL $acl1 `
		| Update-AzureVM

1. Si combineranno quindi i cmdlet e si eseguirà lo script. Per questo esempio i cmdlet combinati saranno simili ai seguenti:

		$acl1 = New-AzureAclConfig
		Set-AzureAclConfig –AddRule –ACL $acl1 –Order 100 `
			–Action permit –RemoteSubnet "10.0.0.0/8" `
			–Description "Sharepoint ACL config"
		Set-AzureAclConfig –AddRule –ACL $acl1 –Order 200 `
			–Action permit –RemoteSubnet "157.0.0.0/8" `
			–Description "web frontend ACL config"
		Get-AzureVM –ServiceName $serviceName –Name $vmName `
		|Add-AzureEndpoint –Name "web" –Protocol tcp –Localport 80 - PublicPort 80 –ACL $acl1 `
		|Update-AzureVM

### Rimuovere una regola dell'elenco di controllo di accesso che consente l'accesso da una subnet remota

L'esempio seguente illustra come rimuovere una regola dell'elenco di controllo di accesso di rete. Per rimuovere una regola dell'elenco di controllo di accesso di rete con regole di autorizzazione per una subnet remota, aprire un'istanza di Azure PowerShell ISE. Copiare e incollare lo script seguente, configurandolo con valori personalizzati, e quindi eseguirlo.

1. È innanzitutto necessario ottenere l'oggetto elenco di controllo di accesso (ACL) di rete per l'endpoint della macchina virtuale. Quindi si rimuoverà la regola dell'elenco di controllo di accesso. In questo caso, la si rimuoverà in base all'ID regola. In questo modo, dall'elenco di controllo di accesso verrà rimosso solo l'ID regola 0. L'oggetto ACL non verrà eliminato dall'endpoint della macchina virtuale.

		Get-AzureVM –ServiceName $serviceName –Name $vmName `
		| Get-AzureAclConfig –EndpointName "web" `
		| Set-AzureAclConfig –RemoveRule –ID 0 –ACL $acl1

1. È quindi necessario applicare l'oggetto ACL di rete all'endpoint della macchina virtuale e aggiornare quest'ultima.

		Get-AzureVM –ServiceName $serviceName –Name $vmName `
		| Set-AzureEndpoint –ACL $acl1 –Name "web" `
		| Update-AzureVM

### Rimuovere un elenco di controllo di accesso di rete dall'endpoint di una macchina virtuale

In alcuni scenari potrebbe essere necessario rimuovere un oggetto ACL di rete dall'endpoint di una macchina virtuale. A tale scopo, aprire un'istanza di Azure PowerShell ISE. Copiare e incollare lo script seguente, configurandolo con valori personalizzati, e quindi eseguirlo.

		Get-AzureVM –ServiceName $serviceName –Name $vmName `
		| Remove-AzureAclConfig –EndpointName "web" `
		| Update-AzureVM

## Passaggi successivi

[Che cos'è un elenco di controllo di accesso di rete?](virtual-networks-acl.md)

<!---HONumber=AcomDC_0810_2016-->