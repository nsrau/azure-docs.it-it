---
title: Gestire Azure Key Vault tramite l'interfaccia della riga di comando | Microsoft Docs
description: "Usare questa esercitazione per automatizzare le attività comuni nell'insieme di credenziali delle chiavi tramite l'interfaccia della riga di comando"
services: key-vault
documentationcenter: 
author: barclayn
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: 66be6e44-684a-411b-802e-884628458ae7
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/14/2017
ms.author: barclayn
ms.openlocfilehash: 94ea95e7f40c8d47dd18422a9c0795655dae365b
ms.sourcegitcommit: 7d107bb9768b7f32ec5d93ae6ede40899cbaa894
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/16/2017
---
# <a name="manage-key-vault-using-cli"></a>Gestire l'insieme di credenziali delle chiavi tramite l'interfaccia della riga di comando

Usare questa esercitazione per imparare a usare l'insieme di credenziali delle chiavi di Azure per creare un contenitore finalizzato (insieme di credenziali) in Azure. L'insieme di credenziali delle chiavi di Azure viene usato per archiviare e gestire chiavi crittografiche e segreti. In questo articolo vengono fornite istruzioni dettagliate su come usare l'interfaccia della riga di comando multipiattaforma di Azure per creare un insieme di credenziali. L'insieme di credenziali verrà quindi usato per eseguire diverse operazioni comuni. 

L'insieme di credenziali delle chiavi di Azure è disponibile nella maggior parte delle aree. Per altre informazioni, vedere la [pagina Insieme di credenziali delle chiavi - Prezzi](https://azure.microsoft.com/pricing/details/key-vault/).

**Tempo previsto per il completamento:** 20 minuti

> [!NOTE]
> Questa esercitazione non fornisce istruzioni su come scrivere l'applicazione Azure inclusa in uno dei passaggi. Lo scopo di questa applicazione di esempio è mostrare come un'applicazione può essere autorizzata a usare una chiave o un segreto archiviato nell'insieme di credenziali delle chiavi.
> Questo articolo è incentrato sulla configurazione dell'insieme di credenziali delle chiavi di Azure tramite l'interfaccia della riga di comando multipiattaforma. Per le istruzioni relative ad Azure PowerShell, vedere [questa esercitazione equivalente](key-vault-get-started.md).

Per informazioni generali sull'insieme di credenziali di Azure, vedere [Cos'è l'insieme di credenziali chiave di Azure?](key-vault-whatis.md)

## <a name="prerequisites"></a>Prerequisiti

Per completare l'esercitazione, sono necessari gli elementi seguenti:

* Una sottoscrizione a Microsoft Azure. Se non si dispone di una sottoscrizione, è possibile iscriversi per una [versione di valutazione gratuita](https://azure.microsoft.com/pricing/free-trial).
* Interfaccia della riga di comando multipiattaforma versione 0.9.1 o successiva. Per installare l'ultima versione e associarla alla sottoscrizione di Azure, vedere [Installare e configurare l'interfaccia della riga di comando multipiattaforma di Azure](../cli-install-nodejs.md).
* Un'applicazione che verrà configurata per usare la chiave o la password creata in questa esercitazione. Un'applicazione di esempio è disponibile nell'[Area download Microsoft](http://www.microsoft.com/download/details.aspx?id=45343). Per istruzioni, vedere il file Readme associato.

## <a name="getting-help-with-azure-cross-platform-command-line-interface"></a>Risorse della Guida per l'interfaccia della riga di comando multipiattaforma di Microsoft Azure

Questa esercitazione si presuppone che si abbia familiarità con l'interfaccia della riga di comando (Bash, terminal, prompt dei comandi)

Il parametro --help o -h consente di visualizzare la Guida per specifici comandi. In alternativa, è anche possibile utilizzare il formato azure help [comando] [opzioni] per ottenere le stesse informazioni. Ad esempio, tutti i comandi seguenti restituiranno le stesse informazioni:

    azure account set --help

    azure account set -h

    azure help account set

In caso di dubbi sui parametri necessari per un comando, fare riferimento alla Guida utilizzando --help, -h o azure help [comando].

È consigliabile leggere anche le seguenti esercitazioni per acquisire familiarità con il modello di distribuzione Gestione risorse di Azure nell'interfaccia della riga di comando multipiattaforma di Azure:

* [Installare e configurare l'interfaccia della riga di comando multipiattaforma di Azure.](../cli-install-nodejs.md)
* [Uso dell'interfaccia della riga di comando multipiattaforma di Azure con Gestione risorse di Azure](../xplat-cli-azure-resource-manager.md)

## <a name="connect-to-your-subscriptions"></a>Connettersi alle sottoscrizioni

Accedere ad Azure usando il comando seguente:

```azurecli-interactive
azure login -u username -p password
```

Se invece si vuole accedere digitando in modo interattivo

```azurecli-interactive
azure login
```

Se si dispone di più sottoscrizioni e se ne vuole specificare una in particolare da usare per l'insieme di credenziali delle chiavi di Azure, digitare quanto segue per visualizzare le sottoscrizioni per il proprio account:

```azurecli-interactive
azure account list
```

Quindi, per specificare la sottoscrizione da usare, digitare.

```azurecli-interactive
azure account set <subscription name>
```

Per altre informazioni sulla configurazione dell'interfaccia della riga di comando multipiattaforma di Azure, vedere [Installare e configurare l'interfaccia della riga di comando multipiattaforma di Azure](../cli-install-nodejs.md).

## <a name="switch-to-using-azure-resource-manager"></a>Passare all'uso di Gestione risorse di Azure
Poiché per l'insieme di credenziali delle chiavi è necessario Gestione risorse di Azure, digitare quanto segue per passare alla modalità Gestione risorse di Azure:

```azurecli-interactive
azure config mode arm
```

## <a name="create-a-new-resource-group"></a>Creare un nuovo gruppo di risorse
Quando si usa Gestione risorse di Azure, tutte le risorse correlate vengono create in un gruppo di risorse. Per questa esercitazione si creerà un nuovo gruppo di risorse denominato 'ContosoResourceGroup'.

```azurecli-interactive
azure group create 'ContosoResourceGroup' 'East Asia'
```

Il primo parametro è il nome del gruppo di risorse e il secondo è la posizione. Per la posizione usare il comando `azure location list` per identificare come si specifica una posizione alternativa a quella di questo esempio. Se servono altre informazioni, digitare: `azure help location`

## <a name="register-the-key-vault-resource-provider"></a>Registrare il provider di risorse dell'insieme di credenziali delle chiavi
Verificare che il provider di risorse dell'insieme di credenziali delle chiavi sia registrato nella sottoscrizione:

```azurecli-interactive
azure provider register Microsoft.KeyVault
```

Quest'operazione deve essere eseguita una volta sola per ogni sottoscrizione.

## <a name="create-a-key-vault"></a>Creare un insieme di credenziali delle chiavi

Usare il comando `azure keyvault create` per creare un insieme di credenziali delle chiavi. Questo script ha tre parametri obbligatori: un nome del gruppo di risorse, un nome dell'insieme di credenziali delle chiavi e la località geografica.

ad esempio:
- Se si usa **ContosoKeyVault** come nome dell'insieme di credenziali
- **ContosoResourceGroup** come nome del gruppo di risorse
- **Stati Uniti orientali** come località digitare:

```azurecli-interactive
azure keyvault create --vault-name 'ContosoKeyVault' --resource-group 'ContosoResourceGroup' --location 'East Asia'
```

L'output di questo comando mostra le proprietà dell'insieme di credenziali delle chiavi appena creato. Le due proprietà più importanti sono:

* **Name**: nell'esempio corrisponde a ContosoKeyVault. Questo nome verrà usato per altri cmdlet di insieme di credenziali delle chiavi.
* **vaultUri**: nell'esempio corrisponde a https://contosokeyvault.vault.azure.net. Le applicazioni che usano l'insieme di credenziali tramite l'API REST devono usare questo URI.

L'account Azure ora è autorizzato a eseguire qualsiasi operazione su questo insieme di credenziali delle chiavi. Nessun altro lo è ancora.

## <a name="add-a-key-or-secret-to-the-key-vault"></a>Aggiungere una chiave o un segreto all'insieme di credenziali delle chiavi

Per usare l'insieme di credenziali delle chiavi di Azure per creare automaticamente una chiave protetta tramite software, eseguire il comando `azure key create` e digitare quanto segue:

```azurecli-interactive
azure keyvault key create --vault-name 'ContosoKeyVault' --key-name 'ContosoFirstKey' --destination software
````

Tuttavia, se si ha una chiave esistente in un file con estensione PEM salvato come file locale in un file denominato softkey.pem che si vuole caricare nell'insieme di credenziali delle chiavi di Azure, digitare il comando seguente per importare la chiave dal file PEM che protegge la chiave tramite software nel servizio dell'insieme di credenziali delle chiavi:

```azurecli-interactive
azure keyvault key import --vault-name 'ContosoKeyVault' --key-name 'ContosoFirstKey' --pem-file './softkey.pem' --password 'PaSSWORD' --destination software
```

A questo punto è possibile fare riferimento alla chiave creata o caricata nell'insieme di credenziali delle chiavi di Azure, usando il relativo URI. Usare **https://ContosoKeyVault.vault.azure.net/keys/ContosoFirstKey** per ottenere sempre la versione corrente e usare **https://ContosoKeyVault.vault.azure.net/keys/ContosoFirstKey/cgacf4f763ar42ffb0a1gca546aygd87** per ottenere questa versione specifica.

Per aggiungere un segreto all'insieme di credenziali, ovvero una password denominata SQLPassword con il valore Pa$$w0rd per l'insieme di credenziali delle chiavi, digitare quanto segue:

```azurecli-interactive
azure keyvault secret set --vault-name 'ContosoKeyVault' --secret-name 'SQLPassword' --value 'Pa$$w0rd'
```

È ora possibile fare riferimento a questa password aggiunta nell'insieme di credenziali delle chiavi di Azure, usando il relativo URI. Usare **https://ContosoVault.vault.azure.net/secrets/SQLPassword** per ottenere sempre la versione corrente e usare **https://ContosoVault.vault.azure.net/secrets/SQLPassword/90018dbb96a84117a0d2847ef8e7189d** per ottenere questa versione specifica.

Ora si può visualizzare la chiave o il segreto appena creato:

* Per visualizzare la chiave, digitare: `azure keyvault key list --vault-name 'ContosoKeyVault'`
* Per visualizzare il segreto, digitare: `azure keyvault secret list --vault-name 'ContosoKeyVault'`

## <a name="register-an-application-with-azure-active-directory"></a>Registrare un'applicazione con Azure Active Directory

Questo passaggio di solito viene eseguito da uno sviluppatore, su un computer separato. Anche se non è specifico dell'insieme di credenziali delle chiavi di Azure, viene incluso qui per completezza.

> [!IMPORTANT]
> Per completare l'esercitazione, l'account, l'insieme di credenziali e l'applicazione in cui si registrerà questo passaggio devono essere tutti nella stessa istanza di Azure Active Directory.

Le applicazioni che usano un insieme di credenziali delle chiavi devono eseguire l'autenticazione con un token di Azure Active Directory. A tale scopo, il proprietario dell'applicazione deve innanzitutto registrare l'applicazione in Azure Active Directory. Al termine della registrazione, il proprietario dell'applicazione ottiene i valori seguenti:

- **ID applicazione** 
- **Chiave di autenticazione**, detta anche segreto condiviso 

L'applicazione deve presentare entrambi questi valori ad Azure Active Directory, per ottenere un token. La configurazione dell'applicazione per eseguire questa operazione dipende dall'applicazione. Per l'[applicazione di esempio per l'insieme di credenziali delle chiavi](https://www.microsoft.com/download/details.aspx?id=45343), il proprietario dell'applicazione imposta questi valori nel file app.config.

Per la procedura dettagliata per registrare un'applicazione in Azure Active Directory, vedere l'articolo [Integrazione di applicazioni con Azure Active Directory](../active-directory/develop/active-directory-integrating-applications.md) o [Usare il portale per creare un'applicazione Azure Active Directory e un'entità servizio che possano accedere alle risorse](../azure-resource-manager/resource-group-create-service-principal-portal.md). Per registrare l'applicazione in Azure Active Directory:

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Fare clic su **Registrazioni per l'app** a sinistra. Se la voce Registrazioni per l'app non viene visualizzata, fare clic su **Altri servizi** per cercarla.  
    >[!NOTE]
    È necessario selezionare la stessa directory che contiene la sottoscrizione di Azure con cui è stato creato l'insieme di credenziali delle chiavi. 
3. Fare clic su **Registrazione nuova applicazione**.
4. Nel pannello **Crea** specificare un nome per l'applicazione e quindi selezionare **Applicazione Web e/o API Web** (impostazione predefinita) e specificare l'**URL di accesso** per l'applicazione Web. Se al momento non si hanno queste informazioni, per questo passaggio si può specificare qualsiasi valore, ad esempio http://test1.contoso.com. Non è importante se questi siti esistono. 

   ![Registrazione nuova applicazione](./media/key-vault-manage-with-cli/new-application-registration.png)

5. Selezionare il pulsante **Create** .
6. Al termine della registrazione dell'app, verrà visualizzato l'elenco delle app registrate. Trovare l'app appena registrata e fare clic su di essa.
7. Fare clic sul pannello **App registrata** e copiare l'**ID applicazione**.
8. Fare clic su **Tutte le impostazioni**.
9. Nel pannello **Impostazioni** fare clic su **chiavi**.
10. Digitare una descrizione nella casella **Descrizione chiave**, selezionare una durata e quindi fare clic su **SALVA**. La pagina viene aggiornata e mostra un valore chiave. 
11. Le informazioni di **ID applicazione** e **Chiave** verranno usate nel passaggio successivo per impostare le autorizzazioni per l'insieme di credenziali.

## <a name="authorize-the-application-to-use-the-key-or-secret"></a>Autorizzare l'applicazione a usare la chiave o il segreto
Per autorizzare l'accesso da parte dell'applicazione alla chiave o al segreto nell'insieme di credenziali, usare:

```azurecli-interactive
azure keyvault set-policy
```

Ad esempio, se il nome dell'insieme di credenziali è ContosoKeyVault e l'applicazione che si desidera autorizzare ha un ID client 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed e si vuole autorizzare l'applicazione a decrittografare e firmare con le chiavi dell'insieme di credenziali, eseguire quanto segue:

```azurecli-interactive
azure keyvault set-policy --vault-name 'ContosoKeyVault' --spn 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed --perms-to-keys '[\"decrypt\",\"sign\"]'
```

> [!NOTE]
> Se si eseguono prompt dei comandi di Windows, sostituire le virgolette singole con virgolette doppie e aggiungere il carattere di escape prima delle virgolette doppie interne. Ad esempio: "[\"decrypt\",\"sign\"]".
> 

Se si desidera autorizzare la stessa applicazione per la lettura di tutti i segreti nell'insieme di credenziali, eseguire le seguenti operazioni:

```azurecli-interactive
azure keyvault set-policy --vault-name 'ContosoKeyVault' --spn 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed --perms-to-secrets '[\"get\"]'
```

## <a name="if-you-want-to-use-a-hardware-security-module-hsm"></a>Per usare un modulo di protezione hardware
Per una maggiore sicurezza, è possibile importare o generare le chiavi in moduli di protezione hardware (HSM) che rimangono sempre entro il limite HSM. I moduli di protezione hardware sono certificati per FIPS 140-2 livello 2. Se questo requisito non è applicabile, saltare questa sezione e andare a [Eliminare l'insieme di credenziali delle chiavi e le chiavi e i segreti associati](#delete-the-key-vault-and-associated-keys-and-secrets).

Per creare queste chiavi HSM protette, è necessaria una sottoscrizione all'insieme di credenziali che supporti le chiavi HSM protette.

Quando si crea l'insieme di credenziali, aggiungere il parametro 'sku':

```azurecli-interactive
azure azure keyvault create --vault-name 'ContosoKeyVaultHSM' --resource-group 'ContosoResourceGroup' --location 'East Asia' --sku 'Premium'
```

È possibile aggiungere a questo insieme di credenziali chiavi protette tramite software (come illustrato in precedenza) e chiavi HSM protette. Per creare una chiave HSM protetta, impostare il parametro Destination su 'HSM':

```azurecli-interactive
azure keyvault key create --vault-name 'ContosoKeyVaultHSM' --key-name 'ContosoFirstHSMKey' --destination 'HSM'
```

È possibile usare il comando seguente per importare una chiave da un file con estensione PEM nel computer. Questo comando importa la chiave nei moduli HSM nel servizio dell'insieme di credenziali delle chiavi:

```azurecli-interactive
azure keyvault key import --vault-name 'ContosoKeyVaultHSM' --key-name 'ContosoFirstHSMKey' --pem-file '/.softkey.pem' --destination 'HSM' --password 'PaSSWORD'
```

Il comando successivo importa un pacchetto "bring your own key" (BYOK). Ciò consente di generare la chiave nel modulo HSM locale e di trasferirlo in moduli HSM nel servizio dell'insieme di credenziali delle chiavi, senza che la chiave esca dal limite HSM:

```azurecli-interactive
azure keyvault key import --vault-name 'ContosoKeyVaultHSM' --key-name 'ContosoFirstHSMKey' --byok-file './ITByok.byok' --destination 'HSM'
```

Per istruzioni più dettagliate su come generare questo pacchetto BYOK, vedere [Come usare chiavi HSM protette con l'insieme di credenziali delle chiavi di Azure](key-vault-hsm-protected-keys.md).

## <a name="delete-the-key-vault-and-associated-keys-and-secrets"></a>Eliminare l'insieme di credenziali delle chiavi e le chiavi e i segreti associati
Se l'insieme di credenziali delle chiavi e la chiave o il segreto associato non sono più necessari, è possibile eliminare l'insieme di credenziali delle chiavi usando il comando azure keyvault delete:

```azurecli-interactive
azure keyvault delete --vault-name 'ContosoKeyVault'
```

In alternativa, è possibile eliminare l'intero gruppo di risorse di Azure, che include l'insieme di credenziali delle chiavi e tutte le altre risorse incluse in quel gruppo:

```azurecli-interactive
azure group delete --name 'ContosoResourceGroup'
```


## <a name="other-azure-cross-platform-command-line-interface-commands"></a>Altri comandi dell'interfaccia della riga di comando multipiattaforma di Azure
Altri comandi che potrebbero essere utili per la gestione dell'insieme di credenziali delle chiavi di Azure.

Questo comando ottiene una visualizzazione tabulare di tutte le chiavi e le proprietà selezionate:

```azurecli-interactive
azure keyvault key list --vault-name 'ContosoKeyVault'
```

Questo comando visualizza un elenco completo di proprietà per la chiave specificata:

```azurecli-interactive
azure keyvault key show --vault-name 'ContosoKeyVault' --key-name 'ContosoFirstKey'
```

Questo comando ottiene una visualizzazione tabulare di tutti nomi dei segreti e tutte le proprietà selezionate:

```azurecli-interactive
azure keyvault secret list --vault-name 'ContosoKeyVault'
```

Ecco un esempio di come rimuovere una chiave specifica:

```azurecli-interactive
azure keyvault key delete --vault-name 'ContosoKeyVault' --key-name 'ContosoFirstKey'
```

Ecco un esempio di come rimuovere un segreto specifico:

```azurecli-interactive
azure keyvault secret delete --vault-name 'ContosoKeyVault' --secret-name 'SQLPassword'
```


## <a name="next-steps"></a>Passaggi successivi
- Per i riferimenti alla programmazione, vedere [Guida per gli sviluppatori dell’insieme di credenziali chiave Azure](key-vault-developers-guide.md).
- Per informazioni generali sull'insieme di credenziali di Azure, vedere [Cos'è l'insieme di credenziali chiave di Azure?](key-vault-whatis.md)
- Per usare un insieme di credenziali delle chiavi tramite PowerShell, vedere [Introduzione all'insieme di credenziali delle chiavi di Azure](key-vault-get-started.md).


