<properties
	pageTitle="Come aggiungere o modificare i ruoli di amministratore di Azure | Microsoft Azure"
	description="Descrive come aggiungere o modificare il co-amministratore di Azure, l’amministratore del servizio e l’amministratore dell’account"
	services="billing"
	documentationCenter=""
	authors="genlin"
	manager="msmbaldwin"
	editor="meerak"
	/>

<tags
	ms.service="billing"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/05/2015"
	ms.author="genli"/>

# Come aggiungere o modificare i ruoli di amministratore di Azure

## Spiegazione dei ruoli di amministratore

Esistono tre tipi di ruoli di amministratore in Microsoft Azure:

| Ruolo amministrativo | Limite | Descrizione
| ------------- | ------------- |---------------|
|Amministratore dell'account | 1 per ogni account di Azure |Indica la persona che si è iscritta o ha acquistato le sottoscrizioni di Azure ed è autorizzata ad accedere al [Centro account](https://account.windowsazure.com/Home/Index) ed eseguire diverse attività di gestione. Queste includono la possibilità di creare sottoscrizioni, annullare sottoscrizioni, modificare la fatturazione per una sottoscrizione e modificare l'amministratore del servizio.
| Amministratore del servizio | 1 per ogni sottoscrizione di Azure |Indica una persona autorizzata a gestire i servizi nel [portale di Azure](https://manage.windowsazure.com/). Per impostazione predefinita, per una nuova sottoscrizione, l'amministratore dell'account è anche amministratore del servizio.|
|Coamministratore|200 per ogni sottoscrizione|Questa persona dispone degli stessi privilegi di accesso dell’amministratore del servizio, ma non può modificare l'associazione di sottoscrizioni alle directory di Azure.|

**Nota** Il controllo degli accessi in base al ruolo di Azure Active Directory consente agli utenti di essere aggiunti a più ruoli. Per altre informazioni, vedere [Controllo degli accessi in base al ruolo di Azure Active Directory](./active-directory/role-based-access-control-configure.md).
## Come aggiungere un coamministratore per una sottoscrizione
1. Accedere al [portale di Azure classico](https://manage.windowsazure.com/).
2. Nel pannello di navigazione, selezionare **Impostazioni**> **Amministratori**> **Aggiungi**. </br>![addcodmin](./media/billing-add-change-azure-subscription-administrator/addcoadmin.png)
3. Digitare l'indirizzo di posta elettronica della persona che si vuole aggiungere come coamministratore e quindi selezionare la sottoscrizione a cui si desidera che il coamministratore abbia accesso.</br> ![addcoadmin2](./media/billing-add-change-azure-subscription-administrator/addcoadmin2.png)</br>

L'indirizzo di posta elettronica seguente può essere aggiunto come co-amministratore:

* **Account Microsoft** (precedentemente noto come ID Windows Live) </br> È possibile usare un account Microsoft per accedere a tutti i prodotti e servizi cloud Microsoft destinati all'utente, quali Outlook (Hotmail), Skype (MSN), OneDrive, Windows Phone e Xbox LIVE.
* **Account dell'organizzazione**</br> Un account dell'organizzazione è un account che viene creato in Azure Active Directory. L'indirizzo dell'account dell'organizzazione è simile al seguente: utente@<your domain>.onmicrosoft.com

**Nota**

 * Ogni sottoscrizione è associata a un'istanza di Azure Active Directory, ossia la directory predefinita. Per trovare la directory predefinita associata alla sottoscrizione, accedere al [portale di Azure classico](https://manage.windowsazure.com/) e selezionare **Impostazioni** > **Sottoscrizioni**. Verificare l'ID sottoscrizione per trovare la directory predefinita.

 * Se si è connessi con un account Microsoft, è possibile aggiungere come coamministratori solo altri account Microsoft o utenti nella directory predefinita.
 * Se si è connessi con un account dell'organizzazione, è possibile aggiungere come coamministratori altri account dell'organizzazione. Ad esempio, abby@contoso.com può aggiungere bob@contoso.com come amministratore del servizio o coamministratore, ma non può aggiungere john@notcontoso.com a meno che john@noncontoso.com non sia l'utente nella directory predefinita. Gli utenti connessi con account dell'organizzazione possono continuare ad aggiungere utenti di account Microsoft come amministratori del servizio o coamministratori.
 * Ora che è possibile accedere ad Azure con un account aziendale, ecco le modifiche ai requisiti di account di amministratore del servizio e di co-amministratore:

| Metodo di accesso| Aggiungere un account Microsoft o utenti in una directory predefinita come coamministratori o amministratori del servizio? |Aggiungere un account aziendale nella stessa organizzazione come coamministratore o amministratore del servizio? |Aggiungere un account aziendale in un'organizzazione diversa come coamministratore o amministratore del servizio?
| ------------- | ------------- |---------------|---------------|
|Account Microsoft |Sì|No|No|
|Account dell'organizzazione|Sì|Sì|No|

## Come modificare l'amministratore del servizio per una sottoscrizione
Solo l'amministratore dell'account può modificare l'amministratore del servizio per una sottoscrizione.

1. Accedere al [portale di gestione dell'account](https://account.windowsazure.com/subscriptions) mediante l'amministratore dell'account.
2. Selezionare la sottoscrizione da modificare.
3. Sul lato destro, fare clic sui dettagli di **Modifica sottoscrizione**. </br> ![editsub](./media/billing-add-change-azure-subscription-administrator/editsub.png)

4. Nel casella **AMMINISTRATORE DEL SERVIZIO** immettere l'indirizzo di posta elettronica del nuovo amministratore del servizio. ![changeSA](./media/billing-add-change-azure-subscription-administrator/changeSA.png)

## Come modificare l'amministratore dell'account

Per trasferire la proprietà dell'account Azure a un altro account, vedere [Trasferimento di una sottoscrizione di Azure](../billing-subscription-transfer.md).

<!---HONumber=AcomDC_0107_2016-->