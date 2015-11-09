<properties
	pageTitle="Come aggiungere o modificare il co-amministratore di Azure, l’amministratore del servizio e l’amministratore dell’account | Microsoft Azure"
	description="Descrive come aggiungere o modificare il co-amministratore di Azure, l’amministratore del servizio e l’amministratore dell’account"
	services="billing"
	documentationCenter=""
	authors="genlin"
	manager="jarrettr"
	editor="meerak"
	tags="billing"
	/>

<tags
	ms.service="billing"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/20/2015"
	ms.author="genli"/>

# Come aggiungere o modificare il co-amministratore di Azure, l’amministratore del servizio e l’amministratore dell’account
## Ruoli dell’amministratore

Esistono tre tipi di ruoli di amministratore in Microsoft Azure:

| Ruolo amministrativo | Limite | Descrizione
| ------------- | ------------- |---------------|
|Amministratore dell’account: | Uno per ogni account di Azure. |Questa è la persona che si è iscritta o ha acquistato le sottoscrizioni di Azure ed è autorizzata ad accedere al centro Account ed eseguire diverse attività di gestione. Queste includono la possibilità di creare sottoscrizioni, annullare sottoscrizioni, modificare la fatturazione per una sottoscrizione e modificare l'amministratore del servizio.
| Amministratore del servizio | 1 per ogni sottoscrizione di Azure |Questa persona è autorizzata ad accedere al portale di gestione di Azure per le sottoscrizioni nell’account a loro assegnato. Per impostazione predefinita, per una nuova sottoscrizione, l'amministratore dell'Account è anche l'amministratore del servizio.|
|Co-amministratore|200 per ogni sottoscrizione (oltre all’amministratore del servizio)|Questa persona dispone degli stessi privilegi di accesso dell’amministratore del servizio, ma non può modificare l'associazione di sottoscrizioni alle directory di Azure.|

## Aggiungere un co-amministratore per una sottoscrizione
1. Accedere al [portale di gestione di Azure](https://manage.windowsazure.com/).
2. Nel pannello di navigazione, selezionare **Impostazioni**> **Amministratori**> **Aggiungi**. </br>![addcodmin](./media/billing-add-change-azure-subscription-administrator/addcoadmin.png)
3. Digitare l'indirizzo di posta elettronica della persona che si desidera aggiungere come co-amministratore e quindi selezionare la sottoscrizione a cui si desidera che il co-amministratore abbia accesso.</br> ![addcoadmin2](./media/billing-add-change-azure-subscription-administrator/addcoadmin2.png)</br>

L'indirizzo di posta elettronica seguente può essere aggiunto come co-amministratore:

* **Account Microsoft** (precedentemente ID Windows Live) </br> è possibile utilizzare un Account Microsoft per accedere a tutti i prodotti e ai servizi cloud di Microsoft destinati all'utente, quali Outlook (Hotmail), Skype (MSN), OneDrive, Windows Phone e Xbox LIVE.
* **Account aziendale**</br> un account aziendale è un account che viene creato in Azure Active Directory. L'indirizzo dell’account aziendale è simile al seguente: utente @<your domain>. onmicrosoft.com

**Nota** * Se si è connessi con un account Microsoft, è possibile aggiungere solo altri account Microsoft come co-amministratore. Si tratta di una questione di sicurezza per impedire agli account non aziendali di scoprire se determinati account (ad esempio janedoe@contoso.com) sono account validi. * Se si è connessi tramite un account aziendale, è possibile aggiungere altri account aziendali nell'organizzazione come co-amministratore. Ad esempio, abby@contoso.com può aggiungere bob@contoso.com come amministratore del servizio o co-amministratore, ma non può aggiungere john@notcontoso.com. Gli che hanno effettuato l’accesso con account aziendali possono continuare ad aggiungere utenti con Account Microsoft come amministratore del servizio o co-amministratore. * Ora che è possibile accedere ad Azure con un account aziendale, ecco le modifiche ai requisiti di account di amministratore del servizio e di co-amministratore:

| Metodo di accesso| Aggiungere un account Microsoft come co-amministratore o amministratore del servizio? |Aggiungere un account dell'organizzazione nella stessa organizzazione come co-amministratore o amministratore del servizio? |Aggiungere un account dell'organizzazione in un'altra organizzazione come co-amministratore o amministratore del servizio?
| ------------- | ------------- |---------------|---------------|
|Account Microsoft |Sì|No|No|
|Account dell'organizzazione|Sì|Sì|No|

## Modificare l'amministratore del servizio per una sottoscrizione
Solo l'amministratore dell'account può modificare l'amministratore del servizio per una sottoscrizione.

1. Accedere al [portale di gestione dell’account](https://account.windowsazure.com/subscriptions) mediante l’amministratore dell’account.
2. Selezionare la sottoscrizione da modificare.
3. Sul lato destro, fare clic sui dettagli di **Modifica sottoscrizione**. </br> ![editsub](./media/billing-add-change-azure-subscription-administrator/editsub.png)

4. Nel casella **AMMINISTRATORE DEL SERVIZIO** immettere l'indirizzo di posta elettronica dell'amministratore del nuovo servizio. ![changeSA](./media/billing-add-change-azure-subscription-administrator/changeSA.png)

## Modificare l’Amministratore dell’account (trasferire la proprietà dell’account di Azure a un altro account)

Per trasferire la proprietà di Azure a un altro account. Vedere [Trasferimento di una sottoscrizione di Azure](../billing-subscription-transfer.md).

<!---HONumber=Nov15_HO1-->