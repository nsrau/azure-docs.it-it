<properties
	pageTitle="Informazioni su PowerApps Enterprise e attività iniziali correlate | Microsoft Azure"
	description="Introduzione a PowerApps Enterprise e creazione dell'ambiente del servizio app"
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
   ms.date="11/29/2015"
   ms.author="litran"/>

# Informazioni su Microsoft PowerApps Enterprise

Microsoft PowerApps Enterprise è un nuovo servizio di Microsoft Azure, che estende la possibilità di creare app per dispositivi mobili agli utenti aziendali della società e consente agli amministratori IT di gestire tali app in modo solido e sicuro.

Mediante un'interfaccia simile a Office, con le barre multifunzione e le formule di Excel, gli utenti aziendali possono creare app che:

- Visualizzano i dati mediante grafici a linee, a torta e a barre, proprio come in Excel.
- Creano interfacce utente che includono i pulsanti, l'inserimento di testo e la formattazione di una data.
- Aggiungono controlli a scelta multipla, tra cui caselle di riepilogo, elenchi a discesa e pulsanti di opzione.
- Caricano immagini, scattano foto e riproducono file audio e video.
- Usano sistemi 'back-end' come Excel e SQL Server per visualizzare e aggiornare le informazioni.
- Aggiungono connettori del servizio app preesistenti alle app che si connettono ai programmi PaaS, ad esempio Twitter e SharePoint.

Gli amministratori IT possono svolgere attività di gestione delle app create dagli utenti aziendali nella propria società, tra cui:

- Gestione delle app e dell'accesso utente a tali app.
- Creazione di API e connessioni a origini dati diverse. 
- Gestione dell'accesso utente per le API e le connessioni a tali origini dati. 

## Come iniziare?

Determinare innanzitutto se è necessario creare un nuovo tenant di Azure Active Directory (Azure AD). Se si dispone già di un tenant di Active Directory, abilitare semplicemente PowerApps Enterprise nel portale di Azure, aggiungere le API e le connessioni e iniziare le attività di gestione (iniziare dal *passaggio 2* in questo argomento).

Se non si dispone di un tenant di Active Directory, creare un nuovo tenant, abilitare PowerApps Enterprise nel portale di Azure, aggiungere le API e le connessioni e iniziare le attività di gestione.

Questo argomento elenca i passaggi specifici.

## Passaggio 1: Creare un nuovo tenant di Azure AD o usarne uno esistente

Per iniziare a usare PowerApps Enterprise, è necessario un tenant di Azure Active Directory (Azure AD). Un tenant è un'istanza dedicata del servizio Azure AD.

Quando l'organizzazione o la società si iscrive a un servizio cloud di Microsoft Azure, ad esempio Microsoft Intune o Office 365, riceve automaticamente un tenant di Active Directory e ne diventa proprietaria. Ogni tenant di Active Directory è distinto e separato dagli altri tenant di Azure AD.

Per determinare se si dispone già di un tenant o per crearne uno nuovo, usare i passaggi seguenti.

#### L'utente dispone di una sottoscrizione di Office 365
Se si dispone di una sottoscrizione di Office 365 esistente (o di Microsoft Dynamic CRM Online, Enterprise Mobility Suite o altri servizi Microsoft), si dispone anche di una sottoscrizione gratuita ad Azure Active Directory. Per creare e gestire account di gruppi e utenti, è possibile usare Azure AD. Se non è possibile accedere al portale di Azure, può essere necessario attivare la sottoscrizione. A tale scopo, passare al [portale di Azure classico](https://manage.windowsazure.com/) e completare il processo di registrazione una tantum. Per accedere al tenant di Azure AD, usare questi [passaggi](https://technet.microsoft.com/library/dn832618.aspx).

#### L'utente dispone di una sottoscrizione di Azure associata a un account Microsoft
Se in precedenza è stata creata una sottoscrizione di Azure usando il proprio account Microsoft personale (hotmail o live), si dispone già di un tenant. Nel [portale di Azure classico](https://manage.windowsazure.com/) il **tenant predefinito** è elencato in **Tutti gli elementi** e in **Active Directory**. È possibile usare questo tenant in base alle esigenze, ma potrebbe essere necessario dover creare un account amministratore aziendale.

A tale scopo, seguire questa procedura: In alternativa, è possibile creare un nuovo tenant e creare un amministratore in quel tenant seguendo una procedura simile.

1.	Accedere al [portale di Azure classico](https://manage.windowsazure.com/) con l'account personale.
2.	Selezionare **Active Directory** nella barra dei menu a sinistra. 
3.	Selezionare **Directory predefinita** nell'elenco delle directory disponibili.
4.	Selezionare la scheda **Utenti** nella parte superiore. È disponibile un unico utente identificato da "Account Microsoft" nella colonna Originato da.
5.	Selezionare **Aggiungi utente** nella parte inferiore. 
6.	Nel **modulo di aggiunta dell'utente** specificare le informazioni seguenti:  
	
	Proprietà | Descrizione
--- | ---
Type of User | Nuovo utente nell'organizzazione
User name | Scegliere un nome utente per questo amministratore
First Name/Last Name/Display Name | Immettere i valori
Role | Amministratore globale
Alternate Email Address | Immettere i valori
Optional | Abilitare il servizio Multi-Factor Authentication  
	
	Selezionare il pulsane **CREA**, per completare e visualizzare la password temporanea.

Al termine, prendere nota della password temporanea per il nuovo utente amministratore. Per modificare la password temporanea, accedere a [https://login.microsoftonline.com](https://login.microsoftonline.com) con questo nuovo account utente e modificare la password. È anche possibile inviare la password direttamente all'utente, usando un indirizzo di posta elettronica alternativo.


#### L'utente dispone di una sottoscrizione di Azure associata a un account dell'organizzazione
Se in precedenza è stata creata una sottoscrizione di Azure usando il proprio account dell'organizzazione, si dispone già di un tenant. Nel [portale di Azure classico](https://manage.windowsazure.com/) il tenant è elencato in **Tutti gli elementi** e in **Active Directory**. È possibile usare questo tenant in base alle esigenze. È inoltre possibile creare un nuovo tenant usando il menu **Nuovo** nella barra delle applicazioni nella parte inferiore.

#### L'utente non dispone di nessuna delle sottoscrizioni precedenti e desidera iniziare da zero
Se nessuno dei casi precedenti si applica alla situazione corrente, passare a [https://account.windowsazure.com/organization](https://account.windowsazure.com/organization) per iscriversi ad Azure con un nuovo account dell'organizzazione. Una volta effettuata l'iscrizione, si disporrà del proprio tenant di Azure AD con il nome di dominio scelto. Nel [portale di Azure classico](https://manage.windowsazure.com/) è possibile visualizzare il tenant di **Active Directory** nel menu a sinistra.

## Passaggio 2: Creare una nuova sottoscrizione di Azure o usarne una esistente
Dopo aver ottenuto il tenant di Active Directory, è possibile creare una nuova sottoscrizione di Azure o usarne una esistente. La sottoscrizione di Azure AD include diverse edizioni. Per PowerApps Enterprise, è possibile usare l'edizione Free. Tuttavia, se è necessario usare il proxy di AAD per creare la connettività ibrida ai dati locali, è necessario usare l'edizione Premium o Basic.

In [Edizioni di Azure Active Directory](../active-directory/active-directory-editions.md) sono elencate altre funzionalità.


## Passaggio 3: Iscriversi a PowerApps Enterprise con la sottoscrizione aziendale di Azure
> [AZURE.NOTE] Per eseguire le operazioni seguenti è necessario che l'amministratore della sottoscrizione acceda al portale di Azure e invii una richiesta.

Dopo aver ottenuto il tenant di Active Directory e una sottoscrizione di Azure, gli amministratori delle sottoscrizioni aziendali possono iscriversi a PowerApps Enterprise. L'amministratore può inoltre aggiungere utenti della società affinché svolgano attività di 'amministrazione' di PowerApps, tra cui assegnazione di autorizzazioni agli utenti e gestione delle pubblicazioni di PowerApps nella sottoscrizione di Azure.

Se non si esegue l'iscrizione a PowerApps Enterprise, viene visualizzato un pannello che vieta l'accesso quando si passa al [portale di Azure](https://portal.azure.com/) e si cerca PowerApps. Per iscrivere la società, l'**amministratore della sottoscrizione** può passare a [PowerApps](http://go.microsoft.com/fwlink/p/?LinkId=716848) per contattare Microsoft in modo da ottenere altre informazioni sui prezzi e sulla procedura di iscrizione.

![][4]

Dopo aver completato il processo di iscrizione e le attività preliminari per l'uso di PowerApps Enterprise, è possibile:

- Aggiungere utenti della società che usano [il controllo degli accessi in base al ruolo](../role-based-access-control-configure.md) e assegnare a tali utenti ruoli di amministrazione di PowerApps per accedere al portale di PowerApps Enterprise.
- Creare un ambiente del servizio app dedicato per ospitare PowerApps.
- Creare le API e le connessioni da eseguire nell'ambiente del servizio app dedicato.
- Aggiungere all'ambiente del servizio app, oltre alle applicazioni create in PowerApps, altre app tra cui app Web, app per dispositivi mobili, app per le API e app per la logica.

Nell'esempio seguente, la società Contoso ha eseguito l'iscrizione a PowerApps. In questo nuovo pannello di **PowerApps** è possibile visualizzare un riepilogo dei diversi tipi di app create usando questo ambiente del servizio app. In **Gestisci API** è possibile visualizzare un riepilogo delle API create da Microsoft (Gestite da Microsoft) e visualizzare le API create da Contoso (Gestite dall'IT):

![Pannello aziendale di esempio di PowerApps][3]


## Passaggio 4: Creare un ambiente del servizio app
Creare un ambiente del servizio app per ospitare le API e le connessioni di PowerApps, nonché app per dispositivi mobili, app Web, app per le API e app per la logica.

Un ambiente del servizio app è un ambiente isolato e dedicato che esegue in modo sicuro tutte le app personali. Le risorse di calcolo sono specifiche di un ambiente del servizio app e sono dedicate esclusivamente all'esecuzione delle app personali. Quando si esegue l'iscrizione a PowerApps Enterprise, viene usato un ambiente del servizio app dedicato per ospitare le API e le connessioni usate dalle app. Si tratta di un tipo "speciale" di ambiente del servizio app. In particolare:

- È possibile usarlo per le operazioni desiderate ed è associato all'azienda, non alla sottoscrizione.
- È possibile configurare le API e le connessioni che le app create in PowerApps possono usare. È tuttavia possibile aggiungere anche app Web, app per dispositivi mobili, app per la logica e app per le API a questo stesso ambiente del servizio app. 
- La fatturazione è fissa e inclusa in PowerApps Enterprise.  
- La scalabilità viene gestita automaticamente. Non è necessario monitorare l'ambiente per determinare se sono necessarie risorse di calcolo aggiuntive.

L'ambiente del servizio app di Azure regolare presenta numerose funzionalità. Per informazioni dettagliate, vedere [Introduzione all'ambiente del servizio app](../app-service-app-service-environment-intro.md).

#### Requisiti iniziali

- Sottoscrizione aziendale di Azure
- Amministratore della sottoscrizione all'interno della società che ha [eseguito l'iscrizione a PowerApps Enterprise per la società](powerapps-get-started-azure-portal.md).
- Accesso al portale di Azure come amministratore di PowerApps ("proprietario" di PowerApps) o amministratore della sottoscrizione.

### Creare un ambiente del servizio app
> [AZURE.NOTE] Se non è presente l'opzione per creare l'ambiente del servizio app, l'ambiente è già stato creato per il tenant. Per visualizzare i dettagli, selezionare **Impostazioni** per aprire l'ambiente del servizio app.

1. Nel [portale di Azure](https://portal.azure.com/) accedere con l'account aziendale. Ad esempio, accedere con *NomeUtente*@*Azienda*.com. Quando si esegue questa operazione, si accede automaticamente alla sottoscrizione della propria società.
 
2. Selezionare **Sfoglia** nella barra delle applicazioni: ![Esplorare PowerApps][1]
  
3. Nell'elenco, è possibile scorrere per trovare PowerApps o digitare *powerapps*: ![Cercare PowerApps][2]

4. Nel pannello di **PowerApps** selezionare **Crea un ambiente del servizio app per iniziare** o selezionare **Ambiente del servizio app** in *Impostazioni*: ![][5]

	> [AZURE.NOTE] Se si seleziona **Crea un ambiente del servizio app per iniziare**, viene visualizzato un pannello aggiuntivo con informazioni dettagliate sull'ambiente del servizio app. Fare clic sul collegamento Crea nel pannello per avviare il pannello correlato.

5. Successivamente, immettere il nome, selezionare la sottoscrizione che si desidera usare, selezionare o creare un nuovo gruppo di risorse e selezionare una rete virtuale. **Si noti** che dopo aver scelto una rete virtuale, non è possibile modificarla: ![][6] Per altre informazioni sul funzionamento delle reti virtuali con un ambiente del servizio app, vedere [Come creare un ambiente del servizio app](../app-service-web-how-to-create-an-app-service-environment.md).

6. Selezionare **Aggiungi** per completare la creazione dell'ambiente del servizio app.

> [AZURE.TIP] Quando si crea l'ambiente del servizio app usando PowerApps, non viene chiesto di configurare il pool delle risorse di calcolo. Questa operazione viene eseguita automaticamente.

Tenere presente che è possibile aggiungere anche app Web, app per dispositivi mobili e app per le API a questo ambiente del servizio app. È in effetti l'ambiente personale ad aggiungere dati a supporto dell'ambiente del servizio app.

### Aggiungere amministratori per gestire l'ambiente del servizio app

Per ottenere l'accesso all'ambiente del servizio app e creare API, connessioni e altre risorse, gli utenti devono essere aggiunti con il ruolo di proprietario.

1. Selezionare l'ambiente del servizio app appena creato.
2. In Informazioni di base selezionare la proprietà **Resource group**. Viene visualizzato il gruppo di risorse che contiene l'ambiente del servizio app: ![][7]
3. Selezionare l'icona RBAC per gestire le autorizzazioni: ![][8] L'aggiunta degli utenti e l'assegnazione dei ruoli è molto simile all'uso del [controllo degli accessi in base al ruolo](https://azure.microsoft.com/documentation/articles/role-based-access-control-configure/) in Azure.

> [AZURE.NOTE] Attualmente, non è possibile assegnare autorizzazioni RBAC all'ambiente del servizio app. È possibile assegnare autorizzazioni RBAC a livello di gruppo di risorse padre.

## Riepilogo e passaggi successivi
La società è ora iscritta a PowerApps e dispone di un ambiente del servizio app. Successivamente, è possibile aggiungere le API e le connessioni che possono essere usate dalle app.

- [Monitorare le app di PowerApps](powerapps-manage-monitor-usage.md)
- [Sviluppare un'API per PowerApps](powerapps-develop-api.md)
- [Aggiungere una nuova API, una connessione e fornire l'accesso agli utenti](powerapps-manage-api-connection-user-access.md)
- [Aggiornare un'API esistente e le relative proprietà](powerapps-configure-apis.md)


[1]: ./media/powerapps-get-started-azure-portal/browseall.png
[2]: ./media/powerapps-get-started-azure-portal/allresources.png
[3]: ./media/powerapps-get-started-azure-portal/powerappsblade.png
[4]: ./media/powerapps-get-started-azure-portal/noaccess.png
[5]: ./media/powerapps-get-started-azure-portal/createase.png
[6]: ./media/powerapps-get-started-azure-portal/aseproperties.png
[7]: ./media/powerapps-get-started-azure-portal/aseessentials.png
[8]: ./media/powerapps-get-started-azure-portal/resourcegrouprbac.png

<!---HONumber=AcomDC_0128_2016-->