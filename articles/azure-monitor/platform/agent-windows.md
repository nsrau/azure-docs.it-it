---
title: Installare Log Analytics Agent nei computer Windows
description: Questo articolo descrive come connettere i computer Windows ospitati in altri cloud o in locale a Monitoraggio di Azure con l'agente di Log Analytics per Windows.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/03/2020
ms.openlocfilehash: d283c2b2cdbbeb3ef4bc4e25f4288dfd95158552
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/27/2020
ms.locfileid: "89003372"
---
# <a name="install-log-analytics-agent-on-windows-computers"></a>Installare Log Analytics Agent nei computer Windows
Questo articolo fornisce informazioni dettagliate sull'installazione dell'agente di Log Analytics nei computer Windows usando i metodi seguenti:

* Installazione manuale utilizzando l' [installazione guidata](#install-agent-using-setup-wizard) o la [riga di comando](#install-agent-using-command-line).
* [DSC (Desired state Configuration) di automazione di Azure](#install-agent-using-dsc-in-azure-automation). 

>[!IMPORTANT]
> I metodi di installazione descritti in questo articolo vengono in genere usati per le macchine virtuali in locale o in altri cloud. Vedere [Opzioni di installazione](log-analytics-agent.md#installation-options) per le opzioni più efficienti che è possibile usare per le macchine virtuali di Azure.

> [!NOTE]
> Se è necessario configurare l'agente per la segnalazione a più di un'area di lavoro, questa operazione non può essere eseguita durante l'installazione iniziale, solo successivamente aggiornando le impostazioni dal pannello di controllo o da PowerShell come descritto in [Aggiunta o rimozione di un'area di lavoro](agent-manage.md#adding-or-removing-a-workspace).  

## <a name="supported-operating-systems"></a>Sistemi operativi supportati

Per un elenco delle versioni di Windows supportate dall'agente di Log Analytics, vedere [Panoramica degli agenti di monitoraggio di Azure](agents-overview.md#supported-operating-systems) .

### <a name="sha-2-code-signing-support-requirement"></a>Requisito del supporto per la firma del codice SHA-2 
Il 17 agosto 2020 l'agente Windows inizierà a usare esclusivamente la firma SHA-2. Questa modifica interesserà i clienti che usano l'agente di Log Analytics in un sistema operativo legacy come parte di un servizio di Azure (Monitoraggio di Azure, Automazione di Azure, Gestione aggiornamenti di Azure, Rilevamento modifiche di Azure, Centro sicurezza di Azure, Azure Sentinel e Windows Defender ATP). La modifica non richiede intervento da parte dei clienti, a meno che l'agente non venga eseguito in una versione legacy del sistema operativo (Windows 7, Windows Server 2008 R2 e Windows Server 2008). I clienti che eseguono una versione legacy del sistema operativo devono intraprendere le azioni seguenti nei computer prima del 17 agosto 2020. In caso contrario gli agenti non invieranno più i dati alle aree di lavoro Log Analytics:

1. Installare il Service Pack più recente per il sistema operativo. Le versioni del Service Pack richieste sono le seguenti:
    - Windows 7 SP1
    - Windows Server 2008 SP2
    - Windows Server 2008 R2 SP1

2. Installare gli aggiornamenti di Windows per la firma SHA-2 per il sistema operativo come descritto in [Requisito di supporto per la firma del codice di SHA-2 2019 per Windows e WSUS](https://support.microsoft.com/help/4472027/2019-sha-2-code-signing-support-requirement-for-windows-and-wsus)
3. Eseguire l'aggiornamento alla versione più recente dell'agente Windows (versione 10.20.18029).
4. È consigliabile configurare l'agente per [usare il protocollo TLS 1.2](agent-windows.md#configure-agent-to-use-tls-12). 

## <a name="network-requirements"></a>Requisiti di rete
Vedere [Panoramica dell'agente di log Analytics](log-analytics-agent.md#network-requirements) per i requisiti di rete per l'agente di Windows.


   
## <a name="configure-agent-to-use-tls-12"></a>Configurare l'agente per usare TLS 1.2
Il protocollo [TLS 1,2](/windows-server/security/tls/tls-registry-settings#tls-12) garantisce la sicurezza dei dati in transito per la comunicazione tra l'agente di Windows e il servizio log Analytics. Se si sta eseguendo l'installazione in un [sistema operativo senza tls 1,2 abilitato per impostazione predefinita](data-security.md#sending-data-securely-using-tls-12), è necessario configurare TLS 1,2 seguendo questa procedura.

1. Individuare la seguente sottochiave del registro di sistema: **HKEY_LOCAL_MACHINE\System\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols**
2. Creare una sottochiave in **Protocolli** per TLS 1.2 **HKLM\System\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2**
3. Creare una sottochiave **Client** sotto la sottochiave della versione di protocollo TLS 1.2 creata in precedenza. Ad esempio, **HKLM\System\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client**.
4. Creare i valori DWORD seguenti in **HKLM\System\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client**:

    * **Enabled** [Value = 1]
    * **DisabledByDefault** [Value = 0]  

Configurare .NET Framework 4.6 o versione successiva per supportare la crittografia sicura, dal momento che è disabilitata per impostazione predefinita. La [crittografia avanzata](/dotnet/framework/network-programming/tls#schusestrongcrypto) usa protocolli di rete più sicuri, come TLS 1.2 che blocca i protocolli non sicuri. 

1. Individuare la seguente sottochiave del registro di sistema: **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\\.NETFramework\v4.0.30319**.  
2. Creare il valore DWORD **SchUseStrongCrypto** sotto questa sottochiave con valore **1**.  
3. Individuare la seguente sottochiave del registro di sistema: **HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\\.NETFramework\v4.0.30319**.  
4. Creare il valore DWORD **SchUseStrongCrypto** sotto questa sottochiave con valore **1**. 
5. Riavviare il sistema per rendere effettive le impostazioni. 

## <a name="install-agent-using-setup-wizard"></a>Installare l'agente utilizzando l'installazione guidata
I passaggi seguenti consentono di installare e configurare l'agente di Log Analytics nel cloud di Azure e di Azure per enti pubblici, usando l'installazione guidata per l'agente nel computer. Per altre informazioni su come configurare l'agente in modo che inoltri segnalazioni a un gruppo di gestione di System Center Operations Manager, vedere [distribuire l'agente di Operations Manager con l'installazione guidata dell'agente](/system-center/scom/manage-deploy-windows-agent-manually#to-deploy-the-operations-manager-agent-with-the-agent-setup-wizard).

1. Nell'area di lavoro Log Analytics, dalla pagina **Server Windows** a cui si è passati in precedenza, selezionare la versione appropriata da scaricare in **Scarica agente Windows** a seconda dell'architettura del processore del sistema operativo Windows.   
2. Eseguire il programma di installazione per installare l'agente nel computer in uso.
2. Nella pagina di **benvenuto** fare clic su **Avanti**.
3. Nella pagina **Condizioni di licenza** leggere la licenza e quindi fare clic su **Accetto**.
4. Nella pagina **Cartella di destinazione** modificare o mantenere la cartella di installazione predefinita e quindi fare clic su **Avanti**.
5. Nella pagina **Opzioni di installazione dell'agente** scegliere di connettere l'agente ad Azure Log Analytics e quindi fare clic su **Avanti**.   
6. Nella pagina **Azure Log Analytics** eseguire le operazioni seguenti:
   1. Incollare **ID area di lavoro** e **Chiave dell'area di lavoro (Chiave primaria)** copiati in precedenza.  Se il computer deve fare riferimento a un'area di lavoro Log Analytics nel cloud di Azure per enti pubblici, selezionare **Azure US Gov** nell'elenco a discesa **Cloud di Azure**.  
   2. Se il computer deve comunicare tramite un server proxy con il servizio Log Analytics, fare clic su **Avanzate** e specificare l'URL e il numero di porta del server proxy.  Se il server proxy richiede l'autenticazione, digitare il nome utente e la password per l'autenticazione nel server proxy, quindi fare clic su **Avanti**.  
7. Fare clic su **Avanti** dopo aver specificato le impostazioni di configurazione necessarie.<br><br> ![incollare ID area di lavoro e chiave primaria](media/agent-windows/log-analytics-mma-setup-laworkspace.png)<br><br>
8. Nella pagina **Pronto per l'installazione** rivedere le scelte effettuate e quindi fare clic su **Installa**.
9. Nella pagina **Configurazione completata** fare clic su **Fine**.

Al termine, verrà visualizzato **Microsoft Monitoring Agent** nel **Pannello di controllo**. Per confermare che fa riferimento a Log Analytics, rivedere [Verificare la connettività dell'agente a Log Analytics](#verify-agent-connectivity-to-azure-monitor). 

## <a name="install-agent-using-command-line"></a>Installare l'agente tramite la riga di comando
Il file scaricato per l'agente è un pacchetto di installazione autonomo.  Il programma di installazione per l'agente e i file di supporto sono contenuti nel pacchetto e devono essere estratti per eseguire correttamente l'installazione tramite la riga di comando mostrata negli esempi seguenti.    

>[!NOTE]
>Per aggiornare un agente è necessario usare l'API di scripting di Log Analytics. Per altre informazioni, vedere l'argomento [Gestione e manutenzione dell'agente di Log Analytics per Windows e Linux](agent-manage.md).

La tabella seguente illustra i parametri specifici supportati dal programma di installazione per l'agente, inclusi quelli distribuiti tramite Automation DSC.

|Opzioni specifiche di MMA                   |Note         |
|---------------------------------------|--------------|
| NOAPM=1                               | Parametro facoltativo. Installa l'agente senza .NET Application Performance Monitoring.|   
|ADD_OPINSIGHTS_WORKSPACE               | 1 = Configura l'agente per fare riferimento a un'area di lavoro                |
|OPINSIGHTS_WORKSPACE_ID                | ID dell'area di lavoro (guid) per l'area di lavoro da aggiungere                    |
|OPINSIGHTS_WORKSPACE_KEY               | Chiave dell'area di lavoro usata per autenticarsi inizialmente nell'area di lavoro |
|OPINSIGHTS_WORKSPACE_AZURE_CLOUD_TYPE  | Specificare l'ambiente cloud in cui si trova l'area di lavoro <br> 0 = Cloud commerciale di Azure (impostazione predefinita) <br> 1 = Azure per enti pubblici |
|OPINSIGHTS_PROXY_URL               | URI per il proxy da usare |
|OPINSIGHTS_PROXY_USERNAME               | Nome utente per accedere a un proxy autenticato |
|OPINSIGHTS_PROXY_PASSWORD               | Password per accedere a un proxy autenticato |

1. Per estrarre i file di installazione dell'agente, da un prompt dei comandi con privilegi elevati eseguire `MMASetup-<platform>.exe /c` e verrà chiesto il percorso in cui estrarre i file.  In alternativa, è possibile specificare il percorso passando gli argomenti `MMASetup-<platform>.exe /c /t:<Full Path>`.  
2. Per installare in modo invisibile l'agente e configurarlo in modo da fare riferimento a un'area di lavoro nel cloud commerciale di Azure, dalla cartella da cui sono stati estratti i file di installazione, digitare: 
   
     ```dos
    setup.exe /qn NOAPM=1 ADD_OPINSIGHTS_WORKSPACE=1 OPINSIGHTS_WORKSPACE_AZURE_CLOUD_TYPE=0 OPINSIGHTS_WORKSPACE_ID="<your workspace ID>" OPINSIGHTS_WORKSPACE_KEY="<your workspace key>" AcceptEndUserLicenseAgreement=1
    ```

   o per configurare l'uso come riferimento del cloud per US Gov di Azure, digitare: 

     ```dos
    setup.exe /qn NOAPM=1 ADD_OPINSIGHTS_WORKSPACE=1 OPINSIGHTS_WORKSPACE_AZURE_CLOUD_TYPE=1 OPINSIGHTS_WORKSPACE_ID="<your workspace ID>" OPINSIGHTS_WORKSPACE_KEY="<your workspace key>" AcceptEndUserLicenseAgreement=1
    ```
    >[!NOTE]
    >I valori stringa per i parametri *OPINSIGHTS_WORKSPACE_ID* e *OPINSIGHTS_WORKSPACE_KEY* devono essere racchiusi tra virgolette doppie per indicare a Windows Installer di interpretarli come opzioni valide per il pacchetto. 

## <a name="install-agent-using-dsc-in-azure-automation"></a>Installare l'agente usando DSC in automazione di Azure

È possibile usare lo script di esempio riportato di seguito per installare l'agente usando DSC in Automazione di Azure.   Se non si dispone di un account di Automazione, vedere [Guida introduttiva di automazione di Azure](../../automation/index.yml) per comprendere i requisiti e la procedura per la creazione di un account di Automazione richiesto per usare Automation DSC.  Se non si ha familiarità con Automation DSC, esaminare [Introduzione ad Automation DSC](../../automation/automation-dsc-getting-started.md).

L'esempio seguente illustra come installare l'agente a 64 bit, identificato dal valore `URI`. È possibile utilizzare anche la versione a 32 bit sostituendo il valore dell'URI. Gli URI per entrambe le versioni sono:

- Agente Windows a 64 bit: https://go.microsoft.com/fwlink/?LinkId=828603
- Agente Windows a 32 bit: https://go.microsoft.com/fwlink/?LinkId=828604


>[!NOTE]
>Questo esempio di procedura e script non supporta l'aggiornamento dell'agente già distribuito in un computer Windows.

Le versioni a 32 e 64 bit del pacchetto agente dispongono di un codice prodotto diverso e le nuove versioni rilasciate dispongono inoltre di un valore univoco.  Il codice prodotto è un GUID che rappresenta l'identificazione principale di un'applicazione o di un prodotto e viene rappresentato dalla proprietà **Codice prodotto** di Windows Installer.  Il valore `ProductId` nello script **MMAgent.ps1** deve corrispondere al codice prodotto dal pacchetto di installazione dell'agente a 32 bit o 64 bit.

Per recuperare direttamente il codice prodotto dal pacchetto di installazione dell'agente, è possibile usare Orca.exe dal [Componenti di Windows SDK per sviluppatori di Windows Installer](/windows/win32/msi/platform-sdk-components-for-windows-installer-developers) che è un componente del Windows Software Development Kit o usare PowerShell seguendo uno [script di esempio](https://www.scconfigmgr.com/2014/08/22/how-to-get-msi-file-information-with-powershell/) scritto da un Microsoft Valuable Professional (MVP).  Per entrambi gli approcci, è necessario innanzitutto estrarre il file **MOMagent.msi** dal pacchetto di installazione MMASetup.  Questa operazione è illustrata in precedenza nel primo passaggio nella sezione [Installare l'agente usando la riga di comando](#install-agent-using-command-line).  

1. Importare il modulo DSC xPSDesiredStateConfiguration da [https://www.powershellgallery.com/packages/xPSDesiredStateConfiguration](https://www.powershellgallery.com/packages/xPSDesiredStateConfiguration) in Automazione di Azure.  
2.    Creare gli asset variabili di Automazione di Azure per *OPSINSIGHTS_WS_ID* e *OPSINSIGHTS_WS_KEY*. Impostare *OPSINSIGHTS_WS_ID* sull'ID dell'area di lavoro Log Analytics e impostare *OPSINSIGHTS_WS_KEY* sulla chiave primaria dell'area di lavoro.
3.    Copiare lo script e salvarlo come MMAgent.ps1.

```powershell
Configuration MMAgent
{
    $OIPackageLocalPath = "C:\Deploy\MMASetup-AMD64.exe"
    $OPSINSIGHTS_WS_ID = Get-AutomationVariable -Name "OPSINSIGHTS_WS_ID"
    $OPSINSIGHTS_WS_KEY = Get-AutomationVariable -Name "OPSINSIGHTS_WS_KEY"

    Import-DscResource -ModuleName xPSDesiredStateConfiguration
    Import-DscResource -ModuleName PSDesiredStateConfiguration

    Node OMSnode {
        Service OIService
        {
            Name = "HealthService"
            State = "Running"
            DependsOn = "[Package]OI"
        }

        xRemoteFile OIPackage {
            Uri = "https://go.microsoft.com/fwlink/?LinkId=828603"
            DestinationPath = $OIPackageLocalPath
        }

        Package OI {
            Ensure = "Present"
            Path  = $OIPackageLocalPath
            Name = "Microsoft Monitoring Agent"
            ProductId = "8A7F2C51-4C7D-4BFD-9014-91D11F24AAE2"
            Arguments = '/C:"setup.exe /qn NOAPM=1 ADD_OPINSIGHTS_WORKSPACE=1 OPINSIGHTS_WORKSPACE_ID=' + $OPSINSIGHTS_WS_ID + ' OPINSIGHTS_WORKSPACE_KEY=' + $OPSINSIGHTS_WS_KEY + ' AcceptEndUserLicenseAgreement=1"'
            DependsOn = "[xRemoteFile]OIPackage"
        }
    }
}

```

4. Aggiornare il valore `ProductId` nello script con il codice prodotto estratto dalla versione più recente del pacchetto di installazione dell'agente usando i metodi consigliati in precedenza. 
5. [Importare lo script di configurazione MMAgent.ps1](../../automation/automation-dsc-getting-started.md#import-a-configuration-into-azure-automation) nell'account di Automazione. 
6. [Assegnare un computer Windows o un nodo](../../automation/automation-dsc-getting-started.md#enable-an-azure-resource-manager-vm-for-management-with-state-configuration) alla configurazione. Entro 15 minuti il nodo controlla la configurazione e viene effettuato il push dell'agente al nodo.

## <a name="verify-agent-connectivity-to-azure-monitor"></a>Verificare la connettività dell'agente a monitoraggio di Azure

Dopo aver completato l'installazione dell'agente, la verifica dell'avvenuta connessione e il riferimento possono essere eseguiti in due modi.  

Dal **Pannello di controllo** del computer, trovare l'elemento **Microsoft Monitoring Agent**.  Selezionarlo e, nella scheda **Azure Log Analytics** l'agente dovrebbe visualizzare un messaggio per segnalare che **Microsoft Monitoring Agent ha eseguito la connessione al servizio Microsoft Operations Management Suite.**<br><br> ![Stato della connessione di MMA a Log Analytics](media/agent-windows/log-analytics-mma-laworkspace-status.png)

È anche possibile eseguire una query di log semplice nel portale di Azure.  

1. Nel portale di Azure cercare e selezionare **Monitoraggio**.
1. Selezionare **Log** nel menu.
1. Nel riquadro **Log**, nel tipo di campo query:  

    ```
    Heartbeat 
    | where Category == "Direct Agent" 
    | where TimeGenerated > ago(30m)  
    ```

Nei risultati della ricerca restituiti, devono venire visualizzati i record di heartbeat per il computer indicanti che è connesso e che invia report al servizio.

## <a name="cache-information"></a>Informazioni cache

I dati dell'agente di Log Analytics vengono memorizzati nella cache del computer locale in *C:\Programmi\Microsoft Monitoring Agent\Agent\Health Service state* prima di essere inviati a monitoraggio di Azure. L'agente tenta di caricare ogni 20 secondi. Se ha esito negativo, attenderà una quantità di tempo esponenzialmente crescente fino a quando non avrà esito positivo. Attenderà 30 secondi prima del secondo tentativo, 60 secondi prima del successivo, 120 secondi e così via fino a un massimo di 8,5 ore tra i tentativi fino a quando non si riconnette correttamente. Questo tempo di attesa è leggermente casuale per evitare che tutti gli agenti tentino contemporaneamente la connessione. Quando viene raggiunto il buffer massimo, i dati meno recenti vengono eliminati.

Le dimensioni predefinite della cache sono di 50 MB, ma possono essere configurate tra un minimo di 5 MB e un massimo di 1,5 GB. Viene archiviato nella chiave del registro di sistema *HKEY_LOCAL_MACHINE valore massimo della cache \system\currentcontrolset\services\healthservice\parameters\persistence*. Il valore rappresenta il numero di pagine, con 8 KB per pagina.


## <a name="next-steps"></a>Passaggi successivi

- Per informazioni sulle modalità di riconfigurazione, aggiornamento o rimozione dell'agente dalla macchina virtuale, rivedere [Gestione e manutenzione dell'agente di Log Analytics per Windows e Linux](agent-manage.md).

- Se si verificano problemi di installazione o gestione dell'agente, vedere [Risoluzione dei problemi dell'agente Windows](agent-windows-troubleshoot.md).
