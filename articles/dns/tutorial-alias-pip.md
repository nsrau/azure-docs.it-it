---
title: Esercitazione - Creare un record alias servizio DNS di Azure per fare riferimento a un indirizzo IP pubblico di Azure.
description: Questa esercitazione illustra come configurare un record alias servizio DNS di Azure per fare riferimento a un indirizzo IP pubblico di Azure.
services: dns
author: vhorne
ms.service: dns
ms.topic: tutorial
ms.date: 9/25/2018
ms.author: victorh
ms.openlocfilehash: 842015d853790e3ac78214cca6a70becb7f9fadf
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/24/2018
ms.locfileid: "46991230"
---
# <a name="tutorial-configure-an-alias-record-to-refer-to-an-azure-public-ip-address"></a>Esercitazione: Configurare un record alias per fare riferimento a un indirizzo IP pubblico di Azure 

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Creare l'infrastruttura di rete
> * Creare una macchina virtuale Web server
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
2. Nella parte superiore sinistra nel portale, fare clic su **Creare una risorsa**, digitare *gruppo di risorse* nella casella di ricerca e creare un gruppo di risorse denominato **RG-DNS-Alias-pip**.
3. Fare clic su **Creare una risorsa**, su **Rete** e quindi fare clic su **Rete virtuale**.
4. Creare una rete virtuale denominata **VNet-Server**, posizionarla nel gruppo di risorse **RG-DNS-Alias-pip** e assegnare il nome **SN-Web** alla subnet.

## <a name="create-a-web-server-virtual-machine"></a>Creare una macchina virtuale Web server
1. Fare clic su **Creare una risorsa** e su **Macchina virtuale Windows Server 2016**.
2. Digitare **Web-01** per il nome e inserire la macchina virtuale nel gruppo di risorse **RG-DNS-Alias-TM**. Digitare nome utente e password, quindi fare clic su **OK**.
3. In **Dimensioni** scegliere una SKU con 8 GB di RAM.
4. In **Impostazioni** selezionare la rete virtuale **VNet-Servers** e la subnet **SN-Web**. Per le porte in ingresso pubbliche, selezionare **HTTP**, **HTTPS** e **RDP (3389)**, quindi fare clic su **OK**.
5. Nella pagina Riepilogo fare clic su **Creare**.

   Per il completamento dell'operazione sono richiesti alcuni minuti.

### <a name="install-iis"></a>Installare IIS

Installare IIS in **Web-01**.

1. Connettersi a **Web-01** ed eseguire l'accesso.
2. Dal dashboard di Server Manager fare clic su **Aggiungere ruoli e funzionalità**.
3. Fare clic su **Avanti** tre volte e nella pagina **Ruoli server** selezionare **Server Web (IIS)**.
4. Fare clic su **Aggiungi funzionalità** e quindi su **Avanti**.
5. Fare clic su **Avanti** quattro volte e quindi fare clic su **Installa**.

   Questa operazione richiederà qualche minuto.
6. Al termine dell'installazione, fare clic su **Chiudi**.
7. Aprire un Web browser e passare a **localhost** per verificare che venga visualizzata la pagina Web di IIS predefinita.

## <a name="create-an-alias-record"></a>Creare un record alias

Creare un record alias che punta all’indirizzo IP pubblico.

1. Fare clic sulla zona DNS di Azure per aprirla.
2. Fare clic su **Set di record**.
3. Nella casella di testo **Nome** **web01**.
4. Lasciare l'opzione **Tipo** impostata su un record **A**.
5. Selezionare la casella di controllo **Set di record alias**.
6. Fare clic su **Scegliere servizio di Azure** e selezionare l’indirizzo IP pubblico **Web-01-ip**.

## <a name="test-the-alias-record"></a>Testare il record alias

1. Nel gruppo di risorse**RG-DNS-Alias-pip**, fare clic sulla macchina virtuale **Web-01**. Si noti l'indirizzo IP pubblico.
1. Da un Web browser, passare al nome di dominio completo per la macchina virtuale Web01-01. Ad esempio: **web01.contoso.com**. Dovrebbe essere visualizzata la pagina Web predefinita di IIS.
2. Chiudere il Web browser.
3. Arrestare la macchina virtuale **Web-01** e quindi ravviarla.
4. Quando si riavvia, si noti il nuovo indirizzo IP pubblico per la macchina virtuale.
5. Aprire un nuovo browser e passare nuovamente al nome di dominio completo per la macchina virtuale Web01-01. Ad esempio: **web01.contoso.com**.
6. Ciò avrà comunque esito positivo, poiché è stato usato un record alias che punta alla risorsa di indirizzo IP pubblico e non un record A standard.

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non è più necessario, è possibile eliminare il gruppo di risorse **RG-DNS-Alias-pip** per eliminare tutte le risorse create per questa esercitazione.


## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato creato un record di alias per fare riferimento a un indirizzo IP pubblico di Azure. Per informazioni su DNS di Azure e le app Web, proseguire con l'esercitazione sulle app Web.

> [!div class="nextstepaction"]
> [Creare record DNS per un'app Web in un dominio personalizzato](./dns-web-sites-custom-domain.md)
