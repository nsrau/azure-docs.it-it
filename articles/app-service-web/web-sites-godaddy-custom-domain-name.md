<properties
	pageTitle="Configurazione di un nome di dominio personalizzato nel servizio app di Azure (GoDaddy)"
	description="Informazioni su come usare un nome di dominio di GoDaddy con App Web di Azure"
	services="app-service\web"
	documentationCenter=""
	authors="MikeWasson"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="app-service-web"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/12/2015"
	ms.author="mwasson"/>

# Configurare un nome di dominio personalizzato nel servizio app di Azure (acquistato direttamente da GoDaddy)

[AZURE.INCLUDE [web-selector](../../includes/websites-custom-domain-selector.md)]


[AZURE.INCLUDE [websites-cloud-services-css-guided-walkthrough](../../includes/websites-cloud-services-css-guided-walkthrough.md)]

> [AZURE.NOTE]Se si è acquistato un dominio tramite App Web del servizio app di Azure, fare riferimento all'ultimo passaggio dell'articolo <a href="/documentation/articles/custom-dns-web-site-buydomains-web-app" title="Web Apps" class="current">Acquistare un dominio per app Web</a>.

[AZURE.INCLUDE [intro](../../includes/custom-dns-web-site-intro.md)]

Questo articolo fornisce istruzioni generiche sull'uso di un nome di dominio personalizzato acquistato direttamente da [GoDaddy](https://godaddy.com) con [App Web del servizio app](http://go.microsoft.com/fwlink/?LinkId=529714).

[AZURE.INCLUDE [introfooter](../../includes/custom-dns-web-site-intro-notes.md)]

<a name="understanding-records"></a>
##Informazioni sui record DNS

[AZURE.INCLUDE [understandingdns](../../includes/custom-dns-web-site-understanding-dns-raw.md)]


<a name="bkmk_configurecname"></a>
## Aggiungere un record DNS per il dominio personalizzato

Per associare il dominio personalizzato a un'app Web nel servizio app, è necessario aggiungere nella tabella DNS una nuova voce per il dominio personalizzato usando gli strumenti forniti da GoDaddy. Per individuare gli strumenti DNS per GoDaddy.com, attenersi alla procedura seguente

1. Accedere al proprio account presso GoDaddy.com e selezionare **Mio account**, quindi **Gestisci i miei domini**. Infine, selezionare il menu a discesa relativo al nome di dominio da usare con l'app Web di Azure e selezionare **Gestisci DNS**.

	![pagina del domino personalizzato per GoDaddy](./media/web-sites-godaddy-custom-domain-name/godaddy-customdomain.png)

2. Nella pagina **Dettagli dominio** selezionare la scheda **DNS Zone File**. In questa sezione è possibile aggiungere e modificare i record DNS per il nome di dominio.

	![Scheda DNS Zone File](./media/web-sites-godaddy-custom-domain-name/godaddy-zonetab.png)

	Selezionare **Aggiungi record** a un record esistente.

	Per **modificare** un record esistente, selezionare l'icona della penna e del foglio accanto al record.

	> [AZURE.NOTE]Prima di aggiungere nuovi record, tenere presente che GoDaddy ha già creato record DNS per i sottodomini più diffusi (denominati **Host** nell'editor,) quali **posta elettronica**, **file**, **posta** e altri. Se il nome che si desidera utilizzare è già presente, modificare il record esistente anziché crearne uno nuovo.

4. Per aggiungere un record, è necessario innanzitutto selezionare un tipo di record.

	![selezionare il tipo di record](./media/web-sites-godaddy-custom-domain-name/godaddy-selectrecordtype.png)

	Quindi, è necessario specificare l'**Host** (il dominio o il sottodominio personalizzato) e l'opzione **Punta a**.

	![aggiungere un record di zona](./media/web-sites-godaddy-custom-domain-name/godaddy-addzonerecord.png)

	* Quando si aggiunge un **record A (host)**, è necessario impostare il campo **Host** su **@** (che rappresenta il nome di dominio radice, ad esempio **contoso.com**), su * (un carattere jolly per la corrispondenza di più sottodomini) o sul sottodominio da usare (ad esempio **www**). È necessario impostare il campo **Punta a** sull'indirizzo IP dell'app Web di Azure.

		> [AZURE.NOTE]Quando si usa un record A (host), è anche necessario aggiungere un record CNAME con la configurazione seguente:
		>
		> * Un valore **Host** di **awverify** che **Punta a** un valore di **awverify.&lt;nomeappweb&gt;.azurewebsites.net**.
		>
		> Questo nome di record CNAME viene utilizzato da Azure per convalidare la proprietà del dominio descritto dal record A.

	* Quando si aggiunge un **record CNAME (alias)**, è necessario impostare il campo **Host** sul sottodominio da usare, ad esempio **www**. È necessario impostare il campo **Punta a** sul nome di dominio **.azurewebsites.net** dell'app Web di Azure. ad esempio **contoso.azurwebsites.net**.


5. Dopo avere completato l'aggiunta o la modifica dei record, fare clic su **Fine** per salvare le modifiche.

<a name="enabledomain"></a>
## Abilitare il nome del dominio nell'app Web

[AZURE.INCLUDE [modes](../../includes/custom-dns-web-site-enable-on-web-site.md)]

>[AZURE.NOTE]Per iniziare a usare il servizio app di Azure prima di registrarsi per ottenere un account Azure, andare a [Prova il servizio app](http://go.microsoft.com/fwlink/?LinkId=523751), dove è possibile creare un'app Web iniziale temporanea nel servizio app. Non è necessario fornire una carta di credito né impegnarsi in alcun modo.

## Modifiche apportate
* Per una guida relativa al passaggio da Siti Web al servizio app, vedere [Servizio app di Azure e impatto sui servizi di Azure esistenti](http://go.microsoft.com/fwlink/?LinkId=529714)
* Per una guida relativa al passaggio dal portale precedente al nuovo portale, vedere [Informazioni di riferimento per l'esplorazione del portale di anteprima](http://go.microsoft.com/fwlink/?LinkId=529715)
 

<!---HONumber=August15_HO6-->