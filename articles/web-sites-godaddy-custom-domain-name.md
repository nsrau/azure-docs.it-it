<properties 
	pageTitle="Configurazione di un nome di dominio personalizzato nel servizio app di Azure (GoDaddy)" 
	description="Informazioni su come usare un nome di dominio di GoDaddy con le app Web di Azure" 
	services="app-service\web" 
	documentationCenter="" 
	authors="wpickett" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-services-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/24/2015" 
	ms.author="wpickett"/>

#Configurazione di un nome di dominio personalizzato nel servizio app di Azure (GoDaddy)

[AZURE.INCLUDE [web-selector](../includes/websites-custom-domain-selector.md)]


[AZURE.INCLUDE [websites-cloud-services-css-guided-walkthrough](../includes/websites-cloud-services-css-guided-walkthrough.md)]

[AZURE.INCLUDE [intro](../includes/custom-dns-web-site-intro.md)]

In questo articolo vengono fornite istruzioni generiche sull'utilizzo di un nome di dominio personalizzato acquistato da [Go Daddy](https://godaddy.com) con il [servizio app per app Web](http://go.microsoft.com/fwlink/?LinkId=529714).

[AZURE.INCLUDE [introfooter](../includes/custom-dns-web-site-intro-notes.md)]

Contenuto dell'articolo:

-   [Informazioni sui record DNS](#understanding-records)
-   [Aggiunta di un record DNS per il dominio personalizzato](#bkmk_configurecname)
-   [Abilitazione del dominio nel Web](#enabledomain)

<h2><a name="understanding-records"></a>Informazioni sui record DNS</h2>

[AZURE.INCLUDE [understandingdns](../includes/custom-dns-web-site-understanding-dns-raw.md)]


<h2><a name="bkmk_configurecname"></a>Aggiunta di un record DNS per il dominio personalizzato</h2>

Per associare il dominio personalizzato a un'app Web nel servizio app, è necessario aggiungere nella tabella DNS una nuova voce per il dominio personalizzato usando gli strumenti forniti da GoDaddy. Per individuare gli strumenti DNS per GoDaddy.com, attenersi alla procedura seguente

1. Accedere al proprio account in GoDaddy.com e selezionare **Account personale**, quindi **Gestisci domini personali**. Infine, selezionare il menu a discesa relativo al nome di dominio da usare con l'app Web di Azure e selezionare **Gestisci DNS**.

	![custom domain page for GoDaddy](./media/web-sites-custom-domain-name/godaddy-customdomain.png)

2. Nella pagina **Dettagli dominio**, selezionare la scheda **File zona DNS**. In questa sezione è possibile aggiungere e modificare i record DNS per il nome di dominio. 

	![DNS Zone File tab](./media/web-sites-custom-domain-name/godaddy-zonetab.png)

	Selezionare **Aggiungi record** a un record esistente.

	Per **modificare** un record esistente, selezionare l'icona a forma di foglio e penna accanto al record.

	> [AZURE.NOTE] Prima di aggiungere nuovi record, tenere presente che GoDaddy ha già creato record DNS per i sottodomini più diffusi (denominati **Host** nell'editor,) come ad esempio, **e-mail**, **file**, **posta** e altri. Se il nome che si desidera usare è già presente, modificare il record esistente anziché crearne uno nuovo.

4. Per aggiungere un record, è necessario innanzitutto selezionare un tipo di record.

	![select record type](./media/web-sites-custom-domain-name/godaddy-selectrecordtype.png)

	Quindi, è necessario specificare l'**host** (il dominio o sottodominio personalizzato) e a cosa **punta**.

	![add zone record](./media/web-sites-custom-domain-name/godaddy-addzonerecord.png)

	* Quando si aggiunge un **record A (host)**, è necessario impostare il campo **Host** su **@** (rappresenta il nome di dominio radice, come ad esempio, **contoso.com**,) * (un carattere jolly corrispondente a più sottodomini) o il sottodominio da utilizzare (ad esempio, **www**.) È necessario impostare il campo **Punta a** sull'indirizzo IP dell'app Web di Azure.
	
		> [AZURE.NOTE] Quando si usa un record A (host), è anche necessario aggiungere un record CNAME con la configurazione seguente:
		> 
		> * Un valore **Host** di **awverify** che **punta a** un valore di **awverify.&lt;yourwebappname&gt;.azurewebsites.net**.
		> 
		> Questo nome di record CNAME viene usato da Azure per convalidare la proprietà del dominio descritto dal record A.

	* Quando si aggiunge un **record CNAME (alias)**, è necessario impostare il campo **Host** sul sottodominio da usare. Ad esempio, **www**. È necessario impostare il campo **Punta a** sul nome di dominio **.azurewebsites.net** dell'app Web di Azure. Ad esempio, **contoso.azurwebsites.net**.


5. Dopo avere completato l'aggiunta o la modifica dei record, fare clic su **Fine** per salvare le modifiche.

<h2><a name="enabledomain"></a>Abilitazione del nome del dominio nell'app Web</h2>

[AZURE.INCLUDE [modalità](../includes/custom-dns-web-site-enable-on-web-site.md)]

>[AZURE.NOTE] Per iniziare a usare il servizio app di Azure prima di registrare un account di Azure, andare a [Prova il servizio app](http://go.microsoft.com/fwlink/?LinkId=523751) dove è possibile creare immediatamente un'app Web di base e temporanea nel servizio app. Non è necessario fornire una carta di credito né impegnarsi in alcun modo.

## Modifiche apportate
* Per una guida relativa al passaggio da Siti Web al servizio app, vedere: [Il servizio app di Azure e il suo impatto sui servizi di Azure esistenti](http://go.microsoft.com/fwlink/?LinkId=529714)
* Per una guida relativa al passaggio dal vecchio al nuovo portale, vedere: [Riferimenti per esplorare il portale di anteprima](http://go.microsoft.com/fwlink/?LinkId=529715)

<!--HONumber=49-->