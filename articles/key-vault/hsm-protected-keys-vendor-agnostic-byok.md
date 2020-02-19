---
title: Come generare e trasferire chiavi protette dal modulo di protezione hardware per Azure Key Vault - Azure Key Vault | Microsoft Docs
description: Questo argomento permette di pianificare, generare e quindi trasferire le proprie chiavi HSM protette da usare con l'insieme di credenziali delle chiavi di Azure. Anche noto come BYOK o Bring Your Own Key.
services: key-vault
author: amitbapat
manager: devtiw
tags: azure-resource-manager
ms.service: key-vault
ms.topic: conceptual
ms.date: 02/17/2020
ms.author: ambapat
ms.openlocfilehash: bd70cfb58c9d89f1d454537721e22f36b1fd3d3e
ms.sourcegitcommit: b8f2fee3b93436c44f021dff7abe28921da72a6d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/18/2020
ms.locfileid: "77429293"
---
# <a name="import-hsm-protected-keys-to-key-vault-preview"></a>Importa chiavi HSM protette per Key Vault (anteprima)

> [!NOTE]
> Questa funzionalità è disponibile in anteprima e solo nelle aree **Stati Uniti orientali 2 EUAP** e **Stati Uniti centrali EUAP** . 

Per una maggiore sicurezza quando si usa Azure Key Vault, è possibile importare o generare chiavi in moduli di protezione hardware (HSM) che non lasciano mai il limite HSM. Questo scenario viene spesso definito con il termine modalità *Bring Your Own Key*o BYOK. Azure Key Vault usa la famiglia nCipher nShield di HSM (FIPS 140-2 livello 2 convalidata) per proteggere le chiavi.

Questo argomento include informazioni utili per pianificare, generare e quindi trasferire le proprie chiavi protette da HSM da usare con l'insieme di credenziali delle chiavi di Azure.

> [!NOTE]
> Questa funzionalità non è disponibile per Azure Cina 21Vianet. 
> 
> Questo metodo di importazione è disponibile solo per [HSM supportati](#supported-hsms). 

Per altre informazioni sull'insieme di credenziali di Azure, vedere [Cos'è l'insieme di credenziali delle chiavi di Azure?](key-vault-overview.md)  Per un'esercitazione introduttiva che illustra la creazione di un insieme di credenziali delle chiavi per chiavi con protezione HSM, vedere [Che cos'è Azure Key Vault?](key-vault-overview.md).

## <a name="overview"></a>Panoramica

* Generare una chiave (denominata chiave per lo scambio delle chiavi o KEK) in Key Vault. Deve essere una chiave RSA-HSM con ' Import ' come unica operazione chiave. Solo lo SKU Premium di Key Vault supporta le chiavi RSA-HSM.
* Scaricare la chiave pubblica di KEK come file con estensione PEM
* Trasferire la chiave pubblica KEK alla workstation offline connessa al modulo di protezione hardware locale.
* Dalla workstation offline, usare lo strumento BYOK fornito dal fornitore del modulo di protezione hardware per creare un file BYOK. 
* La chiave di destinazione viene crittografata con un KEK, che rimane crittografato fino a quando non viene trasferito alla Azure Key Vault HSM. Solo la versione crittografata della chiave lascia il modulo di protezione hardware locale.
* La chiave KEK generata all'interno del Azure Key Vault HSM e non è esportabile. HSM impone che non esista una versione chiara di KEK all'esterno del Key Vault HSM.
* KEK deve trovarsi nello stesso insieme di credenziali delle chiavi in cui deve essere importata la chiave di destinazione.
* Quando il file BYOK viene caricato in Key Vault, Key Vault HSM usa la chiave privata KEK per decrittografare il materiale della chiave di destinazione e importarlo come chiave del modulo di protezione hardware. Questa operazione viene eseguita interamente all'interno Key Vault HSM e la chiave di destinazione rimane sempre nel limite di protezione HSM.

## <a name="prerequisites"></a>Prerequisiti

Nella tabella seguente sono elencati i prerequisiti relativi alla modalità BYOK per l'insieme di credenziali delle chiavi di Azure.

| Requisito | Ulteriori informazioni |
| --- | --- |
| Sottoscrizione di Azure |Per creare un insieme di credenziali delle chiavi di Azure, è necessaria una sottoscrizione di Azure: [Iscriversi per una versione di valutazione gratuita](https://azure.microsoft.com/pricing/free-trial/) |
| Un insieme di credenziali delle chiavi (SKU Premium) per importare chiavi HSM protette |Per altre informazioni su livelli di servizio e funzionalità per l'insieme di credenziali delle chiavi di Azure, vedere il sito Web relativo ai [prezzi dell'insieme di credenziali delle chiavi di Azure](https://azure.microsoft.com/pricing/details/key-vault/). |
| Un modulo di protezione hardware dall'elenco HSM supportato insieme allo strumento BYOK e alle istruzioni fornite dal fornitore del modulo di protezione hardware | È necessario avere accesso a un modulo di protezione hardware e a una conoscenza operativa di base della HSM. Vedere [HSM supportati](#supported-hsms). |
| Versione 2.0.82 o successiva dell'interfaccia della riga di comando di Azure | Per altre informazioni, vedere [installare l'interfaccia della](/cli/azure/install-azure-cli?view=azure-cli-latest) riga di comando di Azure.|

## <a name="supported-hsms"></a>HSM supportati

|Nome del fornitore del modulo di protezione hardware|Modelli HSM supportati|Ulteriori dettagli|
|---|---|---|
|Thales|Famiglia SafeNet Luna HSM 7 con firmware versione 7,3 o successiva| [Documentazione e strumento SafeNet Luna BYOK](https://safenet.gemalto.com/blah-blah)|


> [!NOTE]
> Per importare le chiavi protette da HSM dalla famiglia nCipher nShield di HSM, [usare la procedura BYOK legacy](hsm-protected-keys-legacy.md)


## <a name="generate-and-transfer-your-key-to-azure-key-vault-hsm"></a>Generare e trasferire la chiave al modulo di protezione hardware dell'insieme di credenziali delle chiavi di Azure

Usare i passaggi seguenti per generare e trasferire la chiave in un modulo di protezione hardware Azure Key Vault:

* [Passaggio 1: generare un KEK](#step-1-generate-a-kek)
* [Passaggio 2: scaricare la chiave pubblica KEK](#step-2-download-kek-public-key)
* [Passaggio 3: generare e preparare la chiave per il trasferimento](#step-3-generate-and-prepare-your-key-for-transfer)
* [Passaggio 4: trasferire la chiave a Azure Key Vault](#step-4-transfer-your-key-to-azure-key-vault)

### <a name="step-1-generate-a-kek"></a>Passaggio 1: generare un KEK

La chiave di scambio delle chiavi (KEK) è una chiave RSA generata nel modulo di protezione hardware Key Vault. Questa chiave viene usata per crittografare la chiave da importare (chiave di destinazione).

KEK deve essere:
1. una chiave **RSA-HSM** (2048 bit o 3072 bit o 4096 bit)
2. generato nello stesso insieme di credenziali delle chiavi in cui si intende importare la chiave di destinazione
3. creato con operazioni chiave consentite impostate per l' **importazione**

Usare il comando [AZ Key Vault Key create](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-create) per creare KEK con le operazioni chiave impostate per l'importazione. Annotare l'identificatore di chiave ' Kid ' restituito dal comando seguente. Sarà necessario nel [passaggio 3](#step-3-generate-and-prepare-your-key-for-transfer).


```azurecli
az keyvault key create --kty RSA-HSM --size 4096 --name KEKforBYOK --ops import --vault-name ContosoKeyVaultHSM
```

### <a name="step-2-download-kek-public-key"></a>Passaggio 2: scaricare la chiave pubblica KEK

Usare il comando [AZ Key Vault download](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-download) per scaricare la chiave pubblica KEK in un file con estensione PEM. La chiave di destinazione importata viene crittografata con la chiave pubblica KEK.

```azurecli
az keyvault key download --name KEKforBYOK --vault-name ContosoKeyVaultHSM --file KEKforBYOK.publickey.pem
```

Trasferire il file KEKforBYOK. PublicKey. pem nella workstation offline. Questo file sarà necessario durante il passaggio successivo.

### <a name="step-3-generate-and-prepare-your-key-for-transfer"></a>Passaggio 3: generare e preparare la chiave per il trasferimento

Consultare la documentazione del fornitore del modulo di protezione hardware per scaricare e installare lo strumento BYOK. Seguire le istruzioni del fornitore del modulo di protezione hardware per generare una chiave di destinazione e quindi creare un pacchetto di trasferimento della chiave (un file BYOK). Lo strumento BYOK utilizzerà l'identificatore di chiave dei file [Step 1](#step-1-generate-a-kek) e KEKforBYOK. PublicKey. pem scaricati nel [passaggio 2](#step-2-download-kek-public-key) per generare una chiave di destinazione crittografata in un file BYOK.

Trasferire il file BYOK nella workstation connessa.

> [!NOTE] 
> La chiave di destinazione deve essere una chiave RSA di dimensione 2048 bit o 3072 bit o 4096 bit. L'importazione di chiavi a curva ellittica non è supportata in questo momento.
> <br/><strong>Problema noto:</strong> L'importazione della chiave di destinazione RSA 4K da SafeNet Luna HSM ha esito negativo. Quando il problema viene risolto, questo documento verrà aggiornato.

### <a name="step-4-transfer-your-key-to-azure-key-vault"></a>Passaggio 4: trasferire la chiave a Azure Key Vault

Per questo passaggio finale, trasferire il pacchetto di trasferimento della chiave (un file BYOK) dalla workstation disconnessa alla workstation connessa a Internet e quindi usare il comando [AZ Key Vault Key Import](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-import) per caricare il file BYOK del modulo di protezione hardware Azure Key Vault per completare l'importazione della chiave.

```azurecli
az keyvault key import --vault-name ContosoKeyVaultHSM --name ContosoFirstHSMkey --byok-file KeyTransferPackage-ContosoFirstHSMkey.byok
```

Se il caricamento ha esito positivo, vengono visualizzate le proprietà della chiave appena importata.

## <a name="next-steps"></a>Passaggi successivi

È ora possibile usare questa chiave HSM protetta nell'insieme di credenziali delle chiavi. Per ulteriori informazioni, vedere il [confronto tra](https://azure.microsoft.com/pricing/details/key-vault/)prezzo e funzionalità.
