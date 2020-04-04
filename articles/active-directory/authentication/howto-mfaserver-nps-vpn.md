---
title: Azure MFA Server and third-party VPNs - Azure Active Directory
description: Guide dettagliate alla configurazione di Azure MFA Server per l'integrazione con Cisco, Citrix e Juniper.Step-by-step configuration guides for Azure MFA Server to integrate with Cisco, Citrix, and Juniper.
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
ms.openlocfilehash: 01decb99a9eb24ae60250f83f1f961b4c1690bc0
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/03/2020
ms.locfileid: "80652858"
---
# <a name="advanced-scenarios-with-azure-mfa-server-and-third-party-vpn-solutions"></a>Scenari avanzati con Azure MFA Server e soluzioni VPN di terze parti

Azure Multi-Factor Authentication Server (Azure MFA Server) può essere usato per connettersi senza problemi a varie soluzioni VPN di terze parti. Questo articolo è incentrato sull'appliance Cisco&reg; ASA VPN, sull'appliance VPN SSL Citrix NetScaler e sull'appliance SSL Secure SSL Juniper Networks. Sono disponibili guide alla configurazione utili per questi tre dispositivi comuni. Azure MFA Server può anche integrarsi con la maggior parte degli altri sistemi che usano RADIUS, LDAP, IIS o l'autenticazione basata sulle attestazioni in ADFS. Per altre informazioni, vedere Configurazioni di Server Azure MFA .You can find more details in [Azure MFA Server configurations](howto-mfaserver-deploy.md#next-steps).

> [!IMPORTANT]
> A partire dal 1 luglio 2019, Microsoft non offrirà più MFA Server per le nuove distribuzioni. I nuovi clienti che desiderano richiedere l'autenticazione a più fattori dai propri utenti devono usare L'autenticazione a più fattori di Azure basata su cloud. I clienti esistenti che hanno attivato MFA Server prima del 1 luglio potranno scaricare la versione più recente, gli aggiornamenti futuri e generare le credenziali di attivazione come di consueto.

## <a name="cisco-asa-vpn-appliance-and-azure-mfa-server"></a>Appliance VPN Cisco ASA e Azure MFA Server
Azure MFA Server si&reg; integra con l'appliance VPN Cisco&reg; ASA per fornire ulteriore sicurezza per gli accessi VPN Cisco AnyConnect e l'accesso al portale.  È possibile usare il protocollo LDAP o RADIUS.  Selezionare una delle opzioni seguenti per scaricare le guide dettagliate per la configurazione.

| Guida alla configurazione | Descrizione |
| --- | --- |
| [Configurazione del dispositivo VPN Cisco ASA con Anyconnect e Azure MFA per LDAP](https://download.microsoft.com/download/A/2/0/A201567C-C3DE-4227-AF89-4567A470899E/Cisco_ASA_Azure_MFA_LDAP.docx) | Integrare il dispositivo VPN Cisco ASA con Azure MFA tramite LDAP |
| [Configurazione del dispositivo VPN Cisco ASA con Anyconnect e Azure MFA per RADIUS](https://download.microsoft.com/download/4/5/7/4579C1CF-35B0-4FBE-8A1A-B49CB2CC0382/Cisco_ASA_Azure_MFA_RADIUS.docx) | Integrare il dispositivo VPN Cisco ASA con Azure MFA tramite RADIUS |

## <a name="citrix-netscaler-ssl-vpn-and-azure-mfa-server"></a>VPN SSL Citrix e Azure MFA Server
Azure MFA Server si integra con l'appliance VPN SSL Citrix NetScaler per fornire ulteriore sicurezza per gli accessi VPN SSL Citrix NetScaler e l'accesso al portale.  È possibile usare il protocollo LDAP o RADIUS.  Selezionare una delle opzioni seguenti per scaricare le guide dettagliate per la configurazione.

| Guida alla configurazione | Descrizione |
| --- | --- |
| [Configurazione del dispositivo VPN SSL Citrix NetScaler e Azure MFA per LDAP](https://download.microsoft.com/download/2/4/E/24E1E722-72DF-471F-A88A-D1338DB1AF83/Citrix_NS_Azure_MFA_LDAP.docx) | Integrare il dispositivo VPN SSL Citrix NetScaler con Azure MFA tramite LDAP |
| [Configurazione del dispositivo VPN SSL Citrix NetScaler e Azure MFA per RADIUS](https://download.microsoft.com/download/1/A/4/1A482764-4A63-45C2-A5EC-2B673ACCDD12/Citrix_NS_Azure_MFA_RADIUS.docx) | Integrare il dispositivo VPN SSL Citrix NetScaler con Azure MFA tramite RADIUS |

## <a name="juniperpulse-secure-ssl-vpn-appliance-and-azure-mfa-server"></a>Appliance VPN SSL sicura Juniper/Pulse e server Azure MFA
Azure MFA Server si integra con l'appliance VPN Juniper/Pulse Secure SSL per fornire ulteriore sicurezza per gli accessi VPN SSL Juniper/Pulse Secure e l'accesso al portale.  È possibile usare il protocollo LDAP o RADIUS.  Selezionare una delle opzioni seguenti per scaricare le guide dettagliate per la configurazione.

| Guida alla configurazione | Descrizione |
| --- | --- |
| [Configurazione del client VPN SSA Juniper/Pulse Secure e Azure MFA per LDAP](https://download.microsoft.com/download/6/5/8/6587B418-75B1-4FCB-84D4-984BC479309E/JuniperPulse_Azure_MFA_LDAP.docx) | Integrare il client VPN SSL Juniper/Pulse Secure con Azure MFA tramite LDAP |
| [Configurazione del client VPN SSA Juniper/Pulse Secure e Azure MFA per RADIUS](https://download.microsoft.com/download/7/9/A/79AB3DAD-4799-4379-B1DA-B95ABDF231DC/JuniperPulse_Azure_MFA_RADIUS.docx) | Integrare il client VPN SSL Juniper/Pulse Secure con Azure MFA tramite RADIUS |

## <a name="next-steps"></a>Passaggi successivi

- [Ampliare l'infrastruttura di autenticazione esistente con l'estensione di Server dei criteri di rete per Multi-Factor Authentication di Azure](howto-mfa-nps-extension.md)

- [Configurare le impostazioni di Azure Multi-Factor AuthenticationConfigure Azure Multi-Factor Authentication settings](howto-mfa-mfasettings.md)
