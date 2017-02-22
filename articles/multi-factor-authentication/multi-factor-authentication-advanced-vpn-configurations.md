---
title: Scenari avanzati con Azure MFA e VPN di terze parti
description: Guide di configurazione dettagliate per l&quot;integrazione di Azure MFA con Cisco, Citrix e Juniper.
services: multi-factor-authentication
documentationcenter: 
author: kgremban
manager: femila
editor: yossib
ms.assetid: 1f94a214-d6f6-48a8-8a12-006b5896ae45
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/29/2016
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 6c8420a154d998aa95c0220049ee54b3039a872b
ms.openlocfilehash: 1724d9d482597cd8491ad1ea86f48beefbfa1531

---

# <a name="advanced-scenarios-with-azure-multi-factor-authentication-and-third-party-vpn-solutions"></a>Scenari avanzati con Azure Multi-Factor Authentication e soluzioni VPN di terze parti
È possibile usare Azure Multi-Factor Authentication per connettersi rapidamente a varie soluzioni VPN di terze parti. Questo articolo illustra in particolare i dispositivi VPN Cisco® ASA, Citrix NetScaler SSL e Juniper Networks Secure Access/Pulse Secure Connect Secure SSL. Sono state create guide di configurazione per questi tre dispositivi comuni, ma è possibile integrare il server Multi-Factor Authentication con la maggior parte dei sistemi che usano RADIUS, LDAP, IIS o l'autenticazione basata sulle attestazioni in AD FS. Per informazioni più dettagliate, vedere la sezione relativa alle [configurazioni del server Multi-Factor Authentication](multi-factor-authentication-get-started-server.md#next-steps).

## <a name="cisco-asa-vpn-appliance-and-azure-multi-factor-authentication"></a>Dispositivo VPN Cisco ASA e Azure Multi-Factor Authentication
Azure Multi-Factor Authentication si integra perfettamente con i dispositivi VPN Cisco® ASA per fornire protezione aggiuntiva per gli account di accesso al client VPN Cisco AnyConnect® e l'accesso al portale.  Questa operazione può essere eseguita usando il protocollo LDAP o RADIUS.  Selezionare una delle opzioni seguenti per scaricare le guide dettagliate per la configurazione.

| Guida alla configurazione | Descrizione |
| --- | --- |
| [Configurazione del dispositivo VPN Cisco ASA con Anyconnect e Azure MFA per LDAP](http://download.microsoft.com/download/A/2/0/A201567C-C3DE-4227-AF89-4567A470899E/Cisco_ASA_Azure_MFA_LDAP.docx) |Illustra le modalità di integrazione del dispositivo VPN Cisco ASA con Azure MFA tramite LDAP |
| [Configurazione del dispositivo VPN Cisco ASA con Anyconnect e Azure MFA per RADIUS](http://download.microsoft.com/download/4/5/7/4579C1CF-35B0-4FBE-8A1A-B49CB2CC0382/Cisco_ASA_Azure_MFA_RADIUS.docx) |Illustra le modalità di integrazione del dispositivo VPN Cisco ASA con Azure MFA tramite RADIUS |

## <a name="citrix-netscaler-ssl-vpn-and-azure-multi-factor-authentication"></a>Dispositivo VPN Citrix NetScaler SSL e Azure Multi-Factor Authentication
Azure Multi-Factor Authentication si integra perfettamente con i dispositivi VPN SSL Citrix NetScaler per fornire protezione aggiuntiva per gli account di accesso al client SSL Citrix NetScaler e l'accesso al portale.  Questa operazione può essere eseguita usando il protocollo LDAP o RADIUS.  Selezionare una delle opzioni seguenti per scaricare le guide dettagliate per la configurazione.

| Guida alla configurazione | Descrizione |
| --- | --- |
| [Configurazione del dispositivo VPN SSL Citrix NetScaler e Azure MFA per LDAP](http://download.microsoft.com/download/2/4/E/24E1E722-72DF-471F-A88A-D1338DB1AF83/Citrix_NS_Azure_MFA_LDAP.docx) |Illustra le modalità di integrazione del dispositivo VPN SSL Citrix NetScaler con Azure MFA tramite LDAP |
| [Configurazione del dispositivo VPN SSL Citrix NetScaler e Azure MFA per RADIUS](http://download.microsoft.com/download/1/A/4/1A482764-4A63-45C2-A5EC-2B673ACCDD12/Citrix_NS_Azure_MFA_RADIUS.docx) |Illustra le modalità di integrazione del dispositivo VPN SSL Citrix NetScaler con Azure MFA tramite RADIUS |

## <a name="juniperpulse-secure-ssl-vpn-appliance-and-azure-multi-factor-authentication"></a>Client VPN SSL Juniper/Pulse Secure e Azure Multi-Factor Authentication
Azure Multi-Factor Authentication si integra perfettamente con i client VPN SSL Juniper/Pulse Secure per fornire protezione aggiuntiva per gli account di accesso al client Juniper/Pulse Secure e l'accesso al portale.  Questa operazione può essere eseguita usando il protocollo LDAP o RADIUS.  Selezionare una delle opzioni seguenti per scaricare le guide dettagliate per la configurazione.

| Guida alla configurazione | Descrizione |
| --- | --- |
| [Configurazione del client VPN SSA Juniper/Pulse Secure e Azure MFA per LDAP](http://download.microsoft.com/download/6/5/8/6587B418-75B1-4FCB-84D4-984BC479309E/JuniperPulse_Azure_MFA_LDAP.docx) |Illustra le modalità di integrazione del client VPN SSL Juniper/Pulse Secure con Azure MFA tramite LDAP |
| [Configurazione del client VPN SSA Juniper/Pulse Secure e Azure MFA per RADIUS](http://download.microsoft.com/download/7/9/A/79AB3DAD-4799-4379-B1DA-B95ABDF231DC/JuniperPulse_Azure_MFA_RADIUS.docx) |Illustra le modalità di integrazione del client VPN SSL Juniper/Pulse Secure con Azure MFA tramite RADIUS |



<!--HONumber=Jan17_HO1-->


