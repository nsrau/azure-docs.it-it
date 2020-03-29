---
title: Come generare e trasferire chiavi protette dal modulo di protezione hardware per Azure Key Vault - Azure Key Vault | Microsoft Docs
description: Usare questo articolo per pianificare, generare e trasferire le proprie chiavi protette da HSM da usare con l'insieme di credenziali delle chiavi di Azure.Use this article to help you plan, generate, and transfer your own HSM-protected keys to use with Azure Key Vault. Conosciuto anche come portare la propria chiave (BYOK).
services: key-vault
author: amitbapat
manager: devtiw
tags: azure-resource-manager
ms.service: key-vault
ms.topic: conceptual
ms.date: 02/17/2020
ms.author: ambapat
ms.openlocfilehash: 08a4330f4a786deca8ddb2f1c6803b29152e7f50
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79080131"
---
# <a name="import-hsm-protected-keys-to-key-vault-preview"></a>Importare chiavi con protezione HSM in Key Vault (anteprima)

> [!NOTE]
> Questa funzionalità è in anteprima ed è disponibile solo nelle aree di Azure *Stati Uniti orientali 2 EUAP* e *EUAP degli Stati Uniti centrali.* 

Per una maggiore garanzia quando si usa l'insieme di credenziali delle chiavi di Azure, è possibile importare o generare una chiave in un modulo di sicurezza hardware (HSM); la chiave non lascerà mai il limite HSM. Questo scenario viene spesso definito *bring your own key* (BYOK). Key Vault utilizza la famiglia nCipher nShield degli HSM (convalidati FIPS 140-2 Livello 2) per proteggere le chiavi.

Usare le informazioni contenute in questo articolo per pianificare, generare e trasferire le proprie chiavi protette da HSM da usare con l'insieme di credenziali delle chiavi di Azure.Use the information in this article to help you plan, generate, and transfer your own HSM-protected keys to use with Azure Key Vault.

> [!NOTE]
> Questa funzionalità non è disponibile per Azure China 21Vianet.This functionality is not available for Azure China 21Vianet. 
> 
> Questo metodo di importazione è disponibile solo per [gli HSM supportati.](#supported-hsms) 

Per altre informazioni e per un'esercitazione per iniziare a usare l'insieme di credenziali delle chiavi (incluso come creare un insieme di credenziali delle chiavi per le chiavi protette da HSM), vedere [Che cos'è l'insieme di](key-vault-overview.md)credenziali delle chiavi di Azure? .

## <a name="overview"></a>Panoramica

Ecco una panoramica del processo. I passaggi specifici da completare sono descritti più avanti in questo articolo.

* In Key Vault, generare una chiave (denominata *Key Exchange Key* (KEK). La chiave KEK deve essere una chiave RSA-HSM con solo il funzionamento del `import` tasto. Solo lo SKU Key Vault Premium supporta le chiavi RSA-HSM.
* Scaricare la chiave pubblica KEK come file .pem.
* Trasferire la chiave pubblica KEK in un computer offline connesso a un HSM locale.
* Nel computer offline, utilizzare lo strumento BYOK fornito dal fornitore HSM per creare un file BYOK. 
* La chiave di destinazione viene crittografata con una chiave KEK, che rimane crittografata fino a quando non viene trasferita al Key Vault HSM. Solo la versione crittografata della chiave lascia l'HSM locale.
* Una chiave KEK generata all'interno di un Vault HSM delle chiavi non è esportabile. Gli HSM applicano la regola che non esiste alcuna versione chiara di una chiave KEK all'esterno di un HSM del Vault delle chiavi.
* La chiave KEK deve trovarsi nello stesso insieme di credenziali delle chiavi in cui verrà importata la chiave di destinazione.
* Quando il file BYOK viene caricato in Key Vault, un Vault chiave HSM utilizza la chiave privata KEK per decrittografare il materiale della chiave di destinazione e importarlo come chiave HSM. Questa operazione avviene interamente all'interno di un Key Vault HSM. La chiave di destinazione rimane sempre nel limite di protezione HSM.

## <a name="prerequisites"></a>Prerequisiti

Nella tabella seguente sono elencati i prerequisiti per l'uso di BYOK nell'insieme di credenziali delle chiavi di Azure:The following table lists prerequisites for using BYOK in Azure Key Vault:

| Requisito | Ulteriori informazioni |
| --- | --- |
| Una sottoscrizione di Azure. |Per creare un insieme di credenziali delle chiavi in Azure Key Vault, è necessaria una sottoscrizione di Azure.To create a key vault in Azure Key Vault, you need an Azure subscription. [Iscriviti per una prova gratuita](https://azure.microsoft.com/pricing/free-trial/). |
| Uno SKU di Key Vault Premium per importare le chiavi protette da HSM |Per altre informazioni sui livelli di servizio e sulle funzionalità in Archiviazione delle chiavi di Azure, vedere [Prezzi dell'insieme di](https://azure.microsoft.com/pricing/details/key-vault/)credenziali delle chiavi. |
| Un HSM dall'elenco HSM supportato e uno strumento BYOK e le istruzioni fornite dal fornitore HSM | È necessario disporre delle autorizzazioni per un HSM e una conoscenza di base di come utilizzare l'HSM. Vedere [HSM supportati](#supported-hsms). |
| Interfaccia della riga di comando di Azure versione 2.1.0 o successivaAzure CLI versione 2.1.0 o versione successivaAzure CLI version 2.1 | Vedere [Installare l'interfaccia della riga di comando di Azure.](/cli/azure/install-azure-cli?view=azure-cli-latest)|

## <a name="supported-hsms"></a>HSM supportati

|Nome fornitore|Tipo di fornitore|Modelli HSM supportati|Ulteriori informazioni|
|---|---|---|---|
|Thales|Produttore|Famiglia SafeNet Luna HSM 7 con firmware versione 7.3 o successiva| [Strumento e documentazione SafeNet Luna BYOK](https://supportportal.thalesgroup.com/csm?id=kb_article_view&sys_kb_id=3892db6ddb8fc45005c9143b0b961987&sysparm_article=KB0021016)|
|Fortanix|HSM come servizio|Servizio di gestione delle chiavi (SDKMS) con gestione automatica|[Esportazione di chiavi SDKMS in cloud provider per BYOK - Archiviazione delle chiavi di AzureExporting SDKMS keys to Cloud Providers for BYOK - Azure Key Vault](https://support.fortanix.com/hc/en-us/articles/360040071192-Exporting-SDKMS-keys-to-Cloud-Providers-for-BYOK-Azure-Key-Vault)|


> [!NOTE]
> Per importare chiavi protette da HSM dalla famiglia nCipher nShield di HSMs, utilizzare la [procedura BYOK legacy](hsm-protected-keys-legacy.md).

## <a name="supported-key-types"></a>Tipi di chiave supportati

|Nome della chiave|Tipo di chiave|Dimensione chiavi|Origine|Descrizione|
|---|---|---|---|---|
|Chiave di scambio chiavi (KEK)|RSA| 2.048 bit<br />3.072 bit<br />4.096 bit|Azure Key Vault HSM|Una coppia di chiavi RSA supportata da HSM generata in Azure Key VaultAn HSM-backed RSA key pair generated in Azure Key Vault|
|Chiave di destinazione|RSA|2.048 bit<br />3.072 bit<br />4.096 bit|Fornitore HSM|The key to be transferred to the Azure Key Vault HSM|

## <a name="generate-and-transfer-your-key-to-the-key-vault-hsm"></a>Generare e trasferire la chiave nel Vault HSM chiave

Per generare e trasferire la chiave in un Vault HSM chiave:

* [Passaggio 1: Generare una manofilaStep 1: Generate a KEK](#step-1-generate-a-kek)
* [Passaggio 2: Scaricare la chiave pubblica KEKStep 2: Download the KEK public key](#step-2-download-the-kek-public-key)
* [Passaggio 3: Generare e preparare la chiave per il trasferimentoStep 3: Generate and prepare your key for transfer](#step-3-generate-and-prepare-your-key-for-transfer)
* [Passaggio 4: Trasferire la chiave nell'insieme di credenziali delle chiavi di AzureStep 4: Transfer your key to Azure Key Vault](#step-4-transfer-your-key-to-azure-key-vault)

### <a name="step-1-generate-a-kek"></a>Passaggio 1: Generare una manofilaStep 1: Generate a KEK

Una chiave KEK è una chiave RSA generata in un Key Vault HSM. La chiave KEK viene utilizzata per crittografare la chiave che si desidera importare (la chiave di *destinazione).*

La KEK deve essere:
- Una chiave RSA-HSM (2.048 bit; 3.072 bit; o 4.096 bit)
- Generato nello stesso insieme di credenziali delle chiavi in cui si intende importare la chiave di destinazione
- Creato con le operazioni chiave consentite impostate su`import`

> [!NOTE]
> La chiave KEK deve avere 'import' come unica operazione chiave consentita. 'import' si escludono a vicenda con tutte le altre operazioni chiave.

Utilizzare il comando [az keyvault create](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-create) per creare una `import`chiave KEK con le operazioni dei tasti impostate su . Registrare l'identificatore di chiave (`kid`) restituito dal comando seguente. (Si utilizzerà `kid` il valore nel [passaggio 3](#step-3-generate-and-prepare-your-key-for-transfer).)

```azurecli
az keyvault key create --kty RSA-HSM --size 4096 --name KEKforBYOK --ops import --vault-name ContosoKeyVaultHSM
```

### <a name="step-2-download-the-kek-public-key"></a>Passaggio 2: Scaricare la chiave pubblica KEKStep 2: Download the KEK public key

Utilizzare [az keyvault download](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-download) per scaricare la chiave pubblica KEK in un file .pem. La chiave di destinazione importata viene crittografata utilizzando la chiave pubblica KEK.

```azurecli
az keyvault key download --name KEKforBYOK --vault-name ContosoKeyVaultHSM --file KEKforBYOK.publickey.pem
```

Trasferire il file KEKforBYOK.publickey.pem nel computer offline. Questo file sarà necessario nel passaggio successivo.

### <a name="step-3-generate-and-prepare-your-key-for-transfer"></a>Passaggio 3: Generare e preparare la chiave per il trasferimentoStep 3: Generate and prepare your key for transfer

Consultare la documentazione del fornitore HSM per scaricare e installare lo strumento BYOK. Seguire le istruzioni del fornitore HSM per generare una chiave di destinazione e quindi creare un pacchetto di trasferimento della chiave (un file BYOK). Lo strumento BYOK `kid` utilizzerà il [passaggio 1](#step-1-generate-a-kek) e il file KEKforBYOK.publickey.pem scaricato nel [passaggio 2](#step-2-download-the-kek-public-key) per generare una chiave di destinazione crittografata in un file BYOK.

Trasferire il file BYOK sul computer collegato.

> [!NOTE] 
> L'importazione di chiavi RSA a 1.024 bit non è supportata. Attualmente, l'importazione di una chiave EC (Ellittic Curve) non è supportata.
> 
> **Problema noto**: l'importazione di una chiave di destinazione RSA 4K da SafeNet Luna HSMs è supportata solo con firmware 7.4.0 o versione più recente.

### <a name="step-4-transfer-your-key-to-azure-key-vault"></a>Passaggio 4: Trasferire la chiave nell'insieme di credenziali delle chiavi di AzureStep 4: Transfer your key to Azure Key Vault

Per completare l'importazione della chiave, trasferire il pacchetto di trasferimento della chiave (un file BYOK) dal computer disconnesso al computer connesso a Internet. Utilizzare il comando [az keyvault import](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-import) per caricare il file BYOK nel Key Vault HSM.

```azurecli
az keyvault key import --vault-name ContosoKeyVaultHSM --name ContosoFirstHSMkey --byok-file KeyTransferPackage-ContosoFirstHSMkey.byok
```

Se il caricamento ha esito positivo, l'interfaccia della riga di comando di Azure visualizza le proprietà della chiave importata.

## <a name="next-steps"></a>Passaggi successivi

È ora possibile usare questa chiave HSM protetta nell'insieme di credenziali delle chiavi. Per ulteriori informazioni, vedere questo confronto tra [prezzo e funzionalità](https://azure.microsoft.com/pricing/details/key-vault/).



