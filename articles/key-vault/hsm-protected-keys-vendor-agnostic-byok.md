---
title: Come generare e trasferire chiavi protette dal modulo di protezione hardware per Azure Key Vault - Azure Key Vault | Microsoft Docs
description: Usare questo articolo per pianificare, generare e trasferire chiavi HSM protette da usare con Azure Key Vault. Noto anche come BYOK (Bring your own key).
services: key-vault
author: amitbapat
manager: devtiw
tags: azure-resource-manager
ms.service: key-vault
ms.topic: conceptual
ms.date: 02/17/2020
ms.author: ambapat
ms.openlocfilehash: 9b8f1065660ea8331853f8804e709134fe682ba7
ms.sourcegitcommit: f27b045f7425d1d639cf0ff4bcf4752bf4d962d2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/23/2020
ms.locfileid: "77566115"
---
# <a name="import-hsm-protected-keys-to-key-vault-preview"></a>Importare chiavi con protezione HSM in Key Vault (anteprima)

> [!NOTE]
> Questa funzionalità è disponibile in anteprima e disponibile solo nelle aree di Azure *Stati Uniti orientali 2 EUAP* e *Stati Uniti centrali EUAP*. 

Per una maggiore sicurezza quando si usa Azure Key Vault, è possibile importare o generare una chiave in un modulo di protezione hardware (HSM); la chiave non lascerà mai il limite del modulo di protezione hardware. Questo scenario viene spesso definito *Bring your own key* (BYOK). Key Vault usa la famiglia nCipher nShield di HSM (FIPS 140-2 livello 2 convalidata) per proteggere le chiavi.

Usare le informazioni in questo articolo per pianificare, generare e trasferire chiavi HSM protette da usare con Azure Key Vault.

> [!NOTE]
> Questa funzionalità non è disponibile per Azure Cina 21Vianet. 
> 
> Questo metodo di importazione è disponibile solo per [HSM supportati](#supported-hsms). 

Per altre informazioni e per un'esercitazione su come iniziare a usare Key Vault (inclusa la procedura per creare un insieme di credenziali delle chiavi per le chiavi protette da HSM), vedere [che cos'è Azure Key Vault?](key-vault-overview.md).

## <a name="overview"></a>Panoramica

Ecco una panoramica del processo. I passaggi specifici da completare sono descritti più avanti in questo articolo.

* In Key Vault generare una chiave, definita chiave per lo *scambio delle chiavi* (KEK). KEK deve essere una chiave RSA-HSM con solo l'operazione di `import` chiave. Solo Key Vault SKU Premium supporta le chiavi RSA-HSM.
* Scaricare la chiave pubblica KEK come file con estensione PEM.
* Trasferire la chiave pubblica KEK a un computer offline connesso a un modulo di protezione hardware locale.
* Nel computer offline usare lo strumento BYOK fornito dal fornitore del modulo di protezione hardware per creare un file BYOK. 
* La chiave di destinazione viene crittografata con un KEK, che rimane crittografato fino a quando non viene trasferito al modulo di protezione hardware Key Vault. Solo la versione crittografata della chiave lascia il modulo di protezione hardware locale.
* Una KEK generata all'interno di un modulo di protezione hardware Key Vault non è esportabile. HSM impone la regola che non esista una versione non chiara di KEK all'esterno di un modulo di protezione hardware Key Vault.
* KEK deve trovarsi nello stesso insieme di credenziali delle chiavi in cui verrà importata la chiave di destinazione.
* Quando il file BYOK viene caricato in Key Vault, un modulo di protezione hardware Key Vault usa la chiave privata KEK per decrittografare il materiale della chiave di destinazione e importarlo come chiave del modulo di protezione hardware. Questa operazione viene eseguita interamente all'interno di un modulo di protezione hardware Key Vault. La chiave di destinazione rimane sempre nel limite di protezione HSM.

## <a name="prerequisites"></a>Prerequisites

La tabella seguente elenca i prerequisiti per l'uso di BYOK in Azure Key Vault:

| Requisito | Ulteriori informazioni |
| --- | --- |
| Una sottoscrizione di Azure. |Per creare un insieme di credenziali delle chiavi in Azure Key Vault, è necessaria una sottoscrizione di Azure. [Iscriversi per ottenere una versione di valutazione gratuita](https://azure.microsoft.com/pricing/free-trial/). |
| Uno SKU Key Vault Premium per importare le chiavi protette dal modulo di protezione hardware |Per ulteriori informazioni sui livelli di servizio e sulle funzionalità di Azure Key Vault, vedere [prezzi Key Vault](https://azure.microsoft.com/pricing/details/key-vault/). |
| Un modulo di protezione hardware dall'elenco HSM supportato e da uno strumento BYOK e istruzioni fornite dal fornitore del modulo di protezione hardware | È necessario avere le autorizzazioni per un modulo di protezione hardware e la conoscenza di base di come usare il modulo di protezione hardware. Vedere [HSM supportati](#supported-hsms). |
| Versione 2.1.0 o successiva dell'interfaccia della riga di comando di Azure | Vedere [installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli?view=azure-cli-latest).|

## <a name="supported-hsms"></a>HSM supportati

|Nome del fornitore del modulo di protezione hardware|Modelli HSM supportati|Ulteriori informazioni|
|---|---|---|
|Thales|Famiglia SafeNet Luna HSM 7 con firmware versione 7,3 o successiva| [Documentazione e strumento SafeNet Luna BYOK](https://supportportal.thalesgroup.com/csm?id=kb_article_view&sys_kb_id=3892db6ddb8fc45005c9143b0b961987&sysparm_article=KB0021016)|

> [!NOTE]
> Per importare chiavi HSM protette dalla famiglia di HSM nShield di nCipher, usare la [procedura BYOK legacy](hsm-protected-keys-legacy.md).

## <a name="supported-key-types"></a>Tipi di chiave supportati

|Nome della chiave|Tipo di chiave|Dimensioni chiave|Origine|Descrizione|
|---|---|---|---|---|
|Chiave scambio chiavi (KEK)|RSA| 2\.048 bit<br />3\.072 bit<br />4\.096 bit|Modulo di protezione hardware Azure Key Vault|Coppia di chiavi RSA supportata da HSM generata in Azure Key Vault|
|Chiave di destinazione|RSA|2\.048 bit<br />3\.072 bit<br />4\.096 bit|HSM fornitore|Chiave da trasferire al modulo di protezione hardware Azure Key Vault|

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
- generato nello stesso insieme di credenziali delle chiavi in cui si intende importare la chiave di destinazione
- Creato con operazioni chiave consentite impostate su `import`

Usare il comando [AZ Key Vault Key create](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-create) per creare un KEK con operazioni chiave impostate su `import`. Registrare l'identificatore di chiave (`kid`) restituito dal comando seguente. Il valore `kid` sarà utilizzato nel [passaggio 3](#step-3-generate-and-prepare-your-key-for-transfer).

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

Consultare la documentazione del fornitore del modulo di protezione hardware per scaricare e installare lo strumento BYOK. Seguire le istruzioni del fornitore del modulo di protezione hardware per generare una chiave di destinazione e quindi creare un pacchetto di trasferimento della chiave (un file BYOK). Lo strumento BYOK utilizzerà la `kid` del [passaggio 1](#step-1-generate-a-kek) e il file KEKforBYOK. PublicKey. pem scaricato nel [passaggio 2](#step-2-download-the-kek-public-key) per generare una chiave di destinazione crittografata in un file BYOK.

Trasferire il file BYOK nel computer connesso.

> [!NOTE] 
> L'importazione di chiavi RSA a 1.024 bit non è supportata. Attualmente, l'importazione di una chiave a curva ellittica (EC) non è supportata.
> 
> **Problema noto**: l'importazione di una chiave di destinazione RSA 4K da SafeNet Luna HSM ha esito negativo. Quando il problema viene risolto, questo articolo verrà aggiornato.

### <a name="step-4-transfer-your-key-to-azure-key-vault"></a>Passaggio 4: trasferire la chiave a Azure Key Vault

Per completare l'importazione della chiave, trasferire il pacchetto di trasferimento della chiave (un file BYOK) dal computer disconnesso al computer connesso a Internet. Usare il comando [AZ Key Vault Key Import](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-import) per caricare il file BYOK nel modulo di protezione hardware Key Vault.

```azurecli
az keyvault key import --vault-name ContosoKeyVaultHSM --name ContosoFirstHSMkey --byok-file KeyTransferPackage-ContosoFirstHSMkey.byok
```

Se il caricamento riesce, l'interfaccia della riga di comando di Azure Visualizza le proprietà della chiave importata.

## <a name="next-steps"></a>Passaggi successivi

È ora possibile usare questa chiave HSM protetta nell'insieme di credenziali delle chiavi. Per ulteriori informazioni, vedere [il confronto tra prezzo e funzionalità](https://azure.microsoft.com/pricing/details/key-vault/).



