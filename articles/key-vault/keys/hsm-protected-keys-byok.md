---
title: Come generare e trasferire chiavi con protezione HSM - BYOK - Azure Key Vault
description: Questo articolo permette di pianificare, generare e trasferire le proprie chiavi con protezione HSM da usare con Azure Key Vault. Questo approccio è noto anche come BYOK o Bring Your Own Key.
services: key-vault
author: amitbapat
manager: devtiw
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: keys
ms.topic: tutorial
ms.date: 05/29/2020
ms.author: ambapat
ms.openlocfilehash: 1869ec9b617a7451ec42fa9d092ea3bb5834f9e8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88585475"
---
# <a name="import-hsm-protected-keys-to-key-vault-byok"></a>Importare chiavi con protezione HSM in Key Vault

Per una maggiore sicurezza, quando si usa Azure Key Vault è possibile importare o generare una chiave in un modulo di protezione hardware (HSM, Hardware Security Module). La chiave non uscirà mai dai limiti del modulo di protezione hardware. Questo scenario viene spesso definito *BYOK* (Bring Your Own Key). Key Vault usa i moduli di protezione hardware della famiglia di prodotti nCipher nShield (con convalida FIPS 140-2 Livello 2) per proteggere le chiavi.

Questo articolo include informazioni utili per pianificare, generare e trasferire le proprie chiavi con protezione HSM da usare con Azure Key Vault.

> [!NOTE]
> Questa funzionalità non è disponibile per Azure Cina 21Vianet. 
> 
> Questo metodo di importazione è disponibile solo per i [moduli di protezione hardware supportati](#supported-hsms). 

Per altre informazioni e per un'esercitazione introduttiva per Key Vault, che illustra anche come creare un insieme di credenziali delle chiavi per chiavi con protezione HSM, vedere [Informazioni su Azure Key Vault](../general/overview.md).

## <a name="overview"></a>Panoramica

Ecco una panoramica del processo. I passaggi specifici da completare sono illustrati più avanti nell'articolo.

* In Key Vault generare una chiave, definita *chiave per lo scambio delle chiavi* (KEK, Key Exchange Key). La chiave KEK deve essere una chiave RSA-HSM che ha solo l'operazione `import` della chiave. Solo lo SKU di Key Vault Premium supporta le chiavi RSA-HSM.
* Scaricare la chiave pubblica KEK come file PEM.
* Trasferire la chiave pubblica KEK in un computer offline connesso a un modulo di protezione hardware locale.
* Nel computer offline usare lo strumento BYOK fornito dal fornitore del modulo di protezione hardware per creare un file BYOK. 
* La chiave di destinazione è crittografata con una chiave KEK, che rimane crittografata fino al trasferimento nel modulo di protezione hardware di Key Vault. Solo la versione crittografata della chiave esce dal modulo di protezione hardware locale.
* Una chiave KEK generata in un modulo di protezione hardware di Key Vault non è esportabile. I moduli di protezione hardware impongono la regola in base alla quale non esistono versioni non crittografate di una chiave KEK all'esterno di un modulo di protezione hardware di Key Vault.
* La chiave KEK deve trovarsi nello stesso insieme di credenziali delle chiavi in cui verrà importata la chiave di destinazione.
* Quando il file BYOK viene caricato in Key Vault, un modulo di protezione hardware di Key Vault usa la chiave privata KEK per decrittografare il materiale della chiave di destinazione e importarla come chiave del modulo di protezione hardware. Questa operazione viene eseguita interamente in un modulo di protezione hardware di Key Vault. La chiave di destinazione rimane sempre entro i limiti di protezione del modulo di protezione hardware.

## <a name="prerequisites"></a>Prerequisiti

La tabella seguente elenca i prerequisiti per l'uso di BYOK in Azure Key Vault:

| Requisito | Altre informazioni |
| --- | --- |
| Una sottoscrizione di Azure |Per creare un insieme di credenziali delle chiavi in Azure Key Vault, è necessaria una sottoscrizione di Azure. [Iscriversi per ottenere una versione di valutazione gratuita](https://azure.microsoft.com/pricing/free-trial/). |
| Uno SKU di Key Vault Premium per importare le chiavi con protezione HSM |Per altre informazioni sui livelli di servizio e sulle funzionalità in Azure Key Vault, vedere [Prezzi di Key Vault ](https://azure.microsoft.com/pricing/details/key-vault/). |
| Un modulo di protezione hardware dall'elenco di moduli di protezione hardware supportati, uno strumento BYOK e le istruzioni fornite dal fornitore del modulo di protezione hardware | È necessario avere le autorizzazioni per un modulo di protezione hardware e avere una conoscenza di base dell'uso di un modulo di protezione hardware. Vedere [Moduli di protezione hardware supportati](#supported-hsms). |
| Interfaccia della riga di comando di Azure versione 2.1.0 o successiva | Vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli?view=azure-cli-latest).|

## <a name="supported-hsms"></a>Moduli di protezione hardware supportati

|Nome del fornitore|Tipo di fornitore|Modelli di moduli di protezione hardware supportati|Ulteriori informazioni|
|---|---|---|---|
|nCipher|Produttore,<br/>modulo di protezione hardware come servizio|<ul><li>Famiglia di moduli di protezione hardware nShield</li><li>nShield come servizio</ul>|[Nuovo strumento BYOK e documentazione di nCipher](https://www.ncipher.com/products/key-management/cloud-microsoft-azure)|
|Thales|Produttore|<ul><li>Famiglia di prodotti Luna HSM 7 con firmware con versione 7.3 o successiva</li></ul>| [Strumento BYOK e documentazione di Luna](https://supportportal.thalesgroup.com/csm?id=kb_article_view&sys_kb_id=3892db6ddb8fc45005c9143b0b961987&sysparm_article=KB0021016)|
|Fortanix|Produttore,<br/>modulo di protezione hardware come servizio|<ul><li>Self-Defending Key Management Service (SDKMS)</li><li>Equinix SmartKey</li></ul>|[Esportazione di chiavi SDKMS nei provider di servizi cloud per BYOK - Azure Key Vault](https://support.fortanix.com/hc/en-us/articles/360040071192-Exporting-SDKMS-keys-to-Cloud-Providers-for-BYOK-Azure-Key-Vault)|
|Marvell|Produttore|Moduli di protezione hardware di All LiquidSecurity con<ul><li>Firmware con versione 2.0.4 o successiva</li><li>Firmware con versione 3.2 o successiva</li></ul>|[Strumento BYOK e documentazione di Marvell](https://www.marvell.com/products/security-solutions/nitrox-hs-adapters/exporting-marvell-hsm-keys-to-cloud-azure-key-vault.html)|
|Cryptomathic|ISV (Enterprise Key Management System)|Più marchi e modelli di moduli di protezione hardware, tra cui<ul><li>nCipher</li><li>Thales</li><li>Utimaco</li></ul>Per dettagli, vedere il [sito di Cryptomathic](https://www.cryptomathic.com/azurebyok)|[Strumento BYOK e documentazione di Cryptomathic](https://www.cryptomathic.com/azurebyok)|



## <a name="supported-key-types"></a>Tipi di chiave supportati

|Nome della chiave|Tipo di chiave|Dimensione chiavi|Origine|Descrizione|
|---|---|---|---|---|
|Chiave KEK (Key Exchange Key)|RSA| A 2.048 bit<br />A 3.072 bit<br />A 4.096 bit|Modulo di protezione hardware di Azure Key Vault|Coppia di chiavi RSA supportata da modulo di protezione hardware generata in Azure Key Vault|
|Chiave di destinazione|RSA|A 2.048 bit<br />A 3.072 bit<br />A 4.096 bit|Modulo di protezione hardware del fornitore|Chiave da trasferire nel modulo di protezione hardware di Azure Key Vault|

## <a name="generate-and-transfer-your-key-to-the-key-vault-hsm"></a>Generare e trasferire la chiave al modulo di protezione hardware di Key Vault

Per generare e trasferire la chiave al modulo di protezione hardware di Key Vault:

* [Passaggio 1: Generare una chiave KEK](#step-1-generate-a-kek)
* [Passaggio 2: Scaricare la chiave pubblica KEK](#step-2-download-the-kek-public-key)
* [Passaggio 3: Generare e preparare la chiave per il trasferimento](#step-3-generate-and-prepare-your-key-for-transfer)
* [Passaggio 4: Trasferire la chiave ad Azure Key Vault](#step-4-transfer-your-key-to-azure-key-vault)

### <a name="step-1-generate-a-kek"></a>Passaggio 1: Generare una chiave KEK

Una chiave KEK è una chiave RSA generata in un modulo di protezione hardware di Key Vault. La chiave KEK viene usata per crittografare la chiave da importare, ovvero la chiave di *destinazione*.

La chiave KEK deve essere:
- Una chiave RSA-HSM (a 2.048 bit, a 3.072 bit o a 4.096 bit)
- Generata nello stesso insieme di credenziali delle chiavi in cui si vuole importare la chiave di destinazione.
- Creata con operazioni di chiave consentite impostate su `import`

> [!NOTE]
> La chiave KEK deve avere 'import' come unica operazione di chiave consentita. L'operazione 'import' e tutte le altre operazioni delle chiavi si escludono a vicenda.

Usare il comando [az keyvault key create](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-create) per creare una chiave KEK con operazioni della chiave impostate su `import`. Registrare l'identificatore della chiave (`kid`) restituito dal comando seguente. Il valore `kid` verrà usato nel [Passaggio 3](#step-3-generate-and-prepare-your-key-for-transfer).

```azurecli
az keyvault key create --kty RSA-HSM --size 4096 --name KEKforBYOK --ops import --vault-name ContosoKeyVaultHSM
```

### <a name="step-2-download-the-kek-public-key"></a>Passaggio 2: Scaricare la chiave pubblica KEK

Usare [az keyvault key download](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-download) per scaricare la chiave pubblica KEK in un file PEM. La chiave di destinazione importata viene crittografata mediante la chiave pubblica KEK.

```azurecli
az keyvault key download --name KEKforBYOK --vault-name ContosoKeyVaultHSM --file KEKforBYOK.publickey.pem
```

Trasferire il file KEKforBYOK.publickey.pem nel computer offline. Questo file sarà necessario nel passaggio successivo.

### <a name="step-3-generate-and-prepare-your-key-for-transfer"></a>Passaggio 3: Generare e preparare la chiave per il trasferimento

Vedere la documentazione del fornitore del modulo di protezione hardware per scaricare e installare lo strumento BYOK. Seguire le istruzioni del fornitore del modulo di protezione hardware per generare una chiave di destinazione e quindi creare il pacchetto di trasferimento della chiave, ovvero un file BYOK. Lo strumento BYOK userà il valore `kid` dal [Passaggio 1](#step-1-generate-a-kek) e il file KEKforBYOK.publickey.pem scaricato nel [Passaggio 2](#step-2-download-the-kek-public-key) per generare una chiave di destinazione crittografata in un file BYOK.

Trasferire il file BYOK nel computer connesso.

> [!NOTE] 
> L'importazione delle chiavi RSA a 1.024 bit non è supportata. Non è attualmente supportata l'importazione di una chiave a curva ellittica (EC).
> 
> **Problema noto**: l'importazione di una chiave di destinazione RSA 4K da moduli di protezione hardware di Luna è supportata solo con firmware 7.4.0 o versioni successive.

### <a name="step-4-transfer-your-key-to-azure-key-vault"></a>Passaggio 4: Trasferire la chiave ad Azure Key Vault

Per completare l'importazione della chiave, trasferire il pacchetto di trasferimento della chiave, ovvero un file BYOK, dal computer disconnesso al computer connesso a Internet. Usare il comando [az keyvault key import](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-import) per caricare il file BYOK nel modulo di protezione hardware di Key Vault.

```azurecli
az keyvault key import --vault-name ContosoKeyVaultHSM --name ContosoFirstHSMkey --byok-file KeyTransferPackage-ContosoFirstHSMkey.byok
```

Se il caricamento ha esito positivo, l'interfaccia della riga di comando di Azure mostrerà le proprietà della chiave importata.

## <a name="next-steps"></a>Passaggi successivi

È ora possibile usare questa chiave HSM protetta nell'insieme di credenziali delle chiavi. Per altre informazioni, vedere [questo confronto tra prezzi e funzionalità](https://azure.microsoft.com/pricing/details/key-vault/).



