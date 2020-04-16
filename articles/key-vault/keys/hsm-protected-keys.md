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
ms.openlocfilehash: 58cf3358a9e908070ce9003d05dd0b576b1d2d3f
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81429694"
---
# <a name="import-hsm-protected-keys-to-key-vault"></a>Importare chiavi protette da HSM nell'insieme di credenziali delle chiaviImport HSM-protected keys to Key Vault

Per una maggiore sicurezza, quando si usa l'insieme di credenziali delle chiavi di Azure è possibile importare o generare le chiavi in moduli di protezione hardware (HSM) che rimangono sempre entro il limite HSM. Questo scenario viene spesso definito con il termine modalità *Bring Your Own Key*o BYOK. L'insieme di credenziali delle chiavi di Azure usa nCipher nShield della famiglia di HSM (convalidato FIPS 140-2 Livello 2) per proteggere le chiavi.

Questa funzionalità non è disponibile per Azure China 21Vianet.This functionality is not available for Azure China 21Vianet.

> [!NOTE]
> Per altre informazioni su Archiviazione delle chiavi di Azure, vedere [Che cos'è l'insieme di](../general/overview.md) credenziali delle chiavi di Azure.For more information about Azure Key Vault, see What is Azure Key Vault?  
> Per un'esercitazione introduttiva che illustra la creazione di un insieme di credenziali delle chiavi per chiavi con protezione HSM, vedere [Che cos'è Azure Key Vault?](../general/overview.md).

## <a name="supported-hsms"></a>HSM supportati

Il trasferimento di chiavi protette da HSM all'insieme di credenziali delle chiavi è supportato tramite due metodi diversi a seconda degli HSM in uso. Use the table below to determine which method should be used for your HSMs to generate, and then transfer your own HSM-protected keys to use with Azure Key Vault. 

|Nome produttore|Tipo di fornitore|Modelli HSM supportati|Metodo di trasferimento del tasto HSM supportato|
|---|---|---|---|
|nCifra|Produttore|<ul><li>nShield famiglia di HSMs</li></ul>|[Utilizzare il metodo BYOK legacy](hsm-protected-keys-legacy.md)|
|Thales|Produttore|<ul><li>Famiglia SafeNet Luna HSM 7 con firmware versione 7.3 o più recente</li></ul>| [Usa nuovo metodo BYOK (anteprima)](hsm-protected-keys-vendor-agnostic-byok.md)|
|Fortanix|HSM come servizio|<ul><li>Servizio di gestione delle chiavi (SDKMS) con gestione automatica</li></ul>|[Usa nuovo metodo BYOK (anteprima)](hsm-protected-keys-vendor-agnostic-byok.md)|


## <a name="next-steps"></a>Passaggi successivi

Seguire le procedure consigliate di [Key Vault](../general/best-practices.md) per garantire la sicurezza, la durata e il monitoraggio delle chiavi.
