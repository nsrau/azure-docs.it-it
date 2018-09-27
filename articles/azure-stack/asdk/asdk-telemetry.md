---
title: I dati di telemetria di Azure Stack | Microsoft Docs
description: Viene descritto come configurare le impostazioni di telemetria di Azure Stack usando PowerShell.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/17/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: e50cb33acc95567a24afb19f88f88a3f586e0124
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/26/2018
ms.locfileid: "47224755"
---
# <a name="azure-stack-telemetry"></a>Dati di telemetria di Azure Stack

I dati di sistema Azure Stack o dati di telemetria, viene caricata automaticamente a Microsoft tramite l'esperienza dell'utente connesso. I dati raccolti dai dati di telemetria di Azure Stack sono usati dai team Microsoft principalmente per migliorare le nostre esperienze dei clienti e per l'analisi di sicurezza, dell'integrità, qualità e prestazioni.

Un operatore di Azure Stack, i dati di telemetria possono fornire informazioni utili nelle distribuzioni aziendali e offre una voce che aiuta le versioni future di forma di Azure Stack.

> [!NOTE]
> Azure Stack può anche essere configurato per le informazioni sull'utilizzo di inoltro di Azure per la fatturazione. Ciò è necessario per i clienti a più nodi Azure Stack che scegliere come si-pagamento in fatturazione. Report sull'utilizzo viene controllato in modo indipendente dai dati di telemetria e non è necessaria per i clienti a nodi multipli che scegliere il modello di capacità o per gli utenti di Azure Stack Development Kit. Per questi scenari, segnalazione utilizzo può essere disattivata [usando lo script di registrazione](https://docs.microsoft.com/azure/azure-stack/azure-stack-usage-reporting).

I dati di telemetria di Azure Stack si basa sul componente esperienza per utente connesso di Windows Server 2016 e i dati di telemetria, che usa il [Event Tracing for Windows (ETW)](https://msdn.microsoft.com/library/dn904632(v=vs.85).aspx) tecnologia di registrazione per raccogliere e archiviare gli eventi di telemetria e dati di traccia. I componenti di Azure Stack utilizzano la stessa tecnologia di registrazione per pubblicare eventi e i dati vengono raccolti mediante la registrazione degli eventi di sistema operativo pubblico e la traccia API. Provider di risorse di rete, il Provider di risorse di archiviazione, Provider di risorse di monitoraggio e aggiornamento del Provider di risorse sono esempi di componenti di Azure Stack. Il componente esperienze utente connesse e telemetria crittografa i dati tramite SSL e Usa l'associazione del certificato per trasmettere i dati di telemetria tramite HTTPS al servizio di gestione dati Microsoft.

> [!NOTE]
> Per supportare il flusso di dati di telemetria, la porta 443 (HTTPS) deve essere aperta in rete. Il componente di esperienze utente connesse e telemetria si connette al servizio di gestione dati di Microsoft a https://v10.vortex-win.data.microsoft.com. Il componente esperienze utente connesse e telemetria si connette inoltre a https://settings-win.data.microsoft.com per scaricare le informazioni di configurazione.

## <a name="privacy-considerations"></a>Considerazioni sulla privacy
Il servizio ETW instrada i dati di telemetria all'archiviazione cloud protetto. Il principio dei privilegiato minimi accesso Guide per i dati di telemetria. Solo il personale di Microsoft con un'esigenza aziendale valida vengono concesso di accedere ai dati di telemetria. Microsoft non condivide i dati personali dei clienti con terze parti, ad eccezione a discrezione del cliente o per le finalità limitate descritte nel [informativa sulla Privacy di Azure Stack](https://privacy.microsoft.com/PrivacyStatement). Report di business è condividere con gli OEM e i partner che includono le informazioni di telemetria aggregati, reso anonimo. Decisioni di condivisione dei dati vengono effettuati da un team interno di Microsoft tra cui gli stakeholder di gestione dati, legali e privacy.

Microsoft ritiene e procedure consigliate di minimizzazione di informazioni. Microsoft si impegna raccogliere solo le informazioni che è necessario perché viene archiviata solo finché è necessaria per fornire un servizio o per l'analisi. Molte delle informazioni sulle modalità di funzionamento tra il sistema Azure Stack e i servizi di Azure verrà eliminato entro sei mesi. Riepilogare o dati aggregati vengono conservati per un periodo più lungo.

Siamo consapevoli che la privacy e sicurezza delle informazioni dei clienti è importante. Sono state prese un approccio ponderato e completa per la privacy dei clienti e la protezione dei dati dei clienti con Azure Stack. Gli amministratori IT dispongono di controlli per personalizzare le funzionalità e le impostazioni di privacy in qualsiasi momento. Il nostro impegno per trasparenza e la relazione di trust è chiaro:
- Siamo aperti con i clienti sui tipi di dati che vengono raccolte.
- I clienti aziendali è inclusa nel controllo, è possibile personalizzare le proprie impostazioni di privacy.
- Viene inserito prima della protezione e privacy dei clienti.
- Siamo trasparente sull'utilizzo Ottiene i dati di telemetria.
- Usiamo i dati di telemetria per migliorare le esperienze dei clienti.

Microsoft non si intende raccogliere informazioni riservate, ad esempio numeri di carta di credito, nomi utente e password, indirizzi di posta elettronica o altre informazioni riservate in modo analogo. Se si determina che le informazioni riservate sono state ricevute inavvertitamente, vengono eliminati.

## <a name="examples-of-how-microsoft-uses-the-telemetry-data"></a>Esempi di come Microsoft Usa i dati di telemetria
I dati di telemetria svolge un ruolo importante ci aiuta a identificare rapidamente e risolvere i problemi critici per l'affidabilità in distribuzioni e configurazioni dei clienti. Informazioni dettagliate sui dati di telemetria vengono raccolte consentono a identificare rapidamente i problemi con i servizi o configurazioni hardware. Possibilità di Microsoft per ottenere questi dati dai clienti e apportare miglioramenti nell'ecosistema di consente di aumentare il livello per la qualità dei nostri soluzioni integrate di Azure Stack.

I dati di telemetria consentono inoltre a Microsoft per meglio comprendere come i clienti distribuiscono componenti, usare le funzionalità e usare i servizi per raggiungere i propri obiettivi aziendali. Ottenere informazioni dettagliate da tali dati consente di definire la priorità degli investimenti nella progettazione nelle aree che possono influire direttamente sulle esperienze dei clienti e i carichi di lavoro.

Alcuni esempi di utilizzo del cliente di contenitori, archiviazione e le configurazioni di rete che sono associate i ruoli di Azure Stack. È anche possibile usare le informazioni dettagliate per apportare miglioramenti e intelligence per alcuni dei nostri gestione e monitoraggio delle soluzioni. In questo modo i clienti per diagnosticare i problemi di qualità e risparmiare denaro, rendendo meno supporto le chiamate a Microsoft.

## <a name="manage-telemetry-collection"></a>Gestire raccolta dati di telemetria
Non è consigliabile disattivare i dati di telemetria all'interno dell'organizzazione come dati di telemetria fornisce i dati che determina le funzionalità migliorate del prodotto e la stabilità. Tuttavia, riconosciamo che in alcuni scenari questo potrebbe essere necessario.

In questi casi, è possibile configurare il livello di telemetria inviato a Microsoft tramite la pre-distribuzione delle impostazioni del Registro di sistema o tramite l'endpoint di dati di telemetria dopo la distribuzione.

### <a name="set-telemetry-level-in-the-windows-registry"></a>Livello del set di dati di telemetria nel Registro di sistema Windows
L'Editor del Registro di sistema di Windows viene utilizzato per impostare manualmente il livello di telemetria nel computer host fisico prima di distribuire Azure Stack. Se un criterio di gestione esiste già, ad esempio criteri di gruppo, viene eseguito l'override di questa impostazione del Registro di sistema.

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

I livelli di dati di telemetria sono cumulativi e categorizzate in quattro livelli (0-3):

**0 (sicurezza)**. Solo i dati di sicurezza. Proteggere le informazioni necessarie per mantenere il sistema operativo, inclusi i dati sulle esperienze utente connesse e telemetria impostazioni del componente e Windows Defender. Nessuna telemetria specifici di Azure Stack viene generata questo livello.

**1 (base)**. I dati di sicurezza e integrità di base e la qualità dei dati. Informazioni di base del dispositivo, tra cui: dati relativi alla qualità, compatibilità delle app, i dati sull'utilizzo delle app e i dati dal livello di sicurezza. Impostazione del livello dati di telemetria per la telemetria di Azure Stack consente di base. I dati raccolti a questo livello includono:

- **Informazioni di base del dispositivo** che aiuta a garantire una conoscenza sui tipi e le configurazioni di istanze di Windows Server 2016 native che virtualizzate nell'ecosistema, tra cui:
  - Attributi di computer, ad esempio OEM, modellare,
  - Attributi di rete, ad esempio il numero e la velocità delle schede di rete,
  - Processore e gli attributi di memoria, ad esempio il numero di core, dimensioni della memoria,
  - Attributi di archiviazione, ad esempio il numero di unità, tipo e dimensione.
- **La funzionalità di telemetria**, tra cui percentuale di eventi caricati, eventi eliminati e l'ultimo tempo di caricamento.
- **Informazioni relative alla qualità** che consentono a Microsoft di sviluppare una conoscenza di base delle prestazioni di Azure Stack. Un esempio è il numero di avvisi critici in una configurazione hardware specifica.
- **I dati di compatibilità**, che consente di forniscono una conoscenza intorno al quale i provider di risorse installati in un sistema e macchine virtuali e identifica i potenziali problemi di compatibilità.

**2 (avanzato)**. Raccolta di informazioni aggiuntive, tra cui: come vengono usati il sistema operativo e altri servizi di Azure Stack, prestazioni, affidabilità avanzata dei dati e dati dai livelli Basic e sicurezza.

**3 (completo)**. Tutti i dati necessari per identificare e contribuire alla risoluzione dei problemi, oltre ai dati dal **sicurezza**, **base**, e **avanzato** livelli.

> [!NOTE]
> Il valore del livello dati di telemetria predefinito è 2 (avanzato).

Disattivazione della telemetria di Windows e Azure Stack consente di disattivare i dati di telemetria SQL. Per altre informazioni sulle implicazioni delle impostazioni di telemetria di Windows Server, fare riferimento il [white paper sulla telemetria di Windows](https://aka.ms/winservtelemetry).

> [!IMPORTANT]
> Questi livelli di dati di telemetria si applicano solo ai componenti di Microsoft Azure Stack. I componenti software non Microsoft e i servizi in esecuzione nell'Host del ciclo di vita dell'Hardware dai partner hardware di Azure Stack possono comunicare con i propri servizi cloud di fuori di questi livelli di dati di telemetria. È necessario collaborare con il provider di soluzioni hardware Azure Stack per comprendere i criteri di dati di telemetria e il modo in cui è possibile acconsentire o rifiutare esplicitamente.

### <a name="enable-or-disable-telemetry-after-deployment"></a>Abilitare o disabilitare i dati di telemetria dopo la distribuzione

Per abilitare o disabilitare la telemetria dopo la distribuzione, è necessario avere accesso per il punto finale con privilegi (PEP) che viene esposta nelle macchine virtuali ERCS.
1.  Per abilitare: `Set-Telemetry -Enable`
2.  Per disabilitare: `Set-Telemetry -Disable`

Dettaglio di parametro:
> . PARAMETRO attiva - attiva il caricamento di dati di telemetria

> . PARAMETRO Disable - disattivare il caricamento di dati di telemetria  

**Script per abilitare la telemetria:**
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

**Script per disabilitare la telemetria:**
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
[Aggiungere un elemento del marketplace](asdk-marketplace-item.md)
