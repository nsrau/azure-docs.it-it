---
title: Come generare e trasferire chiavi con protezione HSM - Azure Key Vault
description: Questo argomento permette di pianificare, generare e quindi trasferire le proprie chiavi HSM protette da usare con l'insieme di credenziali delle chiavi di Azure. Anche noto come BYOK o Bring Your Own Key.
services: key-vault
author: amitbapat
manager: devtiw
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: keys
ms.topic: tutorial
ms.date: 05/29/2020
ms.author: ambapat
ms.openlocfilehash: 76b10dbd9b6d801d93cd5d9704531eb1a6de36a5
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/19/2020
ms.locfileid: "88585493"
---
# <a name="import-hsm-protected-keys-to-key-vault"></a>Importare chiavi con protezione HSM in Key Vault

Per una maggiore sicurezza, quando si usa l'insieme di credenziali delle chiavi di Azure è possibile importare o generare le chiavi in moduli di protezione hardware (HSM) che rimangono sempre entro il limite HSM. Questa modalità è spesso definita con il termine *Bring Your Own Key* o BYOK. Azure Key Vault usa i moduli di protezione hardware della famiglia di prodotti nCipher nShield (con convalida FIPS 140-2 Livello 2) per proteggere le chiavi.

Questa funzionalità non è disponibile per Azure Cina 21Vianet.

> [!NOTE]
> Per altre informazioni sull'insieme di credenziali di Azure, vedere [Cos'è l'insieme di credenziali delle chiavi di Azure?](../general/overview.md)  
> Per un'esercitazione introduttiva che illustra la creazione di un insieme di credenziali delle chiavi per chiavi con protezione HSM, vedere [Che cos'è Azure Key Vault?](../general/overview.md).

## <a name="supported-hsms"></a>Moduli di protezione hardware supportati

Il trasferimento delle chiavi con protezione HSM in Key Vault è supportato tramite due metodi diversi, in base al modulo di protezione hardware usato. Usare la tabella seguente per determinare il metodo da usare per i moduli di protezione hardware per generare e quindi trasferire le chiavi con protezione HSM personalizzate da usare con Azure Key Vault. 

|Nome produttore|Tipo di fornitore|Modelli di moduli di protezione hardware supportati|Metodo di trasferimento supportato per le chiavi HSM|
|---|---|---|---|
|[nCipher](https://www.ncipher.com/products/key-management/cloud-microsoft-azure)|Produttore,<br/>modulo di protezione hardware come servizio|<ul><li>Famiglia di moduli di protezione hardware nShield</li><li>nShield come servizio</ul>|**Metodo 1:** [nCipher BYOK](hsm-protected-keys-ncipher.md) (con attestazione avanzata per l'importazione di chiavi e la convalida dei moduli di protezione hardware)<br/>**Metodo 2:** [Usare il nuovo metodo BYOK](hsm-protected-keys-byok.md) |
|Thales|Produttore|<ul><li>Famiglia di prodotti Luna HSM 7 con firmware con versione 7.3 o successiva</li></ul>| [Usare il nuovo metodo BYOK](hsm-protected-keys-byok.md)|
|Fortanix|Produttore,<br/>modulo di protezione hardware come servizio|<ul><li>Self-Defending Key Management Service (SDKMS)</li><li>Equinix SmartKey</li></ul>|[Usare il nuovo metodo BYOK](hsm-protected-keys-byok.md)|
|Marvell|Produttore|Moduli di protezione hardware di All LiquidSecurity con<ul><li>Firmware con versione 2.0.4 o successiva</li><li>Firmware con versione 3.2 o successiva</li></ul>|[Usare il nuovo metodo BYOK](hsm-protected-keys-byok.md)|
|Cryptomathic|ISV (Enterprise Key Management System)|Più marchi e modelli di moduli di protezione hardware, tra cui<ul><li>nCipher</li><li>Thales</li><li>Utimaco</li></ul>Per dettagli, vedere il [sito di Cryptomathic](https://www.cryptomathic.com/azurebyok)|[Usare il nuovo metodo BYOK](hsm-protected-keys-byok.md)|


## <a name="next-steps"></a>Passaggi successivi

* Seguire le [Procedure consigliate di Key Vault](../general/best-practices.md) per garantire la sicurezza, la durabilità e il monitoraggio per le chiavi.
* Per una descrizione completa del nuovo metodo BYOK, vedere la [specifica di BYOK](https://docs.microsoft.com/azure/key-vault/keys/byok-specification)
