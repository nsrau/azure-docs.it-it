---
title: 'Esercitazione: Creare un record alias DNS di Azure per supportare i nomi della radice del dominio con Gestione traffico'
description: Questa esercitazione illustra come configurare un record alias DNS di Azure per supportare l'uso dei nomi della radice del dominio con Gestione traffico.
services: dns
author: vhorne
ms.service: dns
ms.topic: tutorial
ms.date: 9/25/2018
ms.author: victorh
ms.openlocfilehash: 07f10ebbb99712b62c6807560777587033718d2b
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50091952"
---
# <a name="tutorial-configure-an-alias-record-to-support-apex-domain-names-with-traffic-manager"></a>Esercitazione: Configurare un record alias per supportare nomi della radice del dominio con Gestione traffico 

È possibile creare un record alias per la radice del nome di dominio per fare riferimento a un profilo di Gestione traffico di Azure. Un esempio è contoso.com. Invece di usare un servizio di reindirizzamento, configurare DNS di Azure in modo che faccia riferimento a un profilo di Gestione traffico direttamente dalla zona. 


In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Creare una macchina virtuale host e un'infrastruttura di rete.
> * Creare un profilo di Gestione traffico.
> * Creare un record alias.
> * Testare il record alias.


Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti
È necessario disporre di un nome di dominio che si possa ospitare in DNS di Azure per il test. È necessario disporre del controllo completo di questo dominio, inclusa la possibilità di impostare i record di nome server (NS) per il dominio.

Per istruzioni su come ospitare il dominio in DNS di Azure, vedere [Esercitazione: Ospitare un dominio in DNS di Azure](dns-delegate-domain-azure-dns.md).

Il dominio di esempio usato per questa esercitazione è contoso.com, ma in questo caso usare il nome di dominio personale.

## <a name="create-the-network-infrastructure"></a>Creare l'infrastruttura di rete
Creare innanzitutto una rete virtuale e una subnet in cui inserire i server Web.
1. Accedere al portale di Azure all'indirizzo http://portal.azure.com.
2. In alto a sinistra nel portale selezionare **Crea una risorsa**. Immettere *gruppo di risorse* nella casella di ricerca e creare un gruppo di risorse denominato **RG-DNS-Alias-TM**.
3. Selezionare **Crea una risorsa** > **Rete** > **Rete virtuale**.
4. Creare una rete virtuale denominata **VNet-Servers**. Inserirla nel gruppo di risorse **RG-DNS-Alias-TM** e assegnare il nome **SN-Web** alla subnet.

## <a name="create-two-web-server-virtual-machines"></a>Creare due macchine virtuali server Web
1. Selezionare **Crea una risorsa** > **Macchina virtuale Windows Server 2016**.
2. Immettere **Web-01** per il nome e inserire la macchina virtuale nel gruppo di risorse **RG-DNS-Alias-TM**. Immettere nome utente e password e quindi fare clic su **OK**.
3. Per **Dimensioni** selezionare uno SKU con 8 GB di RAM.
4. In **Impostazioni** selezionare la rete virtuale **VNet-Servers** e la subnet **SN-Web**.
5. Selezionare **Indirizzo IP pubblico**. In **Assegnazione** selezionare **Statico** e quindi fare clic su **OK**.
6. Per le porte in ingresso pubbliche selezionare **HTTP** > **HTTPS** > **RDP (3389)** e quindi fare clic su **OK**.
7. Nella pagina **Riepilogo** selezionare **Crea**. Il completamento di questa procedura richiede alcuni minuti.

Ripetere questa procedura per creare un'altra macchina virtuale denominata **Web-02**.

### <a name="add-a-dns-label"></a>Aggiungere un'etichetta DNS
Per usare gli indirizzi IP pubblici con Gestione traffico, è necessaria un'etichetta DNS.
1. Nel gruppo di risorse **RG-DNS-Alias-TM** selezionare l'indirizzo IP pubblico **Web-01-ip**.
2. In **Impostazioni** selezionare **Configurazione**.
3. Nella casella di testo dell'etichetta del nome DNS immettere **web01pip**.
4. Selezionare **Salva**.

Ripetere questa procedura per l'indirizzo IP pubblico **Web-02-ip** usando **web02pip** come etichetta del nome DNS.

### <a name="install-iis"></a>Installare IIS

Installare IIS sia in **Web-01** che in **Web-02**.

1. Connettersi a **Web-01** ed eseguire l'accesso.
2. Nel dashboard **Server Manager** selezionare **Aggiungi ruoli e funzionalità**.
3. Selezionare **Avanti** per tre volte. Nella pagina **Ruoli server** selezionare **Server Web (IIS)**.
4. Selezionare **Aggiungi funzionalità** e quindi **Avanti**.
5. Selezionare **Avanti** per quattro volte. Selezionare quindi **Installa**. Il completamento di questa procedura richiede alcuni minuti.
6. Al termine dell'installazione, selezionare **Chiudi**.
7. Aprire un Web browser. Passare a **localhost** per verificare che venga visualizzata la pagina Web di IIS predefinita.

Ripetere questa procedura per installare IIS in **Web-02**.


## <a name="create-a-traffic-manager-profile"></a>Creare un profilo di Gestione traffico

1. Aprire il gruppo di risorse **RG-DNS-Alias-TM** e selezionare l'indirizzo IP pubblico **Web-01-ip**. Prendere nota dell'indirizzo IP per usarlo in seguito. Ripetere questo passaggio per l'indirizzo IP pubblico **Web-02-ip**.
1. Selezionare **Crea una risorsa** > **Rete** > **Profilo di Gestione traffico**.
2. Per il nome immettere **TM-alias-test**. Inserirla nel gruppo di risorse **RG-DNS-Alias-TM**.
3. Selezionare **Crea**.
4. Al termine della distribuzione, selezionare **Vai alla risorsa**.
5. Nella pagina del profilo di Gestione traffico selezionare **Endpoint** in **Impostazioni**.
6. Selezionare **Aggiungi**.
7. Per **Tipo** selezionare **Endpoint esterno** e per **Nome** immettere **EP-Web01**.
8. Nella casella di testo **Nome di dominio completo (FQDN) o indirizzo IP** immettere l'indirizzo IP per **Web-01-ip** annotato in precedenza.
9. Selezionare la stessa **località** delle altre risorse e quindi fare clic su **OK**.

Ripetere questa procedura per aggiungere l'endpoint **Web-02**, usando l'indirizzo IP annotato in precedenza per **Web-02-ip**.

## <a name="create-an-alias-record"></a>Creare un record alias

Creare un record alias che punta al profilo di Gestione traffico.

1. Selezionare la zona DNS di Azure per aprirla.
2. Selezionare **Set di record**.
3. Lasciare la casella di testo **Nome** vuota per rappresentare la radice del nome di dominio. Un esempio è contoso.com.
4. Lasciare l'opzione **Tipo** impostata su un record **A**.
5. Selezionare la casella di controllo **Set di record alias**.
6. Fare clic su **Choose Azure service** (Scegli servizio di Azure) e selezionare il profilo di Gestione traffico **TM-alias-test**.

## <a name="test-the-alias-record"></a>Testare il record alias

1. Da un Web browser passare alla radice del nome di dominio. Un esempio è contoso.com. Viene visualizzata la pagina Web predefinita di IIS. Chiudere il Web browser.
2. Arrestare la macchina virtuale **Web-01**. Attendere qualche minuto che venga completato l'arresto.
3. Aprire un nuovo Web browser e passare di nuovo alla radice del nome di dominio.
4. Viene visualizzata di nuovo la pagina Web predefinita di IIS, perché Gestione traffico ha gestito la situazione e indirizzato il traffico a **Web-02**.

## <a name="clean-up-resources"></a>Pulire le risorse

Quando le risorse create per questa esercitazione non sono più necessarie, eliminare il gruppo di risorse **RG-DNS-Alias-TM**.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato creato un record alias per usare la radice del nome di dominio per fare riferimento a un profilo di Gestione traffico. Per informazioni su DNS di Azure e le app Web, proseguire con l'esercitazione sulle app Web.

> [!div class="nextstepaction"]
> [Creare record DNS per un'app Web in un dominio personalizzato](./dns-web-sites-custom-domain.md)
