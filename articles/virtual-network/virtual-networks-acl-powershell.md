---
title: Gestire elenchi di controllo di accesso di endpoint di Azure | PowerShell | Modello di distribuzione classica | Documentazione Microsoft
description: Informazioni su come gestire gli elenchi di controllo di accesso con PowerShell
services: virtual-network
documentationcenter: na
author: jimdial
manager: carmonm
editor: tysonn
ms.assetid: c84e40af-f351-4572-b3f0-d572d46bafe7
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/15/2016
ms.author: jdial
ms.openlocfilehash: c3476908447380ccd7e8b9c0f1c2a55ae763cc1e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="manage-endpoint-access-control-lists-using-powershell-in-the-classic-deployment-model"></a>Gestire elenchi di controllo di accesso di endpoint con PowerShell nel modello di distribuzione classica
È possibile creare e gestire gli elenchi di controllo di accesso di rete per gli endpoint tramite Azure PowerShell o nel portale di gestione. Questo argomento illustra le procedure per le attività comuni che è possibile eseguire per tali elenchi usando PowerShell. Per l'elenco dei cmdlet di Azure PowerShell, vedere l'articolo relativo ai [cmdlet di gestione di Azure](http://go.microsoft.com/fwlink/?LinkId=317721). Per altre informazioni sugli elenchi di controllo di accesso, vedere l'articolo relativo alla [definizione di un elenco di controllo di accesso di rete](virtual-networks-acl.md). Se si intende gestire gli elenchi di controllo di accesso tramite il portale di gestione, vedere [Come configurare gli endpoint in una macchina virtuale](../virtual-machines/windows/classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

## <a name="manage-network-acls-by-using-azure-powershell"></a>Gestire gli elenchi di controllo di accesso di rete tramite Azure PowerShell
È possibile usare i cmdlet di Azure PowerShell per creare, rimuovere e configurare (impostare) gli elenchi di controllo di accesso di rete. Di seguito sono riportati alcuni esempi di come configurare un elenco di controllo di accesso tramite PowerShell.

Per recuperare un elenco completo dei cmdlet PowerShell per gli elenchi di controllo di accesso, è possibile usare uno dei comandi seguenti:

    Get-Help *AzureACL*
    Get-Command -Noun AzureACLConfig

### <a name="create-a-network-acl-with-rules-that-permit-access-from-a-remote-subnet"></a>Creare un elenco di controllo di accesso di rete con regole che consentano l'accesso da una subnet remota
L'esempio seguente illustra come creare un nuovo elenco di controllo di accesso contenente regole. Tale elenco viene quindi applicato all'endpoint di una macchina virtuale. Le regole dell'elenco di controllo di accesso nell'esempio seguente consentiranno l'accesso da una subnet remota. Per creare un nuovo elenco di controllo di accesso di rete con regole di autorizzazione per una subnet remota, aprire un'istanza di Azure PowerShell ISE. Copiare e incollare lo script seguente, configurandolo con valori personalizzati, e quindi eseguirlo.

1. Creare il nuovo oggetto elenco di controllo di accesso (ACL) di rete.
   
        $acl1 = New-AzureAclConfig
2. Impostare una regola che consenta l'accesso da una subnet remota. Nell'esempio seguente si imposta la regola *100* (che ha la priorità sulla regola 200 e superiori) per consentire alla subnet remota *10.0.0.0/8* di accedere all'endpoint della macchina virtuale. Sostituire i valori con quelli necessari per la propria configurazione. Il nome "SharePoint ACL config" deve essere sostituito dal nome descrittivo che si intende assegnare alla regola.
   
        Set-AzureAclConfig –AddRule –ACL $acl1 –Order 100 `
            –Action permit –RemoteSubnet "10.0.0.0/8" `
            –Description "SharePoint ACL config"
3. Per aggiungere altre regole, ripetere il cmdlet sostituendo i valori con quelli necessari per la propria configurazione. Ricordarsi di modificare il numero di regola per Order in base all'ordine in cui devono essere applicate le regole. La regola con il numero più basso ha la precedenza sulla regola con il numero più alto.
   
        Set-AzureAclConfig –AddRule –ACL $acl1 –Order 200 `
            –Action permit –RemoteSubnet "157.0.0.0/8" `
            –Description "web frontend ACL config"
4. È quindi possibile creare un nuovo endpoint (Add) o impostare l'elenco di controllo di accesso per un endpoint esistente (Set). In questo esempio si aggiungerà un nuovo endpoint di macchina virtuale denominato "web" e si aggiornerà l'endpoint con le impostazioni dell'elenco di controllo di accesso.
   
        Get-AzureVM –ServiceName $serviceName –Name $vmName `
        | Add-AzureEndpoint –Name "web" –Protocol tcp –Localport 80 - PublicPort 80 –ACL $acl1 `
        | Update-AzureVM
5. Si combineranno quindi i cmdlet e si eseguirà lo script. Per questo esempio i cmdlet combinati saranno simili ai seguenti:
   
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

### <a name="remove-a-network-acl-rule-that-permits-access-from-a-remote-subnet"></a>Rimuovere una regola dell'elenco di controllo di accesso che consente l'accesso da una subnet remota
L'esempio seguente illustra come rimuovere una regola dell'elenco di controllo di accesso di rete.  Per rimuovere una regola dell'elenco di controllo di accesso di rete con regole di autorizzazione per una subnet remota, aprire un'istanza di Azure PowerShell ISE. Copiare e incollare lo script seguente, configurandolo con valori personalizzati, e quindi eseguirlo.

1. È innanzitutto necessario ottenere l'oggetto elenco di controllo di accesso (ACL) di rete per l'endpoint della macchina virtuale. Quindi si rimuoverà la regola dell'elenco di controllo di accesso. In questo caso, la si rimuoverà in base all'ID regola. In questo modo, dall'elenco di controllo di accesso verrà rimosso solo l'ID regola 0. L'oggetto ACL non verrà eliminato dall'endpoint della macchina virtuale.
   
        Get-AzureVM –ServiceName $serviceName –Name $vmName `
        | Get-AzureAclConfig –EndpointName "web" `
        | Set-AzureAclConfig –RemoveRule –ID 0 –ACL $acl1
2. È quindi necessario applicare l'oggetto ACL di rete all'endpoint della macchina virtuale e aggiornare quest'ultima.
   
        Get-AzureVM –ServiceName $serviceName –Name $vmName `
        | Set-AzureEndpoint –ACL $acl1 –Name "web" `
        | Update-AzureVM

### <a name="remove-a-network-acl-from-a-virtual-machine-endpoint"></a>Rimuovere un elenco di controllo di accesso di rete dall'endpoint di una macchina virtuale
In alcuni scenari potrebbe essere necessario rimuovere un oggetto ACL di rete dall'endpoint di una macchina virtuale. A tale scopo, aprire un'istanza di Azure PowerShell ISE. Copiare e incollare lo script seguente, configurandolo con valori personalizzati, e quindi eseguirlo.

        Get-AzureVM –ServiceName $serviceName –Name $vmName `
        | Remove-AzureAclConfig –EndpointName "web" `
        | Update-AzureVM

## <a name="next-steps"></a>Passaggi successivi
[Che cos'è un elenco di controllo di accesso di rete?](virtual-networks-acl.md)

