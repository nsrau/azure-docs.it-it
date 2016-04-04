<properties
	pageTitle="Gestire l'insieme di credenziali delle chiavi tramite l'interfaccia della riga di comando | Microsoft Azure"
	description="Usare questa esercitazione per automatizzare le attività comuni nell'insieme di credenziali delle chiavi tramite l'interfaccia della riga di comando"
	services="key-vault"
	documentationCenter=""
	authors="BrucePerlerMS"
	manager="mbaldwin"
	tags="azure-resource-manager"/>

<tags
	ms.service="key-vault"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/08/2016"
	ms.author="bruceper"/>

# Gestire l'insieme di credenziali delle chiavi tramite l'interfaccia della riga di comando #
L'insieme di credenziali delle chiavi di Azure è disponibile nella maggior parte delle aree. Per altre informazioni, vedere la [pagina Insieme di credenziali delle chiavi - Prezzi](../../../../pricing/details/key-vault/).

## Introduzione  
Usare questa esercitazione per imparare a eseguire facilmente le attività iniziali dell'insieme di credenziali delle chiavi di Azure per creare un contenitore finalizzato (insieme di credenziali) in Azure, in cui archiviare e gestire chiavi crittografiche e segreti in Azure. Verrà inoltre descritto come usare l'interfaccia della riga di comando multipiattaforma di Azure per creare un insieme di credenziali contenente una chiave o una password che sarà possibile usare con un'applicazione Azure. L'esercitazione spiega poi come un'applicazione può usare questa chiave o password.

**Tempo previsto per il completamento:** 20 minuti

>[AZURE.NOTE]  Questa esercitazione non include istruzioni su come scrivere l'applicazione Azure usata nel passaggio che spiega come autorizzare un'applicazione all'uso di una chiave o un segreto nell'insieme di credenziali delle chiavi.
>
>Attualmente non è possibile configurare l'insieme di credenziali delle chiavi di Azure nel portale di Azure. Usare invece le istruzioni relative all'interfaccia della riga di comando multipiattaforma. In alternativa, per le istruzioni relative ad Azure PowerShell, vedere [questa esercitazione equivalente](key-vault-get-started.md).

Per informazioni generali sull'insieme di credenziali di Azure, vedere [Cos'è l'insieme di credenziali delle chiavi di Azure?](key-vault-whatis.md)

## Prerequisiti

Per completare l'esercitazione, sono necessari gli elementi seguenti:

- Una sottoscrizione a Microsoft Azure. Se non si dispone di una sottoscrizione, è possibile iscriversi per una [versione di valutazione gratuita](../../../pricing/free-trial).
- Interfaccia della riga di comando multipiattaforma versione 0.9.1 o successiva. Per installare l'ultima versione e associarla alla sottoscrizione di Azure, vedere [Installare e configurare l'interfaccia della riga di comando multipiattaforma di Azure](../xplat-cli-install.md).
- Un'applicazione che verrà configurata per usare la chiave o la password creata in questa esercitazione. Un'applicazione di esempio è disponibile nell'[Area download Microsoft](http://www.microsoft.com/download/details.aspx?id=45343). Per istruzioni, vedere il file Readme associato.

## Risorse della Guida per l'interfaccia della riga di comando multipiattaforma di Microsoft Azure

Questa esercitazione si presuppone che si abbia familiarità con l'interfaccia della riga di comando (Bash, terminal, prompt dei comandi)

Il parametro --help o -h consente di visualizzare la Guida per specifici comandi. In alternativa, è anche possibile utilizzare il formato azure help [comando] [opzioni] per ottenere le stesse informazioni. Ad esempio, tutti i comandi seguenti restituiranno le stesse informazioni:

    azure account set --help

    azure account set -h

    azure help account set

In caso di dubbi sui parametri necessari per un comando, fare riferimento alla Guida utilizzando --help, -h o azure help [comando].

È consigliabile di leggere anche le seguenti esercitazioni per acquisire familiarità con Gestione risorse di Azure nell'interfaccia della riga di comando multipiattaforma di Azure:

- [Installare e configurare l'interfaccia della riga di comando multipiattaforma di Azure.](../xplat-cli-install.md)
- [Uso dell'interfaccia della riga di comando multipiattaforma di Azure con Gestione risorse di Azure](../xplat-cli-azure-resource-manager.md)


## Connettersi alle sottoscrizioni

Per accedere utilizzando un account aziendale, utilizzare il comando seguente:

    azure login -u username -p password

Se invece si vuole accedere digitando in modo interattivo

    azure login

>[AZURE.NOTE]  Il metodo di accesso funziona solo con l'account aziendale. L'account aziendale corrisponde a un utente gestito dall'organizzazione e definito nel tenant Azure Active Directory dell'organizzazione.


Se non si dispone di un account aziendale e si usa un account Microsoft per accedere alla sottoscrizione di Azure, è possibile crearne facilmente uno utilizzando la procedura seguente.

1.	Accedere al [portale di gestione di Azure](https://manage.windowsazure.com/) e fare clic su Active Directory.
2.	Se non esistono directory, selezionare Crea directory e fornire le informazioni richieste.
3.	Selezionare la directory e aggiungere un nuovo utente. Questo nuovo utente corrisponde a un account aziendale. Durante la creazione dell'utente, verranno forniti un indirizzo di posta elettronica e una password provvisoria. Salvare queste informazioni perché verranno utilizzate in un altro passaggio.
4.	Nel portale selezionare Impostazioni e quindi Amministratori. Selezionare Aggiungi e aggiungere il nuovo utente come co-amministratore. In questo modo l'account aziendale potrà gestire la sottoscrizione di Azure.
5.	Infine, disconnettersi dal portale Azure ed effettuare di nuovo l'accesso con il nuovo account aziendale. La prima volta che si accede con questo account verrà richiesto di cambiare la password.

Per altre informazioni sull'uso dell'account aziendale con Microsoft Azure, vedere [Iscrizione ad Azure come organizzazione](sign-up-organization.md).

Se si dispone di più sottoscrizioni e se ne vuole specificare una in particolare da usare per l'insieme di credenziali delle chiavi di Azure, digitare quanto segue per visualizzare le sottoscrizioni per il proprio account:

    azure account list

Quindi, per specificare la sottoscrizione da usare, digitare.

    azure account set <subscription name>

Per altre informazioni sulla configurazione dell'interfaccia della riga di comando multipiattaforma di Azure, vedere [Installare e configurare l'interfaccia della riga di comando multipiattaforma di Azure](../xplat-cli-install.md).


## Passare all'uso di Gestione risorse di Azure

Poiché per l'insieme di credenziali delle chiavi è necessario Gestione risorse di Azure, digitare quanto segue per passare alla modalità Gestione risorse di Azure:

    azure config mode arm

## Creare un nuovo gruppo di risorse

Quando si usa Gestione risorse di Azure, tutte le risorse correlate vengono create in un gruppo di risorse. Per questa esercitazione si creerà un nuovo gruppo di risorse denominato 'ContosoResourceGroup'.

    azure group create 'ContosoResourceGroup' 'East Asia'

Il primo parametro è il nome del gruppo di risorse e il secondo è la posizione. Per la posizione usare il comando `azure location list` per identificare come si specifica una posizione alternativa a quella di questo esempio. Se servono altre informazioni, digitare: `azure help location`



## Creare un insieme di credenziali delle chiavi

Usare il comando `azure keyvault create` per creare un insieme di credenziali delle chiavi. Questo script ha tre parametri obbligatori: un nome del gruppo di risorse, un nome dell'insieme di credenziali delle chiavi e la località geografica.

Ad esempio, se si usa il nome dell'insieme di credenziali ContosoKeyVault, il nome del gruppo di risorse ContosoResourceGroup e la posizione East Asia, digitare:

    azure keyvault create --vault-name 'ContosoKeyVault' --resource-group 'ContosoResourceGroup' --location 'East Asia'

L'output di questo comando mostra le proprietà dell'insieme di credenziali delle chiavi appena creato. Le due proprietà più importanti sono:

- **Name**: nell'esempio corrisponde a ContosoKeyVault. Questo nome verrà usato per altri cmdlet di insieme di credenziali delle chiavi.
- **vaultUri**: nell'esempio corrisponde a https://contosokeyvault.vault.azure.net. Le applicazioni che usano l'insieme di credenziali tramite l'API REST devono usare questo URI.

L'account Azure ora è autorizzato a eseguire qualsiasi operazione su questo insieme di credenziali delle chiavi. Nessun altro lo è ancora.


## Aggiungere una chiave o un segreto all'insieme di credenziali delle chiavi

Per usare l'insieme di credenziali delle chiavi di Azure per creare automaticamente una chiave protetta tramite software, eseguire il comando `azure key create` e digitare quanto segue:

    azure keyvault key create --vault-name 'ContosoKeyVault' --key-name 'ContosoFirstKey' --destination software

Tuttavia, se si ha una chiave esistente in un file con estensione PEM salvato come file locale in un file denominato softkey.pem che si vuole caricare nell'insieme di credenziali delle chiavi di Azure, digitare il comando seguente per importare la chiave dal file PEM che protegge la chiave tramite software nel servizio dell'insieme di credenziali delle chiavi:

    azure keyvault key import --vaultName 'ContosoKeyVault' --key-name 'ContosoFirstKey' --pem-file './softkey.pem' --password 'PaSSWORD' --destination software

A questo punto è possibile fare riferimento alla chiave creata o caricata nell'insieme di credenziali delle chiavi di Azure, usando il relativo URI. Usare ****https://ContosoKeyVault.vault.azure.net/keys/ContosoFirstKey** per ottenere sempre la versione corrente e usare ****https://ContosoKeyVault.vault.azure.net/keys/ContosoFirstKey/cgacf4f763ar42ffb0a1gca546aygd87** per ottenere questa versione specifica.

Per aggiungere un segreto all'insieme di credenziali, ovvero una password denominata SQLPassword con il valore Pa$$w0rd per l'insieme di credenziali delle chiavi, digitare quanto segue:

    azure keyvault secret set --vault-name 'ContosoKeyVault' --secret-name 'SQLPassword' --value 'Pa$$w0rd'

È ora possibile fare riferimento a questa password aggiunta nell'insieme di credenziali delle chiavi di Azure, usando il relativo URI. Usare ****https://ContosoVault.vault.azure.net/secrets/SQLPassword** per ottenere sempre la versione corrente e usare ****https://ContosoVault.vault.azure.net/secrets/SQLPassword/90018dbb96a84117a0d2847ef8e7189d** per ottenere questa versione specifica.

Ora si può visualizzare la chiave o il segreto appena creato:

- Per visualizzare la chiave, digitare: `azure keyvault key list --vault-name 'ContosoKeyVault'`
- Per visualizzare il segreto, digitare: `azure keyvault secret list --vault-name 'ContosoKeyVault'`


## Registrare un'applicazione con Azure Active Directory

Questo passaggio di solito viene eseguito da uno sviluppatore, su un computer separato. Anche se non è specifico dell'insieme di credenziali delle chiavi di Azure, viene incluso qui per completezza.


>[AZURE.IMPORTANT] Per completare l'esercitazione, l'account, l'insieme di credenziali e l'applicazione in cui si registrerà questo passaggio devono essere tutti nella stessa directory di Azure.

Le applicazioni che usano un insieme di credenziali delle chiavi devono eseguire l'autenticazione con un token di Azure Active Directory. A tale scopo, il proprietario dell'applicazione deve innanzitutto registrare l'applicazione in Azure Active Directory. Al termine della registrazione, il proprietario dell'applicazione ottiene i valori seguenti:


- Un **ID applicazione** (chiamato anche ID client) e una **chiave di autenticazione** (chiamata anche segreto condiviso). L'applicazione deve presentare entrambi questi valori ad Azure Active Directory per ottenere un token. La configurazione dell'applicazione per eseguire questa operazione dipende dall'applicazione. Per l'applicazione di esempio dell'insieme di credenziali delle chiavi, il proprietario dell'applicazione imposta questi valori nel file app.config.



Per registrare l'applicazione in Azure Active Directory:

1. Accedere al portale di Azure.
2. A sinistra fare clic su **Active Directory** e quindi selezionare la directory in cui si registrerà l'applicazione. <br> <br> Nota: è necessario selezionare la stessa directory che contiene la sottoscrizione di Azure con cui si è creato l'insieme di credenziali chiave. Se non si sa quale directory selezionare, fare clic su **Impostazioni**, identificare la sottoscrizione con cui si è creato l'insieme di credenziali chiave e prendere nota del nome della directory visualizzata nell'ultima colonna.

3. Fare clic su **APPLICAZIONI**. Se nessuna app è stata aggiunta alla directory, questa pagina mostrerà solo il collegamento **Aggiungi app**. Fare clic sul collegamento. In alternativa, è possibile fare clic su **AGGIUNGI** sulla barra dei comandi.
4.	Nella procedura guidata **AGGIUNGI APPLICAZIONE**, nella pagina **Come procedere** fare clic su **Aggiungi un'applicazione che l'organizzazione sta sviluppando**.
5.	Nella pagina **Informazioni sull'applicazione** specificare un nome per l'applicazione e selezionare **APPLICAZIONE WEB E/O API WEB** (pagina predefinita). Fare clic sull'icona Avanti.
6.	Nella pagina **Proprietà dell'app** specificare **URL ACCESSO** e **URI ID APP** per l'applicazione Web. Se l'applicazione non dispone di questi valori, è possibile crearli per questo passaggio (ad esempio, è possibile specificare http://test1.contoso.com per entrambe le caselle). Non è importante se questi siti esistono. Ciò che conta è che ogni applicazione nella directory abbia un URI ID app diverso. La directory usa questa stringa per identificare l'app.
7.	Fare clic sull'icona Completa per salvare le modifiche nella procedura guidata.
8.	Nella pagina Avvio rapido fare clic su **CONFIGURA**.
9.	Scorrere fino alla sezione **chiavi**, selezionare la durata e quindi fare clic su **SALVA**. La pagina viene aggiornata e mostra un valore chiave. È necessario configurare l'applicazione con questo valore chiave e con il valore **ID CLIENT**. Le istruzioni per questa configurazione saranno specifiche dell'applicazione.
10.	Copiare da questa pagina il valore dell'ID client che si userà nel passaggio successivo per impostare le autorizzazioni per l'insieme di credenziali.




## Autorizzare l'applicazione a usare la chiave o il segreto

Per autorizzare l'accesso da parte dell'applicazione alla chiave o al segreto nell'insieme di credenziali, usare il comando `azure keyvault set-policy`.

Ad esempio, se il nome dell'insieme di credenziali è ContosoKeyVault e l'applicazione che si desidera autorizzare ha un ID client 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed e si vuole autorizzare l'applicazione a decrittografare e firmare con le chiavi dell'insieme di credenziali, eseguire quanto segue:

    azure keyvault set-policy --vault-name 'ContosoKeyVault' --spn 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed --perms-to-keys '["decrypt","sign"]'

Se si desidera autorizzare la stessa applicazione per la lettura di tutti i segreti nell'insieme di credenziali, eseguire le seguenti operazioni:

	azure keyvault set-policy --vault-name 'ContosoKeyVault' --spn 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed --perms-to-secrets '["get"]'

## Per usare un modulo di protezione hardware ##

Per una maggiore sicurezza, è possibile importare o generare le chiavi in moduli di protezione hardware (HSM) che rimangono sempre entro il limite HSM. I moduli di protezione hardware sono certificati per FIPS 140-2 livello 2. Se questo requisito non è applicabile, saltare questa sezione e andare a [Eliminare l'insieme di credenziali delle chiavi e le chiavi e i segreti associati](#delete-the-key-vault-and-associated-keys-and-secrets).

Per creare queste chiavi HSM protette, è necessaria una sottoscrizione all'insieme di credenziali che supporti le chiavi HSM protette.

Quando si crea l'insieme di credenziali, aggiungere il parametro 'sku':

    azure azure keyvault create --vault-name 'ContosoKeyVaultHSM' --resource-group 'ContosoResourceGroup' --location 'East Asia' --sku 'Premium'

È possibile aggiungere a questo insieme di credenziali chiavi protette tramite software (come illustrato in precedenza) e chiavi HSM protette. Per creare una chiave HSM protetta, impostare il parametro Destination su 'HSM':

    azure keyvault key create --vault-name 'ContosoKeyVaultHSM' --key-name 'ContosoFirstHSMKey' --destination 'HSM'

È possibile usare il comando seguente per importare una chiave da un file con estensione PEM nel computer. Questo comando importa la chiave nei moduli HSM nel servizio dell'insieme di credenziali delle chiavi:

    azure keyvault key import --vault-name 'ContosoKeyVaultHSM' --key-name 'ContosoFirstHSMKey' --pem-file '/.softkey.pem' --destination 'HSM' --password 'PaSSWORD'

Il comando successivo importa un pacchetto "bring your own key" (BYOK). Ciò consente di generare la chiave nel modulo HSM locale e di trasferirlo in moduli HSM nel servizio dell'insieme di credenziali delle chiavi, senza che la chiave esca dal limite HSM:

    azure keyvault key import --vault-name 'ContosoKeyVaultHSM' --key-name 'ContosoFirstHSMKey' --byok-file './ITByok.byok' --destination 'HSM'

Per istruzioni più dettagliate su come generare questo pacchetto BYOK, vedere [Come usare chiavi HSM protette con l'insieme di credenziali delle chiavi di Azure](key-vault-hsm-protected-keys.md).


## Eliminare l'insieme di credenziali delle chiavi e le chiavi e i segreti associati

Se l'insieme di credenziali delle chiavi e la chiave o il segreto associato non sono più necessari, è possibile eliminare l'insieme di credenziali delle chiavi usando il comando azure keyvault delete:

    azure keyvault delete --vault-name 'ContosoKeyVault'

In alternativa, è possibile eliminare l'intero gruppo di risorse di Azure, che include l'insieme di credenziali delle chiavi e tutte le altre risorse incluse in quel gruppo:

    azure group delete --name 'ContosoResourceGroup'


## Altri comandi dell'interfaccia della riga di comando multipiattaforma di Azure

Altri comandi che potrebbero essere utili per la gestione dell'insieme di credenziali delle chiavi di Azure.

Questo comando ottiene una visualizzazione tabulare di tutte le chiavi e le proprietà selezionate:

    azure keyvault key list --vault-name 'ContosoKeyVault'

Questo comando visualizza un elenco completo di proprietà per la chiave specificata:

    azure keyvault key show --vault-name 'ContosoKeyVault' --key-name 'ContosoFirstKey'

Questo comando ottiene una visualizzazione tabulare di tutti nomi dei segreti e tutte le proprietà selezionate:

    azure keyvault secret list --vault-name 'ContosoKeyVault'

Ecco un esempio di come rimuovere una chiave specifica:

    azure keyvault key delete --vault-name 'ContosoKeyVault' --key-name 'ContosoFirstKey'

Ecco un esempio di come rimuovere un segreto specifico:

    azure keyvault secret delete --vault-name 'ContosoKeyVault' --secret-name 'SQLPassword'


## Passaggi successivi

Per i riferimenti alla programmazione, vedere [Guida per gli sviluppatori dell’insieme di credenziali chiave Azure](key-vault-developers-guide.md).

<!---HONumber=AcomDC_0323_2016-->