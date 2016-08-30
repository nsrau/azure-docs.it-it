
<properties
	pageTitle="Come acquistare un nome di dominio personalizzato nelle app Web di Servizio app di Azure"
	description="Informazioni su come acquistare un nome di dominio personalizzato con un'app Web in Servizio app di Azure."
	services="app-service\web"
	documentationCenter=""
	authors="rmcmurray"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="app-service-web"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/11/2016"
	ms.author="robmcm"/>

# Acquistare e configurare un nome di dominio personalizzato in Servizio app di Azure

> [AZURE.SELECTOR]
- [Acquistare un dominio per App Web](custom-dns-web-site-buydomains-web-app.md)
- [Applicazioni Web con domini esterni](web-sites-custom-domain-name.md)
- [App Web con Gestione traffico](web-sites-traffic-manager-custom-domain-name.md)
- [GoDaddy](web-sites-godaddy-custom-domain-name.md)




[AZURE.INCLUDE [websites-cloud-services-css-guided-walkthrough](../../includes/websites-cloud-services-css-guided-walkthrough.md)]

Quando si crea un'app Web, Azure la assegna a un sottodominio di azurewebsites.net. Ad esempio, se l'app Web è denominata **contoso**, l'URL corrisponderà a **contoso.azurewebsites.net**. Azure assegna anche un indirizzo IP virtuale.

Per un'app Web di produzione, è probabile che l'amministratore desideri che gli utenti vedano un nome di dominio personalizzato. Questo articolo spiega come acquistare e configurare un dominio personalizzato con le [app Web del servizio app](http://go.microsoft.com/fwlink/?LinkId=529714).

[AZURE.INCLUDE [introfooter](../../includes/custom-dns-web-site-intro-notes.md)]


## Overview

> [AZURE.NOTE] Non provare ad acquistare un dominio con una sottoscrizione a cui non è associata una carta di credito attiva, perché è possibile che la sottoscrizione venga disabilitata.

Se l'app Web non ha un nome di dominio, è possibile acquistarne uno facilmente sul [portale di Azure](https://portal.azure.com/). Durante il processo di acquisto è possibile scegliere che i record DNS WWW e di radice di dominio vengano associati automaticamente all'app Web. È inoltre possibile gestire il dominio direttamente dal portale di Azure.


Seguire questa procedura per acquistare i nomi di dominio e assegnarli all'app Web.

1. Accedere al [portale di Azure](https://portal.azure.com/) dal browser.

2. Nella scheda **App Web** fare clic sul nome dell'app Web, selezionare **Impostazioni** e quindi **Domini personalizzati e SSL**.

	![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-6.png)

3. Nel pannello **Domini personalizzati e SSL** fare clic su **Acquista domini**.

	![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-1.png)

4. Nella casella di testo del pannello **Acquista domini** immettere il nome del dominio che si vuole acquistare e premere INVIO. I domini disponibili suggeriti verranno visualizzati sotto la casella di testo. Selezionare il dominio che si vuole acquistare. È possibile scegliere di acquistare più domini in una sola volta.

  ![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-2.png)

5. Scegliere **Informazioni di contatto** e compilare il modulo di informazioni di contatto del dominio.

  ![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-3.png)

> [AZURE.NOTE] È molto importante compilare tutti i campi obbligatori con la massima correttezza possibile, in particolare l'indirizzo di posta elettronica. Se si acquista il dominio senza protezione della privacy, è possibile che venga visualizzata una richiesta di verifica dell'indirizzo di posta elettronica prima che il dominio sia attivo. In alcuni casi, i dati non corretti relativi alle informazioni di contatto causano errori nell'acquisto dei domini.

6. A questo punto è possibile scegliere di

	a) Rinnovare automaticamente il dominio ogni anno
	
	b) Fornire il consenso esplicito alla protezione della privacy, funzionalità inclusa nel prezzo GRATUITAMENTE
	
	c) Assegnare nomi host predefiniti per domini WWW e radice all'app Web corrente

  ![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-2.5.png)
  
> [AZURE.NOTE] L'opzione C configura le associazioni DNS e le associazioni del nome host automaticamente. In questo modo, è possibile accedere all'app Web usando il dominio personalizzato non appena l'acquisto è completato (solo in alcuni casi si verificano ritardi di propagazione DNS). Se l'app Web è protetta da Gestione traffico di Azure, non sarà disponibile un'opzione per assegnare il dominio radice, in quanto i record A non funzionano con Gestione traffico.
>
>È sempre possibile assegnare i domini o i sottodomini acquistati tramite un'app Web a un'altra app Web e viceversa. Per informazioni dettagliate, vedere il passaggio 8.

	
7. Fare clic su **Seleziona** nel pannello **Acquista domini**, a questo punto verranno visualizzate le informazioni di acquisto nel pannello **Conferma acquisto**. Se si accettano le condizioni legali e si fa clic su **Acquista**, l'ordine viene inviato ed è possibile monitorare il processo di acquisto in **Notifica**. L'acquisto del dominio può richiedere alcuni minuti.

  ![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-4.png)

  ![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-5.png)

8. Se un dominio è stato ordinato correttamente, è possibile gestirlo e assegnarlo all'app Web. Fare clic sui tre puntini (**...**) a destra del dominio. Questa operazione consente di **annullare l'acquisto** o **gestire il dominio**. Fare clic su **Gestisci dominio** in modo da associare il **sottodominio** all'app Web nel pannello **Gestisci dominio**. Se si desidera associare un **sottodominio** a un'altra app Web, eseguire questo passaggio dal contesto dell'app Web corrispondente. Questa operazione consente di assegnare il dominio all'endpoint di Gestione traffico (se l'app Web è protetta da Gestione traffico) selezionando il nome di Gestione traffico dal menu a discesa. Per effetto di questa operazione, il dominio o il sottodominio verranno assegnati automaticamente a tutte le app Web protette da tale endpoint di Gestione traffico.

	![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-6.png)

> [AZURE.NOTE] È possibile annullare l'acquisto entro 5 giorni per ottenere il rimborso completo. Dopo 5 giorni non sarà possibile annullare l'acquisto e verrà invece visualizzata un'opzione per l'eliminazione del dominio. Se eliminato, il dominio viene rilasciato dalla sottoscrizione senza rimborso e tornerà nuovamente disponibile.

Al termine della configurazione, il nome di dominio personalizzato sarà elencato nella sezione **Associazioni nome host** dell'app Web.

A questo punto sarà possibile immettere il nome di dominio personalizzato nel browser e verificare che il reindirizzamento all'app Web avvenga correttamente.
 
## Cosa accade al dominio personalizzato acquistato

Il dominio personalizzato acquistato nel pannello **Domini personalizzati ed SSL** è associato alla sottoscrizione di Azure. Come risorsa di Azure, questo dominio personalizzato è separato e indipendente dall'app del servizio app per cui è stato inizialmente acquistato il dominio. Ciò significa che:

- All'interno del portale di Azure è possibile usare il dominio personalizzato per più app del servizio app, non soltanto per quella per cui è stato acquistato il dominio personalizzato.
- È possibile gestire tutti i domini personalizzati acquistati nella sottoscrizione di Azure usando il pannello **Domini personalizzati ed SSL** di *qualsiasi* app del servizio app presente in tale sottoscrizione.
- È possibile assegnare qualsiasi applicazione del servizio app dalla stessa sottoscrizione di Azure a un sottodominio all'interno del dominio personalizzato.
- Se si decide di eliminare un'app del servizio app, è possibile scegliere di non eliminare il dominio personalizzato ad essa associato. In questo modo, è possibile continuare a usare tale dominio per altre app.

## Non è possibile visualizzare il dominio personalizzato acquistato

Se il dominio personalizzato è stato acquistato all'interno del pannello **Domini personalizzati ed SSL** ma non viene visualizzato in **Domini gestiti**, verificare quanto segue:

- È possibile che la creazione del dominio personalizzato non sia stata completata. Selezionare l'icona a forma di campanella nella parte superiore del portale di Azure per verificare lo stato di avanzamento.
- È possibile che per qualche motivo la creazione del dominio personalizzato non sia riuscita. Selezionare l'icona a forma di campanella nella parte superiore del portale di Azure per verificare lo stato di avanzamento.
- È possibile che la creazione del dominio personalizzato sia stata completata ma che il pannello non sia ancora stato aggiornato. Provare a riaprire il pannello **Domini personalizzati ed SSL**.
- È possibile che a un certo punto il dominio personalizzato sia stato eliminato. Verificare i log di controllo facendo clic su **Impostazioni** > **Log di controllo** nel pannello principale dell'applicazione.
- Il pannello **Domini personalizzati ed SSL** che si sta esaminando potrebbe appartenere a un'app creata in un'altra sottoscrizione di Azure. Passare a un'altra app in una sottoscrizione differente ed esaminare il relativo pannello **Domini personalizzati ed SSL**. All'interno del portale non sarà possibile visualizzare o gestire domini personalizzati creati in una sottoscrizione di Azure diversa da quella dell'app. Tuttavia, se si fa clic su **Gestione avanzata** nel pannello **Gestisci dominio** del dominio, si verrà reindirizzati al sito Web del provider di dominio, dove sarà possibile [configurare manualmente il dominio personalizzato come qualsiasi dominio personalizzato esterno](web-sites-custom-domain-name.md) per app create in un'altra sottoscrizione di Azure.

<!---HONumber=AcomDC_0817_2016-->