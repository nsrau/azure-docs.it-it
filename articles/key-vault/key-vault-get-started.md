<properties
	pageTitle="Introduzione all'insieme di credenziali delle chiavi di Azure | Microsoft Azure"
	description="Usare questa esercitazione per imparare a eseguire facilmente le attività iniziali dell'insieme di credenziali delle chiavi di Azure per creare un contenitore finalizzato in Azure, in cui archiviare e gestire chiavi e segreti di crittografia in Azure."
	services="key-vault"
	documentationCenter=""
	authors="cabailey"
	manager="mbaldwin"
	tags="azure-resource-manager"/>

<tags
	ms.service="key-vault"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="10/08/2015"
	ms.author="cabailey"/>

# Introduzione all'insieme di credenziali delle chiavi di Azure #
L'insieme di credenziali delle chiavi di Azure è disponibile nella maggior parte delle aree. Per altre informazioni, vedere la [pagina Insieme di credenziali delle chiavi - Prezzi](../../../../pricing/details/key-vault/).

## Introduzione  
Usare questa esercitazione per imparare a eseguire facilmente le attività iniziali dell'insieme di credenziali delle chiavi di Azure per creare un contenitore finalizzato (insieme di credenziali) in Azure, in cui archiviare e gestire chiavi crittografiche e segreti in Azure. Verrà inoltre descritto come usare Windows PowerShell per creare un insieme di credenziali contenente una chiave o una password che sarà possibile usare con un'applicazione Azure. L'esercitazione spiega poi come un'applicazione può usare questa chiave o password.

*Tempo previsto per il completamento:** 20 minuti

>[AZURE.NOTE]Questa esercitazione non include le istruzioni per scrivere l'applicazione Azure usata nel passaggio che spiega come autorizzare un'applicazione a usare una chiave o un segreto nell'insieme di credenziali delle chiavi.
>
>Attualmente non è possibile configurare l'insieme di credenziali delle chiavi di Azure nel portale di Azure. Usare invece queste istruzioni per Azure PowerShell. In alternativa, per le istruzioni relative all'interfaccia della riga di comando multipiattaforma, vedere [questa esercitazione equivalente](key-vault-manage-with-cli.md).

Per informazioni generali sull'insieme di credenziali di Azure, vedere [Cos'è l'insieme di credenziali delle chiavi di Azure?](key-vault-whatis.md)

## Prerequisiti

Per completare l'esercitazione, sono necessari gli elementi seguenti:

- Una sottoscrizione a Microsoft Azure. Se non si dispone di una sottoscrizione, è possibile iscriversi per una [versione di valutazione gratuita](../../../../pricing/free-trial).
- Versione Azure PowerShell 0.9.1 tramite 0.9.8. Per installare Azure Powershell e associarla alla sottoscrizione di Azure, vedere [Come installare e configurare Azure PowerShell](../powershell-install-configure.md).
- Un'applicazione che verrà configurata per usare la chiave o la password creata in questa esercitazione. Un'applicazione di esempio è disponibile nell'[Area download Microsoft](http://www.microsoft.com/it-IT/download/details.aspx?id=45343). Per istruzioni, vedere il file Readme associato.


Questa esercitazione è stata ideata per gli utenti di Windows PowerShell non esperti, ma presuppone che si conoscano i concetti di base, come i moduli, i cmdlet e le sessioni. Per altre informazioni su Windows PowerShell, vedere [Introduzione a Windows PowerShell](https://technet.microsoft.com/library/hh857337.aspx).

Per informazioni dettagliate sui cmdlet usati in questa esercitazione, eseguire il cmdlet **Get-Help**.

	Get-Help <cmdlet-name> -Detailed

Ad esempio, per informazioni sul cmdlet **Add-AzureAccount**, digitare:

	Get-Help Add-AzureAccount -Detailed

Inoltre si possono leggere le seguenti esercitazioni per acquisire familiarità con Gestione risorse di Azure in Windows PowerShell:

- [Come installare e configurare Azure PowerShell](../powershell-install-configure.md)
- [Uso di Windows PowerShell con Gestione risorse](../powershell-azure-resource-manager.md)


## <a id="connect"></a>Connettersi alle sottoscrizioni ##

Avviare una sessione di Azure PowerShell e accedere all'account Azure con il comando seguente:

    Add-AzureAccount

Nella finestra del browser a comparsa, immettere il nome utente e la password dell'account Azure. Windows PowerShell recupererà tutte le sottoscrizioni associate a questo account e, per impostazione predefinita, usa la prima.

Se si dispone di più sottoscrizioni e se ne vuole specificare una in particolare da usare per l'insieme di credenziali delle chiavi di Azure, digitare quanto segue per visualizzare le sottoscrizioni per il proprio account:

    Get-AzureSubscription

Quindi, per specificare la sottoscrizione da usare, digitare.

    Select-AzureSubscription -SubscriptionName <subscription name>

Per altre informazioni sulla configurazione di Azure PowerShell, vedere [Come installare e configurare Azure PowerShell](../powershell-install-configure.md).

## <a id="switch"></a>Passare all'uso di Gestione risorse di Azure ##

Poiché per i cmdlet dell'insieme di credenziali delle chiavi è necessario Gestione risorse di Azure, digitare quanto segue per passare alla modalità Gestione risorse di Azure:

	Switch-AzureMode AzureResourceManager

## <a id="resource"></a>Creare un nuovo gruppo di risorse ##

Quando si usa Gestione risorse di Azure, tutte le risorse correlate vengono create in un gruppo di risorse. Per questa esercitazione verrà creato un nuovo gruppo di risorse denominato **ContosoResourceGroup**:

	New-AzureResourceGroup –Name 'ContosoResourceGroup' –Location 'East Asia'

Per il parametro [-Percorso](https://msdn.microsoft.com/library/azure/dn654582.aspx), usare il comando **Get-AzureLocation** per identificare come specificare una posizione alternativa a quella di questo esempio. Se servono altre informazioni, digitare: `Get-Help Get-AzureLocation`


## <a id="vault"></a>Creare un insieme di credenziali chiave ##

Usare il cmdlet [New-AzureKeyVault](https://msdn.microsoft.com/library/azure/dn903602.aspx) per creare un insieme di credenziali chiave. Questo cmdlet ha tre parametri obbligatori: un **nome di gruppo di risorse**, un **nome di insieme di credenziali chiave** e la **località geografica**.

Ad esempio, se si usa il nome di insieme di credenziali **ContosoKeyVault**, il nome di gruppo di risorse **ContosoResourceGroup** e la località **East Asia**, digitare:

    New-AzureKeyVault -VaultName 'ContosoKeyVault' -ResourceGroupName 'ContosoResourceGroup' -Location 'East Asia'

L'output di questo cmdlet mostra le proprietà dell'insieme di credenziali delle chiavi appena creato. Le due proprietà più importanti sono:

- **Nome**: nell'esempio corrisponde a **ContosoKeyVault**. Questo nome verrà usato per altri cmdlet di insieme di credenziali delle chiavi.
- **vaultUri**: nell'esempio corrisponde a https://contosokeyvault.vault.azure.net/. Le applicazioni che usano l'insieme di credenziali tramite l'API REST devono usare questo URI.

L'account Azure ora è autorizzato a eseguire qualsiasi operazione su questo insieme di credenziali delle chiavi. Nessun altro lo è ancora.

## <a id="add"></a>Aggiungere una chiave o un segreto all'insieme di credenziali chiave ##

Per usare l'insieme di credenziali chiave di Azure per creare automaticamente una chiave protetta tramite software, eseguire il cmdlet [Add-AzureKeyVaultKey](https://msdn.microsoft.com/library/azure/dn868048.aspx) digitando quanto segue:

    $key = Add-AzureKeyVaultKey -VaultName 'ContosoKeyVault' -Name 'ContosoFirstKey' -Destination 'Software'

Se però in un file PFX salvato sull'unità C:\\ e denominato softkey.pfx esiste già una chiave protetta tramite software che si vuole caricare nell'insieme di credenziali chiave di Azure, digitare quanto segue per impostare la variabile **securepfxpwd** per la password **123** per il file PFX:

    $securepfxpwd = ConvertTo-SecureString –String '123' –AsPlainText –Force

Digitare poi quanto segue per importare la chiave dal file PFX, che protegge la chiave tramite software nel servizio dell'insieme di credenziali delle chiavi:

    $key = Add-AzureKeyVaultKey -VaultName 'ContosoKeyVault' -Name 'ContosoFirstKey' -KeyFilePath 'c:\softkey.pfx' -KeyFilePassword $securepfxpwd


È ora possibile fare riferimento a questa chiave creata o caricata nell'insieme di credenziali delle chiavi di Azure, usando il relativo URI. Usare ****https://ContosoKeyVault.vault.azure.net/keys/ContosoFirstKey** per ottenere sempre la versione corrente e usare ****https://ContosoKeyVault.vault.azure.net/keys/ContosoFirstKey/cgacf4f763ar42ffb0a1gca546aygd87** per ottenere questa versione specifica.

Per visualizzare l'URI per questa chiave, digitare:

	$Key.key.kid

Per aggiungere un segreto all'insieme di credenziali, ovvero una password denominata SQLPassword con il valore Pa$$w0rd, convertire innanzitutto il valore Pa$$w0rd in una stringa sicura digitando quanto segue:

	$secretvalue = ConvertTo-SecureString 'Pa$$w0rd' -AsPlainText -Force

Digitare quindi quanto segue:

	$secret = Set-AzureKeyVaultSecret -VaultName 'ContosoKeyVault' -Name 'SQLPassword' -SecretValue $secretvalue

È ora possibile fare riferimento a questa password aggiunta nell'insieme di credenziali delle chiavi di Azure, usando il relativo URI. Usare ****https://ContosoVault.vault.azure.net/secrets/SQLPassword** per ottenere sempre la versione corrente e usare ****https://ContosoVault.vault.azure.net/secrets/SQLPassword/90018dbb96a84117a0d2847ef8e7189d** per ottenere questa versione specifica.

Per visualizzare l'URI per questo segreto, digitare:

	$secret.Id

Ora si può visualizzare la chiave o il segreto appena creato:

- Per visualizzare la chiave, digitare: `Get-AzureKeyVaultKey –VaultName 'ContosoKeyVault'`
- Per visualizzare il segreto, digitare: `Get-AzureKeyVaultSecret –VaultName 'ContosoKeyVault'`

Ora l'insieme di credenziali delle chiavi e la chiave o il segreto sono pronti per essere usati dalle applicazioni. È necessario autorizzare le applicazioni a usarli.

## <a id="register"></a>Registrare un'applicazione con Azure Active Directory ##

Questo passaggio di solito viene eseguito da uno sviluppatore, su un computer separato. Anche se non è specifico dell'insieme di credenziali delle chiavi di Azure, viene incluso qui per completezza.


>[AZURE.IMPORTANT]Per completare l'esercitazione, l'account, l'insieme di credenziali e l'applicazione in cui si registrerà questo passaggio devono essere tutti nella stessa directory di Azure.

Le applicazioni che usano un insieme di credenziali delle chiavi devono eseguire l'autenticazione con un token di Azure Active Directory. A tale scopo, il proprietario dell'applicazione deve innanzitutto registrare l'applicazione in Azure Active Directory. Al termine della registrazione, il proprietario dell'applicazione ottiene i valori seguenti:


- Un **ID applicazione** (chiamato anche ID client) e una **chiave di autenticazione** (chiamata anche segreto condiviso). L'applicazione deve presentare entrambi questi valori ad Azure Active Directory, per ottenere un token. La configurazione dell'applicazione per eseguire questa operazione dipende dall'applicazione. Per l'applicazione di esempio dell'insieme di credenziali delle chiavi, il proprietario dell'applicazione imposta questi valori nel file app.config.

Per registrare l'applicazione in Azure Active Directory:

1. Accedere al portale di Azure.
2. A sinistra fare clic su **Active Directory** e quindi selezionare la directory in cui si registrerà l'applicazione. <br><br>** Nota**: è necessario selezionare la stessa directory che contiene la sottoscrizione di Azure con cui si è creato l'insieme di credenziali chiave. Se non si sa quale directory selezionare, fare clic su **Impostazioni**, identificare la sottoscrizione con cui si è creato l'insieme di credenziali chiave e prendere nota del nome della directory visualizzata nell'ultima colonna.

3. Fare clic su **APPLICAZIONI**. Se nessuna app è stata aggiunta alla directory, questa pagina mostrerà solo il collegamento **Aggiungi app**. Fare clic sul collegamento. In alternativa, è possibile fare clic su **AGGIUNGI** sulla barra dei comandi.
4.	Nella procedura guidata **AGGIUNGI APPLICAZIONE**, nella pagina **Come procedere** fare clic su **Aggiungi un'applicazione che l'organizzazione sta sviluppando**.
5.	Nella pagina **Informazioni sull'applicazione** specificare un nome per l'applicazione e selezionare **APPLICAZIONE WEB E/O API WEB** (pagina predefinita). Fare clic sull'icona **Avanti**.
6.	Nella pagina **Proprietà dell'app** specificare **URL ACCESSO** e **URI ID APP** per l'applicazione Web. Se l'applicazione non dispone di questi valori, è possibile crearli per questo passaggio (ad esempio, è possibile specificare http://test1.contoso.com per entrambe le caselle). Non è importante se questi siti esistono. Ciò che conta è che ogni applicazione nella directory abbia un URI ID app diverso. La directory usa questa stringa per identificare l'app.
7.	Fare clic sull'icona **Completa** per salvare le modifiche nella procedura guidata.
8.	Nella pagina **Avvio rapido** fare clic su **CONFIGURA**.
9.	Scorrere fino alla sezione **chiavi**, selezionare la durata e quindi fare clic su **SALVA**. La pagina viene aggiornata e mostra un valore chiave. È necessario configurare l'applicazione con questo valore chiave e con il valore **ID CLIENT**. Le istruzioni per questa configurazione saranno specifiche dell'applicazione.
10.	Copiare da questa pagina il valore dell'ID client che si userà nel passaggio successivo per impostare le autorizzazioni per l'insieme di credenziali.

## <a id="authorize"></a>Autorizzare l'applicazione a usare la chiave o il segreto ##

Per autorizzare l'applicazione ad accedere alla chiave o al segreto nell'insieme di credenziali, usare il cmdlet [Set-AzureKeyVaultAccessPolicy](https://msdn.microsoft.com/library/azure/dn903607.aspx).

Ad esempio, se il nome dell'insieme di credenziali è **ContosoKeyVault** e l'applicazione che si desidera autorizzare ha un ID client 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed e si vuole autorizzare l'applicazione a decrittografare e firmare con le chiavi dell'insieme di credenziali, eseguire quanto segue:


	Set-AzureKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -ServicePrincipalName 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed -PermissionsToKeys decrypt,sign

Se si desidera autorizzare la stessa applicazione per la lettura di tutti i segreti nell'insieme di credenziali, eseguire le seguenti operazioni:


	Set-AzureKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -ServicePrincipalName 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed -PermissionsToSecrets Get

## <a id="HSM"></a>Per usare un modulo di protezione hardware ##

Per una maggiore sicurezza, è possibile importare o generare le chiavi in moduli di protezione hardware (HSM) che rimangono sempre entro il limite HSM. I moduli di protezione hardware sono certificati per FIPS 140-2 livello 2. Se questo requisito non è applicabile, saltare questa sezione e andare a [Eliminare l'insieme di credenziali chiave e le chiavi e i segreti associati](#delete).

Per creare queste chiavi HSM protette, è necessaria una [sottoscrizione all'insieme di credenziali che supporti le chiavi HSM protette](../../../pricing/free-trial).


Quando si crea l'insieme di credenziali, aggiungere il parametro **-SKU**:


	New-AzureKeyVault -VaultName 'ContosoKeyVaultHSM' -ResourceGroupName 'ContosoResourceGroup' -Location 'East Asia' -SKU 'Premium'



È possibile aggiungere a questo insieme di credenziali chiavi protette tramite software (come illustrato in precedenza) e chiavi HSM protette. Per creare una chiave HSM protetta, impostare il parametro **-Destination** su 'HSM':

	$key = Add-AzureKeyVaultKey -VaultName 'ContosoKeyVaultHSM' -Name 'ContosoFirstHSMKey' -Destination 'HSM'

È possibile usare il comando seguente per importare una chiave da un file PFX sul computer. Questo comando importa la chiave nei moduli HSM nel servizio dell'insieme di credenziali delle chiavi:

	$key = Add-AzureKeyVaultKey -VaultName 'ContosoKeyVaultHSM' -Name 'ContosoFirstHSMKey' -KeyFilePath 'c:\softkey.pfx' -KeyFilePassword $securepfxpwd -Destination 'HSM'


Il comando successivo importa un pacchetto "bring your own key" (BYOK). Ciò consente di generare la chiave nel modulo HSM locale e di trasferirlo in moduli HSM nel servizio dell'insieme di credenziali delle chiavi, senza che la chiave esca dal limite HSM:

	$key = Add-AzureKeyVaultKey -VaultName 'ContosoKeyVaultHSM' -Name 'ContosoFirstHSMKey' -KeyFilePath 'c:\ITByok.byok' -Destination 'HSM'

Per istruzioni più dettagliate su come generare questo pacchetto BYOK, vedere [Come generare e trasferire chiavi HSM protette per l'insieme di credenziali delle chiavi di Azure](key-vault-hsm-protected-keys.md).

## <a id="delete"></a>Eliminare l'insieme di credenziali chiave e le chiavi e i segreti associati ##

Se l'insieme di credenziali chiave e la chiave o il segreto associati non sono più necessari, è possibile eliminare l'insieme di credenziali chiave usando il cmdlet [Remove-AzureKeyVault](https://msdn.microsoft.com/library/azure/dn903603.aspx):

	Remove-AzureKeyVault -VaultName 'ContosoKeyVault'

In alternativa, è possibile eliminare l'intero gruppo di risorse di Azure, che include l'insieme di credenziali delle chiavi e tutte le altre risorse incluse in quel gruppo:

	Remove-AzureResourceGroup -ResourceGroupName 'ContosoResourceGroup'


## <a id="other"></a>Altri cmdlet di Azure PowerShell ##

Altri comandi che potrebbero essere utili per la gestione dell'insieme di credenziali delle chiavi di Azure:

- `$Keys = Get-AzureKeyVaultKey -VaultName 'ContosoKeyVault'`: questo comando ottiene una visualizzazione tabulare di tutte le chiavi e le proprietà selezionate.
- `$Keys[0]`: questo comando visualizza un elenco completo di proprietà per la chiave specificata.
- `Get-AzureKeyVaultSecret`: questo comando elenca in una visualizzazione tabulare tutti i nomi di segreto e le proprietà selezionate.
- `Remove-AzureKeyVaultKey -VaultName 'ContosoKeyVault' -Name 'ContosoFirstKey'`: esempio di come rimuovere una chiave specifica.
- `Remove-AzureKeyVaultSecret -VaultName 'ContosoKeyVault' -Name 'SQLPassword'`: esempio di come rimuovere un segreto specifico.


## <a id="next"></a>Passaggi successivi ##

Per un'esercitazione successiva sull'uso dell'insieme di credenziali delle chiavi di Azure in un'applicazione Web, vedere [Usare l'insieme di credenziali delle chiavi di Azure da un'applicazione Web](key-vault-use-from-web-application.md).

Per un elenco di cmdlet di Windows PowerShell per l'insieme di credenziali chiave di Azure, vedere [Cmdlet per l'insieme di credenziali chiave di Azure](https://msdn.microsoft.com/library/azure/dn868052.aspx).

Per i riferimenti alla programmazione, vedere [Guida per gli sviluppatori dell’insieme di credenziali chiave Azure](key-vault-developers-guide.md).

<!---HONumber=Oct15_HO2-->