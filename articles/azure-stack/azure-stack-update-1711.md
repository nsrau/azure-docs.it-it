---
title: Aggiornamento di Azure Stack 1711 | Documenti Microsoft
description: "Informazioni sulle novità nell'aggiornamento 1711 per Azure Stack integrate di sistemi, i problemi noti e come scaricare l'aggiornamento."
services: azure-stack
documentationcenter: 
author: andredm7
manager: femila
editor: 
ms.assetid: 2b66fe05-3655-4f1a-9b30-81bd64ba0013
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2017
ms.author: andredm
ms.openlocfilehash: b9f45462fb108ff9cc9039cdb0d0a9ef318fc218
ms.sourcegitcommit: 7136d06474dd20bb8ef6a821c8d7e31edf3a2820
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/05/2017
---
# <a name="azure-stack-1711-update"></a>Aggiornamento dello Stack 1711 Azure

*Si applica a: Azure Stack integrate di sistemi*

In questo articolo vengono descritti i miglioramenti e correzioni in questo pacchetto di aggiornamento noti problemi per questa versione e come scaricare l'aggiornamento. Nota problemi sono divisi in problemi noti relativi al processo di aggiornamento direttamente e problemi noti con la compilazione (post-installazione).

> [!IMPORTANT]
> Questo pacchetto di aggiornamento è applicabile solo ai sistemi Azure Stack integrato. Questo pacchetto di aggiornamento non si applicano al Kit di sviluppo dello Stack di Azure.

## <a name="build-reference"></a>Compilazione di riferimento

È il numero di build di aggiornamento di Azure Stack 1711 **171201.3**.

## <a name="before-you-begin"></a>Prima di iniziare

### <a name="prerequisites"></a>Prerequisiti

È necessario installare prima Azure Stack [1710 aggiornare](https://docs.microsoft.com/azure/azure-stack/azure-stack-update-1710) prima di applicare questo aggiornamento.

### <a name="new-features-and-fixes"></a>Nuove funzionalità e correzioni

Questo aggiornamento include i seguenti miglioramenti e correzioni per lo Stack di Azure.

#### <a name="new-features"></a>Nuove funzionalità

- Supporto per la diffusione di modelli di soluzioni
- Aggiornamenti nella registrazione di Graph di Azure Stack e la gestione degli errori
- Possibilità di attivare e disattivare la host
- Gli utenti ora possono attivare automaticamente macchine virtuali di Windows
- Endpoint con privilegi aggiunti i cmdlet di PowerShell per recuperare le chiavi di ripristino BitLocker per scopi di memorizzazione
- Supporto per l'aggiornamento delle immagini offline durante l'aggiornamento dell'infrastruttura

#### <a name="fixes"></a>Correzioni

- Condizione di competizione fissa nel sistema DNS durante unità sostituibile sul campo (FRU) e la registrazione cluster anche aggiornato
- Correzione di riavvio-possibilità di disabilitare-host in unità sostituibile sul campo (FRU)
- Diverse altre prestazioni, sicurezza e stabilità correzioni

#### <a name="windows-server-2016-new-features-and-fixes"></a>Correzioni e nuove funzionalità di Windows Server 2016

- [14 novembre 2017: KB4048953 (Build del sistema operativo 14393.1884)](https://support.microsoft.com/help/4048953)
 
### <a name="known-issues-with-the-update-process"></a>Problemi noti con il processo di aggiornamento

In questa sezione contiene i problemi noti che possono verificarsi durante l'installazione dell'aggiornamento 1711.


1. **Sintomo:** operatori Stack Azure è possibile che venga visualizzato il seguente errore durante il processo di aggiornamento: *"nome: installazione di aggiornamento.", "descrizione": "Installare l'aggiornamento su host e macchine virtuali Infra.", "errorMessage": "digitare 'LiveUpdate' del ruolo ' Macchine virtuali ha generato un'eccezione: \n\nThere spazio insufficiente sul disk.\n\nat <ScriptBlock>, <No file>: line22 ","status":"Errore","startTimeUtc":" 2017-11-10T16:46:59.123Z ","endTimeUtc":" 2017-11-10T19:20:29.669Z ","procedura": []"*
    2. **Causa:** questo problema è causato dalla mancanza di spazio libero su disco in uno o più macchine virtuali che fanno parte dell'infrastruttura di Azure Stack
    3. **Risoluzione:** contattare il servizio clienti e supporto tecnico per assistenza.
<br><br>
2. **Sintomo:** operatori Stack Azure è possibile che venga visualizzato il seguente errore durante il processo di aggiornamento:*eccezione "ExtractToFile" durante la chiamata con argomenti "3": "il processo non è possibile accedere al file ' <\\<machineName>-ERCS01\C$ \ Programma Files\WindowsPowerShell\Modules\Microsoft.AzureStack.Diagnostics\Microsoft.AzureStack.Common.Tools.Diagnostics.AzureStackDiagnostics.dll >'*
    1. **Causa:** questo problema si verifica quando la ripresa di un aggiornamento dal portale che in precedenza era ripreso usando un punto finale con privilegi (PEP).
    2. **Risoluzione:** contattare il servizio clienti e supporto tecnico per assistenza.
<br><br>
3. **Sintomo:**operatori Stack Azure è possibile che venga visualizzato il seguente errore durante il processo di aggiornamento:*"tipo 'CheckHealth' del ruolo 'VirtualMachines' ha generato un controllo di integrità macchina di eccezione: \n\nVirtual per <machineName>-ACS01 prodotto di dopo gli errori. \nThere: errore di recupero di informazioni di macchina virtuale dall'host. Dettagli eccezione: \nGet-VM: l'operazione sul computer 'Node03' non è riuscita: servizio di WS-Management non è in grado di elaborare la richiesta. Il \nservice WMI o il provider WMI ha restituito un errore sconosciuto: HRESULT 0x8004106c ".*
    1. **Causa:** questo problema è causato da un problema di Windows Server che deve essere esaminati e risolti successivi aggiornamenti di server di finestra.
    2. **Risoluzione:** contattare il servizio clienti e supporto tecnico per assistenza.
<br><br>
4. **Sintomo:**operatori Stack Azure è possibile che venga visualizzato il seguente errore durante il processo di aggiornamento:*"tipo 'DefenderUpdate' del ruolo 'URP' ha generato un'eccezione: errore di recupero della versione da \\SU1FileServer\SU1_Public\ DefenderUpdates\x64\{nome file} .exe dopo 60 tentativi di copia AzSDefenderFiles, c:\Programmi\Microsoft Files\WindowsPowerShell\Modules\Microsoft.AzureStack.Defender\Microsoft.AzureStack.Defender.psm1: riga 262"*
    1. **Causa:** questo problema è causato da un download in background non riuscita o incompleta di Windows Defender gli aggiornamenti delle definizioni.
    2. **Risoluzione:** tentare di riprendere l'aggiornamento dopo un massimo di 8 ore sono trascorsi provare il primo aggiornamento.

### <a name="known-issues-post-installation"></a>Problemi noti (post-installazione)

In questa sezione contiene i problemi noti di post-installazione con compilazione **20171201.3**.

#### <a name="portal"></a>di Microsoft Azure

- Potrebbe non essere possibile visualizzare le risorse di calcolo o di archiviazione nel portale di amministrazione. Ciò indica che si è verificato un errore durante l'installazione dell'aggiornamento e che l'aggiornamento è stato segnalato in modo non corretto come esito positivo. Se si verifica questo problema, contattare Microsoft CSS per assistenza.
- È possibile visualizzare un dashboard vuoto nel portale. Per ripristinare il dashboard, selezionare l'icona dell'ingranaggio in alto a destra del portale e quindi selezionare **ripristinare le impostazioni predefinite**.
- Quando si visualizzano le proprietà di un gruppo di risorse, il **spostare** pulsante è disabilitato. Questo comportamento è previsto. Spostare i gruppi di risorse tra le sottoscrizioni non è attualmente supportato.
- Per qualsiasi flusso di lavoro in cui si seleziona una sottoscrizione, gruppo di risorse o alla posizione in un elenco a discesa, si verifichi uno o più dei seguenti problemi:

   - È possibile visualizzare una riga vuota nella parte superiore dell'elenco. È comunque possibile selezionare un elemento come previsto.
   - Se l'elenco di elementi nell'elenco a discesa è breve, non sarà possibile visualizzare i nomi degli elementi.
   - Se si dispone di più sottoscrizioni utente, l'elenco di riepilogo a discesa gruppo di risorse può essere vuoto. 

        > [!NOTE]
        > Per risolvere i problemi ultimi due, è possibile digitare il nome della sottoscrizione o del gruppo di risorse (se si conosce) oppure è possibile usare PowerShell.

- Se si elimina utente sottoscrizioni nelle risorse orfane. In alternativa, eliminare prima le risorse utente o l'intero gruppo di risorse e quindi eliminare le sottoscrizioni dell'utente.
- Non è in grado di visualizzare le autorizzazioni per la sottoscrizione utilizzando i portali di Stack di Azure. In alternativa, è possibile verificare le autorizzazioni tramite PowerShell.

#### <a name="health-and-monitoring"></a>Monitoraggio dell'integrità e

- Se si riavvia un'istanza del ruolo di infrastruttura, si potrebbe ricevere un messaggio che indica che il riavvio non riuscito. Tuttavia, il riavvio è effettivamente riuscito.

#### <a name="marketplace"></a>Marketplace
- Quando si tenta di aggiungere elementi a marketplace Stack Azure utilizzando il **Aggiungi da Azure** opzione, non tutti gli elementi potrebbero essere visibili per il download.
- Gli utenti sfogliare il marketplace completo senza una sottoscrizione e di visualizzare gli elementi di amministrazione come piani e alle offerte. Questi elementi sono non funzionale agli utenti.

#### <a name="compute"></a>Calcolo
- Gli utenti assegnati l'opzione per creare una macchina virtuale con l'archiviazione con ridondanza geografica. Questa configurazione causa l'errore durante la creazione macchina virtuale.
- È possibile configurare una macchina virtuale set di disponibilità solo con un dominio di errore di uno e un dominio di aggiornamento di uno.
- Non sussiste alcuna esperienza di marketplace per creare il set di scalabilità di macchine virtuali. È possibile creare un set, utilizzando un modello di scalabilità.
- Impostazioni di scalabilità per il set di scalabilità di macchine virtuali non sono disponibili nel portale. In alternativa, è possibile utilizzare [Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set). A causa delle differenze di versione di PowerShell, è necessario utilizzare il `-Name` parametro anziché `-VMScaleSetName`.
 
#### <a name="networking"></a>Rete
- Tramite il portale, è possibile creare un servizio di bilanciamento del carico con un indirizzo IP pubblico. In alternativa, è possibile utilizzare PowerShell per creare il servizio di bilanciamento del carico.
- Quando si crea un servizio di bilanciamento del carico di rete, è necessario creare una regola di translation (NAT) indirizzo di rete. In caso contrario, si riceverà un errore quando si tenta di aggiungere una regola NAT, dopo aver creato il bilanciamento del carico.
- Dopo aver creata e associata a tale indirizzo IP della macchina virtuale è non è possibile eliminare l'associazione di un indirizzo IP pubblico da una macchina virtuale (VM). Disassociazione verrà visualizzata a funzionare, ma l'indirizzo IP pubblico assegnato in precedenza rimane associato con la macchina virtuale originale. Questo comportamento si verifica anche se si riassegna l'indirizzo IP per una nuova macchina virtuale (conosciuta come un *scambio VIP*). Tutti i successivi tentativi di connessione attraverso il risultato di indirizzi IP in una connessione alla macchina virtuale di origine associati e non a quello nuovo. Attualmente, è necessario utilizzare solo i nuovi indirizzi IP pubblici per la creazione della nuova macchina virtuale.
- Gli operatori di Azure Stack potrebbero non essere possibile distribuire, eliminare, modificare le reti virtuali o i gruppi di sicurezza di rete. Questo problema si verifica principalmente sui tentativi di aggiornamento successive dello stesso pacchetto. Ciò è causato da un problema di creazione di pacchetti con un aggiornamento che è attualmente in corso il controllo.
 
#### <a name="sqlmysql"></a>SQL o MySQL
- È possibile richiedere un'ora prima che i tenant possono creare database in un nuovo SQL o MySQL SKU. 
- Creazione di elementi direttamente nel server che non vengono eseguite dal provider di risorse di hosting MySQL e SQL Server non è supportata e può comportare uno stato non corrispondente.
 
#### <a name="app-service"></a>Servizio app
- Un utente deve registrare il provider di risorse di archiviazione prima di creare la prima funzione di Azure nella sottoscrizione.

#### <a name="identity"></a>Identità

Ambienti, distribuiti in Azure Active Directory Federation Services (ADFS) di **azurestack\azurestackadmin** account non è più proprietario della sottoscrizione del Provider predefinito. Anziché la registrazione nel **portale di amministrazione / endpoint adminmanagement** con il **azurestack\azurestackadmin**, è possibile utilizzare il **azurestack\cloudadmin** account, questa operazione che è possibile gestire e usare la sottoscrizione di Provider predefinito.

> [!IMPORTANT]
> Anche se il **azurestack\cloudadmin** account è il proprietario della sottoscrizione del Provider predefinito in ambienti di distribuzione di ADFS, non dispone di autorizzazioni a RDP nell'host. Continuare a utilizzare il **azurestack\azurestackadmin** account o l'account amministratore locale per eseguire l'accesso, accedere e gestire l'host in base alle esigenze.

## <a name="download-the-update"></a>Scaricare l'aggiornamento

È possibile scaricare il pacchetto di aggiornamento da Azure Stack 1711 [qui](https://aka.ms/azurestackupdatedownload).

## <a name="more-information"></a>Altre informazioni

Microsoft ha fornito un modo per monitorare e riprendere gli aggiornamenti utilizzando i privilegi punto finale (PEP) installati con aggiornamento 1711.

- Vedere il [monitorare gli aggiornamenti nello Stack di Azure utilizzando la documentazione di endpoint con privilegi](https://docs.microsoft.com/azure/azure-stack/azure-stack-monitor-update). 

## <a name="see-also"></a>Vedere anche

- Vedere [gestire gli aggiornamenti in panoramica di Azure Stack](azure-stack-updates.md) per una panoramica del processo di gestione nello Stack di Azure.
- Vedere [applicare gli aggiornamenti in Azure Stack](azure-stack-apply-updates.md) per ulteriori informazioni su come applicare gli aggiornamenti con lo Stack di Azure.
