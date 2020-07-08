---
title: Come generare e trasferire chiavi protette dal modulo di protezione hardware per Azure Key Vault - Azure Key Vault | Microsoft Docs
description: Questo argomento permette di pianificare, generare e quindi trasferire le proprie chiavi HSM protette da usare con l'insieme di credenziali delle chiavi di Azure. Anche noto come BYOK o Bring Your Own Key.
services: key-vault
author: amitbapat
manager: devtiw
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: keys
ms.topic: conceptual
ms.date: 05/29/2020
ms.author: ambapat
ms.openlocfilehash: 5433d9746cd64d0e942e056cfcd1940eba35c77d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84417923"
---
# <a name="import-hsm-protected-keys-to-key-vault"></a>Importare chiavi con protezione HSM in Key Vault

Per una maggiore sicurezza, quando si usa l'insieme di credenziali delle chiavi di Azure è possibile importare o generare le chiavi in moduli di protezione hardware (HSM) che rimangono sempre entro il limite HSM. Questo scenario viene spesso definito con il termine modalità *Bring Your Own Key*o BYOK. Azure Key Vault usa la famiglia nCipher nShield di HSM (FIPS 140-2 livello 2 convalidata) per proteggere le chiavi.

Questa funzionalità non è disponibile per Azure Cina 21Vianet.

> [!NOTE]
> Per altre informazioni sull'insieme di credenziali di Azure, vedere [Cos'è l'insieme di credenziali delle chiavi di Azure?](../general/overview.md)  
> Per un'esercitazione introduttiva che illustra la creazione di un insieme di credenziali delle chiavi per chiavi con protezione HSM, vedere [Che cos'è Azure Key Vault?](../general/overview.md).

## <a name="supported-hsms"></a>HSM supportati

Il trasferimento di chiavi protette da HSM a Key Vault è supportato tramite due metodi diversi a seconda del HSM usato. Usare la tabella seguente per determinare il metodo da usare per la generazione di HSM e quindi trasferire le proprie chiavi protette da HSM da usare con Azure Key Vault. 

|Nome produttore|Tipo fornitore|Modelli HSM supportati|Metodo di trasferimento della chiave HSM supportato|
|---|---|---|---|
|[nCipher](https://www.ncipher.com/products/key-management/cloud-microsoft-azure)|Produttore<br/>Modulo di protezione hardware come servizio|<ul><li>famiglia nShield di HSM</li><li>nShield come servizio</ul>|**Metodo 1:** [nCipher BYOK](hsm-protected-keys-ncipher.md) (con attestazione avanzata per l'importazione di chiavi e la convalida del modulo di protezione hardware)<br/>**Metodo 2:** [usare il nuovo metodo BYOK](hsm-protected-keys-byok.md) |
|Thales|Produttore|<ul><li>Famiglia lunare HSM 7 con firmware versione 7,3 o successiva</li></ul>| [USA nuovo metodo BYOK](hsm-protected-keys-byok.md)|
|Fortanix|Produttore<br/>Modulo di protezione hardware come servizio|<ul><li>Servizio di gestione delle chiavi a difesa automatica (SDKMS)</li><li>SmartKey Equinix</li></ul>|[USA nuovo metodo BYOK](hsm-protected-keys-byok.md)|
|Marvell|Produttore|Tutte le HSM LiquidSecurity con<ul><li>Versione firmware 2.0.4 o versioni successive</li><li>Firmware versione 3,2 o successiva</li></ul>|[USA nuovo metodo BYOK](hsm-protected-keys-byok.md)|
|Cryptomathic|ISV (Enterprise Key Management System)|Più marche e modelli HSM, tra cui<ul><li>nCipher</li><li>Thales</li><li>Utimaco</li></ul>[Per informazioni dettagliate](https://www.cryptomathic.com/azurebyok) , vedere il sito Cryptomathic|[USA nuovo metodo BYOK](hsm-protected-keys-byok.md)|


## <a name="next-steps"></a>Passaggi successivi

* Seguire [Key Vault procedure consigliate](../general/best-practices.md) per garantire la sicurezza, la durabilità e il monitoraggio delle chiavi.
* Per una descrizione completa del nuovo metodo BYOK, vedere la [specifica BYOK](https://docs.microsoft.com/azure/key-vault/keys/byok-specification)
