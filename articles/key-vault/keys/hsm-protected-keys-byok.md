---
title: Come generare & trasferire chiavi protette da HSM-BYOK-Azure Key Vault
description: Usare questo articolo per pianificare, generare e trasferire chiavi HSM protette da usare con Azure Key Vault. Noto anche come BYOK (Bring your own key).
services: key-vault
author: amitbapat
manager: devtiw
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: keys
ms.topic: conceptual
ms.date: 05/29/2020
ms.author: ambapat
ms.openlocfilehash: 4df934f38a8fb657fa4a8de5922d96197a3d02cc
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87061112"
---
# <a name="import-hsm-protected-keys-to-key-vault-byok"></a>Importare chiavi HSM protette per Key Vault (BYOK)

Per una maggiore sicurezza quando si usa Azure Key Vault, è possibile importare o generare una chiave in un modulo di protezione hardware (HSM); la chiave non lascerà mai il limite del modulo di protezione hardware. Questo scenario viene spesso definito *Bring your own key* (BYOK). Key Vault usa la famiglia nCipher nShield di HSM (FIPS 140-2 livello 2 convalidata) per proteggere le chiavi.

Usare le informazioni in questo articolo per pianificare, generare e trasferire chiavi HSM protette da usare con Azure Key Vault.

> [!NOTE]
> Questa funzionalità non è disponibile per Azure Cina 21Vianet. 
> 
> Questo metodo di importazione è disponibile solo per [HSM supportati](#supported-hsms). 

Per altre informazioni e per un'esercitazione su come iniziare a usare Key Vault (inclusa la procedura per creare un insieme di credenziali delle chiavi per le chiavi protette da HSM), vedere [che cos'è Azure Key Vault?](../general/overview.md).

## <a name="overview"></a>Panoramica

Ecco una panoramica del processo. I passaggi specifici da completare sono descritti più avanti in questo articolo.

* In Key Vault generare una chiave, definita chiave per lo *scambio delle chiavi* (KEK). KEK deve essere una chiave RSA-HSM con solo l' `import` operazione chiave. Solo Key Vault SKU Premium supporta le chiavi RSA-HSM.
* Scaricare la chiave pubblica KEK come file con estensione PEM.
* Trasferire la chiave pubblica KEK a un computer offline connesso a un modulo di protezione hardware locale.
* Nel computer offline usare lo strumento BYOK fornito dal fornitore del modulo di protezione hardware per creare un file BYOK. 
* La chiave di destinazione viene crittografata con un KEK, che rimane crittografato fino a quando non viene trasferito al modulo di protezione hardware Key Vault. Solo la versione crittografata della chiave lascia il modulo di protezione hardware locale.
* Una KEK generata all'interno di un modulo di protezione hardware Key Vault non è esportabile. HSM impone la regola che non esista una versione non chiara di KEK all'esterno di un modulo di protezione hardware Key Vault.
* KEK deve trovarsi nello stesso insieme di credenziali delle chiavi in cui verrà importata la chiave di destinazione.
* Quando il file BYOK viene caricato in Key Vault, un modulo di protezione hardware Key Vault usa la chiave privata KEK per decrittografare il materiale della chiave di destinazione e importarlo come chiave del modulo di protezione hardware. Questa operazione viene eseguita interamente all'interno di un modulo di protezione hardware Key Vault. La chiave di destinazione rimane sempre nel limite di protezione HSM.

## <a name="prerequisites"></a>Prerequisiti

La tabella seguente elenca i prerequisiti per l'uso di BYOK in Azure Key Vault:

| Requisito | Ulteriori informazioni |
| --- | --- |
| Una sottoscrizione di Azure. |Per creare un insieme di credenziali delle chiavi in Azure Key Vault, è necessaria una sottoscrizione di Azure. [Iscriversi per ottenere una versione di valutazione gratuita](https://azure.microsoft.com/pricing/free-trial/). |
| Uno SKU Key Vault Premium per importare le chiavi protette dal modulo di protezione hardware |Per ulteriori informazioni sui livelli di servizio e sulle funzionalità di Azure Key Vault, vedere [prezzi Key Vault](https://azure.microsoft.com/pricing/details/key-vault/). |
| Un modulo di protezione hardware dall'elenco HSM supportato e da uno strumento BYOK e istruzioni fornite dal fornitore del modulo di protezione hardware | È necessario avere le autorizzazioni per un modulo di protezione hardware e la conoscenza di base di come usare il modulo di protezione hardware. Vedere [HSM supportati](#supported-hsms). |
| Versione 2.1.0 o successiva dell'interfaccia della riga di comando di Azure | Vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli?view=azure-cli-latest).|

## <a name="supported-hsms"></a>HSM supportati

|Nome fornitore|Tipo fornitore|Modelli HSM supportati|Ulteriori informazioni|
|---|---|---|---|
|nCipher|Produttore<br/>Modulo di protezione hardware come servizio|<ul><li>famiglia nShield di HSM</li><li>nShield come servizio</ul>|[nCipher nuovo strumento e documentazione di BYOK](https://www.ncipher.com/products/key-management/cloud-microsoft-azure)|
|Thales|Produttore|<ul><li>Famiglia lunare HSM 7 con firmware versione 7,3 o successiva</li></ul>| [Strumento e documentazione di luna BYOK](https://supportportal.thalesgroup.com/csm?id=kb_article_view&sys_kb_id=3892db6ddb8fc45005c9143b0b961987&sysparm_article=KB0021016)|
|Fortanix|Produttore<br/>Modulo di protezione hardware come servizio|<ul><li>Servizio di gestione delle chiavi a difesa automatica (SDKMS)</li><li>SmartKey Equinix</li></ul>|[Esportazione delle chiavi di SDKMS nei provider di servizi cloud per BYOK-Azure Key Vault](https://support.fortanix.com/hc/en-us/articles/360040071192-Exporting-SDKMS-keys-to-Cloud-Providers-for-BYOK-Azure-Key-Vault)|
|Marvell|Produttore|Tutte le HSM LiquidSecurity con<ul><li>Versione firmware 2.0.4 o versioni successive</li><li>Firmware versione 3,2 o successiva</li></ul>|[Strumento e documentazione di Marvell BYOK](https://www.marvell.com/products/security-solutions/nitrox-hs-adapters/exporting-marvell-hsm-keys-to-cloud-azure-key-vault.html)|
|Cryptomathic|ISV (Enterprise Key Management System)|Più marche e modelli HSM, tra cui<ul><li>nCipher</li><li>Thales</li><li>Utimaco</li></ul>[Per informazioni dettagliate](https://www.cryptomathic.com/azurebyok) , vedere il sito Cryptomathic|[Documentazione e strumento BYOK di Cryptomathic](https://www.cryptomathic.com/azurebyok)|



## <a name="supported-key-types"></a>Tipi di chiave supportati

|Nome della chiave|Tipo di chiave|Dimensione chiavi|Origine|Descrizione|
|---|---|---|---|---|
|Chiave scambio chiavi (KEK)|RSA| 2.048 bit<br />3.072 bit<br />4.096 bit|Modulo di protezione hardware Azure Key Vault|Coppia di chiavi RSA supportata da HSM generata in Azure Key Vault|
|Chiave di destinazione|RSA|2.048 bit<br />3.072 bit<br />4.096 bit|HSM fornitore|Chiave da trasferire al modulo di protezione hardware Azure Key Vault|

## <a name="generate-and-transfer-your-key-to-the-key-vault-hsm"></a>Generare e trasferire la chiave nel modulo di protezione hardware Key Vault

Per generare e trasferire la chiave in un modulo di protezione hardware Key Vault:

* [Passaggio 1: generare un KEK](#step-1-generate-a-kek)
* [Passaggio 2: scaricare la chiave pubblica KEK](#step-2-download-the-kek-public-key)
* [Passaggio 3: generare e preparare la chiave per il trasferimento](#step-3-generate-and-prepare-your-key-for-transfer)
* [Passaggio 4: trasferire la chiave a Azure Key Vault](#step-4-transfer-your-key-to-azure-key-vault)

### <a name="step-1-generate-a-kek"></a>Passaggio 1: generare un KEK

Un KEK è una chiave RSA generata in un modulo di protezione hardware Key Vault. Il KEK viene usato per crittografare la chiave che si vuole importare (la chiave di *destinazione* ).

La chiave KEK deve essere:
- Una chiave RSA-HSM (2.048 bit, 3.072 bit o 4.096 bit)
- Generato nello stesso insieme di credenziali delle chiavi in cui si intende importare la chiave di destinazione
- Creato con operazioni chiave consentite impostate su`import`

> [!NOTE]
> La chiave KEK deve avere ' Import ' come unica operazione di chiave consentita. ' Import ' si escludono a vicenda con tutte le altre operazioni chiave.

Usare il comando [AZ Key Vault Key create](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-create) per creare un KEK con operazioni chiave impostate su `import` . Registrare l'identificatore di chiave ( `kid` ) restituito dal comando seguente. Si userà il `kid` valore nel [passaggio 3](#step-3-generate-and-prepare-your-key-for-transfer).

```azurecli
az keyvault key create --kty RSA-HSM --size 4096 --name KEKforBYOK --ops import --vault-name ContosoKeyVaultHSM
```

### <a name="step-2-download-the-kek-public-key"></a>Passaggio 2: scaricare la chiave pubblica KEK

Usare il comando [AZ Key Vault Key Download](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-download) per scaricare la chiave pubblica KEK in un file con estensione PEM. La chiave di destinazione importata viene crittografata usando la chiave pubblica KEK.

```azurecli
az keyvault key download --name KEKforBYOK --vault-name ContosoKeyVaultHSM --file KEKforBYOK.publickey.pem
```

Trasferire il file KEKforBYOK. PublicKey. pem nel computer offline. Questo file sarà necessario nel passaggio successivo.

### <a name="step-3-generate-and-prepare-your-key-for-transfer"></a>Passaggio 3: generare e preparare la chiave per il trasferimento

Consultare la documentazione del fornitore del modulo di protezione hardware per scaricare e installare lo strumento BYOK. Seguire le istruzioni del fornitore del modulo di protezione hardware per generare una chiave di destinazione e quindi creare un pacchetto di trasferimento della chiave (un file BYOK). Lo strumento BYOK utilizzerà il `kid` [passaggio 1](#step-1-generate-a-kek) e il file KEKforBYOK. PublicKey. pem scaricato nel [passaggio 2](#step-2-download-the-kek-public-key) per generare una chiave di destinazione crittografata in un file BYOK.

Trasferire il file BYOK nel computer connesso.

> [!NOTE] 
> L'importazione di chiavi RSA a 1.024 bit non è supportata. Attualmente, l'importazione di una chiave a curva ellittica (EC) non è supportata.
> 
> **Problema noto**: l'importazione di una chiave di destinazione RSA 4K da luna HSM è supportata solo con il firmware 7.4.0 o versione successiva.

### <a name="step-4-transfer-your-key-to-azure-key-vault"></a>Passaggio 4: trasferire la chiave a Azure Key Vault

Per completare l'importazione della chiave, trasferire il pacchetto di trasferimento della chiave (un file BYOK) dal computer disconnesso al computer connesso a Internet. Usare il comando [AZ Key Vault Key Import](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-import) per caricare il file BYOK nel modulo di protezione hardware Key Vault.

```azurecli
az keyvault key import --vault-name ContosoKeyVaultHSM --name ContosoFirstHSMkey --byok-file KeyTransferPackage-ContosoFirstHSMkey.byok
```

Se il caricamento riesce, l'interfaccia della riga di comando di Azure Visualizza le proprietà della chiave importata.

## <a name="next-steps"></a>Passaggi successivi

È ora possibile usare questa chiave HSM protetta nell'insieme di credenziali delle chiavi. Per ulteriori informazioni, vedere [il confronto tra prezzo e funzionalità](https://azure.microsoft.com/pricing/details/key-vault/).



