---
title: Identità del dispositivo e virtualizzazione desktop-Azure Active Directory
description: Informazioni sul modo in cui è possibile usare insieme le identità dei dispositivi VDI e Azure AD
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
ms.openlocfilehash: f0313c27666cda785b24f23436f6ad727f337ca8
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/01/2020
ms.locfileid: "89259354"
---
# <a name="device-identity-and-desktop-virtualization"></a>Identità del dispositivo e virtualizzazione desktop

Gli amministratori distribuiscono in genere piattaforme VDI (Virtual Desktop Infrastructure) che ospitano sistemi operativi Windows nelle organizzazioni. Gli amministratori distribuiscono VDI a:

- Gestione semplificata.
- Ridurre i costi tramite il consolidamento e la centralizzazione delle risorse.
- Offri la mobilità degli utenti finali e la libertà di accedere ai desktop virtuali in qualsiasi momento, ovunque si trovino, su qualsiasi dispositivo.

Esistono due tipi principali di desktop virtuali:

- Persistente
- Non persistente

Le versioni permanenti usano un'immagine desktop univoca per ogni utente o un pool di utenti. Questi desktop univoci possono essere personalizzati e salvati per un uso futuro. 

Le versioni non permanenti usano una raccolta di desktop a cui gli utenti possono accedere in base alle esigenze. Questi desktop non persistenti vengono ripristinati allo stato originale, nel caso di Windows Current<sup>1</sup> , ciò si verifica quando una macchina virtuale viene sottoposta a un processo di arresto/riavvio/ripristino del sistema operativo e in caso di Windows di livello<sup>2</sup> si verifica quando un utente si disconnette.

Si è verificato un incremento nelle distribuzioni VDI non permanenti perché il lavoro remoto continua a essere la nuova norma. Quando i clienti distribuiscono un'infrastruttura VDI non persistente, è importante assicurarsi di gestire la varianza del dispositivo che potrebbe essere causata da una registrazione frequente del dispositivo senza una strategia appropriata per la gestione del ciclo di vita dei dispositivi.

> [!IMPORTANT]
> La mancata gestione della varianza del dispositivo può causare un aumento della pressione sul consumo di utilizzo della quota tenant e il rischio potenziale di interruzioni del servizio, se si esaurisce la quota del tenant. Per evitare questa situazione, è necessario seguire le istruzioni descritte di seguito per la distribuzione di ambienti VDI non permanenti.

In questo articolo vengono illustrate le linee guida di Microsoft per gli amministratori sul supporto per l'identità del dispositivo e l'infrastruttura VDI. Per altre informazioni sull'identità del dispositivo, vedere l'articolo [che cos'è un'identità del dispositivo](overview.md).

## <a name="supported-scenarios"></a>Scenari supportati

Prima di configurare le identità del dispositivo in Azure AD per l'ambiente VDI, acquisire familiarità con gli scenari supportati. La tabella seguente illustra gli scenari di provisioning supportati. Il provisioning in questo contesto implica che un amministratore può configurare le identità dei dispositivi su larga scala senza richiedere alcuna interazione con l'utente finale.

| Tipo di identità del dispositivo | Infrastruttura delle identità | Dispositivi Windows | Versione della piattaforma VDI | Supportato |
| --- | --- | --- | --- | --- |
| Aggiunta a Azure AD ibrido | Federato<sup>3</sup> | Windows Current e Windows legacy-Level | Persistente | Sì |
|   |   | Windows corrente | Non persistente | Sì<sup>5</sup> |
|   |   | Dispositivi Windows di livello inferiore | Non persistente | Sì<sup>6</sup> |
|   | Gestito<sup>4</sup> | Windows Current e Windows legacy-Level | Persistente | Sì |
|   |   | Windows corrente | Non persistente | No |
|   |   | Dispositivi Windows di livello inferiore | Non persistente | Sì<sup>6</sup> |
| Aggiunta ad Azure AD | Federato | Windows corrente | Persistente | No |
|   |   |   | Non persistente | No |
|   | Gestita | Windows corrente | Persistente | No |
|   |   |   | Non persistente | No |
| Registrazione in Azure AD | Federato/gestito | Windows corrente/Windows di livello inferiore | Persistente/non persistente | Non applicabile |

<sup>1</sup> i dispositivi **Windows correnti** rappresentano Windows 10, Windows Server 2016 e Windows Server 2019.

<sup>2</sup> i dispositivi **Windows di livello inferiore** rappresentano Windows 7, Windows 8.1, Windows Server 2008 r2, Windows Server 2012 e Windows Server 2012 R2. Per informazioni di supporto su Windows 7, vedere la pagina relativa al [supporto per Windows 7](https://www.microsoft.com/microsoft-365/windows/end-of-windows-7-support). Per informazioni di supporto su Windows Server 2008 R2, vedere [preparare la fine del supporto tecnico di Windows server 2008](https://www.microsoft.com/cloud-platform/windows-server-2008).

<sup>3</sup> un ambiente **federato** di infrastruttura di identità rappresenta un ambiente con un provider di identità, ad esempio ad FS o un altro IDP di terze parti.

<sup>4</sup> un ambiente di infrastruttura di identità **gestita** rappresenta un ambiente con Azure ad come provider di identità distribuito con la [sincronizzazione dell'hash delle password (pH)](../hybrid/whatis-phs.md) o con [l'autenticazione pass-through (PTA)](../hybrid/how-to-connect-pta.md) con [Single Sign-on senza](../hybrid/how-to-connect-sso.md)problemi.

<sup>5</sup> il **supporto di non persistenza per Windows Current** richiede considerazioni aggiuntive, come descritto di seguito nella sezione delle linee guida. Questo scenario richiede Windows 10 1803, Windows Server 2019 o Windows Server (canale semestrale) a partire dalla versione 1803

<sup>6</sup> il **supporto di non persistenza per Windows di livello inferiore** richiede considerazioni aggiuntive, come descritto di seguito nella sezione delle linee guida.


## <a name="microsofts-guidance"></a>Informazioni aggiuntive su Microsoft

Gli amministratori devono fare riferimento agli articoli seguenti, in base alla relativa infrastruttura di identità, per informazioni su come configurare Hybrid Azure AD join.

- [Configurare il join di Azure Active Directory ibrido per l'ambiente federato](hybrid-azuread-join-federated-domains.md)
- [Configurare il join di Azure Active Directory ibrido per l'ambiente gestito](hybrid-azuread-join-managed-domains.md)

Quando si distribuisce un'infrastruttura VDI non persistente, Microsoft consiglia agli amministratori IT di implementare le indicazioni seguenti. In caso contrario, la directory che contiene molti dispositivi non aggiornati Azure AD ibrido aggiunti alla piattaforma VDI non persistente comporta un aumento della quota dei tenant e il rischio di interruzioni del servizio dovuti all'esaurimento della quota del tenant.

- Se si utilizza l'utilità preparazione sistema (sysprep.exe) e si utilizza un'immagine precedente a Windows 10 1809 per l'installazione, assicurarsi che l'immagine non venga da un dispositivo già registrato con Azure AD come ibrido Azure AD aggiunto.
- Se si utilizza uno snapshot di macchina virtuale (VM) per creare altre macchine virtuali, assicurarsi che lo snapshot non venga da una macchina virtuale già registrata con Azure AD come Azure AD ibrido join.
- Creare e utilizzare un prefisso per il nome visualizzato (ad esempio, NPVDI-) del computer che indica il desktop come basato su VDI non persistente.
- Per Windows di livello inferiore:
   - Implementare il comando **autoworkplacejoin/Leave** come parte dello script di disconnessione. Questo comando deve essere attivato nel contesto dell'utente e deve essere eseguito prima che l'utente si sia disconnesso completamente e che la connettività di rete sia ancora disponibile.
- Per Windows Current in un ambiente federato (ad esempio AD FS):
   - Implementare **dsregcmd/join** come parte della sequenza di avvio della macchina virtuale.
   - **Non eseguire dsregcmd** /Leave come parte del processo di arresto/riavvio della macchina virtuale.
- Definire e implementare il processo per la [gestione dei dispositivi non aggiornati](manage-stale-devices.md).
   - Quando si ha una strategia per identificare i dispositivi non persistenti Azure AD ibrido aggiunti (ad esempio, usando il prefisso del nome visualizzato del computer), è necessario essere più aggressivi per la pulizia di questi dispositivi per assicurarsi che la directory non venga usata con molti dispositivi non aggiornati.
   - Per le distribuzioni VDI non persistenti in Windows correnti e di livello inferiore, è consigliabile eliminare i dispositivi con **ApproximateLastLogonTimestamp** di età superiore a 15 giorni.
 
## <a name="next-steps"></a>Passaggi successivi

[Configurazione del join Azure Active Directory ibrido per l'ambiente federato](hybrid-azuread-join-federated-domains.md)
