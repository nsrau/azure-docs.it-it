<properties
	pageTitle="Accedere dall'interfaccia della riga di comando di Azure | Microsoft Azure"
	description="Connettersi a una sottoscrizione Azure dall'interfaccia della riga di comando di Azure"
	editor="tysonn"
	manager="timlt"
	documentationCenter=""
	authors="dlepow"
	services=""/>

<tags
	ms.service="multiple"
	ms.workload="multiple"
	ms.tgt_pltfrm="command-line-interface"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/09/2015"
	ms.author="danlep"/>

# Connettersi a una sottoscrizione Azure dall'interfaccia della riga di comando di Azure

L'interfaccia della riga di comando di Azure è un insieme di comandi open source e multipiattaforma per utilizzare la piattaforma Azure. Questo documento spiega come connettersi alla sottoscrizione di Azure dall'interfaccia della riga di comando di Azure. Per istruzioni sull'installazione, vedere [Installare l'interfaccia della riga di comando di Azure](xplat-cli-install.md).

<a id="configure"></a>
## Come connettersi alla sottoscrizione di Azure

La maggior parte dei comandi forniti dall'interfaccia della riga di comando di Azure richiedono una connessione a un account Azure. Per configurare l'interfaccia della riga di comando di Azure per l'utilizzo con la sottoscrizione, è possibile eseguire una delle due seguenti operazioni:

* Accedere ad Azure utilizzando un account aziendale o dell'istituto di istruzione (detto anche account aziendale). Questa procedura utilizza Azure Active Directory per autenticare le credenziali.

o

* Scaricare e utilizzare un file di impostazioni di pubblicazione. Con questo metodo viene creato un certificato che consente di eseguire attività di gestione per tutto il periodo di validità della sottoscrizione e del certificato.

Per ulteriori informazioni sulla gestione di autenticazione e sottoscrizione, vedere [Differenza tra autenticazione basata su account e autenticazione basata su certificato][authandsub].

Se non si dispone di un account, è possibile creare un account di valutazione gratuita in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla [versione di valutazione gratuita di Azure][free-trial].

> [AZURE.NOTE]I più flessibili e avanzati servizi di Azure utilizzano Gestione risorse di Azure o la [modalità ARM](xplat-cli-azure-resource-manager.md). La modalità ARM richiede la connessione ad Azure utilizzando un account aziendale o dell'istituto di istruzione, con il metodo di accesso descritto di seguito.

### Utilizzare un metodo di accesso

Questo metodo di accesso funziona solo con un account aziendale o dell'istituto di istruzione. L'account è gestito dall'organizzazione e definito nell'Azure Active Directory aziendale.

> [AZURE.NOTE]Se non si dispone di un account aziendale o dell'istituto di istruzione e si usa un account personale per accedere alla sottoscrizione di Azure, è possibile crearne facilmente uno mediante la procedura seguente.
>
> 1. Accedere al [portale di Azure][portal] e selezionare **Active Directory**.
>
> 2. Se non esiste alcuna directory, selezionare **Create your directory** e specificare le informazioni richieste.
>
> 3. Selezionare la directory e aggiungere un nuovo utente. Il nuovo utente è un account aziendale o dell'istituto di istruzione.
>
>     Durante la creazione dell'utente, verranno forniti un indirizzo di posta elettronica e una password provvisoria. Salvare queste informazioni poiché verranno usate in seguito.
>
> 4. Dal portale di Azure, selezionare **Impostazioni**, quindi **Amministratori**. Selezionare **Add** e aggiungere il nuovo utente come co-amministratore. In questo modo l'account aziendale o dell'istituto di istruzione può gestire la sottoscrizione di Azure.
>
> 5. Infine, disconnettersi dal portale di Azure e ripetere l'accesso con il nuovo account aziendale o dell'istituto di istruzione. La prima volta che si accede con questo account verrà richiesto di cambiare la password.
>
> 6. Assicurarsi di visualizzare le proprie sottoscrizioni quando si effettua l'accesso con l'account aziendale o dell'istituto di istruzione.
>
>Per ulteriori informazioni sugli account aziendali o dell'istituto di istruzione, vedere [Iscrizione ad Azure come organizzazione][signuporg].

Per accedere dall'interfaccia della riga di comando di Azure utilizzando un account aziendale o dell'istituto di istruzione, utilizzare il comando seguente:

	azure login -u <username>

e immettere la password quando viene richiesto.

> [AZURE.NOTE]Se è la prima volta che si effettua l'accesso con queste credenziali, verrà visualizzata la richiesta di specificare se si desidera memorizzare nella cache un token di autenticazione. Questo prompt viene visualizzato anche se è stato precedentemente utilizzato il comando `azure logout` descritto di seguito. Per ignorare la richiesta di scenari di automazione, usare il parametro `-q` con il comando `azure login`.

Per disconnettersi, usare il comando seguente:

	azure logout -u <username>

> [AZURE.NOTE]Se le sottoscrizioni associate all'account sono state autenticate solo con Active Directory, dopo la disconnessione le informazioni di sottoscrizione verranno eliminate dal profilo locale. Se tuttavia per le sottoscrizioni è stato anche importato un file di impostazioni di pubblicazione, con la disconnessione verranno eliminate dal profilo locale solo le informazioni correlate ad Active Directory.

### Usare il metodo del file di impostazioni di pubblicazione

> [AZURE.NOTE]Se per connettersi viene utilizzato questo metodo, è possibile utilizzare solo i comandi di gestione del servizio Azure (o modalità ASM).

Per scaricare le impostazioni di pubblicazione per l'account, usare il comando seguente:

	azure account download

Verrà aperto il browser predefinito e verrà richiesto di effettuare l'accesso al [portale di Azure][portal]. Dopo l'accesso, verrà scaricato un file `.publishsettings`. Prendere nota del percorso in cui il file viene salvato.

> [AZURE.NOTE]Se l'account è associato a più tenant Azure Active Directory, è possibile che venga richiesto di selezionare per quale istanza di Active Directory si desidera scaricare un file di impostazioni di pubblicazione.
>
> Dopo aver effettuato la selezione tramite la pagina di download oppure visitando il portale di Azure, il tenant Active Directory selezionato diventerà quello usato per impostazione predefinita nel portale e nella pagina di download. Dopo la scelta di un'impostazione predefinita, nella parte superiore della pagina di download verrà visualizzato un messaggio analogo al seguente: '__fare clic qui per tornare nella pagina di selezione__'. Usare il collegamento specificato per tornare nella pagina di selezione.

Importare `.publishsettings` eseguendo il comando seguente:

	azure account import <path to your .publishsettings file>

Dopo l'importazione delle impostazioni di pubblicazione, è consigliabile eliminare il file `.publishsettings` in quanto non è più necessario per l'interfaccia della riga di comando di Azure e presenta un rischio di sicurezza perché può essere usato per accedere alla sottoscrizione.

> [AZURE.NOTE]Quando si effettua l'accesso con un account aziendale o dell'istituto di istruzione o si importano le impostazioni di pubblicazione, le informazioni per l'accesso alla sottoscrizione Azure vengono archiviate in una directory `.azure` all'interno della directory `user`. La directory `user` è protetta dal sistema operativo. Tuttavia, si consiglia di eseguire ulteriori passaggi per crittografare la directory `user`. A tale scopo, è possibile procedere come segue:
>
> * In Windows, modificare le proprietà della directory o utilizzare BitLocker.
> * In Mac, abilitare FileVault per la cartella.
> * In Ubuntu, utilizzare la funzionalità per la crittografia della cartella Home. Altre distribuzioni di Linux offrono funzionalità equivalenti.

### Più sottoscrizioni

Se si dispone di più sottoscrizioni Azure, effettuando l'accesso ad Azure sarà possibile accedere a tutte le sottoscrizioni associate alle credenziali. Una sola sottoscrizione verrà selezionata come quella predefinita e usata dall'interfaccia della riga di comando di Azure per l'esecuzione delle operazioni. Per visualizzare le sottoscrizioni, inclusa quella predefinita, utilizzare il comando `azure account list`. Questo comando restituirà informazioni simili alle seguenti:

	info:    Executing command account list
	data:    Name              Id                                    Current
	data:    ----------------  ------------------------------------  -------
	data:    Azure-sub-1       ####################################  true
	data:    Azure-sub-2       ####################################  false

Nell'elenco precedente, la colonna **Current** indica la sottoscrizione predefinita corrente Azure-sub-1. Per cambiare sottoscrizione predefinita, utilizzare il comando `azure account set` e specificarne una diversa. Ad esempio:

	azure account set Azure-sub-2

La sottoscrizione predefinita diventerà in questo modo Azure-sub-2.

> [AZURE.NOTE]La modifica della sottoscrizione predefinita ha effetto immediato ed è una modifica globale. I nuovi comandi dell'interfaccia della riga di comando di Azure, sia che vengano eseguiti dalla stessa istanza della riga di comando o da una diversa, utilizzeranno la nuova sottoscrizione predefinita.

Se si desidera usare una sottoscrizione non predefinita con l'interfaccia della riga di comando di Azure, ma senza cambiare quella attualmente predefinita, è possibile usare l'opzione `--subscription` e specificare il nome della sottoscrizione da usare per l'operazione.

Una volta che si è connessi alla sottoscrizione di Azure, iniziare a utilizzare i comandi dell'interfaccia della riga di comando di Azure. Per altre informazioni, vedere la pagina [Come usare l'interfaccia della riga di comando di Azure](xplat-cli.md).

<a id="additional-resources"></a>
## Risorse aggiuntive

* [Utilizzo dell'interfaccia della riga di comando di Azure con i comandi di Gestione servizi (o modalità ASM)][cliasm]

* [Utilizzo dell'interfaccia della riga di comando di Azure con i comandi di Gestione risorse (o modalità ASM)][cliarm]

* Per informazioni sull'interfaccia della riga di comando di Azure, per scaricare il codice sorgente, segnalare problemi o contribuire al progetto, visitare l'[archivio GitHub per l'interfaccia della riga di comando di Azure](https://github.com/azure/azure-xplat-cli).

* In caso di problemi durante l'uso dell'interfaccia della riga di comando di Azure o di Azure, visitare i [forum di Azure](http://social.msdn.microsoft.com/Forums/windowsazure/home).

* Per ulteriori informazioni su Azure, vedere [http://azure.microsoft.com/](http://azure.microsoft.com).





[authandsub]: http://msdn.microsoft.com/library/windowsazure/hh531793.aspx#BKMK_AccountVCert
[free-trial]: http://azure.microsoft.com/pricing/free-trial/
[portal]: https://manage.windowsazure.com
[signuporg]: http://azure.microsoft.com/documentation/articles/sign-up-organization/
[cliasm]: virtual-machines/virtual-machines-command-line-tools.md
[cliarm]: virtual-machines/xplat-cli-azure-resource-manager.md

<!---HONumber=July15_HO3-->