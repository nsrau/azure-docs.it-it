---
title: Log di controllo per Azure Data Box, eventi Azure Data Box Heavy | Microsoft Docs
description: Vengono descritti i log di controllo completi per Data Box raccolti nelle varie fasi della Azure Data Box e Azure Data Box Heavy ordine.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 07/10/2020
ms.author: alkohli
ms.openlocfilehash: 50dbe9ab649a708fb36b1c9e4fe89bccadc7ea90
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86209684"
---
# <a name="audit-logs-for-your-azure-data-box-and-azure-data-box-heavy"></a>Log di controllo per il Azure Data Box e Azure Data Box Heavy

I log sono record immutabili e con timestamp di eventi discreti che si sono verificati nel tempo. I log contengono informazioni diagnostiche, di controllo e di sicurezza del dispositivo.  

Un Data Box o Data Box Heavy ordine esegue i passaggi seguenti durante la relativa operazione: ordine, configurazione, copia dei dati, ritorno, caricamento in Azure e verifica e cancellazione dei dati. Per ognuno di questi passaggi, tutti gli eventi vengono controllati e registrati.

Questo articolo contiene informazioni sui log di controllo Data Box inclusi i tipi di log e le informazioni raccolte, nonché la posizione dei log. 

Le informazioni contenute in questo articolo si applicano sia a Data Box sia a Data Box Heavy. Nelle sezioni successive, tutti i riferimenti a Data Box si applicano anche a Data Box Heavy. I log raccolti dal servizio Data Box in esecuzione in Azure non sono trattati in questo articolo. 


## <a name="about-audit-logs"></a>Informazioni sui log di controllo 

Nel Data Box vengono raccolti i log seguenti:

- **Log di sistema** : data box essere un dispositivo basato su Windows, vengono registrati tutti gli eventi hardware, software e di sistema. Un set di questi eventi viene raccolto e segnalato nei log di controllo del sistema. 

- Data Box di **sicurezza** : si tratta di un dispositivo basato su Windows, tutti gli eventi di sicurezza vengono registrati. Un set di questi eventi viene raccolto e segnalato nei log di controllo di sicurezza. 

- **Applicazione** : questi log sono specifici solo per data box. Questi log contengono tutti gli eventi generati nel dispositivo in risposta ai servizi Data Box in esecuzione.

Ognuno di questi log è illustrato nella sezione seguente.

### <a name="system-logs"></a>Log di sistema

Gli ID evento registro di sistema seguenti vengono raccolti come log di controllo del sistema nel Data Box:

|Nome del provider di eventi     |ID evento raccolto   |Descrizione evento   |
|-------------------|----------|----------------|
|Microsoft-Windows-kernel-generale|12  |Ora UTC in cui il sistema operativo è stato riavviato.   |
|                                |13  |Ora UTC in cui il sistema operativo è stato arrestato. |
|    |                              |
|Microsoft-Windows-kernel-Power  |41  |Il sistema è stato riavviato senza una chiusura normale.| 
|    |                              |
|Microsoft-Windows-BitLocker-driver|Tutti|    |

### <a name="security-logs"></a>Log di sicurezza

Gli ID degli eventi del registro di sicurezza seguenti vengono raccolti come log di controllo della sicurezza nel Data Box:

|Nome del provider di eventi                   |ID evento raccolto    |Descrizione evento       |
|--------------------------------------|------------|----------|
|Microsoft-Windows-sicurezza-controllo   |4624        |Accesso riuscito. |
|                                      |4625        |Accesso a un account non riuscito. Nome utente sconosciuto o password errata. |
|                                     

### <a name="application-logs"></a>Log applicazioni

Gli ID evento del registro applicazioni seguenti vengono raccolti come parte dei log di controllo del pacchetto nel Data Box.     

- **Microsoft-Azure-databox-OOBE-auditing** -contiene gli eventi che si verificano nell'interfaccia utente locale. 
- **Microsoft-Azure-databox-reprovision-audit** -contiene eventi correlati al provisioning del dispositivo data box. Il nuovo provisioning del Data Box si verifica quando il dispositivo viene reimpostato tramite l'interfaccia utente locale. È possibile scegliere questa opzione quando si desidera cancellare i dati copiati rimuovendo le condivisioni esistenti e ricreando le condivisioni come parte del nuovo provisioning o della reimpostazione del dispositivo.
- **Microsoft-Azure-databox-HcsMgmt-audit** -contiene gli eventi correlati solo al passaggio **prepara per la spedizione** prima che il dispositivo venga rispedito al Data Center di Azure. 
- **Microsoft-Azure-databox-IfxAudit** -contiene i messaggi registrati da entità diverse del prodotto sui processi, log che indicano altre informazioni su ciò che accade in alcuni flussi.

Ecco una tabella che riepiloga i vari provider di eventi e gli ID evento corrispondenti raccolti in ogni caso.

|Nome del provider di eventi    |ID evento    | Note |
|-----------------|-----------------|-------------------|
|Microsoft-Azure-DataBox-OOBE-controllo |4624        |Accesso riuscito.|
|                                      |4625        |Accesso a un account non riuscito. Nome utente sconosciuto o password errata.|
|                                     |4634        |Evento di disconnessione.|
|                                   |  | |
|Microsoft-Azure-DataBox-reprovision-audit    |65001       |Evento di provisioning riuscito.|
|                                                  |65002       |Impossibile eseguire nuovamente il provisioning dell'evento.|
|                                                  |                 |         |
|Microsoft-Azure-DataBox-HcsMgmt-audit        |65003       |Evento di stato Prepara per la spedizione NotStarted, in corso, non riuscito, annullato, riuscito, ScanCompletedWithIssues, SucceededWithWarnings          |
|                                                  |                 |     |
|Microsoft-Azure-DataBox-IfxAudit    |Tutti |Tutti gli eventi vengono registrati con l'API del log di controllo nel codice |

Di seguito è riportato un esempio di log di controllo di strumentazione Framework (IFX):

|     Attività/processo/API                              |     Eventi registrati                                                                                                              | 
|-----------------------------------------------|------------------------------------------------------------------------------------------------------------------------------|
|     Pulizia                                   |     Vengono registrati gli eventi correlati all'avvio, al completamento o all'esito negativo di un processo di pulizia. |                                              
|     Preparare il dispositivo per la spedizione dei clienti    |     Gli eventi correlati all'avvio, al completamento o all'esito negativo del processo di preparazione del dispositivo per la spedizione vengono registrati. |
|     Provisioning                                 |     Vengono registrati gli eventi correlati all'avvio, al completamento o all'errore di un processo di provisioning dei dispositivi.|
|     Processo del pacchetto di controllo                       |     Gli eventi correlati all'avvio, al completamento o all'esito negativo di un processo del pacchetto di controllo che crea una catena di registri di custodia vengono registrati.|
|     Sovrascrittura disco                          |     Errore di sovrascrittura del disco registrato.|
|     Abilitare o disabilitare PowerShell remoto     |     Vengono registrati gli eventi correlati all'abilitazione o alla disabilitazione di PowerShell remoto nel dispositivo. |
|     Ottenere i dettagli della fase di installazione               |     Gli eventi correlati all'installazione del software nel dispositivo in fasi vengono registrati nel Data Center di Azure.|
|     Sbloccare o bloccare il volume di BitLocker           |     Gli eventi vengono registrati per indicare lo stato di BitLocker del volume *basevolume* e *hcsdata* .|
|     Igienizzare il disco                              |     Gli eventi relativi all'errore di dischi fisici che non possono essere cancellati e agli eventi quando tutti i dischi fisici del dispositivo vengono cancellati correttamente, vengono registrati. |
|     Abilitare o disabilitare l'utente locale               |     Vengono registrati gli eventi correlati all'abilitazione o alla disabilitazione degli account utente locali per StorSimpleAdmin e PodSupportAdminUser.| 
|     Reimpostazione della password                          |     Vengono registrati gli eventi correlati alla reimpostazione della password riuscita o non riuscita per l'utente StorSimpleAdmin locale. |


Oltre ai log di controllo di IFX, vengono raccolti anche i log di controllo della catena di custodia per Data Box. Questi log non possono essere visualizzati in tempo reale, ma solo dopo che il processo è stato completato e i dati vengono cancellati dai dischi Data Box. Questi log contengono un subset delle informazioni contenute nei log di controllo di IFX.

Per altre informazioni sulla catena di log di controllo di custodia, vedere [ottenere una catena di log di custodia dopo la cancellazione dei dati](data-box-logs.md#get-chain-of-custody-logs-after-data-erasure).

<!-- write a few lines about order history and link out to the detailed section on order history-->

## <a name="access-audit-logs"></a>Accedere ai log di controllo

Questi log vengono archiviati in Azure e non è possibile accedervi direttamente. Se è necessario accedere a questi log, archiviare un ticket di supporto. Per ulteriori informazioni, vedere [Contattare il supporto tecnico Microsoft](data-box-disk-contact-microsoft-support.md). 

Una volta registrato il ticket di supporto, Microsoft scaricherà e fornirà l'accesso a questi log.


## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [risolvere i problemi relativi alla data box e data box Heavy](data-box-troubleshoot.md).
