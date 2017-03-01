---
title: "Non è possibile connettersi tramite RDP a una macchina virtuale Windows in Azure | Documentazione Microsoft"
description: Risoluzione dei problemi di connessione con Desktop remoto di una macchina virtuale Windows in Azure
keywords: Errore di desktop remoto, errore di connessione al desktop remoto, impossibile connettersi alla macchina virtuale, risoluzione dei problemi di desktop remoto
services: virtual-machines-windows
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
tags: top-support-issue,azure-service-management,azure-resource-manager
ms.assetid: 0d740f8e-98b8-4e55-bb02-520f604f5b18
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: support-article
ms.date: 02/09/2017
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: 9ca20f34d812e15a7f1e6f5dd032ec05c464046d
ms.openlocfilehash: 53920a551e24f29274133315b463c6c69605da10
ms.lasthandoff: 02/27/2017


---
# <a name="troubleshoot-remote-desktop-connections-to-an-azure-virtual-machine"></a>Risolvere i problemi di connessione con Desktop remoto di una macchina virtuale di Azure
La mancata connessione Remote Desktop Protocol (RDP) alla macchina virtuale di Azure basata su Windows può avere varie cause, impedendo l'accesso alla VM. Il problema può dipendere dal servizio di desktop remoto nella VM, dalla connessione di rete o dal client di desktop remoto nel computer host. Questo articolo illustra alcuni dei metodi più comuni per risolvere i problemi di connessione RDP. 

Per ricevere assistenza in qualsiasi punto di questo articolo, contattare gli esperti di Azure nei [forum MSDN e Stack Overflow relativi ad Azure](https://azure.microsoft.com/support/forums/). In alternativa, è possibile archiviare un evento imprevisto di supporto tecnico di Azure. Accedere al [sito del supporto di Azure](https://azure.microsoft.com/support/options/) e selezionare **Ottenere supporto**.

<a id="quickfixrdp"></a>

## <a name="quick-troubleshooting-steps"></a>Passaggi rapidi per la risoluzione dei problemi
Dopo ogni passaggio della procedura di risoluzione dei problemi, tentare la riconnessione alla VM:

1. Ripristinare la configurazione del desktop remoto.
2. Controllare le regole degli endpoint del gruppo di sicurezza di rete/Servizi cloud.
3. Esaminare i log della console della VM.
4. Controllare l'integrità delle risorse della VM.
5. Reimpostare la password della VM.
6. Riavviare la VM.
7. Ripetere la distribuzione della VM.

Continuare la lettura per procedure dettagliate e altre informazioni. Verificare che le apparecchiature di rete locali quali router e firewall non blocchino la porta TCP in uscita 3389, come indicato negli [scenari dettagliati di risoluzione dei problemi RDP](virtual-machines-windows-detailed-troubleshoot-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

> [!TIP]
> Se il pulsante **Connetti** della VM è disattivato nel portale e non si è connessi ad Azure tramite una connessione [Express Route](../expressroute/expressroute-introduction.md) o [VPN da sito a sito](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md), per poter usare il protocollo RDP è necessario creare un indirizzo IP pubblico e assegnarlo alla VM. Altre informazioni sono disponibili in [Indirizzi IP pubblici in Azure](../virtual-network/virtual-network-ip-addresses-overview-arm.md).
> 
> 

## <a name="ways-to-troubleshoot-rdp-issues"></a>Risoluzione dei problemi di connessione RDP
È possibile risolvere i problemi delle macchine virtuali create usando il modello di distribuzione di Resource Manager con uno dei metodi seguenti:

* [Portale di Azure](#using-the-azure-portal), indicato per ripristinare rapidamente la configurazione della connessione RDP o le credenziali dell'utente nel caso in cui non siano stati installati gli strumenti di Azure.
* [Azure PowerShell](#using-azure-powershell): indicato se si ha familiarità con i prompt di PowerShell e si intende ripristinare rapidamente la configurazione della connessione RDP o le credenziali utente usando i cmdlet di Azure PowerShell.

Altri passaggi per la risoluzione dei problemi delle macchine virtuali create usando [modello di distribuzione classico](#troubleshoot-vms-created-using-the-classic-deployment-model).

<a id="fix-common-remote-desktop-errors"></a>

## <a name="troubleshoot-using-the-azure-portal"></a>Risolvere i problemi usando il portale di Azure
Dopo ogni passaggio della procedura di risoluzione dei problemi, ritentare di connettersi alla VM. Se ancora non è possibile connettersi, procedere al passaggio successivo.

1. **Ripristinare la connessione RDP**. Questa procedura di risoluzione dei problemi consente di ripristinare la configurazione della connessione RDP quando, ad esempio, le connessioni remote sono disabilitate o le regole di Windows Firewall la bloccano.
   
    Selezionare la macchina virtuale nel portale di Azure. Scorrere verso il basso nel riquadro delle impostazioni fino alla sezione **Supporto e risoluzione dei problemi**, nella parte inferiore dell'elenco. Fare clic sul pulsante **Reimposta password**. Impostare la **Modalità** su **Reset configuration only **(Reimposta solo configurazione) quindi fare clic sul pulsante **Aggiorna**:
   
    ![Ripristinare la configurazione della connessione RDP nel portale di Azure](./media/virtual-machines-windows-troubleshoot-rdp-connection/reset-rdp.png)
2. **Verificare le regole del gruppo di sicurezza di rete**. Questo passaggio verifica che le regole del gruppo di sicurezza di rete consentono il traffico RDP. La porta RDP predefinita è la porta TCP 3389. Quando si crea la macchina virtuale, non è possibile creare automaticamente una regola che consenta il traffico RDP.
   
    Selezionare la macchina virtuale nel portale di Azure. Nel riquadro delle impostazioni fare clic su **Interfacce di rete**.
   
    ![Visualizzare le interfacce di rete di una macchina virtuale nel portale di Azure](./media/virtual-machines-windows-troubleshoot-rdp-connection/select-network-interfaces.png)
   
    Selezionare l'interfaccia di rete dall'elenco. In genere ne è presente una soltanto:
   
    ![Selezionare l'interfaccia di rete nel portale di Azure](./media/virtual-machines-windows-troubleshoot-rdp-connection/select-interface.png)
   
    Selezionare **Gruppo di sicurezza di rete** per visualizzare il gruppo di sicurezza di rete associato all'interfaccia di rete:
   
    ![Selezionare il gruppo di sicurezza di rete nel portale di Azure](./media/virtual-machines-windows-troubleshoot-rdp-connection/select-nsg.png)
   
    Assicurarsi che sia disponibile una regola in ingresso che consente il traffico RDP sulla porta TCP 3389. L'esempio seguente illustra una regola di sicurezza valida che consente il traffico RDP. È visualizzata la corretta configurazione di `Service` e `Action`:
   
    ![Verificare la regola NSG della connessione RDP nel portale di Azure](./media/virtual-machines-windows-troubleshoot-rdp-connection/verify-nsg-rules.png)
   
    Se non è presente una regola che consente il traffico RDP, [creare una regola del gruppo di sicurezza di rete](virtual-machines-windows-nsg-quickstart-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Consentire il traffico sulla porta TCP 3389.
3. **Rivedere la diagnostica di avvio della macchina virtuale**. Questa procedura di risoluzione dei problemi esamina i log della console della macchina virtuale per stabilire se la macchina virtuale segnala un problema. Poiché la diagnostica di avvio non è abilitata su tutte le macchine virtuali, la procedura può essere facoltativa.
   
    Le procedure di risoluzione dei problemi specifici non rientrano nell'ambito di questo articolo, ma potrebbero segnalare un problema di più ampia portata che ha effetto sulla connessione RDP. Per altre informazioni sull'esame dei log della console e delle schermate della macchina virtuale, vedere l'articolo sulla [diagnostica di avvio per le macchine virtuali](https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/).
4. **Controllare l'integrità delle risorse della VM**. Questa procedura di risoluzione dei problemi verifica l'assenza di problemi noti della piattaforma Azure che potrebbero avere effetto sulla connessione alla macchina virtuale.
   
    Selezionare la macchina virtuale nel portale di Azure. Scorrere verso il basso nel riquadro delle impostazioni fino alla sezione **Supporto e risoluzione dei problemi**, nella parte inferiore dell'elenco. Fare clic sul pulsante **Integrità delle risorse**. Una macchina virtuale integra viene indicata come **Disponibile**:
   
    ![Controllare l'integrità della risorsa VM nel portale di Azure](./media/virtual-machines-windows-troubleshoot-rdp-connection/check-resource-health.png)
5. **Reimpostare le credenziali utente**. Questa procedura di risoluzione dei problemi consente di reimpostare la password di un account amministratore locale se non si è sicuri o si sono dimenticate le credenziali.
   
    Selezionare la macchina virtuale nel portale di Azure. Scorrere verso il basso nel riquadro delle impostazioni fino alla sezione **Supporto e risoluzione dei problemi**, nella parte inferiore dell'elenco. Fare clic sul pulsante **Reimposta password**. Verificare che la **Modalità** sia impostata su **Reimpostazione password**, quindi immettere il nome utente e una nuova password. Infine fare clic sul pulsante **Aggiorna**:
   
    ![Reimpostare le credenziali utente nel portale di Azure](./media/virtual-machines-windows-troubleshoot-rdp-connection/reset-password.png)
6. **Riavviare la VM**. Questa procedura di risoluzione dei problemi consente di correggere eventuali problemi sottostanti riscontrati dalla macchina virtuale.
   
    Selezionare la macchina virtuale nel portale di Azure e fare clic sulla scheda **Panoramica**. Fare clic sul pulsante **Riavvia**:
   
    ![Riavviare la macchina virtuale nel portale di Azure](./media/virtual-machines-windows-troubleshoot-rdp-connection/restart-vm.png)
7. **Ripetere la distribuzione della VM**. Questa procedura di risoluzione dei problemi ripete la distribuzione della macchina virtuale in un altro host all'interno di Azure per correggere eventuali problemi relativi alla rete o alla piattaforma sottostante.
   
    Selezionare la macchina virtuale nel portale di Azure. Scorrere verso il basso nel riquadro delle impostazioni fino alla sezione **Supporto e risoluzione dei problemi**, nella parte inferiore dell'elenco. Fare clic sul pulsante **Ridistribuisci** e quindi su **Ridistribuisci**:
   
    ![Ripetere la distribuzione della macchina virtuale nel portale di Azure](./media/virtual-machines-windows-troubleshoot-rdp-connection/redeploy-vm.png)
   
    Al termine di questa operazione i dati temporanei del disco vanno persi e gli indirizzi IP dinamici associati alla macchina virtuale vengono aggiornati.

Se continuano a verificarsi errori RDP, è possibile [aprire una richiesta di supporto](https://azure.microsoft.com/support/options/) o leggere i [passaggi e concetti relativi alla risoluzione dettagliata dei problemi RDP](virtual-machines-windows-detailed-troubleshoot-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="troubleshoot-using-azure-powershell"></a>Risoluzione dei problemi con Azure PowerShell
Se non è già stato fatto, [installare e configurare la versione più recente di Azure PowerShell](/powershell/azureps-cmdlets-docs).

Negli esempi seguenti vengono usate alcune variabili, ad esempio `myResourceGroup`, `myVM` e `myVMAccessExtension`. Sostituire i nomi e i percorsi delle variabili con i valori personalizzati.

> [!NOTE]
> Reimpostare le credenziali utente e la configurazione della connessione RDP usando il cmdlet [Set-AzureRmVMAccessExtension](https://msdn.microsoft.com/library/mt619447.aspx) di PowerShell. Negli esempi seguenti `myVMAccessExtension` è un nome specificato come parte del processo. Se in precedenza è stato usato il VMAccessAgent, è possibile ottenere il nome dell'estensione esistente usando `Get-AzureRmVM -ResourceGroupName "myResourceGroup" -Name "myVM"` per controllare le proprietà della VM. Esaminare quindi la sezione dell'output relativa alle estensioni per visualizzare il nome.
> 
> 

Dopo ogni passaggio della procedura di risoluzione dei problemi, ritentare di connettersi alla VM. Se ancora non è possibile connettersi, procedere al passaggio successivo.

1. **Ripristinare la connessione RDP**. Questa procedura di risoluzione dei problemi consente di ripristinare la configurazione della connessione RDP quando, ad esempio, le connessioni remote sono disabilitate o le regole di Windows Firewall la bloccano.
   
    L'esempio seguente ripristina la connessione RDP in una macchina virtuale denominata `myVM` nella località `WestUS` e nel gruppo di risorse `myResourceGroup`:
   
    ```powershell
    Set-AzureRmVMAccessExtension -ResourceGroupName "myResourceGroup" `
        -VMName "myVM" -Location Westus -Name "myVMAccessExtension"
    ```
2. **Verificare le regole del gruppo di sicurezza di rete**. Questo passaggio verifica che le regole del gruppo di sicurezza di rete consentono il traffico RDP. La porta RDP predefinita è la porta TCP 3389. Quando si crea la macchina virtuale, non è possibile creare automaticamente una regola che consenta il traffico RDP.
   
    Innanzitutto assegnare tutti i dati di configurazione del gruppo di sicurezza di rete alla variabile `$rules`. L'esempio seguente ottiene informazioni sul gruppo di sicurezza di rete denominato `myNetworkSecurityGroup` nel gruppo di risorse denominato `myResourceGroup`:
   
    ```powershell
    $rules = Get-AzureRmNetworkSecurityGroup -ResourceGroupName "myResourceGroup" `
        -Name "myNetworkSecurityGroup"
    ```
   
    Visualizzare quindi le regole configurate per questo gruppo di sicurezza di rete. Assicurarsi che esista una regola che consente le connessioni in ingresso sulla porta TCP 3389, come indicato di seguito:
   
    ```powershell
    $rules.SecurityRules
    ```
   
    L'esempio seguente illustra una regola di sicurezza valida che consente il traffico RDP. È visualizzata la corretta configurazione di `Protocol`, `DestinationPortRange`, `Direction` e `Access`:
   
    ```powershell
    Name                     : default-allow-rdp
    Id                       : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup/securityRules/default-allow-rdp
    Etag                     : 
    ProvisioningState        : Succeeded
    Description              : 
    Protocol                 : TCP
    SourcePortRange          : *
    DestinationPortRange     : 3389
    SourceAddressPrefix      : *
    DestinationAddressPrefix : *
    Access                   : Allow
    Priority                 : 1000
    Direction                : Inbound
    ```
   
    Se non è presente una regola che consente il traffico RDP, [creare una regola del gruppo di sicurezza di rete](virtual-machines-windows-nsg-quickstart-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Consentire il traffico sulla porta TCP 3389.
3. **Reimpostare le credenziali utente**. Questa procedura di risoluzione dei problemi consente di reimpostare la password dell'account amministratore locale specificata se non si è sicuri o si sono dimenticate le credenziali.
   
    Innanzitutto specificare il nome utente e una nuova password assegnando le credenziali alla variabile `$cred`, come indicato di seguito:
   
    ```powershell
    $cred=Get-Credential
    ```
   
    Aggiornare quindi le credenziali nella macchina virtuale. L'esempio seguente aggiorna le credenziali di una macchina virtuale denominata `myVM` nella località `WestUS` e nel gruppo di risorse `myResourceGroup`:
   
    ```powershell
    Set-AzureRmVMAccessExtension -ResourceGroupName "myResourceGroup" `
        -VMName "myVM" -Location WestUS -Name "myVMAccessExtension" `
        -UserName $cred.GetNetworkCredential().Username `
        -Password $cred.GetNetworkCredential().Password
    ```
4. **Riavviare la VM**. Questa procedura di risoluzione dei problemi consente di correggere eventuali problemi sottostanti riscontrati dalla macchina virtuale.
   
    L'esempio seguente riavvia la macchina virtuale denominata `myVM` nel gruppo di risorse `myResourceGroup`:
   
    ```powershell
    Restart-AzureRmVM -ResourceGroup "myResourceGroup" -Name "myVM"
    ```
5. **Ripetere la distribuzione della VM**. Questa procedura di risoluzione dei problemi ripete la distribuzione della macchina virtuale in un altro host all'interno di Azure per correggere eventuali problemi relativi alla rete o alla piattaforma sottostante.
   
    L'esempio seguente ridistribuisce la macchina virtuale denominata `myVM` nella località `WestUS` e nel gruppo di risorse `myResourceGroup`:
   
    ```powershell
    Set-AzureRmVM -Redeploy -ResourceGroupName "myResourceGroup" -Name "myVM"
    ```

Se continuano a verificarsi errori RDP, è possibile [aprire una richiesta di supporto](https://azure.microsoft.com/support/options/) o leggere i [passaggi e concetti relativi alla risoluzione dettagliata dei problemi RDP](virtual-machines-windows-detailed-troubleshoot-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="troubleshoot-vms-created-using-the-classic-deployment-model"></a>Risolvere i problemi delle VM create con il modello di distribuzione classica
Dopo ogni passaggio della procedura di risoluzione dei problemi, tentare la riconnessione alla VM.

1. **Ripristinare la connessione RDP**. Questa procedura di risoluzione dei problemi consente di ripristinare la configurazione della connessione RDP quando, ad esempio, le connessioni remote sono disabilitate o le regole di Windows Firewall la bloccano.
   
    Selezionare la macchina virtuale nel portale di Azure. Fare clic sul pulsante ** Altro** quindi su **Reimposta accesso remoto**:
   
    ![Ripristinare la configurazione della connessione RDP nel portale di Azure](./media/virtual-machines-windows-troubleshoot-rdp-connection/classic-reset-rdp.png)
2. **Controllare gli endpoint di Servizi cloud**. Questa procedura di risoluzione dei problemi verifica che gli endpoint di Servizi cloud consentano il traffico RDP. La porta RDP predefinita è la porta TCP 3389. Quando si crea la macchina virtuale, non è possibile creare automaticamente una regola che consenta il traffico RDP.
   
   Selezionare la macchina virtuale nel portale di Azure. Fare clic sul pulsante **Endpoint** per visualizzare gli endpoint configurati per la macchina virtuale. Verificare l'esistenza di endpoint che consentono il traffico RDP sulla porta TCP 3389.
   
   L'esempio seguente mostra endpoint validi che consentono il traffico RDP:
   
   ![Verificare gli endpoint di Servizi cloud nel portale di Azure](./media/virtual-machines-windows-troubleshoot-rdp-connection/classic-verify-cloud-services-endpoints.png)
   
   Se non è presente un endpoint che consente il traffico RDP, [creare un endpoint di Servizi cloud](virtual-machines-windows-classic-setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json). Consentire il traffico TCP sulla porta privata 3389.
3. **Rivedere la diagnostica di avvio della macchina virtuale**. Questa procedura di risoluzione dei problemi esamina i log della console della macchina virtuale per stabilire se la macchina virtuale segnala un problema. Poiché la diagnostica di avvio non è abilitata su tutte le macchine virtuali, la procedura può essere facoltativa.
   
    Le procedure di risoluzione dei problemi specifici non rientrano nell'ambito di questo articolo, ma potrebbero segnalare un problema di più ampia portata che ha effetto sulla connessione RDP. Per altre informazioni sull'esame dei log della console e delle schermate della macchina virtuale, vedere l'articolo sulla [diagnostica di avvio per le macchine virtuali](https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/).
4. **Controllare l'integrità delle risorse della VM**. Questa procedura di risoluzione dei problemi verifica l'assenza di problemi noti della piattaforma Azure che potrebbero avere effetto sulla connessione alla macchina virtuale.
   
    Selezionare la macchina virtuale nel portale di Azure. Scorrere verso il basso nel riquadro delle impostazioni fino alla sezione **Supporto e risoluzione dei problemi**, nella parte inferiore dell'elenco. Fare clic sul pulsante **Integrità delle risorse**. Una macchina virtuale integra viene indicata come **Disponibile**:
   
    ![Controllare l'integrità della risorsa VM nel portale di Azure](./media/virtual-machines-windows-troubleshoot-rdp-connection/classic-check-resource-health.png)
5. **Reimpostare le credenziali utente**. Questa procedura consente di reimpostare la password dell'account amministratore locale specificata se non si è sicuri o si sono dimenticate le credenziali.
   
    Selezionare la macchina virtuale nel portale di Azure. Scorrere verso il basso nel riquadro delle impostazioni fino alla sezione **Supporto e risoluzione dei problemi**, nella parte inferiore dell'elenco. Fare clic sul pulsante **Reimposta password**. Immettere il nome utente e una nuova password. Infine fare clic sul pulsante **Salva**:
   
    ![Reimpostare le credenziali utente nel portale di Azure](./media/virtual-machines-windows-troubleshoot-rdp-connection/classic-reset-password.png)
6. **Riavviare la VM**. Questa procedura di risoluzione dei problemi consente di correggere eventuali problemi sottostanti riscontrati dalla macchina virtuale.
   
    Selezionare la macchina virtuale nel portale di Azure e fare clic sulla scheda **Panoramica**. Fare clic sul pulsante **Riavvia**:
   
    ![Riavviare la macchina virtuale nel portale di Azure](./media/virtual-machines-windows-troubleshoot-rdp-connection/classic-restart-vm.png)

Se continuano a verificarsi errori RDP, è possibile [aprire una richiesta di supporto](https://azure.microsoft.com/support/options/) o leggere i [passaggi e concetti relativi alla risoluzione dettagliata dei problemi RDP](virtual-machines-windows-detailed-troubleshoot-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="troubleshoot-specific-rdp-errors"></a>Risolvere errori specifici della connessione RDP
Quando si tenta di connettersi alla VM tramite RDP, potrebbe essere visualizzato un messaggio di errore specifico. Di seguito sono riportati i messaggi di errore più comuni:

* [La sessione remota è stata disconnessa perché non sono disponibili server licenze di Desktop remoto per il rilascio della licenza.](virtual-machines-windows-troubleshoot-specific-rdp-errors.md#rdplicense).
* [Desktop remoto: impossibile rilevare il "nome" del computer](virtual-machines-windows-troubleshoot-specific-rdp-errors.md#rdpname).
* [Si è verificato un errore di autenticazione. Impossibile contattare l'autorità di sicurezza locale](virtual-machines-windows-troubleshoot-specific-rdp-errors.md#rdpauth).
* [Errore di sicurezza di Windows: Le credenziali specificate non funzionano](virtual-machines-windows-troubleshoot-specific-rdp-errors.md#wincred).
* [Il computer non è in grado di connettersi al computer remoto](virtual-machines-windows-troubleshoot-specific-rdp-errors.md#rdpconnect).

## <a name="additional-resources"></a>Risorse aggiuntive
Se non si è verificato alcuno di questi errori e non è ancora possibile connettersi alla VM tramite Desktop remoto, leggere [la guida dettagliata alla risoluzione dei problemi per Desktop remoto](virtual-machines-windows-detailed-troubleshoot-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Per la risoluzione dei problemi di accesso alle applicazioni in esecuzione in una VM, vedere [Troubleshoot access to an application running on an Azure VM](virtual-machines-linux-troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) (Risolvere i problemi di accesso a un'applicazione in esecuzione in una macchina virtuale di Azure).
* Se si verificano problemi relativi all'uso di SSH (Secure Shell) per la connessione a una VM Linux in Azure, vedere [Risoluzione dei problemi di connessione SSH a una macchina virtuale Linux di Azure](virtual-machines-linux-troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).


