<properties 
	pageTitle="Risorse, ruoli e controllo di accesso in Application Insights" 
	description="Proprietari, collaboratori e lettori di informazioni dell'organizzazione." 
	services="application-insights" 
    documentationCenter=""
	authors="alancameronwills" 
	manager="ronmart"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/21/2015" 
	ms.author="awills"/>
 
# Risorse, ruoli e controllo di accesso in Application Insights

È possibile controllare chi ha letto e aggiornare l'accesso ai dati in Visual Studio [Application Insights][start] mediante il [controllo degli accessi in base al ruolo in Microsoft Azure](../role-based-access-control-configure.md).

> [AZURE.IMPORTANT]Assegnare l'accesso agli utenti nella **sottoscrizione o nel gruppo di risorse** a cui la risorsa dell'applicazione appartiene, non nella risorsa stessa. Assegnare il ruolo **collaboratore componente di Application Insights **. In tal modo si garantisce un controllo di accesso uniforme ai test Web e agli avvisi nonché alla risorsa dell'applicazione. [Altre informazioni](#access)


## Risorse, gruppi e sottoscrizioni

Innanzitutto prendere nota di alcune definizioni:

* **Risorse** : un'istanza di un servizio di Microsoft Azure. La risorsa di Application Insights raccoglie, analizza e visualizza i dati di telemetria inviati dall'applicazione. Altri tipi di risorse di Azure includono app Web, database e macchine virtuali. 

    Per visualizzare tutte le risorse, andare al [portale di Azure][portal], accedere e fare clic su Sfoglia.

    ![Scegliere il pulsante Sfoglia e quindi Tutto o Filtra per Application Insights](./media/app-insights-resources-roles-access-control/10-browse.png)

<a name="resource-group"></a>

* [**Gruppo di risorse**][group]: ogni risorsa appartiene a un gruppo. Un gruppo è un modo pratico per gestire risorse correlate, in particolare per il controllo di accesso. In un gruppo di risorse, ad esempio, è possibile inserire un'app Web, una risorsa di Application Insights per monitorare l'app e una risorsa di archiviazione per conservare i dati esportati.

    
    ![Scegliere Sfoglia, gruppi di risorse e quindi scegliere un gruppo](./media/app-insights-resources-roles-access-control/11-group.png)


* [**Sottoscrizione**](https://manage.windowsazure.com): per usare Application Insights o altre risorse di Azure, accedere a una sottoscrizione di Azure. Ogni gruppo di risorse appartiene a una sottoscrizione di Azure, dove si sceglie il pacchetto di prezzo e, se è la sottoscrizione di un'organizzazione, si scelgono i membri e le relative autorizzazioni di accesso.
* [**Account Microsoft**][account]: il nome utente e password usati per accedere a sottoscrizioni Microsoft Azure, XBox Live, Outlook.com e altri servizi Microsoft.


## <a name="access"></a> Controllare l'accesso nel gruppo di risorse

È importante comprendere che oltre la risorsa creata per l'applicazione, sono disponibili anche risorse nascoste distinte per avvisi e i test Web. Vengono collegati allo stesso [gruppo di risorse](#resource-group) dell'applicazione. È anche possibile che siano stati inseriti altri servizi di Azure in tale posizione, ad esempio siti Web o risorsa di archiviazione.

![Risorse in Application Insights](./media/app-insights-resources-roles-access-control/00-resources.png)

Per controllare l'accesso a queste risorse, è quindi consigliabile:

* Controllare l'accesso al livello della **sottoscrizione o del gruppo di risorse**.
* Assegnare il ruolo **collaboratore componente di Application Insights** agli utenti. In tal modo si consente loro di modificare i test Web, gli avvisi e le risorse di Application Insights, senza fornire l'accesso a tutti gli altri servizi del gruppo. 

## Per fornire l'accesso a un altro utente

È necessario disporre dei diritti di proprietario per la sottoscrizione o per il gruppo di risorse.

È necessario avere un [account Microsoft][account]. È possibile fornire l'accesso a utenti e anche a gruppi di utenti definiti in Azure Active Directory.

#### Passare al gruppo di risorse

Aggiungere l'utente da quella posizione.

![Nel pannello di risorse dell'applicazione, aprire Essentials, aprire il gruppo di risorse e quindi selezionare Impostazioni/Utenti. Fare clic su Aggiungi.](./media/app-insights-resources-roles-access-control/01-add-user.png)

oppure è possibile spostarsi ad un altro livello e aggiungere l'utente alla sottoscrizione.

#### Selezionare un ruolo

![Selezionare un ruolo per il nuovo utente](./media/app-insights-resources-roles-access-control/03-role.png)

Ruolo | Nel gruppo di risorse
---|---
Proprietario | È possibile modificare qualsiasi oggetto, incluso l'accesso utente
Collaboratore | È possibile modificare qualsiasi oggetto, incluse tutte le risorse
Collaboratore componente di Application Insights | Può modificare risorse, test Web e avvisi di Application Insights
Reader | È possibile visualizzare qualsiasi oggetto ma non apportare alcuna modifica

'Modifica' include la creazione, l'eliminazione e l'aggiornamento:

* Risorse
* Test Web
* Avvisi
* Esportazione continua

#### Selezionare l'utente


![Digitare l'indirizzo di posta elettronica di un nuovo utente. Selezionare l'utente](./media/app-insights-resources-roles-access-control/04-user.png)

Se l'utente desiderato non è nella directory, è possibile invitare chiunque disponga di un account Microsoft. Se si usano servizi come Outlook.com, OneDrive, Windows Phone o XBox Live, hanno un account Microsoft.



## Utenti e ruoli

* [Controllo di accesso basato sui ruoli in Microsoft Azure](../role-based-access-control-configure.md)



<!--Link references-->

[account]: https://account.microsoft.com
[group]: ../azure-preview-portal-using-resource-groups.md
[portal]: http://portal.azure.com/
[start]: app-insights-get-started.md

<!---HONumber=58--> 