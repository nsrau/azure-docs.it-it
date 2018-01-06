---
title: Aggiornamento di Azure Stack 1712 | Documenti Microsoft
description: "Informazioni sulle novità nell'aggiornamento 1712 per Azure Stack integrate di sistemi, i problemi noti e come scaricare l'aggiornamento."
services: azure-stack
documentationcenter: 
author: andredm7
manager: femila
editor: 
ms.assetid: b14f79ad-025f-45d8-9e1d-e53d2b420bb1
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/05/2018
ms.author: andredm
ms.openlocfilehash: 92708909a472f98b45492f3c0c807634f1c204d7
ms.sourcegitcommit: 1d423a8954731b0f318240f2fa0262934ff04bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/05/2018
---
# <a name="azure-stack-1712-update"></a>Aggiornamento dello Stack 1712 Azure

*Si applica a: Azure Stack integrate di sistemi*

In questo articolo vengono descritti i miglioramenti e correzioni in questo pacchetto di aggiornamento noti problemi per questa versione e come scaricare l'aggiornamento. Nota problemi sono divisi in problemi noti relativi al processo di aggiornamento direttamente e problemi noti con la compilazione (post-installazione).

> [!IMPORTANT]
> Questo pacchetto di aggiornamento è applicabile solo ai sistemi Azure Stack integrato. Questo pacchetto di aggiornamento non si applicano al Kit di sviluppo dello Stack di Azure.

## <a name="build-reference"></a>Compilazione di riferimento

È il numero di build di aggiornamento di Azure Stack 1712 **180103.2**.

## <a name="before-you-begin"></a>Prima di iniziare

> [!WARNING]
> Se si è attualmente in una sessione remota di PEP interattiva attiva, è necessario chiudere la sessione prima di avviare il processo di installazione dello Stack di Azure. È possibile digitare **uscire** nella console per chiudere la sessione remota.
> È possibile verificare se è tutte le sessioni remote PEP eseguendo innanzitutto i due comandi di PowerShell, **Get-PSSession** in cui sono elencate tutte delle sessioni remote attive, quindi **Remove-PSSession** terminare e rimuovere le sessioni remote attive. I comandi di PowerShell devono essere eseguita da qualsiasi computer usato per creare sessioni remote PEP nell'ambiente dello Stack di Azure. Di seguito è riportato l'esempio di comando di PowerShell:
```
Get-PSSession | Remove-PSSession
```
Inoltre, non creare una sessione PEP anche dopo l'aggiornamento dello Stack di Azure è stata avviata.

> [!IMPORTANT]
> Non tentare di creare macchine virtuali durante il processo di installazione di aggiornamenti 1712. Vedere [gestire gli aggiornamenti in panoramica di Azure Stack](https://docs.microsoft.com/azure/azure-stack/azure-stack-updates#plan-for-updates) per altri dettagli.

### <a name="prerequisites"></a>Prerequisiti

È necessario installare prima Azure Stack [1711 aggiornare](https://docs.microsoft.com/azure/azure-stack/azure-stack-update-1711) prima di applicare questo aggiornamento.

### <a name="post-update-steps"></a>Passaggi di post-aggiornamento

Questo aggiornamento è necessario inoltre installare gli aggiornamenti del firmware dal partner OEM dopo aver completato l'installazione dell'aggiornamento 1712 dello Stack di Azure.

> [!NOTE]
> Consultare il sito Web partner OEM per scaricare gli aggiornamenti.

### <a name="new-features-and-fixes"></a>Nuove funzionalità e correzioni

Questo aggiornamento include i seguenti miglioramenti e correzioni per lo Stack di Azure.

#### <a name="new-features"></a>Nuove funzionalità

- Nuova esperienza utente durante la creazione di elementi del Marketplace
- Cmdlet test-AzureStack per convalidare Cloud Stack Azure disponibile tramite l'endpoint con privilegi
- Consente di registrare una distribuzione disconnessa dello Stack di Azure
- Avvisi di monitoraggio per la scadenza dell'account utente e certificati
- Cmdlet di aggiornamento BMCCredential aggiunto in PEP per la rotazione di password BMC
- Aggiornamenti di registrazione di rete per supportare la registrazione su richiesta
- Supporta l'operazione di ricreazione immagine per la macchina virtuale scale set (VMSS)
- Abilitare la modalità tutto schermo in VM ERCS per account di accesso CloudAdmin
- I tenant possono attivare le macchine virtuali Windows automaticamente

#### <a name="fixes"></a>Correzioni

- Correzione per visualizzare lo stato operativo del nodo in modalità manutenzione durante l'esecuzione di repair
- Correggere all'indicatore di data e ora record indirizzo IP pubblico utilizzo corretto
- Diverse altre prestazioni, stabilità e sicurezza correzioni
- TimeSource e Defender con privilegi endpoint modulo correzioni di bug

#### <a name="windows-server-2016-new-features-and-fixes"></a>Correzioni e nuove funzionalità di Windows Server 2016

- [Il 3 gennaio - 2018: KB4056890 (Build del sistema operativo 14393.2007)](https://support.microsoft.com/help/4056890/windows-10-update-kb4056890)
    - Questo aggiornamento include le correzioni di software per il problema di sicurezza del settore descritto da [MSRC Security Advisory ADV 180002](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV180002).
 
### <a name="known-issues-with-the-update-process"></a>Problemi noti con il processo di aggiornamento

In questa sezione contiene i problemi noti che possono verificarsi durante l'installazione dell'aggiornamento 1712.

1. **Sintomo:** operatori Stack Azure è possibile che venga visualizzato il seguente errore durante il processo di aggiornamento: *"tipo 'CheckHealth' del ruolo 'VirtualMachines' generato un'eccezione: \n\nVirtual controllo integrità del computer per - ACS01 generati gli errori seguenti. \nThere è verificato un errore durante il recupero di informazioni macchina virtuale dall'host. Dettagli eccezione: \nGet-VM: l'operazione sul computer 'Node03' non è riuscita: servizio di WS-Management non è in grado di elaborare la richiesta. Il \nservice WMI o il provider WMI ha restituito un errore sconosciuto: HRESULT 0x8004106c. "*
    1. **Causa:** questo problema è causato da un problema di Windows Server che deve essere esaminati e risolti successivi aggiornamenti di server di finestra.
    2. **Risoluzione:** contattare il servizio clienti e supporto tecnico per assistenza.
<br><br>
2. **Sintomo:** operatori Stack Azure è possibile che venga visualizzato il seguente errore durante il processo di aggiornamento:*"Abilitazione dell'anello di valore di inizializzazione VM non è riuscita nel nodo Host Node03 con errore: [Host Node03] connessione al server remoto, Host Node03 non riuscita con il messaggio di errore seguente: il client WinRM ha ricevuto lo stato di errore server HTTP (500), ma il servizio remoto non include eventuali altre informazioni sulla causa dell'errore. "*
    1. **Causa:** questo problema è causato da un problema di Windows Server che deve essere esaminati e risolti successivi aggiornamenti di server di finestra. 
    2. **Risoluzione:** contattare il servizio clienti e supporto tecnico per assistenza.
<br><br>

### <a name="known-issues-post-installation"></a>Problemi noti (post-installazione)

In questa sezione contiene i problemi noti di post-installazione con compilazione **180103.2**.

#### <a name="portal"></a>Portale

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
- Rimuovere alcuni elementi del marketplace in questa versione per motivi di compatibilità. Questi saranno nuovamente abilitati dopo la convalida ulteriormente.
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
- Bilanciamento di carico interno (ILB) gestisce correttamente gli indirizzi MAC per macchine virtuali di back-end che interrompe le istanze di Linux.
 
#### <a name="sqlmysql"></a>SQL o MySQL
- È possibile richiedere un'ora prima che i tenant possono creare database in un nuovo SQL o MySQL SKU. 
- Creazione di elementi direttamente nel server che non vengono eseguite dal provider di risorse di hosting MySQL e SQL Server non è supportata e può comportare uno stato non corrispondente.

    > [!NOTE]
    > Non è necessario impatto per SQL o MySQL resource provider utenti esistenti durante l'aggiornamento dei sistemi integrati dello Stack di Azure alla versione 1712. È possibile continuare a utilizzare le corrente SQL o MySQL resource provider compilazioni fino a quando non è disponibile un nuovo aggiornamento dello Stack di Azure.

#### <a name="app-service"></a>Servizio app
- Un utente deve registrare il provider di risorse di archiviazione prima di creare la prima funzione di Azure nella sottoscrizione.

#### <a name="identity"></a>Identità

Ambienti, distribuiti in Azure Active Directory Federation Services (ADFS) di **azurestack\azurestackadmin** account non è più proprietario della sottoscrizione del Provider predefinito. Anziché la registrazione nel **portale di amministrazione / endpoint adminmanagement** con il **azurestack\azurestackadmin**, è possibile utilizzare il **azurestack\cloudadmin** account, questa operazione che è possibile gestire e usare la sottoscrizione di Provider predefinito.

> [!IMPORTANT]
> Anche se il **azurestack\cloudadmin** account è il proprietario della sottoscrizione del Provider predefinito in ambienti di distribuzione di ADFS, non dispone di autorizzazioni a RDP nell'host. Continuare a utilizzare il **azurestack\azurestackadmin** account o l'account amministratore locale per eseguire l'accesso, accedere e gestire l'host in base alle esigenze.

## <a name="download-the-update"></a>Scaricare l'aggiornamento

È possibile scaricare il pacchetto di aggiornamento da Azure Stack 1712 [qui](https://aka.ms/azurestackupdatedownload).

## <a name="see-also"></a>Vedere anche 

- Vedere [gestire gli aggiornamenti in panoramica di Azure Stack](azure-stack-updates.md) per una panoramica del processo di gestione nello Stack di Azure.
- Vedere [applicare gli aggiornamenti in Azure Stack](azure-stack-apply-updates.md) per ulteriori informazioni su come applicare gli aggiornamenti con lo Stack di Azure.
