<properties
	pageTitle="Accedere ad Azure dall'interfaccia della riga di comando | Microsoft Azure"
	description="Connettersi alla sottoscrizione di Azure dall'interfaccia della riga di comando di Azure per Mac, Linux e Windows"
	editor="tysonn"
	manager="timlt"
	documentationCenter=""
	authors="dlepow"
	services="virtual-machines-linux,virtual-network,storage,azure-resource-manager"
	tags="azure-resource-manager,azure-service-management"/>

<tags
	ms.service="multiple"
	ms.workload="multiple"
	ms.tgt_pltfrm="vm-multiple"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/13/2016"
	ms.author="danlep"/>

# Connettersi a una sottoscrizione Azure dall'interfaccia della riga di comando di Azure

L'interfaccia della riga di comando di Azure è un insieme di comandi multipiattaforma open source per l'uso della piattaforma Azure. Questo articolo descrive alcuni modi per fornire all'account Azure le credenziali al fine di connettere l'interfaccia della riga di comando di Azure alla propria sottoscrizione di Azure. Se l'interfaccia non è stata ancora installata, vedere [Installare l'interfaccia della riga di comando di Azure](xplat-cli-install.md). Se non si dispone di una sottoscrizione di Azure, è possibile creare un [account gratuito](http://azure.microsoft.com/free/) in pochi minuti.

Per connettersi alla propria sottoscrizione dall'interfaccia della riga di comando di Azure, è possibile procedere in due modi:

* **Accedere ad Azure usando l'identità dell'account aziendale o dell'istituto di istruzione o l'identità di un account Microsoft** - Usare il comando `azure login` con ognuno di questi tipi di identità dell'account per autenticarsi mediante Azure Active Directory. È consigliabile usare questo metodo per la creazione della maggior parte delle distribuzioni di Azure. Con determinati account, il comando `azure login` richiede di accedere in modo interattivo tramite un portale Web.

    Usare anche il comando `azure login` per autenticare un'entità servizio per un'applicazione Azure Active Directory, utile per l'esecuzione di servizi automatizzati.
    
    Dopo l'accesso con un'identità di account supportata, è possibile usare i comandi dell'interfaccia della riga di comando della modalità Azure Resource Manager o della modalità Gestione dei servizi di Azure.

* **Scaricare e usare un file di impostazioni di pubblicazione**. In questo caso viene installato un certificato sul computer locale che consente di eseguire attività di gestione per tutto il periodo di validità della sottoscrizione e del certificato.

    Questo metodo consente di usare soltanto i comandi dell'interfaccia della riga di comando della modalità Gestione dei servizi di Azure.

>[AZURE.NOTE] Se si usa una versione dell'interfaccia della riga di comando di Azure precedente alla 0.9.10, è possibile usare il comando `azure login` solo con un account aziendale o dell'istituto di istruzione. Le identità dell'account Microsoft non funzionano. Tuttavia, è possibile [creare un ID dell'account aziendale o dell'istituto di istruzione dall'ID dell'account Microsoft](virtual-machines/virtual-machines-windows-create-aad-work-id.md).

Per informazioni sulle diverse identità dell'account e sulle sottoscrizioni di Azure, vedere [Associare le sottoscrizioni di Azure ad Azure Active Directory](./active-directory/active-directory-how-subscriptions-associated-directory.md).

## Usare azure login per autenticarsi interattivamente

Utilizzare il comando `azure login` -- senza alcun argomento -- per autenticare in modo interattivo con:

- un'identità dell'account aziendale o dell'istituto di istruzione (anche detto *account dell'organizzazione*) che richiede l'autenticazione a più fattori, o
- con un'identità dell'account Microsoft quando si desidera accedere ai comandi della modalità Gestione risorse

> [AZURE.NOTE]  In entrambi i casi, l'autenticazione e l’autorizzazione vengono eseguite mediante Azure Active Directory. Se si utilizza un'identità dell'account Microsoft, il log nel processo accede al dominio predefinito di Azure Active Directory. Se è stata effettuata l'iscrizione per un account Azure gratuito, si potrebbe non essere conoscenza della creazione di un dominio predefinito di Azure Active Directory per l'account.

L’accesso interattivo è semplice; digitare `azure login` e seguire le istruzioni, come illustrato di seguito:

	azure login                                                                                                                                                                                         
	info:    Executing command login
	info:    To sign in, use a web browser to open the page http://aka.ms/devicelogin. Enter the code XXXXXXXXX to authenticate. 

Copiare il codice offerto in precedenza all'utente e aprire un browser all'indirizzo http://aka.ms/devicelogin (o un'altra pagina se specificato). Inserire il codice, poi viene richiesto di inserire il nome utente e la password per l'identità che si desidera utilizzare. Al termine di quel processo, la shell del comando completa la procedura di accesso. Potrebbe essere simile a:

	info:    Added subscription Visual Studio Ultimate with MSDN
	info:    Added subscription Azure Free Trial
	info:    Setting subscription "Visual Studio Ultimate with MSDN" as default
	+
	info:    login command OK

## Usare azure login con un nome utente e una password


Quando si vuole usare un account aziendale o dell'istituto di istruzione che non richiede l'autenticazione a più fattori, usare il comando `azure login` con il parametro username o con entrambi i parametri username e password per autenticarsi. L'esempio seguente fornisce il nome utente di un account dell'organizzazione:

	azure login -u ahmet@contoso.onmicrosoft.com
	info:    Executing command login
	Password: *********
	|info:    Added subscription Visual Studio Ultimate with MSDN
	+
	info:    login command OK

Immettere la password quando viene richiesto.

Se è la prima volta che si accede con queste credenziali, verrà chiesto di specificare se si desidera memorizzare nella cache un token di autenticazione. Questa richiesta viene visualizzata anche se è stato precedentemente usato il comando `azure logout` (descritto più avanti in questo articolo). Per ignorare la richiesta per gli scenari di automazione, eseguire `azure login` con il parametro `-q`.

   

## Usare azure login con un'entità servizio

Se è stata creata un'entità servizio per un'applicazione Active Directory e l'entità servizio possiede autorizzazioni per la sottoscrizione, è possibile usare il comando `azure login` per autenticare l'entità servizio. A seconda dello scenario, le credenziali dell'entità servizio possono essere fornite come parametri espliciti del comando `azure login` oppure mediante uno script dell'interfaccia della riga di comando o il codice dell'applicazione. È anche possibile usare un certificato per autenticare l'entità servizio in modo non interattivo per gli scenari di automazione. Per dettagli ed esempi, vedere [Usare Azure PowerShell per creare un'entità servizio per accedere alle risorse](resource-group-authenticate-service-principal.md).

## Usare un file di impostazioni di pubblicazione

Se è necessario usare soltanto i comandi dell'interfaccia della riga di comando della modalità Gestione dei servizi di Azure, ad esempio per distribuire le VM di Azure nel modello di distribuzione classica, è possibile connettersi con un file di impostazioni di pubblicazione.

* **Per scaricare il file di impostazioni di pubblicazione** per l'account, usare il comando seguente (disponibile soltanto nella modalità Gestione dei servizi):

		azure account download

    Verrà aperto il browser predefinito e richiesto di accedere al [portale di Azure classico](https://manage.windowsazure.com). Dopo l'accesso, viene scaricato un file `.publishsettings`. Prendere nota del percorso in cui il file viene salvato.

    > [AZURE.NOTE] Se l'account è associato a più tenant Azure Active Directory, è possibile che venga richiesto di selezionare per quale istanza di Active Directory si desidera scaricare un file di impostazioni di pubblicazione.

    Dopo aver effettuato la selezione tramite la pagina di download oppure visitando il portale di Azure classico, il tenant Active Directory selezionato diventerà quello usato per impostazione predefinita nel portale classico e nella pagina di download. Dopo la scelta di un'impostazione predefinita, nella parte superiore della pagina di download verrà visualizzato un messaggio analogo al seguente: '__fare clic qui per tornare nella pagina di selezione__'. Usare il collegamento specificato per tornare nella pagina di selezione.

* **Per importare il file di impostazioni di pubblicazione**, eseguire il comando seguente.

		azure account import <path to your .publishsettings file>

	>[AZURE.IMPORTANT]Dopo l'importazione delle impostazioni di pubblicazione, è opportuno eliminare il file `.publishsettings`. Il file non è più necessario per l'interfaccia della riga di comando di Azure e rappresenta un rischio di sicurezza perché potrebbe essere usato per accedere alla sottoscrizione.

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

Una volta che si è connessi alla sottoscrizione di Azure, è possibile iniziare a usare i comandi dell'interfaccia della riga di comando di Azure per lavorare con le risorse di Azure.

## Modalità di comando dell'interfaccia della riga di comando

L'interfaccia della riga di comando di Azure offre due modalità di comando per lavorare con le risorse di Azure ciascuna delle quali presenta un set di comandi diverso:

* **Modalità Resource Manager**: per lavorare con le risorse di Azure nel modello di distribuzione di Resource Manager. Per impostare questa modalità, eseguire `azure config mode arm`.

* **Modalità Gestione dei servizi**: per lavorare con le risorse di Azure nel modello di distribuzione classica. Per impostare questa modalità, eseguire `azure config mode asm`.

Subito dopo l'installazione, l'interfaccia della riga di comando è in modalità Gestione servizi.

>[AZURE.NOTE]La modalità Resource Manager e la modalità Gestione dei servizi si escludono a vicenda, ossia le risorse create in una modalità non possono essere gestite dall'altra.

## Archiviazione delle impostazioni dell'interfaccia della riga di comando

Quando si esegue l'accesso con il comando `azure login` o si importano le impostazioni di pubblicazione, i log e il profilo dell'interfaccia della riga di comando vengono archiviati in una directory `.azure` all'interno della directory `user`. La directory `user` è protetta dal sistema operativo. Tuttavia, si consiglia di eseguire ulteriori passaggi per crittografare la directory `user`. A tale scopo, è possibile procedere come segue:

* In Windows, modificare le proprietà della directory o utilizzare BitLocker.
* In Mac, abilitare FileVault per la cartella.
* In Ubuntu usare la funzionalità per la crittografia della cartella Home. Altre distribuzioni di Linux offrono funzionalità analoghe.

## Disconnessione

Per disconnettersi, usare il comando seguente:

	azure logout -u <username>

Se le sottoscrizioni associate all'account sono state autenticate solo con Active Directory, dopo la disconnessione le informazioni di sottoscrizione verranno eliminate dal profilo locale. Se però per le sottoscrizioni è stato anche importato un file di impostazioni di pubblicazione, con la disconnessione verranno eliminate dal profilo locale solo le informazioni correlate ad Active Directory.
## Passaggi successivi

* Per l'uso dei comandi dell'interfaccia della riga di comando di Azure, vedere [Comandi dell'interfaccia della riga di comando di Azure in modalità Resource Manager](./virtual-machines/azure-cli-arm-commands.md) e [Comandi dell'interfaccia della riga di comando di Azure in modalità Gestione servizi di Azure (asm)](virtual-machines-command-line-tools.md).

* Per altre informazioni sull'interfaccia della riga di comando di Azure, il download del codice sorgente, la segnalazione dei problemi o la collaborazione al progetto, visitare il [repository GitHub per tale interfaccia](https://github.com/azure/azure-xplat-cli).

* In caso di problemi durante l'uso dell'interfaccia della riga di comando di Azure o di Azure, visitare i [forum di Azure](https://social.msdn.microsoft.com/Forums/it-IT/home?forum=azurescripting).

<!---HONumber=AcomDC_0713_2016-->