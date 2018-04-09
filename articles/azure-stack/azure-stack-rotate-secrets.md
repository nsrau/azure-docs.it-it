---
title: Ruotare i segreti nello Stack di Azure | Documenti Microsoft
description: Informazioni su come eseguire la rotazione i segreti nello Stack di Azure.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 49071044-6767-4041-9EDD-6132295FA551
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2018
ms.author: mabrigg
ms.reviewer: ppacent
ms.openlocfilehash: 509570dfe0e3d4be2e589ac1958dd377dc4e8e03
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2018
---
# <a name="rotate-secrets-in-azure-stack"></a>Ruotare i segreti nello Stack di Azure

*Queste istruzioni si applicano solo a Azure Stack integrata sistemi versione 1803 e versioni successive. Non tentare secret rotazione nelle versioni di pre-1802 Azure Stack*

Stack di Azure Usa i segreti diversi per mantenere le comunicazioni protette tra i servizi e risorse di infrastruttura di Azure Stack.

- **Segreti interni**  
Tutti i i certificati, password, le stringhe sicure e le chiavi utilizzate dall'infrastruttura di Azure Stack senza l'intervento dell'operatore di Stack di Azure. 

- **Segreti esterni**  
Certificati di infrastruttura a servizio per i servizi con connessione esterna che vengono forniti dall'operatore di Stack di Azure. Sono inclusi i certificati per i servizi seguenti: 
    - Portale dell'amministratore 
    - Portale pubblico 
    - Amministratore Azure Resource Manager 
    - Gestione risorse di Azure globale 
    - Amministratore Keyvault 
    - Key Vault 
    - ACS (inclusi blob, tabelle e archiviazione di Accodamento) 
    - ADFS<sup>*</sup>
    - Grafico<sup>*</sup>

    > <sup>*</sup> Questo campo è applicabile solo se il provider di identità dell'ambiente Active Directory Federated Services (ADFS).

> [!NOTE]
> Tutti gli altri proteggere le chiavi e stringhe, inclusi BMC e cambiare le password, le password degli account utente e amministratore vengono aggiornati manualmente dall'amministratore. 

Per salvaguardare l'integrità dell'infrastruttura di Azure Stack, gli operatori devono avere la possibilità di ruotare periodicamente i segreti dell'infrastruttura frequenze che siano coerenti con i requisiti di sicurezza della propria organizzazione.

## <a name="alert-remediation"></a>Avviso monitoraggio e aggiornamento

Quando i segreti sono entro 30 giorni di scadenza, nel portale di amministrazione vengono generati gli avvisi seguenti: 

- Scadenza password dell'account del servizio in sospeso 
- Scadenza certificato interno in sospeso 
- Scadenza certificato esterno in sospeso 

Esegue la rotazione secret usando le istruzioni seguenti correggerà questi avvisi.

## <a name="pre-steps-for-secret-rotation"></a>Passaggi preliminari per la rotazione dei segreto

1.  Inviare notifiche agli utenti di tutte le operazioni di manutenzione. Pianificare finestre di manutenzione normale, per quanto possibile, durante le ore non lavorative. Sia i carichi di lavoro di utente e le operazioni del portale, possono influire sulle operazioni di manutenzione.

    > [!note]  
    > I passaggi successivi si applicano solo quando la rotazione di segreti esterni dello Stack di Azure.

2.  Preparare un nuovo set di sostituzione di certificati esterni. Il nuovo set corrisponde al certificato alle specifiche di [requisiti dei certificati di infrastruttura a chiave pubblica di Azure Stack](https://docs.microsoft.com/azure/azure-stack/azure-stack-pki-certs).
3.  Archiviare un backup dei certificati usati per la rotazione in un percorso di backup protetto. Se la rotazione viene eseguita e quindi si verifica un errore, sostituire i certificati nella condivisione di file con le copie di backup prima di eseguire la rotazione. Si noti, mantenere copie di backup nel percorso di backup protetto.
3.  Creare una condivisione file che è possibile accedere da macchine virtuali ERCS. La condivisione file deve essere leggibile e scrivibile per il **CloudAdmin** identità.
4.  Aprire una console di PowerShell ISE da un computer in cui si ha accesso alla condivisione file. Passare alla condivisione di file. 
5.  Eseguire **[CertDirectoryMaker.ps1](http://www.aka.ms/azssecretrotationhelper)** per creare le directory richieste per i certificati esterni.

## <a name="rotating-external-and-internal-secrets"></a>La rotazione dei segreti interni ed esterni

Per ruotare un interno segreti entrambi esterno:

1. All'interno di appena creato **certificati** directory creata nei passaggi precedenti, inserire il nuovo set di certificati esterni di sostituzione nella struttura di directory in base al formato descritto nella sezione certificati obbligatori del [requisiti dei certificati di infrastruttura a chiave pubblica di Azure Stack](https://docs.microsoft.com/azure/azure-stack/azure-stack-pki-certs#mandatory-certificates).
2. Creare una sessione di PowerShell con il [Endpoint con privilegi](https://docs.microsoft.com/azure/azure-stack/azure-stack-privileged-endpoint) usando la **CloudAdmin** account e le sessioni vengono archiviati come una variabile. Utilizzare questa variabile come parametro nel passaggio successivo.

    > [!IMPORTANT]  
    > Non immettere la sessione, la sessione vengono archiviati come una variabile.
    
3. Eseguire  **[invoke-command](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/invoke-command?view=powershell-5.1)**. Passare la variabile di sessione di PowerShell con privilegi di Endpoint come le **sessione** parametro. 
4. Eseguire **inizio SecretRotation** con i parametri seguenti:
    - **PfxFilesPath**  
    Specificare il percorso di rete alla directory certificati creato in precedenza.  
    - **PathAccessCredential**  
    Un oggetto PSCredential le credenziali per la condivisione. 
    - **CertificatePassword**  
    Una stringa sicura della password utilizzata per tutti i file di certificato pfx creati.
4. Attendere. ruotare i segreti.  
Quando la rotazione segreta è stata completata correttamente, la console verrà visualizzato **stato dell'azione complessivo: esito positivo**. 
5. Dopo il completamento della rotazione segreta, rimuovere i certificati dalla condivisione creata nel passaggio precedente e archiviarli in posizione di backup protetto. 

## <a name="walkthrough-of-secret-rotation"></a>Procedura dettagliata di rotazione segreta

L'esempio di PowerShell seguente illustra i cmdlet e parametri per l'esecuzione per ruotare i segreti.

```powershell
#Create a PEP Session
winrm s winrm/config/client '@{TrustedHosts= "<IPofERCSMachine>"}'
$PEPCreds = Get-Credential 
$PEPsession = New-PSSession -computername <IPofERCSMachine> -Credential $PEPCreds -ConfigurationName PrivilegedEndpoint 

#Run Secret Rotation
$CertPassword = "CertPasswordHere" | ConvertTo-SecureString
$CertShareCred = Get-Credential 
$CertSharePath = <NetworkPathofCertShare>   
Invoke-Command -session $PEPsession -ScriptBlock { 
Start-SecretRotation -PfxFilesPath $using:CertSharePath -PathAccessCredential $using:CertShareCred -CertificatePassword $using:CertPassword }
Remove-PSSession -Session $PEPSession
```
## <a name="rotating-only-internal-secrets"></a>La rotazione solo i segreti interni

Per ruotare solo i segreti interni Azure dello Stack:

1. Creare una sessione di PowerShell con il [con privilegi Endpoint](https://docs.microsoft.com/azure/azure-stack/azure-stack-privileged-endpoint).
2. Nella sessione dell'Endpoint con privilegi, eseguire **inizio SecretRotation** senza argomenti.

## <a name="start-secretrotation-reference"></a>Inizio SecretRotation riferimento

Ruota i segreti di un sistema di Stack di Azure. Viene eseguita solo nell'endpoint con privilegi di Azure dello Stack.

### <a name="syntax"></a>Sintassi

Percorso (impostazione predefinita)

```powershell
Start-SecretRotation [-PfxFilesPath <string>] [-PathAccessCredential] <PSCredential> [-CertificatePassword <SecureString>]  
```

### <a name="description"></a>DESCRIZIONE

Il cmdlet Start-SecretRotation ruota i segreti dell'infrastruttura di un sistema di Stack di Azure. Per impostazione predefinita che viene ruotato tutti i segreti esposti alla rete dell'infrastruttura interno, con l'input utente viene anche ruotato i certificati di tutti gli endpoint dell'infrastruttura di rete esterna. Quando la rotazione di endpoint dell'infrastruttura di rete esterna, Start-SecretRotation deve essere eseguita tramite un blocco di script Invoke-Command con sessione di endpoint con privilegi dell'ambiente Azure Stack passata come il parametro session.
 
### <a name="parameters"></a>Parametri

| Parametro | type | Obbligatoria | Posizione | Predefinito | DESCRIZIONE |
| -- | -- | -- | -- | -- | -- |
| PfxFilesPath | string  | False  | denominata  | Nessuna  | Il percorso di condivisione file per il **\Certificates** directory contenente tutti esterno i certificati di endpoint di rete. Necessario solo quando la rotazione di segreti interni ed esterni. Directory di fine deve essere **\Certificates**. |
| CertificatePassword | SecureString | False  | denominata  | Nessuna  | La password per tutti i certificati disponibili in PfXFilesPath. Valore obbligatorio se PfxFilesPath viene fornito quando vengono ruotati segreti interni ed esterni. |
|

### <a name="examples"></a>Esempi
 
**Ruota solo i segreti di infrastruttura interni**

```powershell  
PS C:\> Start-SecretRotation  
```

Questo comando consente di ruotare tutti i segreti infrastruttura esposti alla rete interna dello Stack di Azure. Inizio SecretRotation ruota tutti i segreti generato dello stack, ma poiché non sono disponibili certificati forniti, i certificati di endpoint esterni non essere ruotati.  

**Ruotare i segreti di infrastruttura interni ed esterni**
  
```powershell
PS C:\> Invoke-Command -session $PEPSession -ScriptBlock { 
Start-SecretRotation -PfxFilesPath $using:CertSharePath -PathAccessCredential $using:CertShareCred -CertificatePassword $using:CertPassword } 
Remove-PSSession -Session $PEPSession
```

Questo comando consente di ruotare tutti i segreti infrastruttura esposti alla rete interna dello Stack di Azure, nonché i certificati TLS utilizzati per gli endpoint dell'infrastruttura di rete esterna Azure dello Stack. Inizio SecretRotation ruota tutti i segreti generato dello stack e perché sono disponibili certificati, certificati endpoint esterno anche rotazione.  


## <a name="update-the-baseboard-management-controller-bmc-password"></a>Aggiornare la password di baseboard management controller (BMC)

Baseboard management controller (BMC) consente di monitorare lo stato dei server fisico. Le istruzioni su come aggiornare la password del BMC specifiche variano in base al fornitore dell'hardware (OEM) original equipment manufacturer. È necessario aggiornare le password per i componenti dello Stack di Azure un cadenza regolare.

1. Aggiornare il BMC su server fisici di Stack Azure seguendo le istruzioni OEM. La password per ciascun BMC nell'ambiente in uso deve essere lo stesso.
2. Aprire un endpoint con privilegi in Azure Stack sessioni. Per istruzioni, vedere [utilizzando l'endpoint con privilegi in Azure Stack](azure-stack-privileged-endpoint.md).
3. Dopo il PowerShell Prompt dei comandi è stato modificato in **[nome indirizzo IP o VM ERCS]: PS >** o **[azs ercs01]: PS >**, a seconda dell'ambiente, eseguire `Set-BmcPassword` eseguendo `invoke-command`. Passare la variabile di sessione con privilegi endpoint come parametro. Ad esempio: 

    ```powershell
    # Interactive Version
    $PEip = "<Privileged Endpoint IP or Name>" # You can also use the machine name instead of IP here.
    $PECred = Get-Credential "<Domain>\CloudAdmin" -Message "PE Credentials" 
    $NewBMCpwd = Read-Host -Prompt "Enter New BMC password" -AsSecureString 

    $PEPSession = New-PSSession -ComputerName $PEip -Credential $PECred -ConfigurationName "PrivilegedEndpoint" 

    Invoke-Command -Session $PEPSession -ScriptBlock {
        Set-Bmcpassword -bmcpassword $using:NewBMCpwd
    }
    Remove-PSSession -Session $PEPSession
    ```
    
    È anche possibile utilizzare la versione di PowerShell statica con le password come righe di codice:
    
    ```powershell
    # Static Version
    $PEip = "<Privileged Endpoint IP or Name>" # You can also use the machine name instead of IP here.
    $PEUser = "<Privileged Endpoint user for exmaple Domain\CloudAdmin>"
    $PEpwd = ConvertTo-SecureString "<Privileged Endpoint Password>" -AsPlainText -Force
    $PECred = New-Object System.Management.Automation.PSCredential ($PEUser, $PEpwd) 
    $NewBMCpwd = ConvertTo-SecureString "<New BMC Password>" -AsPlainText -Force 

    $PEPSession = New-PSSession -ComputerName $PEip -Credential $PECred -ConfigurationName "PrivilegedEndpoint" 

    Invoke-Command -Session $PEPSession -ScriptBlock {
        Set-Bmcpassword -bmcpassword $using:NewBMCpwd
    }
    Remove-PSSession -Session $PEPSession
    ```

## <a name="next-steps"></a>Passaggi successivi

[Ulteriori informazioni sulla sicurezza di Azure Stack](azure-stack-security-foundations.md)
