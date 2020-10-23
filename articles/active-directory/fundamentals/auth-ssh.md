---
title: Autenticazione SSH con Azure Active Directory
description: Indicazioni sull'architettura per ottenere l'integrazione SSH con Azure Active Directory
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 10/19/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: eb766150339820f9356fe94311cd1ff33dda5480
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/23/2020
ms.locfileid: "92462914"
---
# <a name="ssh"></a>SSH  

Secure Shell (SSH) è un protocollo di rete che fornisce la crittografia per i servizi di rete operativi in modo sicuro su una rete non protetta. SSH fornisce anche l'accesso da riga di comando, esegue i comandi remoti e trasferisce i file in modo sicuro. Viene comunemente usato nei sistemi basati su UNIX come Linux®. SSH sostituisce il protocollo Telnet, che non fornisce la crittografia in una rete non protetta. 

Azure Active Directory (Azure AD) fornisce un'estensione di macchina virtuale (VM) per sistemi basati su® Linux in esecuzione in Azure. 

## <a name="use-when"></a>Casi di utilizzo 

* Uso di macchine virtuali Linux basate su® che richiedono l'accesso remoto

* Esecuzione di comandi remoti in sistemi Linux basati su®

* Trasferire in modo sicuro i file in una rete non protetta

![diagramma di Azure AD con il protocollo SSH](./media/authentication-patterns/ssh-auth.png)

SSH con Azure AD

## <a name="components-of-system"></a>Componenti del sistema 

* **Utente**: avvia il client SSH per configurare una connessione con le vm Linux® e fornisce le credenziali per l'autenticazione.

* **Web browser**: il componente con cui l'utente interagisce. Comunica con il provider di identità (Azure AD) per autenticare e autorizzare l'utente in modo sicuro.

* **Client SSH**: Guida il processo di configurazione della connessione.

* **Azure ad**: autentica l'identità dell'utente usando il flusso del dispositivo e rilascia il token alle VM Linux.

* **VM Linux**: accetta il token e fornisce una connessione corretta.

## <a name="implement-ssh-with-azure-ad"></a>Implementare SSH con Azure AD 

* [Accedere a una VM Linux® con Azure Active Directory credenziali-macchine virtuali di Azure ](https://docs.microsoft.com/azure/virtual-machines/linux/login-using-aad) 

* [Flusso del codice del dispositivo OAuth 2,0-piattaforma di identità Microsoft ](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-device-code)

* [Integrazione con Azure Active Directory (akamai.com)](https://learn.akamai.com/webhelp/enterprise-application-access/enterprise-application-access/GUID-6B16172C-86CC-48E8-B30D-8E678BF3325F.html)

 
