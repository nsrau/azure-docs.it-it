<properties
	pageTitle="Aggiungere l'API di SQL Server a PowerApps Enterprise | Microsoft Azure"
	description="Creare o configurare una nuova API di SQL Server nell’ambiente del servizio app dell'organizzazione e aggiungere una connessione ai dati locali"
	services=""
    suite="powerapps"
	documentationCenter="" 
	authors="linhtranms"
	manager="dwrede"
	editor=""/>


<tags
   ms.service="powerapps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="03/03/2016"
   ms.author="litran"/>


# Creare una nuova API SQL Server in PowerApps Enterprise

Aggiungere l'API SQL Server all'ambiente del servizio app dell'organizzazione (tenant).

## Creare l'API nel portale di Azure

1. Nel [portale di Azure](https://portal.azure.com/) accedere con l'account aziendale. Ad esempio, accedere con *NomeUtente*@*Azienda*.com. Quando si esegue questa operazione, si accede automaticamente alla sottoscrizione della propria società. 
2. Selezionare **Sfoglia** nella barra delle applicazioni: ![][14]  
3. Nell'elenco, è possibile scorrere per trovare PowerApps o digitare *powerapps*: ![][15]  
4. In **PowerApps** selezionare **Gestione API**.
5. In **Gestione API** selezionare **Aggiungi** per aggiungere la nuova API.
6. Immettere un **nome** descrittivo per l'API. Ad esempio, si aggiunge l'API di SQL Server per la dimostrazione, è possibile denominarla *SQLServerDemo*.  	
7. In **Origine** selezionare **API disponibili** per selezionare le API predefinite e selezionare **SQL Server**. 
8. Selezionare **OK** per completare i passaggi.

Al termine, verrà aggiunta una nuova API di SQL Server all'ambiente del servizio app.

## Configurare la connettività a SQL Server locale

È possibile connettersi a SQL Server locale. Per stabilire questa connettività ibrida, è possibile sfruttare soluzioni di rete ibrida esistenti in Azure, soluzioni di rete tra cui:

- [ExpressRoute](../expressroute/expressroute-introduction.md)
- [Da sito a VPN](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)
- [Connettività Point-to-Site](../vpn-gateway/vpn-gateway-point-to-site-create.md)  

	> [AZURE.NOTE]  Ogni ambiente del servizio app è associato a una rete virtuale. È possibile stabilire questa connettività di rete a questa rete virtuale.  
- [Connessioni ibride](../app-service-web/web-sites-hybrid-connection-get-started.md)  

	> [AZURE.NOTE]  Tutte le API registrate nell'ambiente del servizio app hanno un'app Web corrispondente. È possibile stabilire connessioni ibride da questa app Web proprio come per qualsiasi altra applicazione Web.
	
Il seguente esempio mostra come creare una connessione ibrida:

1. Selezionare l'API di SQL Server appena creata e selezionare il gruppo di risorse. In questo esempio selezionare l'API chiamata *sqlconnectordemo* e selezionare il gruppo di risorse *DedicatedAses*: ![Gruppo di risorse](./media/powerapps-create-api-sqlserver/sqlapi.png)

2.  Selezionare il riquadro **Risorse** e quindi selezionare l’app Web con lo stesso nome dell'API di SQL Server. In questo esempio selezionare *sqlconnectordemo*: ![App Web di SQL](./media/powerapps-create-api-sqlserver/sqlwebapp.png)

3.  In **Impostazioni** selezionare **Rete**. Selezionare **Configurare gli endpoint di connessione ibrida**, quindi seguire [queste istruzioni](../app-service-web/web-sites-hybrid-connection-get-started.md) per creare la connessione ibrida: ![Rete](./media/powerapps-create-api-sqlserver/network.png)

Una volta che la connessione ibrida è stata creata e connessa, è stata abilitata la connessione al server locale. Successivamente, creare la connessione ai dati e fornire agli utenti l'accesso: ![Connessione ibrida](./media/powerapps-create-api-sqlserver/hybridconn.png)

## Creare una connessione per l'API di SQL Server

1. Nel portale di Azure, aprire PowerApps e selezionare **Gestione API**. Viene visualizzato un elenco delle API configurate: ![](./media/powerapps-create-api-sqlserver/apilist.png)

2. Selezionare l'API desiderata. In questo esempio, selezionare **SQLServerDemo** e selezionare **Connessioni**.

3. In Connessioni, selezionare **Aggiungi connessione**: ![](./media/powerapps-create-api-sqlserver/addconnection.png)

4. Immettere un nome per la connessione e inserire la stringa di connessione. L’immissione della stringa di connessione richiede la conoscenza di alcune proprietà specifiche relative al servizio a cui ci si connette. Ad esempio, se ci si connette al server SQL locale, è necessario conoscere il nome utente, la password e altre proprietà necessarie per stabilire la connessione.

5. Selezionare **Aggiungi** per salvare le modifiche.

## Riepilogo e passaggi successivi
In questo argomento è stato aggiunta l'API di SQL Server per connettersi a SQL Server locale. Successivamente, fornire agli utenti accesso all'API, in modo che sia possibile aggiungerla alle loro app:

[Aggiungere una connessione e fornire agli utenti l'accesso](powerapps-manage-api-connection-user-access.md)


[14]: ./media/powerapps-create-api-sqlserver/browseall.png
[15]: ./media/powerapps-create-api-sqlserver/allresources.png

<!---HONumber=AcomDC_0309_2016-->