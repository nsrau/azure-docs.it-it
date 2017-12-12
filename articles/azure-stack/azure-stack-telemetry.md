---
title: Dati di telemetria di Azure Stack | Documenti Microsoft
description: Viene descritto come configurare le impostazioni di telemetria dello Stack di Azure tramite PowerShell.
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 12/08/2017
ms.author: jeffgilb
ms.reviewer: comartin
ms.openlocfilehash: 5cd8d4045764b753c5fdd81ade98d69c72709881
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/11/2017
---
# <a name="azure-stack-telemetry"></a>Dati di telemetria di Azure Stack

*Si applica a: Azure Stack integrate di sistemi Azure Stack Development Kit*

Dati di sistema Azure Stack o dati di telemetria, viene caricata automaticamente a Microsoft tramite l'esperienza dell'utente connesso. Dati raccolti dai dati di telemetria dello Stack di Azure vengono utilizzati dal team Microsoft principalmente per migliorare l'esperienza dei clienti e per l'analisi di sicurezza, integrità, la qualità e prestazioni.

Come operatore di Stack di Azure, dati di telemetria può fornire informazioni dettagliate preziose sulle distribuzioni aziendali e offre una voce che consente di versioni future di forma dello Stack di Azure.

> [!NOTE]
> Stack di Azure può anche essere configurato per informazioni sull'utilizzo di inoltro a Azure per la fatturazione. Ciò è necessario per i clienti a più nodi Azure Stack scegliere fatturazione retribuzione come-di-uso. Report sull'utilizzo viene controllato in modo indipendente da dati di telemetria e non è necessaria per i clienti a più nodi che scegliere il modello di capacità o per gli utenti di Azure Stack Development Kit. Per questi scenari, report di utilizzo può essere disattivata [utilizzando lo script di registrazione](https://docs.microsoft.com/azure/azure-stack/azure-stack-usage-reporting). 

Telemetria Stack Azure si basa sul componente esperienza per utente connesso di Windows Server 2016 e dati di telemetria, che utilizza il [traccia eventi per Windows (ETW)](https://msdn.microsoft.com/library/dn904632(v=vs.85).aspx) tecnologia di registrazione per raccogliere e archiviare i dati e gli eventi di telemetria di traccia. Azure componenti dello Stack utilizzano la stessa tecnologia di registrazione per la pubblicazione di eventi e i dati raccolti tramite la registrazione degli eventi di sistema operativo pubblico e la traccia API. Il Provider di risorse di rete, il Provider di risorse di archiviazione, Provider di risorse di monitoraggio e aggiornamento Provider di risorse sono esempi di componenti dello Stack di Azure. Il componente esperienza dell'utente connesso e i dati di telemetria crittografa i dati utilizzando SSL e utilizza il blocco di certificato per la trasmissione di dati di telemetria su HTTPS per il servizio di gestione dei dati di Microsoft.

> [!NOTE]
> Per supportare il flusso di dati di telemetria, la porta 443 (HTTPS) deve essere aperta nella rete. Il componente esperienza dell'utente connesso e i dati di telemetria si connette al servizio di gestione di dati di Microsoft in https://v10.vortex-win.data.microsoft.com. Il componente esperienza dell'utente connesso e i dati di telemetria è collegato a https://settings-win.data.microsoft.com per scaricare le informazioni di configurazione.

## <a name="privacy-considerations"></a>Considerazioni sulla privacy
Il servizio ETW instrada i dati di telemetria archiviazione cloud protetto. Il principio dei privilegiato minimi accesso guide ai dati di telemetria. Solo il personale di Microsoft con aziendali valido, è necessario sono consentito l'accesso ai dati di telemetria. Microsoft non condivide dati personali, i clienti di terze parti, ad eccezione di a discrezione del cliente o per gli scopi limitati descritto nel [informativa sulla Privacy di Azure Stack](http://windows.microsoft.com/windows/preview-privacy-statement). Si condividono i report di business con gli OEM e i partner che includono informazioni di telemetria anonimi, aggregati. Le decisioni di condivisione dei dati vengono effettuati da un team di Microsoft interno, tra cui le parti interessate di gestione dati, legali e privacy.

Microsoft crede nella e consigliate minimizzazione di informazioni. Si impegna a raccogliere solo le informazioni che è necessario perché viene archiviata per solo fino a quando è necessario fornire un servizio o per l'analisi. La maggior parte delle informazioni sulla modalità di funzionamento del sistema di Stack di Azure e Azure services viene eliminata all'interno di sei mesi. Riepilogare o dati aggregati verranno conservati per un periodo più lungo.

Siamo consapevoli che è molto importante garantire la privacy e protezione delle informazioni dei clienti.  È stato reindirizzato l'approccio attento e completo per la privacy dei clienti e la protezione dei dati dei clienti con lo Stack di Azure. Gli amministratori IT sono controlli per personalizzare le funzionalità e le impostazioni di privacy in qualsiasi momento. Impegno di trasparenza e l'attendibilità è ovvia:
- Ci sono aperti con i clienti sui tipi di dati che è raccogliere.
- I clienti aziendali è stato inserito nel controllo, è possibile personalizzare le proprie impostazioni di privacy.
- È inserito prima della protezione e privacy dei clienti.
- Siamo trasparente sulla modalità di utilizzo dati di telemetria ottiene.
- Utilizziamo telemetria per migliorare le esperienze degli utenti.

Microsoft non si intende raccogliere informazioni riservate, ad esempio numeri di carta di credito, nomi utente e password, indirizzi di posta elettronica o altre informazioni riservate in modo analogo. Se si determina che le informazioni riservate sono state ricevute inavvertitamente, vengono eliminati.

## <a name="examples-of-how-microsoft-uses-the-telemetry-data"></a>Esempi di come Microsoft utilizza i dati di telemetria
Dati di telemetria svolge un ruolo importante che ci aiuta a identificare rapidamente e risolvere i problemi critici per l'affidabilità in distribuzioni dei clienti e le configurazioni. Informazioni dettagliate sui dati di telemetria è raccogliere consentono a identificare rapidamente i problemi con i servizi o configurazioni hardware. Possibilità di Microsoft per ottenere dati dai clienti e apportare miglioramenti nell'ecosistema consente di aumentare gli standard per la qualità delle soluzioni di Azure Stack integrate. 

Dati di telemetria consentono inoltre a Microsoft per meglio comprendere come distribuire i componenti, utilizzare le funzionalità e utilizzare i servizi per raggiungere gli obiettivi di business. Ottenere informazioni dettagliate dai dati di che consente di definire le priorità degli investimenti engineering in aree che possono influire direttamente sulle esperienze dei clienti e carichi di lavoro.

Ecco alcuni esempi di utilizzo al cliente di contenitori, archiviazione e le configurazioni di rete che sono associate ai ruoli Azure Stack. È inoltre possibile utilizzare le informazioni di business intelligence in alcuni dei nostri gestione e monitoraggio di soluzioni e i miglioramenti di unità.  In questo modo i clienti per diagnosticare i problemi di qualità e risparmiare denaro, rendendo meno supporto chiama a Microsoft.

## <a name="manage-telemetry-collection"></a>Gestire raccolta dati di telemetria
Non è consigliabile disattivare la telemetria dell'organizzazione come dati di telemetria fornisce i dati di stabilità e funzionalità migliorate del prodotto. È possibile riconoscere, tuttavia, in alcuni scenari potrebbe essere necessario. 

In questi casi, è possibile configurare il livello di dati di telemetria inviato a Microsoft usando la pre-distribuzione delle impostazioni del Registro di sistema o di post-distribuzione di dati di telemetria relativi endpoint.

### <a name="asdk-set-telemetry-level-in-the-windows-registry"></a>ASDK: impostare il livello di dati di telemetria del Registro di sistema
L'Editor del Registro di sistema di Windows viene utilizzato per impostare manualmente il livello di dati di telemetria nel computer host fisico prima di distribuire Azure Stack. Se un criterio di gestione già esistente, ad esempio criteri di gruppo, esso sostituirà l'impostazione del Registro di sistema. 

Prima di distribuire Azure Stack nell'host di kit di sviluppo, avviare il CloudBuilder.vhdx ed eseguire lo script seguente in una finestra di PowerShell con privilegi elevata:

```powershell
### Get current AllowTelmetry value on DVM Host
(Get-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\DataCollection" `
-Name AllowTelemetry).AllowTelemetry
### Set & Get updated AllowTelemetry value for ASDK-Host 
Set-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\DataCollection" `
-Name "AllowTelemetry" -Value '0' # Set this value to 0,1,2,or3.  
(Get-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\DataCollection" `
-Name AllowTelemetry).AllowTelemetry
```

I livelli dati di telemetria sono cumulativi e suddiviso in categorie in quattro livelli (0-3):

**0 (sicurezza)**. Solo dati di sicurezza. Proteggere informazioni necessarie per mantenere il sistema operativo, inclusi i dati sull'esperienza dell'utente connesso e i dati di telemetria impostazioni del componente e Windows Defender. A questo livello, non viene generato alcun telemetria specifico dello Stack di Azure.

**1 (base)**. Protezione dati e dello stato di base e la qualità dei dati. Informazioni di base dei dispositivi, tra cui: i dati relativi alla qualità, compatibilità delle applicazioni, dati di utilizzo dell'app e dati dal livello di sicurezza. Impostando il livello di dati di telemetria su dati di telemetria dello Stack di Azure consente di base. I dati raccolti a questo livello includono:

- **Informazioni sul dispositivo** che consente di fornire informazioni sui tipi e le configurazioni di istanze di Windows Server 2016 native che virtualizzate nell'ecosistema, tra cui:
 - Gli attributi di computer, ad esempio OEM, modello, 
 - Attributi di rete, ad esempio il numero e velocità delle schede di rete,
 - Processore e gli attributi di memoria, ad esempio il numero di core, dimensioni della memoria, 
 - Attributi di archiviazione, ad esempio il numero di unità, tipo e dimensioni.
- **Funzionalità di telemetria**, tra cui percentuale di eventi caricati, eventi eliminati e l'ultimo caricamento ora.
- **Informazioni relative alla qualità** che consente a Microsoft di sviluppare una conoscenza di base della modalità di esecuzione di Stack di Azure. Un esempio è il numero di avvisi critici in una configurazione hardware specifica.
- **Dati di compatibilità** che fornisce informazioni sui provider di risorse vengono installati in un sistema e di una macchina virtuale e identifica i potenziali problemi di compatibilità.

**2 (avanzato)**. Informazioni aggiuntive, tra cui: utilizzo del sistema operativo e altri servizi Azure Stack, valutarne, i dati sull'affidabilità avanzate e dati dai livelli di sicurezza e di base. 

**3 (full)**. Tutti i dati necessari per identificare e sono utili per risolvere i problemi, oltre ai dati dal **sicurezza**, **base**, e **avanzato** livelli.

> [!NOTE]
> Il valore di livello dati di telemetria predefinito è 2 (avanzata).

Si noti che la disattivazione di telemetria di Windows e Azure Stack disabiliterà anche telemetria di SQL. Per ulteriori informazioni sulle implicazioni di dati di telemetria di Windows Server, impostazioni, consultare il [white paper telemetria Windows](https://aka.ms/winservtelemetry). 

> [!IMPORTANT]
> Questi livelli di dati di telemetria vengono applicate solo ai componenti dello Stack di Microsoft Azure. I componenti software non Microsoft e i servizi in esecuzione nell'Host del ciclo di vita di Hardware da partner hardware dello Stack di Azure possono comunicare con i servizi cloud di fuori di questi livelli di dati di telemetria. È necessario collaborare con il provider di soluzione hardware di Azure Stack per comprendere i criteri di telemetria e come è possibile partecipare o rifiutare esplicitamente. 

### <a name="asdk-and-multinode-enable-or-disable-telemetry-after-deployment"></a>ASDK e MultiNode: abilitare o disabilitare i dati di telemetria dopo la distribuzione

Per abilitare o disabilitare i dati di telemetria dopo la distribuzione, è necessario disporre di accesso per il punto finale con privilegi (PEP) che viene esposto in macchine virtuali di ERCS.
1.  Per abilitare:`Set-Telemetry -Enable`
2.  Per disabilitare:`Set-Telemetry -Disable`

Dettaglio di parametro: 
> . PARAMETRO Enable - Attiva il caricamento di dati di telemetria 

> . PARAMETRO Disable - disattiva il caricamento di dati di telemetria  

**Script per abilitare i dati di telemetria:**
```powershell
$ip = "<IP ADDRESS OF THE PEP VM>" # You can also use the machine name instead of IP here.
$pwd= ConvertTo-SecureString "<CLOUD ADMIN PASSWORD>" -AsPlainText -Force
$cred = New-Object System.Management.Automation.PSCredential ("<DOMAIN NAME>\CloudAdmin", $pwd)
$psSession = New-PSSession -ComputerName $ip -ConfigurationName PrivilegedEndpoint -Credential $cred
Invoke-Command -Session $psSession {Set-Telemetry -Enable}
if($psSession)
{
    Remove-PSSession $psSession
}
```

**Script per disattivare i dati di telemetria:**
```powershell
$ip = "<IP ADDRESS OF THE PEP VM>" # You can also use the machine name instead of IP here.
$pwd= ConvertTo-SecureString "<CLOUD ADMIN PASSWORD>" -AsPlainText -Force
$cred = New-Object System.Management.Automation.PSCredential ("<DOMAIN NAME>\CloudAdmin", $pwd)
$psSession = New-PSSession -ComputerName $ip -ConfigurationName PrivilegedEndpoint -Credential $cred
Invoke-Command -Session $psSession {Set-Telemetry -Disable}
if($psSession)
{
    Remove-PSSession $psSession
}
```

## <a name="next-steps"></a>Passaggi successivi
[Scaricare il pacchetto di distribuzione kit di sviluppo di Azure Stack](https://azure.microsoft.com/overview/azure-stack/try/?v=try)

[Distribuire il kit di sviluppo di Azure Stack](azure-stack-run-powershell-script.md)

