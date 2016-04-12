<properties
	pageTitle="Aggiungere l'API di OneDrive a PowerApps Enterprise | Microsoft Azure"
	description="Creare o configurare una nuova API di OneDrive nell’ambiente del servizio app dell'organizzazione"
	services=""
    suite="powerapps"
	documentationCenter="" 
	authors="rajeshramabathiran"
	manager="dwrede"
	editor=""/>

<tags
   ms.service="powerapps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="03/29/2016"
   ms.author="litran"/>

# Creare una nuova API OneDrive in PowerApps Enterprise

> [AZURE.SELECTOR]
- [App per la logica](../articles/connectors/connectors-create-api-onedrive.md)
- [PowerApps Enterprise](../articles/power-apps/powerapps-create-api-onedrive.md)

Aggiungere l'API OneDrive all'ambiente del servizio app dell'organizzazione (tenant).

## Creare l'API nel portale di Azure

1. Nel [portale di Azure](https://portal.azure.com/) accedere con l'account aziendale. Ad esempio, accedere con *NomeUtente*@*Azienda*.com. Quando si esegue questa operazione, si accede automaticamente alla sottoscrizione della propria società.
 
2. Selezionare **Sfoglia** nella barra delle applicazioni: ![][14]

3. Nell'elenco, è possibile scorrere per trovare PowerApps o digitare *powerapps*: ![][15]

4. In **PowerApps** selezionare **Gestione API**: ![Sfogliare le API registrate][1]

5. In **Gestione API** selezionare **Aggiungi** per aggiungere la nuova API: ![Add API][2]

6. Immettere un **nome** descrittivo per l'API.
	
7. In **Origine**, selezionare **API disponibili** per selezionare le API predefinite e selezionare **OneDrive**: ![selezionare l'API di OneDrive][3]

8. Selezionare **Impostazioni - Configurare le impostazioni necessarie**: ![configurare le impostazioni dell’API di OneDrive][4]

9. Immettere *Chiave app* e *Segreto app* dell'applicazione OneDrive. Se non è disponibile, vedere la sezione "Registrare un'app di OneDrive per l'utilizzo con PowerApps" in questo argomento per creare i valori chiave e segreto necessari.

	> [AZURE.IMPORTANT] Salvare l'**URL di reindirizzamento**. Questo valore potrebbe essere necessario più avanti in questo argomento.

10. Selezionare **OK** per completare i passaggi.

Al termine, verrà aggiunta una nuova API di OneDrive all'ambiente del servizio app.

## Facoltativo: Registrare un'app per OneDrive per l'utilizzo con PowerApps

Se non si dispone di un'app OneDrive esistente con i valori chiave e segreto, utilizzare i passaggi seguenti per creare l'applicazione e ottenere i valori necessari.

1. Passare alla [pagina di creazione app][5] in _Centro per sviluppatori di account Microsoft_ e accedere con il proprio _Account Microsoft_.

2. Immettere il **Nome applicazione** e selezionare **Accetto**: ![Nuova app di OneDrive][6]

3. Nella pagina delle impostazioni:

	1. Selezionare **Impostazioni API**.  
	2. Impostare l'URL di reindirizzamento sul valore corrispondente ricevuto quando è stata aggiunta la nuova API OneDrive nel portale di Azure (in questo argomento).  
	3. Selezionare **Salva**.  

	![Impostazioni dell’app API di OneDrive][7]

Viene creata una nuova app OneDrive. È possibile utilizzare questa app nella configurazione dell'API di OneDrive nel portale di Azure.

## Vedere le API REST

Informazioni di riferimento sull'[API REST Onedrive](../connectors/connectors-create-api-onedrive.md).

## Riepilogo e passaggi successivi
In questo argomento, è stata aggiunta l'API di OneDrive alla PowersApps Enterprise. Successivamente, fornire agli utenti accesso all'API, in modo che sia possibile aggiungerla alle loro app:

[Aggiungere una connessione e fornire agli utenti l'accesso](powerapps-manage-api-connection-user-access.md)

<!--References-->
[1]: ./media/powerapps-create-api-onedrive/browse-to-registered-apis.PNG
[2]: ./media/powerapps-create-api-onedrive/add-api.PNG
[3]: ./media/powerapps-create-api-onedrive/select-onedrive-api.PNG
[4]: ./media/powerapps-create-api-onedrive/configure-onedrive-api.PNG
[5]: https://account.live.com/developers/applications/create
[6]: ./media/powerapps-create-api-onedrive/onedrive-new-app.PNG
[7]: ./media/powerapps-create-api-onedrive/onedrive-app-api-settings.PNG
[14]: ./media/powerapps-create-api-onedrive/browseall.png
[15]: ./media/powerapps-create-api-onedrive/allresources.png

<!---HONumber=AcomDC_0330_2016-->