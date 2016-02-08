<properties
	pageTitle="Accedere ad Azure dall'interfaccia della riga di comando | Microsoft Azure"
	description="Connettersi alla sottoscrizione di Azure dall'interfaccia della riga di comando di Azure per Mac, Linux e Windows"
	editor="tysonn"
	manager="timlt"
	documentationCenter=""
	authors="dlepow"
	services=""
	tags="azure-resource-manager,azure-service-management"/>

<tags
	ms.service="multiple"
	ms.workload="multiple"
	ms.tgt_pltfrm="command-line-interface"
	ms.devlang="na"
	ms.topic="article"
	ms.date="12/29/2015"
	ms.author="danlep"/>

# Connettersi a una sottoscrizione Azure dall'interfaccia della riga di comando di Azure

L'interfaccia della riga di comando di Azure è un insieme di comandi multipiattaforma open source per l'uso della piattaforma Azure. Questo articolo spiega come connettersi alla sottoscrizione di Azure dall'interfaccia della riga di comando di Azure per usare tutti i comandi di tale interfaccia. Se l'interfaccia non è stata ancora installata, vedere [Installare l'interfaccia della riga di comando di Azure](xplat-cli-install.md).


[AZURE.INCLUDE [learn-about-deployment-models](../includes/learn-about-deployment-models-both-include.md)]


Per connettersi alla propria sottoscrizione dall'interfaccia della riga di comando di Azure, è possibile procedere in due modi:

* **Accedere a Azure utilizzando un identità dell’ account aziendale o dell’istituto di istruzione Microsoft** - Questo utilizza entrambi i tipi di identità dell'account per l'autenticazione. L’attuale interfaccia della riga di comando supporta anche l'autenticazione interattiva per gli account che dispongono dell'autenticazione a più fattori abilitata. Dopo l'accesso in modalità interattiva, è possibile usare Gestione risorse o i comandi classici (Gestione dei servizi).

* **Scaricare e usare un file di impostazioni di pubblicazione**. In questo caso viene installato un certificato sul computer locale che consente di eseguire attività di gestione per tutto il periodo di validità della sottoscrizione e del certificato. Questa soluzione consente di usare soltanto i comandi classici (Gestione dei servizi).

Per altre informazioni sulla gestione di autenticazione e sottoscrizione, vedere [Qual è la differenza tra l'autenticazione basata su account e quella basata su certificati?][authandsub].

Se non si dispone di un account Azure, è possibile creare un account di valutazione gratuito in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla [versione di valutazione gratuita di Azure][free-trial].

>[AZURE.NOTE] Se si utilizza una versione dell’interfaccia della riga di comando di Azure precedente alla versione 0.9.10, è possibile utilizzare il comando `azure login` solo per le identità dell’account aziendale o dell’istituto di istruzione. Le identità dell’account Microsoft non funzionano. Tuttavia, è possibile utilizzare qualsiasi identità per accedere all'account con il comando interattivo `azure login` con le versioni dell’interfaccia della riga di comando di Azure 0.9.10 e versioni successive.
>
Le versioni dell’interfaccia della riga di comando 0.9.9 e versioni successive supportano l’autenticazione a più fattori.



## Utilizzare l’accesso interattivo nel metodo

Utilizzare il comando `azure login` -- senza alcun argomento -- per autenticare in modo interattivo con:

- un’identità dell’account aziendale o dell’istituto di istruzione che richiede l'autenticazione a più fattori, o
- con un'identità dell'account Microsoft quando si desidera accedere alle funzionalità di modalità di distribuzione di Gestione risorse

> [AZURE.NOTE]  In entrambi i casi, l'autenticazione e l’autorizzazione vengono eseguite mediante Azure Active Directory. Se si utilizza un'identità dell'account Microsoft, il log nel processo accede al dominio predefinito di Azure Active Directory. (Se è stata effettuata l'iscrizione per una valutazione gratuita, si potrebbe non essere conoscenza della creazione di un dominio predefinito di Azure Active Directory per l'account.)

L’accesso interattivo è semplice; digitare `azure login` e seguire le istruzioni, come illustrato di seguito:

	azure login                                                                                                                                                                                         
	info:    Executing command login
	info:    To sign in, use a web browser to open the page http://aka.ms/devicelogin. Enter the code XXXXXXXXX to authenticate. If you're signing in as an Azure AD application, use the --username and --password parameters.

Copiare il codice offerto in precedenza all'utente e aprire un browser per http://aka.ms/devicelogin. Inserire il codice, poi viene richiesto di inserire il nome utente e la password per l'identità che si desidera utilizzare. Al termine di quel processo, la shell del comando completa la procedura di accesso. Potrebbe essere simile a:

	info:    Added subscription Visual Studio Ultimate with MSDN
	info:    Added subscription Azure Free Trial
	info:    Setting subscription "Visual Studio Ultimate with MSDN" as default
	+
	info:    login command OK

## Utilizzare un log non interattivo con un account aziendale o dell’istituto di istruzione


La soluzione basata sull'accesso non interattivo funziona soltanto con un account aziendale o dell'istituto di istruzione, detto anche *account a livello di organizzazione*. L'account è gestito dall'organizzazione e definito nell'Azure Active Directory aziendale. È possibile [creare un account aziendale](#create-an-organizational-account) se non si dispone di uno, o è possibile [creare un ID aziendale o dell’istituto di istruzione a partire dall’ID account di Microsoft](./virtual-machines/resource-group-create-work-id-from-personal.md). Richiede di specificare un nome utente o un nome utente e una password per il comando `azure login`, come illustrato di seguito:

	azure login -u ahmet@contoso.onmicrosoft.com
	info:    Executing command login
	Password: *********
	|info:    Added subscription Visual Studio Ultimate with MSDN
	+
	info:    login command OK

Immettere la password quando viene richiesto.

	If this is your first time logging in with these credentials, you are asked to verify that you wish to cache an authentication token. This prompt also occurs if you have previously used the `azure logout` command (described below). To bypass this prompt for automation scenarios, run `azure login` with the `-q` parameter.

* **Per disconnettersi**, eseguire il comando seguente:

		azure logout -u <username>

	Se le sottoscrizioni associate all'account sono state autenticate solo con Active Directory, dopo la disconnessione le informazioni di sottoscrizione verranno eliminate dal profilo locale. Se però per le sottoscrizioni è stato anche importato un file di impostazioni di pubblicazione, con la disconnessione verranno eliminate dal profilo locale solo le informazioni correlate ad Active Directory.

## Usare il metodo del file di impostazioni di pubblicazione

Se è necessario usare soltanto i comandi classici (Gestione dei servizi) dell'interfaccia della riga di comando, è possibile connettersi con un file di impostazioni di pubblicazione.

* **Per scaricare il file di impostazioni di pubblicazione** per l'account, usare il comando seguente:

		azure account download

Verrà aperto il browser predefinito e richiesto di accedere al [portale di Azure classico][portal]. Dopo l'accesso, viene scaricato un file `.publishsettings`. Prendere nota del percorso in cui il file viene salvato.

> [AZURE.NOTE] Se l'account è associato a più tenant Azure Active Directory, è possibile che venga richiesto di selezionare per quale istanza di Active Directory si desidera scaricare un file di impostazioni di pubblicazione.
>
> Dopo aver effettuato la selezione tramite la pagina di download oppure visitando il portale di Azure classico, il tenant Active Directory selezionato diventerà quello usato per impostazione predefinita nel portale classico e nella pagina di download. Dopo la scelta di un'impostazione predefinita, nella parte superiore della pagina di download verrà visualizzato un messaggio analogo al seguente: '__fare clic qui per tornare nella pagina di selezione__'. Usare il collegamento specificato per tornare nella pagina di selezione.

* **Per importare il file di impostazioni di pubblicazione**, eseguire il comando seguente.

		azure account import <path to your .publishsettings file>

	Dopo l'importazione delle impostazioni di pubblicazione, è consigliabile eliminare il file `.publishsettings` in quanto non è più necessario per l'interfaccia della riga di comando di Azure e presenta un rischio di sicurezza perché può essere usato per accedere alla sottoscrizione.


## Più sottoscrizioni

Se si dispone di più sottoscrizioni Azure, effettuando l'accesso ad Azure sarà possibile accedere a tutte le sottoscrizioni associate alle credenziali. Una sola sottoscrizione è selezionata come predefinita e usata dall'interfaccia della riga di comando di Azure per l'esecuzione delle operazioni. Per visualizzare le sottoscrizioni, inclusa quella predefinita, utilizzare il comando `azure account list`. Questo comando restituisce informazioni simili alle seguenti:

	info:    Executing command account list
	data:    Name              Id                                    Current
	data:    ----------------  ------------------------------------  -------
	data:    Azure-sub-1       ####################################  true
	data:    Azure-sub-2       ####################################  false

Nell'elenco precedente, la colonna **Current** indica la sottoscrizione predefinita corrente Azure-sub-1. Per cambiare sottoscrizione predefinita, utilizzare il comando `azure account set` e specificarne una diversa. Ad esempio:

	azure account set Azure-sub-2

La sottoscrizione predefinita diventerà in questo modo Azure-sub-2.

> [AZURE.NOTE] La modifica della sottoscrizione predefinita ha effetto immediato ed è una modifica globale. I nuovi comandi dell'interfaccia della riga di comando di Azure, sia che vengano eseguiti dalla stessa istanza della riga di comando o da una diversa, useranno la nuova sottoscrizione predefinita.

Se si desidera usare una sottoscrizione non predefinita con l'interfaccia della riga di comando di Azure, ma senza cambiare quella attualmente predefinita, è possibile usare l'opzione `--subscription` e specificare il nome della sottoscrizione da usare per l'operazione.

Una volta che si è connessi alla sottoscrizione di Azure, iniziare a utilizzare i comandi dell'interfaccia della riga di comando di Azure.

## Archiviazione delle impostazioni dell'interfaccia della riga di comando

Quando si accede con un account aziendale o dell'istituto di istruzione o si importano le impostazioni di pubblicazione, i log e il profilo dell'interfaccia della riga di comando vengono archiviati in una directory `.azure` all'interno della directory `user`. La directory `user` è protetta dal sistema operativo. Tuttavia, si consiglia di eseguire ulteriori passaggi per crittografare la directory `user`. A tale scopo, è possibile procedere come segue:

* In Windows, modificare le proprietà della directory o utilizzare BitLocker.
* In Mac, abilitare FileVault per la cartella.
* In Ubuntu usare la funzionalità per la crittografia della cartella Home. Altre distribuzioni di Linux offrono funzionalità analoghe.

## Risorse aggiuntive

* [Uso dell'interfaccia della riga di comando di Azure con i comandi (classici) di Gestione dei servizi][cliasm]

* [Uso dell'interfaccia della riga di comando di Azure con i comandi di Gestione risorse][cliarm]

* Per altre informazioni sull'interfaccia della riga di comando di Azure, il download del codice sorgente, la segnalazione dei problemi o la collaborazione al progetto, visitare il [repository GitHub per tale interfaccia](https://github.com/azure/azure-xplat-cli).

* In caso di problemi durante l'uso dell'interfaccia della riga di comando di Azure o di Azure, visitare i [forum di Azure](http://social.msdn.microsoft.com/Forums/windowsazure/home).





[authandsub]: http://msdn.microsoft.com/library/windowsazure/hh531793.aspx#BKMK_AccountVCert
[free-trial]: http://azure.microsoft.com/pricing/free-trial/
[portal]: https://manage.windowsazure.com
[signuporg]: http://azure.microsoft.com/documentation/articles/sign-up-organization/
[cliasm]: virtual-machines/virtual-machines-command-line-tools.md
[cliarm]: xplat-cli-azure-resource-manager.md

<!---HONumber=AcomDC_0128_2016-->