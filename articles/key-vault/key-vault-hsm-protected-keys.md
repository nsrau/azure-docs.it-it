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
ms.openlocfilehash: 00d2d38801929454110b41be88d133e3af232af7
ms.sourcegitcommit: b8f2fee3b93436c44f021dff7abe28921da72a6d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/18/2020
ms.locfileid: "77425749"
---
# <a name="import-hsm-protected-keys-to-key-vault"></a>Importare chiavi HSM protette per Key Vault

Per una maggiore sicurezza, quando si usa l'insieme di credenziali delle chiavi di Azure è possibile importare o generare le chiavi in moduli di protezione hardware (HSM) che rimangono sempre entro il limite HSM. Questo scenario viene spesso definito con il termine modalità *Bring Your Own Key*o BYOK. Azure Key Vault usa la famiglia nCipher nShield di HSM (FIPS 140-2 livello 2 convalidata) per proteggere le chiavi.

Questa funzionalità non è disponibile per Azure Cina 21Vianet.

> [!NOTE]
> Per altre informazioni sull'insieme di credenziali di Azure, vedere [Cos'è l'insieme di credenziali delle chiavi di Azure?](key-vault-overview.md)  
> Per un'esercitazione introduttiva che illustra la creazione di un insieme di credenziali delle chiavi per chiavi con protezione HSM, vedere [Che cos'è Azure Key Vault?](key-vault-overview.md).

## <a name="supported-hsms"></a>HSM supportati

Il trasferimento di chiavi protette da HSM a Key Vault è supportato tramite due metodi diversi a seconda del HSM usato. Usare la tabella seguente per determinare il metodo da usare per la generazione di HSM e quindi trasferire le proprie chiavi protette da HSM da usare con Azure Key Vault. 

|Nome del fornitore del modulo di protezione hardware|Modelli HSM supportati|Metodo di trasferimento della chiave HSM supportato|
|---|---|---|
|Thales|<ul><li>Famiglia SafeNet Luna HSM 7 con firmware versione 7,3 o successiva</li></ul>| [USA nuovo metodo BYOK (anteprima)](hsm-protected-keys-vendor-agnostic-byok.md)|
|nCipher|<ul><li>famiglia nShield di HSM</li></ul>|[USA metodo BYOK legacy](hsm-protected-keys-legacy.md)|


## <a name="next-steps"></a>Passaggi successivi

Seguire [Key Vault procedure consigliate](key-vault-best-practices.md) per garantire la sicurezza, la durabilità e il monitoraggio delle chiavi.
