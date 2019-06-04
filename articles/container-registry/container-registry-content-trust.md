---
title: Attendibilità dei contenuti in Registro Azure Container
description: Informazioni su come abilitare l'attendibilità dei contenuti per Registro Azure Container ed eseguire il push e il pull di immagini firmate.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: quickstart
ms.date: 05/06/2019
ms.author: danlep
ms.openlocfilehash: ca9ef32a830f56edb471256b3b9175ba0fbec51d
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/27/2019
ms.locfileid: "65069211"
---
# <a name="content-trust-in-azure-container-registry"></a>Attendibilità dei contenuti in Registro Azure Container

Registro Azure Container implementa il modello di [attendibilità del contenuto][docker-content-trust] di Docker, abilitando il push e il pull delle immagini firmate. Questo articolo illustra come abilitare l'attendibilità del contenuto nei registri contenitori.

> [!NOTE]
> L'attendibilità del contenuto è una funzionalità dello [SKU Premium](container-registry-skus.md) di Registro Azure Container.

## <a name="how-content-trust-works"></a>Funzionamento dell'attendibilità dei contenuti

Per ogni sistema distribuito progettato con particolare attenzione alla sicurezza, è importante verificare sia l'*origine* che l'*integrità* dei dati in ingresso nel sistema. I consumer dei dati devono poter verificare l'editore (origine) dei dati, nonché assicurarsi che i dati non siano stati modificati dopo la pubblicazione (integrità). 

L'attendibilità dei contenuti consente a un editore di immagini di **firmare** le immagini di cui viene eseguito il push nel registro. I consumer delle immagini (persone o sistemi che eseguono il pull delle immagini dal registro) possono configurare i client per eseguire il pull *solo* delle immagini firmate. Quando il consumer di un'immagine esegue il pull di un'immagine firmata, il client Docker verifica l'integrità dell'immagine. In questo modello, i consumer hanno la certezza che le immagini firmate siano state effettivamente pubblicate dall'editore indicato e che non siano state modificate dopo la pubblicazione.

### <a name="trusted-images"></a>Immagini attendibili

L'attendibilità dei contenuti funziona con i **tag** in un repository. I repository di immagini possono contenere immagini con tag sia firmati che non firmati. Ad esempio, si potrebbero firmare solo le immagini `myimage:stable` e `myimage:latest`, ma non le immagini `myimage:dev`.

### <a name="signing-keys"></a>Chiavi di firma

L'attendibilità dei contenuti viene gestita tramite l'uso di un set di chiavi di firma crittografiche. Queste chiavi sono associate a un repository specifico in un registro. Ci sono diversi tipi di chiavi di firma che i client Docker e il registro usano per gestire l'attendibilità per i tag in un repository. Quando si abilita l'attendibilità dei contenuti e la si integra nella pipeline di pubblicazione e utilizzo dei contenitori, è necessario gestire queste chiavi con attenzione. Per altre informazioni, vedere [Gestione delle chiavi](#key-management) più avanti in questo articolo e [Manage keys for content trust][docker-manage-keys] (Gestire le chiavi per l'attendibilità dei contenuti) nella documentazione di Docker.

> [!TIP]
> Quella fornita è una panoramica molto generale del modello di attendibilità dei contenuti Docker. Per un'analisi approfondita dell'attendibilità dei contenuti, vedere [Content trust in Docker][docker-content-trust] (Attendibilità dei contenuti in Docker).

## <a name="enable-registry-content-trust"></a>Abilitare l'attendibilità dei contenuti nel registro

Il primo passaggio consiste nell'abilitare l'attendibilità dei contenuti a livello di registro. Una volta abilita l'attendibilità dei contenuti, i client (utenti o servizi) possono eseguire il push delle immagini firmate nel registro. L'abilitazione dell'attendibilità dei contenuti nel registro non limita l'utilizzo del registro ai soli consumer per cui è abilitata l'attendibilità dei contenuti. I consumer per cui non è abilitata l'attendibilità dei contenuti possono continuare a usare normalmente il registro. I consumer nei cui client è abilitata l'attendibilità dei contenuti, tuttavia, potranno visualizzare *solo* le immagini firmate nel registro.

Per abilitare l'attendibilità dei contenuti per il registro, passare al registro nel portale di Azure. In **Criteri** selezionare **Attendibilità contenuto** > **Abilitata** > **Salva**.

![Abilitazione dell'attendibilità dei contenuti per un registro nel portale di Azure][content-trust-01-portal]

## <a name="enable-client-content-trust"></a>Abilitare l'attendibilità dei contenuti nel client

Per usare immagini attendibili, sia gli editori delle immagini che i loro consumer devono abilitare l'attendibilità dei contenuti per i client Docker. Un editore può firmare le immagini di cui esegue il push in un registro abilitato per l'attendibilità dei contenuti. Per un consumer, l'abilitazione dell'attendibilità dei contenuti limita la visualizzazione solo alle immagini firmate in un registro. L'attendibilità dei contenuti è disabilitata per impostazione predefinita nei client Docker, ma è possibile abilitarla per una sessione di shell o un comando.

Per abilitare l'attendibilità dei contenuti per una sessione di shell, impostare la variabile di ambiente `DOCKER_CONTENT_TRUST` su **1**. Ad esempio, nella shell Bash:

```bash
# Enable content trust for shell session
export DOCKER_CONTENT_TRUST=1
```

Se invece si vuole abilitare o disabilitare l'attendibilità dei contenuti per un singolo comando, diversi comandi Docker supportano l'argomento `--disable-content-trust`. Per abilitare l'attendibilità dei contenuti per un singolo comando:

```bash
# Enable content trust for single command
docker build --disable-content-trust=false -t myacr.azurecr.io/myimage:v1 .
```

Se è stata abilitata l'attendibilità dei contenuti per la sessione di shell e la si vuole disabilitare per un singolo comando:

```bash
# Disable content trust for single command
docker build --disable-content-trust -t myacr.azurecr.io/myimage:v1 .
```

## <a name="grant-image-signing-permissions"></a>Concedere autorizzazioni di firma delle immagini

Solo gli utenti o i sistemi a cui è stata concessa l'autorizzazione possono eseguire il push di immagini attendibili nel registro. Per concedere l'autorizzazione per il push di immagini attendibili a un utente (o a un sistema che usa un'entità servizio), concedere alle relative identità di Azure Active Directory il ruolo `AcrImageSigner`. Questo ruolo è in aggiunta al ruolo `AcrPush` (o equivalente) richiesto per il push delle immagini nel registro. Per informazioni dettagliate, vedere [Ruoli e autorizzazioni di Registro Azure Container](container-registry-roles.md).

I dettagli per la concessione del ruolo `AcrImageSigner` nel portale di Azure e nell'interfaccia della riga di comando di Azure sono illustrati di seguito.

### <a name="azure-portal"></a>Portale di Azure

Passare al registro nel portale di Azure e quindi selezionare **Controllo di accesso (IAM)**  > **Aggiungi assegnazione di ruolo**. In **Aggiungi assegnazione di ruolo** selezionare `AcrImageSigner` in **Ruolo**, quindi in **Seleziona** selezionare uno o più utenti o entità servizio e quindi fare clic su **Salva**.

In questo esempio sono state assegnate due entità al ruolo `AcrImageSigner`: un'entità servizio denominata "service-principal" e un utente denominato "Azure User".

![Abilitazione dell'attendibilità dei contenuti per un registro nel portale di Azure][content-trust-02-portal]

### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

Per concedere le autorizzazioni di firma a un utente con l'interfaccia della riga di comando di Azure, assegnare il ruolo `AcrImageSigner` all'utente, impostando come ambito il registro. Il formato del comando è:

```azurecli
az role assignment create --scope <registry ID> --role AcrImageSigner --assignee <user name>
```

Per concedere, ad esempio, il ruolo a se stessi, è possibile eseguire i comandi seguenti in una sessione dell'interfaccia della riga di comando di Azure autenticata. Modificare il valore di `REGISTRY` in base al nome di Registro Azure Container.

```bash
# Grant signing permissions to authenticated Azure CLI user
REGISTRY=myregistry
USER=$(az account show --query user.name --output tsv)
REGISTRY_ID=$(az acr show --name $REGISTRY --query id --output tsv)

az role assignment create --scope $REGISTRY_ID --role AcrImageSigner --assignee $USER
```

È anche possibile concedere a un'[entità servizio](container-registry-auth-service-principal.md) i diritti per eseguire il push di immagini attendibili nel registro. L'uso di un'entità servizio è utile per i sistemi di compilazione e altri sistemi automatici che devono eseguire il push di immagini attendibili nel registro. Il formato è simile a quello per la concessione di un'autorizzazione utente, ma è necessario specificare un ID di entità servizio per il valore `--assignee`.

```azurecli
az role assignment create --scope $REGISTRY_ID --role AcrImageSigner --assignee <service principal ID>
```

Il valore di `<service principal ID>` può corrispondere a **appId** o **objectId** dell'entità servizio oppure a uno dei relativi valori **servicePrincipalNames**. Per altre informazioni sull'uso di entità servizio e Registro Azure Container, vedere [Autenticazione al Registro Azure Container con entità servizio](container-registry-auth-service-principal.md).

Dopo eventuali modifiche ai ruoli, eseguire `az acr login` per aggiornare il token di identità locale per l'interfaccia della riga di comando di Azure per rendere effettivi i nuovi ruoli.

## <a name="push-a-trusted-image"></a>Eseguire il push di un'immagine attendibile

Per eseguire il push di un tag di un'immagine attendibile nel registro contenitori, abilitare l'attendibilità dei contenuti ed eseguire il push dell'immagine con `docker push`. La prima volta che si esegue il push di un tag firmato, viene chiesto di creare una passphrase sia per una chiave di firma radice che per una chiave di firma del repository. Entrambe le chiavi di firma, del repository e radice, vengono generate e archiviate in locale nel computer.

```console
$ docker push myregistry.azurecr.io/myimage:v1
The push refers to repository [myregistry.azurecr.io/myimage]
ee83fc5847cb: Pushed
v1: digest: sha256:aca41a608e5eb015f1ec6755f490f3be26b48010b178e78c00eac21ffbe246f1 size: 524
Signing and pushing trust metadata
You are about to create a new root signing key passphrase. This passphrase
will be used to protect the most sensitive key in your signing system. Please
choose a long, complex passphrase and be careful to keep the password and the
key file itself secure and backed up. It is highly recommended that you use a
password manager to generate the passphrase and keep it safe. There will be no
way to recover this key. You can find the key in your config directory.
Enter passphrase for new root key with ID 4c6c56a:
Repeat passphrase for new root key with ID 4c6c56a:
Enter passphrase for new repository key with ID bcd6d98:
Repeat passphrase for new repository key with ID bcd6d98:
Finished initializing "myregistry.azurecr.io/myimage"
Successfully signed myregistry.azurecr.io/myimage:v1
```

Dopo la prima esecuzione di `docker push` con l'attendibilità dei contenuti abilitata, il client Docker usa la stessa chiave radice per i push successivi. Per ogni push successivo nello stesso repository, viene chiesta solo la chiave del repository. Ogni volta che si esegue il push di un'immagine attendibile in un nuovo repository, viene chiesto di fornire una passphrase per una nuova chiave del repository.

## <a name="pull-a-trusted-image"></a>Eseguire il pull di un'immagine attendibile

Per eseguire il pull di un'immagine attendibile, abilitare l'attendibilità dei contenuti ed eseguire normalmente il comando `docker pull`. Per eseguire il pull delle immagini attendibili, il ruolo `AcrPull` è sufficiente per i normali utenti. Non sono necessari ruoli aggiuntivi, ad esempio un ruolo `AcrImageSigner`. I consumer per cui è abilitata l'attendibilità dei contenuti possono eseguire il pull solo di immagini con tag firmati. Ecco un esempio di pull di un tag firmato:

```console
$ docker pull myregistry.azurecr.io/myimage:signed
Pull (1 of 1): myregistry.azurecr.io/myimage:signed@sha256:0800d17e37fb4f8194495b1a188f121e5b54efb52b5d93dc9e0ed97fce49564b
sha256:0800d17e37fb4f8194495b1a188f121e5b54efb52b5d93dc9e0ed97fce49564b: Pulling from myimage
8e3ba11ec2a2: Pull complete
Digest: sha256:0800d17e37fb4f8194495b1a188f121e5b54efb52b5d93dc9e0ed97fce49564b
Status: Downloaded newer image for myregistry.azurecr.io/myimage@sha256:0800d17e37fb4f8194495b1a188f121e5b54efb52b5d93dc9e0ed97fce49564b
Tagging myregistry.azurecr.io/myimage@sha256:0800d17e37fb4f8194495b1a188f121e5b54efb52b5d93dc9e0ed97fce49564b as myregistry.azurecr.io/myimage:signed
```

Se un client per cui è abilitata l'attendibilità dei contenuti cerca di eseguire il pull di un tag non firmato, l'operazione ha esito negativo:

```console
$ docker pull myregistry.azurecr.io/myimage:unsigned
No valid trust data for unsigned
```

### <a name="behind-the-scenes"></a>Dietro le quinte

Quando si esegue `docker pull`, il client Docker usa la stessa libreria dell'[interfaccia della riga di comando Notary][docker-notary-cli] per richiedere il mapping del digest da tag a SHA-256 per il tag di cui si esegue il pull. Dopo aver verificato le firme nei dati attendibili, il client comunica al motore Docker di eseguire un "pull in base al digest". Durante il pull, il motore usa il checksum SHA-256 come indirizzo dei contenuti per richiedere e convalidare il manifesto dell'immagine da Registro Azure Container.

## <a name="key-management"></a>Gestione della chiave

Come indicato nell'output di `docker push` quando si esegue il push della prima immagine attendibile, la chiave radice è la più sensibile. Assicurarsi di eseguire il backup della chiave radice e di archiviarla in una posizione sicura. Per impostazione predefinita, il client Docker archivia le chiavi di firma nella directory seguente:

```sh
~/.docker/trust/private
```

Eseguire il backup delle chiavi radice e del repository comprimendole in un archivio e archiviandole in modo sicuro offline (ad esempio in un dispositivo di archiviazione USB). Ad esempio, in Bash:

```bash
umask 077; tar -zcvf docker_private_keys_backup.tar.gz ~/.docker/trust/private; umask 022
```

Insieme alle chiavi radice e del repository generate in locale ne vengono generate e archiviate anche altre da Registro Azure Container quando si esegue il push di un'immagine attendibile. Per informazioni dettagliate sulle diverse chiavi nell'implementazione dell'attendibilità dei contenuti di Docker, incluse indicazioni aggiuntive sulla gestione, vedere [Manage keys for content trust][docker-manage-keys] (Gestire le chiavi per l'attendibilità dei contenuti) nella documentazione di Docker.

### <a name="lost-root-key"></a>Chiave radice persa

Se si perde l'accesso alla chiave radice, si perde l'accesso ai tag firmati in tutti i repository in cui i tag sono stati firmati con tale chiave. Registro Azure Container non è in grado di ripristinare l'accesso ai tag delle immagini firmati con una chiave radice persa. Per rimuovere tutti i dati di attendibilità (firme) per il registro, disabilitare e quindi abilitare di nuovo l'attendibilità dei contenuti per il registro.

> [!WARNING]
> Disabilitando e quindi abilitando di nuovo l'attendibilità dei contenuti nel registro **vengono eliminati tutti i dati di attendibilità per tutti i tag firmati in ogni repository nel registro**. Questa azione è irreversibile e Registro Azure Container non può ripristinare i dati di attendibilità eliminati. La disabilitazione dell'attendibilità dei contenuti non elimina le immagini stesse.

Per disabilitare l'attendibilità dei contenuti per il registro, passare al registro nel portale di Azure. In **Criteri** selezionare **Attendibilità contenuto** > **Disabilitata** > **Salva**. Verrà visualizzato un avviso relativo alla perdita di tutte le firme nel registro. Selezionare **OK** per eliminare in modo permanente tutte le firme nel registro.

![Disabilitazione dell'attendibilità dei contenuti per un registro nel portale di Azure][content-trust-03-portal]

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'attendibilità del contenuto, vedere [Content trust in Docker][docker-content-trust] (Attendibilità del contenuto in Docker). Sebbene in questo articolo siano stati illustrati diversi punti importanti, l'attendibilità dei contenuti è un argomento vasto e viene descritta in modo più dettagliato nella documentazione di Docker.

<!-- IMAGES> -->
[content-trust-01-portal]: ./media/container-registry-content-trust/content-trust-01-portal.png
[content-trust-02-portal]: ./media/container-registry-content-trust/content-trust-02-portal.png
[content-trust-03-portal]: ./media/container-registry-content-trust/content-trust-03-portal.png

<!-- LINKS - external -->
[docker-content-trust]: https://docs.docker.com/engine/security/trust/content_trust
[docker-manage-keys]: https://docs.docker.com/engine/security/trust/trust_key_mng/
[docker-notary-cli]: https://docs.docker.com/notary/getting_started/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - internal -->
[azure-cli]: /cli/azure/install-azure-cli
