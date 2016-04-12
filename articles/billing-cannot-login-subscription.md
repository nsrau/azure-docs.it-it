<properties
	pageTitle="Non è possibile accedere per gestire la sottoscrizione di Azure | Microsoft Azure"
	description="Descrive le informazioni di risoluzione dei problemi per alcuni dei problemi di accesso della sottoscrizione di Azure"
	services="billing"
	documentationCenter=""
	authors="genlin"
	manager="jarrettr"
	editor="na"
	tags="billing"
	/>

<tags
	ms.service="billing"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/30/2016"
	ms.author="genli"/>

# Non è possibile accedere per gestire la sottoscrizione di Azure

Questo articolo consentirà di risolvere alcune cause comuni dei problemi di accesso.

## A quale portale si sta tentando di accedere?

Un amministratore account può accedere al [Centro Account](https://account.windowsazure.com/) solo se gli amministratori del servizio (SA) e i coamministratori (CA) hanno accesso al [portale di Azure](https://portal.azure.com) o al [portale di Azure classico](https://manage.windowsazure.com/).

Per altre informazioni sui ruoli di amministratore di Azure, vedere [Come aggiungere o modificare i ruoli di amministratore di Azure](billing-add-change-azure-subscription-administrator.md).

## La sottoscrizione è associata a un account Microsoft o a un account aziendale?

L’account Microsoft è l'indirizzo di posta elettronica che si utilizza, insieme alla password, per accedere a qualsiasi programma o servizio Windows Live, ad esempio Outlook, Hotmail, MSN o OneDrive. È possibile impostare un account Microsoft usando qualsiasi indirizzo di posta elettronica appartenente all’utente, compresa la posta elettronica aziendale. Vedere [www.microsoft.com/account](http://www.microsoft.com/account) per ulteriori dettagli.

Se l'account è associato a un account aziendale, selezionare l'opzione di accesso corretta come illustrato di seguito. Per altre informazioni sull'uso dell’account aziendale, vedere [Iscrizione ad Azure come organizzazione](./active-directory/sign-up-organization.md):

![pagina di accesso](./media/billing-cannot-login-subscription/signin.png)

## Co-amministratore: si sta utilizzando il tipo di account corretto per gestire altri account?

- Se si è connessi con un account Microsoft, è possibile aggiungere solo altri account Microsoft come co-amministratori. Si tratta di una richiesta di sicurezza per impedire che account non dell'organizzazione scoprano se determinati account, (ad esempio janedoe@contoso.com), sono account validi.
- Se si è connessi con un account dell'organizzazione, è possibile aggiungere altri account dell'organizzazione nell'organizzazione come co-amministratori. Ad esempio, abby@contoso.com può aggiungere bob@contoso.com come amministratore del servizio o co-amministratore, ma non può aggiungere john@notcontoso.com. Gli utenti connessi con account aziendali possono anche aggiungere utenti di account Microsoft come amministratori del servizio o co-amministratori.

Ora che è possibile accedere ad Azure con un account aziendale, ecco le modifiche ai requisiti di account di amministratore del servizio (SA) e di co-amministratore (CA):

| Metodo di accesso| Aggiungere un account Microsoft come co-amministratore o amministratore del servizio? |Aggiungere un account dell'organizzazione nella stessa organizzazione come co-amministratore o amministratore del servizio? |Aggiungere un account dell'organizzazione in un'altra organizzazione come co-amministratore o amministratore del servizio?
| ------------- | ------------- |---------------|---------------|
|Account Microsoft |Sì|No|No|
|Account dell'organizzazione|Sì|Sì|No|

## Provare a eliminare cache/cookie, usare la modalità InPrivate Browsing di Internet Explorer e usare anche un browser diverso

Se è necessaria ulteriore assistenza in qualsiasi punto in questo articolo, è possibile contattare gli esperti di Azure su [MSDN Azure e i forum di overflow dello stack](https://azure.microsoft.com/support/forums/). In alternativa, è anche possibile archiviare un evento imprevisto di supporto tecnico di Azure. Andare al [sito di supporto di Azure](https://azure.microsoft.com/support/options/) e fare clic su Ottenere supporto. Per informazioni sull'uso del Supporto tecnico di Azure, leggere le [Domande frequenti sul supporto tecnico di Azure](https://azure.microsoft.com/support/faq/).

<!---HONumber=AcomDC_0330_2016-->