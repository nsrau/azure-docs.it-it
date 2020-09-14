---
title: Connettersi privatamente a un'app Web usando l'endpoint privato di Azure (anteprima)
description: Questo articolo illustra come connettersi privatamente a un'app Web usando l'endpoint privato di Azure (anteprima).
author: ericgre
ms.assetid: b8c5c7f8-5e90-440e-bc50-38c990ca9f14
ms.topic: how-to
ms.date: 09/08/2020
ms.author: ericg
ms.service: app-service
ms.workload: web
ms.openlocfilehash: ccbcdbe9204120e1cf181136f566556ec30be871
ms.sourcegitcommit: 814778c54b59169c5899199aeaa59158ab67cf44
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/13/2020
ms.locfileid: "90054535"
---
# <a name="connect-privately-to-a-web-app-by-using-azure-private-endpoint-preview"></a>Connettersi privatamente a un'app Web usando l'endpoint privato di Azure (anteprima)

Endpoint privato di Azure (anteprima) è il blocco predefinito fondamentale per il collegamento privato di Azure. Con l'endpoint privato è possibile connettersi privatamente all'app Web. In questo articolo si apprenderà come distribuire un'app Web usando un endpoint privato e quindi connettersi all'app Web da una macchina virtuale (VM).

Per altre informazioni, vedere [usare endpoint privati per un'app Web di Azure][privateendpointwebapp].

> [!Note]
> L'endpoint privato (anteprima) è disponibile nelle aree pubbliche per le app Web Windows di livello PremiumV2, le app Web Linux e il piano Premium di funzioni di Azure (talvolta definito piano elastico Premium). 

## <a name="sign-in-to-the-azure-portal"></a>Accedere al portale di Azure

Prima di iniziare, accedere al [portale di Azure](https://portal.azure.com).

## <a name="create-a-virtual-network-and-virtual-machine"></a>Creare una rete virtuale e una macchina virtuale

Questa sezione illustra come creare una rete virtuale e una subnet per ospitare una macchina virtuale che verrà usata per accedere a un'app Web tramite un endpoint privato.

### <a name="create-the-virtual-network"></a>Creare la rete virtuale

Per creare la rete virtuale e la subnet, eseguire le operazioni seguenti:

1. Nel riquadro sinistro selezionare **Crea una risorsa**  >  **Networking**  >  **rete rete virtuale**.

1. Nel riquadro **Crea rete virtuale** selezionare la scheda **nozioni di base** e quindi immettere le informazioni riportate di seguito:

   > [!div class="mx-imgBorder"]
   > ![Screenshot del riquadro "Crea rete virtuale" nel portale di Azure.][1]

1. Selezionare la scheda **indirizzi IP** , quindi immettere le informazioni riportate di seguito:

   > [!div class="mx-imgBorder"]
   > ![Screenshot della scheda "indirizzi IP" del riquadro Crea rete virtuale.][2]

1. Nella sezione **subnet** selezionare **Aggiungi Subnet**, immettere le informazioni mostrate qui, quindi selezionare **Aggiungi**.

   > [!div class="mx-imgBorder"]
   > ![Screenshot del riquadro "Aggiungi subnet".][3]

1. Selezionare **Rivedi e crea**.

1. Dopo aver completato la convalida, selezionare **Crea**.

### <a name="create-the-virtual-machine"></a>Creare la macchina virtuale

Per creare la macchina virtuale, eseguire le operazioni seguenti:

1. Nel portale di Azure, nel riquadro sinistro, selezionare **Crea una risorsa**  >  **calcolo**  >  **macchina virtuale**.

1. Nel riquadro **Crea una macchina virtuale-nozioni di base** immettere le informazioni riportate di seguito:

   > [!div class="mx-imgBorder"]
   > ![Screenshot del riquadro "creare una macchina virtuale".][4]

1. Selezionare **Avanti: Dischi**.

1. Nel riquadro **dischi** , Mantieni le impostazioni predefinite e quindi seleziona **Avanti: rete**.

1. Nel riquadro **rete** immettere le informazioni indicate di seguito:

   > [!div class="mx-imgBorder"]
   > ![Screenshot della scheda "rete" del riquadro "creare una macchina virtuale".][5]

1. Selezionare **Rivedi e crea**.

1. Dopo aver completato la convalida, selezionare **Crea**.

## <a name="create-a-web-app-and-a-private-endpoint"></a>Creare un'app Web e un endpoint privato

In questa sezione si crea un'app Web privata che usa un endpoint privato.

> [!Note]
> La funzionalità endpoint privato è disponibile solo per il livello PremiumV2.

### <a name="create-the-web-app"></a>Creare l'app Web

1. Nel portale di Azure, nel riquadro sinistro, selezionare **Crea una risorsa**Web  >  **Web**  >  **app**Web.

1. Nel riquadro **app Web** selezionare la scheda **nozioni di base** e quindi immettere le informazioni riportate di seguito:

   > [!div class="mx-imgBorder"]
   > ![Screenshot della scheda "Nozioni di base" del riquadro "app Web".][6]

1. Selezionare **Rivedi e crea**.

1. Dopo aver completato la convalida, selezionare **Crea**.

### <a name="create-the-private-endpoint"></a>Creare l'endpoint privato

1. In proprietà dell'app Web in **Impostazioni**selezionare **rete**e quindi in **connessioni a endpoint privati (anteprima)** selezionare **Configura connessioni a endpoint privato**.

   > [!div class="mx-imgBorder"]
   > ![Screenshot del collegamento "configurare le connessioni all'endpoint privato" nel riquadro rete dell'app Web.][7]

1. Nella procedura guidata **connessioni a endpoint privato (anteprima)** selezionare **Aggiungi**.

   > [!div class="mx-imgBorder"]
   > ![Screenshot del pulsante Aggiungi nella procedura guidata "connessioni a endpoint privati (anteprima)".][8]

1. Selezionare le informazioni corrette negli elenchi a discesa **sottoscrizione**, **rete virtuale**e **subnet** , quindi fare clic su **OK**.

   > [!div class="mx-imgBorder"]
   > ![Screenshot del riquadro "Aggiungi endpoint privato (anteprima)".][9]

1. Monitorare lo stato di avanzamento della creazione dell'endpoint privato.

   > [!div class="mx-imgBorder"]
   > ![Screenshot dello stato di avanzamento dell'aggiunta dell'endpoint privato. ][10]
   >  ![ Screenshot dell'endpoint privato appena creato.][11]

## <a name="connect-to-the-vm-from-the-internet"></a>Connettersi alla macchina virtuale da Internet

1. Nella casella di **ricerca** portale di Azure immettere **myVm**.
1. Selezionare **Connetti**, quindi selezionare **RDP**.

   > [!div class="mx-imgBorder"]
   > ![Screenshot del pulsante "RDP" nel riquadro "myVM".][12]

1. Nel riquadro **Connetti con RDP** selezionare **Scarica file RDP**.  

   > [!div class="mx-imgBorder"]
   > ![Screenshot del pulsante "Scarica file RDP" nel riquadro "Connetti con RDP".][13]

   Azure crea un file di Remote Desktop Protocol (RDP) e lo scarica nel computer.   

1. Aprire il file RDP scaricato.

   a. Al prompt selezionare **Connetti**.  
   b. Immettere il nome utente e la password specificati quando è stata creata la macchina virtuale.

     > [!Note]
     > Per usare queste credenziali, potrebbe essere necessario selezionare **altre opzioni**per  >  **usare un account diverso**.

1. Selezionare **OK**.

   > [!Note]
   > Se si riceve un avviso di certificato durante il processo di accesso, selezionare **Sì** o **continua**.

1. Quando viene visualizzata la finestra del desktop della macchina virtuale, ridurla a icona per tornare al desktop locale.

## <a name="access-the-web-app-privately-from-the-vm"></a>Accedere all'app Web privatamente dalla macchina virtuale

In questa sezione si connette privatamente all'app Web usando l'endpoint privato.

1. Per ottenere l'indirizzo IP privato dell'endpoint privato, nella casella di **ricerca** Digitare **collegamento privato** e quindi nell'elenco dei risultati selezionare **collegamento privato**.

   > [!div class="mx-imgBorder"]
   > ![Screenshot del collegamento "collegamento privato" nell'elenco dei risultati della ricerca.][14]

1. Nel riquadro sinistro di centro collegamenti privati selezionare **endpoint privati** per visualizzare gli endpoint privati.

   > [!div class="mx-imgBorder"]
   > ![Screenshot dell'elenco di endpoint privati in centro collegamenti privati.][15]

1. Selezionare l'endpoint privato che si collega all'app Web e alla subnet.

   > [!div class="mx-imgBorder"]
   > ![Screenshot del riquadro proprietà per un endpoint privato.][16]

1. Copiare l'indirizzo IP privato dell'endpoint privato e il nome di dominio completo (FQDN) dell'app Web. Nell'esempio precedente, l'ID privato è *`webappdemope.azurewebsites.net 10.10.2.4`* .

1. Nel riquadro **myVM** verificare che l'app Web sia inaccessibile tramite l'indirizzo IP pubblico. A tale scopo, aprire un browser e incollare il nome dell'app Web. Nella pagina verrà visualizzato il messaggio "errore 403-accesso negato".

   > [!div class="mx-imgBorder"]
   > ![Screenshot della pagina di errore "errore 403-accesso negato".][17]

   > [!Important]
   > Poiché questa funzionalità è in anteprima, è necessario gestire manualmente la voce Domain Name Service (DNS).

   Per il DNS, effettuare una delle operazioni seguenti:
 
   - Usare il servizio di zona privata di DNS di Azure.  

     a. Creare una zona DNS privata denominata *`privatelink.azurewebsites.net`* e collegarla alla rete virtuale.  
     b. Creare i due record A (ovvero il nome dell'app e il nome di gestione controllo servizi [SCM]) con l'indirizzo IP dell'endpoint privato.  
     > [!div class="mx-imgBorder"]
     > ![Screenshot dei record della zona privata DNS.][21]  

   - Usare il file *hosts* della macchina virtuale.  

     a. Creare la voce hosts, aprire Esplora file e cercare il file *hosts* .  
     > [!div class="mx-imgBorder"]
     > ![Screenshot che mostra il file hosts in Esplora file.][18]  
     b. Aggiungere una voce che contiene l'indirizzo IP privato e il nome pubblico dell'app Web modificando il file *hosts* in un editor di testo.  
     > [!div class="mx-imgBorder"]
     > ![Screenshot del testo del file degli host.][19]  
     c. Salvare il file.

1. In un browser, digitare l'URL dell'app Web.

   > [!div class="mx-imgBorder"]
   > ![Screenshot di un browser che visualizza un'app Web.][20]

È ora possibile accedere all'app Web tramite l'endpoint privato.

## <a name="clean-up-resources"></a>Pulire le risorse

Al termine dell'uso dell'endpoint privato, dell'app Web e della macchina virtuale, eliminare il gruppo di risorse e tutte le risorse in esso contenute.

1. Nella casella di **ricerca** del portale di Azure digitare **Ready-RG**, quindi selezionare **Ready-RG** nell'elenco risultati.

1. Selezionare **Elimina gruppo di risorse**.

1. In **digitare il nome del gruppo di risorse**immettere **Ready-RG**, quindi selezionare **Elimina**.

## <a name="next-steps"></a>Passaggi successivi

In questo articolo è stata creata una VM in una rete virtuale, un'app Web e un endpoint privato. È stata effettuata la connessione a una VM da Internet e la comunicazione protetta con l'app Web è stata effettuata tramite un collegamento privato. 

Per altre informazioni sull'endpoint privato (anteprima), vedere [che cos'è endpoint privato di Azure?][privateendpoint].

<!--Image references-->
[1]: ./media/create-private-endpoint-webapp-portal/createnetwork.png
[2]: ./media/create-private-endpoint-webapp-portal/ipaddresses.png
[3]: ./media/create-private-endpoint-webapp-portal/subnet.png
[4]: ./media/create-private-endpoint-webapp-portal/virtual-machine.png
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
[21]: ./media/create-private-endpoint-webapp-portal/dns-private-zone-records.png


<!--Links-->
[privateendpointwebapp]: https://docs.microsoft.com/azure/app-service/networking/private-endpoint
[privateendpoint]: https://docs.microsoft.com/azure/private-link/private-endpoint-overview
