---
title: Sicurezza fisica di HSM - HSM dedicato di Azure | Microsoft Docs
description: Informazioni sulla sicurezza fisica dei dispositivi HSM dedicati di Azure nei data center
services: dedicated-hsm
author: barclayn
manager: barbkess
ms.custom: mvc, seodec18
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: barclayn
ms.openlocfilehash: 6fe0557aa90ec3345d4ca0c3d9292cab3435bb2f
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "62118061"
---
# <a name="azure-dedicated-hsm-physical-security"></a>Sicurezza fisica di HSM dedicato di Azure

HSM dedicato di Azure consente di soddisfare requisiti di sicurezza avanzati per l'archiviazione delle chiavi. Viene gestito seguendo procedure di sicurezza rigorose nell'intero ciclo di vita, per soddisfare le esigenze dei clienti.

## <a name="security-through-procurement"></a>Sicurezza tramite approvvigionamento

Microsoft segue un processo di approvvigionamento sicuro. Gestisce la catena di custodia e verifica che lo specifico dispositivo ordinato e spedito sia quello consegnato nei data center. I dispositivi sono in buste di plastica a prova di manomissione. Vengono conservati in un'area di deposito protetta finché non vengono messi in funzione nella sala dati del data center.  Il rack che contengono i dispositivi HSM sono considerati ad alto impatto aziendale (High Business Impact, HBI). I dispositivi sono fissati e sottoposti a videosorveglianza costante dalla parte frontale e posteriore.

## <a name="security-through-deployment"></a>Sicurezza tramite la distribuzione

I moduli di protezione hardware vengono installati in rack insieme ai componenti di rete associati. Dopo l'installazione, devono essere configurati prima di essere resi disponibili come parte del servizio HSM dedicato di Azure. L'attività di configurazione viene eseguita da dipendenti Microsoft che sono stati sottoposti a un controllo dei precedenti penali. Per limitare l'accesso ai soli dipendenti autorizzati e solo per il tempo necessario, viene usata l'amministrazione JIT. Le procedure e i sistemi usati assicurano inoltre che tutte le attività correlate ai dispositivi HSM sia registrata.

## <a name="security-in-operations"></a>Sicurezza nelle operazioni

I dispositivi HSM sono appliance hardware (l'effettivo modulo di protezione hardware è una scheda PCI all'interno dell'appliance), quindi è possibile che si verifichino problemi a livello di componenti. I problemi potenziali includono, ad esempio, guasti di alimentatori e ventole. Gli eventi di questo tipo richiedono attività di manutenzione o riparazione per sostituire i componenti rimovibili.

### <a name="component-replacement"></a>Sostituzione di componenti

Dopo il provisioning e quando la gestione del dispositivo è affidata al cliente, l'alimentatore con tecnologia hot swap è l'unico componente che può essere sostituito. Questo componente è all'esterno del limite di sicurezza e non causa un evento di manomissione. Un sistema di creazione ticket viene usato per autorizzare un tecnico Microsoft ad accedere alla parte posteriore del rack ad alto impatto aziendale. Quando il ticket viene elaborato, viene rilasciata una chiave fisica temporanea. Questa chiave offre al tecnico l'accesso al dispositivo e consente di sostituire il componente interessato. Qualsiasi altro accesso (vale a dire che causi un evento di manomissione) verrebbe eseguito quando il dispositivo non è allocato al cliente, riducendo così al minimo i rischi per la disponibilità e la sicurezza.  

### <a name="device-replacement"></a>Sostituzione del dispositivo

In caso di guasto completo del dispositivo, viene seguito un processo simile a quello usato per il guasto di un componente. Se il cliente non è in grado di impostare su zero il dispositivo o quest'ultimo è in uno stato sconosciuto, i dispositivi contenenti dati verranno rimossi e inseriti in un contenitore per la distruzione interno al rack. I dispositivi collocati nel contenitore verranno distrutti in modo controllato e sicuro. Nessun dispositivo contenente dati proveniente da un rack ad alto impatto aziendale lascerà un data center Microsoft.

### <a name="other-rack-access-activities"></a>Altre attività di accesso al rack

Se un tecnico Microsoft deve accedere al rack usato dai dispositivi HSM (ad esempio per la manutenzione dei dispositivi di rete), per accedere al rack HBI protetto verranno seguire le procedure di sicurezza standard. Tutti gli accessi saranno sottoposti a videosorveglianza. I dispositivi HSM sono convalidati [FIPS 140-2 Livello 3](https://nvlpubs.nist.gov/nistpubs/FIPS/NIST.FIPS.140-2.pdf), pertanto qualsiasi accesso non autorizzato verrà segnalato al cliente e i dati verranno impostati su zero.

## <a name="logical-level-security-considerations"></a>Considerazioni sulla sicurezza a livello logico

Il provisioning dei moduli di protezione hardware viene effettuato in una rete virtuale creata dal cliente. Si tratta dello spazio di indirizzo IP privato del cliente.  Questa configurazione offre un utile isolamento a livello di rete logica e garantisce l'accesso esclusivo al cliente. Ciò implica che tutti i controlli di sicurezza a livello logico sono responsabilità del cliente.

## <a name="next-steps"></a>Passaggi successivi

Prima di eseguire il provisioning di dispositivi o di iniziare la progettazione o distribuzione di applicazioni, è consigliabile aver ben compreso tutti i concetti chiave relativi al servizio, ad esempio disponibilità elevata, sicurezza e supporto.

* [Disponibilità elevata](high-availability.md)
* [Rete](networking.md)
* [Supporto](supportability.md)
* [Monitoraggio](monitoring.md)
* [Architettura di distribuzione](deployment-architecture.md)