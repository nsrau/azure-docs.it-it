<properties
	pageTitle="Aggiungere l'API di Ricerca di Bing a PowerApps Enterprise | Microsoft Azure"
	description="Creare o configurare una nuova API di Ricerca di Bing nell’ambiente del servizio app dell'organizzazione"
	services=""
    suite="powerapps"
	documentationCenter="" 
	authors="LinhTran"
	manager="erikre"
	editor=""/>

<tags
   ms.service="powerapps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="03/29/2016"
   ms.author="litran"/>

# Creare una nuova API di Ricerca di Bing in PowerApps Enterprise

> [AZURE.SELECTOR]
- [App per la logica](../articles/connectors/connectors-create-api-bingsearch.md)
- [PowerApps Enterprise](../articles/power-apps/powerapps-create-api-bingsearch.md)

Aggiungere l'API Bing Search all'ambiente del servizio app dell'organizzazione (tenant).

## Creare l'API nel portale di Azure

1. Nel [portale di Azure](https://portal.azure.com/) accedere con l'account aziendale. Ad esempio, accedere con *NomeUtente*@*Azienda*.com. Quando si esegue questa operazione, si accede automaticamente alla sottoscrizione della propria società.
 
2. Selezionare **Sfoglia** nella barra delle applicazioni: ![][4]

3. Nell'elenco, è possibile scorrere per trovare PowerApps o digitare *powerapps*: ![][5]

4. In **PowerApps** selezionare **Gestione API**: ![Sfogliare le API registrate][2]

2. In **Gestione API** selezionare **Aggiungi** per aggiungere la nuova API: ![Add API][3]

3. Immettere un **nome** descrittivo per l'API.

4. In **Origine** selezionare **API disponibile** per selezionare le API predefinite e selezionare **Ricerca di Bing**:

	1. Selezionare **Impostazioni - Configurare le impostazioni necessarie**.
	2. Immettere la *Chiave account*. Se non si dispone di una chiave di Ricerca di Bing, creare un’[offerta di Ricerca di Bing][1] per ottenerne una.
	3. Selezionare **OK**. 

5. Selezionare **OK** per completare i passaggi.

Al termine, verrà aggiunta una nuova API di Ricerca di Bing all'ambiente del servizio app.

## Vedere le API REST

Informazioni di riferimento sull'[API di Ricerca di Bing](../connectors/connectors-create-api-bingsearch.md).


## Riepilogo e passaggi successivi
In questo argomento, è stata aggiunta l'API di Ricerca di Bing alla PowersApps Enterprise. Successivamente, fornire agli utenti accesso all'API, in modo che sia possibile aggiungerla alle loro app:

[Aggiungere una connessione e fornire agli utenti l'accesso](powerapps-manage-api-connection-user-access.md)

<!--References-->
[1]: https://datamarket.azure.com/dataset/bing/search
[2]: ./media/powerapps-create-api-dropbox/browse-to-registered-apis.PNG
[3]: ./media/powerapps-create-api-dropbox/add-api.PNG
[4]: ./media/powerapps-create-api-dropbox/browseall.png
[5]: ./media/powerapps-create-api-dropbox/allresources.png

<!---HONumber=AcomDC_0330_2016-->