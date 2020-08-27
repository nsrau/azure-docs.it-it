---
title: Server di autenticazione a più fattori di Azure e VPN di terze parti-Azure Active Directory
description: Guide dettagliate per la configurazione del server di autenticazione a più fattori di Azure per l'integrazione con Cisco, Citrix e Juniper.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 116446c2ca6512ac39b907e95a2c1ccdf70befda
ms.sourcegitcommit: e69bb334ea7e81d49530ebd6c2d3a3a8fa9775c9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/27/2020
ms.locfileid: "88949287"
---
# <a name="advanced-scenarios-with-azure-mfa-server-and-third-party-vpn-solutions"></a>Scenari avanzati con server di autenticazione a più fattori di Azure e soluzioni VPN di terze parti

Azure server Multi-Factor Authentication (server di autenticazione a più fattori di Azure) può essere usato per connettersi facilmente a diverse soluzioni VPN di terze parti. Questo articolo è incentrato sul dispositivo &reg; VPN Cisco ASA, sull'appliance VPN SSL Citrix NetScaler e sul dispositivo VPN Secure Access/Pulse Secure Connect Secure. Sono disponibili guide alla configurazione utili per questi tre dispositivi comuni. Il server di autenticazione a più fattori di Azure può anche essere integrato con la maggior parte dei sistemi che usano RADIUS, LDAP, IIS o l'autenticazione basata sulle attestazioni per AD FS. Altre informazioni sono disponibili in [configurazioni del server](howto-mfaserver-deploy.md#next-steps)di autenticazione a più fattori di Azure.

> [!IMPORTANT]
> A partire dal 1 ° luglio 2019, Microsoft non offre più il server multi-factor authentication per le nuove distribuzioni. I nuovi clienti che vogliono richiedere l'autenticazione a più fattori durante gli eventi di accesso devono usare Azure Multi-Factor Authentication basato sul cloud.
>
> Per iniziare a usare l'autenticazione a più fattori basata sul cloud, vedere [esercitazione: proteggere gli eventi di accesso utente con multi-factor authentication di Azure](tutorial-enable-azure-mfa.md).
>
> Se si usa l'autenticazione a più fattori basata sul cloud, vedere [integrare l'infrastruttura VPN con l'autenticazione a più fattori di Azure](howto-mfa-nps-extension-vpn.md).
>
> I clienti esistenti che hanno attivato il server multi-factor authentication prima del 1 ° luglio 2019 possono scaricare la versione più recente, gli aggiornamenti futuri e generare le credenziali di attivazione come di consueto.

## <a name="cisco-asa-vpn-appliance-and-azure-mfa-server"></a>Appliance VPN Cisco ASA e server di autenticazione a più fattori di Azure
Il server di autenticazione a più fattori di Azure si integra con l' &reg; appliance VPN Cisco ASA per fornire sicurezza aggiuntiva per gli &reg; accessi VPN Cisco AnyConnect e per l'accesso al portale.  È possibile usare il protocollo LDAP o RADIUS.  Selezionare una delle opzioni seguenti per scaricare le guide dettagliate per la configurazione.

| Guida alla configurazione | Descrizione |
| --- | --- |
| [Configurazione del dispositivo VPN Cisco ASA con Anyconnect e Azure MFA per LDAP](https://download.microsoft.com/download/A/2/0/A201567C-C3DE-4227-AF89-4567A470899E/Cisco_ASA_Azure_MFA_LDAP.docx) | Integrare il dispositivo VPN Cisco ASA con Azure MFA tramite LDAP |
| [Configurazione del dispositivo VPN Cisco ASA con Anyconnect e Azure MFA per RADIUS](https://download.microsoft.com/download/4/5/7/4579C1CF-35B0-4FBE-8A1A-B49CB2CC0382/Cisco_ASA_Azure_MFA_RADIUS.docx) | Integrare il dispositivo VPN Cisco ASA con Azure MFA tramite RADIUS |

## <a name="citrix-netscaler-ssl-vpn-and-azure-mfa-server"></a>VPN SSL Citrix NetScaler e server di autenticazione a più fattori di Azure
Il server di autenticazione a più fattori di Azure si integra con il dispositivo VPN SSL Citrix NetScaler per fornire sicurezza aggiuntiva per gli accessi VPN SSL Citrix NetScaler e l'accesso al portale.  È possibile usare il protocollo LDAP o RADIUS.  Selezionare una delle opzioni seguenti per scaricare le guide dettagliate per la configurazione.

| Guida alla configurazione | Descrizione |
| --- | --- |
| [Configurazione del dispositivo VPN SSL Citrix NetScaler e Azure MFA per LDAP](https://download.microsoft.com/download/2/4/E/24E1E722-72DF-471F-A88A-D1338DB1AF83/Citrix_NS_Azure_MFA_LDAP.docx) | Integrare il dispositivo VPN SSL Citrix NetScaler con Azure MFA tramite LDAP |
| [Configurazione del dispositivo VPN SSL Citrix NetScaler e Azure MFA per RADIUS](https://download.microsoft.com/download/1/A/4/1A482764-4A63-45C2-A5EC-2B673ACCDD12/Citrix_NS_Azure_MFA_RADIUS.docx) | Integrare il dispositivo VPN SSL Citrix NetScaler con Azure MFA tramite RADIUS |

## <a name="juniperpulse-secure-ssl-vpn-appliance-and-azure-mfa-server"></a>Appliance VPN SSL Juniper/Pulse Secure e server di autenticazione a più fattori di Azure
Il server di autenticazione a più fattori di Azure si integra con il dispositivo VPN SSL Juniper/Pulse Secure per fornire sicurezza aggiuntiva per gli accessi VPN SSL/Pulse Secure e l'accesso al portale.  È possibile usare il protocollo LDAP o RADIUS.  Selezionare una delle opzioni seguenti per scaricare le guide dettagliate per la configurazione.

| Guida alla configurazione | Descrizione |
| --- | --- |
| [Configurazione del client VPN SSA Juniper/Pulse Secure e Azure MFA per LDAP](https://download.microsoft.com/download/6/5/8/6587B418-75B1-4FCB-84D4-984BC479309E/JuniperPulse_Azure_MFA_LDAP.docx) | Integrare il client VPN SSL Juniper/Pulse Secure con Azure MFA tramite LDAP |
| [Configurazione del client VPN SSA Juniper/Pulse Secure e Azure MFA per RADIUS](https://download.microsoft.com/download/7/9/A/79AB3DAD-4799-4379-B1DA-B95ABDF231DC/JuniperPulse_Azure_MFA_RADIUS.docx) | Integrare il client VPN SSL Juniper/Pulse Secure con Azure MFA tramite RADIUS |

## <a name="next-steps"></a>Passaggi successivi

- [Ampliare l'infrastruttura di autenticazione esistente con l'estensione di Server dei criteri di rete per Multi-Factor Authentication di Azure](howto-mfa-nps-extension.md)

- [Configurare le impostazioni di Azure Multi-Factor Authentication](howto-mfa-mfasettings.md)
