---
title: Connettersi privatamente a un'app Web usando l'endpoint privato di AzureConnect privately to a Web App using Azure Private Endpoint
description: Connettersi privatamente a un'app Web usando l'endpoint privato di AzureConnect privately to a Web App using Azure Private Endpoint
author: ericgre
ms.assetid: b8c5c7f8-5e90-440e-bc50-38c990ca9f14
ms.topic: article
ms.date: 03/12/2020
ms.author: ericg
ms.service: app-service
ms.workload: web
ms.openlocfilehash: 2f10c7378ae7681b14df6e96b6a6f1adac832d1b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80287816"
---
# <a name="connect-privately-to-a-web-app-using-azure-private-endpoint-preview"></a>Connettersi privatamente a un'app Web usando l'endpoint privato di Azure (anteprima)Connect privately to a Web App using Azure Private Endpoint (Preview)

Azure Private Endpoint is the fundamental building block for Private Link in Azure. Consente di connettersi privatamente all'app Web.
In questa Guida introduttiva verrà illustrato come distribuire un'app Web con endpoint privato e connettersi a questa app Web da una macchina virtuale.

Per altre informazioni, vedere [Uso di endpoint privati per l'app Web][privatenedpointwebapp]di Azure.For more information, see Using Private Endpoints for Azure Web App .

> [!Note]
>L'anteprima è disponibile nelle regioni Stati Uniti orientali e occidentali 2 per tutte le app Web PremiumV2 e Linux. 

## <a name="sign-in-to-azure"></a>Accedere ad Azure

Accedere al portale di Azure all'indirizzo https://portal.azure.com.

## <a name="virtual-network-and-virtual-machine"></a>Rete virtuale e macchina virtuale

In questa sezione verranno create la rete virtuale e la subnet per ospitare la macchina virtuale usata per accedere all'app Web tramite l'endpoint privato.

### <a name="create-the-virtual-network"></a>Creare la rete virtuale

In questa sezione verranno create una rete virtuale e una subnet.

1. Nella parte superiore sinistra dello schermo selezionare Crea una**Networking** > **rete virtuale** di **rete di risorse** > o cercare **Rete virtuale** nella casella di ricerca.

1. In **Crea rete virtuale**immettere o selezionare queste informazioni nella scheda Nozioni di base:

   > [!div class="mx-imgBorder"]
   > ![Crea rete virtuale][1]

1. Fare clic su **"Avanti: indirizzi IP >"** e immettere o selezionare queste informazioni:

   > [!div class="mx-imgBorder"]
   >![Configurare gli indirizzi IP][2]

1. Nella sezione della subnet, fare clic su **"Aggiungi subnet"** e immettere le seguenti informazioni e fare clic su **"Aggiungi"**

   > [!div class="mx-imgBorder"]
   >![Aggiungi subnet][3]

1. Fare clic su **"Revisione e creazione"**

1. Dopo aver superato la convalida, fare clic su **"Crea"**

### <a name="create-virtual-machine"></a>Creare macchina virtuale

1. Nella parte superiore sinistra dello schermo nel portale di Azure selezionare Crea una**macchina virtuale** di**calcolo** >  **delle risorse** > 

1. In Creare una macchina virtuale - Informazioni di base, immettere o selezionare queste informazioni:

   > [!div class="mx-imgBorder"]
   >![Macchina virtuale di base][4]

1. Selezionare **"Avanti: Dischi"**

   Mantenere le impostazioni predefinite.

1. Selezionare **"Avanti: Rete"**, selezionare queste informazioni:

   > [!div class="mx-imgBorder"]
   >![Rete ][5]

1. Fare clic su **"Revisione e creazione"**

1. Quando viene superata la convalida del messaggio, fare clic su **"Crea"**

## <a name="create-your-web-app-and-private-endpoint"></a>Creare l'app Web e l'endpoint privatoCreate your Web App and Private Endpoint

In questa sezione verrà creata un'app Web privata utilizzando un endpoint privato.

> [!Note]
>La funzionalità Endpoint privato è disponibile solo per lo SKU Premium V2.

### <a name="web-app"></a>App Web

1. Nella parte superiore sinistra dello schermo nel portale di Azure selezionare **Crea un'app** > **Web** > **Web App** di risorse

1. In Crea app Web - Nozioni di base immettere o selezionare le informazioni seguente:

   > [!div class="mx-imgBorder"]
   >![App Web di base][6]

1. Selezionare **"Revisione e creazione"**

1. Quando viene superata la convalida del messaggio, fare clic su **"Crea"**

### <a name="create-the-private-endpoint"></a>Creare l'endpoint privatoCreate the Private endpoint

1. Nelle proprietà dell'app Web selezionare **Impostazioni** > **di rete** e fare clic su **"Configura connessioni endpoint privati"**

   > [!div class="mx-imgBorder"]
   >![Rete di app Web][7]

1. Nella procedura guidata, fare clic su **"aggiungi"**

   > [!div class="mx-imgBorder"]
   >![Endpoint privato dell'app Web][8]

1. Compilare le informazioni relative alla sottoscrizione, alla rete virtuale e alla subnet e fare clic su **"OK"**

   > [!div class="mx-imgBorder"]
   >![Rete di app Web][9]

1. Esaminare la creazione dell'endpoint privatoReview the creation of the private endpoint

   > [!div class="mx-imgBorder"]
   >![Esaminare][10]
   >![la visualizzazione finale dell'endpoint privatoReview Final view of the Private endpoint][11]

## <a name="connect-to-a-vm-from-the-internet"></a>Connettersi a una VM da Internet

1. Nella barra di ricerca del portale, immettere **myVm**
1. Selezionare il **pulsante Connetti**. Dopo aver selezionato il pulsante Connetti, si apre Connetti a macchina virtuale, selezionare **RDP**

   > [!div class="mx-imgBorder"]
   >![Pulsante RDP][12]

1. Azure crea un file Remote Desktop Protocol (con estensione rdp) e lo scarica nel computer dopo aver fatto clic su **Scarica file RDP**

   > [!div class="mx-imgBorder"]
   >![Scarica il file RDP][13]

1. Aprire il file downloaded.rdp.

- Quando richiesto, selezionare Connetti.
- Immettere il nome utente e la password specificati al momento della creazione della macchina virtuale.

> [!Note]
> Potrebbe essere necessario selezionare Altre opzioni > Usare un account diverso per specificare le credenziali immesse al momento della creazione della macchina virtuale.

- Selezionare OK.

1. Durante il processo di accesso potrebbe essere visualizzato un avviso relativo al certificato. Se si riceve un avviso relativo al certificato, selezionare Sì oppure Continua.

1. Quando viene visualizzato il desktop della macchina virtuale, ridurlo a icona per tornare al desktop locale.

## <a name="access-web-app-privately-from-the-vm"></a>Accedere all'app Web privatamente dalla macchina virtualeAccess Web App privately from the VM

In questa sezione si connetterà privatamente all'app Web utilizzando l'endpoint privato.

1. Ottenere l'IP privato dell'endpoint privato, nella barra di ricerca digitare **Private Link**, quindi selezionare Private Link

   > [!div class="mx-imgBorder"]
   >![Collegamento privato][14]

1. Nel Centro collegamenti privati, selezionare **Endpoint privati** per elencare tutti gli endpoint privati

   > [!div class="mx-imgBorder"]
   >![Centro collegamenti privati][15]

1. Selezionare il collegamento Endpoint privato all'app Web e alla subnet

   > [!div class="mx-imgBorder"]
   >![Proprietà dell'endpoint privatoPrivate endpoint properties][16]

1. Copiare l'IP privato dell'endpoint privato e il nome di dominio completo dell'app Web, nel caso in cui webappdemope.azurewebsites.net 10.10.2.4

1. Nella myVM verificare che l'app Web non sia accessibile tramite l'IP pubblico. Aprire un browser e incollare il nome dell'app Web, è necessario disporre di una pagina di errore 403 non consentita

   > [!div class="mx-imgBorder"]
   >![Proibito][17]

> [!Important]
> Poiché questa funzionalità è in anteprima, è necessario gestire manualmente la voce DNS.

1. Creare la voce host, aprire Esplora file e individuare il file hosts

   > [!div class="mx-imgBorder"]
   >![File Hosts][18]

1. Aggiungere una voce con l'indirizzo IP privato e il nome pubblico dell'app Web modificando il file hosts con blocco note

   > [!div class="mx-imgBorder"]
   >![Ospita contenuti][19]

1. Salvare il file.

1. Aprire un browser e digitare l'URL dell'app Web

   > [!div class="mx-imgBorder"]
   >![Sito Web con PE][20]

1. Si accede all'app Web tramite l'endpoint privato

## <a name="clean-up-resources"></a>Pulire le risorse

Al termine dell'utilizzo dell'endpoint privato, dell'app Web e della macchina virtuale, eliminare il gruppo di risorse e tutte le risorse in esso contenute:

1. Immettere ready-rg nella casella di ricerca nella parte superiore del portale e selezionare ready-rg dai risultati della ricerca.
1. Selezionare Elimina gruppo di risorse.
1. Immettere ready-rg per TYPE THE RESOURCE GROUP NAME e selezionare Delete .Enter.

## <a name="next-steps"></a>Passaggi successivi

In questa Guida introduttiva è stata creata una macchina virtuale in una rete virtuale, un'app Web e un endpoint privato. Si è connessi a una macchina virtuale da Internet e si è comunicati in modo sicuro all'app Web tramite Private Link. Per altre informazioni sull'endpoint privato, vedere [Che cos'è l'endpoint privato][privateendpoint]di Azure.To learn more about Private Endpoint, see What is Azure Private Endpoint.

<!--Image references-->
[1]: ./media/create-private-endpoint-webapp-portal/createnetwork.png
[2]: ./media/create-private-endpoint-webapp-portal/ipaddresses.png
[3]: ./media/create-private-endpoint-webapp-portal/subnet.png
[4]: ./media/create-private-endpoint-webapp-portal/virtualmachine.png
[5]: ./media/create-private-endpoint-webapp-portal/vmnetwork.png
[6]: ./media/create-private-endpoint-webapp-portal/webapp.png
[7]: ./media/create-private-endpoint-webapp-portal/webappnetworking.png
[8]: ./media/create-private-endpoint-webapp-portal/webapppe.png
[9]: ./media/create-private-endpoint-webapp-portal/webapppenetwork.png
[10]: ./media/create-private-endpoint-webapp-portal/inprogress.png
[11]: ./media/create-private-endpoint-webapp-portal/webapppefinal.png
[12]: ./media/create-private-endpoint-webapp-portal/rdp.png
[13]: ./media/create-private-endpoint-webapp-portal/rdpdownload.png
[14]: ./media/create-private-endpoint-webapp-portal/pl.png
[15]: ./media/create-private-endpoint-webapp-portal/plcenter.png
[16]: ./media/create-private-endpoint-webapp-portal/privateendpointproperties.png
[17]: ./media/create-private-endpoint-webapp-portal/forbidden.png
[18]: ./media/create-private-endpoint-webapp-portal/explorer.png
[19]: ./media/create-private-endpoint-webapp-portal/hosts.png
[20]: ./media/create-private-endpoint-webapp-portal/webappwithpe.png

<!--Links-->
[privatenedpointwebapp]: https://docs.microsoft.com/azure/app-service/networking/private-endpoint
[privateendpoint]: https://docs.microsoft.com/azure/private-link/private-endpoint-overview
