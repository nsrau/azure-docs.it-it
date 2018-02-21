---
title: Introduzione all'insieme di credenziali delle chiavi di Azure | Documentazione Microsoft
description: "Usare questa esercitazione per imparare a eseguire facilmente le attività iniziali dell'insieme di credenziali delle chiavi di Azure per creare un contenitore finalizzato in Azure, in cui archiviare e gestire chiavi e segreti di crittografia in Azure."
services: key-vault
documentationcenter: 
author: barclayn
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: 36721e1d-38b8-4a15-ba6f-14ed5be4de79
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 11/20/2017
ms.author: barclayn
ms.openlocfilehash: 1b70802945b710059e93b54607996ccf74510d1f
ms.sourcegitcommit: f67f0bda9a7bb0b67e9706c0eb78c71ed745ed1d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2017
---
# <a name="get-started-with-azure-key-vault"></a>Introduzione all'insieme di credenziali delle chiavi di Azure
Questo articolo consente di iniziare a usare Azure Key Vault con PowerShell e illustra in dettaglio le attività seguenti:
- Come creare un contenitore finalizzato, ossia un insieme di credenziali, in Azure.
- Come usare Key Vault per archiviare e gestire chiavi crittografiche e segreti in Azure.
- Come un'applicazione può usare tale chiave o password.

L'insieme di credenziali delle chiavi di Azure è disponibile nella maggior parte delle aree. Per altre informazioni, vedere la [pagina Insieme di credenziali delle chiavi - Prezzi](https://azure.microsoft.com/pricing/details/key-vault/).

> [!NOTE]
> Questo articolo non include istruzioni su come scrivere un'applicazione Azure. Per queste procedure è possibile usare l'[applicazione di esempio per Azure Key Vault](https://www.microsoft.com/download/details.aspx?id=45343).

Per le istruzioni relative all'interfaccia della riga di comando multipiattaforma, vedere [questa esercitazione equivalente](key-vault-manage-with-cli2.md).

## <a name="requirements"></a>Requisiti
Prima di procedere con l'articolo, verificare di avere gli elementi seguenti:

- **Una sottoscrizione di Azure**. Se non si ha una sottoscrizione, è possibile iscriversi per ottenere un [account gratuito](https://azure.microsoft.com/pricing/free-trial/).
- **Azure PowerShell**, **versione minima 1.1.0**. Per installare Azure PowerShell e associarlo alla sottoscrizione di Azure, vedere [Come installare e configurare Azure PowerShell](/powershell/azure/overview). Se Azure PowerShell è già stato installato ma non si conosce la versione, dalla console di Azure PowerShell digitare `(Get-Module azure -ListAvailable).Version`. Se sono installate le versioni di Azure PowerShell dalla 0.9.1 alla 0.9.8, è comunque possibile svolgere questa esercitazione, con alcune piccole modifiche. Ad esempio, sarà necessario usare il comando `Switch-AzureMode AzureResourceManager` e alcuni comandi per l'insieme di credenziali delle chiavi di Azure sono cambiati. Per un elenco dei cmdlet per l'insieme di credenziali delle chiavi di Azure per le versioni dalla 0.9.1 alla 0.9.8, vedere [Azure Key Vault Cmdlets](/powershell/module/azurerm.keyvault/#key_vault) (Cmdlet per l'insieme di credenziali delle chiavi di Azure).
- **Applicazione configurabile per l'uso di Key Vault**. Un'applicazione di esempio è disponibile nell'[Area download Microsoft](http://www.microsoft.com/download/details.aspx?id=45343). Per istruzioni, vedere il file **Readme** associato.

>[!NOTE]
L'articolo presuppone una conoscenza di base di PowerShell e Azure. Per altre informazioni su PowerShell, vedere [Guida introduttiva a Windows PowerShell](https://technet.microsoft.com/library/hh857337.aspx).

Per informazioni dettagliate sui cmdlet usati in questa esercitazione, eseguire il cmdlet **Get-Help** .

```powershell-interactive
Get-Help <cmdlet-name> -Detailed
```
    
Ad esempio, per informazioni della Guida per il cmdlet **Login-AzureRmAccount** , digitare:

```PowerShell
Get-Help Login-AzureRmAccount -Detailed
```

Per acquisire familiarità con il modello di distribuzione Azure Resource Manager in Azure PowerShell, vedere anche gli articoli seguenti:

* [Come installare e configurare Azure PowerShell](/powershell/azure/overview)
* [Utilizzo di Azure PowerShell con Gestione risorse](../powershell-azure-resource-manager.md)

## <a id="connect"></a>Connettersi alle sottoscrizioni
Avviare una sessione di Azure PowerShell e accedere all'account Azure con il comando seguente:  

```PowerShell
Login-AzureRmAccount
```

>[!NOTE]
 Se si usa un'istanza specifica di Azure, usare il parametro -Environment, Ad esempio:  
 ```powershell
 Login-AzureRmAccount –Environment (Get-AzureRmEnvironment –Name AzureUSGovernment)
 ```

Nella finestra del browser a comparsa, immettere il nome utente e la password dell'account Azure. Azure PowerShell recupera tutte le sottoscrizioni associate a questo account e, per impostazione predefinita, usa la prima.

Se sono disponibili più sottoscrizioni e se ne vuole specificare una in particolare da usare per l'insieme di credenziali delle chiavi di Azure, digitare quanto segue per visualizzare le sottoscrizioni dell'account:

```powershell
Get-AzureRmSubscription
```

Quindi, per specificare la sottoscrizione da usare, digitare:

```powershell
Set-AzureRmContext -SubscriptionId <subscription ID>
```

Per altre informazioni sulla configurazione di Azure PowerShell, vedere [Come installare e configurare Azure PowerShell](/powershell/azure/overview).

## <a id="resource"></a>Creare un nuovo gruppo di risorse
Quando si usa Gestione risorse di Azure, tutte le risorse correlate vengono create in un gruppo di risorse. Per questa esercitazione verrà creato un nuovo gruppo di risorse denominato **ContosoResourceGroup** :

```powershell
New-AzureRmResourceGroup –Name 'ContosoResourceGroup' –Location 'East US'
```

## <a id="vault"></a>Creare un insieme di credenziali chiave
Usare il cmdlet [New-AzureRmKeyVault](/powershell/module/azurerm.keyvault/new-azurermkeyvault) per creare un insieme di credenziali delle chiavi. Questo cmdlet ha tre parametri obbligatori: un **nome di gruppo di risorse**, un **nome di insieme di credenziali delle chiavi** e la **località geografica**.

Se ad esempio si usano:
- **ContosoKeyVault** come nome dell'insieme di credenziali
- **ContosoResourceGroup** come nome del gruppo di risorse
- **East US** come località

si digiterà:

```powershell
New-AzureRmKeyVault -VaultName 'ContosoKeyVault' -ResourceGroupName 'ContosoResourceGroup' -Location 'East US'
```
![Output al termine del comando di creazione dell'insieme di credenziali delle chiavi](./media/key-vault-get-started/output-after-creating-keyvault.png)

L'output di questo cmdlet mostra le proprietà dell'insieme di credenziali delle chiavi creato. Le due proprietà più importanti sono:

* **Nome dell'insieme di credenziali**: nell'esempio corrisponde a **ContosoKeyVault**. Questo nome verrà usato per altri cmdlet di insieme di credenziali delle chiavi.
* **Vault URI** (URI dell'insieme di credenziali): nell'esempio corrisponde a https://contosokeyvault.vault.azure.net/. Le applicazioni che usano l'insieme di credenziali tramite l'API REST devono usare questo URI.

L'account Azure ora è autorizzato a eseguire qualsiasi operazione su questo insieme di credenziali delle chiavi. Nessun altro lo è ancora.

> [!NOTE]
> Quando si prova a creare il nuovo insieme di credenziali delle chiavi, potrebbe essere visualizzato il messaggio di errore **La sottoscrizione non è registrata per l'uso dello spazio dei nomi 'Microsoft.KeyVault'**. Se viene visualizzato tale messaggio, eseguire `Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.KeyVault"`. Al termine della registrazione, si può eseguire di nuovo il comando New-AzureRmKeyVault. Per altre informazioni, vedere [Register-AzureRmResourceProvider](/powershell/module/azurerm.resources/register-azurermresourceprovider).
>
>

## <a id="add"></a>Aggiungere una chiave o un segreto all'insieme di credenziali chiave
Potrebbe essere necessario interagire con Key Vault e chiavi o segreti in alcuni modi diversi.

### <a name="azure-key-vault-generates-a-software-protected-key"></a>Generazione di una chiave protetta tramite software in Azure Key Vault

Se si vuole che Azure Key Vault crei una chiave protetta tramite software, usare il cmdlet [Add-AzureKeyVaultKey](/powershell/module/azurerm.keyvault/add-azurekeyvaultkey) e digitare:

```powershell
$key = Add-AzureKeyVaultKey -VaultName 'ContosoKeyVault' -Name 'ContosoFirstKey' -Destination 'Software'
```
Per visualizzare l'URI per questa chiave, digitare:
```powershell
$key.id
```

Si può fare riferimento a una chiave creata o caricata in Azure Key Vault usando il relativo URI. È possibile usare **https://ContosoKeyVault.vault.azure.net/keys/ContosoFirstKey** per ottenere la versione corrente e **https://ContosoKeyVault.vault.azure.net/keys/ContosoFirstKey/cgacf4f763ar42ffb0a1gca546aygd87** per ottenere questa versione specifica.  

### <a name="importing-an-existing-pfx-file-into-azure-key-vault"></a>Importazione di un file PFX esistente in Azure Key Vault

Se si vogliono caricare in Azure Key Vault chiavi esistenti archiviate in un file PFX, la procedura è diversa. Ad esempio: 
- Se una chiave protetta tramite software esistente è contenuta in un file PFX
- Il file PFX è denominato softkey.pfx 
- Il file è archiviato nell'unità C

È possibile digitare:

```powershell
$securepfxpwd = ConvertTo-SecureString –String '123' –AsPlainText –Force  // This stores the password 123 in the variable $securepfxpwd
```

Digitare poi quanto segue per importare la chiave dal file PFX, che protegge la chiave tramite software nel servizio dell'insieme di credenziali delle chiavi:

```powershell
$key = Add-AzureKeyVaultKey -VaultName 'ContosoKeyVault' -Name 'ContosoImportedPFX' -KeyFilePath 'c:\softkey.pfx' -KeyFilePassword $securepfxpwd
```

Per visualizzare l'URI per questa chiave, digitare:

```powershell
$Key.id
```
Per visualizzare la chiave, digitare: 

```powershell
Get-AzureKeyVaultKey –VaultName 'ContosoKeyVault'
```
Se si vogliono visualizzare le proprietà del file PFX nel portale, la schermata visualizzata sarà simile all'immagine seguente.

![Visualizzazione di un certificato nel portale](./media/key-vault-get-started/imported-pfx.png)
### <a name="to-add-a-secret-to-azure-key-vault"></a>Per aggiungere un segreto ad Azure Key Vault

Per aggiungere un segreto all'insieme di credenziali, ossia una password denominata SQLPassword con valore Pa$$w0rd per Azure Key Vault, per prima cosa convertire il valore Pa$$w0rd in una stringa sicura digitando quanto segue:

```powershell    
$secretvalue = ConvertTo-SecureString 'Pa$$w0rd' -AsPlainText -Force
```

Digitare quindi:

```powershell
$secret = Set-AzureKeyVaultSecret -VaultName 'ContosoKeyVault' -Name 'SQLPassword' -SecretValue $secretvalue
```


È ora possibile fare riferimento a questa password aggiunta nell'insieme di credenziali delle chiavi di Azure, usando il relativo URI. Usare **https://ContosoVault.vault.azure.net/secrets/SQLPassword** per ottenere sempre la versione corrente e usare **https://ContosoVault.vault.azure.net/secrets/SQLPassword/90018dbb96a84117a0d2847ef8e7189d** per ottenere questa versione specifica.

Per visualizzare l'URI per questo segreto, digitare:

```powershell
$secret.Id
```
Per visualizzare il segreto, digitare `Get-AzureKeyVaultSecret –VaultName 'ContosoKeyVault'`. In alternativa, è possibile visualizzare il segreto nel portale.

![secret](./media/key-vault-get-started/secret-value.png)

Per visualizzare il valore contenuto nel segreto come testo normale:
```powershell
(get-azurekeyvaultsecret -vaultName "Contosokeyvault" -name "SQLPassword").SecretValueText
```
Ora l'insieme di credenziali delle chiavi e la chiave o il segreto sono pronti per essere usati dalle applicazioni. È necessario autorizzare le applicazioni a usarli.  

## <a id="register"></a>Registrare un'applicazione con Azure Active Directory
Questo passaggio di solito viene eseguito da uno sviluppatore, su un computer separato. Non è specifico di Azure Key Vault. Per la procedura dettagliata per registrare un'applicazione in Azure Active Directory, vedere l'articolo [Integrazione di applicazioni con Azure Active Directory](../active-directory/develop/active-directory-integrating-applications.md) o [Usare il portale per creare un'applicazione Azure Active Directory e un'entità servizio che possano accedere alle risorse](../azure-resource-manager/resource-group-create-service-principal-portal.md).

> [!IMPORTANT]
> Per completare l'esercitazione, l'account, l'insieme di credenziali e l'applicazione in cui si registrerà questo passaggio devono essere tutti nella stessa directory di Azure.


Le applicazioni che usano un insieme di credenziali delle chiavi devono eseguire l'autenticazione con un token di Azure Active Directory. A tale scopo, il proprietario dell'applicazione deve innanzitutto registrare l'applicazione in Azure Active Directory. Al termine della registrazione, il proprietario dell'applicazione ottiene i valori seguenti:

- **ID applicazione** 
- **Chiave di autenticazione**, detta anche segreto condiviso 

L'applicazione deve presentare entrambi questi valori ad Azure Active Directory, per ottenere un token. La configurazione dell'applicazione per eseguire questa operazione dipende dall'applicazione. Per l'[applicazione di esempio per l'insieme di credenziali delle chiavi](https://www.microsoft.com/download/details.aspx?id=45343), il proprietario dell'applicazione imposta questi valori nel file app.config.


Per registrare l'applicazione in Azure Active Directory:

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Fare clic su **Registrazioni per l'app** a sinistra. Se la voce Registrazioni per l'app non viene visualizzata, fare clic su **Altri servizi** per cercarla.  
>[!NOTE]
È necessario selezionare la stessa directory che contiene la sottoscrizione di Azure con cui è stato creato l'insieme di credenziali delle chiavi. 
3. Fare clic su **Registrazione nuova applicazione**.
4. Nel pannello **Crea** specificare un nome per l'applicazione e quindi selezionare **Applicazione Web e/o API Web** (impostazione predefinita) e specificare l'**URL di accesso** per l'applicazione Web. Se al momento non si hanno queste informazioni, per questo passaggio si può specificare qualsiasi valore, ad esempio http://test1.contoso.com. Non è importante se questi siti esistono. 

    ![Registrazione nuova applicazione](./media/key-vault-get-started/new-application-registration.png)
    >[!WARNING]
    Assicurarsi di scegliere **Applicazione Web e/o API Web**. In caso contrario, nelle impostazioni non verrà visualizzata l'opzione **chiavi**.

5. Selezionare il pulsante **Create** .
6. Al termine della registrazione dell'app, verrà visualizzato l'elenco delle app registrate. Trovare l'app appena registrata e fare clic su di essa.
7. Fare clic sul pannello **App registrata** e copiare l'**ID applicazione**.
8. Fare clic su **Tutte le impostazioni**.
9. Nel pannello **Impostazioni** fare clic su **chiavi**.
9. Digitare una descrizione nella casella **Descrizione chiave**, selezionare una durata e quindi fare clic su **SALVA**. La pagina viene aggiornata e mostra un valore chiave. 
10. Le informazioni di **ID applicazione** e **Chiave** verranno usate nel passaggio successivo per impostare le autorizzazioni per l'insieme di credenziali.

## <a id="authorize"></a>Autorizzare l'applicazione a usare la chiave o il segreto
Per autorizzare l'applicazione ad accedere alla chiave o al segreto nell'insieme di credenziali, usare il cmdlet [Set-AzureRmKeyVaultAccessPolicy](/powershell/module/azurerm.keyvault/set-azurermkeyvaultaccesspolicy).

Ad esempio, se il nome dell'insieme di credenziali è **ContosoKeyVault** e l'applicazione da autorizzare ha un ID client 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed e si vuole autorizzare l'applicazione a decrittografare e firmare con le chiavi dell'insieme di credenziali, digitare il comando seguente:

```powershell
Set-AzureRmKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -ServicePrincipalName 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed -PermissionsToKeys decrypt,sign
```

Se si desidera autorizzare la stessa applicazione per la lettura di tutti i segreti nell'insieme di credenziali, eseguire le seguenti operazioni:

```powershell
Set-AzureRmKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -ServicePrincipalName 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed -PermissionsToSecrets Get
```

## <a id="HSM"></a>Uso di un modulo di protezione hardware
Per una maggiore sicurezza, è possibile importare o generare le chiavi in moduli di protezione hardware (HSM) che rimangono sempre entro il limite HSM. I moduli di protezione hardware sono certificati per FIPS 140-2 livello 2. Se questo requisito non è applicabile, saltare questa sezione e andare a [Eliminare l'insieme di credenziali chiave e le chiavi e i segreti associati](#delete).

Per creare queste chiavi protette dal modulo di protezione hardware, è necessario usare il [livello di servizio Premium dell'insieme di credenziali delle chiavi di Azure che supporta le chiavi protette dal modulo di protezione hardware](https://azure.microsoft.com/pricing/free-trial/). Si noti anche che questa funzionalità non è disponibile nella versione Azure per la Cina.

Quando si crea l'insieme di credenziali delle chiavi, aggiungere il parametro **-SKU**:

```powershell
New-AzureRmKeyVault -VaultName 'ContosoKeyVaultHSM' -ResourceGroupName 'ContosoResourceGroup' -Location 'East US' -SKU 'Premium'
```


A questo insieme di credenziali delle chiavi è possibile aggiungere chiavi protette tramite software, come descritto in precedenza, e chiavi protette dal modulo di protezione hardware. Per creare una chiave HSM protetta, impostare il parametro **Destinazione** su 'HSM':

```powershell
$key = Add-AzureKeyVaultKey -VaultName 'ContosoKeyVaultHSM' -Name 'ContosoFirstHSMKey' -Destination 'HSM'
```

È possibile usare il comando seguente per importare una chiave da un file PFX sul computer. Questo comando importa la chiave nei moduli HSM nel servizio dell'insieme di credenziali delle chiavi:

```powershell
$key = Add-AzureKeyVaultKey -VaultName 'ContosoKeyVaultHSM' -Name 'ContosoFirstHSMKey' -KeyFilePath 'c:\softkey.pfx' -KeyFilePassword $securepfxpwd -Destination 'HSM'
```

Il comando successivo importa un pacchetto "bring your own key" (BYOK). Questo scenario consente di generare la chiave nel modulo di protezione hardware locale e di trasferirla in moduli di protezione hardware nel servizio dell'insieme di credenziali delle chiavi, facendo in modo che rimanga entro il limite del modulo di protezione hardware:

```powershell
$key = Add-AzureKeyVaultKey -VaultName 'ContosoKeyVaultHSM' -Name 'ContosoFirstHSMKey' -KeyFilePath 'c:\ITByok.byok' -Destination 'HSM'
```

Per istruzioni più dettagliate su come generare questo pacchetto BYOK, vedere [Come generare e trasferire chiavi HSM protette per l'insieme di credenziali delle chiavi di Azure](key-vault-hsm-protected-keys.md).

## <a id="delete"></a>Eliminare l'insieme di credenziali chiave e le chiavi e i segreti associati
Se l'insieme di credenziali delle chiavi e la chiave o il segreto associato non sono più necessari, è possibile eliminare l'insieme di credenziali delle chiavi usando il cmdlet [Remove-AzureRmKeyVault](/powershell/module/azurerm.keyvault/remove-azurermkeyvault):

```powershell
Remove-AzureRmKeyVault -VaultName 'ContosoKeyVault'
```

In alternativa, è possibile eliminare l'intero gruppo di risorse di Azure, che include l'insieme di credenziali delle chiavi e tutte le altre risorse incluse in quel gruppo:

```powershell
Remove-AzureRmResourceGroup -ResourceGroupName 'ContosoResourceGroup'
```

## <a id="other"></a>Altri cmdlet di Azure PowerShell
Altri comandi che potrebbero essere utili per la gestione dell'insieme di credenziali delle chiavi di Azure:

- `$Keys = Get-AzureKeyVaultKey -VaultName 'ContosoKeyVault'`: questo comando ottiene una visualizzazione tabulare di tutte le chiavi e le proprietà selezionate.
- `$Keys[0]`: questo comando visualizza un elenco completo di proprietà per la chiave specificata.
- `Get-AzureKeyVaultSecret`: questo comando elenca in una visualizzazione tabulare tutti i nomi di segreto e le proprietà selezionate.
- `Remove-AzureKeyVaultKey -VaultName 'ContosoKeyVault' -Name 'ContosoFirstKey'`: esempio di come rimuovere una chiave specifica.
- `Remove-AzureKeyVaultSecret -VaultName 'ContosoKeyVault' -Name 'SQLPassword'`: esempio di come rimuovere un segreto specifico.

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni generali sull'insieme di credenziali di Azure, vedere [Cos'è l'insieme di credenziali delle chiavi di Azure?](key-vault-whatis.md)
- Per informazioni sull'uso dell'insieme di credenziali delle chiavi, vedere [Registrazione dell'insieme di credenziali delle chiavi di Azure](key-vault-logging.md).
- Per un'esercitazione successiva sull'uso dell'insieme di credenziali delle chiavi di Azure in un'applicazione Web, vedere [Usare l'insieme di credenziali delle chiavi di Azure da un'applicazione Web](key-vault-use-from-web-application.md).
- Per i riferimenti alla programmazione, vedere [Guida per gli sviluppatori dell'insieme di credenziali chiave Azure](key-vault-developers-guide.md).
- Per un elenco dei cmdlet di Azure PowerShell più recenti per l'insieme di credenziali delle chiavi di Azure, vedere [Azure Key Vault Cmdlets](/powershell/module/azurerm.keyvault/#key_vault) (Cmdlet per l'insieme di credenziali delle chiavi di Azure).