---
title: Gestire Azure Key Vault tramite l'interfaccia della riga di comando | Microsoft Docs
description: "Usare questa esercitazione per automatizzare le attività comuni in Key Vault tramite l'interfaccia della riga di comando 2.0"
services: key-vault
documentationcenter: 
author: amitbapat
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: 
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/08/2017
ms.author: ambapat
ms.translationtype: Human Translation
ms.sourcegitcommit: 18d4994f303a11e9ce2d07bc1124aaedf570fc82
ms.openlocfilehash: 5da9f5eceda71ac85259193e0f183c72813e1679
ms.contentlocale: it-it
ms.lasthandoff: 05/09/2017

---
# <a name="manage-key-vault-using-cli-20"></a>Gestire Key Vault tramite l'interfaccia della riga di comando 2.0
L'insieme di credenziali delle chiavi di Azure è disponibile nella maggior parte delle aree. Per altre informazioni, vedere la [pagina Insieme di credenziali delle chiavi - Prezzi](https://azure.microsoft.com/pricing/details/key-vault/).

## <a name="introduction"></a>Introduzione
Usare questa esercitazione per imparare a eseguire facilmente le attività iniziali dell'insieme di credenziali delle chiavi di Azure per creare un contenitore finalizzato (insieme di credenziali) in Azure, in cui archiviare e gestire chiavi crittografiche e segreti in Azure. Verrà inoltre descritto come usare l'interfaccia della riga di comando multipiattaforma di Azure per creare un insieme di credenziali contenente una chiave o una password che sarà possibile usare con un'applicazione Azure. L'esercitazione spiega poi come un'applicazione può usare questa chiave o password.

**Tempo previsto per il completamento:** 20 minuti

> [!NOTE]
> Questa esercitazione non include istruzioni su come scrivere l'applicazione Azure usata nel passaggio che spiega come autorizzare un'applicazione all'uso di una chiave o un segreto nell'insieme di credenziali delle chiavi.
>
> Questa esercitazione usa l'interfaccia della riga di comando 2.0 di Azure più recente.
>
>

Per informazioni generali sull'insieme di credenziali di Azure, vedere [Cos'è l'insieme di credenziali delle chiavi di Azure?](key-vault-whatis.md)

## <a name="prerequisites"></a>Prerequisiti
Per completare l'esercitazione, sono necessari gli elementi seguenti:

* Una sottoscrizione a Microsoft Azure. Se non si dispone di una sottoscrizione, è possibile iscriversi per una [versione di valutazione gratuita](https://azure.microsoft.com/pricing/free-trial).
* Interfaccia della riga di comando versione 2.0 o successiva. Per installare l'ultima versione e associarla alla sottoscrizione di Azure, vedere [Install and Configure the Azure Cross-Platform Command-Line Interface 2.0](/cli/azure/install-azure-cli) (Installare e configurare l'interfaccia della riga di comando multipiattaforma di Azure 2.0).
* Un'applicazione che verrà configurata per usare la chiave o la password creata in questa esercitazione. Un'applicazione di esempio è disponibile nell'[Area download Microsoft](http://www.microsoft.com/download/details.aspx?id=45343). Per istruzioni, vedere il file Readme associato.

## <a name="getting-help-with-azure-cross-platform-command-line-interface"></a>Risorse della Guida per l'interfaccia della riga di comando multipiattaforma di Microsoft Azure
Questa esercitazione si presuppone che si abbia familiarità con l'interfaccia della riga di comando (Bash, terminal, prompt dei comandi)

Il parametro --help o -h consente di visualizzare la Guida per specifici comandi. In alternativa, è anche possibile utilizzare il formato azure help [comando] [opzioni] per ottenere le stesse informazioni. Ad esempio, tutti i comandi seguenti restituiranno le stesse informazioni:

```
az account set --help
az account set -h
```

In caso di dubbi sui parametri necessari per un comando, fare riferimento alla Guida usando --help, -h o az help [comando].

È consigliabile di leggere anche le seguenti esercitazioni per acquisire familiarità con Gestione risorse di Azure nell'interfaccia della riga di comando multipiattaforma di Azure:

* [Installare l'interfaccia da riga di comando di Azure](/cli/azure/install-azure-cli)
* [Introduzione all'interfaccia della riga di comando di Azure 2.0](/cli/azure/get-started-with-azure-cli)

## <a name="connect-to-your-subscriptions"></a>Connettersi alle sottoscrizioni
Per accedere utilizzando un account aziendale, utilizzare il comando seguente:

```
az login -u username@domain.com -p password
```

Se invece si vuole accedere digitando in modo interattivo

```
az login
```

Se si dispone di più sottoscrizioni e se ne vuole specificare una in particolare da usare per l'insieme di credenziali delle chiavi di Azure, digitare quanto segue per visualizzare le sottoscrizioni per il proprio account:

```
az account list
```

Quindi, per specificare la sottoscrizione da usare, digitare.

```
az account set --subscription <subscription name or ID>
```

Per ulteriori informazioni sulla configurazione dell'interfaccia della riga di comando multipiattaforma di Azure, vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli).

## <a name="create-a-new-resource-group"></a>Creare un nuovo gruppo di risorse
Quando si usa Gestione risorse di Azure, tutte le risorse correlate vengono create in un gruppo di risorse. Per questa esercitazione si creerà un nuovo gruppo di risorse denominato 'ContosoResourceGroup'.

```
az group create -n 'ContosoResourceGroup' -l 'East Asia'
```

Il primo parametro è il nome del gruppo di risorse e il secondo è la posizione. Per la posizione usare il comando `az account list-locations` per identificare come si specifica una posizione alternativa a quella di questo esempio. Se servono altre informazioni, digitare: `az account list-locations -h`.

## <a name="register-the-key-vault-resource-provider"></a>Registrare il provider di risorse dell'insieme di credenziali delle chiavi
Verificare che il provider di risorse dell'insieme di credenziali delle chiavi sia registrato nella sottoscrizione:

```
az provider register -n Microsoft.KeyVault
```

Quest'operazione deve essere eseguita una volta sola per ogni sottoscrizione.

## <a name="create-a-key-vault"></a>Creare un insieme di credenziali delle chiavi
Usare il comando `az keyvault create` per creare un insieme di credenziali delle chiavi. Questo script ha tre parametri obbligatori: un nome del gruppo di risorse, un nome dell'insieme di credenziali delle chiavi e la località geografica.

Ad esempio, se si usa il nome dell'insieme di credenziali ContosoKeyVault, il nome del gruppo di risorse ContosoResourceGroup e la posizione East Asia, digitare:
```
az keyvault create --name 'ContosoKeyVault' --resource-group 'ContosoResourceGroup' --location 'East Asia'
```

L'output di questo comando mostra le proprietà dell'insieme di credenziali delle chiavi appena creato. Le due proprietà più importanti sono:

* **name**: nell'esempio corrisponde a ContosoKeyVault. Questo nome verrà usato per altri comandi di Key Vault.
* **vaultUri**: nell'esempio corrisponde a https://contosokeyvault.vault.azure.net. Le applicazioni che usano l'insieme di credenziali tramite l'API REST devono usare questo URI.

L'account Azure ora è autorizzato a eseguire qualsiasi operazione su questo insieme di credenziali delle chiavi. Nessun altro lo è ancora.

## <a name="add-a-key-or-secret-to-the-key-vault"></a>Aggiungere una chiave o un segreto all'insieme di credenziali delle chiavi
Per usare l'insieme di credenziali delle chiavi di Azure per creare automaticamente una chiave protetta tramite software, eseguire il comando `az key create` e digitare quanto segue:
```
az keyvault key create --vault-name 'ContosoKeyVault' --name 'ContosoFirstKey' --protection software
```
Tuttavia, se si ha una chiave esistente in un file con estensione PEM salvato come file locale in un file denominato softkey.pem che si vuole caricare nell'insieme di credenziali delle chiavi di Azure, digitare il comando seguente per importare la chiave dal file PEM che protegge la chiave tramite software nel servizio dell'insieme di credenziali delle chiavi:
```
az keyvault key import --vault-name 'ContosoKeyVault' --name 'ContosoFirstKey' --pem-file './softkey.pem' --pem-password 'PaSSWORD' --protection software
```
A questo punto è possibile fare riferimento alla chiave creata o caricata nell'insieme di credenziali delle chiavi di Azure, usando il relativo URI. Usare **https://ContosoKeyVault.vault.azure.net/keys/ContosoFirstKey** per ottenere sempre la versione corrente e usare **https://ContosoKeyVault.vault.azure.net/keys/ContosoFirstKey/cgacf4f763ar42ffb0a1gca546aygd87** per ottenere questa versione specifica.

Per aggiungere un segreto all'insieme di credenziali, ovvero una password denominata SQLPassword con il valore Pa$$w0rd per l'insieme di credenziali delle chiavi, digitare quanto segue:
```
az keyvault secret set --vault-name 'ContosoKeyVault' --name 'SQLPassword' --value 'Pa$$w0rd'
```
È ora possibile fare riferimento a questa password aggiunta nell'insieme di credenziali delle chiavi di Azure, usando il relativo URI. Usare **https://ContosoVault.vault.azure.net/secrets/SQLPassword** per ottenere sempre la versione corrente e usare **https://ContosoVault.vault.azure.net/secrets/SQLPassword/90018dbb96a84117a0d2847ef8e7189d** per ottenere questa versione specifica.

Ora si può visualizzare la chiave o il segreto appena creato:

* Per visualizzare la chiave, digitare: `az keyvault key list --vault-name 'ContosoKeyVault'`
* Per visualizzare il segreto, digitare: `az keyvault secret list --vault-name 'ContosoKeyVault'`

## <a name="register-an-application-with-azure-active-directory"></a>Registrare un'applicazione con Azure Active Directory
Questo passaggio di solito viene eseguito da uno sviluppatore, su un computer separato. Anche se non è specifico dell'insieme di credenziali delle chiavi di Azure, viene incluso qui per completezza.

> [!IMPORTANT]
> Per completare l'esercitazione, l'account, l'insieme di credenziali e l'applicazione in cui si registrerà questo passaggio devono essere tutti nella stessa directory di Azure.
>
>

Le applicazioni che usano un insieme di credenziali delle chiavi devono eseguire l'autenticazione con un token di Azure Active Directory. A tale scopo, il proprietario dell'applicazione deve innanzitutto registrare l'applicazione in Azure Active Directory. Al termine della registrazione, il proprietario dell'applicazione ottiene i valori seguenti:

* Un **ID applicazione** (chiamato anche ID client) e una **chiave di autenticazione** (chiamata anche segreto condiviso). L'applicazione deve presentare entrambi questi valori ad Azure Active Directory per ottenere un token. La configurazione dell'applicazione per eseguire questa operazione dipende dall'applicazione. Per l'applicazione di esempio dell'insieme di credenziali delle chiavi, il proprietario dell'applicazione imposta questi valori nel file app.config.

Per registrare l'applicazione in Azure Active Directory:

1. Accedere al portale di Azure.
2. A sinistra fare clic su **Azure Active Directory** e quindi selezionare la directory in cui si registrerà l'applicazione. <br> <br> 

> [!Note] 
> È necessario selezionare la stessa directory che contiene la sottoscrizione di Azure con cui è stato creato l'insieme di credenziali delle chiavi. Se non si sa quale directory selezionare, fare clic su **Impostazioni**, identificare la sottoscrizione con cui si è creato l'insieme di credenziali chiave e prendere nota del nome della directory visualizzata nell'ultima colonna.

3. Fare clic su **APPLICAZIONI**. Se nessuna app è stata aggiunta alla directory, in questa pagina sarà visualizzato solo il collegamento **Aggiungi un'app**. Fare clic sul collegamento. In alternativa, è possibile fare clic su **AGGIUNGI** sulla barra dei comandi.
4. Nella pagina **Come procedere** della procedura guidata **AGGIUNGI APPLICAZIONE** fare clic su **Aggiungi un'applicazione che l'organizzazione sta sviluppando**.
5. Nella pagina **Informazioni sull'applicazione** specificare un nome per l'applicazione e selezionare **APPLICAZIONE WEB E/O API WEB** (opzione predefinita). Fare clic sull'icona Avanti.
6. Nella pagina **Proprietà dell'app** specificare **URL ACCESSO** e **URI ID APP** per l'applicazione Web. Se l'applicazione non ha questi valori, è possibile crearli per questo passaggio (ad esempio, è possibile specificare http://test1.contoso.com per entrambe le caselle). Non è importante se questi siti esistono. Ciò che conta è che ogni applicazione nella directory abbia un URI ID app diverso. La directory usa questa stringa per identificare l'app.
7. Fare clic sull'icona Completa per salvare le modifiche nella procedura guidata.
8. Nella pagina Avvio rapido fare clic su **CONFIGURA**.
9. Scorrere fino alla sezione **chiavi**, selezionare la durata e quindi fare clic su **SALVA**. La pagina viene aggiornata e mostra un valore chiave. È necessario configurare l'applicazione con questo valore chiave e con il valore **ID CLIENT** . Le istruzioni per questa configurazione saranno specifiche dell'applicazione.
10. Copiare da questa pagina il valore dell'ID client che si userà nel passaggio successivo per impostare le autorizzazioni per l'insieme di credenziali.

## <a name="authorize-the-application-to-use-the-key-or-secret"></a>Autorizzare l'applicazione a usare la chiave o il segreto
Per autorizzare l'accesso da parte dell'applicazione alla chiave o al segreto nell'insieme di credenziali, usare il comando `az keyvault set-policy` .

Ad esempio, se il nome dell'insieme di credenziali è ContosoKeyVault e l'applicazione che si desidera autorizzare ha un ID client 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed e si vuole autorizzare l'applicazione a decrittografare e firmare con le chiavi dell'insieme di credenziali, eseguire quanto segue:
```
az keyvault set-policy --name 'ContosoKeyVault' --spn 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed --key-permissions decrypt sign
```

Se si desidera autorizzare la stessa applicazione per la lettura di tutti i segreti nell'insieme di credenziali, eseguire le seguenti operazioni:
```
az keyvault set-policy --name 'ContosoKeyVault' --spn 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed --secret-permissions get
```
## <a name="if-you-want-to-use-a-hardware-security-module-hsm"></a>Per usare un modulo di protezione hardware
Per una maggiore sicurezza, è possibile importare o generare le chiavi in moduli di protezione hardware (HSM) che rimangono sempre entro il limite HSM. I moduli di protezione hardware sono certificati per FIPS 140-2 livello 2. Se questo requisito non è applicabile, saltare questa sezione e andare a [Eliminare l'insieme di credenziali delle chiavi e le chiavi e i segreti associati](#delete-the-key-vault-and-associated-keys-and-secrets).

Per creare queste chiavi HSM protette, è necessaria una sottoscrizione all'insieme di credenziali che supporti le chiavi HSM protette.

Quando si crea l'insieme di credenziali, aggiungere il parametro 'sku':

```
az keyvault create --name 'ContosoKeyVaultHSM' --resource-group 'ContosoResourceGroup' --location 'East Asia' --sku 'Premium'
```
È possibile aggiungere a questo insieme di credenziali chiavi protette tramite software (come illustrato in precedenza) e chiavi HSM protette. Per creare una chiave HSM protetta, impostare il parametro Destination su 'HSM':

```
az keyvault key create --vault-name 'ContosoKeyVaultHSM' --name 'ContosoFirstHSMKey' --protection 'hsm'
```

È possibile usare il comando seguente per importare una chiave da un file con estensione PEM nel computer. Questo comando importa la chiave nei moduli HSM nel servizio dell'insieme di credenziali delle chiavi:

```
az keyvault key import --vault-name 'ContosoKeyVaultHSM' --name 'ContosoFirstHSMKey' --pem-file '/.softkey.pem' --protection 'hsm' --pem-password 'PaSSWORD'
```
Il comando successivo importa un pacchetto "bring your own key" (BYOK). Ciò consente di generare la chiave nel modulo HSM locale e di trasferirlo in moduli HSM nel servizio dell'insieme di credenziali delle chiavi, senza che la chiave esca dal limite HSM:

```
az keyvault key import --vault-name 'ContosoKeyVaultHSM' --name 'ContosoFirstHSMKey' --byok-file './ITByok.byok' --protection 'hsm'
```
Per istruzioni più dettagliate su come generare questo pacchetto BYOK, vedere [Come usare chiavi HSM protette con l'insieme di credenziali delle chiavi di Azure](key-vault-hsm-protected-keys.md).

## <a name="delete-the-key-vault-and-associated-keys-and-secrets"></a>Eliminare l'insieme di credenziali delle chiavi e le chiavi e i segreti associati
Se l'insieme di credenziali delle chiavi e la chiave o il segreto associato non sono più necessari, è possibile eliminare l'insieme di credenziali delle chiavi usando il comando `az keyvault delete`:

```
az keyvault delete --name 'ContosoKeyVault'
```

In alternativa, è possibile eliminare l'intero gruppo di risorse di Azure, che include l'insieme di credenziali delle chiavi e tutte le altre risorse incluse in quel gruppo:

```
az group delete --name 'ContosoResourceGroup'
```

## <a name="other-azure-cross-platform-command-line-interface-commands"></a>Altri comandi dell'interfaccia della riga di comando multipiattaforma di Azure
Altri comandi che potrebbero essere utili per la gestione dell'insieme di credenziali delle chiavi di Azure.

Questo comando ottiene una visualizzazione tabulare di tutte le chiavi e le proprietà selezionate:

az keyvault key list --vault-name 'ContosoKeyVault'

Questo comando visualizza un elenco completo di proprietà per la chiave specificata:

az keyvault key show --vault-name 'ContosoKeyVault' --name 'ContosoFirstKey'

Questo comando ottiene una visualizzazione tabulare di tutti nomi dei segreti e tutte le proprietà selezionate:

az keyvault secret list --vault-name 'ContosoKeyVault'

Ecco un esempio di come rimuovere una chiave specifica:

az keyvault key delete --vault-name 'ContosoKeyVault' --name 'ContosoFirstKey'

Ecco un esempio di come rimuovere un segreto specifico:

az keyvault secret delete --vault-name 'ContosoKeyVault' --name 'SQLPassword'


## <a name="next-steps"></a>Passaggi successivi
Per riferimenti sull'interfaccia della riga di comando di Azure per i comandi delle credenziali delle chiavi, vedere [Key Vault CLI reference](/cli/azure/keyvault) (Riferimento dell'interfaccia della riga di comando di Key Vault)

Per i riferimenti alla programmazione, vedere [Guida per gli sviluppatori dell'insieme di credenziali chiave Azure](key-vault-developers-guide.md).

