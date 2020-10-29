---
title: Azioni non consentite durante l'accesso con privilegi elevati
description: Il motore VMware Annulla le modifiche per garantire che il servizio rimanga ininterrotto quando il motore VMware rileva una delle azioni non consentite seguenti.
titleSuffix: Azure VMware Solution by CloudSimple
ms.date: 10/28/2020
ms.topic: article
ms.service: azure-vmware-cloudsimple
author: divka78
ms.author: dikamath
ms.openlocfilehash: 81a09a9cfe749de56f7306a8d5e1ae5db01295fc
ms.sourcegitcommit: d76108b476259fe3f5f20a91ed2c237c1577df14
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2020
ms.locfileid: "92915434"
---
# <a name="forbidden-actions-during-elevated-access"></a>Azioni non consentite durante l'accesso con privilegi elevati

Durante l'intervallo di tempo di elevazione, alcune azioni sono proibite. Quando il motore VMware rileva una delle azioni non consentite seguenti, il motore VMware Ripristina le modifiche per garantire che il servizio rimanga ininterrotto.

## <a name="cluster-actions"></a>Azioni cluster

- Rimozione di un cluster da vCenter.
- Modifica di vSphere High Availability (HA) in un cluster.
- Aggiunta di un host al cluster da vCenter.
- Rimozione di un host dal cluster da vCenter.

## <a name="host-actions"></a>Azioni host

- Rimozione di archivi dati in un host ESXi.
- Disinstallazione dell'agente vCenter dall'host.
- Modifica della configurazione host.
- Apportare modifiche ai profili host.
- Inserimento di un host in modalità manutenzione.

## <a name="network-actions"></a>Azioni di rete

- Eliminazione del Commuter virtuale distribuito (DVS) predefinito in un cloud privato.
- Rimozione di un host dall'oggetto DVS predefinito.
- Importazione di qualsiasi impostazione DVS.
- Riconfigurazione di qualsiasi impostazione DVS.
- Aggiornamento di qualsiasi DVS.
- Eliminazione del gruppo porte di gestione.
- Modifica della gruppo porte di gestione.

## <a name="roles-and-permissions-actions"></a>Azioni di ruoli e autorizzazioni

- Creazione di un ruolo globale.
- Modifica o eliminazione dell'autorizzazione per tutti gli oggetti di gestione.
- Modifica o rimozione di ruoli predefiniti.
- Aumentare i privilegi di un ruolo a un livello superiore rispetto al ruolo di proprietario cloud.

## <a name="other-actions"></a>Altre azioni

- Rimozione di tutte le licenze predefinite:
  - Server vCenter
  - Nodi ESXi
  - NSX-T
  - HCX
- Modifica o eliminazione del pool di risorse di gestione.
- Clonazione delle macchine virtuali di gestione.


## <a name="next-steps"></a>Passaggi successivi
[Manutenzione e aggiornamenti di CloudSimple](cloudsimple-maintenance-updates.md) 
