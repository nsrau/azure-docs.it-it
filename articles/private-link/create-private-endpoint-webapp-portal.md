---
title: Connettersi privatamente a un'app Web usando un endpoint privato di Azure
description: Connettersi privatamente a un'app Web usando un endpoint privato di Azure
author: ericgre
ms.assetid: b8c5c7f8-5e90-440e-bc50-38c990ca9f14
ms.topic: article
ms.date: 03/12/2020
ms.author: ericg
ms.service: app-service
ms.workload: web
ms.openlocfilehash: f19e4e34b2ec8cebc9e1841f277f26fba941bb89
ms.sourcegitcommit: bc792d0525d83f00d2329bea054ac45b2495315d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/06/2020
ms.locfileid: "78673819"
---
# <a name="connect-privately-to-a-web-app-using-azure-private-endpoint-preview"></a>Connettersi privatamente a un'app Web usando l'endpoint privato di Azure (anteprima)

Endpoint privato di Azure è il blocco predefinito fondamentale per il collegamento privato in Azure. Consente di connettersi privatamente all'app Web.
In questa Guida introduttiva si apprenderà come distribuire un'app Web con endpoint privato e connettersi a questa app Web da una macchina virtuale.

## <a name="sign-in-to-azure"></a>Accedere ad Azure

Accedere al portale di Azure all'indirizzo https://portal.azure.com.

## <a name="virtual-network-and-virtual-machine"></a>Rete virtuale e macchina virtuale

In questa sezione si creeranno la rete virtuale e la subnet per ospitare la macchina virtuale usata per accedere all'app Web tramite l'endpoint privato.

### <a name="create-the-virtual-network"></a>Creare la rete virtuale

Questa sezione illustra come creare una rete virtuale e una subnet.

1. Sul lato superiore sinistro della schermata selezionare **Crea una risorsa** ** > rete** > **rete virtuale** o cercare **rete virtuale** nella casella di ricerca.

1. In **Crea rete virtuale**immettere o selezionare queste informazioni nella scheda nozioni di base:

 ![Creare una rete virtuale][1]

1. Fare clic su **"Avanti: indirizzi IP >"** e immettere o selezionare queste informazioni:

![Configurare gli indirizzi IP][2]

1. Nella sezione subnet fare clic su **"+ Aggiungi subnet"** e immettere le informazioni seguenti e fare clic su **"Aggiungi"** .

![Aggiungi subnet][3]

1. Fare clic su **"verifica + crea"**

1. Una volta superata la convalida, fare clic su **"Crea"**

### <a name="create-virtual-machine"></a>Crea macchina virtuale

1. Sul lato superiore sinistro della schermata nella portale di Azure selezionare **Crea una risorsa** > **calcolo** > **macchina virtuale**

1. In creare una macchina virtuale-nozioni di base immettere o selezionare queste informazioni:

![Macchina virtuale di base ][4]

1. Selezionare **"Next: Disks"**

Mantieni le impostazioni predefinite.

1. Selezionare **"Avanti: rete"** e selezionare queste informazioni:

![Rete ][5]

1. Fare clic su **"verifica + crea"**

1. Quando viene superato il messaggio di convalida, fare clic su **"Crea"**

## <a name="create-your-web-app-and-private-endpoint"></a>Creare l'app Web e l'endpoint privato

In questa sezione si creerà un'app Web privata con un endpoint privato.

### <a name="web-app"></a>App Web

1. Sul lato superiore sinistro della schermata nella portale di Azure selezionare **Crea una risorsa** > **Web** > **app Web**

1. In Crea app Web-Nozioni di base immettere o selezionare queste informazioni:

![App Web di base ][6]

1. Selezionare **"verifica + crea"**

1. Quando viene superato il messaggio di convalida, fare clic su **"Crea"**

### <a name="create-the-private-endpoint"></a>Creare l'endpoint privato

1. Nelle proprietà dell'app Web selezionare **impostazioni** > **rete** e fare clic su **"Configura connessioni endpoint privato"** .

![Rete di app Web][7]

1. Nella procedura guidata fare clic su **"+ Aggiungi"**

![Endpoint privato dell'app Web][8]

1. Inserire le informazioni sulla sottoscrizione, VNET e sulla subnet e fare clic su **"OK"**

![Rete di app Web][9]

1. Esaminare la creazione dell'endpoint privato

![rivedere][10]
![visualizzazione finale dell'endpoint privato][11]

## <a name="connect-to-a-vm-from-the-internet"></a>Connettersi a una VM da Internet

1. Nella barra di ricerca del portale immettere **myVm**
1. Selezionare il **pulsante Connetti**. Dopo aver selezionato il pulsante Connetti, Connetti a macchina virtuale Apri, seleziona **RDP**

![Pulsante RDP][12]

1. Azure crea un file di Remote Desktop Protocol (con estensione RDP) e lo scarica nel computer dopo aver fatto clic su **Scarica file RDP** .

![Scarica file RDP][13]

1. Aprire il file con estensione RDP scaricato.

- Se richiesto, selezionare Connetti.
- Immettere il nome utente e la password specificati al momento della creazione della macchina virtuale.

> [!Note]
> Potrebbe essere necessario selezionare altre opzioni > usare un account diverso per specificare le credenziali immesse durante la creazione della macchina virtuale.

- Selezionare OK.

1. Durante il processo di accesso potrebbe essere visualizzato un avviso relativo al certificato. Se viene visualizzato un avviso di certificato, selezionare Sì o continua.

1. Quando viene visualizzato il desktop della macchina virtuale, ridurlo a icona per tornare al desktop locale.

## <a name="access-web-app-privately-from-the-vm"></a>Accedi privatamente all'app Web dalla macchina virtuale

In questa sezione si effettuerà la connessione privata all'app Web usando l'endpoint privato.

1. Ottenere l'indirizzo IP privato dell'endpoint privato, nella barra di ricerca digitare **collegamento privato**e selezionare collegamento privato

![Collegamento privato][14]

1. Nel centro collegamenti privati selezionare **endpoint privati** per elencare tutti gli endpoint privati

![Centro collegamenti privati][15]

1. Selezionare il collegamento all'endpoint privato per l'app Web e la subnet

![Proprietà endpoint privato][16]

1. Copiare l'indirizzo IP privato dell'endpoint privato e il nome di dominio completo dell'app Web, in questo caso webappdemope.azurewebsites.net 10.10.2.4

1. In myVM verificare che l'app Web non sia accessibile tramite l'indirizzo IP pubblico. Aprire un browser e copiare il nome dell'app Web, è necessario disporre di una pagina di errore 403-accesso negato

![Accesso negato][17]

> [!Note]
> Poiché questa funzionalità è in anteprima, è necessario gestire manualmente la voce DNS.

1. Creare la voce host, aprire Esplora file e individuare il file hosts

![File hosts][18]

1. Aggiungere una voce con l'indirizzo IP privato e il nome pubblico dell'app Web modificando il file degli host con il blocco note

![Contenuto host][19]

1. Salvare il file.

1. Aprire un browser e digitare l'URL dell'app Web

![Sito Web con PE][20]

1. Si accede all'app Web tramite l'endpoint privato

## <a name="clean-up-resources"></a>Pulire le risorse

Al termine dell'uso dell'endpoint privato, dell'app Web e della macchina virtuale, eliminare il gruppo di risorse e tutte le risorse in esso contenute:

1. Immettere Ready-RG nella casella di ricerca nella parte superiore del portale e selezionare Ready-RG nei risultati della ricerca.
1. Selezionare Elimina gruppo di risorse.
1. Immettere Ready-RG per digitare il nome del gruppo di risorse e selezionare Elimina.

## <a name="next-steps"></a>Passaggi successivi

In questa Guida introduttiva è stata creata una VM in una rete virtuale, un'app Web e un endpoint privato. È stata effettuata la connessione a una VM da Internet e la comunicazione in modo sicuro con l'app Web usando un collegamento privato. Per altre informazioni sull'endpoint privato, vedere [che cos'è endpoint privato di Azure][privateendpoint].

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
[privateendpoint]: https://docs.microsoft.com/azure/private-link/private-endpoint-overview
