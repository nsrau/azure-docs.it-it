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
ms.date: 02/17/2020
ms.author: ambapat
ms.openlocfilehash: 048e5072c592cf2de32e533014c99034572a1c47
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79082898"
---
# <a name="import-hsm-protected-keys-to-key-vault"></a>Importare chiavi con protezione HSM in Key Vault

Per una maggiore sicurezza, quando si usa l'insieme di credenziali delle chiavi di Azure è possibile importare o generare le chiavi in moduli di protezione hardware (HSM) che rimangono sempre entro il limite HSM. Questo scenario viene spesso definito con il termine modalità *Bring Your Own Key*o BYOK. Azure Key Vault usa la famiglia nCipher nShield di HSM (FIPS 140-2 livello 2 convalidata) per proteggere le chiavi.

Questa funzionalità non è disponibile per Azure Cina 21Vianet.

> [!NOTE]
> Per ulteriori informazioni su Azure Key Vault, vedere [che cos'è Azure Key Vault?](key-vault-overview.md)  
> Per un'esercitazione introduttiva che illustra la creazione di un insieme di credenziali delle chiavi per chiavi con protezione HSM, vedere [Che cos'è Azure Key Vault?](key-vault-overview.md).

## <a name="supported-hsms"></a>HSM supportati

Il trasferimento di chiavi protette da HSM a Key Vault è supportato tramite due metodi diversi a seconda del HSM usato. Usare la tabella seguente per determinare il metodo da usare per la generazione di HSM e quindi trasferire le proprie chiavi protette da HSM da usare con Azure Key Vault. 

|Nome produttore|Tipo fornitore|Modelli HSM supportati|Metodo di trasferimento della chiave HSM supportato|
|---|---|---|---|
|nCipher|Produttore|<ul><li>famiglia nShield di HSM</li></ul>|[USA metodo BYOK legacy](hsm-protected-keys-legacy.md)|
|Thales|Produttore|<ul><li>Famiglia SafeNet Luna HSM 7 con firmware versione 7,3 o successiva</li></ul>| [USA nuovo metodo BYOK (anteprima)](hsm-protected-keys-vendor-agnostic-byok.md)|
|Fortanix|Modulo di protezione hardware come servizio|<ul><li>Servizio di gestione delle chiavi a difesa automatica (SDKMS)</li></ul>|[USA nuovo metodo BYOK (anteprima)](hsm-protected-keys-vendor-agnostic-byok.md)|










## <a name="next-steps"></a>Passaggi successivi

Seguire [Key Vault procedure consigliate](key-vault-best-practices.md) per garantire la sicurezza, la durabilità e il monitoraggio delle chiavi.
