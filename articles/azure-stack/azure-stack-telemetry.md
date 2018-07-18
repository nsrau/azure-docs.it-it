---
title: Dati di telemetria di Azure Stack | Documenti Microsoft
description: Viene descritto come configurare le impostazioni di telemetria dello Stack di Azure tramite PowerShell.
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
ms.topic: get-started-article
ms.date: 06/07/2018
ms.author: jeffgilb
ms.reviewer: comartin
ms.openlocfilehash: ed3f09f942bdaa803ae8024d5c02230173190107
ms.sourcegitcommit: 50f82f7682447245bebb229494591eb822a62038
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/08/2018
ms.locfileid: "35248198"
---
# <a name="azure-stack-telemetry"></a>Dati di telemetria di Azure Stack

*Si applica a: Azure Stack integrate di sistemi Azure Stack Development Kit*

Dati di telemetria di Azure Stack carica automaticamente i dati di sistema a Microsoft tramite l'esperienza dell'utente connesso. Microsoft Team utilizzano i dati che raccoglie dati di telemetria dello Stack di Azure per migliorare le esperienze degli utenti. Questi dati vengono utilizzati anche per la sicurezza, integrità, la qualità e l'analisi delle prestazioni.

Per un operatore di Stack di Azure, dati di telemetria può fornire informazioni dettagliate preziose sulle distribuzioni aziendali e offre una voce che aiuta a forma di futura dello Stack di Azure.

> [!NOTE]
> È inoltre possibile configurare Azure Stack per inoltrare le informazioni sull'utilizzo per Azure per la fatturazione. Ciò è necessario per i clienti a più nodi Azure Stack scegliere fatturazione retribuzione come-di-uso. Report sull'utilizzo viene controllato in modo indipendente dalla telemetria e non è necessaria per i clienti a più nodi che scegliere il modello di capacità o per gli utenti di Azure Stack Development Kit. Per questi scenari, report di utilizzo può essere disattivata [utilizzando lo script di registrazione](https://docs.microsoft.com/azure/azure-stack/azure-stack-usage-reporting).

Telemetria Stack Azure si basa sul componente Windows Server 2016 connesso l'esperienza utente e dati di telemetria, che usa il [Event Tracing for Windows (ETW)](https://msdn.microsoft.com/library/dn904632(v=vs.85).aspx) tecnologia TraceLogging per raccogliere e archiviare i dati e gli eventi. Azure componenti dello Stack utilizzano la stessa tecnologia per pubblicare eventi e i dati raccolti tramite la registrazione degli eventi di sistema operativo pubblico e la traccia API. Questi componenti dello Stack di Azure sono esempi di questi provider: risorsa di rete, risorsa di archiviazione, risorse di monitoraggio e aggiornamento risorsa. Il componente esperienza dell'utente connesso e telemetria crittografa i dati utilizzando SSL e utilizza il blocco di certificato per trasmettere dati tramite HTTPS per il servizio di gestione dei dati di Microsoft.

> [!IMPORTANT]
> Per abilitare il flusso di dati di telemetria, la porta 443 (HTTPS) deve essere aperta in rete. Il componente esperienza dell'utente connesso e telemetria si connette al servizio di gestione di dati di Microsoft in https://v10.vortex-win.data.microsoft.com. Il componente esperienza dell'utente connesso e dati di telemetria è collegato a https://settings-win.data.microsoft.com per scaricare le informazioni di configurazione.

## <a name="privacy-considerations"></a>Considerazioni sulla privacy

Il servizio ETW instrada i dati di telemetria archiviazione cloud protetto. Il principio di privilegio minimo consente l'accesso ai dati di telemetria. Solo il personale di Microsoft con un'esigenza aziendale valido vengono concesso l'accesso ai dati di telemetria. Microsoft non condivide dati personali con terze parti, ad eccezione a discrezione del cliente o per gli scopi limitati descritto nel [informativa sulla Privacy Microsoft](https://privacy.microsoft.com/PrivacyStatement). Report di business che sono condivisi con gli OEM e partner includono dati aggregati e resi anonimi. Le decisioni di condivisione dei dati vengono effettuati da un team di Microsoft interno, tra cui le parti interessate di gestione dati, legali e privacy.

Microsoft crede e consigliate per la minimizzazione di informazioni. Si impegna a raccogliere solo le informazioni necessarie e archiviano per solo il tempo necessario per fornire un servizio o per l'analisi. La maggior parte delle informazioni sulla modalità di funzionamento del sistema di Stack di Azure e Azure services viene eliminata all'interno di sei mesi. Riepilogare o dati aggregati verranno conservati per un periodo più lungo.

Siamo consapevoli che è importante garantire la privacy e protezione delle informazioni sui clienti.  Microsoft adotta un approccio attento e completo per la privacy dei clienti e la protezione dei dati dei clienti nello Stack di Azure. Gli amministratori IT sono controlli per personalizzare le funzionalità e le impostazioni di privacy in qualsiasi momento. Impegno di trasparenza e l'attendibilità è ovvia:

- Siamo Apri con clienti riguardo i tipi di dati che è raccogliere.
- I clienti aziendali è stato inserito nel controllo, è possibile personalizzare le proprie impostazioni di privacy.
- È inserito prima della protezione e privacy dei clienti.
- Siamo trasparente sulla modalità di utilizzo dati di telemetria ottiene.
- Microsoft utilizzerà i dati di telemetria per migliorare le esperienze degli utenti.

Microsoft non intende raccogliere i dati sensibili, ad esempio numeri di carta di credito, nomi utente e password, indirizzi di posta elettronica o informazioni riservate simili. Se si determina che le informazioni riservate sono state ricevute inavvertitamente, vengono eliminati.

## <a name="examples-of-how-microsoft-uses-the-telemetry-data"></a>Esempi di come Microsoft utilizza i dati di telemetria

Dati di telemetria svolge un ruolo importante nell'aiutare a identificare rapidamente e risolvere i problemi critici per l'affidabilità in distribuzioni dei clienti e le configurazioni. Informazioni dettagliate dai dati di telemetria possono identificare i problemi con i servizi o configurazioni hardware. Possibilità di Microsoft per ottenere i dati dai clienti e apportare miglioramenti nell'ecosistema, genera la barra per la qualità delle soluzioni di Azure Stack integrate.

Dati di telemetria consentono inoltre a Microsoft per meglio comprendere come distribuire i componenti, utilizzare le funzionalità e utilizzare i servizi per raggiungere gli obiettivi di business. Queste considerazioni definire la priorità engineering gli investimenti nelle aree che possono influire direttamente sulle esperienze degli utenti e i carichi di lavoro.

Alcuni esempi includono l'utilizzo di clienti di contenitori, archiviazione e le configurazioni di rete che sono associate ai ruoli Azure Stack. Utilizziamo inoltre le informazioni dettagliate per apportare miglioramenti e business intelligence in Gestione di Azure Stack e soluzioni di monitoraggio. Questi miglioramenti rendono più semplice per i clienti individuare i problemi e risparmiare denaro, rendendo meno supporto chiama a Microsoft.

## <a name="manage-telemetry-collection"></a>Gestire raccolta dati di telemetria

Non consigliata disattivazione della telemetria nell'organizzazione. Tuttavia, in alcuni scenari ciò potrebbe essere necessario.

In questi scenari, è possibile configurare il livello di dati di telemetria inviato a Microsoft utilizzando le impostazioni del Registro di sistema prima di distribuire Azure Stack o utilizzando gli endpoint di telemetria dopo avere distribuito Azure Stack.

### <a name="telemetry-levels-and-data-collection"></a>Raccolta di livelli e i dati di telemetria

Prima di modificare le impostazioni di telemetria, è necessario comprendere i livelli dati di telemetria e quali dati vengono raccolti a ogni livello.

Le impostazioni di telemetria vengono suddivise in quattro raggruppamenti (0-3) che sono cumulativi e suddiviso in categorie come i seguente:

**0 (protezione)**</br>
Solo dati di sicurezza. Informazioni necessarie per proteggere il sistema operativo. Sono inclusi i dati sulle impostazioni del componente connesso l'esperienza utente e telemetria e Windows Defender. A questo livello non viene generato alcun telemetria specifica allo Stack di Azure.

**1 (base)**</br>
Protezione dati e dello stato di base e la qualità dei dati. Informazioni di base del dispositivo, tra cui: dati relativi alla qualità, compatibilità delle applicazioni, i dati di utilizzo di app e i dati dal **sicurezza** livello. Impostando il livello di dati di telemetria su dati di telemetria dello Stack di Azure consente di base. I dati raccolti a questo livello includono:

- *Informazioni sul dispositivo* che fornisce informazioni sui tipi e le configurazioni delle istanze di Windows Server 2016 native e virtuale all'interno dell'ecosistema. Sono inclusi:

  - Attributi di computer, ad esempio OEM e modello.
  - Attributi, ad esempio il numero di schede di rete e la velocità di rete.
  - Attributi di memoria e del processore, ad esempio il numero di core e quantità di memoria installata.
  - Attributi di archiviazione, ad esempio il numero di unità, tipo di unità e le dimensioni dell'unità.

- *La funzionalità dati di telemetria*, inclusa la percentuale di eventi caricati, eventi eliminati e gli ultimi dati caricare ora.
- *Le informazioni relative alla qualità* che consente a Microsoft di sviluppare una conoscenza di base della modalità di esecuzione di Stack di Azure. Ad esempio, il numero di avvisi critici in una configurazione hardware specifica.
- *I dati di compatibilità* che aiuta a offrire informazioni sui provider di risorse vengono installati in un sistema e una macchina virtuale. Identifica potenziali problemi di compatibilità.

**2 (avanzata)**</br>
Ottenere informazioni aggiuntive, tra cui: utilizzo del sistema operativo e i servizi di Azure Stack, come eseguono questi servizi, affidabilità avanzate dati e i dati dal **sicurezza** e **base** livelli.

> [!NOTE]
> Si tratta dell'impostazione di dati di telemetria predefinito.

**3 (completa)**</br>
Tutti i dati necessari per identificare e sono utili per risolvere i problemi, oltre ai dati dal **sicurezza**, **base**, e **avanzato** livelli.

> [!IMPORTANT]
> Questi livelli di dati di telemetria vengono applicate solo ai componenti dello Stack di Microsoft Azure. I componenti software non Microsoft e i servizi in esecuzione nell'Host del ciclo di vita di Hardware da partner hardware dello Stack di Azure possono comunicare con i servizi cloud di fuori di questi livelli di dati di telemetria. È necessario collaborare con il provider di soluzione hardware di Azure Stack per comprendere i criteri di telemetria e come è possibile partecipare o rifiutare esplicitamente.

Disattivazione della telemetria di Windows e Azure Stack disattiva anche i dati di telemetria SQL. Per ulteriori informazioni sulle implicazioni delle impostazioni di telemetria di Windows Server, vedere il [white paper telemetria Windows](https://aka.ms/winservtelemetry).

### <a name="asdk-set-the-telemetry-level-in-the-windows-registry"></a>ASDK: impostare il livello di dati di telemetria nel Registro di sistema Windows

È possibile utilizzare l'Editor del Registro di sistema di Windows per impostare manualmente il livello di dati di telemetria nel computer host fisico prima di distribuire Azure Stack. Se un criterio di gestione già esistente, ad esempio criteri di gruppo, viene eseguito l'override di questa impostazione del Registro di sistema.

Prima di distribuire Azure Stack nell'host kit sviluppo, avviare CloudBuilder.vhdx ed eseguire lo script seguente in una finestra di PowerShell con privilegi elevata:

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

### <a name="asdk-and-multi-node-enable-or-disable-telemetry-after-deployment"></a>ASDK e a più nodi: abilitare o disabilitare i dati di telemetria dopo la distribuzione

Per abilitare o disabilitare i dati di telemetria dopo la distribuzione, è necessario disporre di accesso per il punto finale con privilegi (PEP) che viene esposto in macchine virtuali di ERCS.

1. Per abilitare: `Set-Telemetry -Enable`
2. Per disabilitare: `Set-Telemetry -Disable`

Dettagli del parametro:
> . PARAMETRO Enable - Attiva il caricamento di dati di telemetria</br>
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

- [Scaricare il pacchetto di distribuzione kit di sviluppo di Azure Stack](https://azure.microsoft.com/overview/azure-stack/try/?v=try)

- [Distribuire il kit di sviluppo di Azure Stack](azure-stack-run-powershell-script.md)
