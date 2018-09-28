---
title: 'Esercitazione: Creare un record alias DNS di Azure per supportare i nomi della radice del dominio con Gestione traffico'
description: Questa esercitazione illustra come configurare un record alias DNS di Azure per supportare l'uso dei nomi della radice del dominio con Gestione traffico.
services: dns
author: vhorne
ms.service: dns
ms.topic: tutorial
ms.date: 9/25/2018
ms.author: victorh
ms.openlocfilehash: 4a5d8968861f6f2938e605d2f7106529ca401df4
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/24/2018
ms.locfileid: "46967439"
---
# <a name="tutorial-configure-an-alias-record-to-support-apex-domain-names-with-traffic-manager"></a>Esercitazione: Configurare un record alias per supportare nomi della radice del dominio con Gestione traffico 

È possibile creare un record alias per la radice del nome di dominio (ad esempio, contoso.com) per fare riferimento a un profilo di Gestione traffico. Quindi, invece di usare un servizio di reindirizzamento a questo scopo, è possibile configurare DNS di Azure per fare riferimento a un profilo di Gestione traffico direttamente dalla zona. 


In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Creare una macchina virtuale host e un'infrastruttura di rete
> * Creare un profilo di Gestione traffico
> * Creare un record alias
> * Testare il record alias


Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti
È necessario avere un nome di dominio disponibile che è possibile ospitare in DNS di Azure per il test. È necessario disporre del controllo completo del dominio, inclusa la possibilità di impostare i record di nome server (NS) per il dominio.

Per istruzioni su come ospitare il dominio in DNS di Azure, vedere [Esercitazione: Ospitare un dominio in DNS di Azure](dns-delegate-domain-azure-dns.md).

Il dominio di esempio usato per questa esercitazione è contoso.com, ma è consigliabile usare un nome di dominio personale.

## <a name="create-the-network-infrastructure"></a>Creare l'infrastruttura di rete
Innanzitutto, creare una rete virtuale e una subnet in cui posizionare i server Web.
1. Accedere al portale di Azure all'indirizzo http://portal.azure.com.
2. Nella parte superiore sinistra nel portale fare clic su **Crea una risorsa**, digitare *gruppo di risorse* nella casella di ricerca e creare un gruppo di risorse denominato **RG-DNS-Alias-TM**.
3. Fare clic su **Crea una risorsa**, **Rete** e quindi fare clic su **Rete virtuale**.
4. Creare una rete virtuale denominata **VNet-Servers**, posizionarla nel gruppo di risorse **RG-DNS-Alias-TM** e assegnare il nome **SN-Web** alla subnet.

## <a name="create-two-web-server-virtual-machines"></a>Creare due macchine virtuali server Web
1. Fare clic su **Crea una risorsa** e su **Macchina virtuale Windows Server 2016**.
2. Digitare **Web-01** per il nome e inserire la macchina virtuale nel gruppo di risorse **RG-DNS-Alias-TM**. Digitare nome utente e password, quindi fare clic su **OK**.
3. In **Dimensioni** scegliere una SKU con 8 GB di RAM.
4. In **Impostazioni** selezionare la rete virtuale **VNet-Servers** e la subnet **SN-Web**.
5. Fare clic su **indirizzo IP pubblico**, in **Assegnazione** fare clic su **Statico** e quindi fare clic su **OK**.
6. Per le porte in ingresso pubbliche, selezionare **HTTP**, **HTTPS**, e **RDP (3389)**, quindi fare clic su **OK**.
7. Nella pagina Riepilogo fare clic su **Crea**.

   Per il completamento dell'operazione sono richiesti alcuni minuti.
6. Ripetere questa procedura per creare un'altra macchina virtuale denominata **Web-02**.

### <a name="add-a-dns-label"></a>Aggiungere un'etichetta DNS
Per utilizzare gli indirizzi IP pubblici con Gestione traffico, è necessaria un'etichetta DNS.
1. Nel gruppo di risorse **RG-DNS-Alias-TM** fare clic sull'indirizzo IP pubblico **Web-01-ip**.
2. In **Impostazioni** fare clic su **Configurazione**.
3. Nella casella di testo dell'etichetta del nome DNS digitare **web01pip**.
4. Fare clic su **Save**.

Ripetere questa procedura per l'indirizzo IP pubblico **Web-02-ip** usando **web02pip** come etichetta del nome DNS.

### <a name="install-iis"></a>Installare IIS

Installare IIS sia in **Web-01** che in **Web-02**.

1. Connettersi a **Web-01** ed eseguire l'accesso.
2. Dal dashboard di Server Manager fare clic su **Aggiungi ruoli e funzionalità**.
3. Fare clic su **Avanti** tre volte e nella pagina **Ruoli server** selezionare **Server Web (IIS)**.
4. Fare clic su **Aggiungi funzionalità** e quindi su **Avanti**.
5. Fare clic su **Avanti** quattro volte e quindi fare clic su **Installa**.

   Questa operazione richiederà qualche minuto.
6. Al termine dell'installazione, fare clic su **Chiudi**.
7. Aprire un Web browser e passare a **localhost** per verificare che venga visualizzata la pagina Web di IIS predefinita.

Ripetere questo processo per installare IIS in **Web-02**.


## <a name="create-a-traffic-manager-profile"></a>Creare un profilo di Gestione traffico

Due 

1. Aprire il gruppo di risorse **RG-DNS-Alias-TM** e fare clic sull'indirizzo IP pubblico **Web-01-ip**. Prendere nota dell'indirizzo IP per usarlo in seguito. Ripetere per l'indirizzo IP pubblico **Web-02-ip**.
1. Fare clic su **Crea una risorsa**, **Rete** e quindi fare clic su **Traffic Manager profile** (Profilo di Gestione traffico).
2. Digitare **TM-alias-test** per il nome e inserirlo nel gruppo di risorse **RG-DNS-Alias-TM**.
3. Fare clic su **Create**(Crea).
4. Al termine della distribuzione, fare clic su **Vai alla risorsa**.
5. Nella pagina del profilo di Gestione traffico fare clic su **Endpoint** in **Impostazioni**.
6. Fare clic su **Aggiungi**.
7. Per **Tipo** selezionare **Endpoint esterno** e per **Nome** digitare **EP-Web01**.
8. Nella casella di testo **Nome di dominio completo (FQDN) o indirizzo IP** digitare l'indirizzo IP per **Web-01-ip** annotato in precedenza.
9. Selezionare la stessa **Località** delle altre risorse e quindi fare clic su **OK**.

Ripetere questa procedura per aggiungere l'endpoint **Web-02**, usando l'indirizzo IP annotato in precedenza per **Web-02-ip**.

## <a name="create-an-alias-record"></a>Creare un record alias

Creare un record alias che punta al profilo di Gestione traffico.

1. Fare clic sulla zona DNS di Azure per aprirla.
2. Fare clic su **Set di record**.
3. Lasciare la casella di testo **Nome** vuota per rappresentare la radice del nome di dominio (ad esempio, contoso.com).
4. Lasciare l'opzione **Tipo** impostata su un record **A**.
5. Selezionare la casella di controllo **Set di record alias**.
6. Fare clic su **Choose Azure service** (Scegli servizio di Azure) e selezionare il profilo di Gestione traffico **TM-alias-test**.

## <a name="test-the-alias-record"></a>Testare il record alias

1. Da un Web browser passare alla radice del nome di dominio (ad esempio, contoso.com). Dovrebbe essere visualizzata la pagina Web predefinita di IIS. Chiudere il Web browser.
2. Arrestare la macchina virtuale **Web-01** e attendere qualche minuto che venga completato l'arresto.
3. Aprire un nuovo Web browser e passare di nuovo alla radice del nome di dominio.
4. Dovrebbe essere di nuovo visualizzata la pagina di IIS predefinita, perché Gestione traffico ha gestito la situazione e indirizzato il traffico a **Web-02**.

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non è più necessario, è possibile eliminare il gruppo di risorse **RG-DNS-Alias-TM** per eliminare tutte le risorse create per questa esercitazione.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato creato un record alias per usare la radice del nome di dominio per fare riferimento a un profilo di Gestione traffico. Per informazioni su DNS di Azure e le app Web, proseguire con l'esercitazione sulle app Web.

> [!div class="nextstepaction"]
> [Creare record DNS per un'app Web in un dominio personalizzato](./dns-web-sites-custom-domain.md)
