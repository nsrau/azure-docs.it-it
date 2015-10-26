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
	ms.date="09/18/2015"
	ms.author="danlep"/>

# Connettersi a una sottoscrizione Azure dall'interfaccia della riga di comando di Azure

L'interfaccia della riga di comando di Azure è un insieme di comandi multipiattaforma open source per l'uso della piattaforma Azure. Questo articolo spiega come connettersi alla sottoscrizione di Azure dall'interfaccia della riga di comando di Azure per usare tutti i comandi di tale interfaccia. Se l'interfaccia non è stata ancora installata, vedere [Installare l'interfaccia della riga di comando di Azure](xplat-cli-install.md).


[AZURE.INCLUDE [learn-about-deployment-models](../includes/learn-about-deployment-models-both-include.md)]


Per connettersi alla propria sottoscrizione dall'interfaccia della riga di comando di Azure, è possibile procedere in due modi:

* **Accedere ad Azure usando un account aziendale o dell'istituto di istruzione**. In questo caso si usa Azure Active Directory per autenticare le credenziali. A partire dalla versione 0.9.9, l'interfaccia della riga di comando supporta gli account a livello di organizzazione abilitati per l'autenticazione a più fattori. Dopo l'accesso, è possibile usare Gestione risorse o i comandi classici (Gestione dei servizi).

* **Scaricare e usare un file di impostazioni di pubblicazione**. In questo caso viene installato un certificato che consente di eseguire attività di gestione per tutto il periodo di validità della sottoscrizione e del certificato. Questa soluzione consente di usare soltanto i comandi classici (Gestione dei servizi).

Per altre informazioni sulla gestione di autenticazione e sottoscrizione, vedere [Qual è la differenza tra l'autenticazione basata su account e quella basata su certificati?][authandsub].

Se non si dispone di un account Azure, è possibile creare un account di valutazione gratuito in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla [versione di valutazione gratuita di Azure][free-trial].


## Utilizzare un metodo di accesso

La soluzione basata sull'accesso funziona soltanto con un account aziendale o dell'istituto di istruzione, detto anche *account a livello di organizzazione*. L'account è gestito dall'organizzazione e definito nell'Azure Active Directory aziendale. Se non si dispone di un account a livello di organizzazione, è possibile [crearne uno](#create-an-organizational-account).


* **Per accedere** dall'interfaccia della riga di comando di Azure usando un account aziendale o dell'istituto di istruzione, eseguire il comando seguente:

	```
	azure login -u <username>
	```

	Immettere la password quando viene richiesto.

	Se è la prima volta che si accede con queste credenziali, verrà chiesto di specificare se si desidera memorizzare nella cache un token di autenticazione. Questa richiesta viene visualizzata anche se è stato precedentemente usato il comando `azure logout` descritto di seguito. Per ignorare la richiesta per gli scenari di automazione, eseguire `azure login` con il parametro `-q`.

* **Per disconnettersi**, eseguire il comando seguente:

	```
	azure logout -u <username>
	```

	Se le sottoscrizioni associate all'account sono state autenticate solo con Active Directory, dopo la disconnessione le informazioni di sottoscrizione verranno eliminate dal profilo locale. Se però per le sottoscrizioni è stato anche importato un file di impostazioni di pubblicazione, con la disconnessione verranno eliminate dal profilo locale solo le informazioni correlate ad Active Directory.

### Autenticazione a più fattori
A partire dalla versione 0.9.9 dell'interfaccia della riga di comando di Azure, è possibile accedere con un account a livello di organizzazione che usa l'autenticazione a più fattori, ovvero l'autenticazione con una password e uno o più metodi di verifica aggiuntivi, tra cui l'uso di un dispositivo attendibile o l'immissione di altri dati personali.

Dopo l'esecuzione di `azure login` con il nome utente e la password dell'account, l'interfaccia della riga di comando fornisce l'indirizzo di una pagina Web da aprire. Le istruzioni indicano di immettere un codice nella pagina per proseguire con l'autenticazione. L'accesso viene completato dopo che i criteri di autenticazione sono stati soddisfatti.


### Creare un account a livello di organizzazione.

Se non si dispone di un account aziendale o dell'istituto di istruzione e si usa un account personale per accedere alla sottoscrizione di Azure, è possibile creare facilmente un account a livello di organizzazione mediante la procedura seguente.

1. Accedere al [portale di Azure][portal] e selezionare **Active Directory**.

2. Se non esiste alcuna directory, selezionare **Create your directory** e specificare le informazioni richieste.

3. Selezionare la directory e aggiungere un nuovo utente. Il nuovo utente è un account aziendale o dell'istituto di istruzione.

	Durante la creazione dell'utente, verranno forniti un indirizzo di posta elettronica e una password provvisoria. Salvare queste informazioni poiché verranno usate in seguito.

4. Dal portale di Azure, selezionare **Impostazioni**, quindi **Amministratori**. Selezionare **Add** e aggiungere il nuovo utente come co-amministratore. In questo modo l'account aziendale o dell'istituto di istruzione può gestire la sottoscrizione di Azure.

5. Infine, disconnettersi dal portale di Azure e ripetere l'accesso con il nuovo account aziendale o dell'istituto di istruzione. La prima volta che si accede con questo account viene richiesto di cambiare la password.

	Assicurarsi di visualizzare le proprie sottoscrizioni quando si accede con il nuovo account.

Per altre informazioni sugli account aziendali o dell'istituto di istruzione, vedere [Iscrizione ad Azure come organizzazione][signuporg].

## Usare il metodo del file di impostazioni di pubblicazione

Se è necessario usare soltanto i comandi classici (Gestione dei servizi) dell'interfaccia della riga di comando, è possibile connettersi con un file di impostazioni di pubblicazione.

* **Per scaricare il file di impostazioni di pubblicazione** per l'account, usare il comando seguente:

	```
	azure account download
	```

	Verrà aperto il browser predefinito e richiesto di accedere al [portale di Azure][portal]. Dopo l'accesso, viene scaricato un file `.publishsettings`. Prendere nota del percorso in cui il file viene salvato.

	> [AZURE.NOTE]Se l'account è associato a più tenant Azure Active Directory, è possibile che venga richiesto di selezionare per quale istanza di Active Directory si desidera scaricare un file di impostazioni di pubblicazione.
	>
	> Dopo aver effettuato la selezione tramite la pagina di download oppure visitando il portale di Azure, il tenant Active Directory selezionato diventerà quello usato per impostazione predefinita nel portale e nella pagina di download. Dopo la scelta di un'impostazione predefinita, nella parte superiore della pagina di download verrà visualizzato un messaggio analogo al seguente: '__fare clic qui per tornare nella pagina di selezione__'. Usare il collegamento specificato per tornare nella pagina di selezione.

* **Per importare il file di impostazioni di pubblicazione**, eseguire il comando seguente.

	```
	azure account import <path to your .publishsettings file>
	```

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

> [AZURE.NOTE]La modifica della sottoscrizione predefinita ha effetto immediato ed è una modifica globale. I nuovi comandi dell'interfaccia della riga di comando di Azure, sia che vengano eseguiti dalla stessa istanza della riga di comando o da una diversa, useranno la nuova sottoscrizione predefinita.

Se si desidera usare una sottoscrizione non predefinita con l'interfaccia della riga di comando di Azure, ma senza cambiare quella attualmente predefinita, è possibile usare l'opzione `--subscription` e specificare il nome della sottoscrizione da usare per l'operazione.

Una volta che si è connessi alla sottoscrizione di Azure, iniziare a utilizzare i comandi dell'interfaccia della riga di comando di Azure. Per altre informazioni, vedere la pagina [Come usare l'interfaccia della riga di comando di Azure](xplat-cli-install.md).

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
[cliarm]: virtual-machines/xplat-cli-azure-resource-manager.md

<!---HONumber=Oct15_HO3-->