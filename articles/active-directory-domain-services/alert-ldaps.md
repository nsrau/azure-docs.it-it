---
title: 'Azure Active Directory Domain Services: Risolvere i problemi relativi a LDAP sicuro | Microsoft Docs'
description: Risoluzione dei problemi relativi a LDAP sicuro di Azure AD Domain Services
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: ''
editor: ''
ms.assetid: 81208c0b-8d41-4f65-be15-42119b1b5957
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/22/2019
ms.author: iainfou
ms.openlocfilehash: 8f9f4a8b52548dad011f5e825fa42c50da970ea7
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/19/2019
ms.locfileid: "69613149"
---
# <a name="azure-ad-domain-services---troubleshooting-secure-ldap-configuration"></a>Azure AD Domain Services - Risoluzione dei problemi relativi alla configurazione di LDAP sicuro

Questo articolo fornisce soluzioni per i problemi comuni che si verificano durante la [configurazione di LDAP sicuro](tutorial-configure-ldaps.md) di Azure AD Domain Services.

## <a name="aadds101-secure-ldap-network-security-group-configuration"></a>AADDS101: Configurazione del gruppo di sicurezza di rete LDAP sicuro

**Messaggio di avviso:**

*LDAP sicuro su Internet è abilitato per il dominio gestito. L'accesso alla porta 636 non è tuttavia bloccato tramite un gruppo di sicurezza di rete. È quindi possibile che gli account utente sul dominio gestito siano esposti ad attacchi di forza bruta alle password.*

### <a name="secure-ldap-port"></a>Porta LDAP sicuro

Quando è abilitato LDAP sicuro, è consigliabile creare regole aggiuntive per consentire l'accesso LDAPS in ingresso solo da determinati indirizzi IP. Queste regole proteggono il dominio da attacchi di forza bruta che possono comportare rischi per la sicurezza. La porta 636 consente l'accesso al dominio gestito. Seguire questa procedura per aggiornare il gruppo di sicurezza di rete in modo da consentire l'accesso per LDAP sicuro:

1. Passare alla [scheda Gruppi di sicurezza di rete](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.Network%2FNetworkSecurityGroups) nel portale di Azure.
2. Scegliere dalla tabella il gruppo di sicurezza di rete associato al dominio.
3. Fare clic su **Regole di sicurezza in ingresso**.
4. Creare la regola per la porta 636:
   1. Fare clic su **Aggiungi** sulla barra di spostamento in alto.
   2. Scegliere **Indirizzi IP** per l'origine.
   3. Specificare gli intervalli di porte di origine per questa regola.
   4. Inserire "636" per gli intervalli di porte di destinazione.
   5. Il protocollo è **TCP**.
   6. Assegnare alla regola un nome, una descrizione e una priorità appropriati. La priorità di questa regola deve essere maggiore rispetto alla priorità della regola "Deny all", se disponibile.
   7. Fare clic su **OK**.
5. Verificare che la regola sia stata creata.
6. Controllare l'integrità del dominio dopo due ore per assicurarsi che la procedura sia stata eseguita correttamente.

> [!TIP]
> L'uso della porta 636 non è l'unica regola necessaria per la corretta esecuzione di Azure AD Domain Services. Per altre informazioni, vedere gli articoli [Considerazioni sulla rete](network-considerations.md) o [Risoluzione dei problemi relativi alla configurazione del gruppo di sicurezza di rete](alert-nsg.md).
>

## <a name="aadds502-secure-ldap-certificate-expiring"></a>AADDS502: Scadenza del certificato LDAP sicuro

**Messaggio di avviso:**

*Il certificato LDAP sicuro per il dominio gestito scadrà in [data]* .

**Risoluzione:**

Creare un nuovo certificato LDAP sicuro eseguendo la procedura descritta nell'articolo [Configurare l'accesso LDAP sicuro](tutorial-configure-ldaps.md).

## <a name="contact-us"></a>Contatti
Contattare il team di prodotto di Servizi di dominio Azure AD per [condividere commenti e suggerimenti o per chiedere supporto tecnico](contact-us.md).
