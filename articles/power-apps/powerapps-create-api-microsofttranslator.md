<properties
	pageTitle="Aggiungere l'API di Microsoft Translator a PowerApps Enterprise | Microsoft Azure"
	description="Creare o configurare una nuova API di Microsoft Translator nell’ambiente del servizio app dell'organizzazione"
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

# Creare una nuova API Microsoft Translator in PowerApps Enterprise

> [AZURE.SELECTOR]
- [App per la logica](../articles/connectors/create-api-microsofttranslator.md)
- [PowerApps Enterprise](../articles/power-apps/powerapps-create-api-microsofttranslator.md)

Aggiungere l'API Microsoft Translator API nell'ambiente del servizio app dell'organizzazione (tenant).

## Creare l'API nel portale di Azure

1. Nel [portale di Azure](https://portal.azure.com/) accedere con l'account aziendale. Ad esempio, accedere con *NomeUtente*@*Azienda*.com. Quando si esegue questa operazione, si accede automaticamente alla sottoscrizione della propria società.
 
2. Selezionare **Sfoglia** nella barra delle applicazioni: ![][7]

3. Nell'elenco, è possibile scorrere per trovare PowerApps o digitare *powerapps*: ![][8]

4. In **PowerApps** selezionare **Gestione API**: ![Sfogliare le API registrate][1]

5. In **Gestione API** selezionare **Aggiungi** per aggiungere la nuova API: ![Add API][2]

6. Immettere un **nome** descrittivo per l'API.
	
7. In **Origine** selezionare **API disponibili** per selezionare le API predefinite e selezionare **Microsoft Translator **: ![selezionare l’API di Microsoft Translator][3]

8. Selezionare **Impostazioni - Configurare le impostazioni necessarie**: ![configurare le impostazioni dell’API di Microsoft Translator][4]

9. Immettere *Id client* e *Segreto client* dell'applicazione Microsoft Translator. Se non è disponibile, vedere la sezione "Registrare un'app di Microsoft Translator per l'utilizzo con PowerApps" in questo argomento per creare i valori ID e segreto necessari.

9. Selezionare **OK** per completare i passaggi.

Al termine, verrà aggiunta una nuova API di Microsoft Translator all'ambiente del servizio app.


## Facoltativo: Registrare un'app per Microsoft Translator per l'utilizzo con PowerApps

Se non si dispone di un'app Microsoft Translator esistente con i valori ID e segreto, utilizzare i passaggi seguenti per creare l'applicazione e ottenere i valori necessari.


1. Passare alla [Pagina per gli sviluppatori di Azure Data Market][5] e accedere con il proprio account Microsoft. 

2. Selezionare **Registrazione**.

3. In **Registrare l'applicazione**:

	1. Immettere un valore per **ID client**.  
	2. Immettere il **nome** dell'applicazione.  
	3. Immettere un valore fittizio per l'**URL di reindirizzamento**, Ad esempio, immettere: **https://contosoredirecturl*.
4. Immettere una **descrizione**.  
	5. Selezionare **Create**.  

	![Registrare l'applicazione][6]

Viene creata una nuova applicazione Microsoft Translator. È possibile utilizzare questa app nella configurazione dell'API di Microsoft Translator nel portale di Azure.

## Vedere le API REST

Informazioni di riferimento sull'[API REST Microsoft Translator](../connectors/create-api-microsofttranslator.md).

## Riepilogo e passaggi successivi
In questo argomento, è stata aggiunta l'API di Microsoft Translator alla PowersApps Enterprise. Successivamente, fornire agli utenti accesso all'API, in modo che sia possibile aggiungerla alle loro app:

[Aggiungere una connessione e fornire agli utenti l'accesso](powerapps-manage-api-connection-user-access.md)


<!--References-->
[1]: ./media/powerapps-create-api-microsofttranslator/browse-to-registered-apis.PNG
[2]: ./media/powerapps-create-api-microsofttranslator/add-api.PNG
[3]: ./media/powerapps-create-api-microsofttranslator/select-microsofttranslator-api.PNG
[4]: ./media/powerapps-create-api-microsofttranslator/configure-microsofttranslator-api.PNG
[5]: https://datamarket.azure.com/developer/applications/
[6]: ./media/powerapps-create-api-microsofttranslator/register-your-application.PNG
[7]: ./media/powerapps-create-api-microsofttranslator/browseall.png
[8]: ./media/powerapps-create-api-microsofttranslator/allresources.png

<!---HONumber=AcomDC_0309_2016-->