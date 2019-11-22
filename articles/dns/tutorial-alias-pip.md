---
title: 'Esercitazione: Creare un record alias servizio DNS di Azure per fare riferimento a un indirizzo IP pubblico di Azure'
description: Questa esercitazione illustra come configurare un record alias servizio DNS di Azure per fare riferimento a un indirizzo IP pubblico di Azure.
services: dns
author: asudbring
ms.service: dns
ms.topic: tutorial
ms.date: 9/25/2018
ms.author: allensu
ms.openlocfilehash: 81dfbe5f46116d263c4a04d6178437a2c8bc1185
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/14/2019
ms.locfileid: "74072125"
---
# <a name="tutorial-configure-an-alias-record-to-refer-to-an-azure-public-ip-address"></a>Esercitazione: Configurare un record alias per fare riferimento a un indirizzo IP pubblico di Azure 

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Creare un'infrastruttura di rete.
> * Creare una macchina virtuale Web server.
> * Creare un record alias.
> * Testare il record alias.


Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti
È necessario disporre di un nome di dominio che si possa ospitare in DNS di Azure per il test. È necessario disporre del controllo completo di questo dominio, inclusa la possibilità di impostare i record di nome server (NS) per il dominio.

Per istruzioni su come ospitare il dominio in DNS di Azure, vedere [Esercitazione: Ospitare un dominio in DNS di Azure](dns-delegate-domain-azure-dns.md).

Il dominio di esempio usato per questa esercitazione è contoso.com, ma in questo caso usare il nome di dominio personale.

## <a name="create-the-network-infrastructure"></a>Creare l'infrastruttura di rete
Creare innanzitutto una rete virtuale e una subnet in cui inserire i server Web.
1. Accedere al portale di Azure all'indirizzo https://portal.azure.com.
2. In alto a sinistra nel portale selezionare **Crea una risorsa**. Immettere *gruppo di risorse* nella casella di ricerca e creare un gruppo di risorse denominato **RG-DNS-Alias-pip**.
3. Selezionare **Crea una risorsa** > **Rete** > **Rete virtuale**.
4. Creare una rete virtuale denominata **VNet-Server**. Inserirla nel gruppo di risorse **RG-DNS-Alias-pip** e assegnare il nome **SN-Web** alla subnet.

## <a name="create-a-web-server-virtual-machine"></a>Creare una macchina virtuale Web server
1. Selezionare **Crea una risorsa** > **Macchina virtuale Windows Server 2016**.
2. Immettere **Web-01** per il nome e inserire la macchina virtuale nel gruppo di risorse **RG-DNS-Alias-TM**. Immettere nome utente e password e quindi fare clic su **OK**.
3. Per **Dimensioni** selezionare uno SKU con 8 GB di RAM.
4. In **Impostazioni** selezionare la rete virtuale **VNet-Servers** e la subnet **SN-Web**. Per le porte in ingresso pubbliche selezionare **HTTP** > **HTTPS** > **RDP (3389)** e quindi fare clic su **OK**.
5. Nella pagina **Riepilogo** selezionare **Crea**.

Il completamento di questa procedura richiede alcuni minuti.

### <a name="install-iis"></a>Installare IIS

Installare IIS in **Web-01**.

1. Connettersi a **Web-01** ed eseguire l'accesso.
2. Nel dashboard **Server Manager** selezionare **Aggiungi ruoli e funzionalità**.
3. Selezionare **Avanti** per tre volte. Nella pagina **Ruoli server** selezionare **Server Web (IIS)** .
4. Selezionare **Aggiungi funzionalità** e quindi **Avanti**.
5. Selezionare **Avanti** quattro volte e quindi selezionare **Installa**. Il completamento di questa procedura richiede alcuni minuti.
6. Al termine dell'installazione, selezionare **Chiudi**.
7. Aprire un Web browser. Passare a **localhost** per verificare che venga visualizzata la pagina Web di IIS predefinita.

## <a name="create-an-alias-record"></a>Creare un record alias

Creare un record alias che punta all’indirizzo IP pubblico.

1. Selezionare la zona DNS di Azure per aprirla.
2. Selezionare **Set di record**.
3. Nella casella di testo **Nome** selezionare **web01**.
4. Lasciare l'opzione **Tipo** impostata su un record **A**.
5. Selezionare la casella di controllo **Set di record alias**.
6. Selezionare l'opzione per **scegliere il servizio di Azure** e quindi l'indirizzo IP pubblico **Web-01-ip**.

## <a name="test-the-alias-record"></a>Testare il record alias

1. Nel gruppo di risorse**RG-DNS-Alias-pip** selezionare la macchina virtuale **Web-01**. Si noti l'indirizzo IP pubblico.
1. In un Web browser passare al nome di dominio completo della macchina virtuale Web01-01. Un esempio è **web01.contoso.com**. Viene visualizzata la pagina Web predefinita di IIS.
2. Chiudere il Web browser.
3. Arrestare la macchina virtuale **Web-01** e quindi ravviarla.
4. Dopo il riavvio della macchina virtuale, notare il nuovo indirizzo IP pubblico della la macchina virtuale.
5. Aprire un nuovo browser. Passare di nuovo al nome di dominio completo della macchina virtuale Web01-01. Un esempio è **web01.contoso.com**.

Questa procedura riuscirà perché è stato usato un record alias che punta alla risorsa di indirizzo IP pubblico e non un record A standard.

## <a name="clean-up-resources"></a>Pulire le risorse

Quando le risorse create per questa esercitazione non sono più necessarie, eliminare il gruppo di risorse **RG-DNS-Alias-pip**.


## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato creato un record alias per fare riferimento a un indirizzo IP pubblico di Azure. Per informazioni su DNS di Azure e le app Web, proseguire con l'esercitazione sulle app Web.

> [!div class="nextstepaction"]
> [Creare record DNS per un'app Web in un dominio personalizzato](./dns-web-sites-custom-domain.md)
