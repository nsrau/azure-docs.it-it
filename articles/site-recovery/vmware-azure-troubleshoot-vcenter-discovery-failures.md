---
title: Risoluzione dei problemi relativi al failback in un'istanza locale durante il ripristino di emergenza di macchine virtuali VMware in Azure con Azure Site Recovery | Microsoft Docs
description: Questo articolo descrive le soluzioni agli errori di failback e riprotezione che possono verificarsi durante il ripristino di emergenza di macchine virtuali VMware in Azure con Azure Site Recovery.
author: vDonGlover
manager: JarrettRenshaw
ms.service: site-recovery
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: v-doglov
ms.openlocfilehash: c598c5e238458c010500579c5371622b85e71de0
ms.sourcegitcommit: cf971fe82e9ee70db9209bb196ddf36614d39d10
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2019
ms.locfileid: "58540806"
---
# <a name="troubleshoot-vcenter-discovery-failures"></a>Risoluzione dei problemi di individuazione di vCenter

Questo articolo consente di risolvere i problemi che si verificano a causa di errori di individuazione di VMware vCenter.

## <a name="non-numeric-values-in-the-maxsnapshots-property"></a>Valori non numerici nella proprietà maxSnapShots

Nelle versioni precedenti 9.20, vCenter disconnette durante il recupero di un valore non numerico per la proprietà `snapshot.maxSnapShots` proprietà in una macchina virtuale.

Questo problema identificato dall'ID di errore 95126.

    ERROR :: Hit an exception while fetching the required informationfrom vCenter/vSphere.Exception details:
    System.FormatException: Input string was not in a correct format.
       at System.Number.StringToNumber(String str, NumberStyles options, NumberBuffer& number, NumberFormatInfo info, Boolean parseDecimal)
       at System.Number.ParseInt32(String s, NumberStyles style, NumberFormatInfo info)
       at VMware.VSphere.Management.InfraContracts.VirtualMachineInfo.get_MaxSnapshots()
    
Per risolvere il problema:

- Identificare la macchina virtuale e impostare il valore su un valore numerico (macchina virtuale Modifica impostazioni in vCenter).

Oppure

- Aggiornare il server di configurazione a 9.20 o versione successiva.

## <a name="proxy-configuration-issues-for-vcenter-connectivity"></a>Problemi di configurazione proxy per la connettività di vCenter

vCenter individuazione rispetta le impostazioni di proxy predefinito di sistema configurate dall'utente del sistema. Il servizio DRA rispetta le impostazioni del proxy fornite dall'utente durante l'installazione del server di configurazione tramite il programma di installazione unificata o il modello con estensione OVA. 

In generale, viene utilizzato il proxy per comunicare su reti pubbliche; quali la comunicazione con Azure. Se il proxy è configurato e vCenter è in un ambiente locale, non sarà in grado di comunicare con i DRA.

Quando viene rilevato questo problema, si verificano le situazioni seguenti:

- Il server vCenter \<vCenter > non è raggiungibile a causa dell'errore: Il server remoto ha restituito un errore: Non disponibile (503) server
- Il server vCenter \<vCenter > non è raggiungibile a causa dell'errore: Il server remoto ha restituito un errore: Non è possibile connettersi al server remoto.
- Impossibile connettersi al server vCenter/ESXi.

Per risolvere il problema:

Scaricare il [lo strumento PsExec](https://aka.ms/PsExec). 

Utilizzare lo strumento PsExec per accedere al contesto utente di sistema e determinare se l'indirizzo del proxy è configurato. È quindi possibile aggiungere vCenter all'elenco di esclusione eseguendo la procedura seguente.

Per la configurazione del proxy di individuazione:

1. Aprire Internet Explorer nel contesto utente di sistema utilizzando lo strumento PsExec.
    
    psexec -s -i "%programfiles%\Internet Explorer\iexplore.exe"

2. Modificare le impostazioni di proxy in Internet Explorer per ignorare l'indirizzo IP di vCenter.
3. Riavviare il servizio tmanssvc.

Per la configurazione del proxy DRA:

1. Aprire un prompt dei comandi e aprire la cartella Provider di Microsoft Azure Site Recovery.
 
    **CD C:\Program Files\Microsoft Azure Site Recovery Provider**

3. Dal prompt dei comandi, eseguire il comando seguente.
   
   **DRCONFIGURATOR. File EXE / Configura /AddBypassUrls [IP indirizzo/FQDN di vCenter Server fornito al momento dell'aggiunta di vCenter]**

4. Riavviare il servizio provider DRA.

## <a name="next-steps"></a>Passaggi successivi

[Gestire il server di configurazione per il ripristino di emergenza di VM VMware](https://docs.microsoft.com/azure/site-recovery/vmware-azure-manage-configuration-server#refresh-configuration-server) 
