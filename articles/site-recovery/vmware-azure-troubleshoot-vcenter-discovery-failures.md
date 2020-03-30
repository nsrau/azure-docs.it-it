---
title: Risolvere gli errori di individuazione vMware vCenter in Azure Site RecoveryTroubleshoot VMware vCenter discovery failures in Azure Site Recovery
description: In questo articolo viene descritto come risolvere gli errori di individuazione di VMware vCenter in Azure Site Recovery.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/29/2019
ms.author: mayg
ms.openlocfilehash: f00c7b12accde9df9a5708a2b8b378d70428318d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74091244"
---
# <a name="troubleshoot-vcenter-server-discovery-failures"></a>Risolvere gli errori di individuazione di vCenter ServerTroubleshoot vCenter Server discovery failures

In questo articolo viene possibile risolvere i problemi che si verificano a causa di errori di individuazione vCenter di VMware.

## <a name="non-numeric-values-in-the-maxsnapshots-property"></a>Valori non numerici nella proprietà maxSnapShots

Nelle versioni precedenti alla 9.20, vCenter si disconnette quando `snapshot.maxSnapShots` recupera un valore non numerico per la proprietà della proprietà in una macchina virtuale.

Questo problema è identificato dall'ID errore 95126.

    ERROR :: Hit an exception while fetching the required informationfrom vCenter/vSphere.Exception details:
    System.FormatException: Input string was not in a correct format.
       at System.Number.StringToNumber(String str, NumberStyles options, NumberBuffer& number, NumberFormatInfo info, Boolean parseDecimal)
       at System.Number.ParseInt32(String s, NumberStyles style, NumberFormatInfo info)
       at VMware.VSphere.Management.InfraContracts.VirtualMachineInfo.get_MaxSnapshots()
    
Per risolvere il problema:

- Identificare la macchina virtuale e impostare il valore su un valore numerico (impostazioni di modifica della macchina virtuale in vCenter).

Oppure

- Aggiornare il server di configurazione alla versione 9.20 o successiva.

## <a name="proxy-configuration-issues-for-vcenter-connectivity"></a>Problemi di configurazione proxy per la connettività vCenter

vCenter Discovery rispetta le impostazioni proxy predefinite del sistema configurate dall'utente del sistema. Il servizio DRA rispetta le impostazioni proxy fornite dall'utente durante l'installazione del server di configurazione utilizzando il programma di installazione unificato o il modello OVA. 

In generale, il proxy viene utilizzato per comunicare alle reti pubbliche; come comunicare con Azure. Se il proxy è configurato e vCenter si trova in un ambiente locale, non sarà in grado di comunicare con DRA.

Quando si verifica questo problema, si verificano le seguenti situazioni:

- Il> \<vCenter del server vCenter non è raggiungibile a causa dell'errore: il server remoto ha restituito un errore: (503) Server non disponibile
- Il server \<vCenter di vCenter> non è raggiungibile a causa dell'errore: il server remoto ha restituito un errore: Impossibile connettersi al server remoto.
- Impossibile connettersi al server vCenter/ESXi.

Per risolvere il problema:

Scaricare lo [strumento PsExec](https://aka.ms/PsExec). 

Utilizzare lo strumento PsExec per accedere al contesto utente di sistema e determinare se l'indirizzo proxy è configurato. È quindi possibile aggiungere vCenter all'elenco di esclusione utilizzando le procedure seguenti.

Per la configurazione del proxy di individuazione:

1. Aprire IE nel contesto utente di sistema utilizzando lo strumento PsExec.
    
    psexec -s -i "%programfiles%

2. Modificare le impostazioni proxy in Internet Explorer per ignorare l'indirizzo IP del vCenter.
3. Riavviare il servizio tmanssvc.

Per la configurazione del proxy DRA:

1. Aprire un prompt dei comandi e aprire la cartella Microsoft Azure Site Recovery Provider.
 
    **cd C: , Programmi , Provider di site recovery di Microsoft Azure**

3. Dal prompt dei comandi, eseguire il comando seguente.
   
   **DRCONFIGURATOR. EXE /configure /AddBypassUrls [Indirizzo IP/FQDN di vCenter Server fornito al momento dell'aggiunta di vCenter]**

4. Riavviare il servizio del provider DRA.

## <a name="next-steps"></a>Passaggi successivi

[Gestire il server di configurazione per il ripristino di emergenza di macchine virtuali VMware](https://docs.microsoft.com/azure/site-recovery/vmware-azure-manage-configuration-server#refresh-configuration-server) 
