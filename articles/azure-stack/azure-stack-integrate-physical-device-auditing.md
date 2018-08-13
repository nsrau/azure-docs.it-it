---
title: Controllo del dispositivo fisico di Azure Stack
description: Descrive come integrare il controllo di accesso dispositivo fisico in Azure Stack
services: azure-stack
author: PatAltimore
manager: femila
ms.service: azure-stack
ms.topic: article
ms.date: 08/01/2018
ms.author: patricka
ms.reviewer: fiseraci
keywords: ''
ms.openlocfilehash: 459cdf4e1a70ee02d818dd6abe101e4fc3475b68
ms.sourcegitcommit: 387d7edd387a478db181ca639db8a8e43d0d75f7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/10/2018
ms.locfileid: "40034839"
---
# <a name="azure-stack-datacenter-integration---physical-device-auditing"></a>Integrazione di Data Center di Azure Stack - il controllo dispositivo fisico

Tutti i dispositivi fisici in Azure Stack, ad esempio i baseboard management controller (BMC) e le opzioni di rete, generano log di controllo e devono essere integrate in una soluzione di controllo globale. Poiché i dispositivi possono variare tra diversi fornitori di hardware Azure Stack OEM, contattare il fornitore per la documentazione sul controllo dell'integrazione. Le sezioni seguenti forniscono informazioni generali per il controllo dispositivo fisico in Azure Stack.  

## <a name="physical-device-access-auditing"></a>Controllo dell'accesso al dispositivo fisico

Tutti i dispositivi fisici in Azure Stack supportano l'uso di TACACS o RADIUS. Ciò include l'accesso a baseboard management controller (BMC) e commutatori di rete.

Soluzioni di Azure Stack non vengono forniti con RADIUS o TACACS incorporato. Tuttavia, le soluzioni sono state convalidate per supportare l'uso di RADIUS o TACACS soluzioni esistenti disponibili sul mercato.

MSCHAPv2 RADIUS, è stata convalidata. Rappresenta l'implementazione più sicura tramite RADIUS.
Rivolgersi al fornitore dell'hardware OEM per abilitare TACAS o RADIUS nei dispositivi inclusi con la soluzione di Azure Stack.

## <a name="syslog-forwarding-for-network-devices"></a>Inoltro Syslog per i dispositivi di rete

Tutti i dispositivi di rete fisici in Azure Stack supportano i messaggi syslog. Soluzioni di Azure Stack non viene fornito un server syslog. Tuttavia, i dispositivi sono stati convalidati per supportare l'invio di messaggi alle soluzioni esistenti di syslog disponibili sul mercato.

L'indirizzo di destinazione syslog è un parametro facoltativo raccolto per la distribuzione, ma può anche essere aggiunto dopo la distribuzione. Rivolgersi al fornitore dell'hardware OEM per configurare syslog sui dispositivi di rete di inoltro.

## <a name="next-steps"></a>Passaggi successivi

[Criteri di manutenzione](azure-stack-servicing-policy.md)
