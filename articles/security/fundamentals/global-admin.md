---
title: Abilitare MFA per tutti gli amministratori di Azure
description: Linee guida per l'abilitare l'amministratore globale
ms.service: security
ms.subservice: security-fundamentals
author: barclayn
manager: barbkess
editor: TomSh
ms.topic: article
ms.date: 03/20/2018
ms.author: barclayn
ms.openlocfilehash: e30ce71a66dd5cb6c810111d359660d875ae97a8
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/09/2019
ms.locfileid: "68927909"
---
# <a name="enforce-multi-factor-authentication-mfa-for-subscription-administrators"></a>Applicare l'autenticazione a più fattori (MFA) per gli amministratori della sottoscrizione

Quando si creano gli amministratori, incluso l'account amministratore globale, è essenziale usare metodi di autenticazione avanzati.

È possibile eseguire le operazioni di amministrazione quotidiana assegnando ruoli di amministratore specifici, ad esempio amministratore di Exchange o amministratore password, ad account utente del personale IT in base alle esigenze.
Inoltre, l'abilitazione di [Azure Multi-Factor Authentication (MFA)](../../active-directory/authentication/multi-factor-authentication.md) per gli amministratori consente di aggiungere un secondo livello di sicurezza agli accessi e alle transazioni degli utenti. Grazie a Azure MFA, i reparti IT possono anche ridurre la probabilità di accesso ai dati dell'organizzazione usando credenziali compromesse.

Ad esempio:  Applicare l'autenticazione a più fattori di Azure per gli utenti e configurarla per l'uso di una telefonata o di un SMS come verifica. Se le credenziali dell'utente vengono compromesse, l'autore dell'attacco non sarà in grado di accedere alle risorse perché non avranno accesso al telefono dell'utente. Le organizzazioni che non aggiungono livelli supplementari di protezione delle identità sono più vulnerabili agli attacchi con furto di credenziali, con conseguente rischio di compromissione dei dati.

In alternativa, le organizzazioni che vogliono mantenere in locale tutto il controllo dell'autenticazione possono usare il [server Microsoft Azure Multi-Factor Authentication](../../active-directory/authentication/howto-mfaserver-deploy.md), detto anche "MFA in locale". Questo metodo consentirà comunque di applicare la Multi-Factor Authentication, mantenendo il server MFA in locale.

Per verificare chi sono coloro che nell'organizzazione dispongono di privilegi amministrativi, si può usare il seguente comando PowerShell di Microsoft Azure AD V2:

```azurepowershell-interactive
Get-AzureADDirectoryRole | Where { $_.DisplayName -eq "Company Administrator" } | Get-AzureADDirectoryRoleMember | Ft DisplayName
```

## <a name="enabling-mfa"></a>Abilitazione di MFA

Rivedere il funzionamento di [MFA](../../active-directory/authentication/howto-mfa-mfasettings.md) prima di procedere.

Se gli utenti hanno licenze che comprendono Azure Multi-Factor Authentication, non è necessario eseguire operazioni per attivare Azure MFA. È possibile iniziare a richiedere la verifica in due passaggi per i singoli utenti. Le licenze che abilitano Azure MFA sono le seguenti:

- Azure Multi-Factor Authentication
- Azure Active Directory Premium
- Enterprise Mobility + Security

## <a name="turn-on-two-step-verification-for-users"></a>Attivare la verifica in due passaggi per gli utenti

Usare una delle procedure elencate in [Come richiedere la verifica in due passaggi per un utente o un gruppo](../../active-directory/authentication/howto-mfa-userstates.md) per iniziare a usare Azure MFA. È possibile scegliere di applicare la verifica in due passaggi per tutti gli accessi oppure è possibile creare criteri di accesso condizionale per richiedere la verifica in due passaggi solo quando è importante.

