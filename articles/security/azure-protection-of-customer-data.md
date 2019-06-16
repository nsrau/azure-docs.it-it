---
title: Protezione dei dati dei clienti in Azure
description: Questo articolo spiega in che modo Azure protegge i dati dei clienti.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: TomSh
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/28/2018
ms.author: terrylan
ms.openlocfilehash: 04163d1fa2a46a2de877702d479f439a5e8711d7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65603143"
---
# <a name="azure-customer-data-protection"></a>Protezione dei dati dei clienti di Azure   
L'accesso ai dati dei clienti da parte del personale di supporto e delle operazioni Microsoft è negato per impostazione predefinita. Quando viene concesso l'accesso ai dati dei clienti, è necessaria l'approvazione della leadership prima di poter gestire e registrare l'accesso in modo accurato. I requisiti di controllo di accesso vengono definiti dai criteri di sicurezza di Azure seguenti:

- Nessun accesso ai dati dei clienti per impostazione predefinita.
- Nessun account utente o amministratore nelle macchine virtuali (VM) dei clienti.
- Concessione dei privilegi minimi necessari per completare l'attività; controllo e registrazione delle richieste di accesso.

Al personale di supporto di Azure vengono assegnati account aziendali Active Directory univoci da parte di Microsoft. Azure si basa su Active Directory aziendale di Microsoft, gestito da Microsoft Information Technology (MSIT), per controllare l'accesso ai principali sistemi informativi. È necessaria l'autenticazione a più fattori e l'accesso viene concesso solo da console sicure.

Tutti i tentativi di accesso vengono monitorati e possono essere visualizzati tramite un set di report di base.

## <a name="data-protection"></a>Protezione dati
Azure offre ai clienti un livello efficace di sicurezza dei dati, per impostazione predefinita e come opzione.

**Separazione dei dati**: Azure è un servizio multi-tenant, ovvero un servizio in cui le distribuzioni e le macchine virtuali di più clienti vengono archiviate nello stesso hardware fisico. Microsoft usa l'isolamento logico per separare i dati di ogni cliente dagli altri. La separazione offre i vantaggi economici e di ridimensionamento dei servizi multi-tenant, impedendo al contempo in modo rigoroso ai clienti di accedere ai dati altrui.

**Protezione dei dati inattivi**: i clienti sono tenuti a garantire che i dati archiviati in Azure vengano crittografati in base ai propri standard. Azure offre un'ampia gamma di funzionalità di crittografia, fornendo ai clienti la flessibilità di scegliere la soluzione che meglio rispondono alle loro esigenze. Azure Key Vault consente ai clienti di mantenere in modo semplice il controllo sulle chiavi usate dalle applicazioni e dai servizi cloud per crittografare i dati. Crittografia dischi di Azure permette inoltre ai clienti di crittografare le macchine virtuali, mentre la crittografia del servizio di archiviazione di Azure consente di crittografare tutti i dati inseriti nell'account di archiviazione di un cliente.

**Protezione dei dati in movimento**: i clienti possono abilitare la crittografia del traffico tra le proprie macchine virtuali e gli utenti finali. Azure protegge i dati in movimento da o verso i componenti esterni e a livello interno, ad esempio tra due reti virtuali. Azure usa il protocollo TLS (Transport Layer Security) 1.2 o superiore, standard del settore, con chiavi di crittografia RSA/SHA256 a 2,048 bit, come consigliato da CESG/NCSC, per crittografare le comunicazioni tra:

- il cliente e il cloud e
- i sistemi e i data center di Azure (internamente).

**Crittografia**: la crittografia dei dati archiviati e in movimento può essere distribuita dai clienti come procedura consigliata per garantire la riservatezza e l'integrità dei dati. È molto semplice per i clienti configurare i servizi cloud di Azure per l'uso di SSL al fine di proteggere le comunicazioni da Internet e anche tra le proprie VM ospitate in Azure.

**Ridondanza dei dati**: Microsoft consente di proteggere i dati in caso di attacco informatico o danni fisici in un data center. I clienti possono scegliere varie opzioni:

- Spazio di archiviazione in paese / nell'area per considerazioni sulla conformità o la latenza.
- Archiviazione out-di-out-di-paese per scopi di ripristino di emergenza o sicurezza.

I dati possono essere replicati all'interno di un'area geografica selezionata per la ridondanza, ma non possono essere trasmessi al suo esterno. I clienti hanno più opzioni a disposizione per la replica dei dati, tra cui il numero delle copie e il numero e la posizione dei data center di replica.

Quando si crea un account di archiviazione, selezionare una delle opzioni di replica seguenti:

- **Archiviazione con ridondanza locale**.  Con l'archiviazione con ridondanza locale vengono conservate tre copie dei dati. Tale tipo di archiviazione viene replicato per tre volte all'interno di una singola struttura di una singola area. Questa opzione di archiviazione con ridondanza locale protegge i dati dai normali errori hardware ma non dagli errori di una singola struttura.
- **Archiviazione con ridondanza della zona (ZRS)** .  Con l'archiviazione con ridondanza della zona vengono conservate tre copie dei dati. Questa opzione viene replicata tre volte in due o tre strutture per garantire una durabilità maggiore rispetto all'archiviazione con ridondanza locale. La replica viene eseguita in una singola area o in due aree. Questa opzione ZRS consente di garantire la durabilità dei dati all'interno di una singola area.
- **Archiviazione con ridondanza geografica**: L'archiviazione con ridondanza geografica è abilitata per impostazione predefinita quando si crea l'account di archiviazione. Con tale tipo di archiviazione vengono conservate sei copie dei dati. Con l'archiviazione con ridondanza geografica, i dati vengono replicati tre volte all'interno dell'area primaria. I dati vengono replicati per tre volte anche in un'area secondaria situata a centinaia di chilometri di distanza dall'area primaria, fornendo il massimo livello di durabilità. In caso di errore nell'area primaria, il servizio di Archiviazione di Azure esegue il failover all'area secondaria. L'archiviazione con ridondanza geografica consente di garantire la durabilità dei dati in due aree distinte.

**Distruzione dei dati**: quando i clienti eliminano i dati o cessano di usare Azure, Microsoft applica rigidi standard per la sovrascrittura delle risorse di archiviazione prima del riutilizzo ed esegue l'eliminazione fisica dell'hardware ritirato. Microsoft esegue l'eliminazione completa dei dati su richiesta dei clienti e al termine del contratto.

## <a name="customer-data-ownership"></a>Proprietà dei dati dei clienti
Microsoft non analizza, approva o monitora le applicazioni distribuite dai clienti in Azure. Inoltre, Microsoft non conosce i tipi di dati che i clienti hanno scelto di archiviare in Azure. Microsoft non rivendica la proprietà dei dati rispetto alle informazioni sul cliente immesse in Azure.

## <a name="records-management"></a>Gestione dei record
Azure ha stabilito requisiti interni di conservazione dei record per i dati backend. I clienti sono tenuti a identificare i propri requisiti di conservazione dei record. Per i record archiviati in Azure, i clienti sono responsabili dell'estrazione dei dati e della conservazione del contenuto all'esterno di Azure per un periodo specificato dal cliente.

Azure consente ai clienti di esportare i dati e i report di controllo dal prodotto. Le esportazioni vengono salvate in locale per conservare le informazioni per un periodo definito dal cliente.

## <a name="electronic-discovery-e-discovery"></a>Individuazione elettronica (e-discovery)
I clienti di Azure sono tenuti a garantire la conformità ai requisiti di e-discovery nel loro uso dei servizi di Azure. Se i clienti di Azure devono conservare i loro dati, possono esportarli e salvarli in locale. Inoltre, i clienti possono richiedere esportazioni dei propri dati al reparto del supporto tecnico di Azure. Oltre a consentire ai clienti di esportare i dati, Azure esegue attività complete di registrazione e monitoraggio a livello interno.

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sulle operazioni eseguite da Microsoft per proteggere l'infrastruttura di Azure, vedere:

- [Azure facilities, premises, and physical security](azure-physical-security.md) (Sicurezza fisica, presupposti e strutture di Azure)
- [Disponibilità dell'infrastruttura di Azure](azure-infrastructure-availability.md)
- [Azure information system components and boundaries](azure-infrastructure-components.md) (Componenti e limiti del sistema informativo di Azure)
- [Architettura di rete di Azure](azure-infrastructure-network.md)
- [Rete di produzione di Azure](azure-production-network.md)
- [Funzionalità di sicurezza del database SQL di Azure](azure-infrastructure-sql.md)
- [Azure production operations and management](azure-infrastructure-operations.md) (Operazioni e gestione della produzione di Azure)
- [Monitoraggio dell'infrastruttura di Azure](azure-infrastructure-monitoring.md)
- [Integrità dell'infrastruttura di Azure](azure-infrastructure-integrity.md)
