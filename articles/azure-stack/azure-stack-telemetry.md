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
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: jeffgilb
ms.reviewer: comartin
ms.lastreviewed: 10/15/2018
ms.openlocfilehash: 1977ced65b6dd62a023a79ce8949a8b428d2f965
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57760359"
---
# <a name="azure-stack-telemetry"></a>Dati di telemetria di Azure Stack

*Si applica a: Azure Stack Development Kit e i sistemi integrati di Azure Stack*

I dati di telemetria di Azure Stack carica automaticamente i dati di sistema di Microsoft tramite l'esperienza dell'utente connesso. I team Microsoft usano i dati che consente di raccogliere i dati di telemetria di Azure Stack per migliorare le esperienze dei clienti. Questi dati vengono utilizzati anche per la sicurezza, l'integrità, qualità e analisi delle prestazioni.

Per un operatore di Azure Stack, i dati di telemetria possono fornire informazioni utili nelle distribuzioni aziendali e offre una voce che aiuta le versioni future di forma di Azure Stack.

> [!NOTE]
> È anche possibile configurare Azure Stack per inoltrare le informazioni sull'utilizzo di Azure per la fatturazione. Ciò è necessario per i clienti a più nodi Azure Stack che scegliere come si-pagamento in fatturazione. Report sull'utilizzo viene controllato in modo indipendente dai dati di telemetria e non è necessaria per i clienti a nodi multipli che scegliere il modello di capacità o per gli utenti di Azure Stack Development Kit. Per questi scenari, segnalazione utilizzo può essere disattivata [usando lo script di registrazione](https://docs.microsoft.com/azure/azure-stack/azure-stack-usage-reporting).

I dati di telemetria di Azure Stack si basa sul componente esperienza per utente connesso di Windows Server 2016 e i dati di telemetria, che usa il [Event Tracing for Windows (ETW)](https://msdn.microsoft.com/library/dn904632(v=vs.85).aspx) tecnologia TraceLogging per raccogliere e archiviare gli eventi e dati. I componenti di Azure Stack utilizzano la stessa tecnologia per pubblicare eventi e i dati raccolti tramite la registrazione degli eventi di sistema operativo pubblico e la traccia API. Questi provider sono esempi di questi componenti di Azure Stack: Risorsa, risorsa di archiviazione, il monitoraggio risorse di rete e aggiornare la risorsa. Il componente esperienze utente connesse e telemetria crittografa i dati tramite SSL e Usa l'associazione del certificato per trasmettere dati tramite HTTPS al servizio di gestione dati Microsoft.

> [!IMPORTANT]
> Per abilitare il flusso di dati di telemetria, la porta 443 (HTTPS) deve essere aperta in rete. Il componente di esperienze utente connesse e telemetria si connette al servizio di gestione dati di Microsoft a https://v10.vortex-win.data.microsoft.com. Il componente esperienze utente connesse e telemetria si connette inoltre a https://settings-win.data.microsoft.com per scaricare le informazioni di configurazione.

## <a name="privacy-considerations"></a>Considerazioni sulla privacy

Il servizio ETW instrada i dati di telemetria all'archiviazione cloud protetto. Il principio del privilegio minimo Guida l'accesso ai dati di telemetria. Solo il personale di Microsoft con un'esigenza aziendale valida vengano concesso l'accesso ai dati di telemetria. Microsoft non condivide i dati personali dei clienti con terze parti, ad eccezione a discrezione del cliente o per le finalità limitate descritte nel [informativa sulla Privacy Microsoft](https://privacy.microsoft.com/PrivacyStatement). Report di business che vengono condivise con gli OEM e i partner includono i dati aggregati, reso anonimi. Decisioni di condivisione dei dati vengono effettuati da un team interno di Microsoft tra cui gli stakeholder di gestione dati, legali e privacy.

Microsoft ritiene e procedure consigliate di minimizzazione di informazioni. Microsoft si impegna raccogliere solo le informazioni necessarie e archiviano per solo il tempo necessario per fornire un servizio o per l'analisi. Molte delle informazioni sulle modalità di funzionamento tra il sistema Azure Stack e i servizi di Azure verrà eliminato entro sei mesi. Riepilogare o dati aggregati verranno conservati per un periodo più lungo.

Siamo consapevoli che la privacy e sicurezza delle informazioni dei clienti è importante.  Microsoft adotta un approccio ponderato e completa per la privacy dei clienti e la protezione dei dati dei clienti in Azure Stack. Gli amministratori IT dispongono di controlli per personalizzare le funzionalità e le impostazioni di privacy in qualsiasi momento. Il nostro impegno per trasparenza e la relazione di trust è chiaro:

- Siamo aperti con i clienti sui tipi di dati che vengono raccolte.
- I clienti aziendali è inclusa nel controllo, è possibile personalizzare le proprie impostazioni di privacy.
- Mettiamo privacy e sicurezza al primo posto.
- Siamo trasparente sull'utilizzo Ottiene i dati di telemetria.
- Usiamo i dati di telemetria per migliorare le esperienze dei clienti.

Microsoft non intende raccogliere i dati sensibili, ad esempio numeri di carta di credito, nomi utente e password, indirizzi di posta elettronica o informazioni riservate simile. Se si determina che le informazioni riservate sono state ricevute inavvertitamente, vengono eliminati.

## <a name="examples-of-how-microsoft-uses-the-telemetry-data"></a>Esempi di come Microsoft Usa i dati di telemetria

I dati di telemetria svolge un ruolo importante nel supporto per identificare rapidamente e risolvere i problemi critici per l'affidabilità in configurazioni e distribuzioni dei clienti. Informazioni dettagliate dai dati di telemetria consentono di identificare i problemi con i servizi o configurazioni hardware. Capacità di Microsoft per ottenere questi dati dai clienti e apportare miglioramenti all'ecosistema, linea di demarcazione per la qualità delle soluzioni integrate di Azure Stack.

I dati di telemetria consentono inoltre a Microsoft per meglio comprendere come i clienti distribuiscono componenti, usare le funzionalità e usare i servizi per raggiungere i propri obiettivi aziendali. Queste informazioni consentono di definire le priorità degli investimenti nella progettazione nelle aree che possono influire direttamente sulle esperienze dei clienti e i carichi di lavoro.

Alcuni esempi includono l'uso dei clienti di contenitori, archiviazione e le configurazioni di rete che sono associate i ruoli di Azure Stack. È anche possibile usare le informazioni dettagliate per apportare miglioramenti e business intelligence in Azure Stack gestione e monitoraggio delle soluzioni. Questi miglioramenti rendono più semplice per i clienti diagnosticare i problemi e risparmiare denaro, rendendo meno supporto le chiamate a Microsoft.

## <a name="manage-telemetry-collection"></a>Gestire raccolta dati di telemetria

Non è consigliata la disattivazione i dati di telemetria all'interno dell'organizzazione. Tuttavia, in alcuni scenari questo potrebbe essere necessario.

In questi scenari, è possibile configurare il livello di telemetria inviato a Microsoft tramite le impostazioni del Registro di sistema prima di distribuire Azure Stack o tramite gli endpoint di dati di telemetria dopo avere distribuito Azure Stack.

### <a name="telemetry-levels-and-data-collection"></a>Raccolta di dati e i livelli di dati di telemetria

Prima di modificare le impostazioni di telemetria, è necessario comprendere i livelli di dati di telemetria e i dati raccolti a ogni livello.

Le impostazioni di telemetria sono raggruppate in quattro livelli (0-3) che sono cumulativi e categorizzate come l'illustrato di seguito:

**0 (Security)**</br>
Solo i dati di sicurezza. Informazioni necessarie per proteggere il sistema operativo. Sono inclusi i dati sulle esperienze utente connesse e telemetria impostazioni del componente e Windows Defender. A questo livello viene generato alcun dato di telemetria specifico di Azure Stack.

**1 (Basic)**</br>
I dati di sicurezza e integrità di base e la qualità dei dati. Informazioni di base del dispositivo, tra cui: relative alla qualità dei dati, compatibilità delle applicazioni, i dati sull'utilizzo delle app e i dati dal **sicurezza** livello. Impostazione del livello dati di telemetria per la telemetria di Azure Stack consente di base. I dati raccolti a questo livello includono:

- *Informazioni di base del dispositivo* che consente di conoscere sui tipi e le configurazioni di istanze di Windows Server 2016 native e virtuali all'interno dell'ecosistema. Sono inclusi:

  - Attributi di computer, ad esempio l'OEM e modello.
  - Attributi, ad esempio il numero di schede di rete e delle prestazioni di rete.
  - Attributi di processore e memoria, ad esempio il numero di core e quantità di memoria installata.
  - Attributi di archiviazione, ad esempio il numero di unità, il tipo di unità e le dimensioni dell'unità.

- *La funzionalità di telemetria*, tra cui la percentuale di caricato gli eventi, eventi eliminati e la data di ultimo tempo di caricamento.
- *Informazioni relative alla qualità* che consentono a Microsoft di sviluppare una conoscenza di base delle prestazioni di Azure Stack. Ad esempio, il numero di avvisi critici in una configurazione hardware specifica.
- *I dati di compatibilità* che aiuta a garantire una comprensione intorno al quale vengono installati i provider di risorse in un sistema e una macchina virtuale. Identifica potenziali problemi di compatibilità.

**2 (avanzato)**</br>
Raccolta di informazioni aggiuntive, tra cui: come vengono usati il sistema operativo e i servizi di Azure Stack, come eseguono questi servizi, un'affidabilità avanzata dei dati e i dati dal **sicurezza** e **base** livelli.

> [!NOTE]
> Questo è l'impostazione di dati di telemetria.

**3 (completa)**</br>
Tutti i dati necessari per identificare e contribuire alla risoluzione dei problemi, oltre ai dati dal **sicurezza**, **base**, e **avanzato** livelli.

> [!IMPORTANT]
> Questi livelli di dati di telemetria si applicano solo ai componenti di Microsoft Azure Stack. I componenti software non Microsoft e i servizi in esecuzione nell'Host del ciclo di vita dell'Hardware dai partner hardware di Azure Stack possono comunicare con i propri servizi cloud di fuori di questi livelli di dati di telemetria. È necessario collaborare con il provider di soluzioni hardware Azure Stack per comprendere i criteri di dati di telemetria e il modo in cui è possibile acconsentire o rifiutare esplicitamente.

Disattivazione della telemetria di Windows e Azure Stack viene disabilitato anche i dati di telemetria SQL. Per altre informazioni sulle implicazioni delle impostazioni di telemetria di Windows Server, vedere la [white paper sulla telemetria di Windows](https://aka.ms/winservtelemetry).

### <a name="asdk-set-the-telemetry-level-in-the-windows-registry"></a>ASDK: impostare il livello di telemetria nel Registro di sistema Windows

È possibile utilizzare l'Editor del Registro di sistema di Windows per impostare il livello di telemetria manualmente nel computer host fisico prima di distribuire Azure Stack. Se un criterio di gestione esiste già, ad esempio criteri di gruppo, viene eseguito l'override di questa impostazione del Registro di sistema.

Prima di distribuire Azure Stack nell'host di kit di sviluppo, eseguire l'avvio in CloudBuilder.vhdx ed eseguire lo script seguente in una finestra di PowerShell con privilegi elevata:

```powershell
### Get current AllowTelemetry value on DVM Host
(Get-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\DataCollection" `
-Name AllowTelemetry).AllowTelemetry
### Set & Get updated AllowTelemetry value for ASDK-Host
Set-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\DataCollection" `
-Name "AllowTelemetry" -Value '0' # Set this value to 0,1,2,or3.  
(Get-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\DataCollection" `
-Name AllowTelemetry).AllowTelemetry
```

### <a name="asdk-and-multi-node-enable-or-disable-telemetry-after-deployment"></a>ASDK e multinodo: abilitare o disabilitare i dati di telemetria dopo la distribuzione

Per abilitare o disabilitare la telemetria dopo la distribuzione, è necessario avere accesso per il punto finale con privilegi (PEP) che viene esposta nelle macchine virtuali ERCS.

1. Per abilitare: `Set-Telemetry -Enable`
2. Per disabilitare: `Set-Telemetry -Disable`

Dettagli dei parametri:
> . PARAMETRO attiva - attiva il caricamento di dati di telemetria</br>
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

[Registrare Azure Stack con Azure](azure-stack-registration.md)