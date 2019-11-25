---
title: Framework di sicurezza - Azure IoT Edge | Microsoft Docs
description: Informazioni sugli standard di sicurezza, autenticazione e autorizzazione che sono stati usati per sviluppare Azure IoT Edge e che devono essere tenuti in considerazione durante la progettazione della soluzione
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 08/30/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 0d315c7955fff854fc52f73de16eda71b9296b40
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/24/2019
ms.locfileid: "74452441"
---
# <a name="security-standards-for-azure-iot-edge"></a>Standard di sicurezza per Azure IoT Edge

Azure IoT Edge addresses the risks that are inherent when moving your data and analytics to the intelligent edge. The IoT Edge security standards balance flexibility for different deployment scenarios with the protection that you expect from all Azure services. 

IoT Edge runs on various makes and models of hardware, supports several operating systems, and applies to diverse deployment scenarios. The risk of a deployment scenario depends on factors that include solution ownership, deployment geography, data sensitivity, privacy, application vertical, and regulatory requirements. Rather than offering concrete solutions for specific scenarios, IoT Edge is an extensible security framework based on well-grounded principles that are designed for scale. 
 
Questo articolo offre una panoramica sul framework di sicurezza di IoT Edge. For more information, see [Securing the intelligent edge](https://azure.microsoft.com/blog/securing-the-intelligent-edge/).

## <a name="standards"></a>Standard

Gli standard promuovono un controllo e un'implementazione semplice, che sono alla base della sicurezza. A security solution should lend itself to scrutiny under evaluation to build trust and shouldn't be a hurdle to deployment. The design of the framework to secure Azure IoT Edge is based on time-tested and industry proven security protocols for familiarity and reuse. 

## <a name="authentication"></a>Authentication

When you deploy an IoT solution, you need to know that only trusted actors, devices, and modules have access to your solution. Certificate-based authentication is the primary mechanism for authentication for the Azure IoT Edge platform. This mechanism is derived from a set of standards governing Public Key Infrastructure (PKiX) by the Internet Engineering Task Force (IETF).     

Tutti i dispositivi, i moduli e gli attori che interagiscono con il dispositivo Azure IoT Edge, fisicamente o tramite una connessione di rete, devono avere un'identità certificato univoca. Not every scenario or component may lend itself to certificate-based authentication, so the extensibility of the security framework offers secure alternatives. 

For more information, see [Azure IoT Edge certificate usage](iot-edge-certs.md).

## <a name="authorization"></a>Authorization

Secondo il principio dei privilegi minimi, gli utenti e i componenti di un sistema devono avere accesso solo al set minimo di risorse e dati necessari per svolgere i rispettivi ruoli. I dispositivi, i moduli e gli attori devono ottenere l'accesso solo alle risorse e ai dati nel proprio ambito di autorizzazione e solo quando è consentito a livello di architettura. Some permissions are configurable with sufficient privileges and others are architecturally enforced.  For example, some modules may be authorized to connect to Azure IoT Hub. However, there is no reason why a module in one IoT Edge device should access the twin of a module in another IoT Edge device.

Other authorization schemes include certificate signing rights and role-based access control (RBAC). 

## <a name="attestation"></a>Attestazione

Attestation ensures the integrity of software bits, which is important for detecting and preventing malware.  The Azure IoT Edge security framework classifies attestation under three main categories:

* Attestazione statica
* Attestazione di runtime
* Attestazione di software

### <a name="static-attestation"></a>Attestazione statica

Static attestation verifies the integrity of all software on a device during power-up, including the operating system, all runtimes, and configuration information. Because static attestation occurs during power-up, it's often referred to as secure boot. The security framework for IoT Edge devices extends to manufacturers and incorporates secure hardware capabilities that assure static attestation processes. These processes include secure boot and secure firmware upgrade.  Working in close collaboration with silicon vendors eliminates superfluous firmware layers, so minimizes the threat surface. 

### <a name="runtime-attestation"></a>Attestazione di runtime

Once a system has completed a secure boot process, well-designed systems should detect attempts to inject malware and take proper countermeasures. Malware attacks may target the system's ports and interfaces. If malicious actors have physical access to a device, they may tamper with the device itself or use side-channel attacks to gain access. Such malcontent, whether malware or unauthorized configuration changes, can't be detected by static attestation because it is injected after the boot process. Le contromisure offerte o applicate dall'hardware del dispositivo aiutano ad allontanare tali minacce.  The security framework for IoT Edge explicitly calls for extensions that combat runtime threats.  

### <a name="software-attestation"></a>Attestazione di software

All healthy systems, including intelligent edge systems, need patches and upgrades.  Security is important for update processes, otherwise they can be potential threat vectors.  The security framework for IoT Edge calls for updates through measured and signed packages to assure the integrity of and authenticate the source of the packages.  This standard applies to all operating systems and application software bits. 

## <a name="hardware-root-of-trust"></a>Radice di attendibilità dell'hardware

For many intelligent edge devices, especially devices that can be physically accessed by potential malicious actors, hardware security is the last defense for protection. Tamper resistant hardware is crucial for such deployments. Azure IoT Edge encourages secure silicon hardware vendors to offer different flavors of hardware root of trust to accommodate various risk profiles and deployment scenarios. L'attendibilità hardware può anche provenire da standard comuni del protocollo di sicurezza come Trusted Platform Module (ISO/IEC 11889) e Device Identifier Composition Engine di Trusted Computing Group. Secure enclave technologies like TrustZones and Software Guard Extensions (SGX) also provide hardware trust. 

## <a name="certification"></a>Certificazione

To help customers make informed decisions when procuring Azure IoT Edge devices for their deployment, the IoT Edge framework includes certification requirements.  Foundational to these requirements are certifications pertaining to security claims and certifications pertaining to validation of the security implementation.  For example, a security claim certification means that the IoT Edge device uses secure hardware known to resist boot attacks. A validation certification means that the secure hardware was properly implemented to offer this value in the device.  In keeping with the principle of simplicity, the framework tries to keep the burden of certification minimal.   

## <a name="extensibility"></a>Estendibilità

With IoT technology driving different types of business transformations, security should evolve in parallel to address emerging scenarios.  The Azure IoT Edge security framework starts with a solid foundation on which it builds in extensibility into different dimensions to include: 

* Servizi di sicurezza del produttore ad esemio il Servizio Device Provisioning per l'hub IoT di Azure.
* Third-party services like managed security services for different application verticals (like industrial or healthcare) or technology focus (like security monitoring in mesh networks, or silicon hardware attestation services) through a rich network of partners.
* I sistemi legacy includono l'adattamento alle strategie di sicurezza alternative, ad esempio con la tecnologia di protezione diversa dai certificati per l'autenticazione e la gestione delle identità.
* Proteggere l'hardware per l'adozione di nuove tecnologie di protezione dell'hardware e contributi di partner produttori di processori.

In the end, securing the intelligent edge requires collaborative contributions from an open community driven by the common interest in securing IoT.  These contributions might be in the form of secure technologies or services.  The Azure IoT Edge security framework offers a solid foundation for security that is extensible for the maximum coverage to offer the same level of trust and integrity in the intelligent edge as with Azure cloud.  

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulla sicurezza offerta da Azure IoT Edge, vedere [Securing the intelligent edge](https://azure.microsoft.com/blog/securing-the-intelligent-edge/) (Protezione dei dispositivi perimetrali intelligenti).
