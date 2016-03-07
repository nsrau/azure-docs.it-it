<properties
	pageTitle="Creazione e configurazione di API gestite da Microsoft e gestite dal reparto IT in PowerApps Enterprise | Microsoft Azure"
	description="Informazioni sulle API disponibili in PowerApps e come registrarle nel portale di Azure"
	services=""
    suite="powerapps"
	documentationCenter="" 
	authors="MandiOhlinger"
	manager="dwrede"
	editor=""/>

<tags
   ms.service="powerapps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="01/21/2016"
   ms.author="guayan"/>

# Registrazione di un'API gestita da Microsoft o gestita dal reparto IT
Esistono API **gestite da Microsoft** e API **gestite dal reparto IT**. Quando si abilita PowerApps Enterprise, le API gestite da Microsoft sono disponibili automaticamente. Inoltre, vengono gestite automaticamente anche memoria, connettività, attendibilità e molto altro. Il passaggio successivo consiste nell'inserimento di tutte le impostazioni utente specifiche, ad esempio un account Twitter e una password.

Tramite le API gestite dal reparto IT, è possibile controllare e monitorare ogni aspetto, tra cui memoria, connettività, attendibilità e altro. Le API gestite dal reparto IT comprendono anche quelle in grado di connettersi a un sistema locale, ad esempio SQL Server e SharePoint Server.

Per utilizzare le API **gestite da Microsoft** o le API **gestite dal reparto IT**, è necessario "registrare" le API nel portale di Azure. Una volta registrate, è possibile utilizzare queste API nelle app. Sono disponibili le seguenti opzioni:

- Registrare un'API gestita da Microsoft o gestita dal reparto IT predefinita (in questo argomento).
- Registrare un'app Web, un'app per le API e un'app per dispositivi mobili ospitata nell'[ambiente del servizio app](powerapps-register-api-hosted-in-app-service.md).
- Registrare una delle API Swagger usando una [definizione API di Swagger 2.0](powerapps-register-existing-api-from-api-definition.md).

Questo articolo è incentrato sulla **registrazione di API predefinite gestite da Microsoft e gestite dal reparto IT**.

#### Prerequisiti per iniziare

- Iscrizione a [PowerApps Enterprise](powerapps-get-started-azure-portal.md).
- Creazione di un [ambiente di servizio app](powerapps-get-started-azure-portal.md).


## Visualizzazione delle API gestite da Microsoft disponibili
Le API **gestite da Microsoft** vengono fornite con PowerApps Enterprise e sono ospitate da Microsoft stessa. In molti scenari, le API gestite di Microsoft sono ideali per le app. Ad esempio, se l'app invia un tweet, carica un file in OneDrive o visualizza i dati da un file di Excel, le API gestite da Microsoft rappresentano una scelta ottimale.

Fra gli ulteriori vantaggi:

- Si ottengono tutte le API gestite da Microsoft disponibili per la registrazione di un'istanza personalizzata. 
- Le risorse, tra cui rete, memoria o configurazioni di sicurezza, vengono monitorate automaticamente. Ad esempio, se è necessario aggiungere ulteriore memoria per visualizzare i dati di Excel nell'app, tale aggiunta avviene automaticamente. 
- Viene creata automaticamente una relazione di trust tra l'app e l'API, come Office e Twitter. 


#### API gestite da Microsoft

API | Descrizione | Collegamento ai passaggi
--- | --- | ---
![][31] | **Dropbox**<br/><br/> È possibile ottenere, aggiornare ed eliminare elementi e molto altro. | [**Introduzione**](powerapps-create-api-dropbox.md)
![][32] | **DynamicsCRM Online**<br/><br/> È possibile ottenere, aggiornare ed eliminare elementi e molto altro. | [**Introduzione**](powerapps-create-api-crmonline.md)
![][33] | **Excel**<br/><br/> È possibile ottenere, aggiornare ed eliminare elementi e molto altro. | [**Introduzione**](powerapps-create-api-excel.md)
![][34] | **Google Drive**<br/><br/> È possibile ottenere, aggiornare ed eliminare elementi e molto altro. | [**Introduzione**](powerapps-create-api-googledrive.md)
![][35] | **Microsoft Translator**<br/><br/>Traduce il testo, rileva le lingue e molto altro. | [**Introduzione**](powerapps-create-api-microsofttranslator.md)
![][36] | **Office 365 Outlook**<br/><br/> Per la gestione della posta elettronica. | [**Introduzione**](powerapps-create-api-office365-outlook.md)
![][37] | **Utenti di Office 365**<br/><br/> Per accedere ai profili utente, i gestori, i report diretti e molto altro. | [**Introduzione**](powerapps-create-api-office365-users.md)
![][38] | **OneDrive**<br/><br/> È possibile ottenere, aggiornare ed eliminare elementi e molto altro. | [**Introduzione**](powerapps-create-api-onedrive.md)
![][39] | **Salesforce**<br/><br/> È possibile ottenere, aggiornare ed eliminare elementi e molto altro. | [**Introduzione**](powerapps-create-api-salesforce.md)
![][40] | **SharePoint Online**<br/><br/> È possibile ottenere, aggiornare ed eliminare elementi e molto altro. | [**Introduzione**](powerapps-create-api-sharepointonline.md)
![][43] | **Twitter**<br/><br/> Per inviare e cercare tweet, visualizzare follower e molto altro. | [**Introduzione**](powerapps-create-api-twitter.md)


## Visualizzazione delle API gestite dal reparto IT disponibili
Le API **gestite dal reparto IT** sono controllate e gestite dall'utente. Non vengono eseguite nell'ambiente gestito da Microsoft. In alcuni scenari, l'utilizzo di queste API nel proprio ambiente gestito dal reparto IT può soddisfare le esigenze delle app. Ad esempio, l'app utilizza l'API di Twitter ed è necessario utilizzare la chiave di Twitter della propria organizzazione (anziché la chiave di Twitter di Microsoft). In questo caso, è consigliabile configurare l'API di Twitter come un'API gestita dal reparto IT. In un altro esempio, l'app utilizza l'API Server SQL per connettersi a un database locale. In un ambiente gestito dal reparto IT, è possibile configurare una rete virtuale o utilizzare Express Route per la connessione locale. Ciò è da considerarsi in base alle proprie esigenze.

Fra gli ulteriori vantaggi:

- Le risorse, tra cui rete, memoria o configurazioni di sicurezza, vengono monitorate dall'utente. Ad esempio, se è necessario aggiungere ulteriore memoria per visualizzare i dati di Excel nell'app, è possibile controllare la quantità di memoria da aggiungere all'ambiente. 
- È l'utente a impostare la relazione di trust e a controllare la sicurezza tra le app e l'API. Ad esempio, l'utente stabilisce se l'API di Office 365 può essere gestita da Microsoft (un trust automatico) o se utilizzare l'API di Office 365 nel proprio ambiente (creazione del proprio trust). 
- **Tutte** le API gestite da Microsoft possono essere gestite anche dal reparto IT. Ad esempio, ciò è possibile per creare un'istanza personalizzata di Office 365 e avere il controllo completo su di essa. È quindi possibile utilizzare l'API di Office 365 gestita dal reparto IT e quella gestita da Microsoft nel medesimo ambiente. Tutto dipende dalle esigenze della propria app.
- In caso di connessione a sistemi locali o di utilizzo dell'API di ricerca Bing, l'utente ha il controllo di sicurezza, autenticazione, licenze e molto altro.


#### API gestite dal reparto IT
> [AZURE.NOTE] Tenere presente che **tutte** le API gestite di Microsoft possono essere gestite anche dal reparto IT. Le API seguenti sono solo gestite dal reparto IT e non possono essere gestite da Microsoft.

API | Descrizione | Collegamento ai passaggi
--- | --- | ---
![][30] | **Ricerca Bing**<br/><br/>Per incorporare i risultati delle ricerche, aggiungere funzionalità di ricerca e molto altro. | [**Introduzione**](powerapps-create-api-bingsearch.md)
![][42] | **SQL Server**<br/><br/>È possibile ottenere, aggiornare ed eliminare elementi e molto altro. | [**Introduzione**](powerapps-create-api-sqlserver.md)
![][41] | **SharePoint Server**<br/><br/>È possibile ottenere, aggiornare ed eliminare elementi e molto altro. | [**Introduzione**](powerapps-create-api-sharepointserver.md)


#### Perché registrare le istanze personalizzate

Utilizzare le API gestite da Microsoft pronte all'uso è comodo. In ogni caso, la registrazione delle istanze personalizzate come API gestite dal reparto IT presenta numerosi vantaggi. In generale, è consigliabile creare API gestite dal reparto IT quando si desidera:

- Gestire tutti gli aspetti delle API, compresi l'accesso utente, la sicurezza durante la connessione ad altri sistemi, i limiti di chiamata delle API, il monitoraggio e funzionalità avanzate come i criteri e molto altro.
- Accedere ai dati locali, poiché l'ambiente del servizio app supporta le reti virtuali.
- Configurare le API per gli utenti aziendali, che potrebbero non essere in grado di utilizzarle da soli.

Nella tabella seguente vengono confrontate le funzionalità delle API gestite da Microsoft e di quelle gestite dal reparto IT:

| Funzionalità | Gestita da Microsoft | Gestita dal reparto IT |
| ---------- | ----------------- | ------------ |
| Limiti di chiamata dell'API | Definita da Microsoft | Definita dall'utente (tramite criteri) |
| Trasferire la propria chiave durante la connessione a SaaS | Non supportato | Supportato |
| Accesso utente all'API | Abilitato per tutti gli utenti | Gestione completa al livello di gruppo e utente ADD |
| Monitoraggio API | Non supportato | Supportato |
| Criteri API | Non supportato | Supportato |
| Accesso utente alla connessione | Solo visualizzazione | Gestione completa al livello di gruppo e utente ADD |
| Gestione delle connessioni | Solo visualizzazione | Gestione completa |


## Registrazione di un'API gestita da Microsoft o gestita dal reparto IT

1. Nel [portale di Azure](https://portal.azure.com/) accedere con l'account aziendale (*nomeUtente*@*società*.com). Si accederà automaticamente alla sottoscrizione della propria società.
2. Selezionare **Sfoglia**, quindi **PowerApp** e infine **API gestite**: ![][17]
3. In Gestisci API, selezionare **Aggiungi**: ![][18]  
4. In **Aggiungi API**, immettere le proprietà dell'API:  

	- In **Nome**, immettere un nome per l'API. Tenere presente che il nome immesso viene compreso nell'URL di runtime dell'API. Il nome deve essere significativo e univoco all'interno dell'organizzazione.
	- In **Origine**, selezionare **Da API disponibili**: ![][19]
5. Selezionare **API** e quindi scegliere l'API da registrare: ![][20]
6. Selezionare l'API specifica e aggiungere tutte le proprietà configurabili.
7. Selezionare **AGGIUNGI** per completare questi passaggi.

> [AZURE.TIP] Quando si registra un'API, ciò avviene nel proprio ambiente del servizio app. Una volta effettuata tale registrazione, l'API è utilizzabile da altre app presenti nello stesso ambiente del servizio app.


## Riepilogo e passaggi successivi

In questo argomento si è visto come registrare un'istanza personalizzata delle API disponibili e fornite pronte per l'uso da PowerApps. Per altre informazioni sulle PowerApps, vedere gli articoli e le risorse correlati seguenti:


- [Configurare le proprietà dell'API](powerapps-configure-apis.md)
- [Consentire agli utenti di accedere alle API](powerapps-manage-api-connection-user-access.md)
- [Iniziare a creare le app in PowerApps](https://powerapps.microsoft.com/tutorials/)


<!--References-->

[17]: ./media/powerapps-register-from-available-apis/registered-apis-part.png
[18]: ./media/powerapps-register-from-available-apis/add-api-button.png
[19]: ./media/powerapps-register-from-available-apis/add-api-blade.png
[20]: ./media/powerapps-register-from-available-apis/add-api-select-from-marketplace-blade.png
[30]: ./media/powerapps-register-from-available-apis/bingsearch.png
[31]: ./media/powerapps-register-from-available-apis/dropbox.png
[32]: ./media/powerapps-register-from-available-apis/dynamicscrmonline.png
[33]: ./media/powerapps-register-from-available-apis/excel.png
[34]: ./media/powerapps-register-from-available-apis/googledrive.png
[35]: ./media/powerapps-register-from-available-apis/microsofttranslator.png
[36]: ./media/powerapps-register-from-available-apis/office365outlook.png
[37]: ./media/powerapps-register-from-available-apis/office365users.png
[38]: ./media/powerapps-register-from-available-apis/onedrive.png
[39]: ./media/powerapps-register-from-available-apis/salesforce.png
[40]: ./media/powerapps-register-from-available-apis/sharepointonline.png
[41]: ./media/powerapps-register-from-available-apis/sharepointserver.png
[42]: ./media/powerapps-register-from-available-apis/sqlserver.png
[43]: ./media/powerapps-register-from-available-apis/twitter.png

<!---HONumber=AcomDC_0224_2016-->