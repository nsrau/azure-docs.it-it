<properties
	pageTitle="Come installare e configurare Azure PowerShell"
	description="Informazioni su come installare e configurare Azure PowerShell."
	editor="tysonn"
	manager="stevenka"
	documentationCenter=""
	services=""
	authors="coreyp-at-msft"/>

<tags
	ms.service="multiple"
	ms.workload="multiple"
	ms.tgt_pltfrm="powershell"
	ms.devlang="na"
	ms.topic="article"
	ms.date="12/01/2015"
	ms.author="coreyp"/>

# Come installare e configurare Azure PowerShell#

<div class="dev-center-tutorial-selector sublanding"><a href="/manage/install-and-configure-windows-powershell/" title="PowerShell" class="current">PowerShell</a><a href="/manage/install-and-configure-cli/" title="Interfaccia della riga di comando di Azure">Interfaccia della riga di comando di Azure</a></div>

##Informazioni su Azure PowerShell.#
Azure PowerShell è un modulo che offre i cmdlet per gestire Azure con Windows PowerShell. È possibile utilizzare i cmdlet per creare, testare, distribuire e gestire soluzioni e servizi offerti tramite la piattaforma Azure. Nella maggior parte dei casi, è possibile utilizzare i cmdlet per le stesse attività presenti nel portale di gestione, come ad esempio la creazione e la configurazione di servizi cloud, macchine virtuali, reti virtuali e app web.

Il modulo e il codice sorgente sono disponibili per il download in un archivio disponibile pubblicamente:

- [PowerShell 1.0.1](https://github.com/Azure/azure-powershell/releases/download/v1.0.1-November2015/azure-powershell.1.0.1.msi)
- [Codice sorgente di Azure PowerShell 1.0.1](https://github.com/Azure/azure-powershell/archive/v1.0.1-November2015.zip)

<a id="Install"></a>
## Passaggio 1: installare
Scaricare e installare [Azure PowerShell 1.0.1](https://github.com/Azure/azure-powershell/releases/download/v1.0.1-November2015/azure-powershell.1.0.1.msi) <a id="Connect"></a>

## Passaggio 2: avviare
Il modulo installa una console personalizzata per Azure PowerShell. È possibile eseguire i cmdlet dalla console standard di Windows PowerShell, o da quella di Azure PowerShell.

## Passaggio 3: connettere
Per la gestione dei servizi da parte dei cmdlet, è necessario disporre di una sottoscrizione. È possibile acquistare una sottoscrizione di Azure, se non se ne possiede già una. Per istruzioni, vedere [Introduzione ad Azure](http://go.microsoft.com/fwlink/p/?LinkId=320795).

1. Digitare Aggiungi-AzureAccount

2. Digitare l'indirizzo di posta elettronica e la password associati all'account. Le informazioni delle credenziali vengono autenticate e salvate in Azure, quindi la finestra viene chiusa.

--OPPURE--

Accedere all'account aziendale o dell'istituto di istruzione:

        $cred = Get-Credential
        Add-AzureAccount -Credential $cred

	>
	> [AZURE.NOTE] This non-interactive login method only works with a work or school account. A work or school account is a user that is managed by your work or school, and defined in the Azure Active Directory instance for your work or school. If you do not currently have a work or school account, and are using a Microsoft account to log in to your Azure subscription, you can easily create one using the following steps.
	>
	> 1. Login to the [Azure Management Portal](https://manage.windowsazure.com), and click on **Active Directory**.
	>
	> 2. If no directory exists, select **Create your directory** and provide the requested information.
	>
	> 3. Select your directory and add a new user. This new user can sign in using a work or school account.
	>
	>     During the creation of the user, you will be supplied with both an e-mail address for the user and a temporary password. Save this  information as it is used in another step.
	>
	> 4. From the management portal, select **Settings** and then select **Administrators**. Select **Add**, and add the new user as a co-administrator. This allows the work or school account to manage your Azure subscription.
	>
	> 5. Finally, log out of the Azure portal and then log back in using the work or school account. If this is the first time logging in with this account, you will be prompted to change the password.
	>
	>For more information on signing up for Microsoft Azure with a work or school account, see [Sign up for Microsoft Azure as an Organization](sign-up-organization.md).


### Visualizzare account e dettagli della sottoscrizione

Con Azure PowerShell è possibile utilizzare più account e sottoscrizioni. Per aggiungere altri account, è sufficiente eseguire più volte **Add-AzureAccount**.

Per ottenere gli account Azure disponibili, digitare:

	Get-AzureAccount

Per ottenere le sottoscrizioni Azure, digitare:

	Get-AzureSubscription








## Passaggio 4: test<a id="Ex"></a>


Dopo aver installato il modulo e aver configurato il computer per la connessione alla sottoscrizione, è possibile creare un'app Web di Azure per assicurarsi che tutto funzioni correttamente.

1. Avviare la console di Azure PowerShell.

2. Scegliere un nome per l'app web. È necessario sceglierne uno conforme alle convenzioni di denominazione DNS. I nomi validi possono contenere solo lettere dalla 'a' alla 'z', numeri da '0' a '9', nonché il segno meno ('-').

	Il nome dell'app Web deve essere univoco in Azure. In questo esempio viene usato "mySite". Assicurarsi tuttavia di scegliere un nome diverso da questo, ad esempio il nome del proprio account seguito da un numero.

	Dopo aver selezionato un nome, digitare un comando simile al seguente. Sostituire il nome dell'app web per "mySite".

		New-AzureWebsite mySite

	Il cmdlet crea l'app web e restituisce un oggetto che rappresenta la nuova app Web. Le proprietà dell'oggetto includono informazioni utili sull'app Web.

3. Per ottenere informazioni sull'app Web, digitare questo comando. Vengono restituite alcune informazioni su tutte le app Web nella sottoscrizione, inclusa quella appena creata.

		Get-AzureWebsite

4. Per ottenere altre informazioni sull'app Web, aggiungere il nome dell'app al comando. Assicurarsi di sostituire "mySite" con il nome dell'app Web.

		Get-AzureWebsite -Name mySite

5. Le app Web vengono avviate dopo la creazione. Per arrestare l'app Web, digitare il comando seguente, incluso il nome dell'app Web.

		Stop-AzureWebsite -Name mySite

6. Per verificare che il sito Web si sia arrestato, eseguire di nuovo il comando Get-AzureWebsite.

		Get-AzureWebsite

7. Per completare questo test, eliminare l'app Web. Digitare:

		Remove-AzureWebsite -Name mySite

7. Per completare questa attività, confermare l'eliminazione dell'app Web.

		Get-AzureWebsite -Name mySite

##<a id="Help"></a>Risorse della Guida##

Queste risorse consentono di ottenere informazioni per cmdlet specifici:


-   Dall'interno della console è possibile utilizzare la Guida incorporata. Per accedere a tale Guida, utilizzare il cmdlet **Get-Help**. 



- Informazioni di riferimento sui cmdlet nei moduli Azure PowerShell sono inoltre disponibili nella raccolta della documentazione di Azure. Per informazioni, vedere [Riferimento ai cmdlet di Azure](http://msdn.microsoft.com/library/windowsazure/jj554330.aspx).

Per le informazioni messe a disposizione dalla community, visitare i seguenti forum:

- [Forum Azure su MSDN](http://go.microsoft.com/fwlink/p/?LinkId=320212)
- [Stackoverflow](http://go.microsoft.com/fwlink/?LinkId=320213)

<!---HONumber=AcomDC_1203_2015-->