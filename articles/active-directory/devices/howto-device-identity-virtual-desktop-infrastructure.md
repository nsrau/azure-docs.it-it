---
title: Identità del dispositivo e virtualizzazione desktop - Azure Active DirectoryDevice identity and desktop virtualization - Azure Active Directory
description: Informazioni su come le identità dei dispositivi VDI e Azure AD possono essere usate insieme
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 10/15/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7b431cee3b8e5fc168dec2766442d6f6b9869d1e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74900364"
---
# <a name="device-identity-and-desktop-virtualization"></a>Identità del dispositivo e virtualizzazione desktop

Gli amministratori distribuiscono in genere piattaforme VDI (Virtual Desktop Infrastructure) che ospitano sistemi operativi Windows nelle proprie organizzazioni. Gli amministratori distribuiscono VDI a:

- Semplifica la gestione.
- Riduci i costi attraverso il consolidamento e la centralizzazione delle risorse.
- Offri agli utenti finali la mobilità e la libertà di accedere ai desktop virtuali in qualsiasi momento, da qualsiasi luogo e da qualsiasi dispositivo.

Esistono due tipi principali di desktop virtuali:

- Persistente
- Non persistente

Le versioni permanenti utilizzano un'immagine desktop univoca per ogni utente o un pool di utenti. Questi desktop unici possono essere personalizzati e salvati per un uso futuro. 

Le versioni non persistenti utilizzano una raccolta di desktop a cui gli utenti possono accedere in base alle esigenze. Questi desktop non persistenti vengono ripristinati allo stato originale dopo la disconnessione dell'utente.

In questo articolo verranno fornite le indicazioni di Microsoft per gli amministratori sul supporto per l'identità del dispositivo e VDI. Per ulteriori informazioni sull'identità del dispositivo, vedere l'articolo [Che cos'è un'identità del dispositivo](overview.md).

## <a name="supported-scenarios"></a>Scenari supportati

Prima di configurare le identità dei dispositivi in Azure AD per l'ambiente VDI, acquisire familiarità con gli scenari supportati. La tabella seguente illustra gli scenari di provisioning supportati. Il provisioning in questo contesto implica che un amministratore può configurare le identità dei dispositivi su larga scala senza richiedere alcuna interazione con l'utente finale.

| Tipo di identità del dispositivo | Infrastruttura di identità | Dispositivi Windows | Versione della piattaforma VDI | Supportato |
| --- | --- | --- | --- | --- |
| Aggiunta a Azure AD ibrido | Federato | Windows corrente e Windows di livello inferiore | Persistente | Sì |
|   |   | Corrente di Windows | Non persistente | No |
|   |   | Dispositivi Windows di livello inferiore | Non persistente | Sì |
|   | Gestito . | Windows corrente e Windows di livello inferiore | Persistente | Sì |
|   |   | Corrente di Windows | Non persistente | No |
|   |   | Dispositivi Windows di livello inferiore | Non persistente | Sì |
| Aggiunta ad Azure AD | Federato | Corrente di Windows | Persistente | No |
|   |   |   | Non persistente | No |
|   | Gestito | Corrente di Windows | Persistente | No |
|   |   |   | Non persistente | No |
| Registrazione in Azure AD | Federato | Corrente di Windows | Persistente | No |
|   |   |   | Non persistente | No |
|   | Gestito | Corrente di Windows | Persistente | No |
|   |   |   | Non persistente | No |

\*Un ambiente di infrastruttura di identità **federata** rappresenta un ambiente con un provider di identità, ad esempio ADFS o un altro IDP di terze parti.

\*\*Un ambiente di infrastruttura di identità **gestita** rappresenta un ambiente con Azure AD come provider di identità distribuito con la [sincronizzazione con hash delle password (PHS)](../hybrid/whatis-phs.md) o l'autenticazione [pass-through (PTA)](../hybrid/how-to-connect-pta.md) con [Single Sign-On senza soluzione di continuità.](../hybrid/how-to-connect-sso.md)

\*\*\*I dispositivi **Windows correnti** rappresentano Windows 10, Windows Server 2016 e Windows Server 2019.

\*\*\*\*I dispositivi **Windows down-level** rappresentano Windows 7, Windows 8.1, Windows Server 2008 R2, Windows Server 2012 e Windows Server 2012 R2. Per informazioni di supporto in Windows 7, vedere [Il supporto per Windows 7 sta terminando](https://www.microsoft.com/microsoft-365/windows/end-of-windows-7-support). Per informazioni di supporto in Windows Server 2008 R2, vedere [Preparazione per la fine del supporto di Windows Server 2008](https://www.microsoft.com/cloud-platform/windows-server-2008).

## <a name="microsofts-guidance"></a>Indicazioni di Microsoft

Gli amministratori devono fare riferimento agli articoli seguenti, in base all'infrastruttura di identità, per informazioni su come configurare l'aggiunta ad Azure AD ibrido.

- [Configurare l'aggiunta di Azure Active Directory ibrido per l'ambiente federatoConfigure hybrid Azure Active Directory join for federated environment](hybrid-azuread-join-federated-domains.md)
- [Configurare l'aggiunta ibrida ad Azure Active Directory per l'ambiente gestitoConfigure hybrid Azure Active Directory join for managed environment](hybrid-azuread-join-managed-domains.md)

Se si utilizza l'Utilità preparazione sistema (sysprep.exe) e si usa un'immagine precedente a Windows 10 1809 per l'installazione, assicurarsi che l'immagine non provenga da un dispositivo già registrato con Azure AD come aggiunta ibrida ad Azure AD.

Se si utilizza uno snapshot della macchina virtuale (VM) per creare macchine virtuali aggiuntive, assicurarsi che lo snapshot non provenga da una macchina virtuale già registrata con Azure AD come join ibrido di Azure AD.

Quando si distribuiscono VDI non persistenti, gli amministratori IT devono prestare particolare attenzione alla gestione dei dispositivi obsoleti in Azure AD. Microsoft consiglia agli amministratori IT di implementare le linee guida riportate di seguito. In caso contrario, la directory connumeroserà molti dispositivi aggiunti ad Azure AD di Azure AD non aggiornati registrati dalla piattaforma VDI non persistente.

- Creare e utilizzare un prefisso per il nome visualizzato del computer che indica il desktop come basato su VDI.
- Implementare il comando seguente come parte dello script di disconnessione. Questo comando attiverà una chiamata con il massimo sforzo ad Azure AD per eliminare il dispositivo.
   - Per i dispositivi di livello inferiore di Windows – autoworkplace.exe /leave
- Definire e implementare il processo per [la gestione dei dispositivi non aggiornati.](manage-stale-devices.md)
   - Dopo aver identificato i dispositivi aggiunti ad Azure AD ibrido non persistenti, è possibile essere più aggressivi nella pulizia di questi dispositivi per garantire che la directory non venga usata con molti dispositivi non aggiornati.
 
## <a name="next-steps"></a>Passaggi successivi

[Configurazione dell'aggiunta di Azure Active Directory ibrido per l'ambiente federatoConfiguring hybrid Azure Active Directory join for federated environment](hybrid-azuread-join-federated-domains.md)
