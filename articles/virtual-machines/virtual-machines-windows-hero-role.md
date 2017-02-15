---
title: Installare IIS nella prima VM Windows | Microsoft Docs
description: Sperimentare l&quot;uso della prima macchina virtuale Windows tramite l&quot;installazione di IIS e l&quot;apertura della porta 80 con il portale di Azure.
keywords: 
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 6334ea45-db6b-4e08-abb5-1f98927ebc34
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 09/06/2016
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: 5919c477502767a32c535ace4ae4e9dffae4f44b
ms.openlocfilehash: 63a3ffb0e0c52643994f95dbf588f3534c8de06f


---
# <a name="experiment-with-installing-a-role-on-your-windows-vm"></a>Sperimentare l'installazione di un ruolo in una VM Windows
Dopo aver creato e reso operativa la prima macchina virtuale (VM), è possibile dedicarsi all'installazione di software e servizi. Per questa esercitazione, verrà usato Server Manager nella VM Windows Server per installare IIS. Si creerà quindi un gruppo di sicurezza di rete tramite il portale di Azure per aprire la porta 80 al traffico IIS. 

Se non è ancora stata creata la prima macchina virtuale, tornare all'esercitazione [Creare la prima macchina virtuale Windows nel portale di Azure](virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) prima di continuare con questa esercitazione.

## <a name="make-sure-the-vm-is-running"></a>Assicurarsi che la VM sia in esecuzione
1. Aprire il [portale di Azure](https://portal.azure.com).
2. Scegliere **Macchine virtuali**dal menu Hub. Selezionare la macchina virtuale dall'elenco.
3. Se lo stato è **Arrestato (deallocato)**, fare clic sul pulsante **Avvia** nel pannello **Informazioni di base** della VM. Quando lo stato indica che la macchina virtuale è **In esecuzione**, è possibile passare al passaggio successivo.

## <a name="connect-to-the-virtual-machine-and-sign-in"></a>Connettersi alla macchina virtuale ed eseguire l'accesso
1. Scegliere **Macchine virtuali**dal menu Hub. Selezionare la macchina virtuale dall'elenco.
2. Nel pannello della macchina virtuale fare clic su **Connetti**. Verrà creato e scaricato un file Remote Desktop Protocol (file con estensione rdp), che è analogo a un collegamento per la connessione alla macchina virtuale. È consigliabile salvare il file sul desktop per semplificare l'accesso. **Aprire** questo file per connettersi alla macchina virtuale.
   
    ![Screenshot del portale di Azure che illustra come connettersi alla VM](./media/virtual-machines-windows-hero-tutorial/connect.png)
3. Verrà visualizzato un avviso che indica che l'autore del file RDP è sconosciuto. Si tratta di una situazione normale. Nella finestra di Desktop remoto, fare clic su **Connetti** per continuare.
   
    ![Screenshot di un avviso relativo a un autore sconosciuto](./media/virtual-machines-windows-hero-tutorial/rdp-warn.png)
4. Nella finestra Sicurezza di Windows immettere il nome utente e la password per l'account locale creato quando è stata creata la VM. Il nome utente viene immesso come *nomevm*&#92;*nomeutente*. Fare quindi clic su **OK**.
   
    ![Screenshot relativo all'immissione del nome della VM, del nome utente e della password](./media/virtual-machines-windows-hero-tutorial/credentials.png)
5. Verrà visualizzato un avviso che indica che non è possibile verificare il certificato. Si tratta di una situazione normale. Fare clic su **Sì** per verificare l'identità della macchina virtuale e terminare la procedura di accesso.
   
   ![Screenshot che mostra un messaggio sulla verifica dell'identità della VM](./media/virtual-machines-windows-hero-tutorial/cert-warning.png)

In caso di problemi quando si cerca di eseguire la connessione, vedere [Risolvere i problemi di connessioni Desktop remoto a una macchina virtuale di Azure che esegue Windows](virtual-machines-windows-troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="install-iis-on-your-vm"></a>Installare IIS nella macchina virtuale
Dopo aver eseguito l'accesso alla macchina virtuale, verrà installato un ruolo del server che permette di fare ulteriori prove.

1. Aprire **Server Manager** se non è già aperto. Fare clic sul menu **Start** e su **Server Manager**.
2. In **Server Manager** selezionare **Server locale** nel riquadro a sinistra. 
3. Nel menu selezionare **Gestione** > **Aggiungi ruoli e funzionalità**.
4. Nella pagina **Tipo di installazione** dell'Aggiunta guidata ruoli e funzionalità scegliere **Installazione basata su ruoli o basata su funzionalità** e quindi fare clic su **Avanti**.
   
    ![Screenshot che illustra la scheda Tipo di installazione dell'Aggiunta guidata ruoli e funzionalità](./media/virtual-machines-windows-hero-tutorial/role-wizard.png)
5. Selezionare la macchina virtuale dal pool di server e fare clic su **Avanti**.
6. Nella pagina **Ruoli server** selezionare **Server Web (IIS)**.
   
    ![Screenshot che illustra la scheda Ruoli server dell'Aggiunta guidata ruoli e funzionalità](./media/virtual-machines-windows-hero-tutorial/add-iis.png)
7. Nella finestra popup sull'aggiunta di funzionalità necessarie per IIS accertarsi che sia selezionata l'opzione **Includi strumenti di gestione**, quindi fare clic su **Aggiungi funzionalità**. Quando si chiude la finestra popup, fare clic su **Avanti** nella procedura guidata.
   
    ![Screenshot che illustra la finestra popup per la conferma dell'aggiunta del ruolo IIS](./media/virtual-machines-windows-hero-tutorial/confirm-add-feature.png)
8. Nella pagina delle funzionalità fare clic su **Avanti**.
9. Nella pagina **Ruolo Server Web (IIS)** fare clic su **Avanti**. 
10. Nella pagina **Servizi ruolo** fare clic su **Avanti**. 
11. Nella pagina **Conferma** fare clic su **Installa**. 
12. Al termine dell'installazione fare clic su **Chiudi** nella procedura guidata.

## <a name="open-port-80"></a>Aprire la porta 80
Perché la macchina virtuale possa accettare il traffico in ingresso attraverso porta 80, è necessario aggiungere una regola in ingresso al gruppo di sicurezza di rete. 

1. Aprire il [portale di Azure](https://portal.azure.com).
2. In **Macchine virtuali** selezionare la VM creata.
3. Nelle impostazioni della macchina virtuale scegliere **Interfacce di rete** e quindi selezionare l'interfaccia di rete esistente.
   
    ![Screenshot che illustra l'impostazione della macchina virtuale per le interfacce di rete](./media/virtual-machines-windows-hero-tutorial/network-interface.png)
4. Nel pannello **Informazioni di base** per l'interfaccia di rete fare clic sul **Gruppo di sicurezza di rete**.
   
    ![Screenshot che illustra la sezione Informazioni di base per l'interfaccia di rete](./media/virtual-machines-windows-hero-tutorial/select-nsg.png)
5. Nel pannello **Informazioni di base** per il gruppo di sicurezza di rete dovrebbe essere già presente una regola in ingresso predefinita per **default-allow-rdp**, che consente di accedere alla macchina virtuale. Aggiungere un'altra regola in ingresso per consentire il traffico IIS. Fare clic su **Regole di sicurezza in ingresso**.
   
    ![Screenshot che illustra la sezione Informazioni di base per il gruppo di sicurezza di rete](./media/virtual-machines-windows-hero-tutorial/inbound.png)
6. In **Regole di sicurezza in ingresso** fare clic su **Aggiungi**.
   
    ![Screenshot che mostra il pulsante per l'aggiunta di una regola di sicurezza](./media/virtual-machines-windows-hero-tutorial/add-rule.png)
7. In **Regole di sicurezza in ingresso** fare clic su **Aggiungi**. Digitare **80** nell'intervallo di porte e assicurarsi che sia selezionata l'opzione **Consenti**. Al termine, fare clic su **OK**.
   
    ![Screenshot che mostra il pulsante per l'aggiunta di una regola di sicurezza](./media/virtual-machines-windows-hero-tutorial/port-80.png)

Per altre informazioni sui gruppi di sicurezza di rete e le regole in ingresso e in uscita, vedere [Consentire l'accesso esterno alla VM con il portale di Azure](virtual-machines-windows-nsg-quickstart-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="connect-to-the-default-iis-website"></a>Connettersi al sito Web IIS predefinito
1. Nel portale di Azure fare clic su **Macchine virtuali** e quindi selezionare la macchina virtuale.
2. Copiare l'**indirizzo IP pubblico** nel pannello **Informazioni di base**.
   
    ![Screenshot che illustra dove trovare l'indirizzo IP pubblico della VM](./media/virtual-machines-windows-hero-tutorial/ipaddress.png)
3. Aprire un browser e digitare l'indirizzo IP pubblico nella barra degli indirizzi come segue: http://<publicIPaddress> e premere **INVIO** per passare a tale indirizzo.
4. Il browser deve aprire la pagina Web IIS predefinita. Verrà visualizzata una schermata simile alla seguente:
   
    ![Screenshot che mostra l'aspetto della pagina IIS predefinita in un browser](./media/virtual-machines-windows-hero-tutorial/iis-default.png)

## <a name="next-steps"></a>Passaggi successivi
* È anche possibile provare a [collegare un disco dati](virtual-machines-windows-attach-disk-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) alla macchina virtuale. I dischi dati forniscono più risorse di archiviazione per la macchina virtuale.




<!--HONumber=Nov16_HO3-->


