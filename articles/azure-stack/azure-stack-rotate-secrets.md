---
title: Rotazione dei segreti in Azure Stack | Microsoft Docs
description: Informazioni su come eseguire la rotazione i segreti in Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/18/2018
ms.author: mabrigg
ms.reviewer: ppacent
ms.openlocfilehash: 27fd02503881ef1f0587ccb0301f8490101d5bcb
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/21/2018
ms.locfileid: "53720646"
---
# <a name="rotate-secrets-in-azure-stack"></a>Rotazione dei segreti in Azure Stack

*Queste istruzioni si applicano solo a Azure Stack integrati sistemi versione 1803 e versioni successive. Non tentare la rotazione segreta nelle versioni di pre-1802 Azure Stack*

Azure Stack utilizza vari segreti per gestire comunicazioni sicure tra servizi e delle risorse di infrastruttura di Azure Stack.

- **Segreti interni**  
Tutti i certificati, password, stringhe sicure e le chiavi usate dall'infrastruttura di Azure Stack senza l'intervento dell'operatore di Stack di Azure. 

- **Segreti esterni**  
Certificati di infrastruttura a servizio per i servizi esterni che vengono forniti dall'operatore di Stack di Azure. Ciò include i certificati per i servizi seguenti: 
  - Portale dell'amministratore  
  - Portale pubblico  
  - Amministratore di Azure Resource Manager  
  - Global Azure Resource Manager  
  - Insieme di credenziali di amministratore  
  - Key Vault  
  - Host dell'estensione di amministrazione  
  - Servizio contenitore di AZURE (inclusi blob, tabelle e archiviazione code)  
  - ADFS *  
  - Grafico *  

\* Questo campo è applicabile solo se il provider di identità dell'ambiente Active Directory Federated Services (ADFS).

> [!Note]  
> Tutti gli altri proteggere chiavi e le stringhe, tra cui BMC e cambiare le password, le password degli account utente e amministratore vengono aggiornati manualmente dall'amministratore. 

> [!Note]  
> Partire dalla versione di Azure Stack 1811, rotazione segreta è stata inserita per i certificati interni ed esterni. 

Per mantenere l'integrità dell'infrastruttura di Azure Stack, gli operatori devono avere la possibilità di ruotare periodicamente i segreti dell'infrastruttura, le frequenze che siano coerenti con i requisiti di sicurezza della propria organizzazione.

### <a name="rotating-secrets-with-external-certificates-from-a-new-certificate-authority"></a>La rotazione dei segreti con certificati esterni da un'autorità di certificazione nuovo

Azure Stack supporta la rotazione segreta con certificati esterni da un autorità di certificazione (CA) nuova nei contesti seguenti:

|Installazione certificato CA|Autorità di certificazione per ruotare per|Supportato|Versioni di Azure Stack è supportate|
|-----|-----|-----|-----|
|Da autofirmato|Per Enterprise|Non supportato||
|Da autofirmato|Per autofirmato|Non supportato||
|Da autofirmato|Pubblico<sup>*</sup>|Supportato|1803 e versioni successive|
|Da Enterprise|Per Enterprise|Supportato purché i clienti usano la stessa CA usato in fase di distribuzione dell'organizzazione|1803 e versioni successive|
|Da Enterprise|Per autofirmato|Non supportato||
|Da Enterprise|Pubblico<sup>*</sup>|Supportato|1803 e versioni successive|
|Pubblico<sup>*</sup>|Per Enterprise|Non supportato|1803 e versioni successive|
|Pubblico<sup>*</sup>|Per autofirmato|Non supportato||
|Pubblico<sup>*</sup>|Pubblico<sup>*</sup>|Supportato|1803 e versioni successive|

<sup>*</sup>Indica che l'autorità di certificazione pubbliche sono quelli che fanno parte del programma Windows Trusted Root. È possibile trovare l'elenco completo nell'articolo [programma Microsoft Trusted Root Certificate: I partecipanti (a partire dal 27 giugno 2017)](https://gallery.technet.microsoft.com/Trusted-Root-Certificate-123665ca).

## <a name="alert-remediation"></a>Correzione dell'avviso

Quando i segreti sono entro 30 giorni di scadenza, nel portale di amministrazione vengono generati gli avvisi seguenti: 

- Scadenza password dell'account del servizio in sospeso 
- Scadenza del certificato interno in sospeso 
- Scadenza certificato esterno in sospeso 

Esecuzione di rotazione segreta usando le istruzioni seguenti verranno correggere questi avvisi.

> [!Note]  
> Gli ambienti Azure Stack su versioni pre-1811 potrebbero visualizzare gli avvisi per certificato interno o le scadenze segreto in sospeso. Questi avvisi non sono precise e devono essere ignorati senza eseguire la rotazione segreta interna. Gli avvisi di scadenza del segreto non accurati interne sono un problema noto che è stato risolto in 1811 – interna dei segreti non scada mai, a meno che l'ambiente è stato attivo per due anni. 

## <a name="pre-steps-for-secret-rotation"></a>Passaggi preliminari per la rotazione segreta

   > [!IMPORTANT]  
   > Se è già stata eseguita la rotazione segreta nell'ambiente Azure Stack è necessario aggiornare il sistema alla versione 1811 o versione successiva prima di eseguire nuovamente la rotazione segreta. Rotazione segreta deve essere eseguita da tramite il [Privileged Endpoint](https://docs.microsoft.com/azure/azure-stack/azure-stack-privileged-endpoint) e richiede le credenziali di Azure Stack operatore. Se l'ambiente Azure Stack contattabili non si sa se rotazione segreta è stata eseguita nel proprio ambiente, aggiornare 1811 prima di eseguire nuovamente la rotazione segreta.

1. È consigliabile che l'istanza di Azure Stack l'aggiornamento alla versione 1811.

    > [!Note] 
    > Per le versioni di pre-1811 non occorre eseguire la rotazione dei segreti per aggiungere i certificati di host di estensione. È consigliabile seguire le istruzioni nell'articolo [prepararsi host dell'estensione per Azure Stack](azure-stack-extension-host-prepare.md) per aggiungere i certificati di host di estensione.

2.  Gli operatori potrebbero notare avvisi aprono e chiudono automaticamente durante la rotazione dei segreti di Azure Stack.  Questo comportamento è previsto e gli avvisi possono essere ignorati.  Gli operatori possono verificare la validità di questi avvisi eseguendo **Test-AzureStack**.  Per gli operatori tramite SCOM per monitorare i sistemi Azure Stack, impostazione di un sistema in modalità manutenzione, potrà raggiungere i propri sistemi di gestione dei servizi IT di questi avvisi ma continueranno a genera un avviso se il sistema Azure Stack non diventi irraggiungibile. 
3. Inviare notifiche agli utenti di tutte le operazioni di manutenzione. Pianificare finestre di manutenzione normale, quanto più possibile, durante non lavorative. I carichi di lavoro utente e operazioni nel portale, possono influire sulle operazioni di manutenzione.

    > [!Note]  
    > I passaggi successivi si applicano solo quando la rotazione dei segreti esterni di Azure Stack.

4. Eseguire **[Test AzureStack](https://docs.microsoft.com/azure/azure-stack/azure-stack-diagnostic-test)** e verificare che tutti gli output di test siano integri prima della rotazione dei segreti.
5. Preparare un nuovo set di sostituzione di certificati esterni. Il nuovo set corrisponde al certificato alle specifiche di [requisiti dei certificati di infrastruttura a chiave pubblica di Azure Stack](https://docs.microsoft.com/azure/azure-stack/azure-stack-pki-certs). È possibile generare un certificato (CSR) richiesta di firma per richiedere l'acquisto o la creazione di nuovi certificati usando la procedura descritta nel [generare i certificati PKI](https://docs.microsoft.com/azure/azure-stack/azure-stack-get-pki-certs) e prepararli per l'uso nell'ambiente Azure Stack seguendo i passaggi contenuti [ Preparare i certificati di infrastruttura a chiave pubblica di Azure Stack](https://docs.microsoft.com/azure/azure-stack/azure-stack-prepare-pki-certs). Assicurarsi di convalidare i certificati preparato con la procedura descritta nel [convalidare i certificati PKI](https://docs.microsoft.com/azure/azure-stack/azure-stack-validate-pki-certs). 
6.  Store backup i certificati usati per la rotazione in un percorso di backup sicura. Se la rotazione viene eseguita e quindi si verifica un errore, sostituire i certificati nella condivisione file con le copie di backup prima di rieseguire la rotazione. Si noti, mantenere copie di backup nel percorso di backup sicuro.
7.  Creare una condivisione file che è possibile accedere da macchine virtuali ERCS. La condivisione file deve essere leggibile e scrivibile per il **CloudAdmin** identità.
8.  Aprire una console di PowerShell ISE da un computer in cui si ha accesso alla condivisione file. Passare alla condivisione di file. 
9.  Eseguire **[CertDirectoryMaker.ps1](https://www.aka.ms/azssecretrotationhelper)** per creare le directory necessarie per i certificati esterni.

## <a name="rotating-external-secrets"></a>La rotazione dei segreti esterni

Per ruotare i segreti esterni:

1. All'interno di appena creato **certificati** directory creata nei passaggi precedenti, posizionare il nuovo set di certificati esterni di sostituzione nella struttura di directory in base al formato descritto nella sezione certificati obbligatori del [requisiti dei certificati di infrastruttura a chiave pubblica di Azure Stack](https://docs.microsoft.com/azure/azure-stack/azure-stack-pki-certs#mandatory-certificates).
2. Creare una sessione di PowerShell con il [Endpoint con privilegi](https://docs.microsoft.com/azure/azure-stack/azure-stack-privileged-endpoint) usando la **CloudAdmin** dell'account e archiviare le sessioni come una variabile. Si userà questa variabile come parametro nel passaggio successivo.

    > [!IMPORTANT]  
    > Non immettere la sessione, archiviare la sessione come una variabile.
    
3. Eseguire  **[invoke-command](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/invoke-command?view=powershell-5.1)**. Passare la variabile di sessione di PowerShell con privilegi di Endpoint come le **sessione** parametro. 
4. Eseguire **Start-SecretRotation** con i parametri seguenti:
    - **PfxFilesPath**  
    Specificare il percorso di rete per la directory dei certificati creato in precedenza.  
    - **PathAccessCredential**  
    Un oggetto PSCredential per le credenziali alla condivisione. 
    - **CertificatePassword**  
    Una stringa sicura della password utilizzata per tutti i file di certificato pfx creati.
4. Attendere. ruotare i segreti. Rotazione segreta esterna richiede in genere circa un'ora. Quando la rotazione segreta viene completata correttamente, verrà visualizzata la console di **stato azione generale: Operazione riuscita**. 
    > [!Note]  
    > Se la rotazione segreta non riesce, seguire le istruzioni nel messaggio di errore ed eseguire di nuovo **start-secretrotation** con il **-Riesegui** parametro. 

    ```PowerShell  
    Start-SecretRotation -Rerun
    ```
    Contattare il supporto tecnico se si riscontrano ripetuti errori di rotazione segreta.
5. Dopo il completamento della rotazione segreta, rimuovere i certificati dalla condivisione creata nel passaggio precedente e archiviarle nella posizione di backup sicura. 

## <a name="walkthrough-of-secret-rotation"></a>Procedura dettagliata di rotazione segreta

L'esempio di PowerShell seguente illustra i cmdlet e parametri per l'esecuzione per poter essere ruotate i segreti.

```powershell
#Create a PEP Session
winrm s winrm/config/client '@{TrustedHosts= "<IPofERCSMachine>"}'
$PEPCreds = Get-Credential 
$PEPsession = New-PSSession -computername <IPofERCSMachine> -Credential $PEPCreds -ConfigurationName PrivilegedEndpoint 

#Run Secret Rotation
$CertPassword = ConvertTo-SecureString "Certpasswordhere" -Force
$CertShareCred = Get-Credential 
$CertSharePath = "<NetworkPathofCertShare>"
Invoke-Command -session $PEPsession -ScriptBlock { 
Start-SecretRotation -PfxFilesPath $using:CertSharePath -PathAccessCredential $using:CertShareCred -CertificatePassword $using:CertPassword }
Remove-PSSession -Session $PEPSession
```

## <a name="rotating-only-internal-secrets"></a>Rotazione solo interni segreti

> [!Note]  
> Rotazione di chiave privata interna deve essere eseguita solo se si sospetta che un segreto interno è stato compromesso da entità dannose o se si riceve un avviso (in fase di compilazione 1811 o versione successiva) per indicare certificati interni sono prossime alla scadenza. Gli ambienti Azure Stack su versioni pre-1811 potrebbero visualizzare gli avvisi per certificato interno o le scadenze segreto in sospeso. Questi avvisi non sono precise e devono essere ignorati senza eseguire la rotazione segreta interna. Gli avvisi di scadenza del segreto non accurati interne sono un problema noto che è stato risolto in 1811 – interna dei segreti non scada mai, a meno che l'ambiente è stato attivo per due anni.

Per ruotare solo i segreti interni di Azure Stack:

1. Creare una sessione di PowerShell con il [Privileged Endpoint](https://docs.microsoft.com/azure/azure-stack/azure-stack-privileged-endpoint).
2. Nella sessione dell'Endpoint con privilegi, eseguire **Start-SecretRotation-interno**.

    > [!Note]  
    > Gli ambienti Azure Stack su versioni pre-1811 non richiederanno la **-interno** flag. **Start-SecretRotation** ruoterà solo informazioni segrete interne.

3. Attendere. ruotare i segreti.  
Quando la rotazione segreta viene completata correttamente, verrà visualizzata la console di **stato azione generale: Operazione riuscita**. 
    > [!Note]  
    > Se la rotazione segreta non riesce, seguire le istruzioni nel messaggio di errore ed eseguire di nuovo **start-secretrotation** con il **– interno** e **-rieseguire** parametri.  

    ```PowerShell  
    Start-SecretRotation -Internal -Rerun
    ```
    Contattare il supporto tecnico se si riscontrano ripetuti errori di rotazione segreta.

## <a name="start-secretrotation-reference"></a>Start-SecretRotation riferimento

Ruota i segreti di un sistema di Azure Stack. Viene eseguito solo in base all'Endpoint con privilegi di Azure Stack.

### <a name="syntax"></a>Sintassi

#### <a name="for-external-secret-rotation"></a>Per la rotazione segreta esterna

```Powershell  
Start-SecretRotation [-PfxFilesPath <string>] [-PathAccessCredential] <PSCredential> [-CertificatePassword <SecureString>]  
```

#### <a name="for-internal-secret-rotation"></a>Per la rotazione segreta interna 

```Powershell  
Start-SecretRotation [-Internal]  
```

#### <a name="for-external-secret-rotation-rerun"></a>Per la rotazione segreta esterna eseguire di nuovo 

```Powershell  
Start-SecretRotation [-Rerun]
```

#### <a name="for-internal-secret-rotation-rerun"></a>Per la rotazione segreta interna eseguire di nuovo 

```Powershell  
Start-SecretRotation [-Rerun] [-Internal]
```
### <a name="description"></a>DESCRIZIONE

Il **Start-SecretRotation** cmdlet ruota i segreti dell'infrastruttura di un sistema di Azure Stack. Per impostazione predefinita viene ruotato solo i certificati di tutti gli endpoint dell'infrastruttura di rete esterna. Se usata con-flag interno interno rotazione dei segreti dell'infrastruttura. Per ruotare un oggetto endpoint dell'infrastruttura di rete esterna **Start-SecretRotation** deve essere eseguita con un **Invoke-Command** del blocco di script con l'ambiente Azure Stack con privilegi sessione endpoint passato come parametro di sessione.
 
### <a name="parameters"></a>Parametri

| Parametro | type | Obbligatorio | Posizione | Predefinito | DESCRIZIONE |
| -- | -- | -- | -- | -- | -- |
| PfxFilesPath | string  | False  | denominata  | Nessuna  | Il percorso di condivisione file per il **\Certificates** directory contenente tutti esterni i certificati di endpoint di rete. Necessaria solo per la rotazione dei segreti esterni o tutti i segreti. Directory di fine deve essere **\Certificates**. |
| CertificatePassword | SecureString | False  | denominata  | Nessuna  | La password per tutti i certificati forniti in PfXFilesPath. Valore obbligatorio se PfxFilesPath è disponibile quando vengono ruotati segreti interni ed esterni. |
| Interno | string | False | denominata | Nessuna | Flag interno deve essere utilizzata ogni volta che un operatore di Azure Stack desidera ruotare i segreti di infrastruttura interna. |
| PathAccessCredential | PSCredential | False  | denominata  | Nessuna  | La credenziale di PowerShell per la condivisione file del **\Certificates** directory contenente tutti esterni i certificati di endpoint di rete. Necessaria solo per la rotazione dei segreti esterni o tutti i segreti.  |
| Esegui di nuovo | SwitchParameter | False  | denominata  | Nessuna  | Riesegui devono essere utilizzata ogni volta che la rotazione segreta viene ritentata dopo un tentativo non riuscito. |

### <a name="examples"></a>Esempi
 
#### <a name="rotate-only-internal-infrastructure-secrets"></a>Ruota solo i segreti di infrastruttura interna

Questo deve essere eseguito tramite Azure Stack [dell'ambiente con privilegi endpoint](https://docs.microsoft.com/azure/azure-stack/azure-stack-privileged-endpoint).

```powershell  
PS C:\> Start-SecretRotation  -Internal
```

Questo comando consente di ruotare tutti i segreti dell'infrastruttura esposti alla rete interna di Azure Stack. 

#### <a name="rotate-only-external-infrastructure-secrets"></a>Ruota solo i segreti esterni dell'infrastruttura  

```powershell  
PS C:\> Invoke-Command -session $PEPSession -ScriptBlock {  

Start-SecretRotation -PfxFilesPath $using:CertSharePath -PathAccessCredential $using:CertShareCred -CertificatePassword $using:CertPassword }  

Remove-PSSession -Session $PEPSession
```

Questo comando consente di ruotare i certificati TLS usati per gli endpoint dell'infrastruttura di Azure Stack rete esterna.   

**Ruotare i segreti di infrastruttura interni ed esterni**
  
```powershell
PS C:\> Invoke-Command -session $PEPSession -ScriptBlock { 
Start-SecretRotation -PfxFilesPath $using:CertSharePath -PathAccessCredential $using:CertShareCred -CertificatePassword $using:CertPassword } 
Remove-PSSession -Session $PEPSession
```

Questo comando consente di ruotare tutti i segreti dell'infrastruttura esposti alla rete interna di Azure Stack, nonché i certificati TLS usati per gli endpoint dell'infrastruttura di Azure Stack rete esterna. Start-SecretRotation ruota tutti i segreti generato dello stack e perché sono disponibili certificati, certificati endpoint esterno anche rotazione.  


## <a name="update-the-baseboard-management-controller-bmc-credential"></a>Aggiornare le credenziali di baseboard management controller (BMC)

Baseboard management controller (BMC) consente di monitorare lo stato dei server fisico. Le istruzioni su come aggiornare il nome dell'account utente e la password del BMC specifiche variano in base al fornitore dell'hardware OEM (OEM). È necessario aggiornare le password per i componenti di Azure Stack a intervalli regolari.

1. Aggiornare il BMC in server fisici di Azure Stack, seguendo le istruzioni OEM. Il nome dell'account utente e la password per ciascun BMC nell'ambiente in uso deve essere lo stesso.
2. Aprire un endpoint con privilegi nelle sessioni di Azure Stack. Per istruzioni, vedere [usando l'endpoint con privilegi in Azure Stack](azure-stack-privileged-endpoint.md).
3. Dopo la PowerShell Prompt dei comandi è stato modificato per **[indirizzo IP o ERCS VM name]: PS >** o a **[azs-ercs01]: PS >**, a seconda dell'ambiente, eseguire `Set-BmcCredemtial` eseguendo `invoke-command`. Passare la variabile di sessione con privilegi endpoint come parametro. Ad esempio: 

    ```powershell
    # Interactive Version
    $PEip = "<Privileged Endpoint IP or Name>" # You can also use the machine name instead of IP here.
    $PECred = Get-Credential "<Domain>\CloudAdmin" -Message "PE Credentials" 
    $NewBMCpwd = Read-Host -Prompt "Enter New BMC password" -AsSecureString
    $NewBMCuser = Read-Host -Prompt "Enter New BMC user name"

    $PEPSession = New-PSSession -ComputerName $PEip -Credential $PECred -ConfigurationName "PrivilegedEndpoint" 

    Invoke-Command -Session $PEPSession -ScriptBlock {
        # Parameter BMCPassword is mandatory, while the BMCUser parameter is optional.
        Set-Bmccredential -bmcpassword $using:NewBMCpwd -bmcuser $using:NewBMCuser
    }
    Remove-PSSession -Session $PEPSession
    ```
    
    È anche possibile usare la versione di PowerShell statica con le password come righe di codice:
    
    ```powershell
    # Static Version
    $PEip = "<Privileged Endpoint IP or Name>" # You can also use the machine name instead of IP here.
    $PEUser = "<Privileged Endpoint user for example Domain\CloudAdmin>"
    $PEpwd = ConvertTo-SecureString "<Privileged Endpoint Password>" -Force
    $PECred = New-Object System.Management.Automation.PSCredential ($PEUser, $PEpwd) 
    $NewBMCpwd = ConvertTo-SecureString "<New BMC Password>" -Force
    $NewBMCuser = "<New BMC User name>" 

    $PEPSession = New-PSSession -ComputerName $PEip -Credential $PECred -ConfigurationName "PrivilegedEndpoint" 

    Invoke-Command -Session $PEPSession -ScriptBlock {
        Set-Bmccredential -bmcpassword $using:NewBMCpwd -bmcuser $using:NewBMCuser
    }
    Remove-PSSession -Session $PEPSession
    ```

## <a name="next-steps"></a>Passaggi successivi

[Altre informazioni sulla sicurezza di Azure Stack](azure-stack-security-foundations.md)
